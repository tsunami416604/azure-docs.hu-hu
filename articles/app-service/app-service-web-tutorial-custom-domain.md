---
title: 'Oktatóanyag: meglévő egyéni DNS-név leképezése'
description: Megtudhatja, hogyan adhat hozzá meglévő egyéni DNS-tartománynevet (egyéni tartományt) egy webalkalmazáshoz, egy mobilalkalmazás hátérrendszeréhez vagy egy API-alkalmazáshoz az Azure App Service-ben.
keywords: app service, azure app service, tartomány-hozzárendelés, tartománynév, meglévő tartomány, gazdagépnév
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: 4a2c65e2685dada6412adf8c8ad9c63f472b91e8
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962281"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Oktatóanyag: meglévő egyéni DNS-név leképezése Azure App Service

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan képezhető le egy meglévő egyéni DNS-név a Azure App Service.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Altartomány hozzárendelése (például `www.contoso.com`) CNAME rekorddal
> * Gyökértartomány hozzárendelése (például `contoso.com`) A rekorddal
> * Helyettesítő karaktert tartalmazó tartomány hozzárendelése (például `*.contoso.com`) CNAME rekorddal
> * Alapértelmezett URL-cím átirányítása egyéni könyvtárra
> * A tartományleképezés automatizálása szkriptekkel

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [Hozzon létre egy App Service-alkalmazást](./index.yml), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
* Vásároljon egy tartománynevet, és győződjön meg arról, hogy hozzáfér a tartományszolgáltatója (például a GoDaddy) DNS-jegyzékéhez.

  Például a `contoso.com` és a `www.contoso.com` DNS-bejegyzéseinek hozzáadásához képesnek kell lennie a `contoso.com` gyökértartomány DNS-beállításainak konfigurálására.

  > [!NOTE]
  > Ha nem rendelkezik meglévő tartománynévvel, az [Azure Portallal vásárolhat egy tartományt](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

Ha egyéni DNS-nevet szeretne hozzárendelni egy webalkalmazáshoz, a webalkalmazás [app Service csomagjának](https://azure.microsoft.com/pricing/details/app-service/) fizetős szintnek kell lennie (**közös**, **alapszintű**, **standard**, **prémium** **vagy Azure functions** ). Ebben a lépésben ellenőrzi, hogy az App Service-alkalmazás a támogatott tarifacsomagban van-e.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="select-the-app-in-the-azure-portal"></a>Válassza ki az alkalmazást a Azure Portal

Keresse meg és válassza ki a **app Services**.

![App Services kiválasztása](./media/app-service-web-tutorial-custom-domain/app-services.png)

A **app Services** lapon válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/select-app.png)

Megjelenik az App Service-alkalmazás felügyeleti oldala.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

Az alkalmazás lapjának bal oldali navigációs sávján görgessen a **Beállítások** szakaszra, és válassza ki a **Vertikális felskálázás (App Service-csomag)** elemet.

![Vertikális felskálázás menü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Győződjön meg arról, hogy az alkalmazás nem az **F1** szinten van. Az egyéni DNS nem támogatott az **F1** szinten. 

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Ha az App Service-csomag nem az **F1** szinten van, zárja be a **Vertikális felskálázás** oldalt és folytassa a [CNAME rekord leképezése](#cname) lépéssel.

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

![Skálázási művelet megerősítése](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-domain-verification-id"></a>Tartomány-ellenőrzési azonosító lekérése

Ha egyéni tartományt szeretne felvenni az alkalmazásba, ellenőriznie kell a tartomány tulajdonjogát úgy, hogy egy ellenőrző azonosítót ad hozzá TXT-rekordként a tartományi szolgáltatóhoz. Az alkalmazás lap bal oldali navigációs sávján kattintson az **Egyéni tartományok**elemre. Másolja az **egyéni tartomány-ellenőrzési azonosítót** az **Egyéni tartományok** lapon a következő lépéshez.

![Egyéni tartomány ellenőrző AZONOSÍTÓjának beolvasása](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> A tartomány-ellenőrzési azonosítók az egyéni tartományhoz való hozzáadásával megakadályozható a DNS-bejegyzések kihelyezése és az altartományon belüli átvételek elkerülése. A gyakori, nagy súlyosságú fenyegetésekkel kapcsolatos további információkért lásd: [altartomány átvétele](../security/fundamentals/subdomain-takeover.md).

## <a name="map-your-domain"></a>Saját tartomány leképezése

Az egyéni DNS-neveket **CNAME rekordok** vagy **A rekordok** használatával is leképezheti az App Service-re. Hajtsa végre a megfelelő lépéseket:

- [CNAME rekord hozzárendelése](#map-a-cname-record)
- [A rekord hozzárendelése](#map-an-a-record)
- [Helyettesítő karaktert tartalmazó tartomány leképezése (CNAME-rekorddal)](#map-a-wildcard-domain)

> [!NOTE]
> A gyökértartományok (például: `contoso.com`) kivételével minden egyéni DNS-névhez CNAME-rekordot használjon. Gyökértartományokhoz A-rekordokat használjon.

### <a name="map-a-cname-record"></a>CNAME rekord hozzárendelése

Az oktatóanyag példájában a `www` altartományhoz (például `www.contoso.com`) tartozó CNAME rekordot ad hozzá.

Ha a-től eltérő altartománnyal rendelkezik `www` , cserélje le az `www` altartományra (például `sub` Ha az egyéni tartománya `sub.constoso.com` ).

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Altartomány hozzárendelése az alkalmazás alapértelmezett tartománynevéhez (ahol az az `<app-name>.azurewebsites.net` `<app-name>` alkalmazás neve). Ha CNAME leképezést szeretne létrehozni az `www` altartományhoz, hozzon létre két rekordot:

| Rekordtípus | Gazda | Érték | Megjegyzések |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Maga a tartomány-hozzárendelés. |
| TXT | `asuid.www` | [A korábban kapott ellenőrző azonosító](#get-domain-verification-id) | App Service hozzáfér a `asuid.<subdomain>` txt-rekordhoz az egyéni tartomány tulajdonjogának ellenőrzéséhez. |

A CNAME és TXT rekordok hozzáadása után a DNS-rekordok oldal a következő példához hasonlóan néz ki:

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>A CNAME rekord hozzárendelésének engedélyezése az Azure-ban

1. Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

    ![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Az alkalmazás **Egyéni tartományok** oldalán adja hozzá a teljes egyéni DNS-nevet (`www.contoso.com`) a listához.

1. Válassza az **egyéni tartomány hozzáadása**lehetőséget.

    ![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írja be a teljes tartománynevet, amelyhez hozzáadta a CNAME rekordot adott (például `www.contoso.com`).

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik az **egyéni tartomány hozzáadása** lap.

1. Győződjön meg arról, hogy az **állomásnév bejegyzéstípusa** **CNAME (www \. example.com vagy bármely altartomány)** értékre van beállítva. Válassza az **egyéni tartomány hozzáadása**lehetőséget.

    ![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

    ![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Az egyéni tartományra vonatkozó figyelmeztetési címke azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz, és a böngészőtől az egyéni tartományba irányuló HTTPS-kérések a böngészőtől függően kapnak, illetve hibát vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).

    Ha kihagyott egy lépést, vagy korábban valamit elgépelt, egy ellenőrzési hiba látható a lap alján.

    ![Ellenőrzési hiba](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>A rekord hozzárendelése

Az oktatóanyag példájában egy A rekordot ad hozzá a gyökértartományhoz (például `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Az alkalmazás IP-címének másolása

A rekord leképezéséhez az alkalmazás külső IP-címére van szükség. Ezt az IP-címet az alkalmazás **Egyéni tartományok** oldalán találja az Azure Portalon.

Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az **Egyéni tartományok** oldalon másolja az alkalmazás IP-címét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Az A rekord létrehozása

Ha egy rekordot egy alkalmazáshoz szeretne hozzárendelni, általában a legfelső szintű tartományhoz, hozzon létre két rekordot:

| Rekordtípus | Gazda | Érték | Megjegyzések |
| - | - | - |
| A | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím | Maga a tartomány-hozzárendelés ( `@` általában a legfelső szintű tartományt jelenti). |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#get-domain-verification-id) | App Service hozzáfér a `asuid.<subdomain>` txt-rekordhoz az egyéni tartomány tulajdonjogának ellenőrzéséhez. A gyökérszintű tartományhoz használja a következőt: `asuid` . |

> [!NOTE]
> Egy altartomány (például) egy `www.contoso.com` , az ajánlott [CNAME-rekord](#map-a-cname-record)helyett egy rekord használatával való hozzáadásához a rekordnak és a txt-rekordnak a következő táblázathoz hasonlóan kell kinéznie:
>
> | Rekordtípus | Gazda | Érték |
> | - | - | - |
> | A | `www` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
> | TXT | `asuid.www` | `<app-name>.azurewebsites.net` |
>

A rekordok hozzáadása után a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![DNS-rekordok oldala](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Az A rekord hozzárendelésének engedélyezése az alkalmazásban

Az Azure Portalon az alkalmazás **Egyéni tartományok** oldalán adja hozzá a teljes egyéni DNS-nevet (például `contoso.com`) a listához.

1. Válassza az **egyéni tartomány hozzáadása**lehetőséget.

    ![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írja be az A rekordhoz konfigurált teljes tartománynevet (például `contoso.com`). 

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik az **egyéni tartomány hozzáadása** lap.

1. Győződjön meg arról, hogy a **Gazdagépnév rekordtípusa** beállítás értéke **A rekord (example.com)**. Válassza az **egyéni tartomány hozzáadása**lehetőséget.

    ![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

    ![A rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Az egyéni tartományra vonatkozó figyelmeztetési címke azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz, és a böngészőtől az egyéni tartományba irányuló HTTPS-kérések a böngészőtől függően kapnak, illetve hibát vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).
    
    Ha kihagyott egy lépést, vagy korábban valamit elgépelt, egy ellenőrzési hiba látható a lap alján.
    
    ![Ellenőrzési hiba](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Helyettesítő karaktert tartalmazó tartomány hozzárendelése

Az oktatóanyag példájában egy [helyettesítő karaktert tartalmazó DNS-nevet](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (például `*.contoso.com`) képez le az App Service-alkalmazásra egy CNAME rekord hozzáadásával.

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Rendelje hozzá a helyettesítő karaktert `*` az alkalmazás alapértelmezett tartománynevéhez ( `<app-name>.azurewebsites.net` ahol az az `<app-name>` alkalmazás neve). A helyettesítő karakter nevének leképezéséhez hozzon létre két rekordot:

| Rekordtípus | Gazda | Érték | Megjegyzések |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Maga a tartomány-hozzárendelés. |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#get-domain-verification-id) | App Service hozzáfér a `asuid` txt-rekordhoz az egyéni tartomány tulajdonjogának ellenőrzéséhez. |

A `*.contoso.com` tartomány példájában a CNAME rekord a `*` előtagot a `<app-name>.azurewebsites.net` elemre képezi le.

A CNAME hozzáadása után a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>A CNAME rekord hozzárendelésének engedélyezése az alkalmazásban

Mostantól hozzáadhat bármely olyan altartományt, amely megfelel a helyettesítő karakter nevének (például, `sub1.contoso.com` `sub2.contoso.com` és `*.contoso.com` mindkét egyezés `*.contoso.com` ).

1. Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

    ![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Válassza az **egyéni tartomány hozzáadása**lehetőséget.

    ![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írjon be egy olyan teljes tartománynevet, amely megfelel a helyettesítő karaktert tartalmazó tartománynak (például `sub1.contoso.com`), majd válassza az **Érvényesítés** lehetőséget.

    Az **egyéni tartomány hozzáadása** gomb aktiválva van.

1. Győződjön meg arról, hogy az **állomásnév bejegyzéstípusa** **CNAME rekord (www \. example.com vagy bármely altartomány)**. Válassza az **egyéni tartomány hozzáadása**lehetőséget.

    ![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

1. Kattintson **+** ismét az ikonra egy másik olyan egyéni tartomány hozzáadásához, amely megfelel a helyettesítő karakteres tartománynak. Például adja hozzá a következőt: `sub2.contoso.com`.

    ![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Az egyéni tartományra vonatkozó figyelmeztetési címke azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz, és a böngészőtől az egyéni tartományba irányuló HTTPS-kérések a böngészőtől függően kapnak, illetve hibát vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).
    
## <a name="test-in-browser"></a>Tesztelés a böngészőben

Keresse meg a korábban konfigurált DNS-neve(ke)t (például `contoso.com`, `www.contoso.com`, `sub1.contoso.com` és `sub2.contoso.com`).

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>A 404 feloldása nem található

Ha HTTP 404-es (nem található) hiba történik az egyéni tartomány URL-címének megnyitásakor, a <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a> használatával ellenőrizze, hogy a tartomány az alkalmazás IP-címére van-e feloldva. Ha nem, azt a következők okozhatják:

- A konfigurált egyéni tartományból hiányzik egy A rekord és/vagy egy CNAME rekord.
- A böngészőügyfél gyorsítótárazta a tartomány régi IP-címét. Ürítse ki a gyorsítótárat, és tesztelje ismét a DNS-feloldást. Windows-gépen az `ipconfig /flushdns` paranccsal törölheti a gyorsítótárat.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Aktív tartomány migrálása

Élő webhely és hozzá tartozó DNS-tartománynév migrálása leállás nélkül az App Service-be: [Aktív DNS-név migrálása az Azure App Service-be](manage-custom-dns-migrate-domain.md).

## <a name="redirect-to-a-custom-directory"></a>Átirányítás egyéni könyvtárra

Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Bizonyos webes keretrendszerek azonban nem a gyökérkönyvtárban indulnak. A [Laravel](https://laravel.com/) például a `public` alkönyvtárban indul. A `contoso.com` DNS példát folytatva, az ilyen alkalmazások elérhetők a `http://contoso.com/public` könyvtárban, de célszerűbb a `http://contoso.com` átirányítása a `public` könyvtárba. Ez a lépés nem DNS-feloldással, hanem a virtuális könyvtár testreszabásával történik.

Ehhez válassza ki a webalkalmazás oldalán a bal oldali navigációs sávban lévő **Alkalmazásbeállítások** elemet. 

A lap alján, a virtuális gyökérkönyvtár (`/`) alapértelmezés szerint a `site\wwwroot` könyvtárra mutat, amely az alkalmazáskód gyökérkönyvtára. Módosítsa úgy, hogy ehelyett a `site\wwwroot\public` könyvtárra mutasson, és mentse a módosításokat.

![A virtuális könyvtár testreszabása](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

A művelet befejezése után az alkalmazásnak vissza kell adni a jobb oldalt a gyökér elérési útjában (például: `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Szkriptekkel automatizálhatja az egyéni tartományok felügyeletét az [Azure CLI](/cli/azure/install-azure-cli) vagy az [Azure PowerShell](/powershell/azure/) használatával. 

### <a name="azure-cli"></a>Azure CLI 

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz. 

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

További információ: [Egyéni tartomány leképezése egy webalkalmazásra](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

További információ: [Egyéni tartomány hozzárendelése egy webalkalmazáshoz](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Altartomány hozzárendelése CNAME rekorddal
> * Gyökértartomány hozzárendelése A rekorddal
> * Helyettesítő karaktert tartalmazó tartomány hozzárendelése CNAME rekorddal
> * Alapértelmezett URL-cím átirányítása egyéni könyvtárra
> * A tartományleképezés automatizálása szkriptekkel

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan köthető egy egyéni TLS/SSL-tanúsítvány egy webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure App Service](configure-ssl-bindings.md)
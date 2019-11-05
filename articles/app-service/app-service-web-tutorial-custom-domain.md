---
title: Meglévő egyéni DNS-név leképezése – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá meglévő egyéni DNS-tartománynevet (egyéni tartományt) egy webalkalmazáshoz, egy mobilalkalmazás hátérrendszeréhez vagy egy API-alkalmazáshoz az Azure App Service-ben.
keywords: app service, azure app service, tartomány-hozzárendelés, tartománynév, meglévő tartomány, gazdagépnév
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 52394eb150a4206b7fb31cdf4b801762511e1a8c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73471373"
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

* [Hozzon létre egy App Service-alkalmazást](/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
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

## <a name="map-your-domain"></a>Saját tartomány leképezése

Az egyéni DNS-neveket **CNAME rekordok** vagy **A rekordok** használatával is leképezheti az App Service-re. Hajtsa végre a megfelelő lépéseket:

- [CNAME-rekord leképezése](#map-a-cname-record)
- [A-rekord leképezése](#map-an-a-record)
- [Helyettesítő karaktert tartalmazó tartomány leképezése (CNAME-rekorddal)](#map-a-wildcard-domain)

> [!NOTE]
> A gyökértartományok (például: `contoso.com`) kivételével minden egyéni DNS-névhez CNAME-rekordot használjon. Gyökértartományokhoz A-rekordokat használjon.

### <a name="map-a-cname-record"></a>CNAME rekord hozzárendelése

Az oktatóanyag példájában a `www` altartományhoz (például `www.contoso.com`) tartozó CNAME rekordot ad hozzá.

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Adjon hozzá egy CNAME-rekordot egy altartománynak az alkalmazás alapértelmezett tartománynevéhez való hozzárendeléséhez (`<app_name>.azurewebsites.net`, ahol a `<app_name>` az alkalmazás neve).

A `www.contoso.com` tartomány példája esetében adjon hozzá egy CNAME rekordot, amely a `www` előtagot az `<app_name>.azurewebsites.net` elemre képezi le.

Miután hozzáadta a CNAME rekordot, a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>A CNAME rekord hozzárendelésének engedélyezése az Azure-ban

Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az alkalmazás **Egyéni tartományok** oldalán adja hozzá a teljes egyéni DNS-nevet (`www.contoso.com`) a listához.

Válassza az **egyéni tartomány hozzáadása**elem melletti **+** ikont.

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be a teljes tartománynevet, amelyhez hozzáadta a CNAME rekordot adott (például `www.contoso.com`).

Válassza az **Érvényesítés** lehetőséget.

Megjelenik az **egyéni tartomány hozzáadása** lap.

Győződjön meg arról, hogy az **állomásnév bejegyzéstípusa** **CNAME (www\.example.com vagy bármely altartomány)** értékre van beállítva.

Válassza az **Egyéni tartomány hozzáadása** lehetőséget.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nem kötődik SSL-tanúsítványhoz, és a böngészőtől az egyéni tartományba IRÁNYULó HTTPS-kérések a böngészőtől függően kapnak, illetve hibát vagy figyelmeztetést kapnak. SSL-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).

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

Ha egy A rekordot egy alkalmazásra kíván leképezni, az App Service-nek **két** DNS-rekordra van szüksége:

- egy **A** rekordra, amelyet leképezhet az alkalmazás IP-címére.
- Egy **txt** -rekord, amely az alkalmazás alapértelmezett tartománynevéhez `<app_name>.azurewebsites.net`képezhető le. Az App Service ezt a rekordot csak a konfiguráláskor használja annak ellenőrzéséhez, hogy Ön-e az egyéni tartomány tulajdonosa. Miután az App Service-ben érvényesítette és konfigurálta az egyéni tartományt, törölheti ezt a TXT típusú rekordot.

Az `contoso.com` tartomány példájában a következő táblának megfelelően hozza létre az A és a TXT típusú rekordot (a `@` általában a gyökértartományt jelöli).

| Rekordtípus | Gazdagép | Érték |
| - | - | - |
| A | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
| TXT | `@` | `<app_name>.azurewebsites.net` |

> [!NOTE]
> Egy altartomány (például `www.contoso.com`) egy, az ajánlott [CNAME-rekord](#map-a-cname-record)helyett egy rekord használatával való hozzáadásához a rekordnak és a txt-rekordnak a következő táblázathoz hasonlóan kell kinéznie:
>
> | Rekordtípus | Gazdagép | Érték |
> | - | - | - |
> | A | `www` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
> | TXT | `www` | `<app_name>.azurewebsites.net` |
>

A rekordok hozzáadása után a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![DNS-rekordok oldala](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Az A rekord hozzárendelésének engedélyezése az alkalmazásban

Az Azure Portalon az alkalmazás **Egyéni tartományok** oldalán adja hozzá a teljes egyéni DNS-nevet (például `contoso.com`) a listához.

Válassza az **egyéni tartomány hozzáadása**elem melletti **+** ikont.

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be az A rekordhoz konfigurált teljes tartománynevet (például `contoso.com`).

Válassza az **Érvényesítés** lehetőséget.

Megjelenik az **egyéni tartomány hozzáadása** lap.

Győződjön meg arról, hogy a **Gazdagépnév rekordtípusa** beállítás értéke **A rekord (example.com)** .

Válassza az **Egyéni tartomány hozzáadása** lehetőséget.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![A rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nem kötődik SSL-tanúsítványhoz, és a böngészőtől az egyéni tartományba IRÁNYULó HTTPS-kérések a böngészőtől függően kapnak, illetve hibát vagy figyelmeztetést kapnak. SSL-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).

Ha kihagyott egy lépést, vagy korábban valamit elgépelt, egy ellenőrzési hiba látható a lap alján.

![Ellenőrzési hiba](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Helyettesítő karaktert tartalmazó tartomány hozzárendelése

Az oktatóanyag példájában egy [helyettesítő karaktert tartalmazó DNS-nevet](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (például `*.contoso.com`) képez le az App Service-alkalmazásra egy CNAME rekord hozzáadásával.

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Adjon hozzá egy CNAME-rekordot a helyettesítő karakter nevének az alkalmazás alapértelmezett tartománynevéhez (`<app_name>.azurewebsites.net`) való leképezéséhez.

A `*.contoso.com` tartomány példájában a CNAME rekord a `*` előtagot a `<app_name>.azurewebsites.net` elemre képezi le.

A CNAME hozzáadása után a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>A CNAME rekord hozzárendelésének engedélyezése az alkalmazásban

Most már bármilyen altartományt hozzáadhat az alkalmazáshoz, amely megfelel a helyettesítő karaktert tartalmazó névnek (például a `sub1.contoso.com` és a `sub2.contoso.com` megfelel a `*.contoso.com` névnek).

Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Válassza az **egyéni tartomány hozzáadása**elem melletti **+** ikont.

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írjon be egy olyan teljes tartománynevet, amely megfelel a helyettesítő karaktert tartalmazó tartománynak (például `sub1.contoso.com`), majd válassza az **Érvényesítés** lehetőséget.

Az **egyéni tartomány hozzáadása** gomb aktiválva van.

Győződjön meg arról, hogy az **állomásnév bejegyzéstípusa** **CNAME rekord (www\.example.com vagy bármely altartomány)** .

Válassza az **Egyéni tartomány hozzáadása** lehetőséget.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

Kattintson ismét a **+** ikonra egy másik olyan egyéni tartomány hozzáadásához, amely megfelel a helyettesítő karakteres tartománynak. Például adja hozzá a következőt: `sub2.contoso.com`.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> Megjegyzés: az egyéni tartományhoz tartozó **biztonságos** címke azt jelenti, hogy még nincs SSL-tanúsítvány kötve, és a böngészőtől az egyéni tartományba IRÁNYULó HTTPS-kérések a böngészőtől függően megérkeznek, illetve hibákba vagy figyelmeztetésbe kerülnek. SSL-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).

## <a name="test-in-browser"></a>Tesztelés a böngészőben

Keresse meg a korábban konfigurált DNS-neve(ke)t (például `contoso.com`, `www.contoso.com`, `sub1.contoso.com` és `sub2.contoso.com`).

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>404-es „Nem található” hiba feloldása

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

A művelet befejezése után az alkalmazás a megfelelő oldalt adja vissza a gyökérútvonalon (például http://contoso.com)).

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Szkriptekkel automatizálhatja az egyéni tartományok felügyeletét az [Azure CLI](/cli/azure/install-azure-cli) vagy az [Azure PowerShell](/powershell/azure/overview) használatával. 

### <a name="azure-cli"></a>Azure parancssori felület (CLI) 

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

További információ: [Egyéni tartomány leképezése egy webalkalmazásra](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz.

```powershell  
Set-AzWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net")
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

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan köthet egyéni SSL-tanúsítványt webalkalmazásokhoz.

> [!div class="nextstepaction"]
> [Egyéni DNS-név biztonságossá tétele SSL-kötéssel Azure App Service](configure-ssl-bindings.md)

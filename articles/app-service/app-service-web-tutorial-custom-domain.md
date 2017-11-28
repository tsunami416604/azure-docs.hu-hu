---
title: "Egy meglévő egyéni DNS-névvel hozzárendelése az Azure Web Apps |} Microsoft Docs"
description: "Útmutató egy webalkalmazást, mobil-háttéralkalmazás vagy az Azure App Service API-alkalmazás hozzáadása egy meglévő egyéni DNS-tartománynevet (személyes tartománnyal)."
keywords: "az App service, azure app service, tartományi leképezés, tartománynév, meglévő tartományhoz, állomásnév"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 1a0b54e75bd6356ba7ba351d51d5f4a59bd64c75
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Egy meglévő egyéni DNS-névvel hozzárendelése az Azure Web Apps

Az [Azure Web Apps](app-service-web-overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Az oktatóanyag bemutatja, hogyan képezheti Azure Web Apps egy meglévő egyéni DNS-névvel.

![Az Azure alkalmazásba portálnavigációjával](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Altartomány leképezése (például `www.contoso.com`) egy olyan CNAME rekordot használatával
> * Egy legfelső szintű tartomány leképezése (például `contoso.com`) az A rekord használatával
> * Rendelni egy helyettesítő tartományt (például `*.contoso.com`) egy olyan CNAME rekordot használatával
> * Tartomány leképezése parancsfájlokkal automatizálhatja

Választhat egy **CNAME rekord** vagy egy **rekord** App Service egy egyéni DNS-név hozzárendelése. 

> [!NOTE]
> Azt javasoljuk, hogy használ egy olyan CNAME REKORDOT a legfelső szintű tartomány kivételével az összes egyéni DNS-nevek (például `contoso.com`).

Az App Service egy élő hely és a DNS-tartománynév áttelepítéséhez lásd: [egy aktív DNS-név áttelepítése az Azure App Service](app-service-custom-domain-name-migrate.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [App Service-alkalmazás létrehozása](/azure/app-service/), vagy egy másik az oktatóanyaghoz létrehozott alkalmazást használni.
* Vásároljon egy tartomány nevét, és ellenőrizze, elérhető lesz a tartomány-szolgáltató (például a GoDaddy) a DNS-beállításjegyzék.

  Ahhoz például, hogy vegye fel a DNS-bejegyzéseket `contoso.com` és `www.contoso.com`, be kell tudnia a DNS-beállításainak konfigurálásához a `contoso.com` gyökértartomány.

  > [!NOTE]
  > Ha még nem rendelkezik egy meglévő tartomány nevét, érdemes lehet [egy tartományhoz az Azure portál használatával megvásárlásáról](custom-dns-web-site-buydomains-web-app.md). 

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

Egy egyéni DNS-név hozzárendelése egy webes alkalmazás, a webes alkalmazás [App Service-csomag](https://azure.microsoft.com/pricing/details/app-service/) fizetős rétegben kell lennie (**megosztott**, **alapvető**, **szabványos**, vagy  **Prémium szintű**). Ezt a lépést akkor győződjön meg arról, hogy az App Service alkalmazás van a támogatott az IP-címek.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg a [Azure-portálon](https://portal.azure.com) és jelentkezzen be az Azure-fiókjával.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Keresse meg az alkalmazás az Azure-portálon

A bal oldali menüben válassza ki a **alkalmazásszolgáltatások**, majd válassza ki az alkalmazás nevét.

![Az Azure alkalmazásba portálnavigációjával](./media/app-service-web-tutorial-custom-domain/select-app.png)

Az App Service alkalmazás a felügyeleti oldal akkor jelenik meg.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>Ellenőrizze az árképzési szint

Az alkalmazás oldal bal oldali navigációs, görgessen a **beállítások** válassza ki azt **vertikális felskálázás (App Service-csomag)**.

![Méretezett menü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Az alkalmazás jelenlegi rétegtől kék szegély ki van jelölve. Győződjön meg arról, hogy az alkalmazás nem szerepel a **szabad** réteg. Nem támogatja az egyéni DNS a **szabad** réteg. 

![Ellenőrizze a tarifacsomag](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Ha az App Service-csomag **szabad**, zárja be a **válasszon tarifacsomagot** lapon, és folytassa a [képezi le egy olyan CNAME rekordot](#cname).

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>Vertikális felskálázás az App Service-csomag

Válassza ki valamelyik nem szabad rétegek (**megosztott**, **alapvető**, **szabványos**, vagy **prémium**). 

Kattintson a **Kiválasztás** gombra.

![Ellenőrizze a tarifacsomag](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a méretezési művelet befejeződött.

![Skálázási művelet megerősítése](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>A CNAME rekord leképezése

Az oktatóanyag példában hozzá egy CNAME rekordot a `www` altartomány (például `www.contoso.com`).

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Adjon hozzá egy CNAME rekordot altartomány hozzárendelése az alkalmazás alapértelmezett állomásnév (`<app_name>.azurewebsites.net`, ahol `<app_name>` az alkalmazás neve).

Az a `www.contoso.com` tartomány például egy CNAME rekordot, amely rendeli hozzá `www` való `<app_name>.azurewebsites.net`.

Miután hozzáadta a CNAME REKORDOT, a DNS-rekordok lapon néz ki a következő példa:

![Az Azure alkalmazásba portálnavigációjával](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Az Azure-ban a CNAME rekord hozzárendelésének engedélyezése

Az alkalmazás oldal az Azure-portálon a bal oldali navigációs, válassza ki a **egyéni tartományok**. 

![Az egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az a **egyéni tartományok** lap az alkalmazáshoz, adja hozzá a teljes egyéni DNS-nevet (`www.contoso.com`) listájához.

Válassza ki a  **+**  melletti ikon **állomásnév hozzáadása**.

![Adja hozzá a gazdagép neve](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be a teljesen minősített tartománynevét, egy CNAME rekordot, például a hozzáadott `www.contoso.com`. 

Válassza ki **érvényesítése**.

A **állomásnév hozzáadása** gomb aktiválásakor. 

Győződjön meg arról, hogy **állomásnév rekordtípus** értéke **CNAME (www.example.com vagy bármely altartomány)**.

Válassza ki **állomásnév hozzáadása**.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Ez eltarthat egy ideig, megjelennek az alkalmazás új gazdagép **egyéni tartományok** lap. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Ha nem talált meg egy lépést, vagy elgépelte valahol korábban, egy ellenőrzési hiba a lap alján látható.

![Ellenőrzési hiba](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Az A rekord leképezése

Az oktatóanyag példában a legfelső szintű tartomány az A rekord hozzáadása (például `contoso.com`). 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>Az alkalmazás IP-cím másolása

Képezi rekorddal, az alkalmazás külső IP-cím szükséges. Az IP-címet az alkalmazásban található **egyéni tartományok** oldal az Azure portálon.

Az alkalmazás oldal az Azure-portálon a bal oldali navigációs, válassza ki a **egyéni tartományok**. 

![Az egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az a **egyéni tartományok** lapon, az alkalmazás IP-cím másolásához.

![Az Azure alkalmazásba portálnavigációjával](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>Az A rekord létrehozása

Az A rekord leképezése egy alkalmazást, az App Service szükséges **két** DNS-rekordokat:

- Egy **A** rekord az alkalmazás IP-cím hozzárendelése.
- A **TXT** rekord leképezése az alkalmazás alapértelmezett állomásnév `<app_name>.azurewebsites.net`. App Service a rekord csak időben konfigurációs, győződjön meg arról, hogy Ön a tulajdonosa az egyéni tartomány használja. Miután az egyéni tartomány érvényesítve, és az App Service-ben konfigurált, törölheti a TXT-rekord. 

Az a `contoso.com` tartomány például az alábbi táblázat szerint és TXT-rekordok létrehozása (`@` a gyökértartomány általában jelöli). 

| Bejegyzéstípus | Gazdagép | Érték |
| - | - | - |
| A | `@` | IP-cím a [másolja az alkalmazás IP-cím](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

A rekordok kerülnek, ha a DNS-rekordok lapon néz ki a következő példa:

![DNS-rekordok lap](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>Az alkalmazásban, az A rekord hozzárendelésének engedélyezése

Vissza az alkalmazásban lévő **egyéni tartományok** oldalra az Azure portálon, adja hozzá a teljesen minősített egyéni DNS-nevét (például `contoso.com`) listájához.

Válassza ki a  **+**  melletti ikon **állomásnév hozzáadása**.

![Adja hozzá a gazdagép neve](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Írja be a teljesen minősített tartománynevét, az A rekordot, például a konfigurált `contoso.com`.

Válassza ki **érvényesítése**.

A **állomásnév hozzáadása** gomb aktiválásakor. 

Győződjön meg arról, hogy **állomásnév rekordtípus** értéke **egy olyan rekordot (example.com)**.

Válassza ki **állomásnév hozzáadása**.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Ez eltarthat egy ideig, megjelennek az alkalmazás új gazdagép **egyéni tartományok** lap. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![Egy hozzáadott bejegyzés](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Ha nem talált meg egy lépést, vagy elgépelte valahol korábban, egy ellenőrzési hiba a lap alján látható.

![Ellenőrzési hiba](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>A helyettesítő tartomány leképezése

Az oktatóanyag példában leképez egy [helyettesítő DNS-név](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (például `*.contoso.com`) az App Service alkalmazásba adja hozzá egy CNAME rekordot. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Adjon hozzá egy CNAME rekordot egy helyettesítő karakteres nevet hozzárendelése az alkalmazás alapértelmezett állomásnév (`<app_name>.azurewebsites.net`).

Az a `*.contoso.com` tartomány például a CNAME rekord felelteti meg a név `*` való `<app_name>.azurewebsites.net`.

Amikor a CNAME ad hozzá, a DNS-rekordok lapon néz ki a következő példa:

![Az Azure alkalmazásba portálnavigációjával](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>Az alkalmazásban a CNAME rekord hozzárendelésének engedélyezése

Mostantól hozzáadhatja azok bármely altartomány, amely megfelel a helyettesítő karakteres nevének az alkalmazáshoz (például `sub1.contoso.com` és `sub2.contoso.com` megfelelő `*.contoso.com`). 

Az alkalmazás oldal az Azure-portálon a bal oldali navigációs, válassza ki a **egyéni tartományok**. 

![Az egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Válassza ki a  **+**  melletti ikon **állomásnév hozzáadása**.

![Adja hozzá a gazdagép neve](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írjon be egy teljesen minősített tartománynevet, amely megfelel a helyettesítő tartomány (például `sub1.contoso.com`), majd válassza ki **ellenőrzése**.

A **állomásnév hozzáadása** gomb aktiválásakor. 

Győződjön meg arról, hogy **állomásnév rekordtípus** értéke **CNAME rekord (www.example.com vagy bármely altartomány)**.

Válassza ki **állomásnév hozzáadása**.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Ez eltarthat egy ideig, megjelennek az alkalmazás új gazdagép **egyéni tartományok** lap. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

Válassza ki a  **+**  ikonra újra egy másik állomásnév, amely megfelel a helyettesítő tartomány hozzáadásához. Adja hozzá például `sub2.contoso.com`.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>A böngészőben tesztelése

Keresse meg a korábban konfigurált DNS-nevek (például `contoso.com`, `www.contoso.com`, `sub1.contoso.com`, és `sub2.contoso.com`).

![Az Azure alkalmazásba portálnavigációjával](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-error-web-site-not-found"></a>Hárítsa el a "Nem található Web Site" 404-es hiba

Ha HTTP 404-es (nem található) hiba történt az egyéni tartomány URL-címét böngészésekor, ellenőrizze, hogy a tartomány oldja fel az alkalmazás IP-cím használatával <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Ha nem, akkor elképzelhető, hogy a következő okok egyikéből adódóan:

- A konfigurált egyéni tartomány hiányzik egy A rekordot és/vagy egy olyan CNAME rekordot.
- A böngészőalapú ügyfél gyorsítótárazott a régi IP-cím a tartomány. Kapcsolja ki a gyorsítótárat, és a vizsgálati DNS-feloldás újra. Windows-gépen, törli a gyorsítótár `ipconfig /flushdns`.

<a name="virtualdir"></a>

## <a name="direct-default-url-to-a-custom-directory"></a>Közvetlen alapértelmezett URL-cím, egy egyéni könyvtárba

Alapértelmezés szerint az App Service arra utasítja a webes kérelmek gyökérkönyvtárára mutatva, az alkalmazás kódját. Azonban bizonyos webes keretrendszerek ne indítsa a gyökérkönyvtárban. Például [Laravel](https://laravel.com/) indítja el a `public` alkönyvtár. A folytatáshoz a `contoso.com` DNS példa ilyen alkalmazás címen elérhető lenne `http://contoso.com/public`, de valójában történő közvetlen kívánt `http://contoso.com` való a `public` directory helyette. Ez a lépés nem tartalmaz, amely DNS-feloldás, de a virtuális könyvtár testreszabása.

Ehhez az szükséges, válassza ki a **Alkalmazásbeállítások** a bal oldali navigációs sáv a weblap alkalmazás. 

A lapon, a virtuális gyökérkönyvtár alján `/` mutat `site\wwwroot` alapértelmezés szerint ez az alkalmazás kódját gyökérkönyvtárában. Módosítsa úgy, hogy mutasson a `site\wwwroot\public` ehelyett a példában, és mentse a módosításokat. 

![A virtuális könyvtár testreszabása](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

A művelet után alkalmazást a jobb oldalon, a legfelső szintű (például http://contoso.com) elérési úton kell visszaadnia.

## <a name="automate-with-scripts"></a>Parancsfájlok automatizálásához

Automatizálható a parancsfájlok, az egyéni tartományok felügyeleti használatával a [Azure CLI](/cli/azure/install-azure-cli) vagy [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>Azure CLI 

A következő parancsot egy konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazást. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \ 
    --hostname <fully_qualified_domain_name> 
``` 

További információkért lásd: [egy egyéni tartomány leképezése a webes alkalmazás](scripts/app-service-cli-configure-custom-domain.md). 

### <a name="azure-powershell"></a>Azure PowerShell 

A következő parancsot egy konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazást. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

További információkért lásd: [rendelje hozzá az egyéni tartománynév a webes alkalmazás](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A CNAME rekord használatával altartomány térkép
> * Az A rekord használatával a legfelső szintű tartomány leképezése
> * Térkép egy helyettesítő tartomány CNAME rekord használatával
> * Tartomány leképezése parancsfájlokkal automatizálhatja

Továbblépés a következő oktatóanyag áttekintésével megismerheti, hogyan lehet kötést létrehozni egy egyéni SSL-tanúsítványt egy webalkalmazást.

> [!div class="nextstepaction"]
> [Egy meglévő egyéni SSL-tanúsítvány kötését az Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

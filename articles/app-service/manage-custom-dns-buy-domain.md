---
title: Egyéni tartománynév vásárlása
description: Megtudhatja, hogyan vásárolhat egy App Service tartományt, és hogyan használhatja egyéni tartományként az alkalmazás Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: 0723a3b0c96339ba86b98ab851c44d2a65e6f34b
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672381"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Egyéni tartománynév vásárlása Azure App Service

App Service tartományok olyan legfelső szintű tartományok, amelyek közvetlenül az Azure-ban kezelhetők. Megkönnyítik [Azure app Service](overview.md)egyéni tartományának kezelését. Ez az oktatóanyag bemutatja, hogyan vásárolhat egy App Service tartományt, és hogyan rendelhet hozzá DNS-neveket Azure App Servicehoz.

Azure-beli virtuális gép vagy Azure Storage esetében lásd: [app Service tartomány kiosztása Azure-beli virtuális géphez vagy Azure Storage-hoz](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Cloud Services esetében lásd: [Egyéni tartománynév konfigurálása Azure Cloud Service-hez](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [Hozzon létre egy App Service-alkalmazást](/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
* [Távolítsa el az előfizetés](../billing/billing-spending-limit.md#remove)költségkeretét. Ingyenes előfizetési Kredittel rendelkező App Service-tartományok nem vásárolhatók meg.

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ha Azure App Service szeretné használni az egyéni tartományokat, az alkalmazás [app Service csomagjának](https://azure.microsoft.com/pricing/details/app-service/) fizetős szintnek kell lennie (**közös**, **alapszintű**, **standard**vagy **prémium**). Ebben a lépésben ellenőrizze, hogy az alkalmazás a támogatott díjszabási szinten van-e.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Az alkalmazás megkeresése az Azure Portalon

A bal oldali menüben válassza az **App Services** lehetőséget, majd válassza ki az alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/select-app.png)

Megjelenik az App Service-alkalmazás felügyeleti oldala.  

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

Az alkalmazás lapjának bal oldali navigációs sávján görgessen a **Beállítások** szakaszra, és válassza ki a **Vertikális felskálázás (App Service-csomag)** elemet.

![Vertikális felskálázás menü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Győződjön meg arról, hogy az alkalmazás nem az **F1** szinten van. Az egyéni DNS nem támogatott az **F1** szinten. 

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Ha az App Service-csomag nem az **F1** -es szinten található, akkor a vertikális **felskálázás** oldal bezárásával ugorjon [a tartomány megvásárlása](#buy-the-domain)elemre.

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Apply** (Alkalmaz) gombra.

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

![Skálázási művelet megerősítése](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>A tartomány megvásárlása

### <a name="pricing-information"></a>Díjszabási információk
Azure App Service tartományokra vonatkozó díjszabási információkért tekintse meg a [app Service díjszabási oldalát](https://azure.microsoft.com/pricing/details/app-service/windows/) , és görgessen le app Service tartományhoz.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Nyissa meg az [Azure Portalt](https://portal.azure.com/), majd jelentkezzen be az Azure-fiókjával.

### <a name="launch-buy-domains"></a>A Buy Domains elindítása
A **app Services** lapon kattintson az alkalmazás nevére, válassza a **Beállítások**, majd az **Egyéni tartományok** elemet.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Az **Egyéni tartományok** lapon kattintson a **tartomány vásárlása**elemre.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Ha nem látja a **app Service tartományok** szakaszt, el kell távolítania az Azure-fiók költségkeretét (lásd: [Előfeltételek](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>A tartomány megvásárlásának konfigurálása

A **app Service tartomány** lap **Keresés tartományba** mezőjébe írja be a megvásárolni kívánt tartománynevet, és írja be a `Enter`. A javasolt elérhető tartományok csak a szövegmező alatt jelennek meg. Válasszon ki egy vagy több megvásárolni kívánt tartományt.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> App Service tartományok a következő [legfelső szintű tartományokat](https://wikipedia.org/wiki/Top-level_domain) támogatják: _com_, _net_, _Co.uk_, _org_, _nl_, _in_, _BIZ_, _org.uk_és _Co.in_.
>
>

Kattintson a **kapcsolattartási adatok** elemre, és töltse ki a tartomány kapcsolattartási adatai űrlapot. Ha elkészült, kattintson **az OK** gombra a app Service tartomány lapra való visszatéréshez.

Fontos, hogy az összes kötelező mezőt kitöltse a lehető legnagyobb pontossággal. A kapcsolattartási adatok helytelen adatai miatt nem lehet megvásárolni a tartományokat.

Ezután válassza ki a tartományhoz a kívánt beállításokat. A magyarázatokat a következő táblázat tartalmazza:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|Adatvédelem | Engedélyezés | Ha az "Adatvédelem" lehetőséget választja, az _ingyenes_vásárlási díj részét képezi. Néhány legfelső szintű tartományt a regisztrátorok felügyelnek, amelyek nem támogatják az adatvédelem védelmét, és az **Adatvédelem** lapon vannak felsorolva. |
| Alapértelmezett állomásnevek kiosztása | **www** és **\@** | Szükség esetén válassza ki a kívánt állomásnév-kötéseket. A tartományi megvásárlási művelet befejezésekor az alkalmazás a kiválasztott gazdagépeken is elérhető. Ha az alkalmazás az [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)mögött van, nem jelenik meg a gyökértartomány (@) hozzárendelésének lehetősége, mert Traffic Manager nem támogatja a rekordokat. A tartománynév-hozzárendelések módosítása a tartomány megvásárlása után végezhető el. |

### <a name="accept-terms-and-purchase"></a>Feltételek és vásárlás elfogadása

A feltételek és a díjak áttekintéséhez kattintson a **jogi feltételek** elemre, majd kattintson a **vásárlás**elemre.

> [!NOTE]
> App Service tartományok a GoDaddy használatával tartományi regisztrációt használnak, és Azure DNS a tartományok üzemeltetéséhez. A tartományi regisztrációs díj mellett a Azure DNS használati díjai is érvényesek. További információ: [Azure DNS díjszabása](https://azure.microsoft.com/pricing/details/dns/).
>
>

Vissza a **app Service tartomány** lapon kattintson **az OK**gombra. Amíg a művelet folyamatban van, a következő értesítések jelennek meg:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Az állomásnevek tesztelése

Ha az alkalmazáshoz az alapértelmezett állomásnevek vannak hozzárendelve, akkor az egyes kiválasztott állomásnévről is megjelenik egy sikeres értesítés.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

A kiválasztott állomásnevek a Custom **Domains** (egyéni tartományok) lapon, az **Egyéni állomásnevek** szakaszban is láthatók.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nincs SSL-tanúsítvány kötve, és a böngészőtől az egyéni tartományba IRÁNYULó HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. Az SSL-kötés konfigurálásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).
>

A gazdagépek teszteléséhez navigáljon a felsorolt gazdagépekhez a böngészőben. Az előző képernyőképen található példában próbálja meg navigálni a _kontoso.net_ és a _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Állomásnévk társítása az alkalmazáshoz

Ha úgy dönt, hogy nem rendel hozzá egy vagy több alapértelmezett állomásnevet az alkalmazáshoz a vásárlási folyamat során, vagy ha nem a listához kell rendelnie egy állomásnevet, bármikor hozzárendelhet egy állomásnevet.

A App Service tartományban lévő gazdagépeket más alkalmazásokhoz is hozzárendelheti. A lépések attól függnek, hogy a App Service tartomány és az alkalmazás ugyanahhoz az előfizetéshez tartozik-e.

- Eltérő előfizetés: rendelje hozzá az egyéni DNS-rekordokat a App Service tartományból az alkalmazáshoz, például egy külsőleg megvásárolt tartományhoz. Az egyéni DNS-nevek App Service tartományhoz való hozzáadásával kapcsolatos információkért lásd: [Egyéni DNS-rekordok kezelése](#custom). Ha egy külső megvásárolt tartományt szeretne hozzárendelni egy alkalmazáshoz, tekintse [meg a meglévő egyéni DNS-név leképezése Azure app Service](app-service-web-tutorial-custom-domain.md). 
- Azonos előfizetés: kövesse az alábbi lépéseket.

### <a name="launch-add-hostname"></a>Az állomásnév hozzáadásának indítása
A **app Services** lapon válassza ki annak az alkalmazásnak a nevét, amelyhez állomásnévket szeretne hozzárendelni, válassza a **Beállítások**, majd az **Egyéni tartományok**elemet.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Győződjön meg arról, hogy a megvásárolt tartomány szerepel a **app Service tartományok** szakaszban, de ne jelölje ki. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Az adott előfizetésben lévő összes App Service tartomány megjelenik az alkalmazás **Egyéni tartományok** lapján. Ha a tartomány az alkalmazás előfizetésében szerepel, de nem jelenik meg az alkalmazás **Egyéni tartományok** lapján, próbálja meg újból megnyitni az **Egyéni tartományok** lapot, vagy frissítse a weblapot. Emellett tekintse meg az értesítési harangot a Azure Portal tetején a folyamatjelző vagy a létrehozási hibákért.
>
>

Válassza a **Gazdagépnév hozzáadása** lehetőséget.

### <a name="configure-hostname"></a>Állomásnév konfigurálása
Az **állomásnév hozzáadása** párbeszédpanelen írja be a app Service tartomány vagy bármely altartomány teljes tartománynevét. Példa:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Ha elkészült, válassza az **Érvényesítés**lehetőséget. Az állomásnév bejegyzéstípus automatikusan ki lesz választva.

Válassza a **Gazdagépnév hozzáadása** lehetőséget.

Ha a művelet befejeződött, megjelenik egy sikeres értesítés a hozzárendelt állomásnévről.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Állomásnév hozzáadásának lezárása
Az **állomásnév hozzáadása** lapon a kívánt módon rendeljen hozzá egy másik gazdagépet az alkalmazáshoz. Ha elkészült, az **Add hostname (állomásnév hozzáadása** ) oldal bezárásához.

Ekkor az újonnan hozzárendelt állomásnév (eke) t kell látnia az alkalmazás **Egyéni tartományok** lapján.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Az állomásnevek tesztelése

Navigáljon a felsorolt gazdagépekhez a böngészőben. Az előző képernyőképen található példában próbálja meg navigálni a _ABC.kontoso.net_.

## <a name="renew-the-domain"></a>A tartomány megújítása

A megvásárolt App Service tartomány a vásárlás időpontjában egy évig érvényes. Alapértelmezés szerint a tartomány úgy van konfigurálva, hogy automatikusan megújítsa a fizetési módot a következő évre. A tartománynevet manuálisan is megújíthatja.

Ha ki szeretné kapcsolni az automatikus megújítást, vagy ha manuálisan szeretné megújítani a tartományt, kövesse az alábbi lépéseket.

A **app Services** lapon kattintson az alkalmazás nevére, válassza a **Beállítások**, majd az **Egyéni tartományok**elemet.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

A **app Service tartományok** szakaszban válassza ki a konfigurálni kívánt tartományt.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

A tartomány bal oldali navigációs sávján válassza a **tartomány megújítása**lehetőséget. Ha nem szeretné automatikusan megújítani a tartományt, válassza a **kikapcsolva**, majd a **Mentés**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

A tartomány manuális megújításához válassza a **tartomány megújítása**lehetőséget. Ez a gomb azonban [a tartomány lejárata előtt 90 nappal](#when-domain-expires)nem aktív.

Ha a tartomány megújítása sikeres, a rendszer 24 órán belül értesítést küld e-mailben.

## <a name="when-domain-expires"></a>Ha a tartomány lejár

Az Azure a következő módon bánik a lejáró vagy lejárt App Service tartománnyal:

* Ha az automatikus megújítás le van tiltva: 90 nappal a tartomány lejárata előtt, a rendszer elküldi a megújítási értesítő e-mailt, és a **megújítási tartomány** gomb aktiválva lesz a portálon.
* Ha engedélyezve van az automatikus megújítás: a tartomány lejárati dátuma utáni napon az Azure megpróbál kiszámlázni a tartománynév megújítása után.
* Ha hiba történik az automatikus megújítás során (például a kártyán a fájl lejárt), vagy ha az automatikus megújítás le van tiltva, és lehetővé teszi, hogy a tartomány lejárjon, az Azure értesíti a tartomány lejáratáról és a tartománynevet. A tartományt [manuálisan is megújíthatja](#renew-the-domain) .
* Az Azure a lejárat után a 4. és a 12. napon belül további értesítő e-maileket küld. A tartományt [manuálisan is megújíthatja](#renew-the-domain) .
* A lejárat utáni 19. napon a tartomány továbbra is érvényben marad, de a rendszer beváltási díjat számít fel. Az ügyfélszolgálat meghívásával megújíthatja a tartománynevet, a megújítási és beváltási díjakra is érvényes.
* Az Azure a lejárat utáni 25. napon üzembe helyezi az árverést a tartománynév-ágazati aukciós szolgáltatással. Az ügyfélszolgálat meghívásával megújíthatja a tartománynevet, a megújítási és beváltási díjakra is érvényes.
* A lejárat után 30 napon belül már nem tudja beváltani a tartományt.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Egyéni DNS-rekordok kezelése

Az Azure-ban a App Service tartomány DNS-rekordjait [Azure DNS](https://azure.microsoft.com/services/dns/)használatával felügyeli a rendszer. Hozzáadhat, eltávolíthat és frissíthet DNS-rekordokat, ugyanúgy, mint a külsőleg megvásárolt tartományhoz.

### <a name="open-app-service-domain"></a>App Service tartomány megnyitása

A Azure Portal bal oldali menüjében válassza a **minden szolgáltatás** > **app Service tartományok**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Válassza ki a kezelni kívánt tartományt. 

### <a name="access-dns-zone"></a>Hozzáférés a DNS-zónához

A tartomány bal oldali menüjében válassza a **DNS-zóna**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Ez a művelet megnyitja a App Service tartományának [DNS-zóna](../dns/dns-zones-records.md) lapját Azure DNS. További információ a DNS-rekordok szerkesztéséről: [DNS-zónák kezelése a Azure Portalban](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Vásárlás megszakítása (tartomány törlése)

Miután megvásárolta a App Service tartományt, öt napja van, hogy megszakítsa a vásárlást teljes visszatérítésre. Öt nap elteltével törölheti a App Service tartományt, de nem kaphat visszatérítést.

### <a name="open-app-service-domain"></a>App Service tartomány megnyitása

A Azure Portal bal oldali menüjében válassza a **minden szolgáltatás** > **app Service tartományok**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Válassza ki a megszakítani vagy törölni kívánt tartományt. 

### <a name="delete-hostname-bindings"></a>Állomásnév-kötések törlése

A tartomány bal oldali menüjében válassza az **állomásnév-kötések**lehetőséget. Az összes Azure-szolgáltatás állomásnév-kötéseit itt találja.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

A App Service tartományt csak akkor törölheti, ha az összes állomásnév-kötés törölve lett.

Az egyes állomásnév-kötések törléséhez válassza a **...**  > **Törlés**lehetőséget. Az összes kötés törlése után válassza a **Mentés**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Megszakítás vagy törlés

A tartomány bal oldali menüjében válassza az **Áttekintés**lehetőséget. 

Ha a megvásárolt tartomány megszakítási időszaka nem telt el, válassza a **vásárlás megszakítása**lehetőséget. Ellenkező esetben a Delete ( **Törlés** ) gomb jelenik meg. Ha a tartományt visszatérítés nélkül szeretné törölni, válassza a **Törlés**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

A művelet megerősítéséhez válassza az **Igen**lehetőséget.

A művelet befejezése után a tartományt a rendszer az előfizetésből bocsátja ki, és bárki számára elérhetővé válik. 

## <a name="direct-default-url-to-a-custom-directory"></a>Egyéni könyvtár közvetlen alapértelmezett URL-címe

Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Ha egy alkönyvtárba szeretné irányítani őket (például `public`), tekintse meg az [alapértelmezett URL-cím egyéni könyvtárba való közvetlen elérését](app-service-web-tutorial-custom-domain.md#virtualdir)ismertető témakört.

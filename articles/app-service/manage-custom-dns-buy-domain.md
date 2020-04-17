---
title: Egyéni tartománynév vásárlása
description: Megtudhatja, hogyan vásárolhat egy App Service-tartományt, és hogyan használhatja azt egyéni tartományként az alkalmazás Azure App Service-hez.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: 47daf4ecd034c390a1460610e78d4fffd9277ac7
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535706"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Egyéni tartománynév vásárlása az Azure App Service-hez

Az App Service-tartományok olyan legfelső szintű tartományok, amelyek et közvetlenül az Azure-ban kezelik. Megkönnyítik az [Azure App Service](overview.md)egyéni tartományainak kezelését. Ez az oktatóanyag bemutatja, hogyan vásárolhat egy App Service-tartományt, és hogyan rendelhet DNS-neveket az Azure App Service-hez.

Az Azure VM vagy az Azure Storage, [lásd: App Service-tartomány hozzárendelése az Azure VM vagy az Azure Storage.](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage) A Felhőszolgáltatásokról az [Azure-felhőszolgáltatás egyéni tartománynevének konfigurálása](../cloud-services/cloud-services-custom-domain-name-portal.md)című témakörben található.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [Hozzon létre egy App Service-alkalmazást](/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
* [Távolítsa el az előfizetés költségkeretét.](../cost-management-billing/manage/spending-limit.md#remove) Ingyenes előfizetési kreditekkel nem vásárolhat App Service-tartományokat.

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Az Azure App Service egyéni tartományainak használatához az alkalmazás [Alkalmazásszolgáltatási csomagjának](https://azure.microsoft.com/pricing/details/app-service/) fizetős szintnek (**megosztott,** **alapszintű,** **standard**vagy **prémium szintűnek)** kell lennie. Ebben a lépésben győződjön meg arról, hogy az alkalmazás a támogatott tarifacsomag.

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

Ha az App Service-csomag nem szerepel az **F1-csomagban,** zárja be a **Felskálázás** lapot, és ugorjon a tartomány [megvásárlása](#buy-the-domain)lapra.

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

![Skálázási művelet megerősítése](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>A tartomány megvásárlása

### <a name="pricing-information"></a>Árképzési információk
Az Azure App Service-tartományok díjszabási információkért látogasson el az [App Service díjszabási lapjára,](https://azure.microsoft.com/pricing/details/app-service/windows/) és görgessen le az App Service-tartományhoz.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Nyissa meg az [Azure Portalt](https://portal.azure.com/), majd jelentkezzen be az Azure-fiókjával.

### <a name="launch-buy-domains"></a>Vásárlás indítása tartományok
Az **App Services** lapon kattintson az alkalmazás nevére, válassza a **Beállítások**lehetőséget, majd válassza az Egyéni **tartományok lehetőséget.**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Az **Egyéni tartományok** lapon kattintson a **Tartomány vásárlása gombra.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Ha nem látja az **App Service-tartományok szakaszt,** el kell távolítania a költségkeretet az Azure-fiókjából (lásd: [Előfeltételek).](#prerequisites)
>
>

### <a name="configure-the-domain-purchase"></a>A tartományvásárlás konfigurálása

Az **App Service-tartomány** lap **Tartomány keresése** mezőjébe írja be a megvásárolni kívánt tartománynevet, és írja be a mezőbe. `Enter` A javasolt elérhető tartományok a szövegdoboz alatt jelennek meg. Jelöljön ki egy vagy több megvásárolni kívánt tartományt.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Az App [Service-tartományok](https://wikipedia.org/wiki/Top-level_domain) a következő legfelső szintű tartományokat támogatják: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_és _co.in_.
>
>

Kattintson a **Névjegyadatok elemre,** és töltse ki a tartomány kapcsolattartási adatait. Ha végzett, kattintson az **OK** gombra az App Service-tartomány lapra való visszatéréshez.

Fontos, hogy az összes szükséges mezőt a lehető legpontosabban töltse ki. A kapcsolattartási adatok helytelen adatai a tartományok megvásárlásának sikertelenségéhez vezethetnek.

Ezután válassza ki a kívánt beállításokat a tartományhoz. Az alábbi táblázatban a következő táblázat ban megkeresve a következő magyarázatokat:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|Adatvédelem | Bekapcsolás | Jelentkezzen a "Adatvédelem", amely tartalmazza a vételár _ingyen_. Egyes legfelső szintű tartományokat olyan regisztrátorok kezelnek, akik nem támogatják az adatvédelmet, és az **Adatvédelmi** oldalon jelennek meg. |
| Alapértelmezett állomásnevek hozzárendelése | **www** és**\@** | Szükség esetén válassza ki a kívánt állomásnév-kötéseket. Amikor a tartományvásárlási művelet befejeződött, az alkalmazás elérhető a kiválasztott állomásneveken. Ha az alkalmazás az [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)mögött található, nem látja a gyökértartomány (@) hozzárendelésének lehetőségét, mivel a Traffic Manager nem támogatja az A rekordokat. A tartományvásárlás befejezése után módosíthatja az állomásnév-hozzárendeléseket. |

### <a name="accept-terms-and-purchase"></a>Feltételek és vásárlás elfogadása

Kattintson a **Jogi feltételek gombra** a feltételek és a díjak áttekintéséhez, majd kattintson a **Vásárlás**gombra.

> [!NOTE]
> Az App Service-tartományok a GoDaddy segítségével regisztrálják a tartományokat, az Azure DNS pedig a tartományokat. A tartomány regisztrációs díja mellett az Azure DNS használati díjai is érvényesek. További információt az [Azure DNS-díjszabás című témakörben](https://azure.microsoft.com/pricing/details/dns/)talál.
>
>

Az **App Service-tartomány** lapon kattintson az **OK**gombra. Aművelet folyamatban van, a következő értesítések jelennek meg:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Az állomásnevek tesztelése

Ha alapértelmezett állomásneveket rendelt az alkalmazáshoz, akkor minden egyes kiválasztott állomásnévhez megjelenik egy sikeres értesítés is.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

A kijelölt állomásnevek az **Egyéni tartományok** lapon, az **Egyéni állomásnevek** szakaszban is megjelennek.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nincs TLS/SSL-tanúsítványhoz kötve, és a böngészőből az egyéni tartományba érkező HTTPS-kérelmek a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. A TLS-kötés konfigurálásához olvassa [el Az egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel az Azure App Service szolgáltatásban című témakört.](configure-ssl-bindings.md)
>

Az állomásnevek teszteléséhez keresse meg a böngészőben a felsorolt állomásneveket. Az előző képernyőképen látható példában próbáljon meg _navigálni a kontoso.net_ és a www _\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Állomásnevek hozzárendelése az alkalmazáshoz

Ha úgy dönt, hogy a vásárlási folyamat során nem rendel egy vagy több alapértelmezett állomásnevet az alkalmazáshoz, vagy ha nem szerepel a listában, bármikor hozzárendelhet egy állomásnevet.

Az App Service-tartományban állomásneveket is hozzárendelhet bármely más alkalmazáshoz. A lépések attól függnek, hogy az App Service-tartomány és az alkalmazás ugyanahhoz az előfizetéshez tartozik-e.

- Különböző előfizetések: Rendeljen egyéni DNS-rekordokat az App Service-tartományból az alkalmazáshoz, például egy külsőleg megvásárolt tartományhoz. Az egyéni DNS-nevek App Service-tartományhoz való hozzáadásáról az [Egyéni DNS-rekordok kezelése című](#custom)témakörben talál további információt. Ha külső megvásárolt tartományt szeretne leképezni egy alkalmazáshoz, olvassa el [a Meglévő egyéni DNS-név hozzárendelése az Azure App Service szolgáltatáshoz című témakört.](app-service-web-tutorial-custom-domain.md) 
- Ugyanaz az előfizetés: Kövesse a következő lépéseket.

### <a name="launch-add-hostname"></a>Indítás állomásnév hozzáadása
Az **App Services** lapon jelölje ki annak az alkalmazásnak a nevét, amelyhez állomásneveket szeretne rendelni, válassza a **Beállítások**lehetőséget, majd válassza az **Egyéni tartományok**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Győződjön meg arról, hogy a megvásárolt tartomány szerepel az **App Service-tartományok** szakaszban, de ne jelölje ki. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Az ugyanabban az előfizetésben lévő összes App Service-tartomány megjelenik az alkalmazás **egyéni tartományok** lapján. Ha a tartomány az alkalmazás előfizetésében van, de nem látható az alkalmazás **Egyéni tartományok** lapján, próbálja meg újra megnyitni az **Egyéni tartományok** lapot, vagy frissítse a weblapot. Emellett ellenőrizze az értesítési csengő tetején az Azure Portalon a folyamat előrehaladását vagy a létrehozási hibákat.
>
>

Válassza a **Gazdagépnév hozzáadása** lehetőséget.

### <a name="configure-hostname"></a>Állomásnév konfigurálása
Az **Állomásnév hozzáadása** párbeszédpanelen írja be az App Service-tartomány vagy bármely altartomány teljesen minősített tartománynevét. Például:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Ha végzett, válassza **az Érvényesítés**lehetőséget. A rendszer automatikusan kiválasztja az állomásnév bejegyzés típusát.

Válassza a **Gazdagépnév hozzáadása** lehetőséget.

Amikor a művelet befejeződött, megjelenik egy sikeres értesítést a hozzárendelt állomásnév.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Állomásnév hozzáadása bezárása
A **Hostname hozzáadása** lapon igény szerint rendeljen hozzá bármely más állomásnevet az alkalmazáshoz. Ha végzett, zárja be a **HostName hozzáadása** lapot.

Most az alkalmazás egyéni tartományok lapján kell látnia az újonnan hozzárendelt **állomásnév(eke)t.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Az állomásnevek tesztelése

Keresse meg a böngészőben a felsorolt állomásneveket. Az előző képernyőképen látható példában próbáljon meg _navigálni a abc.kontoso.net_.

## <a name="renew-the-domain"></a>A tartomány megújítása

A megvásárolt App Service-tartomány a vásárlás időpontjától egy évig érvényes. Alapértelmezés szerint a tartomány úgy van beállítva, hogy automatikusan megújuljon a következő évre vonatkozó fizetési mód felszámításával. A tartománynevet manuálisan is megújíthatja.

Ha ki szeretné kapcsolni az automatikus megújítást, vagy manuálisan szeretné megújítani a tartományt, kövesse az itt leírt lépéseket.

Az **App Services** lapon kattintson az alkalmazás nevére, válassza a **Beállítások**lehetőséget, majd válassza az **Egyéni tartományok**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Az **App Service-tartományok csoportban** jelölje ki a konfigurálni kívánt tartományt.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

A tartomány bal oldali navigációs sávján válassza a **Tartomány megújítása**lehetőséget. A tartomány automatikus megújításának leállításához válassza **a Ki**lehetőséget, majd a **Mentés lehetőséget.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

A tartomány manuális megújításához válassza **a Tartomány megújítása**lehetőséget. Ez a gomb azonban csak [90 nappal a tartomány lejárata előtt](#when-domain-expires)aktív.

Ha a tartomány megújítása sikeres, 24 órán belül e-mailben értesítést kap.

## <a name="when-domain-expires"></a>A tartomány lejártakor

Az Azure az alábbiak szerint foglalkozik a lejáró vagy lejárt App Service-tartományokkal:

* Ha az automatikus megújítás le van tiltva: 90 nappal a tartomány lejárata előtt a rendszer megújítási értesítő e-mailt küld Önnek, és **aktiválja** a Tartomány megújítása gombot a portálon.
* Ha az automatikus megújítás engedélyezve van: A tartomány lejárati dátumát követő napon az Azure megpróbálja kiszámlázni a tartománynév-megújítást.
* Ha hiba történik az automatikus megújítás során (például a fájlban lévő kártya lejárt), vagy ha az automatikus megújítás le van tiltva, és engedélyezi a tartomány lejáratát, az Azure értesíti a tartomány lejárati és leparkolja a tartomány nevét. A [tartományt manuálisan is megújíthatja.](#renew-the-domain)
* A lejárat utáni 4. A [tartományt manuálisan is megújíthatja.](#renew-the-domain)
* A lejárat utáni 19. A tartománynév megújításához hívhatja az ügyfélszolgálatot, a vonatkozó megújítási és beváltási díjaktól függően.
* A lejárat utáni 25. A tartománynév megújításához hívhatja az ügyfélszolgálatot, a vonatkozó megújítási és beváltási díjaktól függően.
* A lejárat utáni 30.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Egyéni DNS-rekordok kezelése

Az Azure-ban az App Service-tartomány DNS-rekordjait az Azure DNS-en keresztül kezeli a [probléma.](https://azure.microsoft.com/services/dns/) Dns-rekordokat ugyanúgy hozzáadhat, távolíthat el és frissíthet, mint egy külsőleg megvásárolt tartományesetében.

### <a name="open-app-service-domain"></a>Alkalmazásszolgáltatás-tartomány megnyitása

Az Azure Portalbal a bal oldali menüben válassza az **Összes szolgáltatás** > **alkalmazásszolgáltatás-tartományt.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Válassza ki a kezelni kívánt tartományt. 

### <a name="access-dns-zone"></a>Access DNS-zóna

A tartomány bal oldali menüjében válassza a **DNS-zóna**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Ez a művelet megnyitja az App Service-tartomány [DNS-zónalapját](../dns/dns-zones-records.md) az Azure DNS-ben. A DNS-rekordok szerkesztésével kapcsolatos további tudnivalókért olvassa el [a DNS-zónák kezelése az Azure Portalon című témakört.](../dns/dns-operations-dnszones-portal.md)

## <a name="cancel-purchase-delete-domain"></a>Vásárlás visszavonása (tartomány törlése)

Az App Service-tartomány megvásárlása után öt napja van arra, hogy a teljes visszatérítéshez törölje a vásárlást. Öt nap elteltével törölheti az App Service-tartományt, de nem kaphat visszatérítést.

### <a name="open-app-service-domain"></a>Alkalmazásszolgáltatás-tartomány megnyitása

Az Azure Portalbal a bal oldali menüben válassza az **Összes szolgáltatás** > **alkalmazásszolgáltatás-tartományt.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Jelölje ki a törölni vagy törölni kívánt tartományt. 

### <a name="delete-hostname-bindings"></a>Állomásnév-kötések törlése

A tartomány bal oldali menüjében válassza a **Hostname kötések**lehetőséget. Az összes Azure-szolgáltatás állomásnév-kötései itt vannak felsorolva.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Az App Service-tartomány nem törölhető, amíg az összes állomásnév-kötés t nem törli.

Törölje az egyes állomásnév-kötést **a ...**  >  **Törölje**a gombot. Az összes kötés törlése után válassza a **Mentés gombot.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Mégse vagy törlés

A tartomány bal oldali menüjében válassza **az Áttekintés**lehetőséget. 

Ha a megvásárolt tartomány lemondási időszaka még nem telt el, válassza **a Vásárlás visszavonása lehetőséget.** Ellenkező esetben a **Törlés** gomb jelenik meg. Ha visszatérítés nélkül szeretné törölni a tartományt, válassza a **Törlés**lehetőséget.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

A művelet megerősítéséhez válassza az **Igen**lehetőséget.

A művelet befejezése után a tartomány felszabadul az előfizetésből, és bárki számára elérhető, hogy újra megvásárolhatja. 

## <a name="direct-default-url-to-a-custom-directory"></a>Egyéni könyvtár közvetlen alapértelmezett URL-címe

Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Ha egy alkönyvtárba szeretné `public`irányítani őket, például a közvetlen [alapértelmezett URL-cím egy egyéni könyvtárba](app-service-web-tutorial-custom-domain.md#virtualdir)című témakörben olvashat.

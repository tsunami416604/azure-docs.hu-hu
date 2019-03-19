---
title: Vásároljon egyéni tartománynevet az Azure-ban – App Service-ben
description: Ismerje meg, hogyan-webalkalmazás létrehozása az Azure App Service-ben az egyéni tartománynév vásárlása.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0c59e892c8fd5a8bcc74d23e16eaabf1dc1a08f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121536"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Az Azure App Service egyéni tartománynév vásárlása

App Service-tartományok (előzetes verzió) olyan legfelső szintű tartományok, közvetlenül az Azure-ban kezelt. Megkönnyítik az egyéni tartományainak felügyeletét [Azure App Service](overview.md). Ez az oktatóanyag bemutatja, hogyan App Service-tartományok vásárlása és DNS-nevek hozzárendelése az Azure App Service-ben.

Azure virtuális gépek és Azure Storage: [rendelje hozzá az App Service-tartomány Azure virtuális Gépen vagy az Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). A Cloud Services esetében lásd: [Azure cloud Services számára egyéni tartománynév beállítása](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [Hozzon létre egy App Service-alkalmazást](/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
* [Az előfizetés a költségkeret](../billing/billing-spending-limit.md#remove). App Service-tartományok együtt ingyenes előfizetési kreditek, nem vásárolhat.

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Egyéni tartományok az Azure App Service, az alkalmazás által használandó [App Service-csomag](https://azure.microsoft.com/pricing/details/app-service/) fizetős csomagra kell lennie (**megosztott**, **alapszintű**, **Standard**, vagy  **Prémium szintű**). Ebben a lépésben, győződjön meg arról, hogy az alkalmazást a támogatott tarifacsomagban van.

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

Ha az App Service-csomag nem található a **F1** szint, zárjon be a **vertikális felskálázás** lapon, és ugorjon [a tartomány vásárlása](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

![Skálázási művelet megerősítése](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>A tartomány vásárlása

### <a name="pricing-information"></a>Díjszabási információk
Díjszabási információk az Azure App Service-tartományok, látogasson el a [App Service díjszabási oldalát](https://azure.microsoft.com/pricing/details/app-service/windows/) , és görgessen le az App Service-tartomány.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Nyissa meg az [Azure Portalt](https://portal.azure.com/), majd jelentkezzen be az Azure-fiókjával.

### <a name="launch-buy-domains"></a>Indítsa el a vásárlás tartományok
Az a **App Services** lapra, majd az alkalmazást, válassza a neve **beállítások**, majd válassza ki **egyéni tartományok**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Az a **egyéni tartományok** kattintson **tartomány vásárlása**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Ha nem látja a **App Service-tartományok** szakaszban, el kell távolítania a költségkeretet, az Azure-fiókjával (lásd: [Előfeltételek](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurálja a tartomány vásárlása

Az a **App Service-tartomány** lap a **tartomány keresése** mezőbe írja be a tartománynév vásárlása és írja be a kívánt `Enter`. A javasolt elérhető tartományok a szövegmező alatt jelennek meg. Válassza ki egy vagy több tartományt szeretne vásárolni.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> A következő [legfelső szintű tartományok](https://wikipedia.org/wiki/Top-level_domain) támogatottak az App Service-tartományok: _com_, _nettó_, _co.uk_, _szervezeti_, _nl_, _a_, _biz_, _org.uk_, és _co.in_.
>
>

Kattintson a **kapcsolattartási adatai** és a tartomány kapcsolattartási adatait űrlap kitöltésekor. Ha befejezte, kattintson a **OK** az App Service-tartomány lapra való visszatéréshez.

Fontos a lehető legnagyobb pontosságú minden kötelező mezőt kitöltötte. Helytelen adatok a kapcsolattartási vásárolhat hibát eredményezhet.

Ezután válassza ki a kívánt beállításokat a tartomány. Tekintse meg az alábbi táblázatban találja:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|Adatvédelem | Bekapcsolás | Részvétel a "Adatvédelem", amely szerepel a vételár _ingyenes_. Néhány legfelső szintű tartományok, amelyek nem támogatják az adatvédelmet regisztráló szervezetek által kezelt, és ezek jelennek meg a **adatvédelmet** lapot. |
| Alapértelmezett állomásnevek hozzárendelése | **www** és **\@** | Ha szükséges, válassza ki a kívánt gazdagépnévvel kötéseket. A tartomány beszerzési művelet befejeződése után az alkalmazás a kijelölt állomásnevek címen érhető el. Ha az alkalmazás mögötti [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), nem jelenik meg a beállítást, a gyökértartomány hozzárendelése (@), mert a Traffic Manager does nem támogatja A-rekordokat. A tartomány vásárlás befejezése után módosíthatja a hostname-hozzárendeléseket. |

### <a name="accept-terms-and-purchase"></a>Fogadja el a feltételeket, és vásárolja meg

Kattintson a **jogi feltételek** tekintse át a feltételeket és a költségek, majd kattintson a **vásárlása**.

> [!NOTE]
> App Service-tartományok GoDaddy tartomány regisztráció és az Azure DNS segítségével a tartományok. A tartomány regisztrációs díj mellett az Azure DNS számára a használati díjak érvényesek. További információ: [Azure DNS díjszabási](https://azure.microsoft.com/pricing/details/dns/).
>
>

Térjen vissza a **App Service-tartomány** kattintson **OK**. Amíg a művelet folyamatban van, a következő értesítés jelenik meg:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>A gazdanév tesztelése

Az alkalmazás alapértelmezett állomásnevek rendelt, ha minden kiválasztott gazdagép sikeres értesítés is megjelenik. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

A kiválasztott gazdanév található is megjelenik a **egyéni tartományok** lap a **egyéni állomásnevek** szakaszban. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

A gazdanév teszteléséhez nyissa meg a listában szereplő állomásnevek a böngészőben. A példa az előző képernyőképen látható, próbálja meg úgy ellenőrizheti, hogy _kontoso.net_ és _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Alkalmazás állomásnevek hozzárendelése

Ha úgy dönt, hogy nem az alkalmazás egy vagy több alapértelmezett állomásnevek hozzárendelése a vásárlás során, vagy ha hozzá kell rendelnie egy állomásnév nem szerepel a listán, hozzárendelheti egy állomásnevet, bármikor.

Gazdanév található az App Service-tartomány bármely más alkalmazáshoz is rendelhet. A lépései attól függnek, hogy az App Service-tartomány és az alkalmazás tartozik ugyanahhoz az előfizetéshez.

- Másik előfizetésbe: Térkép egyéni DNS-rekordok az App Service-tartomány az alkalmazás, például egy kívülről vásárolt tartományhoz. Információ az egyéni DNS-neveket adhat hozzá egy App Service-tartomány: [egyéni DNS-rekordok kezelése](#custom). Külső megvásárolt tartomány hozzárendelése egy alkalmazáshoz, tekintse meg [meglévő egyéni DNS-név leképezése az Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Ugyanabban az előfizetésben: Kövesse az alábbi lépéseket.

### <a name="launch-add-hostname"></a>Indítsa el a gazdagépnév hozzáadása
Az a **App Services** lapra, jelölje be az állomásnevek, válassza ki a hozzárendelni kívánt alkalmazás nevét **beállítások**, majd válassza ki **egyéni tartományok**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Győződjön meg arról, hogy a megvásárolt tartomány szerepel az **App Service-tartományok** szakaszt, de ne válassza ki. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Minden App Service-tartományok ugyanabban az előfizetésben látható az alkalmazás **egyéni tartományok** lapot. Ha a tartomány az alkalmazás-előfizetés, de nem látja az alkalmazások **egyéni tartományok** lapon, próbálja meg újból megnyitni a **egyéni tartományok** oldalon, vagy frissítse a weblapot. Emellett ellenőrizze az értesítési csengőre folyamatban vagy létrehozási hibák az Azure portal tetején.
>
>

Válassza a **Gazdagépnév hozzáadása** lehetőséget.

### <a name="configure-hostname"></a>Állomásnév konfigurálása
Az a **gazdagépnév hozzáadása** párbeszédpanelen írja be az App Service-tartomány vagy bármely altartomány teljesen minősített tartománynevét. Példa:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Amikor végzett, válassza ki a **ellenőrzése**. A gazdagépnév rekordtípusa automatikusan ki van jelölve, az Ön számára.

Válassza a **Gazdagépnév hozzáadása** lehetőséget.

A művelet befejeződése után a hozzárendelt gazdagép sikeres értesítés jelenik meg.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Zárja be, gazdagépnév hozzáadása
Az a **gazdagépnév hozzáadása** lapon, a bármely más állomásnév hozzárendelése az alkalmazáshoz, igény szerint. Amikor végzett, zárja be a **gazdagépnév hozzáadása** lapot.

Az alkalmazás ekkor megjelenik az újonnan hozzárendelt hostname(s) **egyéni tartományok** lapot.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>A gazdanév tesztelése

Keresse meg a listában szereplő állomásnevek a böngészőben. A példa az előző képernyőképen látható, próbálja meg úgy ellenőrizheti, hogy _abc.kontoso.net_.

## <a name="renew-the-domain"></a>A tartomány megújítása

Az App Service-tartomány, amelytől vásárolta a vásárlás időpontjától egy évig érvényes. Alapértelmezés szerint a tartományhoz van konfigurálva a következő évre díjszabási a fizetési mód szerint automatikusan megújul. Ha azt szeretné, kapcsolja ki az automatikus megújítását, vagy ha azt szeretné, manuálisan megújítani a tartományát, kövesse ezeket a lépéseket.

Az a **App Services** lapra, majd az alkalmazást, válassza a neve **beállítások**, majd válassza ki **egyéni tartományok**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Az a **App Service-tartományok** területen válassza ki a konfigurálni kívánt tartományt.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

A tartomány a bal oldali navigációs sávján válassza **tartománymegújítás**. A tartomány automatikus megújítása leállításához válassza ki **ki**, majd **mentése**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Válassza ki a manuálisan megújítani a tartományban, **tartomány megújítása**. Ez a gomb viszont nem aktív a tartomány lejártát megelőzően 90 napig.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Egyéni DNS-rekordok kezelése

Az Azure-ban, egy App Service-tartomány DNS-rekordok segítségével kezelt [Azure DNS](https://azure.microsoft.com/services/dns/). Adhat hozzá, eltávolításához és DNS-rekordok frissítése, akárcsak egy kívülről vásárolt tartományhoz.

### <a name="open-app-service-domain"></a>Nyissa meg az App Service-tartomány

Az Azure Portalon, a bal oldali menüben válassza ki a **minden szolgáltatás** > **App Service-tartományok**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Válassza ki a tartományt, kezelheti. 

### <a name="access-dns-zone"></a>Hozzáférés DNS-zóna

A tartomány bal oldali menüben válassza ki a **DNS-zóna**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Ez a művelet megnyitja a [DNS-zóna](../dns/dns-zones-records.md) lapján az App Service-tartomány Azure DNS-ben. DNS-rekordok szerkesztésére vonatkozó információkért lásd: [DNS-zónák kezelése az Azure Portalon](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Vásárlás (tartomány törlése)

Miután Ön megvásárolta, az App Service-tartomány, a vásárlás teljes visszatérítést öt napja van. Öt nap után törölheti az App Service-tartomány, de nem számolt visszatérítést kap.

### <a name="open-app-service-domain"></a>Nyissa meg az App Service-tartomány

Az Azure Portalon, a bal oldali menüben válassza ki a **minden szolgáltatás** > **App Service-tartományok**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Válassza ki a tartományt, megszakítása vagy törölni szeretné. 

### <a name="delete-hostname-bindings"></a>Állomásnévkötések törlése

A tartomány bal oldali menüben válassza ki a **állomásnévkötések**. Az összes Azure-szolgáltatásokból származó állomásnévkötések Itt jelennek meg.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Az App Service-tartomány nem lehet törölni, csak az összes állomásnévkötések törlése.

Törölje minden eszköznév kötésének kiválasztásával **...**   >  **Törlése**. Után minden kötés törlése, jelölje be az **mentése**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Mégse vagy törlése

A tartomány bal oldali menüben válassza ki a **áttekintése**. 

Ha a megszakítási időszak a megvásárolt tartományon nem telt meg, válassza ki a **vásárlás**. Jelenik meg egy **törlése** inkább gombra. A tartomány visszatérítés nélkül törléséhez válassza ki **törlése**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

A művelet megerősítéséhez válassza **Igen**.

A művelet befejezése után a tartományban kiadott az előfizetésből, és újra beszerzési mindenki számára elérhető. 

## <a name="direct-default-url-to-a-custom-directory"></a>Egyéni könyvtár közvetlen alapértelmezett URL-címe

Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Például a közvetlen őket egy alkönyvtár `public`, lásd: [alapértelmezett URL-címe, egyéni könyvtár közvetlen](app-service-web-tutorial-custom-domain.md#virtualdir).

## <a name="more-resources"></a>További erőforrások

[GYAKORI KÉRDÉSEK: App Service-tartomány (előzetes verzió) és az egyéni tartományok](https://blogs.msdn.microsoft.com/appserviceteam/2017/08/08/faq-app-service-domain-preview-and-custom-domains/)

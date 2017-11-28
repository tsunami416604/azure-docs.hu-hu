---
title: "Egy egyéni tartománynevet, az Azure Web Apps megvásárlása"
description: "Ismerje meg, hogyan vásárolható meg egy egyéni tartománynevet és egy webalkalmazást az Azure App Service-ben."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.openlocfilehash: 2ba6e3a79e5eb4eca4a3c7d35ada8c58bfe2295e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="buy-a-custom-domain-name-for-azure-web-apps"></a>Egy egyéni tartománynevet, az Azure Web Apps megvásárlása

App Service (előzetes verzió) tartományai közvetlenül az Azure-ban kezelt a legfelső szintű tartományoknak. Ezek megkönnyítik az egyéni tartományok kezelése [Azure Web Apps](app-service-web-overview.md). Ez az oktatóanyag bemutatja, hogyan kellene vásárolnia egy App Service-tartományhoz, és a DNS-nevek hozzárendelése az Azure Web Apps.

Ez a cikk az Azure App Service-(Web Apps, az API Apps, Mobile Apps, a Logic Apps) van. Azure virtuális gép és az Azure Storage: [rendelje hozzá az App Service tartomány Azure virtuális gép vagy az Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Cloud Services, lásd: [egy egyéni tartománynevet, az Azure-felhőszolgáltatás konfigurálása](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [App Service-alkalmazás létrehozása](/azure/app-service/), vagy egy másik az oktatóanyaghoz létrehozott alkalmazást használni.
* [Távolítsa el az előfizetéshez a költségkeret maximumát](../billing/billing-spending-limit.md#remove). App Service tartomány, szabad előfizetési kreditek nem vásárolhat.

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

Egyéni tartományok az Azure Web Apps, a webes alkalmazás által használandó [App Service-csomag](https://azure.microsoft.com/pricing/details/app-service/) fizetős rétegben kell lennie (**megosztott**, **alapvető**, **szabványos**, vagy  **Prémium szintű**). Ezt a lépést akkor győződjön meg arról, hogy a webes alkalmazás van a támogatott az IP-címek.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg a [Azure-portálon](https://portal.azure.com) és jelentkezzen be az Azure-fiókjával.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Keresse meg az alkalmazás az Azure-portálon

A bal oldali menüben válassza ki a **alkalmazásszolgáltatások**, majd válassza ki az alkalmazás nevét.

![Az Azure alkalmazásba portálnavigációjával](./media/app-service-web-tutorial-custom-domain/select-app.png)

Az App Service alkalmazás a felügyeleti oldal akkor jelenik meg.  

### <a name="check-the-pricing-tier"></a>Ellenőrizze az árképzési szint

Az alkalmazás oldal bal oldali navigációs, görgessen a **beállítások** válassza ki azt **vertikális felskálázás (App Service-csomag)**.

![Méretezett menü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Az alkalmazás jelenlegi rétegtől kék szegély ki van jelölve. Győződjön meg arról, hogy az alkalmazás nem szerepel a **szabad** réteg. Nem támogatja az egyéni DNS a **szabad** réteg. 

![Ellenőrizze a tarifacsomag](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Ha az App Service-csomag **szabad**, zárja be a **válasszon tarifacsomagot** lapon, és folytassa a [a tartomány vásárlása](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Vertikális felskálázás az App Service-csomag

Válassza ki valamelyik nem szabad rétegek (**megosztott**, **alapvető**, **szabványos**, vagy **prémium**). 

Kattintson a **Kiválasztás** gombra.

![Ellenőrizze a tarifacsomag](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a méretezési művelet befejeződött.

![Skálázási művelet megerősítése](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>A tartomány vásárlása

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Nyissa meg a [Azure-portálon](https://portal.azure.com/) és jelentkezzen be az Azure-fiókjával.

### <a name="launch-buy-domains"></a>Indítsa el a vásárlás tartományok
Az a **webalkalmazások** lapra, kattintson a nevére, webalkalmazás, jelölje be **beállítások**, majd válassza ki **egyéni tartományok**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Az a **egyéni tartományok** kattintson **tartomány vásárlása**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Ha nem látható a **App Service tartományok** szakaszban, el kell távolítania a költségkeret maximumát, az Azure-fiókjába (lásd: [Előfeltételek](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>A tartomány beszerzési konfigurálása

Az a **App Service tartomány** lap a **keresési tartomány** mezőbe írja be a tartomány nevét, vásárolnia, és írja be a kívánt `Enter`. A javasolt elérhető tartományok szövegmező alatt jelennek meg. Válasszon egy vagy több tartományt szeretne vásárolni.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> A következő [a legfelső szintű tartományoknak](https://wikipedia.org/wiki/Top-level_domain) tartományok App Service által támogatott: _com_, _nettó_, _co.uk_, _szervezeti_, _nl_, _a_, _biz_, _org.uk_, és _co.in_.
>
>

Kattintson a **elérhetőségi adatai** és töltse ki a tartomány kapcsolattartási adatokat űrlapot. Ha befejezte, kattintson a **OK** az App Service tartomány lapra való visszatéréshez.
   
Fontos, hogy a lehető legtöbb pontossággal minden kötelező mezőt kitöltötte. Helytelen adatok elérhetőségét tartományok beszerzési hibát eredményezhet. 

Ezután válassza ki a kívánt beállításokat a tartomány. Lásd az alábbi táblázatban ismereteket szeretnének elsajátítani:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|Adatvédelem | Bekapcsolás | Az "Adatvédelem", amely szerepel a vételár részt _szabad_. Néhány a legfelső szintű tartományoknak regisztráló szervezetek, amelyek nem támogatják az adatvédelem kezeli, és azok szerepel a **adatvédelmet** lap. |
| Alapértelmezett állomásnevek hozzárendelése | **www** és**@** | Válassza ki a kívánt állomásnévkötéseket, ha szükséges. Ha a tartomány beszerzési művelet befejeződött, a webalkalmazás a kijelölt állomásnevek, érhető el. Ha a webes alkalmazás mögött található [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), a gyökértartomány hozzárendelni a beállítás nem jelenik meg (@), mivel a Traffic Manager nem támogatja A rekordok. A tartomány a vásárlás befejezése után módosíthatja az állomásnév-hozzárendeléseket. |

### <a name="accept-terms-and-purchase"></a>Fogadja el a feltételeket és a vásárlások

Kattintson a **jogi feltételeket** tekintse meg a feltételeket és a költségek, és kattintson a **megvásárlása**.

> [!NOTE]
> App Service-tartományok Azure DNS használatával a tartományok üzemeltetésére. A tartomány Regisztr mellett Azure DNS-használati díjak vonatkoznak. További információ: [Azure DNS árképzési](https://azure.microsoft.com/pricing/details/dns/).
>
>

Vissza a **App Service tartomány** kattintson **OK**. Amíg a művelet van folyamatban, tekintse meg a következő értesítéseket:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Tesztelje a gazdagép neve

Ha a webalkalmazás alapértelmezett állomásnevek rendelt, is látja az összes kijelölt állomásnév sikeres értesítést. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Is megjelenik a kijelölt állomásnevek a **egyéni tartományok** lap a **egyéni állomásnevek** szakasz. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

A gazdagép neve teszteléséhez keresse meg a listában szereplő gazdagép neve a böngészőben. Az előző képernyőképen látható példa, próbálja meg máshová _kontoso.net_ és _www.kontoso.net_.

## <a name="assign-hostnames-to-web-app"></a>A webes alkalmazás állomásnevek hozzárendelése

Ha nem kíván egy vagy több alapértelmezett állomásnevek hozzárendelése a webalkalmazás a vásárlás során, vagy hozzá kell rendelnie az állomásnév nem szerepel a listában, bármikor rendelhet hozzá, az állomásnév.

Az App Service tartomány állomásnevek más a webes alkalmazás is hozzárendelhetők. A lépései attól függnek, hogy az App Service tartomány és a web app tartozik ugyanahhoz az előfizetéshez.

- Másik előfizetést: képezi le egy beszerzett kívülről tartományhoz hasonlóan a webalkalmazás az App Service tartományból egyéni DNS-rekordokat. Információ az egyéni DNS-nevek hozzáadása egy App Service tartományhoz: [egyéni DNS-rekordok kezelése](#custom). Egy külső megvásárolt tartomány leképezése egy webalkalmazást, lásd: [egy meglévő egyéni DNS-névvel hozzárendelése az Azure Web Apps](app-service-web-tutorial-custom-domain.md). 
- Ugyanahhoz az előfizetéshez: kövesse az alábbi lépéseket.

### <a name="launch-add-hostname"></a>Indítsa el az állomásnév hozzáadása
Az a **alkalmazásszolgáltatások** lapon, válassza ki, hogy ki szeretné osztani az állomásnevek, válassza ki a webalkalmazás nevét **beállítások**, majd válassza ki **egyéni tartományok**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Győződjön meg arról, hogy a megvásárolt tartomány szerepel a **App Service tartományok** szakaszában, de nem adja meg azt. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> A webalkalmazás összes App Service tartomány ugyanabban az előfizetésben látható **egyéni tartományok** lap. Ha a tartomány a webes alkalmazás előfizetésben, de nem látható a webalkalmazás **egyéni tartományok** lapján próbálja megnyitni a **egyéni tartományok** oldalon vagy a weblap frissítése. Ellenőrizze a folyamatban lévő vagy a létrehozás sikertelen az Azure portál felső értesítési Csengő is.
>
>

Válassza ki **állomásnév hozzáadása**.

### <a name="configure-hostname"></a>Állomásnév konfigurálása
Az a **állomásnév hozzáadása** párbeszédpanelen adja meg az App Service tartományhoz vagy bármely altartomány teljesen minősített tartománynevét. Példa:

- kontoso.NET
- www.kontoso.NET
- ABC.kontoso.NET

Ha elkészült, válassza ki a **ellenőrzése**. Az állomásnév rekordtípus automatikusan ki van jelölve, az Ön.

Válassza ki **állomásnév hozzáadása**.

Ha a művelet befejeződik, megjelenik a hozzárendelt gazdagép sikeres értesítést.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Zárja be az állomásnév hozzáadása
Az a **állomásnév hozzáadása** lapon adjon meg a webes alkalmazás tetszés szerint bármely más állomásnév,. Ha elkészült, zárja be a **állomásnév hozzáadása** lap.

Az alkalmazás ekkor megjelenik az újonnan hozzárendelt hostname(s) **egyéni tartományok** lap.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Tesztelje a gazdagép neve

Nyissa meg a listában szereplő gazdagép neve a böngészőben. Az előző képernyőképen látható példa, próbálja meg máshová _abc.kontoso.net_.

## <a name="renew-the-domain"></a>A tartomány megújításához

Az App Service vásárolt tartománya érvényes beszerzési óta egy évig. Alapértelmezés szerint a tartományvezérlők által a fizetési módot a következő évre díjszabási automatikusan megújítani. Ha azt szeretné, kapcsolja ki az automatikus megújítását, vagy manuálisan megújítani a tartományba, kövesse az itt lépéseket.

Az a **webalkalmazások** lapra, kattintson a nevére, webalkalmazás, jelölje be **beállítások**, majd válassza ki **egyéni tartományok**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Az a **App Service tartományok** területen válassza ki a konfigurálni kívánt tartományt.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Válassza ki a bal oldali navigációs sávon a tartomány, **tartomány megújítási**. A tartomány automatikusan megújítása leállításához válassza ki **ki**, majd **mentése**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Manuálisan megújítani a tartományhoz, válassza ki a **megújítási tartomány**. Azonban ez a gomb nincs aktív 90 nappal a tartomány lejártát megelőzően.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Egyéni DNS-rekordok kezelése

Az Azure DNS-rekordok egy App Service-tartomány segítségével felügyelhetők [Azure DNS](https://azure.microsoft.com/services/dns/). Hozzáadhat, távolítsa el, és DNS-rekordok frissítése, akárcsak a beszerzett kívülről tartományban.

### <a name="open-app-service-domain"></a>Nyissa meg az App Service-tartomány

Az Azure portálon a bal oldali menüben válassza ki a **több szolgáltatások** > **App Service tartományok**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Válassza ki a tartományt kezeléséhez. 

### <a name="access-dns-zone"></a>Hozzáférés DNS-zóna

A tartomány bal oldali menüben válasszon ki **DNS-zóna**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Ez a művelet megnyitja az [DNS-zóna](../dns/dns-zones-records.md) az alkalmazástartomány szolgáltatás az Azure DNS oldalán. Információ a DNS-rekord szerkesztése: [DNS-zónák kezelése az Azure portálon](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Szakítsa meg a beszerzési (delete tartomány)

Az App Service tartomány vásárol, után 5 napos lemondani vásárlását teljes visszatérítést. Öt nap után törölheti a alkalmazás Service tartományhoz, de nem visszatérítést.

### <a name="open-app-service-domain"></a>Nyissa meg az App Service-tartomány

Az Azure portálon a bal oldali menüben válassza ki a **több szolgáltatások** > **App Service tartományok**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Válassza ki a tartomány, akkor megszakítja, vagy a törölni kívánt. 

### <a name="delete-hostname-bindings"></a>Törölje az állomásnévkötéseket

A tartomány bal oldali menüben válasszon ki **állomásnévkötéseket**. Az itt felsorolt az állomásnévkötéseket az összes Azure-szolgáltatásokhoz.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Az App Service tartomány nem törölhető, csak az összes állomásnévkötéseket törlése.

Minden egyes állomásnév kötés törlése kiválasztásával **...**   >  **Törlése**. A kötéseket a törlés után válassza ki **mentése**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Mégse vagy törlése

A tartomány bal oldali menüben válasszon ki **áttekintése**. 

Ha a megszakítási időszak a megvásárolt tartományban nem telt meg, válassza ki a **szakítsa meg a beszerzési**. Ellenkező esetben látni egy **törlése** gombra kattint, helyette. A tartomány visszatérítés nélkül törléséhez válassza ki **törlése**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

A művelet megerősítését, válassza ki a **Igen**.

A művelet befejezése után, a tartomány pedig az előfizetésből kiadott és bárki beszerzés újból. 

## <a name="direct-default-url-to-a-custom-directory"></a>Közvetlen alapértelmezett URL-cím, egy egyéni könyvtárba

Alapértelmezés szerint az App Service arra utasítja a webes kérelmek gyökérkönyvtárára mutatva, az alkalmazás kódját. Például a közvetlen őket egy alkönyvtár `public`, lásd: [alapértelmezett URL-cím, egy egyéni könyvtárba közvetlen](app-service-web-tutorial-custom-domain.md#virtualdir).

## <a name="more-resources"></a>További erőforrások

[Gyakran ismételt kérdések: App Service-tartomány (előzetes verzió) és az egyéni tartományok](https://blogs.msdn.microsoft.com/appserviceteam/2017/08/08/faq-app-service-domain-preview-and-custom-domains/)
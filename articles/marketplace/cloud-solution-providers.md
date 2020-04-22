---
title: Felhőalapú megoldások szolgáltatói | Azure Piactér
description: A kiadók mostmár a Microsoft Cloud Solution Provider (CSP) partnercsatornáján keresztül értékesíthetik ajánlataikat.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: c4c8f21234cfbf040d57c0e1c8dbecbb698d7d50
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685926"
---
# <a name="cloud-solution-providers"></a>Felhőszolgáltatók

A szoftverajánlatok a Microsoft [webkirakatain](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)keresztül elérhető, a Microsoft által kiszolgált partnerek által kiszolgált több millió minősített Microsoft-ügyfelet érhetnek el.

A kiadók a csp-programban való elérhetőségre vonatkozó ajánlatokat opt-in alapon, új vagy meglévő ajánlatra konfigurálják, lehetővé téve a partnerek számára, hogy eladják a termékeket, és csomagban megoldásokat hozzanak létre az ügyfelek számára.

A kiadók felelősek azért, hogy törésjavító támogatást nyújtsanak a végfelhasználóknak, és hogy a CSP-programban részt vevő partnerek és/vagy ügyfelek segítséget kapnak. A kriptaprogram partnereinek ajánlott felhasználói dokumentációt, képzést és szolgáltatásállapot-kimaradási értesítéseket (adott esetben) biztosítani a kriptaprogramban lévő partnerek számára, hogy az ügyfelek 1.  

A következő ajánlatok jogosultak a kriptaprogramban részt venni partnerek általi értékesítésre:

- Szoftver-as-a-Service (SaaS) tranzakciós ajánlatok
- Virtuális gépek (VM-ek)
- Megoldássablonok
- Felügyelt alkalmazások

> [!NOTE]
> A tárolókat és a Bring Your Own License (BYOL) vm-sku-kat alapértelmezés szerint a kriptaprogramban részt vevő partnerek értékesítik.

## <a name="how-to-configure-an-offering"></a>Ajánlat konfigurálása

A CSP-program opt-in beállítása a Partnerközpontban vagy a Cloud Partner Portal ban van konfigurálva. [További információ a közzétevők változó élményéről.](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)

### <a name="partner-center-opt-in"></a>Partnerközpont opt-in

A Partnerközpontban a CSP-viszonteladói közönségmodul ban találja meg az opt-in élményt.

![CSP viszonteladói közönség](media/marketplace-publishers-guide/csp-reseller-audience.png)

A CSP viszonteladói közönségmodulban három lehetőség közül választhat:

- Első lehetőség: A kriptaprogramban részt vehet bármely partner
- Második lehetőség: Az általam kiválasztott KP-program egyes partnerei
- Harmadik lehetőség: A kriptaprogramban nincs partner

#### <a name="option-one-any-partner-in-the-csp-program"></a>Első lehetőség: A kriptaprogramban részt vehet bármely partner

![Bármely partner a CSP programban](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Ha ezt a lehetőséget választja, a CSP programban részt vevő összes partner jogosult arra, hogy az ajánlatát értékesítse ügyfeleinek.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Második lehetőség: Az általam kiválasztott KP-program egyes partnerei

![Az általam kiválasztott kriptaprogram konkrét partnerei](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Ha ezt a lehetőséget választja, engedélyezi, hogy a CSP programban mely partnerek jogosultak az ajánlat viszonteladására.

Partnerek engedélyezéséhez kattintson **a CSP-partnerek kiválasztása parancsra,** és megjelenik egy menü, amely lehetővé teszi a partnernév vagy a CSP Azure Active Directory (AAD) bérlői azonosítója alapján való keresést.

![Válassza a CSP menüt](media/marketplace-publishers-guide/csp-pop-up-module.png)

Keresési szűrőket alkalmazhat, például **Ország,** **Kompetencia**vagy **Szakértelem**.

![Ország-, kompetencia- és szakértelemszűrők a partnerek kereséséhez](media/marketplace-publishers-guide/csp-add-resellers.png)

Miután kiválasztotta a partnerek listáját, válassza a **Hozzáadás**lehetőséget.

![Példa a kriptaprogramban részt vehet meghatalmazott partnerek listájára](media/marketplace-publishers-guide/csp-add-resellers-details.png)

A kiválasztott partnerek listáját megjelenítő táblázat megjelenik a CSP viszonteladói közönségoldalán.

![A partnerlista a CSP viszonteladói közönségoldalán](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

A módosítások regisztrálásához válassza a **Piszkozat mentése** lehetőséget.

Ha ez az ajánlat nincs közzétéve, közzé kell tennie az ajánlatot, hogy elérhetővé tegye a kiválasztott partnerek számára.

>[!NOTE]
>Ha egy adott régióban engedélyezi a kriptába tartozó partnert, az ajánlatot bármely olyan vevőnek eladhatják, aki az adott régióhoz tartozik. A [felhőalapú megoldásszolgáltató regionális piacokat és pénznemeket](https://docs.microsoft.com/partner-center/regional-authorization-overview) tartalmaz további információt arról, hogy a kript.p ajánlatok régiók ba vannak sorolva.

Ha egy már közzétett ajánlat kriptalistáját frissíti, adja hozzá a további partnereket, és válassza a **KRIP-közönség szinkronizálása lehetőséget.**

Ha olyan ajánlata van, amely már rendelkezik az engedélyezett partnerek listájával, és ugyanazt a listát szeretné használni egy másik ajánlathoz, használja az **Importálás/exportálás**lehetőséget. Nyissa meg a kriptalistát tartalmazó ajánlatot, és válassza **a KT-k exportálása**lehetőséget. A függvény egy másik ajánlatba importálható .csv fájlt fejleszt.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Harmadik lehetőség: A kriptaprogramban nincs partner

![Nincspartner a kriptaprogramban](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Ha ezt a lehetőséget választja, leiratkozik az ajánlatáról a KP-programból. Ezt a beállítást bármikor módosíthatja.

### <a name="cloud-partner-portal-opt-in"></a>A Cloud Partner Portal jelentkezése

A Cloud Partner Portal on a opt-in a Marketplace vagy a Storefront lapon van beállítva. A CSP programban meghatározott partnerek kiválasztásának lehetősége csak a Partnerközpontban érhető el.

![A CPP-ben a CSP-k részvételi élménye](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Partnerek feljogosításának visszavetése a kriptaprogramban

Ha engedélyezte a csp-programban részt vevő partnert, és az adott partner már eladta a terméket az ügyfeleinek, akkor nem jogosult a partner feljogosítására.

Ha a kriptaprogram egyik partnere nem adta el a terméket az ügyfeleinek, és az ajánlat közzététele után el szeretné távolítani a kriptát, kövesse az alábbi utasításokat:

1. Nyissa meg a [Támogatási kérelem lapot](https://aka.ms/marketplacepublishersupport). Az első néhány legördülő menü automatikusan kitöltődik az Ön számára.

   > [!NOTE]
   > Ne módosítsa az előre kitöltött legördülő menük kiválasztásait.

2. A **Termékverzió kiválasztása**területen válassza az **Élő ajánlat kezelés lehetőséget.**
3. A **Probléma leginkább ismertetése kategóriának**kiválasztása csoportban válassza ki az ajánlatra hivatkozó kategóriát.
4. A **Probléma alegjobban ismertetése érdekében**válassza a **Meglévő ajánlat frissítése**lehetőséget.
5. Válassza a **Tovább** lehetőséget, ha a probléma részleteinek megadásához a **Probléma részletei lapra** szeretne irányítani.
6. A probléma címének használata a **kriptacím deauthorize feljogosítása,** és töltse ki a többi szükséges szakaszt.




## <a name="navigate-between-options"></a>Navigálás a beállítások között

Ebben a szakaszban navigálhat a kripta-viszonteladó három viszonteladói opcióközött.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navigálás az első lehetőségből: A kriptaprogram bármely partnere

Ha az ajánlat jelenleg **1.**

1. Nyissa meg a [Támogatási kérelem lapot](https://aka.ms/marketplacepublishersupport). Az első néhány legördülő menü automatikusan kitöltődik az Ön számára.

   > [!NOTE]
   > Ne módosítsa az előre kitöltött legördülő menük kiválasztásait.

2. A **Termékverzió kiválasztása**területen válassza az **Élő ajánlat kezelés lehetőséget.**
3. A **Probléma leginkább ismertetése kategóriának**kiválasztása csoportban válassza ki az ajánlatra hivatkozó kategóriát.
4. A **Probléma alegjobban ismertetése érdekében**válassza a **Meglévő ajánlat frissítése**lehetőséget.
5. Válassza a **Tovább** lehetőséget, ha a probléma részleteinek megadásához a **Probléma részletei lapra** szeretne irányítani.
6. A probléma címének használata a **kriptacím deauthorize feljogosítása,** és töltse ki a többi szükséges szakaszt.

> [!NOTE]
> Ha a második lehetőségre próbál navigálni, és a CSP-programban szereplő partner már eladta az ajánlatot az ügyfeleinek, akkor ez a partner alapértelmezés szerint már szerepel a jogosult partnerek listáján.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navigálás a második lehetőségből: Az általam kiválasztott kriptaprogram egyes partnerei

Ha az ajánlat jelenleg **2.** **Option one: Any partner in the CSP program**

1. Nyissa meg a [Támogatási kérelem lapot](https://aka.ms/marketplacepublishersupport). Az első néhány legördülő menü automatikusan kitöltődik az Ön számára.

   > [!NOTE]
   > Ne módosítsa az előre kitöltött legördülő menük kiválasztásait.

2. A **Termékverzió kiválasztása**területen válassza az **Élő ajánlat kezelés lehetőséget.**
3. A **Probléma leginkább ismertetése kategóriának**kiválasztása csoportban válassza ki az ajánlatra hivatkozó kategóriát.
4. A **Probléma alegjobban ismertetése érdekében**válassza a **Meglévő ajánlat frissítése**lehetőséget.
5. Válassza a **Tovább** lehetőséget, ha a probléma részleteinek megadásához a **Probléma részletei lapra** szeretne irányítani.
6. A probléma címének használata a **kriptacím deauthorize feljogosítása,** és töltse ki a többi szükséges szakaszt.

 Ha az ajánlat a jelenleg **2.** **Option 3: No partners in the CSP program** Kérés létrehozásához használja az alábbi utasításokat:

1. Nyissa meg a [Támogatási kérelem lapot](https://aka.ms/marketplacepublishersupport). Az első néhány legördülő menü automatikusan kitöltődik az Ön számára.

   > [!NOTE]
   > Ne módosítsa az előre kitöltött legördülő menük kiválasztásait.

2. A **Termékverzió kiválasztása**területen válassza az **Élő ajánlat kezelés lehetőséget.**
3. A **Probléma leginkább ismertetése kategóriának**kiválasztása csoportban válassza ki az ajánlatra hivatkozó kategóriát.
4. A **Probléma alegjobban ismertetése érdekében**válassza a **Meglévő ajánlat frissítése**lehetőséget.
5. Válassza a **Tovább** lehetőséget, ha a probléma részleteinek megadásához a **Probléma részletei lapra** szeretne irányítani.
6. A probléma címének használata a **kriptacím deauthorize feljogosítása,** és töltse ki a többi szükséges szakaszt.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navigálás a 3.

Ha az ajánlat jelenleg **3.**

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Értékesítési és támogatási anyagok megosztása a CSP-programban részt veőket támogató partnerekkel

Annak érdekében, hogy a Felhőszolgáltató programban részt venni a partnerek a leghatékonyabban képviselhessék az ajánlatát, és kapcsolatba léphessenek a szervezettel, be kell nyújtania a viszonteladók számára elérhető értékesítési és támogatási anyagokat. Ezek az erőforrások nem lesznek kitéve az ügyfelek a piactér i kirakatokban.

### <a name="partner-center-csp-channel"></a>Partnerközpont CSP-csatornája

Ha a Partnerközpontban engedélyezte a CSP-csatornát, a megjelenítőknek meg kell adniuk egy URL-címet, amely releváns marketinganyagokat és csatornakapcsolattartási adatokat tartalmaz a CSP-csatornának az ajánlatlista modul alatt:

![Partnerközpont CSP-biztosítéki információi](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Cloud Partner Portal CSP-csatorna

Ha a Cloud Partner Portal on csatlakozott a KP-csatornához, a megjelenítőknek meg kell adniuk egy URL-címet, amely releváns marketinganyagokat és csatornakapcsolattartási adatokat tárol a kriptacsatornás csatornához:

![Felhőpartneri portál csp-biztosítéki információi](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>További lépések

Látogasson el az [Azure Piactérre és az AppSource Publisher guide-ra.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)

Ha többet szeretne megtudni a piactérGTM-szolgáltatásairól, olvassa [el a Piacra lépő szolgáltatások](https://partner.microsoft.com/reach-customers/gtm).

Jelentkezzen be a [Partnerközpontba](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) vagy [a Cloud Partner Portálra](https://cloudpartner.azure.com/) az ajánlat létrehozásához és konfigurálásához.

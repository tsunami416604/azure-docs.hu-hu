---
title: Oldja meg az Azure Active Directory csoport licencproblémáinak |} A Microsoft Docs
description: Hogyan lehet azonosítani és megoldani a licenc-hozzárendelési problémák, amikor az Azure Active Directory Csoportalapú licencelést használ
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5d64cf71ea3a44b7539835e3616150218e8b3635
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861772"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Azonosíthatja és a egy Azure Active Directory-csoporthoz hozzárendelt kapcsolatos problémák megoldásához

Az Azure Active Directoryban (Azure AD) Csoportalapú licencelés bemutatja a egy licencelési hibás állapotú felhasználók. Ebben a cikkben azt meg, miért felhasználók előfordulhat, hogy végül ebben az állapotban.

Hozzárendelésekor licencek közvetlenül az egyes felhasználókkal, Csoportalapú licencelés használata nélkül, a hozzárendelés sikertelen lehet. Ha például a PowerShell-parancsmag végrehajtása `Set-MsolUserLicense` egy felhasználó rendszerben a parancsmag nem sikerül, annak számos oka lehet, kapcsolódó üzleti logikát. Előfordulhat például, elegendő licenccel vagy két service-csomagot, amely nem rendelhetők hozzá egyszerre közötti ütközés. A probléma, hogy azonnal jelentett vissza.

Használata esetén a Csoportalapú licenceléssel és a fordulhat elő, a hibákat, de a háttérben, történik, amíg az Azure AD szolgáltatás a licencek felhasználókhoz rendelése. Ebből kifolyólag a hibák nem hirdetőtáblájáról is értesülni azonnal. Ehelyett azok fájl rögzíti a felhasználói objektum, és majd jelentett a felügyeleti portálon keresztül. Eredeti célja, hogy a felhasználói licenc soha nem elvesznek, de a jövőbeli vizsgálati és megoldási hibás állapotú rögzítse azt.

## <a name="how-to-find-license-assignment-errors"></a>Licenc-hozzárendelési hibák keresése
**Licenc-hozzárendelési hibákkal található**

   1. Adott csoportban lévő hibás állapotú felhasználók megkereséséhez a csoporthoz tartozó panel megnyitásához. A **licencek**, egy értesítés jelenik meg, ha nincsenek hibás állapotú felhasználók.

   ![Csoport hibaértesítésre](./media/licensing-groups-resolve-problems/group-error-notification.png)

   2. Válassza ki az értesítést, nyissa meg az összes érintett felhasználók listáját. Kiválaszthatja, hogy minden egyes felhasználói külön-külön kiválasztásával további részleteket.

   ![A hibás állapotú felhasználók csoport listája](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

   3. A legalább egy hibát tartalmazó összes csoportok kereséséhez a **Azure Active Directory** panelen válassza ki **licencek**, majd válassza ki **áttekintése**. Egy információs mező jelenik meg csoport beavatkozást igényel.

   ![Áttekintés, a hibás állapotú csoportokkal kapcsolatos információ](./media/licensing-groups-resolve-problems/group-errors-widget.png)

   4. Jelölje be az összes csoportot hibákkal listájának megtekintéséhez. Kiválaszthatja az egyes csoportok további részletekért.

   ![Áttekintés, hibákkal csoport listája](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)


A következő szakaszok adjon meg minden esetleges problémát, és úgy oldható meg a leírását.

## <a name="not-enough-licenses"></a>Nincs elég licenc

**Probléma:** nincs elég rendelkezésre álló licenc a csoportban megadott termékek közül. Vásároljon több licencet a termékhez, vagy szabadítson fel nem használt felhasználókkal vagy csoportokkal licenceket kell.

Megtekintéséhez, hogy hány licenc érhető el, keresse fel a **Azure Active Directory** > **licencek** > **minden termék**.

Ha szeretné látni, milyen felhasználókkal és csoportokkal licenceket használ fel, válasszon ki egy terméket. A **licenccel rendelkező felhasználók**, minden olyan felhasználók, akik rendelkeznek licenccel közvetlenül vagy egy vagy több csoportot listájának megtekintéséhez. A **licenccel rendelkező csoportok**, láthatja, amelyeken a hozzárendelt összes csoport.

**PowerShell:** PowerShell-parancsmagok a hiba, jelentse a _CountViolation_.

## <a name="conflicting-service-plans"></a>Ütköző szolgáltatási csomagok

**Probléma:** a termékeket, a csoportban megadott egyik tartalmaz egy service-csomagot, amely ütközik egy másik service-csomag már hozzá van rendelve egy másik termékkel küldése a felhasználónak. Néhány service-csomagok olyan módon, hogy azok nem rendelhető hozzá ugyanahhoz a felhasználóhoz, mint egy másik, a kapcsolódó service-csomagban vannak konfigurálva.

Vegye figyelembe az alábbi példában. Egy felhasználói licenccel rendelkezik az Office 365 nagyvállalati verzió *E1* közvetlenül, rendelt minden a tervek engedélyezve van. A felhasználó van adva egy csoportot, amelynek az Office 365 nagyvállalati verzió *E3* termék rendelve. A E3 termék service-csomagok, amelyek nem lehetnek átfedésben a terv részét képező E1, így a licenc-hozzárendelés sikertelen, és a "Ütköző szolgáltatási csomagok" tartalmazza. Ebben a példában az ütköző szolgáltatási csomagok a következők:

-   A SharePoint Online (2. csomag) nem felel meg a SharePoint Online (1. csomag).
-   Az Exchange Online (2. csomag) ütközik a Exchange Online (1. csomag).

Az ütközés elhárításához tiltsa le a csomagok közül kettő kell. Letilthatja a közvetlenül hozzárendelt a felhasználóhoz E1 csomag licenc. Vagy a teljes csoport licenc-hozzárendelés módosítása, és tiltsa le a csomagok a E3-licenc szükséges. Azt is megteheti dönthet, hogy a E1 licenc eltávolítása a felhasználó a E3 licenc kontextusában redundáns esetén.

Ütköző terméklicencek mindig megoldásáról a döntést a rendszergazda tartozik. Az Azure AD automatikusan nem licenc ütközések feloldásához.

**PowerShell:** PowerShell-parancsmagok a hiba, jelentse a _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Más termékek függenek ettől a licenctől

**Probléma:** a termékeket, a csoportban megadott egyik tartalmaz egy szolgáltatáscsomag, amely egy másik service-csomagot, egy másik termékkel, működéséhez engedélyezni kell. Ez a hiba akkor fordul elő, amikor az Azure AD megpróbálja eltávolítani az alapul szolgáló service-csomag. Ez például akkor fordulhat elő, amikor a felhasználó eltávolítása a csoportból.

Ez a probléma megoldásához, győződjön meg arról, hogy a szükséges csomag rendelte a felhasználók számára valamilyen más módszerrel, vagy az, hogy a függő szolgáltatások le vannak tiltva, azoknak a felhasználóknak kell. Után, de ezek a felhasználók a csoportlicenc megfelelően eltávolíthatja.

**PowerShell:** PowerShell-parancsmagok a hiba, jelentse a _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>A felhasználási hely nem engedélyezett

**Probléma:** bizonyos Microsoft-szolgáltatások nem érhetők el az összes hely miatt a helyi jogszabályoknak és előírásoknak. Mielőtt licencet hozzárendelni egy felhasználóhoz, meg kell adnia a **a felhasználási hely** tulajdonság a felhasználó. A hely alapján is megadhat a **felhasználói** > **profil** > **beállítások** szakaszban az Azure Portalon.

Amikor az Azure AD megpróbálja csoport licencet rendel egy felhasználói, akiknek felhasználási hely nem támogatott, sikertelen lesz, és rögzíti a felhasználó a hibát.

A probléma megoldásához távolítsa el a felhasználókat nem támogatott helyek a licenccel rendelkező csoport. Azt is megteheti Ha az aktuális használati helye nem jelölik a tényleges felhasználói helyét, módosíthatja őket, hogy a rendszer megfelelően rendelt licenceket legközelebb (ha az új hely támogatott).

**PowerShell:** PowerShell-parancsmagok a hiba, jelentse a _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Az Azure AD csoport licenceket rendel hozzá, anélkül, hogy a megadott felhasználási hely bármely felhasználó örökli a könyvtár helye. Azt javasoljuk, hogy a rendszergazdák be a megfelelő használati értékei, a felhasználók helyi jogszabályoknak és előírásoknak ahhoz, hogy a Csoportalapú licencelés használata előtt.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Mi történik, ha egy csoport egynél több termék licence van?

Egynél több terméklicenc hozzárendelheti egy csoporthoz. Ha például rendelhet Office 365 nagyvállalati E3 csomag és az Enterprise Mobility + Security egy csoportot, hogy könnyen engedélyezheti a felhasználók részeként elérhető szolgáltatások.

Az Azure AD megkísérli hozzárendelni minden olyan licenc, amely a csoport minden egyes felhasználó szerepel. Azure ad-ben nem rendelhető hozzá az egyik terméket üzleti logika problémák miatt, ha, az a csoport más licencei vagy nem fog hozzárendelni. Ilyen például, ha nincs elegendő licenccel az összes, vagy ha más szolgáltatásokkal, amelyek engedélyezve vannak a felhasználóra, ütközés.

Láthatja, hogy a felhasználók, akik hozzárendelve, és ellenőrizze, hogy a probléma által érintett termékek nem sikerült.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Az Előfeltételek termékek licenceinek kezelése

Egyes Microsoft Online termékek, előfordulhat, hogy a saját *bővítmények*. A bővítmények előfeltétel service-csomag, a felhasználó vagy csoport engedélyezni kell, mielőtt hozzárendelhető egy licenc szükséges. A Csoportalapú licenceléssel, a rendszer megköveteli, hogy az Előfeltételek és a kiegészítő service-csomagok ugyanabban a csoportban. Ez történik, győződjön meg arról, hogy a csoportba felvett felhasználók kaphatnak a teljes mértékben működő termék. Vegyünk például az alábbi példában:

Microsoft Workplace Analytics, amely egy bővítményt. Tartalmaz egy azonos nevű egyetlen service-csomagot. A Microsoft csak rendelhet a service-csomag egy felhasználó vagy csoport, amikor az alábbi előfeltételek közül is hozzá van rendelve:
- Exchange Online (1. csomag) 
- Exchange Online (2. csomag)

Próbálja ki ezt a terméket a saját hozzárendelése egy csoporthoz, ha a portálon hibát ad vissza. A hiba a bejelentés kiválasztásával tekinthet meg a következő adatokat:

![Hiányzik a csoporthoz, előfeltételként szükséges](./media/licensing-groups-resolve-problems/group-prerequisite-required.png)

Ha kiválasztjuk a részleteket, a következő hibaüzenetet jeleníti meg:

>A licencművelet sikertelen volt. Győződjön meg arról, hogy a csoport szükséges szolgáltatásokkal rendelkezik, mielőtt hozzáadna vagy eltávolítana egy függő szolgáltatás. **A szolgáltatás a Microsoft Workplace Analytics Exchange Online (2. csomag) engedélyezése is szükséges.**

A licenc hozzárendelése egy csoporthoz, hogy biztosítania kell, hogy a csoport is tartalmaz az előfeltételként szükséges service-csomag. Ha például frissítjük előfordulhat, hogy egy meglévő csoportot, amely már tartalmazza a teljes Office 365 E3-termék, és a kiegészítő termék hozzáadása.

Akkor is, hozzon létre egy különálló csoportot, amely tartalmazza a csak a minimálisan szükséges termékek, hogy a bővítmény működjön. A kiegészítő termék csak a kijelölt felhasználók használható. Ebben a példában azt az alábbi termékek rendelt ugyanabba a csoportba:
- Office 365 nagyvállalati E3 csomag és az csak az Exchange Online (2. csomag) service-csomagban engedélyezve
- Microsoft Workplace Analytics

![Csoport előfeltételként szükséges szoftvert tartalmaz](./media/licensing-groups-resolve-problems/group-addon-with-prerequisite.png)

Ehhez a csoporthoz hozzáadott felhasználók mostantól egy-egy licencet a E3 termék és a egy-egy licencet a Workplace Analytics termék felhasználását. Egy időben számukra egy másik csoportra, amely a teljes E3 termék tagjai lehetnek, és továbbra is a termék csak egy-egy licencet használnak.

> [!TIP]
> Minden előfeltétel szolgáltatáscsomag több csoportot is létrehozhat. Például az Office 365 nagyvállalati E1 csomag és az Office 365 nagyvállalati E3 csomag használatakor a felhasználók csoportot is létrehozhat két Microsoft Workplace Analytics licencre: egy előfeltétel, míg a másik E3 használó E1 használó. Ez lehetővé teszi a bővítményt az E1, E3 és felhasználók terjesztése további licenceket felhasználása nélkül.

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>A licenc-hozzárendelés sikertelen csendes egy felhasználó ismétlődő proxycímeket miatt az Exchange Online

Ha az Exchange Online használata esetén a bérlő néhány felhasználója előfordulhat, hogy megfelelően konfigurálva proxy cím ugyanarra az értékre. Ha Csoportalapú licencelést rendeljen egy licencet az ilyen felhasználó próbál, nem sikerül, és nem rögzíti a hibát. A hiba, jegyezze fel a hiba ebben a példában ez a funkció előzetes verziójának korlátozása, és cím azt megelőzően fogjuk *általános rendelkezésre állás*.

> [!TIP]
> Ha azt tapasztalja, hogy néhány felhasználó nem kapott licencet, és nem történt hiba rögzített azoknak a felhasználóknak, először ellenőrizze, ha van egy azonos proxycímmel.
> Szeretné látni, hogy van-e ismétlődő proxycímet, hajtsa végre a következő PowerShell-parancsmag elleni Exchange online-hoz:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> A problémáról további információk: ["proxycím már használatban van" hibaüzenet jelenik meg az Exchange online-hoz](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). A cikk emellett tartalmaz információt a [hogyan kapcsolódhat az Exchange online-hoz távoli PowerShell-lel](https://technet.microsoft.com/library/jj984289.aspx).

Teljesítése után bármely proxy cím az érintett felhasználók számára, ügyeljen arra, hogy a csoport, győződjön meg arról, hogy az licenceket is érvényesek, a licenc feldolgozási kényszerítése.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Hogyan kényszerítheti licenc feldolgozási hibák megoldásához egy csoportban?

Milyen lépéseket, a hibák elhárításához készített, attól függően szükség lehet manuálisan indítható a feldolgozása egy csoportot, hogy a felhasználó állapotának frissítése.

Például ha Ön szabadítson fel néhány licenccel, közvetlen licenc-hozzárendelés eltávolítása a felhasználók által szüksége csoportokat, amelyek korábban nem sikerült teljesen licenc az összes felhasználói tagok feldolgozásának indításához. Egy csoport újrafeldolgozása, nyissa meg a csoport panelen nyissa meg **licencek**, majd válassza ki a **újrafeldolgozása** gombra az eszköztáron.

## <a name="next-steps"></a>További lépések

Licenc felügyeleti csoportok használatával kapcsolatos egyéb forgatókönyvek kapcsolatos további információkért tekintse meg a következőket:

* [Licencek hozzárendelése egy csoporthoz az Azure Active Directory](licensing-groups-assign.md)
* [Mit jelent a Csoportalapú licencelés az Azure Active Directoryban?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Hogyan kell egyéni licenccel rendelkező felhasználók migrálása Csoportalapú licencelésre, az Azure Active Directoryban](licensing-groups-migrate-users.md)
* [Csoportalapú licencelés további forgatókönyvek az Azure Active Directory](licensing-group-advanced.md)

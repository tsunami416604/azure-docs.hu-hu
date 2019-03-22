---
title: Azure Active Directory - csoporthoz hozzárendelt kapcsolatos problémák megoldásához |} A Microsoft Docs
description: Hogyan lehet azonosítani és megoldani a licenc-hozzárendelési problémák, amikor az Azure Active Directory Csoportalapú licencelést használ
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92969015910cc5bd72e2d9339d5c15c1f7af48b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201534"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Azonosíthatja és a egy Azure Active Directory-csoporthoz hozzárendelt kapcsolatos problémák megoldásához

Az Azure Active Directoryban (Azure AD) Csoportalapú licencelés bemutatja a egy licencelési hibás állapotú felhasználók. Ebben a cikkben azt meg, miért felhasználók előfordulhat, hogy végül ebben az állapotban.

Hozzárendelésekor licencek közvetlenül az egyes felhasználókkal, Csoportalapú licencelés használata nélkül, a hozzárendelés sikertelen lehet. Ha például a PowerShell-parancsmag végrehajtása `Set-MsolUserLicense` egy felhasználó rendszerben a parancsmag nem sikerül, annak számos oka lehet, kapcsolódó üzleti logikát. Előfordulhat például, elegendő licenccel vagy két service-csomagot, amely nem rendelhetők hozzá egyszerre közötti ütközés. A probléma, hogy azonnal jelentett vissza.

Használata esetén a Csoportalapú licenceléssel és a fordulhat elő, a hibákat, de a háttérben, történik, amíg az Azure AD szolgáltatás a licencek felhasználókhoz rendelése. Ebből kifolyólag a hibák nem hirdetőtáblájáról is értesülni azonnal. Ehelyett azok fájl rögzíti a felhasználói objektum, és majd jelentett a felügyeleti portálon keresztül. Eredeti célja, hogy a felhasználói licenc soha nem elvesznek, de a jövőbeli vizsgálati és megoldási hibás állapotú rögzítse azt.

## <a name="how-to-find-license-assignment-errors"></a>Licenc-hozzárendelési hibák keresése
**Licenc-hozzárendelési hibákkal található**

1. Adott csoportban lévő hibás állapotú felhasználók megkereséséhez a csoporthoz tartozó panel megnyitásához. A **licencek**, egy értesítés jelenik meg, ha nincsenek hibás állapotú felhasználók.

   ![Csoport és a hiba értesítések üzenet](./media/licensing-groups-resolve-problems/group-error-notification.png)

2. Válassza ki az értesítést, nyissa meg az összes érintett felhasználók listáját. Kiválaszthatja, hogy minden egyes felhasználói külön-külön kiválasztásával további részleteket.

   ![a csoport licencelésének hibás állapotú felhasználók listája](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

3. A legalább egy hibát tartalmazó összes csoportok kereséséhez a **Azure Active Directory** panelen válassza ki **licencek**, majd válassza ki **áttekintése**. Egy információs mező jelenik meg csoport beavatkozást igényel.

   ![Áttekintés és a hibás állapotú csoportokkal kapcsolatos információ](./media/licensing-groups-resolve-problems/group-errors-widget.png)

4. Jelölje be az összes csoportot hibákkal listájának megtekintéséhez. Kiválaszthatja az egyes csoportok további részletekért.

   ![Áttekintés és a hibákkal csoport listája](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)


A következő szakaszok adjon meg minden esetleges problémát, és úgy oldható meg a leírását.

## <a name="not-enough-licenses"></a>Nincs elég licenc

**Probléma:** Nincs elég rendelkezésre álló licenc a csoportban megadott termékek közül. Vásároljon több licencet a termékhez, vagy szabadítson fel nem használt felhasználókkal vagy csoportokkal licenceket kell.

Megtekintéséhez, hogy hány licenc érhető el, keresse fel a **Azure Active Directory** > **licencek** > **minden termék**.

Ha szeretné látni, milyen felhasználókkal és csoportokkal licenceket használ fel, válasszon ki egy terméket. A **licenccel rendelkező felhasználók**, minden olyan felhasználók, akik rendelkeznek licenccel közvetlenül vagy egy vagy több csoportot listájának megtekintéséhez. A **licenccel rendelkező csoportok**, láthatja, amelyeken a hozzárendelt összes csoport.

**PowerShell:** A hiba, jelentse a PowerShell-parancsmagok _CountViolation_.

## <a name="conflicting-service-plans"></a>Ütköző szolgáltatási csomagok

**Probléma:** A csoportban megadott termékek közül, hogy nem felel meg a felhasználó egy másik termékkel már hozzá van rendelve egy másik service-csomag tartalmaz service-csomag. Néhány service-csomagok olyan módon, hogy azok nem rendelhető hozzá ugyanahhoz a felhasználóhoz, mint egy másik, a kapcsolódó service-csomagban vannak konfigurálva.

Lásd az alábbi példát. Egy felhasználói licenccel rendelkezik az Office 365 nagyvállalati verzió *E1* közvetlenül, rendelt minden a tervek engedélyezve van. A felhasználó van adva egy csoportot, amelynek az Office 365 nagyvállalati verzió *E3* termék rendelve. A E3 termék service-csomagok, amelyek nem lehetnek átfedésben a terv részét képező E1, így a licenc-hozzárendelés sikertelen, és a "Ütköző szolgáltatási csomagok" tartalmazza. Ebben a példában az ütköző szolgáltatási csomagok a következők:

-   A SharePoint Online (2. csomag) nem felel meg a SharePoint Online (1. csomag).
-   Az Exchange Online (2. csomag) ütközik a Exchange Online (1. csomag).

Az ütközés elhárításához tiltsa le a csomagok közül kettő kell. Letilthatja a közvetlenül hozzárendelt a felhasználóhoz E1 csomag licenc. Vagy a teljes csoport licenc-hozzárendelés módosítása, és tiltsa le a csomagok a E3-licenc szükséges. Azt is megteheti dönthet, hogy a E1 licenc eltávolítása a felhasználó a E3 licenc kontextusában redundáns esetén.

Ütköző terméklicencek mindig megoldásáról a döntést a rendszergazda tartozik. Az Azure AD automatikusan nem licenc ütközések feloldásához.

**PowerShell:** A hiba, jelentse a PowerShell-parancsmagok _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Más termékek függenek ettől a licenctől

**Probléma:** A csoportban megadott termékek egyik tartalmaz egy szolgáltatáscsomag, amely egy másik service-csomagot, egy másik termékkel, működéséhez engedélyezni kell. Ez a hiba akkor fordul elő, amikor az Azure AD megpróbálja eltávolítani az alapul szolgáló service-csomag. Ez például akkor fordulhat elő, amikor a felhasználó eltávolítása a csoportból.

Ez a probléma megoldásához, győződjön meg arról, hogy a szükséges csomag rendelte a felhasználók számára valamilyen más módszerrel, vagy az, hogy a függő szolgáltatások le vannak tiltva, azoknak a felhasználóknak kell. Után, de ezek a felhasználók a csoportlicenc megfelelően eltávolíthatja.

**PowerShell:** A hiba, jelentse a PowerShell-parancsmagok _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>A felhasználási hely nem engedélyezett

**Probléma:** Bizonyos Microsoft-szolgáltatások nem érhetők el az összes hely miatt a helyi jogszabályoknak és előírásoknak. Mielőtt licencet hozzárendelni egy felhasználóhoz, meg kell adnia a **a felhasználási hely** tulajdonság a felhasználó. A hely alapján is megadhat a **felhasználói** > **profil** > **beállítások** szakaszban az Azure Portalon.

Amikor az Azure AD megpróbálja csoport licencet rendel egy felhasználói, akiknek felhasználási hely nem támogatott, sikertelen lesz, és rögzíti a felhasználó a hibát.

A probléma megoldásához távolítsa el a felhasználókat nem támogatott helyek a licenccel rendelkező csoport. Azt is megteheti Ha az aktuális használati helye nem jelölik a tényleges felhasználói helyét, módosíthatja őket, hogy a rendszer megfelelően rendelt licenceket legközelebb (ha az új hely támogatott).

**PowerShell:** A hiba, jelentse a PowerShell-parancsmagok _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Az Azure AD csoport licenceket rendel hozzá, anélkül, hogy a megadott felhasználási hely bármely felhasználó örökli a könyvtár helye. Azt javasoljuk, hogy a rendszergazdák be a megfelelő használati értékei, a felhasználók helyi jogszabályoknak és előírásoknak ahhoz, hogy a Csoportalapú licencelés használata előtt.

## <a name="duplicate-proxy-addresses"></a>Ismétlődő proxycímek

Ha az Exchange Online használata esetén a bérlő néhány felhasználója előfordulhat, hogy megfelelően konfigurálva proxy cím ugyanarra az értékre. Ha Csoportalapú licencelést rendeljen egy licencet az ilyen felhasználó próbál, nem sikerül, és látható a "proxycím már használatban van".

> [!TIP]
> Szeretné látni, hogy van-e ismétlődő proxycímet, hajtsa végre a következő PowerShell-parancsmag elleni Exchange online-hoz:
> ```
> Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> A problémáról további információk: ["proxycím már használatban van" hibaüzenet jelenik meg az Exchange online-hoz](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). A cikk emellett tartalmaz információt a [hogyan kapcsolódhat az Exchange online-hoz távoli PowerShell-lel](https://technet.microsoft.com/library/jj984289.aspx). További információt ebben a cikkben [hogyan a proxyAddresses attribútum feltöltése az Azure ad-ben a](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

Teljesítése után bármely proxy cím az érintett felhasználók számára, ügyeljen arra, hogy a csoport, győződjön meg arról, hogy az licenceket is érvényesek, a licenc feldolgozási kényszerítése.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Mi történik, ha egy csoport egynél több termék licence van?

Egynél több terméklicenc hozzárendelheti egy csoporthoz. Ha például rendelhet Office 365 nagyvállalati E3 csomag és az Enterprise Mobility + Security egy csoportot, hogy könnyen engedélyezheti a felhasználók részeként elérhető szolgáltatások.

Az Azure AD megkísérli hozzárendelni minden olyan licenc, amely a csoport minden egyes felhasználó szerepel. Azure ad-ben nem rendelhető hozzá az egyik terméket üzleti logika problémák miatt, ha, az a csoport más licencei vagy nem fog hozzárendelni. Ilyen például, ha nincs elegendő licenccel az összes, vagy ha más szolgáltatásokkal, amelyek engedélyezve vannak a felhasználóra, ütközés.

Láthatja, hogy a felhasználók, akik hozzárendelve, és ellenőrizze, hogy a probléma által érintett termékek nem sikerült.

## <a name="what-happens-when-a-group-with-licenses-assigned-is-deleted"></a>Mi történik, ha a csoport hozzárendelt licencekkel rendelkeznek törlődik?

Minden licenc rendelve egy csoportot, a csoport törlése előtt el kell távolítania. Azonban a csoportban lévő összes felhasználó licenceinek eltávolításával időt is igénybe vehet. Licenc-hozzárendelés eltávolítása a csoportból, közben lehet sikertelen, ha a felhasználó egy függő licenccel rendelkezik, vagy ha a proxy cím ütközést probléma, amely megakadályozza, hogy a licenc eltávolítása. Ha egy felhasználó egy licencet, amely a csoport törlése miatt eltávolított függő licenccel rendelkezik, a licenc-hozzárendelést a felhasználónak át a közvetlen, örökölt.

Vegyük példaként egy csoportot, amelynek az Office 365 E3 vagy E5 hozzárendelt egy Skype-on, az üzleti service-csomaghoz engedélyezve van. Is tegyük fel, hogy a csoport tagjai néhány közvetlenül hozzárendelt hang konferencia licenccel rendelkeznek-e. A csoport törlése esetén Csoportalapú licencelés megpróbálja eltávolítani az Office 365 E3 vagy E5 az összes felhasználó. Hang konferencia az adott nyelvtől függ a Skype vállalati verzió, mert azok a felhasználók Audio-konferencia a hozzárendelt, a Csoportalapú licencelés alakítja át a Office 365 E3 vagy E5 licenceket a közvetlen licenc-hozzárendelést.

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

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Hogyan kényszerítheti licenc feldolgozási hibák megoldásához egy csoportban?

Milyen lépéseket, a hibák elhárításához készített, attól függően szükség lehet manuálisan indítható a feldolgozása egy csoportot, hogy a felhasználó állapotának frissítése.

Például ha Ön szabadítson fel néhány licenccel, közvetlen licenc-hozzárendelés eltávolítása a felhasználók által szüksége csoportokat, amelyek korábban nem sikerült teljesen licenc az összes felhasználói tagok feldolgozásának indításához. Egy csoport újrafeldolgozása, nyissa meg a csoport panelen nyissa meg **licencek**, majd válassza ki a **újrafeldolgozása** gombra az eszköztáron.

## <a name="how-do-you-force-license-processing-on-a-user-to-resolve-errors"></a>Hogyan kényszerítheti licenc feldolgozási egy felhasználót, hogy ki a hibákat?

Milyen lépéseket, a hibák elhárításához készített, attól függően szükség lehet manuálisan indítható a felhasználót, hogy a felhasználók állapotának frissítése feldolgozását.

Például miután ismétlődő proxy cím egy érintett felhasználót a probléma megoldásához szüksége a felhasználó a feldolgozás aktiválásához. Újból feldolgozza a felhasználó, lépjen a felhasználói panelen nyissa meg a **licencek**, majd válassza ki a **újrafeldolgozása** gombra az eszköztáron.

## <a name="next-steps"></a>További lépések

Licenc felügyeleti csoportok használatával kapcsolatos egyéb forgatókönyvek kapcsolatos további információkért tekintse meg a következőket:

* [Mit jelent a Csoportalapú licencelés az Azure Active Directoryban?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](licensing-groups-assign.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](licensing-groups-migrate-users.md)
* [Felhasználók az Azure Active Directoryban Csoportalapú licencelést használ terméklicencek közötti migrálása](licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](licensing-group-advanced.md)
* [PowerShell forgatókönyvek Csoportalapú licenceléshez az Azure Active Directoryban](licensing-ps-examples.md)

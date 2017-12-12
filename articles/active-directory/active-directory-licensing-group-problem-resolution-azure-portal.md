---
title: "A csoport az Azure Active Directoryban licenc teljesítése |} Microsoft Docs"
description: "Hogyan azonosítása és elhárítása licenc hozzárendelése Azure Active Directory biztonságicsoport-alapú licencelési használatakor"
services: active-directory
keywords: "Az Azure AD-licencelés"
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5bd28eeb8d67dc0dcb3303fdb0e3c20b32f7c431
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>A csoport az Azure Active Directoryban licenc hozzárendelése problémák azonosításához és megoldásához

Az Azure Active Directory (Azure AD) Csoportalapú licencelési bemutatja a licencelési hiba állapotban lévő felhasználók. Ez a cikk azt meg, miért felhasználók előfordulhat, hogy végül ebben az állapotban.

Ha rendel licencek közvetlenül az egyes felhasználók Csoportalapú licencelési használata nélkül, a hozzárendelés művelet sikertelen lehet. Ha például a PowerShell-parancsmag végrehajtása `Set-MsolUserLicense` felhasználói rendszerre, a parancsmag kapcsolódó üzleti logika számos oka lehet sikertelen. Előfordulhat például, egy megfelelő számú licenccel, illetve nem rendelhető hozzá egy időben két service-csomagok közötti ütközés. A probléma azonnali bejelentések vissza azt.

Csoportalapú használatakor licencelési, akkor fordulhat elő, a hibákat, de azok fordul elő a háttérben, amíg az Azure AD szolgáltatás van licencek hozzárendelésével. Emiatt a hibák nem lehetett továbbítani meg azonnal. Ehelyett azok a user objektum rögzített, és a felügyeleti portálon majd jelentett. Az eredeti kísérlet történt a felhasználó licenc soha nem elvesznek, de a későbbi vizsgálat céljára és feloldási állapota rögzítése.

## <a name="how-to-find-license-assignment-errors"></a>Licenc hozzárendelése hibák megkeresése
**Licenc hozzárendelése hibák kereséséhez**

   1. Adott csoportból hibás állapotú felhasználók kereséséhez nyissa meg a csoport a panelen. A **licencek**, egy értesítés jelenik meg, ha azokat a felhasználókat hibás állapotú.

   ![Értesítési. hiba csoportjában.](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

   2. Válassza ki az értesítést, nyissa meg az összes érintett felhasználók listáját. Kiválaszthatja, hogy minden felhasználóhoz külön-külön kiválasztásával további részleteket.

   ![Hibás állapotú felhasználók csoportot, listája](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

   3. A legalább egy hibát tartalmazó összes csoport kereséséhez a **Azure Active Directory** panelen válassza ki **licencek**, majd válassza ki **áttekintése**. Az információk lista megjelenését, amikor csoportokat az Ön figyelmét igénylik.

   ![Áttekintés, hibaállapotban információt](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

   4. Jelölje be az összes csoport hibákkal listájának megtekintéséhez. Kiválaszthatja, hogy minden egyes csoport további részleteket.

   ![Áttekintés, csoportok hibák listája](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


Az alábbiakban adjon meg leírást minden lehetséges problémákról és feloldásához.

## <a name="not-enough-licenses"></a>Nincs elegendő licenc

**Probléma:** nincs elég rendelkezésre álló licence az egyik terméket, amely a csoport szerepel. Vásároljon több licencet a termékhez, vagy szabadítson fel más felhasználóknak vagy csoportoknak a használaton kívüli licenccel kell.

Hány licencet érhetők el, válassza **Azure Active Directory** > **licencek** > **összes**.

Mely felhasználók és csoportok által felhasznált licencek megtekintéséhez válasszon egy termék. A **licenccel rendelkező felhasználók**, megtekintheti az összes olyan felhasználó, akik közvetlenül, sem segítségével egy vagy több csoportjára licenccel rendelkezik. A **licenccel rendelkező csoportok**, láthatja, hogy a termékek hozzárendelt összes csoportokat.

**PowerShell:** PowerShell-parancsmagok jelentse a hibát, _CountViolation_.

## <a name="conflicting-service-plans"></a>Ütköző service-csomagokról

**Probléma:** az egyik terméket, amely a csoport szerepel egy service-csomagot, amely ütközik a már hozzá van rendelve egy másik termékkel a felhasználó egy másik service-csomag tartalmazza. Néhány service-csomagok olyan módon, hogy azok nem lehet hozzárendelni egy másik, a kapcsolódó service-csomag a felhasználónak vannak konfigurálva.

Tekintse meg a következő példát. A felhasználó rendelkezik licenccel az Office 365 vállalati *E1* közvetlenül, rendelni, amelyben engedélyezve van minden a tervek. A felhasználó egy csoportot, amely rendelkezik az Office 365 vállalati bővült *E3* termék rendelve. A E3 termék tartalmaz service-csomagokról, amely nem fedi át a terv E1, található, a licenc-hozzárendelést "Ütköző service-csomagokról" hibával meghiúsul. Ebben a példában az ütköző service-csomagok a következők:

-   SharePoint Online (megtervezése 2) ütközik a SharePoint Online (megtervezése 1).
-   Exchange Online (megtervezése 2) ütközik az Exchange Online (megtervezése 1).

Oldja meg az ütközést, tiltsa le a tervek két kell. Letilthatja a E1 licenc, amely közvetlenül van hozzárendelve a felhasználóhoz. Vagy módosítsa a teljes csoport licenc-hozzárendelést, és tiltsa le a E3 licenc tervek kell. Azt is megteheti dönthet a E1 licenc eltávolítása a felhasználó a E3 licenc környezetében redundáns esetén.

Ütköző terméklicencek mindig megoldásával kapcsolatos döntési tartozik a rendszergazda számára. Az Azure AD automatikusan nem licenc ütközések feloldásához.

**PowerShell:** PowerShell-parancsmagok jelentse a hibát, _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Ez a licenc függenek más termékek

**Probléma:** az egyik terméket, amely a csoport szerepel, amely egy másik service-csomag egy másik termék működéséhez engedélyezni kell a service-csomag tartalmazza. Ez a hiba akkor fordul elő, amikor az Azure AD megpróbálja eltávolítani az alapul szolgáló service-csomag. Ez például akkor fordulhat elő, amikor a felhasználó eltávolítása a csoportból.

A probléma megoldásához szükség győződjön meg arról, hogy a szükséges terv van rendelve a felhasználók számára a valamilyen más módszerrel, vagy az, hogy a függő szolgáltatások azoknak a felhasználóknak vannak-e letiltva. Ezután, amely, akkor megfelelően eltávolíthatja a csoport licenc azoknak a felhasználóknak.

**PowerShell:** PowerShell-parancsmagok jelentse a hibát, _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Használat helye nem engedélyezett

**Probléma:** bizonyos Microsoft-szolgáltatások nem érhetők el az összes hely helyi törvény és szabályozás miatt. Mielőtt egy licencet a felhasználó, meg kell adnia a **felhasználási hely** tulajdonság a felhasználó számára. Megadhatja a helyére a **felhasználói** > **profil** > **beállítások** szakaszban az Azure portálon.

Ha egy csoport licenc hozzárendelése egy felhasználóhoz, amelynek használat helye nem támogatott az Azure AD próbál, nem sikerül, és rögzíti a felhasználó hibát.

A probléma megoldásához távolítsa el a licencelt csoportból nem támogatott helyek felhasználók. Azt is megteheti Ha az aktuális használati értékei nem határoz meg a tényleges felhasználói mappájába, módosíthatja azokat, hogy a licencek megfelelően hozzárendelésének legközelebb (ha az új helyre támogatott).

**PowerShell:** PowerShell-parancsmagok jelentse a hibát, _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Az Azure AD csoport licenceit rendeli, a felhasználók a megadott felhasználási hely nélkül örökli a könyvtár helye. Azt javasoljuk, hogy a rendszergazdák előtt állítsa be a megfelelő használati értékei a felhasználók helyi törvény és szabályozás ahhoz, hogy a csoport-alapú licencelés használata.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Mi történik, ha egy csoport több termék licencét?

Egynél több termék licence rendelhet egy csoportot. Például hozzárendelheti Office 365 nagyvállalati E3 csomag és az Enterprise Mobility + Security könnyen engedélyezése a felhasználók számára az összes belefoglalt szolgáltatások csoporthoz.

Az Azure AD megkísérli a csoport minden felhasználó számára megadott összes licencek hozzárendelése. Az Azure AD nem rendelhető hozzá a termékek üzleti logika problémák miatt, ha az nem fog a többi licencek hozzárendelése a a csoport vagy. Példa: Ha nincs elegendő az összes licencet, vagy ha más szolgáltatásokkal, amelyek engedélyezve vannak a felhasználó ütközések.

A felhasználókat, akik legyenek hozzárendelve, és ellenőrizze, hogy a probléma által érintett termékek nem tekintheti meg.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Előfeltételek termékek licenceinek kezelése

Néhány Microsoft Online termékek, előfordulhat, hogy saját *bővítmények*. A bővítmények egy előfeltételként szükséges service-csomag egy felhasználó vagy csoport engedélyezni kell, mielőtt rendelés licenc szükséges. Csoportalapú licenceléshez, a rendszer megköveteli, hogy ugyanabban a csoportban található legyen-e az Előfeltételek és a bővítmény service-csomagokról. Ez annak biztosítása érdekében, hogy a csoportba felvett felhasználók is megkapják a teljes mértékben működő termék történik. Mérlegeljük, az alábbi példa:

A Microsoft munkahelyi Analytics egy bővítés. Tartalmaz egy azonos nevű egyetlen service-csomagot. Jelenleg csak rendelhet a service-csomag egy felhasználó vagy csoport, amikor az alábbi előfeltételek közül is hozzá van rendelve:
- Exchange Online (1. csomag) 
- Exchange Online (2. csomag)

Próbálja meg a termékkel kapcsolatban egy saját rendelhet hozzá egy csoporthoz, ha a portál hibát ad vissza. A Hibaértesítések kijelölése látható a következő adatokat:

![Csoport, előfeltétel hiányzik](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

Ha azt választja, a részletes adatait, a következő hibaüzenetet jeleníti meg:

>Licenc művelet sikertelen volt. Győződjön meg arról, hogy a csoport szükséges szolgáltatások hozzáadása vagy egy függő szolgáltatás eltávolítása előtt. **A Microsoft munkahelyi Analytics szolgáltatáshoz Exchange Online (2 megtervezése) is engedélyezni kell.**

A licenc hozzárendelése egy csoporthoz, igazolnia kell, hogy a csoport is tartalmazza az előfeltételként szükséges service-csomag. Például frissítjük előfordulhat, hogy egy meglévő csoportot, amely már tartalmazza a teljes Office 365 E3 termék, és a bővítmény termék hozzáadása.

Azt is is létrehozhat egy különálló csoportot, amely csak a minimálisan szükséges termékeket tartalmaz ahhoz, hogy működik a bővítmény. A bővítmény termék csak a kijelölt felhasználók licenc használható. Ebben a példában a következő termékek ugyanahhoz a csoporthoz rendelte azt:
- Az Office 365 nagyvállalati E3 csomag tervvel csak az Exchange Online (terv 2) szolgáltatás engedélyezve van
- A Microsoft munkahelyi elemzés

![Csoportjában előfeltétel tartalmazza](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

Ehhez a csoporthoz hozzáadott felhasználók ettől kezdve a felhasználhatják az egy-egy licencet a E3 termék és a munkahelyi Analytics termék egy-egy licencet. Egy időben azoknak a felhasználóknak egy másik csoportot, amely a teljes E3 termék tagjai lehetnek, és továbbra is használják ki csak egy licencet a termékhez.

> [!TIP]
> Minden előfeltétel service-csomag több csoportot is létrehozhat. Például ha Office 365 nagyvállalati E1 csomag és az Office 365 nagyvállalati E3 csomag is használ a felhasználók számára, létrehozhat két csoportokat a Microsoft munkahelyi Analytics licenc: előfeltétel, míg a másik E3 használó E1 használnak. Ez lehetővé teszi a bővítmény E1 és E3 felhasználók terjesztése lefoglalása további licenceket.

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>A licenc-hozzárendelést nem beavatkozás nélkül a felhasználók ismétlődő proxycímeket miatt az Exchange Online

Ha az Exchange Online eszközt használja, a bérlő bizonyos felhasználói előfordulhat, hogy nem megfelelően van konfigurálva proxy címe ugyanarra az értékre. Csoportalapú licencelési ilyen felhasználói licenc hozzárendelése, akkor sikertelen lesz és nem rögzíti a hibát. Kapcsolatos hibát jegyezze fel a hiba ebben a példában ez a szolgáltatás előzetes verzióját korlátozása, és megoldására, mielőtt fogjuk *általánosan rendelkezésre álló*.

> [!TIP]
> Bizonyára észrevette, hogy egyes felhasználók nem kapott licencet, és nincs azoknak a felhasználóknak rögzített hiba, ha először ellenőrizze, hogy rendelkeznek-e ismétlődő proxycímet.
> Ismétlődő proxycím meg hajtható végre a következő PowerShell-parancsmag Exchange online-hoz képest:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> A problémával kapcsolatban további információkért lásd: ["proxycímet már használja" hibaüzenet jelenik meg az Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). A cikk adatokat is tartalmaz a [csatlakoztatása Exchange online-hoz távoli PowerShell-lel](https://technet.microsoft.com/library/jj984289.aspx).

Teljesítése után bármely proxy címét az érintett felhasználók számára, győződjön meg arról, győződjön meg arról, hogy a licencek alkalmazhatók a csoport licenc terhelése kényszerítése.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Hogyan kényszeríti licenc feldolgozási hibák csoportban?

Attól függően, hogy milyen lépéseket, hogy fordítani javítsa ki a hibákat szükség lehet manuálisan elindítható egy csoportot a felhasználói állapot frissítése feldolgozását.

Például ha szabadítsa fel néhány licencek közvetlen licenc-hozzárendeléseivel eltávolításával a felhasználóktól, szeretné elindítani, amely korábban nem sikerült teljesen az összes felhasználó tag licenc csoportok feldolgozása. Újból feldolgozza a csoport, nyissa meg a csoport panelen megnyitott **licencek**, majd válassza ki a **újból feldolgozza** gomb az eszköztáron.

## <a name="next-steps"></a>Következő lépések

Más esetekben a Licenckezelés csoportokon keresztül kapcsolatos további tudnivalókért olvassa el a következőket:

* [Licencek hozzárendelése az Azure Active Directory csoport](active-directory-licensing-group-assignment-azure-portal.md)
* [Mi az a csoport-alapú licencelése az Azure Active Directoryban?](active-directory-licensing-whatis-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési egyedi licenccel rendelkező felhasználók áttelepítése](active-directory-licensing-group-migration-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési további helyzeteket is](active-directory-licensing-group-advanced.md)

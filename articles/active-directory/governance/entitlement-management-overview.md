---
title: Mi az Azure ad-ben tagjogosultság-kezelés? (Előzetes verzió) – az Azure Active Directory
description: Tagjogosultság-kezelés Azure Active Directoryban, és hogyan használhatja azt a belső és külső felhasználók számára csoportok, alkalmazások és a SharePoint Online-webhelyekkel való hozzáférés kezelése áttekintést kaphat.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efd3ff8a6e7ddf2aa6242cc322d8a6536a6bd26b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474068"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Mi az Azure ad-ben tagjogosultság-kezelés? (Előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A szervezet alkalmazottai a különböző csoportok, alkalmazások és webhelyek végezhető el a feladat hozzá kell férniük. A hozzáférés-kezelés nagyobb kihívást jelent. A legtöbb esetben nem nem rendezett lista egy felhasználónak adott projekthez tartozó összes erőforrást. A projektvezető rendelkezik a szükséges erőforrásokat, a felhasználók részt vevő, és mennyi a projekt legutóbbi fog beható ismerete. Azonban a projektvezető általában nincs engedélye, amelyben jóváhagyhatja vagy hozzáférést biztosítani másoknak. Ebben a forgatókönyvben beolvasása bonyolultabb, külső egyének és vállalatok együttműködve megkísérlésekor.

Az Azure Active Directory (Azure AD) tagjogosultság-kezelés segítségével kezelheti a belső felhasználók, és a szervezeten kívüli felhasználók csoportokat, alkalmazásokat és a SharePoint Online-webhelyekkel való hozzáférését.

## <a name="why-use-entitlement-management"></a>Tagjogosultság-kezelés miért érdemes használni?

Vállalati szervezetek gyakran szembesülnek olyan kihívásokkal, ha például az erőforrásokhoz való hozzáférés kezelése:

- Felhasználók nem lehetséges, hogy tudja, milyen hozzáféréssel kell rendelkezniük
- Felhasználók nehézségekbe, a megfelelő felhasználók vagy az erőforrások megkeresése
- Felhasználók keresése, és a egy erőforráshoz hozzáférést kapnak a, akkor előfordulhat, hogy tartsa lenyomva az eléréséhez szükséges üzleti célra hosszabb

Ezek a problémák vannak bonyolítja a felhasználók számára, akiknek szükség van egy másik címtár, például az ellátási lánc szervezetek és más üzleti partnerek külső felhasználók hozzáférését. Példa:

- Előfordulhat, hogy szervezetek nem ismeri az adott személyek más címtárakban lehet meghívni őket az összes
- Akkor is, ha a szervezetek is ezeket a felhasználókat meghívni, szervezetek nem emlékeznek egységesen kezelheti a felhasználói hozzáférés az összes

Az Azure AD tagjogosultság-kezelés segítségével, ezek a kihívások megoldása.

## <a name="what-can-i-do-with-entitlement-management"></a>Mire használhatom az tagjogosultság-kezelés?

Íme néhány tagjogosultság-kezelés képességeit:

- Csomagok, amelyek a felhasználók kérhetik kapcsolódó erőforrások létrehozása
- Erőforrás-kérelmek és a hozzáférési lejáratának hogyan-szabályok definiálása
- Belső és külső felhasználók hozzáférését életciklusának szabályozása
- Az erőforrások felügyelet delegálása
- Kérelmek jóváhagyása a jóváhagyók kijelölése
- Előzmények nyomon jelentések létrehozása

Identitáskezelést és tagjogosultság-kezelés áttekintését a következő videó a 2018-as Ignite konferenciáról:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Milyen erőforrásokat lehet kezelni?

Az alábbiakban a típusú erőforrásokat tagjogosultság-kezelés való hozzáférést kezelheti:

- Az Azure AD biztonsági csoportok
- Office 365-csoportok
- Az Azure AD vállalati alkalmazások, például SaaS-alkalmazás és az egyéni integrált alkalmazások, amelyek támogatják az összevonási vagy kiépítése
- A SharePoint Online webhelycsoportokhoz és helyek

Szabályozhatja, hogy az Azure AD biztonsági csoportok vagy az Office 365-csoportok támaszkodik más erőforrásokhoz való hozzáférést is.  Példa:

- Adhat felhasználói licenceket a Microsoft Office 365-höz egy hozzáférés-csomag az Azure AD biztonsági csoportok használatáról és konfigurálásáról [Csoportalapú licencelés](../users-groups-roles/licensing-groups-assign.md) az adott csoporthoz
- Engedélyezheti a felhasználók számára hozzáférést Azure-erőforrások kezelése az Azure AD biztonsági csoportok használatával egy hozzáférési csomagban, és hozzon létre egy [Azure szerepkör-hozzárendelés](../../role-based-access-control/role-assignments-portal.md) az adott csoporthoz

## <a name="what-are-access-packages-and-policies"></a>Melyek a hozzáférési csomagok és a szabályzatok?

Tagjogosultság-kezelés bemutatja a egy *hozzáférés csomag*. Egy hozzáférés-csomag egy felhasználó számára a projekthez, vagy a feladat végrehajtásához szükséges összes erőforrás bundle. Az erőforrások közé tartoznak a csoportok, alkalmazások vagy helyek elérését. Hozzáférési csomagok segítségével szabályozzák a hozzáférést a belső alkalmazottak, valamint a szervezeten kívüli felhasználók számára. Hozzáférési csomagok meghatározott nevű tárolók *katalógusok*.

Hozzáférési csomagok is tartalmaznak egy vagy több *házirendek*. A szabályzat határozza meg, a szabályok vagy guardrails eléréséhez egy hozzáférés-csomagot. A szabályzat engedélyezése kikényszeríti, hogy csak a megfelelő felhasználók kapnak hozzáférést, a megfelelő erőforrásokat és a megfelelő mennyiségű időt.

![Hozzáférés csomag- és szabályzatok](./media/entitlement-management-overview/elm-overview-access-package.png)

Egy hozzáférési csomaghoz és a rájuk vonatkozó szabályzatoknak a hozzáférés-kezelő határozza meg:

- További források
- A felhasználók szerepkörökhöz kell az erőforrások
- Belső és külső felhasználók, amelyek jogosultak arra, hogy a hozzáférés kérése
- Jóváhagyási folyamat és a felhasználók, jóváhagyhatja vagy megtagadja a hozzáférést
- A felhasználó hozzáférésének időtartama

Az alábbi ábrán látható egy példa a különböző elemekhez az tagjogosultság-kezelés. Ez a két példa hozzáférési csomagok jeleníti meg.

- **1 hozzáférési csomag** erőforrásként egyetlen csoportot tartalmaz. Hozzáférési szabályzat, amely lehetővé teszi, hogy a felhasználók a címtárban való hozzáférését van definiálva.
- **2. hozzáférés-csomag** tartalmaz egy csoport, alkalmazás és a SharePoint Online-webhely erőforrásként. Hozzáférés két különböző szabályzatok van meghatározva. Az első házirend lehetővé teszi, hogy a felhasználók a címtárban való hozzáférését. A második szabályzat lehetővé teszi, hogy egy külső könyvtár a felhasználók hozzáférést kérhetnek.

![A jogosultság – áttekintés](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Külső felhasználók

Használatakor a [az Azure AD--vállalatközi (B2B)](../b2b/what-is-b2b.md) meghívása a tapasztalatok már ismernie kell a külső vendégfelhasználóknak az erőforrás-könyvtár állapotba, és dolgozhat e-mail címét. Ez a rövid távú vagy kisebb projekten dolgozik, és résztvevők már jól ismert, de ez jóval nehezebb kezelése, ha a használni kívánt felhasználók számos lehetősége van, vagy ha az idő előrehaladtával változik a résztvevők nagyszerű működik.  Például, előfordulhat, hogy lehet egy másik szervezet használata és az adott szervezet kapcsolatba lépnek egy pont, de idővel további felhasználókat a szervezetben lévő is kell hozzáférést.

Tagjogosultság-kezelés definiálhat egy szabályzatot, amely lehetővé teszi, hogy adja meg, az igénylést egy hozzáférés-csomagot az Azure AD-ben is használó szervezetek. Megadhatja, hogy e jóváhagyásra szükség, és a hozzáférés a lejárati dátumot. Jóváhagyásra szükség, ha akkor is kijelölhet egy jóváhagyó egy vagy több felhasználót a külső szervezet, amely korábban meghívót – mivel valószínű, hogy tudja, melyik külső felhasználók a szervezet hozzáférésre van szükségük. Miután konfigurálta a hozzáférés-csomag, küldhet egy hivatkozást a hozzáférés csomaghoz, a kapcsolattartó személy a szervezetnél, külső. Azt a külső szervezet más felhasználókkal megoszthatja, és használhatják erre a hivatkozásra a hozzáférés csomag kéréséhez.  A címtárba már meghívott szervezetben lévő felhasználók hivatkozás is használhatja.

Amikor jóváhagyják a kérését, tagjogosultság-kezelés is üzembe helyezi a felhasználót a szükséges hozzáférést, amelyek magukban foglalhatják bejelentkezésre kéri a felhasználót, ha még nincsenek a címtárban. Az Azure AD automatikusan létrehoz egy B2B-fiókot a számukra.  Vegye figyelembe, hogy a rendszergazda korábban áramellátásuk beállításával melyik szervezetek engedélyezettek az együttműködéshez, a [B2B engedélyezik vagy megtagadják a lista](../b2b/allow-deny-list.md) engedélyezésére vagy letiltására meghívót küld a más szervezetek számára.  Ha a felhasználó nem engedélyezett az engedélyezési vagy letiltási listát, amelyet ezután nem lehet meghívni.

Mivel nem szeretné, hogy a külső felhasználó hozzáférése az utolsó tartja, megadhatja lejárati dátumot a házirendet, például a 180 nap. 180 nap elteltével a hozzáférése nem újítja meg, ha tagjogosultság-kezelés eltávolítja a hozzáférés-csomaghoz társított összes hozzáférést.  Tagjogosultság-kezelés révén meghívták a felhasználó nem rendelkezik más hozzáférési csomag hozzárendelésekkel, ha majd ha megszakad a utolsó hozzárendelés B2B-fiókjuk 30 napig bejelentkezés blokkolva, és azt követően eltávolítja.  Ez megakadályozza, hogy a szükségtelen fiókok elterjedése.  

## <a name="terminology"></a>Terminológia

Tagjogosultság-kezelés és a hozzá tartozó dokumentáció jobb megértéséhez, tekintse át az alábbi feltételek.

| Kifejezés vagy fogalom | Leírás |
| --- | --- |
| tagjogosultság-kezelés | Egy szolgáltatás, amely hozzárendeli, visszavonja, és felügyeli a hozzáférési csomagok. |
| Access-csomag | Engedélyeket és szabályzatokat olyan felhasználók kérhetik gyűjteménye. Egy hozzáférés-csomag mindig szerepel egy katalógust. |
| hozzáférési kérelem | Egy olyan hozzáférési csomagot elérésére irányuló kérelmet. Egy kérelem általában halad végig egy munkafolyamatot. |
| policy | Szabályok készletét, amely meghatározza az access-életciklus, például hogyan felhasználók is hozzáférhetnek, akik jóváhagyhatja és mennyi ideig a felhasználók hozzáférhetnek. A házirendek például alkalmazottak hozzáférését és külső hozzáférés tartalmazza. |
| catalog | Egy kapcsolódó erőforrások és a hozzáférési csomagok tároló. |
| Általános katalógus | A beépített katalógus, amely mindig elérhető. Az általános katalógus erőforrások felvétele a bizonyos engedélyek szükségesek. |
| resource | Egy eszköz vagy szolgáltatás (például egy csoport, alkalmazás vagy webhely), amely egy felhasználó kaphat engedélyeket. |
| Erőforrás típusa | Típusú erőforrások, például a csoportokat, alkalmazásokat és a SharePoint Online-webhelyhez. |
| erőforrás-szerepkör | Egy gyűjtemény társítva erőforrás. |
| erőforrás-könyvtár | Egy könyvtárat, amely rendelkezik egy vagy több erőforrás megosztására. |
| hozzárendelt felhasználó | Hozzárendelés-hozzáférés egy felhasználó vagy csoport csomag. |
| Engedélyezése | A folyamat a felhasználók kérvényezhetik a kívánt elérhetővé egy hozzáférés-csomagot. |

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Tagjogosultság-kezelés a feladat függvény alapján különböző szerepkörrel rendelkezik.

| Szerepkör | Leírás |
| --- | --- |
| [Felhasználói adminisztrátor](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) | Tagjogosultság-kezelés minden szempontjának kezeléséhez.<br/>Felhasználók és csoportok létrehozása. |
| Katalógus létrehozója | Hozzon létre és katalógusok kezeléséhez. Általában a rendszergazda vagy az erőforrás tulajdonosa. A személy, amely automatikusan létrehozza a katalógus lesz a katalógus első katalógus tulajdonosa. |
| Katalógus tulajdonosa | Szerkessze, és a meglévő katalógusok kezelése. Általában a rendszergazda vagy az erőforrás tulajdonosa. |
| Hozzáférés a Csomagkezelő | Szerkessze, és kezelni átveheti a katalógusban az összes meglévő hozzáférési csomagok. |
| Jóváhagyó | Hagyja jóvá a csomagok hozzáférés kérése. |
| Kérelmező | A kérelem hozzáférési csomagok. |

Az alábbi táblázat ezek a szerepkörök engedélyeit.

| Tevékenység | Felhasználói rendszergazda | Katalógus létrehozója | Katalógus tulajdonosa | Hozzáférés a Csomagkezelő | Jóváhagyó |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Hozzon létre egy új hozzáférési csomagot az általános-katalógusban](entitlement-management-access-package-create.md) | :heavy_check_mark: |  :heavy_check_mark: |  |  |  |
| [Hozzon létre egy új hozzáférési csomagot a katalógusban található](entitlement-management-access-package-create.md) | :heavy_check_mark: |   | :heavy_check_mark: |  |  |
| [Erőforrás-szerepkörökkel és a egy hozzáférés-csomag hozzáadása/eltávolítása](entitlement-management-access-package-edit.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Adja meg, akik kérhet egy hozzáférés-csomag](entitlement-management-access-package-edit.md#add-a-new-policy) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Felhasználó hozzárendelése közvetlenül egy hozzáférés-csomag](entitlement-management-access-package-edit.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Az access-csomag-hozzárendeléssel rendelkezik felhasználó megtekintése](entitlement-management-access-package-edit.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Egy hozzáférés-csomagot kérelmek megtekintése](entitlement-management-access-package-edit.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Egy kérelem kézbesítési hibák megtekintése](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Függőben lévő kérelem megszakítása](entitlement-management-access-package-edit.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Egy hozzáférés-csomag elrejtése](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Töröl egy hozzáférés-csomagot](entitlement-management-access-package-edit.md#delete) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hozzáférési kérelem jóváhagyása](entitlement-management-request-approve.md) |  |  |  |  | :heavy_check_mark: |
| [Katalógus létrehozása](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Erőforrások és-tárolókról a általános katalógus hozzáadása/eltávolítása](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  |  |  |  |
| [Erőforrások és-tárolókról a katalógus hozzáadása/eltávolítása](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Katalógus tulajdonosok hozzáadása vagy csomagkezelők eléréséhez](entitlement-management-catalog-create.md#add-catalog-owners-or-access-package-managers) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Szerkesztheti vagy törölheti a katalógusban](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Speciális felhőkben, mint például az Azure Government, Azure Germany és Azure China 21Vianet nem érhetők el jelenleg ebben az előzetes verzióban használható.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az első hozzáférési csomag létrehozása](entitlement-management-access-package-first.md)
- [Gyakori forgatókönyvek](entitlement-management-scenarios.md)

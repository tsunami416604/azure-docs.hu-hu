---
title: Az Azure Lighthouse nagyvállalati forgatókönyvekben
description: Az Azure Lighthouse képességeinek használatával egyszerűsítheti a több-bérlős felügyeletet egy olyan vállalaton belül, amely több Azure AD-bérlőt használ.
ms.date: 09/25/2019
ms.topic: conceptual
ms.openlocfilehash: 91089a6fb1a965191489e87027ef508c7ebe2aa2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75749207"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Az Azure Lighthouse nagyvállalati forgatókönyvekben

Az [Azure Lighthouse](../overview.md) leggyakoribb forgatókönyve egy olyan szolgáltató, amely az ügyfelek Azure Active Directory (Azure ad) bérlők erőforrásait kezeli. Az Azure Lighthouse képességei azonban a több-bérlős felügyelet egyszerűsítésére is használhatók egy vállalaton belül, amely több Azure AD-bérlőt használ.

## <a name="single-vs-multiple-tenants"></a>Egyetlen és több bérlő

A legtöbb szervezet számára egyszerűbb a felügyelet egyetlen Azure AD-Bérlővel. Az egyik bérlőn belüli összes erőforrás lehetővé teszi a felügyeleti feladatok központosított kezelését a kijelölt felhasználók, felhasználói csoportok vagy az adott bérlőn belüli egyszerű szolgáltatásnév alapján. Ha lehetséges, javasoljuk, hogy egy bérlőt használjon a szervezete számára.

Ugyanakkor előfordulhatnak olyan helyzetek, amikor egy szervezetnek több Azure AD-bérlőt kell fenntartania. Bizonyos esetekben ez ideiglenes helyzet lehet, ahogy a beszerzések megtörténtek, és a hosszú távú bérlői konszolidációs stratégia eltarthat egy ideig. Előfordulhat, hogy egy szervezetnek folyamatosan több bérlőt kell fenntartania (a teljesen független leányvállalatok, földrajzi vagy jogi követelmények miatt stb.). Ha több-bérlős architektúrára van szükség, az Azure-beli delegált erőforrás-kezelés használatával központosíthatja és egyszerűsítheti a felügyeleti műveleteket. A több bérlőből származó előfizetések beállíthatók az Azure-beli [delegált erőforrás-kezeléshez](azure-delegated-resource-management.md), amely lehetővé teszi a kijelölt felhasználók számára, hogy központosított és méretezhető módon hajtsák végre a [bérlők közötti felügyeleti funkciókat](cross-tenant-management-experience.md) .

## <a name="tenant-management-architecture"></a>Bérlői felügyeleti architektúra

Ha több bérlő között központosítja a felügyeleti műveleteket, meg kell határoznia, hogy melyik bérlő fogja tartalmazni a többi bérlő felügyeleti műveleteit végző felhasználókat. Más szóval meg kell határoznia, hogy melyik bérlő lesz a bérlők kezelése más bérlők számára.

Tegyük fel például, hogy a szervezet egyetlen Bérlővel rendelkezik, amely az *a bérlőt*hívja meg. A szervezet ezután két további bérlőt, a *B bérlőt* és a *C bérlőt*szerzi be, és olyan üzleti okokból áll rendelkezésére, amelyekkel külön bérlőként kell karbantartani őket.

A szervezet ugyanazt a házirend-definíciót, biztonsági mentési gyakorlatot és biztonsági folyamatokat kívánja használni az összes bérlőn. Mivel az A bérlőn belül a feladatok elvégzéséhez a felhasználók (beleértve A felhasználói csoportokat és az egyszerű szolgáltatásokat is) felelősek, az összes előfizetést bevezetheti a B bérlőre és a C bérlőre, hogy az A bérlőn belül ugyanazon felhasználók is elvégezhetik ezeket a feladatokat.

![A bérlő felhasználói A B bérlőn és C bérlőn lévő erőforrásokat kezelhetik](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Biztonsági és hozzáférési megfontolások

A legtöbb nagyvállalati forgatókönyv esetében érdemes egy teljes előfizetést delegálni az Azure-beli delegált erőforrás-kezeléshez, de csak meghatározott erőforráscsoportokat delegálhat egy előfizetésen belül.

Mindkét esetben ügyeljen arra, hogy [kövesse a legalacsonyabb jogosultsági szint elvét, amikor meghatározza, hogy mely felhasználók férhetnek hozzá az erőforrásokhoz](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Így biztosíthatja, hogy a felhasználók csak a szükséges feladatok elvégzéséhez szükséges engedélyekkel rendelkezzenek, és csökkentik a véletlen hibák esélyét.

Az Azure Lighthouse és az Azure-beli meghatalmazott erőforrás-kezelés csak logikai kapcsolatokat biztosít a bérlők és a felügyelt bérlők között, és nem fizikailag helyezi át az adatátvitelt és az erőforrásokat. Továbbá a hozzáférés mindig csak egy irányba mutat, a bérlő kezelése a felügyelt bérlők felé.  A kezelő bérlő felhasználói és csoportjai továbbra is használhatják a többtényezős hitelesítést a felügyelt bérlői erőforrásokon végzett felügyeleti műveletek végrehajtásakor.

A belső vagy külső irányítási és megfelelőségi guardrails rendelkező vállalatok az [Azure-tevékenység naplóit](../../azure-monitor/platform/platform-logs-overview.md) használhatják az átláthatósági követelmények teljesítéséhez. Ha a vállalati bérlők felügyeleti és felügyelt bérlői kapcsolatokat létesítettek, az egyes bérlők felhasználói a naplózott tevékenységek megtekintésével megtekinthetik és láthatják a másik bérlő felhasználói által végrehajtott műveleteket.

## <a name="onboarding-process-considerations"></a>Előkészítési folyamat szempontjai

Az előfizetések (vagy az előfizetésen belüli erőforráscsoportok) az Azure-beli delegált erőforrás-kezelésbe helyezhetők, akár magántulajdonban, akár nyilvánosan, akár az Azure Marketplace-en közzétett, felügyelt szolgáltatásokon keresztül, akár a Azure Resource Manager-sablonok üzembe helyezésével.

Mivel a vállalati felhasználók általában közvetlenül hozzáférhetnek a vállalat bérlői számára, és nincs szükség a felügyeleti ajánlatok értékesítésére vagy előléptetésére, általában gyorsabb és egyszerűbb a Azure Resource Manager-sablonokkal való üzembe helyezés. Noha a [bevezetési útmutatóban](../how-to/onboard-customer.md)a szolgáltatókra és az ügyfelekre is hivatkozunk, a vállalatok ugyanezeket a folyamatokat használhatják.

Ha szeretné, a vállalaton belüli bérlőket a [felügyelt szolgáltatások Azure Marketplace-re való közzétételével](../how-to/publish-managed-services-offers.md)lehet előkészíteni. Annak biztosítása érdekében, hogy az ajánlat csak a megfelelő bérlők számára legyen elérhető, győződjön meg arról, hogy a csomagok magánjellegűként vannak megjelölve. Egy privát csomaggal megadhatja a bevezetéshez tervezett összes bérlő előfizetés-azonosítóját, és senki más nem fogja tudni felvenni az ajánlatot.

## <a name="terminology-notes"></a>Terminológiai megjegyzések

A vállalaton belüli több-bérlős felügyelet esetén az Azure Lighthouse dokumentációjában található szolgáltatókra való hivatkozásokat a vállalaton belüli felügyeletre, azaz a bérlőre vonatkozik, amely a többi bérlő erőforrásait kezelő felhasználókat tartalmazza az Azure-beli delegált erőforrás-kezeléssel. Hasonlóképpen, az ügyfelekre mutató hivatkozások is megtekinthetők azokra a bérlők esetében, amelyek az erőforrásokat a bérlők felügyelete alatt lévő felhasználókon keresztül kezelik.

Például a fent ismertetett példában az A bérlő a szolgáltató bérlője (a bérlők kezelése) és a B bérlő, a C bérlő pedig az ügyfél bérlője lehet.

Ebben a példában a bérlő a megfelelő engedélyekkel rendelkező felhasználók [megtekinthetik és kezelhetik a delegált erőforrásokat](../how-to/view-manage-customers.md) a Azure Portal **saját ügyfelek** lapján. Hasonlóképpen, a B bérlő és a bérlő C felhasználója a megfelelő engedélyekkel [megtekintheti és kezelheti az a bérlőhöz delegált erőforrásokat](../how-to/view-manage-service-providers.md) a Azure Portal **szolgáltató** lapján.

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről](cross-tenant-management-experience.md).
- Megismerheti az [Azure által delegált erőforrás-kezelés](azure-delegated-resource-management.md) részleteit.
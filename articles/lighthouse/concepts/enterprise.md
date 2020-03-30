---
title: Az Azure Lighthouse nagyvállalati forgatókönyvekben
description: Az Azure Lighthouse képességei segítségével egyszerűsítheti a több Azure AD-bérlőt használó vállalaton belül a bérlők közötti felügyeletet.
ms.date: 09/25/2019
ms.topic: conceptual
ms.openlocfilehash: 91089a6fb1a965191489e87027ef508c7ebe2aa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749207"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Az Azure Lighthouse nagyvállalati forgatókönyvekben

Az [Azure Lighthouse](../overview.md) leggyakoribb forgatókönyve az ügyfelek Azure Active Directory (Azure AD) bérlőierőforrások kezelésével foglalkozó szolgáltató. Az Azure Lighthouse képességei azonban a több Azure AD-bérlőt használó vállalaton belüli bérlők közötti felügyelet egyszerűsítésére is használhatók.

## <a name="single-vs-multiple-tenants"></a>Egy és több bérlő

A legtöbb szervezet számára a felügyelet egyszerűbb egyetlen Azure AD-bérlővel. Az összes erőforrás egy bérlőn belül lehetővé teszi a felügyeleti feladatok központosítását a kijelölt felhasználók, felhasználói csoportok vagy egyszerű szolgáltatás a bérlőn belül. Azt javasoljuk, hogy egy bérlő a szervezet számára, amikor csak lehetséges.

Ugyanakkor vannak olyan helyzetek, amelyek több Azure AD-bérlő karbantartásához szükséges szervezet. Bizonyos esetekben ez átmeneti helyzet lehet, mivel amikor akvizíciók történtek, és a hosszú távú bérlői konszolidációs stratégia meghatározása némi időt vesz igénybe. Előfordulhat, hogy egy szervezetnek folyamatosan több bérlőt kell fenntartania (teljesen független leányvállalatok, földrajzi vagy jogi követelmények és így tovább) miatt. Azokban az esetekben, ahol több-bérlős architektúra van szükség, az Azure delegált erőforrás-kezelés segítségével központosíthatja és egyszerűsítheti a felügyeleti műveleteket. Előfizetések több bérlőlehet az Azure [delegált erőforrás-kezelése,](azure-delegated-resource-management.md)amely lehetővé teszi a kijelölt felhasználók egy felügyelt bérlő i [hajtani a bérlők közötti felügyeleti funkciók](cross-tenant-management-experience.md) at egy központosított és méretezhető módon.

## <a name="tenant-management-architecture"></a>Bérlőkezelési architektúra

A felügyeleti műveletek több bérlő közötti központosításakor meg kell határoznia, hogy melyik bérlő tartalmazza a többi bérlő felügyeleti műveleteit végző felhasználókat. Más szóval meg kell határoznia, hogy melyik bérlő lesz a többi bérlő irányító bérlője.

Tegyük fel például, hogy a szervezetnek egyetlen bérlője van, amelyet *az A bérlőnek*hívunk. A szervezet ezután két további bérlőt, *a B bérlőt* és *a C bérlőt*szerez be, és üzleti okai vannak, amelyek megkövetelik, hogy külön bérlőként tartsa fenn őket.

A szervezet ugyanazokat a házirend-definíciókat, biztonsági mentési eljárásokat és biztonsági folyamatokat szeretné használni az összes bérlőben. Mivel már vannak olyan felhasználók (beleértve a felhasználói csoportokat és a szolgáltatásnéveket is), amelyek felelősek ezeknek a feladatoknak a bérlőn belüli feladatok végrehajtásáért, a B bérlőn és a C bérlőn belüli összes előfizetést beviheti, így az A bérlő ben lévő felhasználók elvégezhetik ezeket a Feladatok.

![Az "A" bérlő felhasználói, akik a B és a C bérlő erőforrásait kezelik](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Biztonsági és hozzáférési szempontok

A legtöbb vállalati forgatókönyvben érdemes delegálni egy teljes előfizetést az Azure delegált erőforrás-kezeléséhez, bár csak adott erőforráscsoportokat delegálhat egy előfizetésen belül.

Akárhogy is, győződjön meg róla, hogy [kövesse a minimális jogosultság elvét annak meghatározásakor, hogy mely felhasználók férhetnek hozzá az erőforrásokhoz.](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege) Ezzel biztosíthatja, hogy a felhasználók csak a szükséges feladatok elvégzéséhez szükséges engedélyekkel rendelkezzenek, és csökkenti a véletlen hibák esélyét.

Az Azure Lighthouse és az Azure delegált erőforrás-kezelése csak logikai kapcsolatokat biztosít a kezelő bérlő és a felügyelt bérlők között, nem pedig fizikailag áthelyezi az adatokat vagy az erőforrásokat. Továbbá a hozzáférés mindig csak egy irányba megy, a kezelő bérlőtől a felügyelt bérlőkig.  A felügyelt bérlői erőforrásokon végzett felügyeleti műveletek végrehajtásakor a felügyeleti műveletek végrehajtásakor továbbra is használnia kell a többtényezős hitelesítést a felügyeleti bérlők nél.

A belső vagy külső cégirányítási és megfelelőségi korlátokkal rendelkező vállalatok az [Azure Activity naplók at használhatják](../../azure-monitor/platform/platform-logs-overview.md) az átláthatósági követelményeik teljesítéséhez. Ha a vállalati bérlők felügyelt és felügyelt bérlői kapcsolatokat hoztak létre, az egyes bérlők felhasználói figyelhetik és áttekinthetik a másik bérlő felhasználói által végrehajtott műveleteket a naplózott tevékenységek megtekintésével.

## <a name="onboarding-process-considerations"></a>A bevezetési folyamattal kapcsolatos szempontok

Az előfizetések (vagy az előfizetésen belüli erőforráscsoportok) az Azure delegált erőforrás-kezelésére is beszervezhetők az Azure Resource Manager-sablonok telepítésével vagy az Azure Piactéren közzétett felügyelt szolgáltatások ajánlatain keresztül, akár magánúton, akár Nyilvánosan.

Mivel a vállalati felhasználók általában közvetlen hozzáférést kapnak a vállalati bérlőkhöz, és nincs szükség felügyeleti ajánlatok forgalmazására vagy népszerűsítésére, általában gyorsabb és egyszerűbb közvetlenül telepíteni az Azure Resource Manager-sablonokkal. Míg a [bevezetési útmutatóban](../how-to/onboard-customer.md)a szolgáltatókra és az ügyfelekre hivatkozunk, a vállalatok ugyanazokat a folyamatokat használhatják.

Ha szeretné, a vállalati bérlők egy felügyelt [szolgáltatások ajánlatának az Azure Marketplace-en való közzétételével](../how-to/publish-managed-services-offers.md)is beszállhatnak. Annak érdekében, hogy az ajánlat csak a megfelelő bérlők számára érhető el, győződjön meg arról, hogy a csomagok privátként vannak megjelölve. Egy privát csomag, akkor adja meg az előfizetési azonosítók minden bérlő, amely azt tervezi, hogy a fedélzeten, és senki más nem lesz képes-hoz kap-a felajánl.

## <a name="terminology-notes"></a>Terminológia megjegyzések

A vállalaton belüli bérlők közötti felügyelet esetében az Azure Világítótorony dokumentációjában szereplő szolgáltatókra mutató hivatkozások érthetőek lehetnek úgy, hogy a vállalaton belüli irányító bérlőre vonatkozzanak, azaz a bérlőre, amely tartalmazza az erőforrásokat kezelő felhasználókat. más bérlőknél az Azure delegált erőforrás-kezelése révén. Hasonlóképpen az ügyfelekre mutató hivatkozások is értelmezhetők, hogy azok a bérlők, amelyek a kezelő bérlő felhasználóiáltal kezelendő erőforrásokat delegálnak.

Például a fent leírt példában bérlő "A" bérlő lehet tekinteni, mint a szolgáltató bérlő (a kezelő bérlő) és a B bérlő és a C bérlő lehet tekinteni, mint az ügyfél bérlők.

Ebben a példában a megfelelő engedélyekkel rendelkező bérlő i a felhasználók [megtekinthetik és kezelhetik a delegált erőforrásokat](../how-to/view-manage-customers.md) az Azure Portal **Ügyfelei** lapon. Hasonlóképpen a megfelelő engedélyekkel rendelkező B bérlő és C bérlő felhasználói megtekinthetik és kezelhetik az Okat az Azure Portal **Service-szolgáltatók** lapján [delegált erőforrásokat.](../how-to/view-manage-service-providers.md)

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről.](cross-tenant-management-experience.md)
- Megismerheti az [Azure által delegált erőforrás-kezelés](azure-delegated-resource-management.md) részleteit.
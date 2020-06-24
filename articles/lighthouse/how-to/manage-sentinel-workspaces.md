---
title: Azure Sentinel-munkaterületek kezelése nagy méretekben
description: Ismerje meg, hogyan kezelheti hatékonyan az Azure Sentinelt a delegált felhasználói erőforrásokon.
ms.date: 06/17/2020
ms.topic: how-to
ms.openlocfilehash: bca5b6fdc84fa5a7a5553fe64c0218c5f0b44aa6
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85269069"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Azure Sentinel-munkaterületek kezelése nagy méretekben

Szolgáltatóként több ügyfél-bérlőt is felkészített az Azure-beli delegált erőforrás-kezeléshez. Az Azure Lighthouse lehetővé teszi a szolgáltatók számára, hogy egyszerre több Azure Active Directory (Azure AD) bérlőre kiterjedő műveleteket hajtsanak végre, így hatékonyabbá téve a felügyeleti feladatokat.

Az Azure Sentinel biztonsági elemzési és veszélyforrási intelligenciát biztosít, így egyetlen megoldást nyújt a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a fenyegetésekre való reagálásra. Az Azure Lighthouse használatával több Azure Sentinel-munkaterületet is kezelhet a bérlők között a skálán. Ez olyan forgatókönyveket tesz lehetővé, mint például a lekérdezések futtatása több munkaterületen, illetve munkafüzetek létrehozása a csatlakoztatott adatforrásokból származó adatok megjelenítéséhez és figyeléséhez. Az IP-címek, például a lekérdezések és a forgatókönyvek megmaradnak a kezelő bérlőben, de az ügyfél-bérlők biztonsági felügyeletét is elvégezheti.

Ez a témakör áttekintést nyújt arról, hogyan használható az [Azure Sentinel](../../sentinel/overview.md) skálázható módon a több-bérlős láthatóság és a felügyelt biztonsági szolgáltatások számára.

## <a name="architectural-considerations"></a>Építészeti megfontolások

Egy olyan felügyelt biztonsági szolgáltató (MSSP) esetében, amely az Azure Sentinel használatával szeretné kiépíteni a szolgáltatásként nyújtott biztonsági szolgáltatást, egyetlen biztonsági operatív központot (SOC) lehet szükség az egyes ügyfelek bérlői számára üzembe helyezett Azure Sentinel-munkaterületek központilag figyelésére, kezelésére és konfigurálására. Hasonlóképpen, a több Azure AD-Bérlővel rendelkező vállalatok központilag kezelhetik több, a bérlők között üzembe helyezett Azure Sentinel-munkaterületet.

Ez a központi telepítési modell a következő előnyökkel jár:

- Az adattulajdonos az egyes felügyelt bérlők esetében is megmarad.
- A támogatja a földrajzi határokon belül tárolt adattárolási követelményeket.
- Biztosítja az adatelkülönítést, mivel a több ügyfélhez tartozó adategységek nem ugyanabban a munkaterületen vannak tárolva. 
- Megakadályozza a felügyelt bérlők adatainak kiszűrése, így biztosítva az adatok megfelelőségét.
- A kapcsolódó költségeket a bérlők kezelése helyett az egyes felügyelt bérlők terhelik.
- Az összes adatforrásból és adatösszekötőből származó, az Azure Sentinel szolgáltatással integrált adatok (például az Azure AD-tevékenységek naplói, az Office 365-naplók vagy a Microsoft Threat Protection-riasztások) az egyes ügyfelek bérlőn belül maradnak.
- Csökkenti a hálózati késést.
- Egyszerűen hozzáadhat vagy eltávolíthat új leányvállalatokat vagy ügyfeleket.

## <a name="granular-role-based-access-control-rbac"></a>Részletes szerepköralapú hozzáférés-vezérlés (RBAC)

A MSSP által kezelt összes ügyfél-előfizetést [Az Azure-beli delegált erőforrás-kezeléshez](onboard-customer.md)kell bevezetni. Ez lehetővé teszi a bérlők számára, hogy az ügyfél-bérlők számára üzembe helyezett Azure Sentinel-munkaterületeken felügyeleti műveleteket férhessenek hozzá és végezzenek el.

Az engedélyek létrehozásakor az Azure Sentinel beépített szerepköreit hozzárendelheti a felhasználókhoz, csoportokhoz vagy egyszerű szolgáltatásokhoz a kezelő bérlőben:

- [Azure Sentinel-olvasó](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel-válaszadó](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel közreműködő](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

További beépített szerepköröket is szeretne rendelni további függvények végrehajtásához. További információ az Azure Sentinel használatával használható szerepkörökről: [engedélyek az Azure sentinelben](../../sentinel/roles.md).

Az ügyfelek bevezetését követően a kijelölt felhasználók bejelentkezhetnek a kezelő bérlőbe, és [közvetlenül hozzáférhetnek az ügyfél Azure Sentinel-munkaterületéhez](../../sentinel/multiple-tenants-service-providers.md) a hozzárendelt szerepkörökkel.

## <a name="view-and-manage-incidents-across-workspaces"></a>Incidensek megtekintése és kezelése munkaterületek között

Ha több ügyfél számára kezel Azure Sentinel-erőforrásokat, egyszerre több bérlőn is megtekintheti és kezelheti az incidenseket több munkaterületen. További információ: az [incidensek használata egyszerre sok munkaterületen](../../sentinel/multiple-workspace-view.md) , és az [Azure Sentinel kibővítése a munkaterületeken és a bérlők között](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Győződjön meg arról, hogy a felügyeleti bérlő felhasználói olvasási és írási engedélyekkel rendelkeznek a felügyelt munkaterületekhez. Ha egy felhasználó csak olvasási jogosultsággal rendelkezik egyes munkaterületeken, figyelmeztető üzenetek jelenhetnek meg az incidensek kiválasztásakor az adott munkaterületeken, és a felhasználó nem módosíthatja ezeket az incidenseket, vagy bármely más Ön által választott elemet (még akkor is, ha rendelkezik a többire vonatkozó engedélyekkel).

## <a name="configure-playbooks-for-mitigation"></a>Forgatókönyvek beállítása a mérsékléshez

A forgatókönyvek a riasztások aktiválásakor automatikus mérséklésre [használhatók](../../sentinel/tutorial-respond-threats-playbook.md) . Ezek a forgatókönyvek manuálisan is futtathatók, vagy automatikusan futtathatók, ha adott riasztásokat indítanak el. A forgatókönyvek a bérlő vagy az ügyfél bérlője számára is üzembe helyezhetők, és azok a válaszadási eljárások, amelyek alapján a bérlő felhasználói a biztonsági fenyegetésekre reagálva műveleteket hajtanak végre.

## <a name="create-cross-tenant-workbooks"></a>Több-bérlős munkafüzetek létrehozása

[Azure monitor munkafüzetek az Azure sentinelben](../../sentinel/overview.md#workbooks) a csatlakoztatott adatforrásokból származó adatok vizualizációját és figyelését teszik lehetővé az elemzések megszerzéséhez. Használhatja az Azure Sentinel beépített munkafüzet-sablonjait, vagy létrehozhat egyéni munkafüzeteket a forgatókönyvekhez.

A felügyeleti bérlőben munkafüzetek helyezhetők üzembe, és méretezhető irányítópultokat hozhat létre, amelyekkel figyelheti és lekérdezheti az ügyfelek bérlői közötti adatait. További információ: több [munkaterület figyelése](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). Vegye figyelembe, hogy egyes funkciók [nem támogatottak több munkaterületen](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

A munkafüzeteket közvetlenül is üzembe helyezheti egy egyéni bérlőben, amelyet az adott ügyfélhez tartozó forgatókönyvek esetében kezel.

## <a name="run-queries-across-azure-sentinel-workspaces"></a>Lekérdezések futtatása Azure Sentinel-munkaterületeken

A fenyegetés észleléséhez központilag hozhat létre és menthet Log Analytics-lekérdezéseket a bérlő kezelése szolgáltatásban. Ezeket a lekérdezéseket ezután a Union operátor és a munkaterület () kifejezés használatával lehet futtatni az összes ügyfél Azure Sentinel-munkaterületén. További információ: több [munkaterület lekérdezése](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Automatizálás használata a munkaterületek közötti felügyelethez

Az Automation használatával több Azure Sentinel-munkaterület is kezelhető, és a [vadászati lekérdezések](../../sentinel/hunting.md), a forgatókönyvek és a munkafüzetek is konfigurálhatók. További információ: a [munkaterület-kezelés automatizálással](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

Vegye figyelembe, hogy egyes funkciók [jelenleg nem támogatottak több munkaterületen](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

## <a name="next-steps"></a>További lépések

- További információ az [Azure sentinelről](../../sentinel/overview.md).
- Tekintse át az [Azure Sentinel díjszabási oldalát](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).


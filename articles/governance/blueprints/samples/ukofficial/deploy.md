---
title: Az Egyesült Királyság hivatalos & egyesült királyságbeli NHS-tervezetminták
description: Az Egyesült Királyság hivatalos és egyesült királyságbeli NHS-tervezetmintáinak üzembe helyezése, beleértve a tervezetösszetevő paraméterének részleteit.
ms.date: 06/26/2019
ms.topic: sample
ms.openlocfilehash: 3d69f4477163618aa225150ac6352a16e901f826
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546396"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Az Egyesült Királyság hivatalos és egyesült királyságbeli NHS-tervezetmintáinak telepítése

Az Egyesült Királyság hivatalos és egyesült királyságbeli NHS-tervezetmintáinak telepítéséhez a következő lépéseket kell tenni:

> [!div class="checklist"]
> - Új tervrajz létrehozása a mintából
> - A minta példányának megjelölése **közzétettként**
> - A tervrajz másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először valósítsa meg a tervtervezet minta egy új tervtervezet a környezetben a minta, mint a kezdő.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. A bal oldali **Első lépések** lapon válassza a **Létrehozás** gombot a _Rajzrajz létrehozása_csoportban.

1. Keresse meg az **Egyesült Királyság hivatalos** vagy egyesült **királyságbeli** NHS-tervezetmintáját _az Egyéb minták csoportban,_ és válassza **a Minta használata**lehetőséget.

1. Adja meg a tervminta _alapjait:_

   - **Tervrajz neve:** Adja meg a tervezet minta példányának nevét.
   - **Definíció helye:** Használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az _Eltérések_ lapot a lap tetején, vagy a **Tovább: Eltérések** a lap alján.

1. Tekintse át a tervmintát kiálló összetevők listáját. Sok összetevő nek vannak paraméterei, amelyeket később definiálunk. Válassza **a Vázlat mentése lehetőséget,** ha befejezte a tervezetminta áttekintését.

## <a name="publish-the-sample-copy"></a>A mintapéldány közzététele

A tervtervezet minta példánya most már létre lett hozva a környezetében. **Vázlat** módban jön létre, és közzé kell **tenni,** mielőtt hozzárendelhető és üzembe helyezhető. A tervezetminta másolata testreszabható a környezetés az igények igényei szerint, de a módosítás áthelyezheti azt a szabványtól.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, majd válassza ki.

1. Válassza a **Tervrajz közzététele** lehetőséget a lap tetején. A jobb oldali új lapon adja meg a tervezetminta egy **verzióját.** Ez a tulajdonság akkor hasznos, ha később módosítja a munkát. Adjon **változás megjegyzi,** mint például a "First version közzé az Egyesült Királyság hivatalos vagy uk NHS tervezet mintát." Ezután válassza a **Közzététel** lehetőséget a lap alján.

## <a name="assign-the-sample-copy"></a>A mintapéldány hozzárendelése

Miután a tervezetminta példánya sikeresen **közzélett,,** hozzárendelhető egy előfizetéshez a felügyeleti csoporton belül, amelybe mentve mentették. Ez a lépés az, ahol paraméterek et biztosítanak, hogy a tervezet minta másolatának minden egyes központi telepítése egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, majd válassza ki.

1. Válassza **a Blueprint hozzárendelése** lehetőséget a tervezetdefiníciós lap tetején.

1. Adja meg a tervezet hozzárendelésének paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések:** Válasszon ki egy vagy több előfizetést, amely abban a felügyeleti csoportban van, amelybe a tervezetminta másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez egy-egy hozzárendelés jön létre a megadott paraméterek használatával.
     - **Hozzárendelés neve**: A név előre ki van töltve a tervrajz neve alapján.
       Szükség szerint változtassa meg, vagy hagyja úgy, ahogy van.
     - **Hely**: Válassza ki azt a régiót, amelyben a felügyelt identitást létre szeretné hozni. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [Felügyelt identitások az Azure-erőforrásokhoz.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Blueprint-definíciós verzió:** Válasszon egy **közzétett** változata a tervezet minta példánya.

   - Hozzárendelés zárolása

     Válassza ki a környezetéhez való tervezetzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszer hez rendelt_ felügyelt identitás beállítást.

   - Műtermék-paraméterek

     Az ebben a szakaszban meghatározott paraméterek a műtermékre vonatkoznak, amely alatt definiálva van. Ezek a paraméterek [dinamikus paraméterek,](../../concepts/parameters.md#dynamic-parameters) mivel a terv hozzárendelése során vannak definiálva. A teljes lista- vagy műtermék-paramétereket és azok leírását a [Műtermék paraméterek táblázatban láthatja.](#artifact-parameters-table)

1. Miután az összes paramétert megadta, válassza a **Hozzárendelés** lehetőséget a lap alján. A tervrajz-hozzárendelés jön létre, és a műtermék üzembe helyezése kezdődik. A telepítés nagyjából egy órát vesz igénybe. A telepítés állapotának ellenőrzéséhez nyissa meg a tervhozzárendelést.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervezetminták **ingyenesek.** Az Azure-erőforrások [ára termékenként.](https://azure.microsoft.com/pricing/) A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segítségével becsülje meg a tervezet minta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Műtermék-paraméterek táblája

Az alábbi táblázat a tervezet összetevő paramétereinek listáját tartalmazza:

Műtermék neve|Műtermék típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Az Egyesült Királyság hivatalos vagy egyesült királyságbeli NHS-ének tervezetkezdeményezése|Szabályzat-hozzárendelés |A diagnosztikai naplók naplózására szolgáló erőforrástípusok (Politika: Blueprint kezdeményezés az Egyesült Királyság hivatalos vagy egyesült királyságbeli NHS-hez) |Naplózandó erőforrástípusok listája, ha a diagnosztikai napló beállítása engedélyezve van.  Az elfogadható értékeket lásd: [Támogatott szolgáltatások, sémák és kategóriák az Azure diagnosztikai naplók.](../../../../azure-monitor/platform/diagnostic-logs-schema.md) |
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez |Szabályzat-hozzárendelés |Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek \[támogatták a hatókörhöz hozzáadni kívánt Linux operációs rendszert (Szabályzat: Előzetes verzió\]: Log Analytics-ügynök linuxos virtuális gépekhez történő telepítése) |(Nem kötelező) Az alapértelmezett érték _nincs_. További információ: [Log Analytics-munkaterület létrehozása az Azure Portalon.](../../../../azure-monitor/learn/quick-create-workspace.md) |
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez |Szabályzat-hozzárendelés |Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek \[támogatták a Windows operációs rendszer hatókörhöz való hozzáadását (Házirend: Előzetes verzió:\]Log Analytics ügynök telepítése Windows virtuális gépekhez) |(Nem kötelező) Az alapértelmezett érték _nincs_. További információ: [Log Analytics-munkaterület létrehozása az Azure Portalon.](../../../../azure-monitor/learn/quick-create-workspace.md) |

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Egyesült Királyság hivatalos és az egyesült királyságbeli NHS tervezetminták telepítésének lépéseit, látogasson el a következő cikkekbe, hogy megismerje az áttekintést és az ellenőrzés feltérképezését:

> [!div class="nextstepaction"]
> [Egyesült Királyság hivatalos és az Egyesült Királyság NHS tervrajzok - Áttekintés](./index.md)
> [uk hivatalos és brit NHS tervrajzok - Control feltérképezése](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)
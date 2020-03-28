---
title: CAF Foundation-tervezet mintatelepítése
description: Üzembe helyezése lépéseket a CAF Foundation tervezet minta, beleértve a tervezet összetevő paraméter részleteit.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: df17f8c3f539e25635ea4718be9d51d5e5e3f708
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74545522"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Telepítse a Microsoft Cloud Adoption Framework for Azure Foundation tervezetminta

A Microsoft Cloud Adoption Framework for Azure (CAF) Foundation blueprint minta központi telepítéséhez a következő lépéseket kell elvégezni:

> [!div class="checklist"]
> - Új tervrajz létrehozása a mintából
> - A minta példányának megjelölése **közzétettként**
> - A tervrajz másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először valósítsa meg a tervtervezet minta egy új tervtervezet a környezetben a minta, mint a kezdő.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. A bal oldali **Első lépések** lapon válassza a **Létrehozás** gombot a _Rajzrajz létrehozása_csoportban.

1. Keresse meg a **CAF Foundation** tervezet minta alatt _Egyéb minták_ és válassza a Használja ezt **a mintát**.

1. Adja meg a tervminta _alapjait:_

   - **Tervrajz neve:** Adja meg a caf alapítvány tervminta példányának nevét.
   - **Definíció helye:** Használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az _Eltérések_ lapot a lap tetején, vagy a **Tovább: Eltérések** a lap alján.

1. Tekintse át a tervmintát kiálló összetevők listáját. Sok összetevő nek vannak paraméterei, amelyeket később definiálunk. Válassza **a Vázlat mentése lehetőséget,** ha befejezte a tervezetminta áttekintését.

## <a name="publish-the-sample-copy"></a>A mintapéldány közzététele

A tervtervezet minta példánya most már létre lett hozva a környezetében. **Vázlat** módban jön létre, és közzé kell **tenni,** mielőtt hozzárendelhető és üzembe helyezhető. A tervezet minta példánya testreszabható a környezet és az igények, de a módosítás elmozdíthatja a CAF Foundation tervrajz.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, majd válassza ki.

1. Válassza a **Tervrajz közzététele** lehetőséget a lap tetején. A jobb oldali új lapon adja meg a tervezetminta egy **verzióját.** Ez a tulajdonság akkor hasznos, ha később módosítja a munkát. Adjon **meg módosítási megjegyzéseket,** például "A CAF Foundation tervezetmintából közzétett első verzió". Ezután válassza a **Közzététel** lehetőséget a lap alján.

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
     - **Hely**: Válassza ki azt a régiót, amelyben a felügyelt identitást létre szeretné hozni.
     - Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja.
       További információ: [Felügyelt identitások az Azure-erőforrásokhoz.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Blueprint-definíciós verzió:** Válasszon egy **közzétett** változata a tervezet minta példánya.

   - Hozzárendelés zárolása

     Válassza ki a környezetéhez való tervezetzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Válassza az alapértelmezett _rendszerhozzárendelt_ felügyelt identitás beállítást vagy a _felhasználó által hozzárendelt_ identitásbeállítást.

   - Tervparaméterek

     Az ebben a szakaszban meghatározott paramétereket a tervezet definíciójában szereplő számos műtermék használja a konzisztencia biztosításához.

     - **Szervezet:** Adja meg a szervezet nevét, például contoso, egyedinek kell lennie.
     - **Azure régió**: Válassza ki az Azure-régió t.
     - **Engedélyezett helyek:** Mely Azure-régiók engedélyezi az erőforrások beépítését?
     
   - Műtermék-paraméterek

     Az ebben a szakaszban meghatározott paraméterek a műtermékre vonatkoznak, amely alatt definiálva van. Ezek a paraméterek [dinamikus paraméterek,](../../concepts/parameters.md#dynamic-parameters) mivel a terv hozzárendelése során vannak definiálva. A teljes lista- vagy műtermék-paramétereket és azok leírását a [Műtermék paraméterek táblázatban láthatja.](#artifact-parameters-table)

1. Miután az összes paramétert megadta, válassza a **Hozzárendelés** lehetőséget a lap alján. A tervrajz-hozzárendelés jön létre, és a műtermék üzembe helyezése kezdődik. A telepítés nagyjából egy órát vesz igénybe. A telepítés állapotának ellenőrzéséhez nyissa meg a tervhozzárendelést.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervezetminták **ingyenesek.** Az Azure-erőforrások [ára termékenként.](https://azure.microsoft.com/pricing/) A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segítségével becsülje meg a tervezet minta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Műtermék-paraméterek táblája

Az alábbi táblázat a tervezet összetevő paramétereinek listáját tartalmazza:

|Műtermék neve|Műtermék típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Engedélyezett tárfiók-termékváltozatok|Szabályzat-hozzárendelés|Policy_Allowed-StorageAccount-SKUs|Diagnosztikai naplótárfiókokban használt termékváltozat|
|Engedélyezett virtuálisgép-skus|Szabályzat-hozzárendelés|Policy_Allowed-VM-SKUs|Engedélyezett virtuálisgép-skus|
|CostCenter TAG hozzáfűzése erőforráscsoportokhoz|Szabályzat-hozzárendelés|Policy_CostCenter_Tag|A CostCenter TAG és annak értéke hozzáfűzése az erőforráscsoportból|
|Olyan erőforrástípusok, amelyeket nem kíván engedélyezni a környezetben|Szabályzat-hozzárendelés|Házirend-_Allowed-erőforrás-típusok|Mely Azure-erőforrásokat szeretné engedélyezni a környezetében?|
|A Key Vault telepítése|Resource Manager-sablon|KV-AccessPolicy|**Zárolva** – Az Azure <Object ID> AD-csoport vagy a kulcstárolóban engedélyeket adó felhasználó|
|A Log Analytics telepítése|Resource Manager-sablon|LogAnalytics_DataRetention|**Zárolva** – Az adatok napok száma megmarad a Log Analytics szolgáltatásban|
|A Log Analytics telepítése|Resource Manager-sablon|LogAnalytics_Location|**Zárolva** - A munkaterület létrehozásakor használt terület|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a CAF Foundation tervminta telepítésének lépéseit, látogasson el a következő cikkbe, és ismerje meg az architektúrát:

> [!div class="nextstepaction"]
> [CAF Alapítvány tervrajza - Áttekintés](./index.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)
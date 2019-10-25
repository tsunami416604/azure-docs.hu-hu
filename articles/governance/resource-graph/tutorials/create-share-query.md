---
title: Lekérdezés létrehozása és megosztása Azure Portal
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy Resource Graph-lekérdezést, és hogyan oszthatja meg másokkal a Azure Portalban.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: resource-graph
ms.openlocfilehash: 10f93f34923fb2399a4b2053167576ba004ae467
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821682"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-azure-portal"></a>Oktatóanyag: Azure Resource Graph-lekérdezés létrehozása és megosztása Azure Portal

Az Azure Resource Graph Explorer lehetővé teszi, hogy közvetlenül a Azure Portalon mentse az erőforrás-gráf lekérdezéseit. Kétféle lekérdezés létezik, a _magán_ -és a _megosztott_. A rendszer a _privát_ lekérdezést a Azure Portal beállításokba menti, de a _megosztott_ lekérdezés olyan Resource Manager-erőforrás, amely szerepköralapú hozzáférés-vezérléssel (RBAC) felügyelhető és erőforrás-zárolásokkal védett.

A lekérdezések mentése a Azure Portal menti a kedvenc vagy gyakran használt lekérdezések keresésének idejét. A lekérdezések megosztásakor lehetővé teszi, hogy a csapat konzisztens és ismételhető legyen. Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> - _Privát_ lekérdezés létrehozása és törlése
> - _Megosztott_ lekérdezés létrehozása
> - _Megosztott_ lekérdezések felderítése
> - _Megosztott_ lekérdezés törlése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-and-delete-a-private-query"></a>Privát lekérdezés létrehozása és törlése

A _privát_ lekérdezések csak az őket létrehozó fiók számára érhetők el vagy láthatók. Ahogy azokat a rendszer a fiók Azure Portal beállításaiban menti, azok csak a Azure Portalon hozhatók létre, használhatók és törölhetők. Egy _privát_ lekérdezés nem Resource Manager-erőforrás. Hozzon létre egy új _privát_ lekérdezést a következő lépések végrehajtásával:

1. A portál menüjében válassza a minden szolgáltatás lehetőséget, vagy használja az összes oldal tetején található Azure Search mezőt.
   Keresse meg és válassza ki a "Resource Graph Explorer" kifejezést.

1. Az Azure Resource Graph Explorer "lekérdezés 1" lapján adja meg a következő lekérdezést. A lekérdezéssel kapcsolatos információkért lásd: [minták – virtuális gépek száma operációs rendszer típusa szerint](../samples/starter.md#count-virtual-machines-by-os-type).
   Válassza a **lekérdezés futtatása** lehetőséget, hogy megjelenjen a lekérdezés eredményei a th alsó ablaktáblán.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Válassza a **Save (Mentés** ) vagy **a Mentés másként**lehetőséget, írja be a _következőt_ : "a virtuális gépek száma operációs rendszer szerint", hagyja a "Private Query" _nevet_ , majd kattintson a Save ( **Mentés** ) gombra a _lekérdezés mentése_ ablaktábla alján. A lap címe "Query 1" típusról "virtuális gépek száma az operációs rendszer szerint" értékre változik.

1. Böngésszen az Azure Resource Graph Explorerben Azure Portal, majd térjen vissza. A mentett lekérdezés már nem jelenik meg, és az "1. lekérdezés" lapot adta vissza.

1. Válassza **a lekérdezés megnyitása**lehetőséget. Győződjön meg arról, hogy a _típus_ "Private Query". A mentett "virtuális gépek száma operációs rendszer szerint" üzenet most megjelenik a _lekérdezés neve_ listában. Válassza ki a mentett lekérdezés cím hivatkozását, és egy új lapra töltődik be a lekérdezés nevével.

   > [!NOTE]
   > Ha egy mentett lekérdezés meg van nyitva, és a TAB megjeleníti a _nevét_, a **Save (Mentés** ) gomb frissíti a módosításokat. Új mentett lekérdezés létrehozásához használja a **Mentés másként** lehetőséget, és kövesse a lépéseket úgy, mintha új mentett lekérdezés lenne.

1. A mentett lekérdezés törléséhez válassza ismét **a lekérdezés megnyitása** lehetőséget, és győződjön meg arról, hogy a _típus_ "Private Query". A mentett "virtuális gépek száma operációs rendszer szerint" lekérdezés sorában válassza a Kuka ikont. A megerősítő párbeszédpanelen válassza az **Igen** lehetőséget a lekérdezés törlésének befejezéséhez. Ezután zárjuk be a _lekérdezés megnyitása_ ablaktáblát.

## <a name="create-a-shared-query"></a>Megosztott lekérdezés létrehozása

Egy _privát_ lekérdezéstől eltérően a _megosztott_ lekérdezés egy Resource Manager-erőforrás. Ez azt jelenti, hogy a lekérdezés egy erőforráscsoporthoz lett mentve, felügyelhető és vezérelhető a RBAC, és az erőforrás-zárolásokkal is védve van. Erőforrásként a megfelelő engedélyekkel rendelkező bárki megtekintheti és használhatja azt. Hozzon létre egy új _megosztott_ lekérdezést a következő lépések végrehajtásával:

1. A portál menüjében válassza a minden szolgáltatás lehetőséget, vagy használja az összes oldal tetején található Azure Search mezőt.
   Keresse meg és válassza ki a "Resource Graph Explorer" kifejezést.

1. Az Azure Resource Graph Explorer "lekérdezés 1" lapján adja meg a következő lekérdezést. A lekérdezéssel kapcsolatos információkért lásd: [minták – virtuális gépek száma operációs rendszer típusa szerint](../samples/starter.md#count-virtual-machines-by-os-type).
   Válassza a **lekérdezés futtatása** lehetőséget a lekérdezés eredményeinek megjelenítéséhez az alsó ablaktáblán.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Válassza a **Mentés** vagy **a Mentés másként**lehetőséget.

   ![Az új lekérdezés mentése a Save (Mentés) gomb használatával](../media/create-share-query/save-shared-query-buttons.png)

1. A _lekérdezés mentése_ panelen írja be a "virtuális gépek száma operációs rendszer szerint" _nevet_ , _írja be a típust_ a "Shared Query" értékre, állítsa a _leírást_ a "virtuális gépek száma operációs rendszer típusa szerint" értékre, és válassza ki azt az _előfizetést_ , amelyben a lekérdezési erőforrást létrehozták. Hagyja bejelölve a "közzététel az erőforráshoz – gráf-lekérdezések erőforráscsoporthoz" jelölőnégyzetet, és az _erőforráscsoport helye_ : "(US) West Central us". Ezután kattintson a **Save (Mentés** ) gombra a _lekérdezés mentése_ ablaktábla alján. A lap címe "Query 1" típusról "virtuális gépek száma az operációs rendszer szerint" értékre változik. Az "erőforrás-gráf-lekérdezések" erőforráscsoport első alkalommal való használatakor a Mentés az erőforráscsoport létrehozásakor tovább tart.

   ![Az új lekérdezés mentése megosztott lekérdezésként](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE]
   > Ha kívánja, távolítsa el az ellenőrzési csoportot, és adja meg egy meglévő erőforráscsoport nevét, amelybe a megosztott lekérdezést menteni szeretné. A lekérdezések alapértelmezett megnevezett erőforráscsoport használatával könnyebben derítheti fel a _megosztott_ lekérdezéseket. Emellett nyilvánvalóvá teszi az erőforráscsoport célját. Egy meglévő erőforráscsoport kiválasztása azonban a meglévő engedélyek alapján biztonsági okokból is elvégezhető.

1. Böngésszen az Azure Resource Graph Explorerben Azure Portal, majd térjen vissza. A mentett lekérdezés már nem jelenik meg, és az "1. lekérdezés" lapot adta vissza.

1. Válassza **a lekérdezés megnyitása**lehetőséget. Győződjön meg arról, hogy a _típus_ "Shared Query", és az _előfizetés_ és az _erőforráscsoport_ kombinációja megegyezik a lekérdezés mentésekor megadott értékkel. A mentett "virtuális gépek száma operációs rendszer szerint" üzenet most megjelenik a _lekérdezés neve_ listában. Válassza ki a mentett lekérdezés cím hivatkozását, és egy új lapra töltődik be a lekérdezés nevével. _Megosztott_ lekérdezésként megjelenít egy ikont a cím melletti lapon, amely azt jelzi, hogy megosztott.

   ![A megosztott lekérdezés ikonjának megjelenítése](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE]
   > Ha egy mentett lekérdezés meg van nyitva, és a TAB megjeleníti a _nevét_, a **Save (Mentés** ) gomb frissíti a módosításokat. Új mentett lekérdezés létrehozásához használja a **Mentés másként** lehetőséget, és kövesse a lépéseket úgy, mintha új mentett lekérdezés lenne.

## <a name="discover-shared-queries"></a>Megosztott lekérdezések felderítése

Mivel a _megosztott_ lekérdezés egy Resource Manager-erőforrás, többféleképpen is megtalálhatja őket:

- A Resource Graph Explorerben válassza **a lekérdezés megnyitása** és a _típus_ beállítása a "Shared Query" lehetőséget.
- Az erőforrás-gráf lekérdezési portál lapja
- Az az erőforráscsoport, amelybe mentve lett
- Lekérdezéssel az erőforrás-gráfhoz

### <a name="view-resource-graph-queries"></a>Resource Graph-lekérdezések megtekintése

Azure Portal az Erőforrásgrafikon-lekérdezések lap azokat a _megosztott_ lekérdezéseket jeleníti meg, amelyekhez a bejelentkezett fiók hozzáfér. Ezen a lapon engedélyezheti a szűrést név, előfizetés, erőforráscsoport és az Erőforrásgrafikon-lekérdezés egyéb tulajdonságai alapján. Az erőforrás-gráf lekérdezései az interfész használatával is megcímkézhető, exportálhatók és törölhetők.

A lekérdezések egyikének kiválasztásával megnyílik az erőforrás-gráf lekérdezési lapja. A többi Resource Manager-erőforráshoz hasonlóan ez az oldal interaktív áttekintést nyújt a műveletnapló, a hozzáférés-vezérlés és a címkék mellett. Az erőforrás-zárolást közvetlenül ezen a lapon is lehet alkalmazni.

A portál menüjéből válassza a minden szolgáltatás lehetőséget, vagy az összes oldal tetején található Azure Search (összes szolgáltatás) elemet. Keresse meg és válassza ki a "Resource Graph Explorer" kifejezést.

### <a name="list-resource-groups-resources"></a>Erőforráscsoportok erőforrásainak listázása

Az erőforrás-gráf lekérdezés az erőforráscsoport részét képező egyéb erőforrások mellett szerepel.
Ha kijelöli az Erőforrásgrafikon-lekérdezést, megnyílik a lekérdezés lapja. A három pontra vagy a jobb gombbal kattintva elérhető beállítások ugyanúgy működnek, mint az erőforrás-gráf lekérdezési lapja.

### <a name="query-resource-graph"></a>Erőforrás-diagram lekérdezése

Resource Manager-erőforrásként az erőforrás-gráf lekérdezései az erőforrás-gráf lekérdezésével is megtalálhatók.
A következő erőforrás-gráf lekérdezési határértékek `Microsoft.ResourceGraph/queries`szerint vannak korlátozva, majd a `project` használatával csak a nevet, a módosított időpontot és a lekérdezéseket listázza:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Megosztott lekérdezés törlése

Ha már nincs szükség _megosztott_ lekérdezésre, törölje azt. _Megosztott_ lekérdezés törlése eltávolítja a tényleges Resource Manager-erőforrást. Minden olyan irányítópult, amelybe az eredmények diagramja be lett rögzítve, hibaüzenet jelenik meg. Ha ez a hibaüzenet jelenik meg, a **Eltávolítás az irányítópultról** gombbal törölje az irányítópultot.

A következő felületekből törölhető egy _megosztott_ lekérdezés:
- Resource Graph-lekérdezések lap
- Erőforrás-diagram lekérdezési lapja
- Resource Graph Explorer – lekérdezési oldal megnyitása
- Erőforráscsoportok lap

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az Oktatóanyaggal, törölje a létrehozott _privát_ és _megosztott_ lekérdezéseket, ha már nem szeretné őket.

## <a name="next-steps"></a>Következő lépések

- Az első lekérdezés futtatása a [Azure Portal](../first-query-portal.md)
- További információ a [lekérdezés nyelvéről](../concepts/query-language.md)
- Információ az [erőforrások felfedezéséről](../concepts/explore-resources.md)
- Az [Alapszintű lekérdezések](../samples/starter.md) példáinak megtekintése
- Tekintse meg a [Speciális lekérdezések](../samples/advanced.md) példáit
- Visszajelzés küldése a [UserVoice-ról](https://feedback.azure.com/forums/915958-azure-governance)
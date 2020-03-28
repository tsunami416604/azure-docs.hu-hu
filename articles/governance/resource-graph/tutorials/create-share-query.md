---
title: 'Oktatóanyag: Lekérdezések kezelése az Azure Portalon'
description: Ebben az oktatóanyagban hozzon létre egy Resource Graph-lekérdezést, és ossza meg az új lekérdezést másokkal az Azure Portalon.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: 00cb3f95112804c81beb6bce6fc35891e6197e60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74303950"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Oktatóanyag: Azure Resource Graph-lekérdezés létrehozása és megosztása az Azure Portalon

Az Azure Resource Graph Explorer lehetővé teszi, hogy mentse a Resource Graph-lekérdezések közvetlenül az Azure Portalon. Kétféle lekérdezés létezik: _Privát_ és _Megosztott_. A privát lekérdezés tava van az Azure Portal beállításait. Mivel a megosztott lekérdezés olyan Erőforrás-kezelő erőforrás, amely szerepköralapú hozzáférés-vezérléssel (RBAC) kezelhető, és erőforrászárolással védett. Mindkét típusú lekérdezések titkosítva vannak inkamintikálva.

Ha lekérdezéseket ment az Azure Portalon, időt takaríthat meg, amelyet egyébként a kedvenc vagy általánosan használt lekérdezések keresésére fordíthat. Amikor megosztja a lekérdezéseket, az ismétlések révén segít a csapatnak a konzisztencia és a hatékonyság céljainak megvalósításában.

Ebben az oktatóanyagban a következő feladatokat hajthatja végre:

> [!div class="checklist"]
> - Privát lekérdezés létrehozása és törlése
> - Megosztott lekérdezés létrehozása
> - Megosztott lekérdezések felderítése
> - Megosztott lekérdezés törlése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-and-delete-a-private-query"></a>Privát lekérdezés létrehozása és törlése

A privát lekérdezések csak az azokat létrehozó fiók számára érhetők el és láthatók. A fiók Azure Portal beállításaiba való mentésük során csak az Azure Portalon belül hozhatók létre, használhatók és törölhetők. A privát lekérdezés nem Erőforrás-kezelő erőforrás.A Private query is nem resource manager resource. Új személyes lekérdezés létrehozásához kövesse az alábbi lépéseket:

1. A portál menüben válassza a **Minden szolgáltatás** lehetőséget, vagy használja az Azure keresőmezőjét az összes lap tetején. Keresse meg a keresést, és válassza az **Erőforrásgráf-kezelő lehetőséget.**

1. Az Azure Resource Graph Explorer lap **Lekérdezés 1** lapján adja meg a következő lekérdezést:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Válassza a **Lekérdezés futtatása** lehetőséget, ha az alsó ablaktáblán szeretné látni a lekérdezés eredményeit.

   A lekérdezésről további információt a [Minták – Virtuális gépek megszámlálása operációsrendszer-típus szerint című témakörben](../samples/starter.md#count-virtual-machines-by-os-type)talál.


1. Válassza a **Mentés** vagy **a Mentés másként**lehetőséget, írja be **a Gépkezelő által gépszám-számlálónevet** névként, hagyja a típust **privát lekérdezésként,** majd válassza a **Mentés** gombot a **Mentés lekérdezés** ablaktábla alján. A lap címe **lekérdezés 1-ről** **gépi rendszer által számított virtuális gépekre**változik.

1. Lépjen el az Azure Resource Graph Explorertől az Azure Portalon, majd térjen vissza hozzá. Figyelje meg, hogy a mentett lekérdezés már nem jelenik meg, és a **Lekérdezés 1** lap visszaadta.

1. Válassza **a Lekérdezés megnyitása**lehetőséget. Győződjön meg arról, hogy a típus **privát lekérdezés.** Az **operációs rendszer által mentett virtuális gépek száma** most már megjelenik a lekérdezés **neve** listában. Amikor kiválasztja a mentett lekérdezés címhivatkozását, az egy új lapba töltődik be a lekérdezés nevével.

   > [!NOTE] 
   > Ha egy mentett lekérdezés meg van nyitva, és a lapon megjelenik a neve, a **Mentés** gomb kiválasztásával frissíti a végrehajtott módosításokat. Ha új mentett lekérdezést szeretne létrehozni ebből a megnyitott lekérdezésből, válassza a **Mentés másként** lehetőséget, és folytassa úgy, mintha egy teljesen új lekérdezést mentene.

1. A mentett lekérdezés törléséhez jelölje be ismét a **Lekérdezés megnyitása jelölőnégyzetet,** és ellenőrizze, hogy a **Típus** mező beállítása **Privát lekérdezés.** A mentett `Count VMs by OS` lekérdezés sorában válassza a **Törlés** (Lomtár ikon) lehetőséget. A megerősítést kérő párbeszédpanelen válassza az **Igen** lehetőséget a lekérdezés törlésének befejezéséhez.
   Ezután zárja be a **Lekérdezés megnyitása** ablaktáblát.

## <a name="create-a-shared-query"></a>Megosztott lekérdezés létrehozása

A privát lekérdezésektől eltérően a megosztott lekérdezés erőforrás-kezelői erőforrás. Ez a tény azt jelenti, hogy a lekérdezés egy erőforráscsoportba kerül, az RBAC-kal kezelhető és vezérelhető, és akár erőforrászárolásokkal is védhető. Erőforrásként bárki láthatja és használhatja, aki rendelkezik a megfelelő engedélyekkel.
Új megosztott lekérdezés létrehozásához kövesse az alábbi lépéseket:

1. A portál menüben válassza a **Minden szolgáltatás**lehetőséget, vagy használja az Összes lap tetején található Azure keresőmezőt az **Erőforrásgráf-kezelő**kereséséhez és kiválasztásához.

1. Az Azure Resource Graph Explorer lap **Lekérdezés 1** lapján adja meg a következő lekérdezést:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Válassza a **Lekérdezés futtatása** lehetőséget, ha az alsó ablaktáblán szeretné látni a lekérdezés eredményeit.

   A lekérdezésről további információt a [Minták – Virtuális gépek megszámlálása operációsrendszer-típus szerint című témakörben](../samples/starter.md#count-virtual-machines-by-os-type)talál.

1. Válassza a **Mentés** vagy **a Mentés másként**lehetőséget.

   
   ![Az új lekérdezés mentése a Mentés gombbal](../media/create-share-query/save-shared-query-buttons.png)

1. A **Lekérdezés mentése** ablaktáblán adja meg a **virtuális gépek száma operációs rendszerenként** a nevet.

1. Módosítsa a típust **Megosztott lekérdezésre**, állítsa a leírást **a virtuális gépek száma operációsrendszer-típus szerint**, és állítsa be az **Előfizetés** t, hogy megadja, hol jön létre a lekérdezési erőforrás.

1. Hagyja bejelölve a **Közzététel erőforrásgráfra lekérdezések erőforráscsoportra** jelölőnégyzetet, és az **Erőforráscsoport helye** **(US) Usa nyugati középső régiója**értékre van állítva.

1. A **Mentés lekérdezés** ablaktábla alján válassza a **Mentés** lehetőséget. A lap címe **lekérdezés 1-ről** **gépi rendszer által számított virtuális gépekre**változik. Az **erőforrásgráf-lekérdezések** erőforráscsoport első használatakor a mentés a vártnál tovább tart, amikor az erőforráscsoport létrejön.
   
   ![Az új lekérdezés mentése megosztott lekérdezésként](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > Törölheti a jelet a **Közzététel erőforrásgráfon lekérdezések erőforráscsoport** jelölőnégyzetből, ha meg szeretné adni egy meglévő erőforráscsoport nevét a megosztott lekérdezés mentéséhez. Az alapértelmezett nevű erőforráscsoport használata a lekérdezésekhez megkönnyíti a megosztott lekérdezések felderítését. Emellett az erőforráscsoport célját is nyilvánvalóbbá teszi. Biztonsági okokból azonban dönthet úgy, hogy egy meglévő erőforráscsoportot választ a meglévő engedélyek alapján.

1. Lépjen el az Azure Resource Graph Explorertől az Azure Portalon, majd térjen vissza hozzá. Figyelje meg, hogy a mentett lekérdezés már nem jelenik meg, és a **Lekérdezés 1** lap visszaadta.

1. Válassza **a Lekérdezés megnyitása**lehetőséget. Ellenőrizze, hogy a típus **megosztott lekérdezésre van-e** állítva, és az **Előfizetés** és az **Erőforrás csoport** kombinációja megegyezik a lekérdezés mentési helyével. A mentett **darabszám virtuális gépek operációs rendszer** szerint elem most megjelenik a lekérdezés **neve** listában. A mentett lekérdezés címhivatkozásának kiválasztásával töltse be egy új lapra, amelynek neve a lekérdezés neve. Megosztott lekérdezésként egy ikont jelenít meg a cím melletti lapon, amely megosztottként jelöli.

   ![A megosztott lekérdezés megjelenítése ikonnal](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > Ha egy mentett lekérdezés meg van nyitva, és a lapon megjelenik a neve, a **Mentés** gomb frissíti az elvégzett módosításokat. Új mentett lekérdezés létrehozásához válassza a **Mentés másként** lehetőséget, és folytassa úgy, mintha egy teljesen új lekérdezést mentene.

## <a name="discover-shared-queries"></a>Megosztott lekérdezések felderítése

Mivel a megosztott lekérdezés erőforrás-kezelői erőforrás, többféleképpen is megkereshetegyet:

- Az Erőforrásgráf-kezelőben válassza **a Lekérdezés megnyitása** lehetőséget, és állítsa a típust Megosztott **lekérdezésre**.
- Az Erőforrásgráf lekérdezések portállapon.
- Abból az erőforráscsoportból, amelyben a megosztott lekérdezést mentette.
- Az Erőforrás-grafikon lekérdezésén keresztül.

### <a name="view-resource-graph-queries"></a>Erőforrásgráf-lekérdezések megtekintése

Az Azure Portalon az Erőforrás-gráf lekérdezések lap megjeleníti a megosztott lekérdezések, amelyek a bejelentkezett fiók hozzáféréssel rendelkezik. Ez a lap lehetővé teszi a szűrést név, előfizetés, erőforráscsoport és az Erőforrásgráf lekérdezés egyéb tulajdonságai szerint. Az Erőforrásgráf-lekérdezéseket ezen a felületen is megcímkézheti, exportálhatja és törölheti.

Az egyik lekérdezés kiválasztása megnyitja az Erőforrásgráf lekérdezési lapot. Más Erőforrás-kezelő idokhoz hasonlóan ez a lap is interaktív áttekintést nyújt a tevékenységnaplóval, a hozzáférés-vezérléssel és a címkékkel együtt. Az erőforrás-zárolást közvetlenül erről az oldalról is alkalmazhatja.

A portál menüjében a Forrásgráf lekérdezések lapját a **Minden szolgáltatás** kiválasztásával vagy az összes lap tetején található Azure keresőmező használatával szerezheti meg. Keresse meg és válassza az **Erőforrásgráf-kezelőt.**

### <a name="list-resource-groups-resources"></a>Erőforráscsoportok erőforrások listázása

Az Erőforrásgráf lekérdezés az erőforráscsoport részét játszó egyéb erőforrások mellett jelenik meg.
Az Erőforrásgráf lekérdezés kijelölésével megnyílik a lekérdezés lapja. A három pont és a helyi menü beállításai (amelyeket a jobb gombbal történő kattintás indít el) ugyanúgy működnek, mint az Erőforrásgráf lekérdezési lapon.

### <a name="query-resource-graph"></a>Lekérdezési erőforrás grafikonja

Az Erőforrásgráf lekérdezéseit az Erőforrásgráf lekérdezésein keresztül találhatja meg. A következő Resource Graph `Microsoft.ResourceGraph/queries`lekérdezés típus `project` szerint korlátoz, majd csak a név, a módosított idő és maga a lekérdezés felsorolásával jelenik meg:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Megosztott lekérdezés törlése

Ha a megosztott lekérdezésre már nincs szükség, törölje azt. Megosztott lekérdezés törlésével eltávolítja a megfelelő Erőforrás-kezelő erőforrást. Azok az irányítópultok, amelyekhez az eredménydiagramot rögzítették, hibaüzenetet jelenítenek meg. Amikor megjelenik ez a hibaüzenet, az Eltávolítás az **irányítópultról** gombbal megtisztíthatja az irányítópultot.

Megosztott lekérdezést a következő felületeken keresztül törölheti:
- Az Erőforrásgráf lekérdezések lapja
- Az Erőforrásgráf lekérdezési lapja
- Lekérdezés **megnyitása** lap az Erőforrásgráf kezelőben
- Erőforráscsoportok lap

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az oktatóanyagot, törölje a létrehozott privát és megosztott lekérdezéseket, ha már nincs rájuk szüksége.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban privát és megosztott lekérdezéseket hozott létre. Ha többet szeretne megtudni az Erőforrás-diagram nyelvéről, folytassa a lekérdezésnyelv részletei lappal.

> [!div class="nextstepaction"]
> [További információ a lekérdezés nyelvéről](../concepts/query-language.md)
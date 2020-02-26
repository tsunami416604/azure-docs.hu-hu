---
title: A szolgáltatási erőforrások áthelyezése régiók között
titleSuffix: Azure Cognitive Search
description: Ez a cikk bemutatja, hogyan helyezheti át Azure Cognitive Search-erőforrásait az egyik régióból a másikba az Azure-felhőben.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599300"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Azure Cognitive Search-szolgáltatás áthelyezése másik Azure-régióba

Az Azure kognitív szolgáltatási fiók egyik régióból a másikba való áthelyezéséhez létre kell hoznia egy exportálási sablont az előfizetés (ok) áthelyezéséhez. Az előfizetés áthelyezése után át kell helyeznie az adatait, és újra létre kell hoznia a szolgáltatást.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Sablon exportálása.
> * Módosítsa a sablont: adja hozzá a cél régiót, a keresési és a tárolási fiókok nevét.
> * Telepítse a sablont az új keresési és Storage-fiókok létrehozásához.
> * A szolgáltatás állapotának ellenőrzése az új régióban
> * Erőforrások törlése a forrás régióban.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a fiók által használt szolgáltatások és szolgáltatások támogatottak a célként megadott régióban.

- Az előzetes verziójú funkciók esetében győződjön meg arról, hogy az előfizetése engedélyezett a célként megadott régióban. További információ az előzetes verziójú funkciókról: a [Knowledge Stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), a [növekményes](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)bővítés és a [magánhálózati végpont](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## <a name="assessment-and-planning"></a>Értékelés és tervezés

Ha áthelyezi a keresési szolgáltatást az új régióba, [át kell helyeznie az adatait az új Storage szolgáltatásba](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) , majd újra létre kell hoznia az indexeket, a szakértelmével és a tudásbázist. Az aktuális beállításokat jegyezze fel, és másolja a JSON-fájlokat, hogy a szolgáltatás újraépítése egyszerűbb és gyorsabb legyen.

## <a name="moving-your-search-services-resources"></a>A keresési szolgáltatás erőforrásainak áthelyezése

A kezdéshez exportálni kell, majd módosítania kell egy Resource Manager-sablont.

### <a name="export-a-template"></a>Sablon exportálása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

2. Nyissa meg az erőforráscsoport lapját.

> [!div class="mx-imgBorder"]
> ![erőforráscsoport-oldal például](./media/search-move-resource/export-template-sample.png)

3. Válassza az **Összes erőforrás** elemet.

3. A bal oldali navigációs menüben válassza a **sablon exportálása**lehetőséget.

4. A **sablon exportálása** lapon kattintson a **Letöltés** elemre.

5. Keresse meg a portálról letöltött. zip fájlt, és bontsa ki a fájlt egy tetszőleges mappába.

A zip-fájl tartalmazza a sablont és a parancsfájlokat tartalmazó. JSON fájlokat a sablon telepítéséhez.

### <a name="modify-the-template"></a>A sablon módosítása

A sablont a Search és a Storage-fiók nevének és régiói módosításával fogja módosítani. A névnek követnie kell az egyes szolgáltatások és régiók elnevezési konvenciók szabályait. 

A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, az **USA középső** = **CentralUS**.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget.

2. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza a **Template deployment** lehetőséget.

4. Kattintson a **Létrehozás** gombra.

5. Válassza a **Saját sablon készítése a szerkesztőben** lehetőséget.

6. Válassza a **fájl betöltése**lehetőséget, majd kövesse az utasításokat a letöltött és kibontott **template. JSON** fájl betöltéséhez az előző szakaszban.

7. A **sablon. JSON** fájlban adja meg a cél keresési és a tárolási fiókok nevet a keresési és a tárolási fiókok nevének alapértelmezett értékének megadásával. 

8. Szerkessze a **Location (hely** ) tulajdonságot a **template. JSON** fájlban a keresési és a tárolási szolgáltatások céljának megfelelően. Ez a példa a célként megadott régiót állítja be `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Mentse a **template. JSON** fájlt.

2. Adja meg vagy válassza ki a tulajdonságértékek értékét:

- **Előfizetés**: válasszon ki egy Azure-előfizetést.

- **Erőforráscsoport**: Válassza az **Új létrehozása** lehetőséget, majd adjon nevet az erőforráscsoportnak.

- **Hely**: válasszon ki egy Azure-helyet.

3. Jelölje **be az Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet, majd kattintson a **Vásárlás kiválasztása** gombra.

## <a name="verifying-your-services-status-in-new-region"></a>A szolgáltatások állapotának ellenőrzése az új régióban

Az áthelyezés ellenőrzéséhez nyissa meg az új erőforráscsoportot, és a szolgáltatások megjelennek az új régióban.

Ha át szeretné helyezni az adatait a forrás régiójából a célként megadott régióba, tekintse meg ezt a cikk útmutatásait az [adatok az új Storage-fiókba való áthelyezéséhez](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## <a name="clean-up-resources-in-your-original-region"></a>Erőforrások törlése az eredeti régióban

Ha véglegesíteni szeretné a módosításokat, és befejezi a szolgáltatásfiók áthelyezését, törölje a forrás-szolgáltatási fiókot.

## <a name="next-steps"></a>Következő lépések

[Index létrehozása](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Készségkészlet létrehozása](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Knowledge Store létrehozása](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)


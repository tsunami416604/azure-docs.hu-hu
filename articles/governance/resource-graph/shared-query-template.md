---
title: 'Gyors útmutató: megosztott lekérdezés létrehozása sablonokkal'
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablon (ARM-sablon) használatával hoz létre egy olyan erőforrás-gráf megosztott lekérdezést, amely az operációs rendszer által használt virtuális gépeket számolja.
ms.date: 07/06/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: a05be04064df81373f856ea0e8ca59664078695f
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252368"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Gyors útmutató: megosztott lekérdezés létrehozása ARM-sablon használatával

Az erőforrás-gráf lekérdezéseit _privát lekérdezésként_ vagy _megosztott lekérdezésként_lehet menteni. A privát lekérdezéseket a rendszer az egyéni portál profiljába menti, és mások számára nem látható. A megosztott lekérdezés egy olyan Resource Manager-objektum, amely engedélyek és szerepköralapú hozzáférés használatával megosztható másokkal. A megosztott lekérdezés az erőforrás-felderítés általános és konzisztens végrehajtását teszi lehetővé. Ez a rövid útmutató egy Azure Resource Manager sablont (ARM-sablont) használ egy megosztott lekérdezés létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonok használatát, válassza az **üzembe helyezés az Azure** -ban gombot. A sablon megnyílik a Azure Portalban.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Az ARM-sablon üzembe helyezése megosztott lekérdezés létrehozásához az Azure-ban" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Ebben a rövid útmutatóban létrehoz egy "Count VM" nevű megosztott lekérdezést az _operációs rendszer szerint_. A lekérdezés SDK-ban vagy a Portalon a Resource Graph Explorerben való kipróbálásához lásd: [minták – virtuális gépek száma operációs rendszer típusa szerint](./samples/starter.md#count-os).

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/)származik.

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

A sablonban definiált erőforrás:

- [Microsoft. ResourceGraph/lekérdezések](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

> [!NOTE]
> Az Azure Resource Graph szolgáltatás ingyenes. További információ: [Az Azure Resource Graph áttekintése](./overview.md).

1. Az alábbi képre kattintva jelentkezzen be az Azure Portalra, és nyissa meg a sablont:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Az ARM-sablon üzembe helyezése megosztott lekérdezés létrehozásához az Azure-ban" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Válassza ki vagy adja meg a következő értékeket:

   | Név | Érték |
   |------|-------|
   | Előfizetés | Válassza ki az Azure-előfizetését. |
   | Erőforráscsoport | Válassza az **új létrehozása**lehetőséget, adjon meg egy nevet, majd kattintson **az OK gombra**. |
   | Hely | Válasszon régiót. Például: **USA középső régiója**. |
   | Lekérdezés neve | Hagyja meg az alapértelmezett értéket a **virtuális gépek operációs rendszer szerinti számlálásával**. |
   | Lekérdezési kód | Hagyja meg az alapértelmezett értéket`Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Lekérdezés leírása | Hagyja meg az alapértelmezett értéket **, ez a megosztott lekérdezés az összes virtuálisgép-erőforrást megszámolja, és az operációs rendszer típusa szerint összegzi.** |
   | Elfogadom a fenti feltételeket és kikötéseket | Válassza |

1. Válassza a **Vásárlás** lehetőséget.

Néhány további erőforrás:

- További minták sablonjait az Azure rövid útmutató [sablonjában](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)talál.
- A sablon hivatkozásának megtekintéséhez nyissa meg az [Azure-sablonok referenciáját](/azure/templates/microsoft.resourcegraph/allversions).
- Az ARM-sablonok fejlesztéséről a [Azure Resource Manager dokumentációjában](../../azure-resource-manager/management/overview.md)talál további információt.
- Az előfizetési szintű központi telepítés megismeréséhez tekintse meg [Az erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](../../azure-resource-manager/templates/deploy-to-subscription.md)című témakört.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az új megosztott lekérdezés futtatásához kövesse az alábbi lépéseket:

1. A portálon keresse meg az **Erőforrásgrafikon lekérdezéseket** , és jelölje ki.

1. Válassza ki a **Count virtuális gépek**nevű megosztott lekérdezést operációs rendszer szerint, majd válassza ki az **eredmények** lapot az **Áttekintés** oldalon.

Azt is megteheti, hogy a megosztott lekérdezést megnyithatja a Resource Graph Explorerben:

1. A portálon keresse meg a **Resource Graph Explorert** , és jelölje ki.

1. Kattintson a **lekérdezés megnyitása** gombra.

1. Módosítsa a **típust** _megosztott lekérdezésekre_. Ha a listában nem jelenik meg a **virtuális gépek száma operációs rendszer szerint** , használja a szűrő mezőt az eredmények korlátozásához. Ha a **virtuális gépek száma az operációs rendszer** megosztott lekérdezésével látható, válassza ki a nevét.

1. A lekérdezés betöltését követően kattintson a **lekérdezés futtatása** gombra. Az eredmények az alábbi **eredmények** lapon jelennek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott megosztott lekérdezés eltávolításához kövesse az alábbi lépéseket:

1. A portálon keresse meg az **Erőforrásgrafikon lekérdezéseket** , és jelölje ki.

1. Állítsa be a Count VM nevű megosztott lekérdezés melletti jelölőnégyzetet az **operációs rendszer szerint**.

1. Kattintson a **Törlés** gombra az oldal tetején.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Resource Graph megosztott lekérdezést.

Ha többet szeretne megtudni a megosztott lekérdezésekről, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Lekérdezések kezelése a Azure Portalban](./tutorials/create-share-query.md)
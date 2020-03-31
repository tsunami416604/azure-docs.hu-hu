---
title: Jegyzetfüzetek engedélyezése az Azure Cosmos DB-fiókban (előzetes verzió)
description: Az Azure Cosmos DB beépített jegyzetfüzetei lehetővé teszik az adatok elemzését és megjelenítését a portálon belül. Ez a cikk ismerteti, hogyan engedélyezheti ezt a szolgáltatást a Cosmos-fiókok.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768022"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Jegyzetfüzetek engedélyezése Az Azure Cosmos DB-fiókokhoz (előzetes verzió)

> [!IMPORTANT]
> Az Azure Cosmos DB beépített jegyzetfüzetei jelenleg a következő Azure-régiókban érhetők el: Kelet-Ausztrália, USA keleti régiója, USA keleti régiója 2, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, Az Egyesült Királyság dél-, Nyugat-Európa és USA nyugati régiói 2. Jegyzetfüzetek használatához [hozzon létre egy új fiókot jegyzetfüzetekkel,](#enable-notebooks-in-a-new-cosmos-account) vagy [engedélyezze a jegyzetfüzeteket egy meglévő fiókban](#enable-notebooks-in-an-existing-cosmos-account) ezen régiók egyikében.

Az Azure Cosmos DB beépített Jupyter-jegyzetfüzetei lehetővé teszik az Azure Portaladatainak elemzését és megjelenítését. Ez a cikk leírja, hogyan lehet engedélyezni ezt a funkciót az Azure Cosmos DB-fiókhoz.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Jegyzetfüzetek engedélyezése új Cosmos-fiókban
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza **az Erőforrás-adatbázisok** > **létrehozása** > az**Azure Cosmos DB**lehetőséget.
1. Az **Azure Cosmos DB-fiók létrehozása** lapon válassza **a Jegyzetfüzetek**lehetőséget. 
 
    ![Jegyzetfüzetek kiválasztása az Azure Cosmos DB Create blade-ben](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Válassza az **Áttekintés + létrehozás** lehetőséget. Kihagyhatja a **Hálózat** és **címkék** lehetőséget. 
1. Tekintse át a fiókbeállításokat, és válassza a **Létrehozás gombot.** A fiók létrehozása néhány percet vesz igénybe. Várja meg, amíg a portállap megjelenik **A telepítés befejeződött.** 

    ![Az Azure Portal Értesítések panelje](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Válassza **az Ugrás az erőforráshoz** lehetőséget az Azure Cosmos DB-fiók lapra való ugráshoz. 

    ![Az Azure Cosmos DB-fiók lapja](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Nyissa meg az **Adatkezelő** ablaktábláját. Most látnia kell a jegyzetfüzetek munkaterületét.

    ![Új Azure Cosmos DB-jegyzetfüzetek munkaterülete](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Jegyzetfüzetek engedélyezése meglévő Cosmos-fiókban
A jegyzetfüzeteket meglévő fiókokon is engedélyezheti. Ezt a lépést fiókonként csak egyszer kell elvégezni.

1. Nyissa meg a Cosmos-fiók **Adatkezelő** ablaktábláját.
1. Válassza **a Jegyzetfüzetek engedélyezése**lehetőséget.

    ![Új jegyzetfüzet-munkaterület létrehozása az Adatkezelőben](media/enable-notebooks/enable-notebooks-workspace.png)
1. Ez új jegyzetfüzet-munkaterület létrehozását kéri. Válassza **a Teljes beállítás lehetőséget.**
1. Fiókja most már engedélyezve van a notebookok használatához!

## <a name="create-and-run-your-first-notebook"></a>Az első jegyzetfüzet létrehozása és futtatása

Annak ellenőrzéséhez, hogy használhat-e jegyzetfüzeteket, jelöljön ki egyet a Mintajegyzetfüzetek csoportban. Ezzel menti a jegyzetfüzet egy példányát a munkaterületre, és megnyitja azt.

Ebben a példában a **GettingStarted.ipynb programot használjuk.** 

![A GettingStarted.ipynb jegyzetfüzet megtekintése](media/enable-notebooks/select-getting-started-notebook.png)

A jegyzetfüzet futtatása:
1. Válassza ki a Python-kódot tartalmazó első kódcellát. 
1. A cella futtatásához válassza a **Futtatás** lehetőséget. A cella futtatásához **a Shift + Enter billentyűkombinációt** is használhatja.
1. Frissítse az erőforrás-ablaktáblát a létrehozott adatbázis és tároló megtekintéséhez.

    ![Első lépések jegyzetfüzete](media/enable-notebooks/run-first-notebook-cell.png)

Az Új **jegyzetfüzet** lehetőséget új jegyzetfüzet létrehozásához vagy meglévő jegyzetfüzetfájl (.ipynb) feltöltéséhez a Jegyzetfüzetek menü **Fájl feltöltése parancsára** kattintva is **kiválaszthatja.** 

![Új jegyzetfüzet létrehozása vagy feltöltése](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg az [Azure Cosmos DB Jupyter-jegyzetfüzetek](cosmosdb-jupyter-notebooks.md) előnyeit

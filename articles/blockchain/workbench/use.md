---
title: Alkalmazások használata az Azure Blockchain Workbench ben
description: Oktatóanyag az alkalmazásszerződések azure Blockchain Workbench előzetes verziójában való használatáról.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74324042"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Oktatóanyag: Alkalmazások használata az Azure Blockchain Workbench-ben

A Blockchain Workbench segítségével szerződéseket hozhat létre és hozhat létre műveleteket. Megtekintheti a szerződés részleteit is, például az állapotot és a tranzakcióelőzményeket.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Új szerződés létrehozása
> * Művelet művelet e szerződéssel

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A Blockchain Workbench üzembe helyezése. További információ: [Azure Blockchain Workbench deployment](deploy.md) a telepítéssel kapcsolatos részletekért
* Telepített blockchain alkalmazás a Blockchain Workbench-ben. Lásd: [Blockchain-alkalmazás létrehozása az Azure Blockchain Workbench alkalmazásban](create-app.md)

[Nyissa meg a Blockchain Workbench](deploy.md#blockchain-workbench-web-url) böngészőjét.

![Blockchain Workbench](./media/use/workbench.png)

Be kell jelentkeznie a Blockchain Workbench tagjaként. Ha nincsenek felsorolt alkalmazások, akkor a Blockchain Workbench tagja, de nem tagja egyetlen alkalmazásnak sem. A Blockchain Workbench rendszergazda tagokat rendelhet az alkalmazásokhoz.

## <a name="create-new-contract"></a>Új szerződés létrehozása

Új szerződés létrehozásához **szerződéskezdeményezőként**megadott tagnak kell lennie. A szerződés alkalmazásszerepköreit és kezdeményezőit meghatározó [témakörökben a konfigurációs áttekintés munkafolyamatai találhatók.](configuration.md#workflows) A tagok alkalmazásszerepkörökhöz való hozzárendelésével kapcsolatos további tudnivalókért olvassa [el a Tag hozzáadása az alkalmazáshoz](manage-users.md#add-member-to-application)című témakört.

1. A Blockchain Workbench alkalmazás szakaszban válassza ki a létrehozni kívánt szerződést tartalmazó alkalmazáscsempét. Megjelenik az aktív szerződések listája.

2. Új szerződés létrehozásához válassza az **Új szerződés**lehetőséget.

    ![Új egyezmény gomb](./media/use/contract-list.png)

3. Megjelenik **az Új szerződés** ablaktábla. Adja meg a kezdeti paraméterek értékeit. Kattintson a **Létrehozás** gombra.

    ![Új szerződés ablaktábla](./media/use/new-contract.png)

    Az újonnan létrehozott szerződés megjelenik a többi aktív szerződéssel együtt a listában.

    ![Aktív szerződések listája](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Teendők a szerződéssel

Attól függően, hogy a szerződés milyen állapotban van, a tagok műveleteket hozhatnak a szerződés következő állapotára való áttéréshez. A műveletek az [állapoton](configuration.md#states)belüli [átmenetek.](configuration.md#transitions) Az átmenet engedélyezett alkalmazás- vagy példányszerepköréhez tartozó tagok végrehajthatják a műveletet. 

1. A Blockchain Workbench alkalmazás szakaszban válassza ki a szerződést tartalmazó alkalmazáscsempét a művelet hez.
2. Jelölje ki a szerződést a listában. A szerződés részletei különböző szakaszokban jelennek meg. 

    ![Szerződés részletei](./media/use/contract-details.png)

    | Section  | Leírás  |
    |---------|---------|
    | status | A szerződési szakaszokon belüli aktuális előrehaladás felsorasztása |
    | Részletek | A szerződés aktuális értékei |
    | Műveletek | Az utolsó művelet részletei |
    | Tevékenység | A szerződés tranzakciós előzményei |
    
3. A **Művelet csoportban** válassza a **Művelet művelet et.**

4. A szerződés aktuális állapotának részletei egy ablaktáblában jelennek meg. Válassza ki a legördülő menüben végrehajtani kívánt műveletet. 

    ![Művelet kiválasztása](./media/use/choose-action.png)

5. A művelet elindításához jelölje be a **Művelet** kezdeményezése jelölőnégyzetet.
6. Ha a művelethez paraméterek szükségesek, adja meg a művelet értékeit.

    ![Művelet megtemitetése](./media/use/take-action.png)

7. A művelet végrehajtásához jelölje be a **Művelet végrehajtása** jelölőnégyzetet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench alkalmazásverzió-verziók](version-app.md)

---
title: Alkalmazások használata az Azure Blockchain Workbench használatával
description: Alkalmazás használatát ismertető szerződések, az Azure Blockchain Workbench alkalmazásban.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 89c83ed6d02a60978bd54fb97d37063e34f6c0c7
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578850"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Oktatóanyag: Alkalmazások használata az Azure Blockchain Workbench használatával

Blockchain Workbench használatával hozzon létre, és a műveletek végrehajtása a szerződések. Megtekintheti például állapota és a tranzakciós előzmények szerződést kötöttek.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Hozzon létre egy új szerződést
> * Egy művelet végrehajtása a szerződés

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A Blockchain Workbench üzembe helyezés. További információkért lásd: [Azure Blockchain Workbench üzembe helyezési](deploy.md) részleteket az üzembe helyezés
* Egy telepített blockchain alkalmazás a Blockchain Workbenchet. Lásd: [a blockchain-alkalmazások létrehozása az Azure Blockchain Workbench használatával](create-app.md)

[Nyissa meg a Blockchain Workbenchet](deploy.md#blockchain-workbench-web-url) a böngészőben.

![Blockchain Workbench](./media/use/workbench.png)

Jelentkezzen be a Blockchain Workbenchet tagjaként kell. Ha nincsenek a felsorolt alkalmazások, Ön tagja Blockchain Workbenchet, de nem tagja olyan alkalmazást. A Blockchain Workbenchet rendszergazda rendelhet a tagok alkalmazások.

## <a name="create-new-contract"></a>Hozzon létre új szerződést

Hozzon létre egy új szerződést, hogy a szerződés, a megadott tag szüksége **kezdeményező**. Alkalmazás-szerepkörök és a szerződés kezdeményezők információkért lásd: [munkafolyamatok a konfigurálása – áttekintés](configuration.md#workflows). A tagok hozzárendelése alkalmazás-szerepkörök további információkért lásd: [alkalmazást adhat hozzá olyan tagot](manage-users.md#add-member-to-application).

1. A Blockchain Workbench alkalmazás szakaszban válassza ki az alkalmazás csempe, amely tartalmazza a létrehozandó szerződést. Aktív szerződések listája jelenik meg.

2. Hozzon létre egy új szerződést, jelölje be **új szerződést**.

    ![Új szerződés gomb](./media/use/contract-list.png)

3. A **új szerződés** ablaktáblán jelenik meg. Adja meg a kezdeti paraméterek értékeit. Kattintson a **Létrehozás** gombra.

    ![Új szerződés ablaktábla](./media/use/new-contract.png)

    Az újonnan létrehozott szerződés a más aktív szerződések a listán jelenik meg.

    ![Aktív szerződések listája](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Művelet végrehajtása a szerződés

Állapotától függően a szerződés, a tagok váltania műveleteket hajthatja végre a következő állapotra a szerződés. Műveletek vannak meghatározva [átmenetek](configuration.md#transitions) belül egy [állapot](configuration.md#states). Átmenet engedélyezett alkalmazás vagy a példány szerepkörhöz tartozó tagok is hajtsa végre a műveletet. 

1. Blockchain Workbench alkalmazás szakaszban jelölje be az alkalmazás csempe, amely tartalmazza a szerződést, hajtsa végre a műveletet.
2. A listában válassza ki a szerződést. A szerződés adatait különböző szakaszaiban jelennek meg. 

    ![Szerződés részletei](./media/use/contract-details.png)

    | Section  | Leírás  |
    |---------|---------|
    | status | A jelenlegi folyamatban felsorolja a szerződés szakaszokra belül |
    | Részletek | A szerződés az aktuális értékek |
    | Műveletek | Az utolsó művelet részleteit |
    | Tevékenység | A szerződés tranzakciós előzményekben |
    
3. Az a **művelet** szakaszban jelölje be **művelet végrehajtása**.

4. Egy panelen jelennek meg a szerződés aktuális állapotának részleteit. Válassza ki a szeretné állítani a legördülő művelet. 

    ![Művelet kiválasztása](./media/use/choose-action.png)

5. Válassza ki **művelet végrehajtása** kezdeményezése a műveletet.
6. Ha a művelethez szükséges paramétereket, adja meg a műveletet értékeit.

    ![Művelet végrehajtása](./media/use/take-action.png)

7. Válassza ki **művelet végrehajtása** a művelet végrehajtásához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench alkalmazás verziószámozása](version-app.md)

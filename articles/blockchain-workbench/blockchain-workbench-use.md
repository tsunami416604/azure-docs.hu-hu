---
title: Alkalmazások használata az Azure Blockchain Workbench használatával
description: Alkalmazás használata az Azure Blockchain Workbench alkalmazásban szerződések.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e17a9275792e3a7fdbea6e3b95e596eaa15f4359
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105811"
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Alkalmazások használata az Azure Blockchain Workbench használatával

Blockchain Workbench használatával hozzon létre, és a műveletek végrehajtása a szerződések. Megtekintheti például állapota és a tranzakciós előzmények szerződést kötöttek.

## <a name="prerequisites"></a>Előfeltételek

* A Blockchain Workbench üzembe helyezés. További információkért lásd: [Azure Blockchain Workbench üzembe helyezési](blockchain-workbench-deploy.md) részleteket az üzembe helyezés
* Egy telepített blockchain alkalmazás a Blockchain Workbenchet. Lásd: [a blockchain-alkalmazások létrehozása az Azure Blockchain Workbench használatával](blockchain-workbench-create-app.md)

[Nyissa meg a Blockchain Workbenchet](blockchain-workbench-deploy.md#blockchain-workbench-web-url) a böngészőben.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Jelentkezzen be a Blockchain Workbenchet tagjaként kell. Ha nincsenek a felsorolt alkalmazások, Ön tagja Blockchain Workbenchet, de nem tagja olyan alkalmazást. A Blockchain Workbenchet rendszergazda rendelhet a tagok alkalmazások.

## <a name="create-new-contract"></a>Hozzon létre új szerződést 

Hozzon létre egy új szerződést, meg kell lennie egy szerződés, a megadott tag **kezdeményező**. Alkalmazás-szerepkörök és a szerződés kezdeményezők információkért lásd: [munkafolyamatok a konfigurálása – áttekintés](blockchain-workbench-configuration-overview.md#workflows). A tagok hozzárendelése alkalmazás-szerepkörök további információkért lásd: [alkalmazást adhat hozzá olyan tagot](blockchain-workbench-manage-users.md#add-member-to-application).

1. A Blockchain Workbench alkalmazás szakaszban válassza ki az alkalmazás csempe, amely tartalmazza a létrehozandó szerződést. Egy aktív szerződések listája megjelenik.

2. Hozzon létre egy új szerződést, jelölje be **új szerződést**.

    ![Új szerződés gomb](media/blockchain-workbench-use/contract-list.png)

3. A **új szerződés** ablaktáblán jelenik meg. Adja meg a kezdeti paraméterek értékeit. Kattintson a **Létrehozás** gombra.

    ![Új szerződés ablaktábla](media/blockchain-workbench-use/new-contract.png)

    Az újonnan létrehozott szerződés a más aktív szerződések a listán jelenik meg.

    ![Aktív szerződések listája](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Művelet végrehajtása a szerződés

Állapotától függően a szerződés, a tagok váltania műveleteket hajthatja végre a következő állapotra a szerződés. Műveletek vannak meghatározva [átmenetek](blockchain-workbench-configuration-overview.md#transitions) belül egy [állapot](blockchain-workbench-configuration-overview.md#states). Átmenet engedélyezett alkalmazás vagy a példány szerepkörhöz tartozó tagok is hajtsa végre a műveletet. 

1. Blockchain Workbench alkalmazás szakaszban jelölje be az alkalmazás csempe, amely tartalmazza a szerződést, hajtsa végre a műveletet.
2. A listában válassza ki a szerződést. A szerződés adatait különböző szakaszaiban jelennek meg. 

    ![Szerződés részletei](media/blockchain-workbench-use/contract-details.png)

    | Section  | Leírás  |
    |---------|---------|
    | status | A jelenlegi folyamatban felsorolja a szerződés szakaszokra belül |
    | Részletek | A szerződés az aktuális értékek |
    | Műveletek | Az utolsó művelet részleteit |
    | Tevékenység | A szerződés tranzakciós előzményekben |
    
3. Az a **művelet** szakaszban jelölje be **művelet végrehajtása**.

4. Egy panelen jelennek meg a szerződés aktuális állapotának részleteit. Válassza ki a szeretné állítani a legördülő művelet. 

    ![Művelet kiválasztása](media/blockchain-workbench-use/choose-action.png)

5. Válassza ki **művelet végrehajtása** kezdeményezése a műveletet.
6. Ha a művelethez szükséges paramétereket, adja meg a műveletet értékeit.

    ![Művelet végrehajtása](media/blockchain-workbench-use/take-action.png)

7. Válassza ki **művelet végrehajtása** a művelet végrehajtásához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hogyan háríthatók el az Azure Blockchain Workbench használatával](blockchain-workbench-troubleshooting.md)

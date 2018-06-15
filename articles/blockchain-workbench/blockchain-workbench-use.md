---
title: Alkalmazások használata az Azure Blockchain munkaterület
description: Alkalmazás használata Azure Blockchain munkaterület szerződések.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b15d4980875b805b49f7dc1cdb941e607232f3cb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258039"
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Alkalmazások használata az Azure Blockchain munkaterület

Blockchain munkaterület segítségével hozzon létre és műveletek végrehajtása a szerződéseket. Megtekintheti például állapot és a tranzakciós előzményeket részletek szerződést.

## <a name="prerequisites"></a>Előfeltételek

* A Blockchain munkaterület központi telepítés. További információkért lásd: [Azure Blockchain munkaterület központi telepítési](blockchain-workbench-deploy.md) részleteinek a központi telepítés
* Egy telepített blockchain alkalmazás Blockchain munkaterület. Lásd: [blockchain-alkalmazás létrehozása az Azure Blockchain munkaterület]()

[Nyissa meg a Blockchain munkaterület](blockchain-workbench-deploy.md#blockchain-workbench-web-url) a böngészőben.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Jelentkezzen be a Blockchain munkaterület tagjaként kell. Ha nincsenek a felsorolt alkalmazások, is Blockchain munkaterület tagja, de nem tagja az alkalmazásokat. A munkaterület Blockchain rendszergazda tagok rendelhet alkalmazások.

## <a name="create-new-contract"></a>Hozzon létre új szerződés 

Létre kell hoznia egy új szerződést, meg kell lennie a szerződés, egy megadott tag **kezdeményező**. Alkalmazási szerepköröknek és kezdeményezők a Szerződés meghatározása információkért lásd: [munkafolyamatok konfigurációs áttekintésében](blockchain-workbench-configuration-overview.md#workflows). A tagok hozzárendelése szerepkörökhöz információkért lásd: [tag hozzáadása alkalmazás](blockchain-workbench-manage-users.md#add-member-to-application).

1. Blockchain munkaterület alkalmazás területen válassza ki az alkalmazás csempe, amely tartalmazza a szerződés szeretne létrehozni. Egy aktív adategyezmények listáján jelennek meg.

2. Létre kell hoznia egy új szerződést, jelölje be **új szerződés**.

    ![Új szerződés gomb](media/blockchain-workbench-use/contract-list.png)

3. A **új szerződés** ablaktáblán jelenik meg. Adja meg a kezdeti paraméterek értékét. Kattintson a **Létrehozás** gombra.

    ![Új szerződés ablaktábla](media/blockchain-workbench-use/new-contract.png)

    Az újonnan létrehozott szerződés a listát a más aktív szerződések jelenik meg.

    ![Aktív szerződések listája](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Művelet végrehajtása a szerződés

Állapotától függően a szerződést, akkor tagok való áttéréssel műveletek hajthatók végre a következő az egyezmény állapotba. Műveletek is meg van adva [átmenetek](blockchain-workbench-configuration-overview.md#transitions) belül egy [állapot](blockchain-workbench-configuration-overview.md#states). Az átmenet engedélyezett alkalmazás vagy a példány szerepkörhöz tartozó tagok is hajtsa végre a műveletet. 

1. Blockchain munkaterület alkalmazás területen válassza ki az alkalmazás csempe, amely tartalmazza a szerződés hajtsa végre a műveletet.
2. A listában válassza ki a szerződést. A szerződés adatait különböző szakaszokat jelennek meg. 

    ![Szerződés részletei](media/blockchain-workbench-use/contract-details.png)

    | Section  | Leírás  |
    |---------|---------|
    | status | Felsorolja az aktuálisan futó folyamatát a szerződés szakaszból belül |
    | Részletek | A szerződés az aktuális értékek |
    | Műveletek | Az utolsó művelet részleteit |
    | Tevékenység | A szerződés tranzakció előzményei |
    
3. Az a **művelet** szakaszban jelölje be **hajtsa végre a műveletet**.

4. A szerződés az aktuális állapotával kapcsolatos információkat a részletek ablaktáblában jelennek meg. Válassza ki a kívánt műveletet, a legördülő lista a hálózatról. 

    ![A művelet kiválasztása](media/blockchain-workbench-use/choose-action.png)

5. Válassza ki **hajtsa végre a műveletet** való kezdeményezze a műveletet.
6. A művelet paraméterek szükségesek, ha a művelet a értékeket megadni.

    ![Művelet végrehajtása](media/blockchain-workbench-use/take-action.png)

7. Válassza ki **hajtsa végre a műveletet** a művelet végrehajtásához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Blockchain munkaterület hibaelhárítása](blockchain-workbench-troubleshooting.md)
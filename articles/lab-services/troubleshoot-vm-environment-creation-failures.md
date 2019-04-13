---
title: Hibáinak VM és a környezet létrehozása az Azure DevTest Labs |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a virtuális gép (VM) és az Azure DevTest Labs szolgáltatásban hibák környezet létrehozása során.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: 985f28e9a29ebd20abb1db17e6597b2942c9b0c0
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551127"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Végezzen hibaelhárítást a virtuális gép (VM) és a környezet létrehozása sikertelen, az Azure DevTest Labs szolgáltatásban
DevTest Labs lehetővé teszi, figyelmeztetések, ha egy gép neve érvénytelen, vagy ha a kívánt megsértik a labor házirend. Egyes esetekben látja red `X` mellett a labor virtuális gép vagy a környezet állapotát, amely tájékoztatja, hogy probléma merült fel.  Ez a cikk ismerteti, amellyel az alapul szolgáló problémát talál, és, remélhetőleg, a probléma jövőbeni elkerülése néhány trükköket. 

## <a name="portal-notifications"></a>Portál értesítési
Ha használ az Azure Portalon, és tekintse meg az első hely van a **értesítések panel**.  Az értesítések panelen érhető el a fő parancssávon kattintson a **harang ikonra**, megtudhatja, hogy a labor virtuális gép vagy a környezet létrehozása sikeres volt-e.  Ha hiba lépett fel, a hibaüzenet a létrehozásának sikertelensége társított láthatja. A részletek gyakran adjanak meg további segítséget a probléma megoldásához. A következő példában a virtuális gép létrehozása nem sikerült, kevés a magok miatt. A részletes üzenet bemutatja, hogyan lehet kijavítani a problémát, és alapvető a kvóta növelésére.

![Az Azure portal értesítése](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>Tevékenységnaplók 
Ha a hiba a némi várakozás után a virtuális gép vagy a környezet létrehozásának megkísérlése lekérdezéskapcsolatokról meg tevékenységeket tartalmazó naplók. Ez a szakasz bemutatja, hogyan találhatja meg a naplók a virtuális gépek és környezetek. 

## <a name="activity-logs-for-virtual-machines"></a>A virtuális gépek tevékenységeket tartalmazó naplók

1. A kezdőlapon a tesztkörnyezethez, válassza ki a virtuális gép elindítása a **virtuális gép** lapot.
2. Az a **virtuális gép** lap a **figyelés** szakaszban a bal oldali menüben válassza a **tevékenységnapló** a virtuális Géphez társított összes napló megtekintéséhez.  
3. A tevékenység cikkek válassza ki a sikertelen műveletet. A sikertelen műveletet hívja meg általában `Write Virtualmachines`.   
4. A jobb oldali ablaktáblán váltson a JSON-lapon. A részletek a naplóban JSON nézetében látja.

    ![A tevékenységnapló egy virtuális géphez](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Nézze át a JSON-naplót, amíg meg nem találja a `statusMessage` tulajdonság. Biztosít a fő hibaüzenet jelenik meg, és további részletes információkat, ha van ilyen. A következő JSON a cikk korábbi részében látható példa az alapvető quoted túllépte hiba.

    ```json
    "properties": { 
        "statusCode": "Conflict", 
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at http://aka.ms/corequotaincrease.\"}]}}", 
    }, 
    ```

## <a name="activity-log-for-an-environment"></a>Tevékenységnapló-környezetben

Tekintse meg a tevékenységnapló-környezet létrehozásához, kövesse az alábbi lépéseket:

1. Válassza ki a labor kezdőlapja, **Konfigurace a zásady** a bal oldali menüben.
2. az a **Konfigurace a zásady** lapon jelölje be **tevékenységeket tartalmazó naplók** menüben. 
3. A hiba a tevékenység listában a naplóban keresse meg és jelölje ki. 
4. A jobb oldali ablaktáblán, váltson át a JSON-lapon, és keresse meg a **állapotüzenet**. 

    ![Környezet tevékenységnapló](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Resource Manager-sablon telepítési naplók
Ha a környezet vagy a virtuális gép létrejött, az automatizálás, van egy utolsó helyen, és tekintse meg a hibaüzenetet. Ez az Azure Resource Manager-sablon telepítési naplót. A lab-erőforrás létrehozásakor az automatizálás, gyakran keresztül történik egy Azure Resource Manager-sablon üzembe helyezése. Lásd:[ https://github.com/Azure/azure-devtestlab/tree/master/Samples ](https://github.com/Azure/azure-devtestlab/tree/master/Samples) a minta az Azure Resource Manager-sablonok, amelyek a DevTest Labs-erőforrások létrehozásához. 

A lab sablon telepítési naplók megtekintéséhez kövesse az alábbi lépéseket:

1. Indítsa el az erőforráscsoport, amelyben a labor található az oldal.
2. Válassza ki **központi telepítések** a bal oldali menü alatt **beállítások**. 
3. Keresse meg a sikertelen állapotú központi telepítések, és válassza ki azt. 
4. Az a **üzembe helyezési** lapon jelölje be **művelet részletei** meghiúsult művelet hivatkozására. 
5. Nem sikerült a művelet részleteit láthatja a **művelet részletei** ablak. 

## <a name="next-steps"></a>További lépések
Lásd: [az összetevők hibáinak elhárítása](devtest-lab-troubleshoot-artifact-failure.md)
---
title: Virtuális gép- és környezeti hibák elhárítása Az Azure DevTest Labs
description: Ismerje meg, hogyan háríthatja el a virtuális gép (VM) és a környezet létrehozási hibáit az Azure DevTest Labs szolgáltatásban.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166351"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Virtuálisgép-hibák és környezetlétrehozási hibák elhárítása az Azure DevTest Labs ben
DevTest Labs ad figyelmeztetéseket, ha egy gép neve érvénytelen, vagy ha a labor házirend megsértése. Néha piros `X` jelenik meg a labor virtuális gép vagy a környezet állapota, amely tájékoztatja, hogy valami elromlott.  Ez a cikk néhány trükköt tartalmaz, amelyeket az alapul szolgáló probléma megkeresésére használhat, és remélhetőleg a jövőben elkerülheti a problémát.

## <a name="portal-notifications"></a>Portálértesítései
Ha az Azure Portalt használja, az első hely, ahol megnézheti az **értesítések panelt.**  Az értesítések panel, amely a fő parancssávon érhető el a **csengő ikonra**kattintva, megmondja, hogy a tesztkörnyezet virtuális gépe vagy a környezet létrehozása sikeres volt-e vagy sem.  Ha hiba történt, megjelenik a létrehozási hibához kapcsolódó hibaüzenet. A részletek gyakran további információkat tartalmaznak a probléma megoldásához. A következő példában a virtuális gép létrehozása nem sikerült, mert kifogyott a magok. A részletes üzenet bemutatja, hogyan oldhatja meg a problémát, és kérheti az alapvető kvótanövelését.

![Az Azure Portal értesítése](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>Virtuális gép korrupciós állapotban
Ha a virtuális gép állapotát a tesztkörnyezetben **sérültként**látja, előfordulhat, hogy az alapul szolgáló virtuális gép törlődött a **virtuális gép** lapról, amelyre a felhasználó a **Virtuális gépek** lapról navigálhat (nem a DevTest Labs lapról). Tisztítsa meg a laborban a DevTest Labs törlésével a virtuális gép a laborból. Ezután hozza létre újra a virtuális gép a laborban. 

![A virtuális gép sérült állapotban](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Tevékenységnaplók
Tekintse meg a tevékenységnaplókat, ha egy hiba kivizsgálását vizsgálja valamikor a virtuális gép vagy a környezet létrehozása után. Ez a szakasz bemutatja, hogyan keresheti meg a virtuális gépek és környezetek naplóit.

## <a name="activity-logs-for-virtual-machines"></a>Virtuális gépek tevékenységnaplói

1. A tesztkörnyezet kezdőlapján válassza ki a virtuális gépet a **Virtuálisgép** lap elindításához.
2. A **Virtuálisgép** lap **figyelése** szakaszában a bal oldali menüben válassza **a Tevékenységnapló** lehetőséget a virtuális géphez társított összes napló megtekintéséhez.
3. A tevékenységnapló-elemekben válassza ki a sikertelen műveletet. A sikertelen művelet neve általában `Write Virtualmachines`a .
4. A jobb oldali ablaktáblában váltson a JSON fülre. A részleteket a napló JSON nézetében láthatja.

    ![Virtuális gép tevékenységnaplója](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Nézze át a JSON naplót, amíg meg nem találja az `statusMessage` ingatlant. Ez adja meg a fő hibaüzenetet, és további részleteket, ha van ilyen. A következő JSON egy példa a mag idézett túllépte hiba korábban látható ebben a cikkben.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Környezeti tevékenységnapló

A környezet létrehozásához szükséges tevékenységnapló megtekintéséhez kövesse az alábbi lépéseket:

1. A tesztkörnyezet kezdőlapján válassza a bal oldali menü **Konfiguráció és házirendek** elemét.
2. a **Konfiguráció és házirendek** lapon válassza a **menü Tevékenységnaplók parancsát.**
3. Keresse meg a hibát a tevékenységlistában a naplóban, és válassza ki.
4. A jobb oldali ablaktáblában váltson a JSON lapra, és keresse meg az **állapotüzenet**.

    ![Környezeti tevékenységnapló](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Erőforrás-kezelő sablontelepítési naplói
Ha a környezet vagy a virtuális gép automatizálással jött létre, van egy utolsó hely, ahol meg keresheti a hibainformációkat. Ez az Azure Resource Manager-sablon központi telepítési naplója. Ha egy tesztkörnyezet-erőforrás automatizálással jön létre, gyakran egy Azure Resource Manager-sablon üzembe helyezésén keresztül történik. Tekintse[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) meg a devtest labs-erőforrásokat létrehozó Minta-Azure Resource Manager-sablonokat.

A tesztkörnyezetsablon telepítési naplóinak megtekintéséhez kövesse az alábbi lépéseket:

1. Indítsa el annak az erőforráscsoportnak a lapját, amelyben a tesztkörnyezet létezik.
2. Válassza a Beállítások menü **Settings**Bal oldali **menütelepítések parancsát.**
3. Keresse meg a sikertelen állapotú központi telepítéseket, és válassza ki azt.
4. A **Telepítés** lapon válassza a **Művelet részletei** hivatkozást a sikertelen művelethez.
5. A **művelet részletei** ablakban láthatja a sikertelen művelet részleteit.

## <a name="next-steps"></a>További lépések
Lásd: [Műtermék-hibák elhárítása](devtest-lab-troubleshoot-artifact-failure.md)

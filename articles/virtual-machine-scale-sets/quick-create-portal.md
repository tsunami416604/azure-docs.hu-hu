---
title: Rövid útmutató – Virtuálisgép-méretezési csoport létrehozása az Azure Portalon | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre gyorsan virtuálisgép-méretezési csoportot az Azure Portalon
keywords: virtuálisgép-méretezési csoportok
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: quickstart
ms.custom: H1Hack27Feb2017
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: 0a9975b6f44a97d198dc308e263879c3358aab82
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672695"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Gyors útmutató: Virtuálisgép-méretezési csoport létrehozása az Azure Portalon
A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát beállíthatja manuálisan, de automatikus méretezési szabályokat is megadhat az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Egy Azure-terheléselosztó ezután elosztja a forgalmat a méretezési csoportban lévő virtuálisgép-példányok között. Ebben a rövid útmutatóban egy virtuálisgép-méretezési csoportot hoz létre az Azure Portalon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a http://portal.azure.com címen.


## <a name="create-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása
Méretezési csoportokat Windows Server-rendszerképpel vagy Linux-rendszerképpel (például RHEL, CentOS, Ubuntu vagy SLES) helyezhet üzembe.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keresse meg a *méretezési csoportot*, válassza a **Virtuálisgép-méretezési csoport**, majd a **Létrehozás** lehetőséget.
3. Adja meg a méretezési csoport nevét, például *myScaleSet*.
4. Válassza ki a kívánt operációsrendszer-típust, például *Windows Server 2016 Datacenter*.
5. Adja meg az erőforráscsoport kívánt nevét (például *myResourceGroup*) és helyét (például az *USA keleti régiója*).
6. Adja meg a kívánt felhasználónevet, és válasszon ki egy tetszőleges hitelesítési típust.
    - A **jelszónak** legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi feltétel közül háromnak: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter. További információkért lásd: [felhasználónév- és jelszókövetelmények](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
    - Ha a Linux operációsrendszer-lemezt választja, választhatja az **SSH nyilvános kulcs** lehetőséget is. Csak a nyilvános kulcsot adja meg, például: *~/.ssh/id_rsa.pub*. [SSH-kulcsok létrehozásához és használatához](../virtual-machines/linux/mac-create-ssh-keys.md) használhatja a portál Azure Cloud Shell szolgáltatását is.

    ![Alapvető adatait a virtuálisgép-méretezési csoport létrehozásához állítsa be az Azure Portalon](./media/virtual-machine-scale-sets-create-portal/create-scale-set-basic-details.png)
1. Válassza ki egy terheléselosztási beállítás, például *Load balancer*alatt **válassza a beállítások terheléselosztás**. A terheléselosztási beállítás adja meg a fennmaradó részleteit. Például *terheléselosztó* meg kell adnia egy **nyilvános IP-cím neve** és **tartománynévcímke**.
1. Adja meg, hogy virtuális hálózat alatt **virtuális hálózatok konfigurálása**. Például létrehozhat egy új virtuális hálózat *myVirtualNetwork*, és a egy új alhálózatot *alapértelmezett*.
1. A méretezési csoport beállításainak megerősítéséhez, válassza a **Létrehozás** lehetőséget.
    ![Egy virtuálisgép-méretezési csoport létrehozásához szükséges adatok Hálózatkezelés beállítása az Azure Portalon](./media/virtual-machine-scale-sets-create-portal/create-scale-set-networking-details.png)



## <a name="connect-to-a-vm-in-the-scale-set"></a>Kapcsolódás a méretezési csoportban lévő virtuális géphez
Méretezési csoport létrehozásakor a portálon létrejön egy terheléselosztó is. A hálózati címfordítási (NAT-) szabályok a forgalom elosztására használhatók a méretezési csoport példányi között a távoli kapcsolat (például RDP vagy SSH) esetében.

Ezen NAT-szabályok és a méretezési csoport példányaira vonatkozó kapcsolati információk megtekintéséhez tegye a következőket:

1. Válassza ki az előző lépésben létrehozott erőforráscsoportot, például a *myResourceGroup* csoportot.
2. Az erőforrások listájában válassza ki a **terheléselosztót**, például *myScaleSetLab*.
3. Az ablak bal oldali menüjéből válassza a **Bejövő NAT-szabályok** lehetőséget.

    ![A bejövő NAT-szabályok lehetővé teszik a virtuálisgép-méretezési csoport példányaihoz való kapcsolódást](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

Ezen NAT-szabályokkal a méretezési csoport mindegyik virtuális gépéhez kapcsolódhat. Minden virtuálisgép-példány megjelenít egy cél IP-címet és egy TCP-portértéket. Például, ha a cél IP-cím *104.42.1.19* és a TCP-port *50001*, az alábbiak szerint kapcsolódik a virtuálisgép-példányhoz:

- Windows méretezési csoport esetében RDP-vel kapcsolódjon a virtuálisgép-példányhoz a `104.42.1.19:50001` helyen
- Linux méretezési csoport esetében SSH-val kapcsolódjon a virtuálisgép-példányhoz az `ssh azureuser@104.42.1.19 -p 50001` helyen

Amikor a rendszer erre kéri, adja meg a méretezési csoport létrehozásakor, az előző lépésben megadott hitelesítő adatokat. A méretezési csoport példányai normál virtuális gépek, amelyeket a megszokott módon használhat. További információ arról, hogyan helyezhet üzembe és futtathat alkalmazásokat a méretezési csoport példányain: [Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokon](virtual-machine-scale-sets-deploy-app.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje az erőforráscsoportot, a méretezési csoportot és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, és kattintson a **Törlés** elemre.


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egy alapszintű méretezési csoportot hozott létre az Azure Portalon. Ha bővebb információra van szüksége, lépjen tovább az Azure-beli virtuálisgép-méretezési csoportok létrehozásáról és kezeléséről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli virtuálisgép-méretezési csoportok létrehozása és kezelése](tutorial-create-and-manage-powershell.md)

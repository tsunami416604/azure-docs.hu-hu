---
title: "Hozzon létre egy Linux virtuálisgép-méretezési beállítása az Azure portálon |} Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre gyorsan egy virtuálisgép-méretezési az Azure-portálon"
keywords: "Virtuálisgép-méretezési csoportok"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa6bf6b34d8b93ffa9aceaf7c6112c63d4cb9f1c
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Egy virtuálisgép-méretezési készlet létrehozása az Azure portálon
A virtuálisgép-méretezési csoport lehetővé teszi, telepítéséhez és kezeléséhez azonos, az automatikus skálázást virtuális gépek halmazát jelenti. A méretezési csoportban lévő virtuális gépek száma manuálisan méretezhető, vagy szabályokat definiálhat, például a Processzor, memória igény szerint vagy a hálózati forgalom erőforrás-használat alapján automatikus skálázást. Első lépések cikkben hozzon létre egy virtuálisgép-méretezési beállítása az Azure portálon. Is létrehozhat egy méretezési állítható be a [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) vagy [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.


## <a name="create-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása
A skála állítható be egy Windows Server vagy Linux-lemezképbe RHEL, CentOS, Ubuntu vagy SLES például telepítheti.

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.
2. Keresse meg *méretezési*, válassza a **virtuálisgép-méretezési csoport**, majd jelölje be **létrehozása**.
3. Írjon be egy nevet a méretezési, például *myScaleSet*.
4. Válassza ki a kívánt operációs rendszer típusa, például a *Windows Server 2016 Datacenter*.
5. Adja meg a kívánt erőforrás csoport nevét, például *myResourceGroup*, és a helyen, például a *USA keleti régiója*.
6. Adjon meg a kívánt felhasználónevet, és jelölje ki a inkább hitelesítés típusát.
    - A **jelszó** legalább 12 karakter hosszú, és megfelelnek a következő négy bonyolult három kell lennie: egy kisbetű, egy nagybetű, egy számot és egy speciális karaktert. További információkért lásd: [felhasználónév és jelszó](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
    - Ha bejelöli a Linux operációs rendszer lemezképét, választhatja **nyilvános SSH-kulcs**. Csak olyan, mint a nyilvános kulcs *~/.ssh/id_rsa.pub*. Használhatja az Azure felhőalapú rendszerhéj a portálról [létrehozhat és használhat SSH-kulcsok](../virtual-machines/linux/mac-create-ssh-keys.md).

7. Adjon meg egy **nyilvános IP-cím neve**, például a *myPublicIP*.
8. Adjon meg egy egyedi **tartománynév-címke**, például a *myuniquedns*. A DNS-címke elé a méretezési a terheléselosztó FQDN alapja képezi.
9. A méretezési beállítások elfogadásához válassza **létrehozása**.

    ![Hozzon létre egy virtuálisgép-méretezési beállítása az Azure-portálon](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>Csatlakoztassa a virtuális Gépet, a méretezési csoportban lévő
A skála a portálon beállított létrehozásakor egy terhelés-kiegyenlítő jön létre. Hálózati címfordítás (NAT) szabályok a méretezési készlet példányok távoli kapcsolathoz, például az RDP és az SSH-forgalom terjesztésére szolgálnak.

Ezen NAT megtekintéséhez a szabályok és a skála-kapcsolódási információt példányok beállítása:

1. Válassza ki az előző lépésben, például a létrehozott erőforráscsoport *myResourceGroup*.
2. Az erőforrások listájához, jelölje ki a **terheléselosztó**, például a *myScaleSetLab*.
3. Válasszon **bejövő forgalmat kezelő NAT-szabályok** az ablak bal oldalán a menüből.

    ![Bejövő NAT-szabályok lehetővé teszik virtuális gép méretezési készlet példányokhoz való csatlakozáshoz](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

A méretezési készletben, ezek a NAT-szabályok használata a virtuális gépek kapcsolódhat. Minden egyes Virtuálisgép-példány egy IP-célcím és a TCP-port értékének sorolja fel. Például, ha a cél IP-cím *104.42.1.19* és a TCP-port *50001*, csatlakozik a Virtuálisgép-példány az alábbiak szerint:

- A Windows-méretezési csoport kapcsolódjon a virtuális gép RDP-példány a`104.42.1.19:50001`
- A Linux-méretezési csoport kapcsolódjon a virtuális gép SSH-példány a`ssh azureuser@104.42.1.19 -p 50001`

Amikor a rendszer kéri, adja meg a hitelesítő adatokat adja meg az előző lépésben a méretezési csoport létrehozásakor. A méretezési készlet példányai is, amely hatással lehet a normál virtuális gépek normál. A központi telepítéséről és az alkalmazások a méretezési készlet példányán fusson további információkért lásd: [a virtuálisgép-méretezési csoportok az alkalmazás központi telepítése](virtual-machine-scale-sets-deploy-app.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Már nem szükséges, törölje a csoportot, ha a virtuális gépek méretezési csoportjának, és az összes kapcsolódó erőforrások. Ehhez válassza ki a virtuális gép erőforráscsoportját, és kattintson a **Törlés** elemre.


## <a name="next-steps"></a>További lépések
Első lépések cikkben egy alapszintű méretezési készletben az Azure-portálon létrehozott. A jobb méretezhetőség és automatizálás bontsa ki a skála állítható be a következő útmutatók:

- [A virtuálisgép-méretezési csoportok az alkalmazás központi telepítése](virtual-machine-scale-sets-deploy-app.md)
- Automatikusan átméretezi [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), a [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), vagy a [Azure-portálon](virtual-machine-scale-sets-autoscale-portal.md)
- [Automatikus OS frissítéseket használja a méretezési készlet Virtuálisgép-példányok](virtual-machine-scale-sets-automatic-upgrade.md)

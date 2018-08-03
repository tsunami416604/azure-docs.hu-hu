---
title: Egy HPC Pack átjárócsomópont létrehozása az Azure virtuális gép |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre a Microsoft HPC Pack 2012 R2 átjárócsomópont egy Azure-beli virtuális gépen az Azure portal és a Resource Manager üzemi modell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 70c1d95f704315ee6a481367188e2bb620916702
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428956"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>HPC Pack-fürt fő csomópontjának létrehozása piactéri lemezképet használó Azure-beli virtuális gépen
Használja a [Microsoft HPC Pack 2012 R2 virtuálisgép-lemezkép](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) a HPC-fürt fő csomópontjának létrehozása az Azure Marketplace-en és az Azure Portalon. A Windows Server 2012 R2 Datacenter HPC Pack 2012 R2 Update 3 előre telepítve van a HPC Pack VM-lemezkép alapján történik. Az átjárócsomópont fogalom központi telepítését az Azure-beli HPC Pack megvalósíthatósági használja. Számítási csomópontok ezután hozzáadhatja a HPC számítási feladatok futtatásához a fürthöz.

> [!TIP]
> Az Azure-ban, amely tartalmazza a fő csomópontot és a számítási csomópontok teljes HPC Pack 2012 R2-fürt üzembe helyezése, azt javasoljuk, hogy egy automatizált módszert használja. A lehetőségek között a [HPC Pack IaaS telepítési szkripttel](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) és a Resource Manager-sablonok, mint például a [HPC Pack-fürt Windows számítási feladatokhoz](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Resource Manager-sablonok is elérhetők az [Microsoft HPC Pack 2016 fürt](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Tervezési megfontolások
Az alábbi ábrán látható, a HPC Pack fő csomópontot az Active Directory-tartomány egy Azure virtuális hálózaton üzembe helyezi.

![HPC Pack átjárócsomópont][headnode]

* **Active Directory-tartomány**: a virtuális Gépen található szolgáltatások a HPC Pack 2012 R2 átjárócsomópont csatlakozniuk kell az Active Directory-tartományban, az Azure-ban a HPC megkezdése előtt. Ahogyan az ebben a cikkben üzembe helyezési fogalom, megvalósíthatósági léptetheti elő a virtuális Gépet hoz létre az átjárócsomópont tartományvezérlőként a HPC-szolgáltatások indítása előtt. Egy másik lehetőség, hogy telepítsen egy külön tartományvezérlő és az Azure-ban, amelyhez csatlakozhat az átjárócsomóponthoz VM erdőben.

* **Üzemi modell**: A legtöbb új telepítéshez, a Microsoft javasolja, hogy a Resource Manager üzembe helyezési modellt használja. Ez a cikk azt feltételezi, hogy ezt a központi telepítési modellt használja.

* **Az Azure virtual network**: Ha a Resource Manager üzemi modell segítségével az átjárócsomópont, adja meg, vagy hozzon létre egy Azure virtuális hálózatra. Ha az átjárócsomópont csatlakoztatása egy meglévő Active Directory-tartomány van szüksége, használja a virtuális hálózatot. Is szükséges, hogy később számítási csomópont virtuális gépek hozzáadása a fürthöz.

## <a name="steps-to-create-the-head-node"></a>Az átjárócsomópont létrehozásának lépései
Az alábbiakban a magas szintű lépései az Azure portal használatával egy Azure virtuális Gépen az HPC Pack átjárócsomópont létrehozása a Resource Manager-alapú üzemi modell használatával. 

1. Ha szeretne egy új Active Directory-erdő létrehozása az Azure-ban külön tartományvezérlő virtuális gépet, egy lehetőség, hogy használjon egy [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Koncepció telepítési egyszerű megvalósíthatósági is megfelel, ezt a lépést kihagyhatja, és a fő csomópont virtuális gépre konfigurálása tartományvezérlőként. Ez a beállítás leírása a cikk későbbi részében.
1. Az a [HPC Pack 2012 R2, Windows Server 2012 R2 oldalon](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) az Azure Marketplace-en, kattintson a **virtuális gép létrehozása**. 
1. A portálon a a **HPC Pack 2012 R2, Windows Server 2012 R2** lapon válassza ki a **Resource Manager** üzembe helyezési modell, és kattintson **létrehozás**.
   
    ![HPC Pack-lemezkép][marketplace]
1. A portál használatával adja meg a beállításokat, és a virtuális gép létrehozásához. Ha most ismerkedik az Azure-ba, kövesse az oktatóanyag [Windows virtuális gép létrehozása az Azure Portalon](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Koncepció telepítési megvalósíthatósági általában elfogadhatja az alapértelmezett, vagy az ajánlott beállítások.
   
   > [!NOTE]
   > Ha azt szeretné, a fő csomópontot az Azure-ban egy meglévő Active Directory-tartományhoz csatlakozni, ellenőrizze a virtuális gép létrehozásakor adja meg a virtuális hálózat ahhoz a tartományhoz.
   > 
   > 
1. Miután létrehozta a virtuális gép és a virtuális gép fut, [csatlakozzon a virtuális Géphez](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) által a távoli asztal. 
1. Az Active Directory-tartomány erdő csatlakoztatni a virtuális Gépet a következő lehetőségek közül:
   
   * Létrehozta a virtuális gép egy meglévő tartomány erdő egy Azure virtuális hálózaton, csatlakoztassa a virtuális Gépet az erdő standard a Kiszolgálókezelő vagy a Windows PowerShell-eszközök használatával. Ezután indítsa újra.
   * Létrehozta a virtuális Gépet az új virtuális hálózat (nélkül egy meglévő tartomány erdő), majd a virtuális gép előléptetése tartományvezérlővé. Standard szintű lépések segítségével telepítse és konfigurálja az Active Directory tartományi szolgáltatások szerepkör a fő csomópontot. Részletes lépéseiért lásd: [új Windows Server 2012 Active Directory-erdő telepítése](https://technet.microsoft.com/library/jj574166.aspx).
1. Miután a virtuális gép fut-e, és csatlakoztatni kell egy Active Directory-erdőben, a következő indítsa el a HPC Pack szolgáltatásokat:
   
    a. Csatlakozás az átjárócsomóponthoz virtuális Gépet, amely tagja a helyi Rendszergazdák csoport tartományi fiókkal. Például beállíthat egy, az átjárócsomópont virtuális gép létrehozásakor rendszergazdai fiókot használja.
   
    b. Az alapértelmezett fő csomópont-konfiguráció indítsa el a Windows Powershellt rendszergazdaként, és írja be a következőt:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    A HPC Pack-szolgáltatások indítása több percet is igénybe vehet.
   
    Átjárócsomópont további konfigurációs lehetőségeket, írja be a következőt `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>További lépések
* Most már használhatja a HPC Pack-fürt főcsomópontjához. Például a HPC Cluster Manager elindításához, és hajtsa végre a [telepítési feladatlista](https://technet.microsoft.com/library/jj884141.aspx).
* Ha azt szeretné, a fürt számítási kapacitást igény szerinti növelését, vegye fel [Azure-kapacitásnövelés csomópontok](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) felhőszolgáltatásban. 
* Próbálja ki a fürt egy tesztelési számítási feladatok futtatása. Egy vonatkozó példáért tekintse meg a HPC Pack [– első lépések útmutató](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png

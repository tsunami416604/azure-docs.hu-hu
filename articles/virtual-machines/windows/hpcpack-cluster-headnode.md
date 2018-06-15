---
title: Hozzon létre egy Azure virtuális gép egy HPC Pack átjárócsomópont |} Microsoft Docs
description: Útmutató az Azure portál és a Resource Manager üzembe helyezési modellben a Microsoft HPC Pack 2012 R2 átjárócsomópont egy Azure virtuális gép létrehozásához.
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
ms.openlocfilehash: acd4cd44dd35a5b1755d9456f683076567d62165
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915259"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>HPC Pack-fürt fő csomópontjának létrehozása piactéri lemezképet használó Azure-beli virtuális gépen
Használja a [Microsoft HPC Pack 2012 R2 virtuálisgép-lemezkép](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) átjárócsomópontjához HPC-fürt létrehozása az Azure piactéren és az Azure-portálon. A HPC Pack Virtuálisgép-lemezkép a Windows Server 2012 R2 Datacenter HPC Pack 2012 R2 Update 3 előtelepített alapul. Az átjárócsomópont használata az Azure-ban HPC csomag koncepció telepítését igazolása. Számítási csomópontok ezután hozzáadása a fürt HPC munkaterhelések futtatásához.

> [!TIP]
> Telepíteni egy teljes HPC Pack 2012 R2-fürt, amely tartalmazza az átjárócsomópont és a számítási csomópontok az Azure-ban, azt javasoljuk, hogy egy automatizált módot használja. A választható lehetőségek az [HPC Pack IaaS telepítési parancsfájl](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) és a Resource Manager-sablonok, mint a [munkaterhelések Windows HPC Pack fürt](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Resource Manager-sablonok is elérhetők a [Microsoft HPC Pack 2016 fürtök](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Tervezési megfontolások
A következő ábrán látható, hogy átjárócsomópont a HPC Pack Azure virtuális hálózat az Active Directory-tartományhoz.

![HPC Pack átjárócsomópont][headnode]

* **Active Directory-tartomány**: A HPC Pack 2012 R2 átjárócsomópont csatlakoznia kell az Azure Active Directory-tartomány ahhoz, hogy a HPC-szolgáltatások a virtuális Gépen. Ahogy az ebben a cikkben a koncepció telepítési igazolása előléptetheti a virtuális Gépet hoz létre az átjárócsomópont tartományvezérlőként a HPC-szolgáltatások indítása előtt. Egy másik lehetőség egy központi telepítése egy külön tartományvezérlő és az erdő, amelyhez az átjárócsomóponthoz virtuális gép csatlakoztatása az Azure-ban.

* **Telepítési modell**: új központi telepítésére, a Microsoft javasolja, hogy a Resource Manager üzembe helyezési modellben. Ez a cikk feltételezi, hogy ezt a központi telepítési modellt használja.

* **Azure-beli virtuális hálózat**: Ha a Resource Manager telepítési modell segítségével az átjárócsomópont, adja meg, vagy hozzon létre egy Azure virtuális hálózatot. Ha az átjárócsomópont csatlakoztatása egy meglévő Active Directory-tartományhoz kell használja a virtuális hálózatot. Is kell, hogy később számítási csomópont virtuális gépek hozzáadása a fürthöz.

## <a name="steps-to-create-the-head-node"></a>Az átjárócsomópont létrehozásának lépései
Az alábbiakban a magas szintű lépései az Azure-portál használatával hozzon létre egy Azure virtuális Gépen az HPC Pack átjárócsomópont a Resource Manager üzembe helyezési modellben. 

1. Ha azt szeretné, hogy hozzon létre egy új Active Directory-erdő külön tartományvezérlő virtuális gépeket az Azure-ban, egy lehetőség, hogy használja a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). A koncepció telepítési egyszerű igazolása esetén hagyja ki ezt a lépést, és a virtuális gépért átjárócsomópont konfigurálása tartományvezérlőként sikeres. Ez a beállítás később ebben a cikkben ismertetett.
2. Az a [HPC Pack 2012 R2, Windows Server 2012 R2 oldalon](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) kattintson az Azure piactéren **virtuális gép létrehozása**. 
3. A portálon a a **HPC Pack 2012 R2, Windows Server 2012 R2** lapon jelölje be a **erőforrás-kezelő** üzembe helyezési modellt, és kattintson **létrehozása**.
   
    ![HPC Pack kép][marketplace]
4. A portál használatával adja meg a beállításokat, és hozza létre a virtuális Gépet. Ha most ismerkedik az Azure-ba, az oktatóanyag a [Windows virtuális gép létrehozása az Azure portálon](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A koncepció telepítési igazolása általában elfogadhatja az alapértelmezett, vagy ajánlott beállítások.
   
   > [!NOTE]
   > Ha azt szeretné, az átjárócsomópont csatlakoztatása az Azure-ban meglévő Active Directory-tartomány, győződjön meg arról, hogy a tartomány a virtuális hálózat adja meg a virtuális gép létrehozásakor.
   > 
   > 
5. Miután a virtuális gép létrehozása és a virtuális gép fut, [csatlakoztassa a virtuális Gépet](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) által a távoli asztal. 
6. A virtuális gép csatlakoztatása az Active Directory-tartomány erdő válasszon az alábbi lehetőségek közül:
   
   * Ha létrehozta a virtuális Gépet egy Azure virtuális hálózatban egy meglévő tartomány erdő az, csatlakoztassa a virtuális gép az erdő szabványos a Kiszolgálókezelő vagy a Windows PowerShell-eszközökkel. Indítsa újra.
   * Ha létrehozott egy új virtuális hálózat (nélkül egy meglévő tartomány erdő) a virtuális Gépet, majd a virtuális gép előléptetés tartományvezérlőként működik. Standard szintű lépések segítségével telepítse és konfigurálja az Active Directory tartományi szolgáltatások szerepkör az átjárócsomópont. Részletes útmutató: [egy új Windows Server 2012 Active Directory-erdő telepítése](https://technet.microsoft.com/library/jj574166.aspx).
7. Miután a virtuális gép fut, és csatlakozik egy Active Directory-erdőben, az alábbiak szerint indítsa el a HPC Pack szolgáltatásokat:
   
    a. A virtuális gép, amely tagja a helyi Rendszergazdák csoport tartományi fiókkal átjárócsomópontjához csatlakozik. Például használja a rendszergazdai fiók beállítása az átjárócsomóponthoz virtuális gép létrehozása után.
   
    b. Alapértelmezett átjáró-csomóponti konfiguráció esetén indítsa el a Windows Powershellt rendszergazdaként, és írja be a következőt:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    A HPC Pack szolgáltatásokat több percet is igénybe vehet.
   
    Átjárócsomópont további konfigurációs beállítások, írja be a következőt `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>További lépések
* A HPC Pack fürt átjárócsomópontjához mostantól együttműködik. Például, indítsa el a HPC Cluster Manager, és végezze el a [telepítési feladatlista](https://technet.microsoft.com/library/jj884141.aspx).
* Ha azt szeretné, a fürt számítási kapacitás igény növelése érdekében adja [Azure-kapacitásnövelés csomópontok](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) felhőszolgáltatásban. 
* Próbálja meg futtatni egy teszt munkaterhelés a fürtön. Egy vonatkozó példáért lásd: a HPC Pack [– első lépések útmutató](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png

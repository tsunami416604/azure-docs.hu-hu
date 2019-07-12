---
title: Az Azure Linux rendszerű virtuális gép mérete – HPC |} A Microsoft Docs
description: A nagy teljesítményű Linux rendszerű virtuális gépek az Azure-ban elérhető különböző méretű sorolja fel. Tartalmazza a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség-sorozat méretei száma.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 847f25d9be1a8654bbc0435d7874acb0ff793304
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695600"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Nagy teljesítményű számítási virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Az SR-IOV engedélyezett Virtuálisgép-méretek az Azure-ban használandó MPI, szinte bármilyen íz engedélyezése.
Virtuális gépeken nem SR-IOV engedélyezve van csak az Intel MPI 5.x verziók támogatottak. Újabb verzió (2017-es, 2018.) az Intel MPI-futtatókörnyezetet könyvtár feltétlenül nem kompatibilis az Azure Linux RDMA-illesztőprogramokat.


### <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
 
Az Azure Marketplace-en számos Linux-disztribúció, amelyek támogatják az RDMA-kapcsolattal rendelkezik:
  
* **CentOS-alapú HPC** – nem SR-IOV engedélyezve van a virtuális gépek, 6.5-ös verziójú CentOS-alapú HPC- vagy újabb verzió, akár 7.5 alkalmasak. A H-sorozatú virtuális gépek esetében a 7.5 7.1-es verziók használata ajánlott. RDMA-illesztőprogramok és az Intel MPI 5.1 telepítve a virtuális gépen.
  Az SR-IOV virtuális gépek a CentOS-HPC 7.6 optimalizált és az RDMA-illesztőprogramok és a különféle telepített MPI-csomagok előre betöltött származnak.
  További RHEL/CentOS Virtuálisgép-rendszerképeket adja hozzá a InfiniBandLinux bővítményt, InfiniBand engedélyezéséhez. A Linux VM-bővítmény telepítése Mellanox OFED illesztőprogramjai (virtuális gépeken az SR-IOV) RDMA-kapcsolattal. A következő PowerShell-parancsmag telepíti a legújabb verzióra (1.0-s verzió) a InfiniBandDriverLinux bővítmény meglévő RDMA-kompatibilis virtuális gép. Az RDMA-kompatibilis virtuális gép *myVM* nevű erőforráscsoport van üzembe helyezve, és *myResourceGroup* a a *USA nyugati RÉGIÓJA* régió az alábbiak szerint:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Azt is megteheti a Virtuálisgép-bővítmények hozzáadhatók az Azure Resource Manager-sablonok az egyszerű telepítés és a következő JSON-elem:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  A következő parancsot a legújabb 1.0-s verzió InfiniBandDriverLinux bővítmény telepítését pedig egy meglévő Virtuálisgép-méretezési csoportban nevű RDMA-kompatibilis virtuális gépek *myVMSS* nevű erőforráscsoportban üzembe helyezett *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > A CentOS-alapú HPC-képekhez kernelfrissítés le vannak tiltva a **yum** konfigurációs fájlt. Ez azért, mert az RPM-csomag Linux RDMA-illesztőprogramok terjesztett, és illesztőprogramok frissítéseit nem lehet, hogy működik, ha a kernel frissül.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 HPC, SLES 12 SP3 HPC (Premium), SLES 12 SP3 HPC, SLES 12 SP1 HPC (Premium), SLES 12 SP4 és SLES 15 SP1. RDMA-illesztőprogramok lesznek telepítve, és az Intel MPI csomagok oszlanak meg a virtuális gépen. MPI telepítse a következő parancs futtatásával:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** – Ubuntu Server 16.04 LTS, 18.04 LTS. RDMA-illesztőprogramok a virtuális gép konfigurálása és regisztrálása az Intel MPI letöltéséhez Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  InfiniBand, MPI, beállításának engedélyezésével kapcsolatos további részletekért lásd: [engedélyezése InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Fürt konfigurációs lehetőségek

Az Azure Linuxos HPC virtuális gépeken, amely az RDMA hálózati használatával kommunikálhatnak fürtöket számos lehetőséget kínál a többek között: 

* **Virtuális gépek** – az RDMA-kompatibilis HPC virtuális gépek ugyanazon rendelkezésre állási csoportjának (Ha használja az Azure Resource Manager üzemi modell) üzembe helyezése. Ha a klasszikus üzemi modellt használja, az ugyanazon a felhőszolgáltatáson virtuális gépek üzembe helyezése. 

* **A Virtual machine scale sets** – a virtuálisgép-méretezési csoportot beállítani, győződjön meg arról, hogy korlátozza az üzembe helyezés az elhelyezési csoportból. Például egy Resource Manager-sablon beállítása az `singlePlacementGroup` tulajdonságot `true`. 

* **Virtuális gépek között MPI** – Ha MPI-kommunikáció a virtuális gépek (VM-EK) között szükség esetén győződjön meg arról, hogy a virtuális gépeket egyazon rendelkezésre állási vagy vannak beállítva a virtuális gépet azonos méretezési csoportot.

* **Az Azure CycleCloud** – a HPC-fürt létrehozása [Azure CycleCloud](/azure/cyclecloud/) MPI-feladatok futtatása a Linux-csomópontokat.

* **Az Azure Batch** – hozzon létre egy [Azure Batch](/azure/batch/) MPI számítási feladatok futtatása Linux-készlet számítási csomópontjain. További információkért lásd: [használata RDMA-kompatibilis vagy a GPU-kompatibilis példányok a Batch-készletek](../../batch/batch-pool-compute-intensive-sizes.md). További tájékoztatás a [Batch hajógyárnak](https://github.com/Azure/batch-shipyard) tárolóalapú számítási feladatok futtatásához a Batch-projektben.

* **A Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) számos Linux-disztribúció futtatását számítási csomópontokra RDMA-kompatibilis Azure-beli virtuális gépeken, támogatja a Windows Server fő csomópontot kezeli. Telepítését bemutató példát lásd: [HPC Pack Linux RDMA-fürt létrehozása az Azure-ban](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>A hálózatokkal kapcsolatos szempontok
* Nem – az SR-IOV, a Linux rendszerű virtuális gépek RDMA-kompatibilis az Azure-ban, eth1 RDMA hálózati forgalom számára van fenntartva. Ne módosítsa bármely eth1 beállításai vagy a konfigurációs fájl hivatkozó ezen a hálózaton található bármely információ.
* Az SR-IOV engedélyezve van a virtuális gépek (HB és a hibrid kapcsolat sorozat), ib0 RDMA hálózati forgalom számára van fenntartva.
* Az Azure-ban az RDMA hálózati fenntartja a cím terület 172.16.0.0/16. MPI-alkalmazások egy Azure virtuális hálózaton üzembe helyezett példányokon futnak, győződjön meg róla, hogy a virtuális hálózat címtere nem fedi át az RDMA-hálózatot.
* Fürtkezelő válaszaitól függően szükség lehet további rendszerkonfiguráció MPI-feladatok futtatásához. Például a virtuális gépek fürtjeit, szükség lehet a fürt csomópontjai közötti bizalmi kapcsolat létrehozásához, az SSH-kulcsok létrehozásakor, vagy beállításának SSH-bejelentkezési kísérletekre létrehozó.


## <a name="other-sizes"></a>További méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Előző generációs szoftvereknél jobban](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

- További információ annak beállítása, optimalizálásában és méretezésében [HPC számítási feladatok](../workloads/hpc/configure.md) az Azure-ban.
- Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.

---
title: Azure-beli VM-méretek – HPC | Microsoft Docs
description: Felsorolja a nagy teljesítményű számítástechnikai virtuális gépek számára elérhető különböző méreteket az Azure-ban. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: b900a95df00ccdd0ad9b5bee3887364195c7d1c2
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226721"
---
# <a name="high-performance-compute-vm-sizes"></a>Nagy teljesítményű számítási VM-méretek

Az Azure H-sorozatú virtuális gépek (VM-EK) úgy vannak kialakítva, hogy vezetői szintű teljesítményt, MPI-skálázhatóságot és költséghatékonyságot nyújtsanak számos valós HPC-számítási feladathoz.

[HBv2 sorozat](hbv2-series.md) A virtuális gépek 200 GB/s Mellanox HDR-InfiniBand, míg a HB és a HC sorozatú virtuális gépek 100 GB/s Mellanox EDR InfiniBand rendelkeznek. Ezen virtuálisgép-típusok mindegyike egy nem blokkoló FAT-fában csatlakozik az optimalizált és konzisztens RDMA teljesítmény érdekében. A HBv2 virtuális gépek támogatják az adaptív útválasztást és a dinamikus csatlakoztatott átvitelt (a DCT a standard RC és UD átvitelek esetében). Ezek a funkciók növelik az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és használata erősen ajánlott.

[HB sorozat](hb-series.md) A virtuális gépek a memória sávszélessége által vezérelt alkalmazások számára vannak optimalizálva, például a Fluid Dynamics, a explicit véges elemek elemzése és az időjárási modellezés. A HB-beli virtuális gépeken 60 AMD EPYC 7551 processzor-mag, 4 GB RAM/CPU mag, és nincs feleznie. Az AMD EPYC platform több mint 260 GB/s memóriát biztosít.

[HC sorozat](hc-series.md) A virtuális gépek a sűrű számítások által vezérelt alkalmazások számára vannak optimalizálva, például implicit, véges elemek elemzése, molekuláris dinamika és számítási kémia. A HC-alapú virtuális gépek a 44 Intel Xeon Platinum 8168 processzor-magot, a CPU-mag 8 GB RAM-ot, a feleznie pedig nem rendelkeznek. Az Intel Xeon Platinum platform támogatja az Intel olyan szoftverek gazdag ökoszisztémáját, mint az Intel Math kernel Library.

[H sorozat](h-series.md) A virtuális gépeket magas CPU-gyakorisággal vagy nagy memóriával rendelkező alkalmazások számára optimalizálták. A H-sorozatú virtuális gépek 8 vagy 16 Intel Xeon E5 2667 v3 processzorral, 7 vagy 14 GB RAM memóriával rendelkeznek, és nem feleznie. A H-sorozat 56 GB/s Mellanox FDR InfiniBand a konzisztens RDMA teljesítményének nem blokkoló FAT-konfigurációjában. A H-sorozatú virtuális gépek támogatják az Intel MPI 5. x és az MS-MPI használatát.

## <a name="deployment-considerations"></a>Telepítési szempontok

- **Azure-előfizetés** – több számítási igényű példány üzembe helyezéséhez vegye fontolóra az utólagos elszámolású előfizetést vagy az egyéb vásárlási lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

- **Díjszabás és rendelkezésre állás** – ezeket a virtuálisgép-méreteket a rendszer csak a standard szintű díjszabás keretében kínálja. Tekintse meg az Azure-régiókban rendelkezésre [álló régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/) .
- **Magok kvótája** – előfordulhat, hogy a magok kvótáját az alapértelmezett érték alapján kell megnövelni az Azure-előfizetésben. Előfordulhat, hogy az előfizetés bizonyos virtuálisgép-méretekben üzembe helyezhető magok számát is korlátozhatja, beleértve a H-sorozatot is. A kvóta növeléséhez [Nyisson meg egy online ügyfélszolgálati kérést](../azure-supportability/how-to-create-azure-support-request.md) díjmentesen. (Az alapértelmezett korlátok az előfizetési kategóriától függően változhatnak.)

  > [!NOTE]
  > Ha nagy léptékű kapacitásra van szüksége, forduljon az Azure ügyfélszolgálatához. Az Azure-kvóták hitelkeretek, nem kapacitási garanciák. A kvótától függetlenül csak a használt magokért kell fizetnie.
  
- **Virtual Network (virtuális hálózat** ) – az Azure [virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/) nem szükséges a nagy számítási igényű példányok használatához. Számos központi telepítés esetében azonban szükség van legalább egy felhőalapú Azure-beli virtuális hálózatra, vagy egy helyek közötti kapcsolatra, ha a helyszíni erőforrásokhoz kell hozzáférnie. Ha szükséges, hozzon létre egy új virtuális hálózatot a példányok telepítéséhez. Ha nagy számítási igényű virtuális gépeket ad hozzá egy affinitási csoportban lévő virtuális hálózathoz, az nem támogatott.
- **Átméretezés** – a speciális hardverek miatt csak a számítási igényű példányokat lehet átméretezni ugyanazon a méretű családon belül (H-sorozat vagy nagy számítási igényű a-sorozat). Például csak egy h sorozatú virtuális gépet lehet átméretezni egy H-sorozatos méretről egy másikra. Emellett a nem számítási igényű méretről a nagy számítási igényű méretre való átméretezés nem támogatott.  

> [!NOTE]
> Az A8 – A11-es virtuális gépek a 3/2021-es kivonulásra vannak tervezve. További információ: [HPC áttelepítési útmutató](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>RDMA-kompatibilis példányok

A nagy számítási igényű példányok egy részhalmaza (A8, A9, H16r, H16mr, HB és HC) egy hálózati adaptert biztosít a távoli közvetlen memória-hozzáférési (RDMA) kapcsolathoz. Az r-vel jelölt kiválasztott N sorozatú méretek (például a NC24rs konfigurációk (NC24rs_v2 és NC24rs_v3) is RDMA-kompatibilisek. Ez az interfész a szabványos Azure hálózati adapteren kívül más virtuálisgép-méretek számára is elérhető.

Ez az interfész lehetővé teszi, hogy a RDMA-kompatibilis példányok InfiniBand (IB) hálózaton keresztül kommunikáljanak, amely a HB, a HC, a H16r, a H16mr és a RDMA-kompatibilis N sorozatú virtuális gépekhez használható, valamint az A8-as és A9-es virtuális gépek QDR-díjain alapul. Ezek a RDMA-képességek növelhetik bizonyos Message Passing Interface-(MPI-) alkalmazások méretezhetőségét és teljesítményét. A sebességgel kapcsolatos további információkért tekintse meg az ezen a lapon található táblázatok részleteit.

> [!NOTE]
> Az Azure-ban az IB-alapú IP-cím csak az SR-IOV-kompatibilis virtuális gépeken támogatott (SR-IOV for InfiniBand, jelenleg HB és HC). Az IB protokollon keresztüli RDMA minden RDMA-kompatibilis példány esetében támogatott.
- **Operációs rendszer** – Windows Server 2016 a HPC sorozat összes virtuális gépe. A Windows Server 2012 R2, a Windows Server 2012 szintén támogatott a nem SR-IOV-kompatibilis virtuális gépeken (így a HB és a HC kivételével).

- **MPI** – az SR-IOV által engedélyezett virtuálisgép-méretek az Azure-ban (HB, HC) lehetővé teszik, hogy szinte bármilyen típusú MPI-t MELLANOX-OFED lehessen használni.
A nem SR-IOV-kompatibilis virtuális gépeken támogatott MPI-implementációk a Microsoft Network Direct (ND) felületet használják a példányok közötti kommunikációhoz. Ezért csak a Microsoft MPI (MS-MPI) 2012 R2 vagy újabb, illetve az Intel MPI 5. x verziói támogatottak. Az Intel MPI runtime library újabb verziói (2017, 2018) vagy esetleg nem kompatibilisek az Azure RDMA-illesztőprogramokkal.

- **InfiniBandDriverWindows** virtuálisgép-BŐVÍTMÉNY – RDMA-kompatibilis virtuális gépeken a InfiniBandDriverWindows-bővítmény hozzáadásával engedélyezheti a InfiniBand. Ez a Windowsos virtuálisgép-bővítmény telepíti a Windows Network Direct-illesztőprogramokat (nem SR-IOV virtuális gépeken) vagy a Mellanox OFED-illesztőprogramokat (SR-IOV virtuális gépeken) a RDMA-kapcsolathoz.
Az A8-as és A9-es példányok bizonyos telepítései esetében a HpcVmDrivers-bővítmény automatikusan hozzáadódik. Vegye figyelembe, hogy a HpcVmDrivers VM-bővítmény elavult; nem lesz frissítve. A virtuálisgép-bővítmény virtuális géphez való hozzáadásához [Azure PowerShell](/powershell/azure/overview) parancsmagokat használhat. 

  A következő parancs a 1,0-es InfiniBandDriverWindows-bővítményt telepíti egy meglévő RDMA-kompatibilis virtuális gépre, amelyet az *USA nyugati* régiójában, az *myResourceGroup* *nevű erőforráscsoport helyez üzembe* .

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Alternatív megoldásként a virtuálisgép-bővítmények Azure Resource Manager sablonokban is elérhetők az egyszerű üzembe helyezéshez, a következő JSON-elemmel:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  A következő parancs telepíti a legújabb 1,0 InfiniBandDriverWindows-bővítményt az összes RDMA-kompatibilis virtuális gépen egy *myVMSS* nevű meglévő virtuálisgép-méretezési csoportba, amelyet a *myResourceGroup*nevű erőforráscsoport telepít:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  További információ: [virtuálisgép-bővítmények és-szolgáltatások](./extensions/overview.md). A [klasszikus üzemi modellben](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)üzembe helyezett virtuális gépek bővítményei is használhatók.

- **RDMA hálózati címtartomány** – az Azure RDMA-hálózata fenntartja a 172.16.0.0/16 címtartomány méretét. Ha az MPI-alkalmazásokat egy Azure-beli virtuális hálózaton üzembe helyezett példányokon szeretné futtatni, győződjön meg arról, hogy a virtuális hálózati címtartomány nem fedi át a RDMA-hálózatot.

## <a name="cluster-configuration-options"></a>Fürt konfigurációs beállításai

Az Azure számos lehetőséget kínál a RDMA-hálózattal kommunikáló Windows HPC virtuális gépek fürtjének létrehozására, beleértve a következőket: 

- **Virtual Machines** – helyezze üzembe a RDMA-kompatibilis HPC virtuális gépeket ugyanabban a rendelkezésre állási készletben (az Azure Resource Manager üzemi modell használatakor). Ha a klasszikus üzemi modellt használja, telepítse a virtuális gépeket ugyanabban a felhőalapú szolgáltatásban. 

- **Virtuálisgép-méretezési** csoportok – egy virtuálisgép-méretezési csoportban győződjön meg arról, hogy egyetlen elhelyezési csoportra korlátozza a központi telepítést. Például egy Resource Manager-sablonban állítsa a `singlePlacementGroup` tulajdonságot `true`re. 

- **MPI a virtuális gépek között** – ha a virtuális gépek (VM-EK) között MPI-kommunikációra van szükség, győződjön meg arról, hogy a virtuális gépek ugyanahhoz a rendelkezésre állási csoporthoz vagy a virtuális géphez hasonló méretezési csoporthoz tartoznak.

- **Azure CycleCloud** – HPC-fürt létrehozása az [Azure CYCLECLOUD](/azure/cyclecloud/) -ben MPI-feladatok futtatásához Windows-csomópontokon.

- **Azure batch** – hozzon létre egy [Azure batch](/azure/batch/) -készletet MPI-számítási feladatok futtatásához Windows Server számítási csomópontokon. További információ: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](../batch/batch-pool-compute-intensive-sizes.md). Tekintse meg a [Batch-hajógyár](https://github.com/Azure/batch-shipyard) projektjét, amely a tároló-alapú számítási feladatok futtatására használható a Batch szolgáltatásban.

- A **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) olyan FUTÁSIDEJŰ környezetet tartalmaz az MS-MPI számára, amely az Azure RDMA-hálózatot használja, ha RDMA-kompatibilis Linux rendszerű virtuális gépekre van telepítve. Központi telepítések például: [Linux RDMA-fürt beállítása HPC-csomaggal MPI-alkalmazások futtatásához](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

- A Windows Serveren futó HPC Packtel rendelkező, nagy számítási igényű példányok használatát ellenőrző Feladatlisták: [Linux RDMA-fürt beállítása HPC-csomaggal MPI-alkalmazások futtatásához](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

- Ha Azure Batch használatával MPI-alkalmazások futtatásához nagy számítási igényű példányokat szeretne használni, tekintse meg a [többpéldányos feladatok használata a Message Passing Interface (MPI) alkalmazások Azure batch-ben való futtatásához](../batch/batch-mpi.md)című témakört.

- További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
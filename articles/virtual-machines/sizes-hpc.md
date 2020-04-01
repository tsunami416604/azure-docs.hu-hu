---
title: Az Azure virtuális gépek méretei - HPC | Microsoft dokumentumok
description: Az Azure-ban a nagy teljesítményű számítástechnikai virtuális gépekhez elérhető különböző méretek listája. A vCPU-k, adatlemezek és hálózati adapterek számával, valamint a tárolóátviteli és hálózati sávszélességgel kapcsolatos információkat sorolja fel a sorozat méreteihez.
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
ms.openlocfilehash: df22c857571e51bb886ff1d25db185a306999540
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420875"
---
# <a name="high-performance-computing-vm-sizes"></a>Nagy teljesítményű számítástechnikai virtuális gépek méretei

Az Azure H sorozatú virtuális gépek (VM-ek) úgy vannak kialakítva, hogy a vezetéshez szükséges teljesítményt, mpi-méretezhetőséget és költséghatékonyságot biztosítsanak a különböző valós HPC-számítási feladatokhoz.

[HBv2 sorozat](hbv2-series.md) A virtuális gépek 200 Gb/sec Mellanox HDR InfiniBand- tel rendelkeznek, míg a HB és hc sorozatú virtuális gépek 100 Gb/sec Mellanox EDR InfiniBand-el rendelkeznek. Ezek a virtuálisgép-típusok mindegyike egy nem blokkoló zsírfában csatlakozik az optimalizált és konzisztens RDMA-teljesítmény érdekében. A HBv2 virtuális gépek támogatják az adaptív útválasztást és a dinamikus csatlakoztatott átvitelt (DCT, a szabványos RC és UD átvitelek mellett). Ezek a funkciók növelik az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és használatuk erősen ajánlott.

[HB-sorozat](hb-series.md) A virtuális gépek a memória sávszélessége által vezérelt alkalmazásokhoz vannak optimalizálva, például a folyadékdinamika, az explicit véges elemek elemzése és az időjárás modellezése. A HB virtuális gépek 60 AMD EPYC 7551 processzormagot, 4 GB RAM-ot cpu magonként tartalmaznak, és nincs hyperthreading. Az AMD EPYC platform több mint 260 GB/s memória sávszélességet biztosít.

[HC-sorozat](hc-series.md) A virtuális gépek a sűrű számítás által vezérelt alkalmazásokhoz vannak optimalizálva, például implicit véges elemelemzés, molekuláris dinamika és számítási kémia. A HC virtuális gépek 44 Intel Xeon Platinum 8168 processzormagot, cpumagonként 8 GB RAM-ot és hyperthreading nélküli processzormagot tartalmaznak. Az Intel Xeon Platinum platform támogatja az Intel gazdag ökoszisztéma szoftver eszközök, mint például az Intel Math Kernel Library.

[H-sorozat](h-series.md) A virtuális gépek a magas CPU-frekvenciák vagy az alapvető követelmények szerint nagy memória által vezérelt alkalmazásokhoz vannak optimalizálva. A H sorozatú virtuális gépek 8 vagy 16 Intel Xeon E5 2667 v3 processzormagot, cpumagonként 7 vagy 14 GB RAM-ot tartalmaznak, és nem tartalmaznak hyperthreading-et. A H sorozat 56 Gb/sec Mellanox FDR InfiniBand-et tartalmaz, amely nem blokkolja a zsírfa konfigurációt az egyenletes RDMA teljesítmény érdekében. A H sorozatú virtuális gépek támogatják az Intel MPI 5.x és MS-MPI-t.

> [!NOTE]
> Az A8 – A11-es virtuális gépek a tervek szerint 3/2021-re tervezik a nyugdíjba vonulást. További információ: [HPC Migration Guide](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>RDMA-kompatibilis példányok

A legtöbb HPC vm méret (HBv2, HB, HC, H16r, H16mr, A8 és A9) rendelkezik egy hálózati interfésza távoli közvetlen memória-hozzáférés (RDMA) kapcsolat. A kiválasztott [N-sorozat] (azhttps://docs.microsoft.com/azure/virtual-machines/nc-series) "r" betűvel jelölt méretek, mint például az NC24rs konfigurációk (NC24rs_v3, NC24rs_v2 és NC24r) szintén RDMA-kompatibilisek. Ez a felület a más virtuális gépméretekben elérhető szabványos Azure hálózati felületen kívül található.

Ez az interfész lehetővé teszi, hogy az RDMA-kompatibilis példányok InfiniBand (IB) hálózaton keresztül kommunikáljanak, hdr-díjokkal működnek a HBv2, a HB, HC, A H16r, H16mr és RDMA-képes N-sorozatú virtuális gépek Esetében a QDR-díjak, valamint az A8 és A9 virtuális gépek QDR-díjai. Ezek az RDMA-képességek növelhetik bizonyos MPI-alkalmazások méretezhetőségét és teljesítményét. A sebességről további információt az ezen az oldalon található táblázatokban talál.

> [!NOTE]
> Az Azure HPC-ben a virtuális gépek két osztálya van attól függően, hogy azok SR-IOV engedélyezve vannak-e az InfiniBand számára. Jelenleg az SR-IOV az InfiniBand-kompatibilis virtuális gépekhez a következő: HBv2, HB, HC és NCv3. Az InfiniBand-kompatibilis virtuális gépek többi része nincs sr-IOV engedélyezve.
> RdMA iB-en keresztül minden RDMA-kompatibilis virtuális gép támogatott.
> Az IB-n keresztüli IP-cím csak az SR-IOV-kompatibilis virtuális gépeken támogatott.

- **Operációs rendszer** - A Linux nagyon jól támogatott a HPC virtuális gépek, disztribúciók, mint a CentOS, RHEL, Ubuntu, SUSE gyakoriak. Ami a Windows-támogatást illeti, a Windows Server 2016 minden HPC sorozatú virtuális gépen támogatott. A Nem SR-IOV-kompatibilis virtuális gépek en a Windows Server 2012 2012 rendszer 2012-es és Windows Server 2012 rendszerű.

- **MPI** - Az SR-IOV engedélyezte a virtuális gép méreteit az Azure-ban (HBv2, HB, HC, NCv3), amelyek lehetővé teszik az MPI szinte bármilyen ízét a Mellanox OFED-vel.
A nem SR-IOV-kompatibilis virtuális gépeken a támogatott MPI-implementációk a Microsoft Network Direct (ND) felületen keresztül kommunikálnak a virtuális gépek között. Ezért csak a Microsoft MPI (MS-MPI) 2012 R2 vagy újabb és az Intel MPI 5.x verziók támogatottak. Az Intel MPI-futásidejű függvénytár későbbi verziói (2017, 2018) lehetnek, hogy nem kompatibilisek az Azure RDMA-illesztőprogramokkal.

- **InfiniBandDriver<Linux| Windows> virtuálisgép-bővítmény** - RDMA-kompatibilis virtuális gépeken adja hozzá az InfiniBandDriver<Linuxot| A Windows> bővítményt az InfiniBand engedélyezéséhez. Linuxon az InfiniBandDriverLinux VM bővítmény telepíti a Mellanox OFED illesztőprogramokat (SR-IOV virtuális gépeken) rdma-kapcsolathoz. Windows rendszeren az InfiniBandDriverWindows VM bővítmény telepíti a Windows Network Direct illesztőprogramokat (nem SR-IOV virtuális gépeken) vagy Mellanox OFED illesztőprogramokat (SR-IOV virtuális gépeken) rdma-kapcsolathoz.
Az A8-as és A9-es példányok bizonyos telepítéseiesetén a HpcVmDrivers bővítmény automatikusan hozzáadódik. Vegye figyelembe, hogy a HpcVmDrivers vm bővítmény elavult; nem lesz frissítve.
A virtuális gép bővítmény hozzáadása a virtuális géphez, használhatja az Azure PowerShell-parancsmagokat. [Azure PowerShell](/powershell/azure/overview) 

  A következő parancs a legújabb 1.0-s verziójú InfiniBandDriverWindows bővítményt telepíti egy meglévő RDMA-kompatibilis virtuális gépre, *amelyet myVM-nek* neveznek el az *USA nyugati régiójában* a *myResourceGroup* nevű erőforráscsoportban:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Másik lehetőségként a virtuálisgép-bővítmények az Azure Resource Manager-sablonokban is szerepelhetnek az egyszerű üzembe helyezés érdekében, a következő JSON-elemmel:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  A következő parancs a legújabb 1.0-s verziójú InfiniBandDriverWindows bővítményt telepíti az összes RDMA-kompatibilis virtuális gépre egy *myVMSS* nevű, a *myResourceGroup*nevű erőforráscsoportban telepített meglévő virtuálisgép-méretezési csoportban:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  További információt a [Virtuálisgép-bővítmények és -szolgáltatások című témakörben talál.](./extensions/overview.md) A [klasszikus üzembe helyezési modellben](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)üzembe helyezett virtuális gépek bővítményeivel is dolgozhat.

- **RDMA hálózati címterület** – Az Azure RDMA-hálózata a 172.16.0.0/16 címteret fenntartja. Mpi-alkalmazások futtatásához az Azure virtuális hálózatban telepített példányokon győződjön meg arról, hogy a virtuális hálózati címterület nem fedi át az RDMA-hálózatot.

## <a name="cluster-configuration-options"></a>Fürt konfigurációs beállításai

Az Azure számos lehetőséget kínál az RDMA-hálózaton keresztül kommunikáló Windows HPC-virtuális gépek fürtjeinek létrehozására, többek között a következőket: 

- **Virtuális gépek** – Telepítse az RDMA-kompatibilis HPC-virtuális gépeket ugyanabban a méretezési vagy rendelkezésre állási csoportban (az Azure Resource Manager központi telepítési modelljének használatakor). Ha a klasszikus üzembe helyezési modellt használja, telepítse a virtuális gépeket ugyanabban a felhőszolgáltatásban.

- **Virtuálisgép-méretezési készletek** – Egy virtuálisgép-méretezési csoport (VMSS) győződjön meg arról, hogy korlátozza a központi telepítés egyetlen elhelyezési csoportra. Egy Erőforrás-kezelő sablonban például `singlePlacementGroup` állítsa `true`a tulajdonságot . Vegye figyelembe, hogy a maximális VMSS-méret, amely megpördülhet fel `singlePlacementGroup` a tulajdonság `true` gal éri, alapértelmezés szerint 100 virtuális gép. Ha a HPC-feladat méretezési igényei nek nagyobb, mint 100 virtuális gép egy VMSS-bérlőben, kérheti a növekedést, [megnyithat egy online ügyfélszolgálati kérelmet](../azure-supportability/how-to-create-azure-support-request.md) díjmentesen.

- **MPI virtuális gépek között** – Ha RDMA (pl. MPI-kommunikáció) szükséges a virtuális gépek (Virtuális gépek) között, győződjön meg arról, hogy a virtuális gépek ugyanabban a virtuális gép méretezési vagy rendelkezésre állási csoportban vannak.

- **Azure CycleCloud** – Hozzon létre egy HPC-fürtöt az [Azure CycleCloudban](/azure/cyclecloud/) az MPI-feladatok futtatásához.

- **Azure Batch** – Hozzon létre egy [Azure Batch-készlet](/azure/batch/) et az MPI-számítási feladatok futtatásához. Ha nagy számítási igényű példányokat szeretne használni az MPI-alkalmazások Azure Batch-el történő futtatásakor, olvassa el [a Többpéldányos feladatok használata az MPI-alkalmazások futtatásához](../batch/batch-mpi.md)az Azure Batchben című témakört.

- **A Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) egy MS-MPI-hez készült futásidejű környezetet tartalmaz, amely az Azure RDMA-hálózatot használja rdma-kompatibilis Linux-alapú virtuális gépeken történő üzembe helyezésekor. Például a központi telepítések, [lásd: Linux RDMA-fürt beállítása HPC Pack MPI-alkalmazások futtatásához](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Telepítési szempontok

- **Azure-előfizetés** – Ha néhánynál több nagy számítási igényű példányt szeretne üzembe helyezni, fontolja meg a használatalapú előfizetést vagy más vásárlási lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

- **Díjszabás és rendelkezésre állás** – Ezek a virtuális gép méretek csak a standard tarifacsomagban. Tekintse meg [a régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/) et az Azure-régiókban való elérhetőségszempontjából.

- **Cores kvóta** – Előfordulhat, hogy növelnie kell a magok kvótáját az Azure-előfizetésben az alapértelmezett értékről. Az előfizetés is korlátozhatja a magok száma, amelyet üzembe helyezhet bizonyos virtuálisgép-méretcsaládokban, beleértve a H-sorozat. A kvótanövelésének igényléséhez [nyisson meg egy online ügyfélszolgálati kérelmet](../azure-supportability/how-to-create-azure-support-request.md) díjmentesen. (Az alapértelmezett korlátok az előfizetési kategóriától függően változhatnak.)

  > [!NOTE]
  > Lépjen kapcsolatba az Azure-támogatással, ha nagy kapacitással rendelkezik. Az Azure-kvóták hitelkeret, nem kapacitásgarancia. A kvótától függetlenül csak a használt magokért kell fizetnie.
  
- **Virtuális hálózat** – Az Azure-beli [virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/) nem szükséges a nagy számítási igényű példányok használatához. Azonban sok központi telepítések szüksége van legalább egy felhőalapú Azure virtuális hálózat, vagy egy hely-hely kapcsolat, ha a helyszíni erőforrások eléréséhez szükséges. Szükség esetén hozzon létre egy új virtuális hálózatot a példányok üzembe helyezéséhez. A számítási igényű virtuális gépek hozzáadása egy affinitási csoport ban lévő virtuális hálózathoz nem támogatott.

- **Átméretezés** – Speciális hardverük miatt csak azonos méretű (H-sorozatú vagy számításigényes A-sorozat) számított nagy példányokat méretezhet át. Például csak átméretezheti a H-sorozat virtuális gép egyik H-sorozat mérete a másikra. Emellett a nem számítási igényű méretről a számításigényes méretre való átméretezés nem támogatott.  


## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

- További információ a HPC-alkalmazás Azure-beli optimalizálásáról és néhány példa a [HPC-munkaterhelések] ( hpc-munkaterhelések) című webhelyen(https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.

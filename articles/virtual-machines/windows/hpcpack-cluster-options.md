---
title: "A Windows HPC Pack fürt beállítások az Azure-ban |} Microsoft Docs"
description: "További tudnivalók a Microsoft HPC Pack létrehozása és kezelése a Windows nagy teljesítményű számítástechnikai (HPC) fürt Azure felhőben beállítások"
services: virtual-machines-windows,cloud-services,batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: 3dd00d4fb0d334e836256e66207358c034fc005a
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>HPC Pack létrehozása és kezelése Windows HPC-munkaterhelések az Azure-ban fürt beállítások
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Ez a cikk foglalkozik, a Windows-munkaterhelések futtatásához HPC Pack fürtök létrehozására vonatkozó beállításokat. Módon is HPC Pack fürtök létrehozására vonatkozó futtatásához [Linux HPC munkaterhelések](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Az Azure virtuális gépek és Virtuálisgép-méretezési készlet HPC Pack fürt
### <a name="azure-templates"></a>Azure-sablonok
* (GitHub) [HPC Pack 2016 fürt sablonok](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2-fürt sablonok](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [Munkaterhelések Windows HPC Pack fürt](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [HPC Pack fürt Excel munkaterhelések](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (Gyors üzembe helyezés) [HPC-fürt létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Gyors üzembe helyezés) [Egyéni számítási csomópont lemezképpel HPC-fürt létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Az Azure Virtuálisgép-rendszerképek
* [Windows Server 2016 HPC Pack 2016 átjárócsomópontjához](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [A Windows Server 2016 HPC Pack 2016 számítási csomópont](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [HPC Pack 2016 átjárócsomópont Windows Server 2012 R2 rendszeren](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [HPC Pack 2016 számítási csomóponton a Windows Server 2012 R2 rendszeren](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [HPC Pack 2012 R2 átjárócsomópont Windows Server 2012 R2 rendszeren](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [HPC Pack 2012 R2 számítási csomóponton a Windows Server 2012 R2 rendszeren](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [HPC Pack számítási csomópont az Excel alkalmazással Windows Server 2012 R2 rendszeren](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>PowerShell telepítési parancsfájlt a HPC Pack 2012 R2 rendszerben
* [A HPC Pack IaaS telepítési parancsfájl a HPC-fürt létrehozása](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Oktatóanyagok
* [Oktatóanyag: HPC Pack 2016-fürt üzembe helyezése az Azure-ban](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Oktatóanyag: Ismerkedés az Azure-, Excel és SOA alkalmazásokat és szolgáltatásokat futtathatnak HPC Pack fürtöt](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Az Azure portálon manuális telepítése
* [Egy Azure virtuális gép HPC Pack fürt átjárócsomópontjához beállítása](hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>Fürtkezelés
* [Az Azure-ban HPC Pack-fürtben lévő számítási csomópontok kezelése](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Növelhető vagy csökkenthető a HPC Pack-fürtben lévő Azure számítási erőforrásokat](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Az Azure-ban HPC Pack fürthöz feladatok elküldéséhez](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack feladatok kezelése](https://technet.microsoft.com/library/jj899585.aspx)
* [Az Azure szolgáltatásban az Azure Active Directory HPC Pack 2016 fürtöt kezelése](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-worker-role-nodes"></a>-Kapacitásnövelés a feldolgozói szerepkör csomópontok 
* [-Kapacitásnövelés a HPC Pack Azure feldolgozói példány](https://technet.microsoft.com/library/gg481749.aspx)
* [Oktatóanyag: Az Azure-ban HPC Pack hibrid fürt beállítása](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Az Azure "kapacitásnövelés" csomópontok hozzáadása a HPC Pack átjárócsomópont az Azure-ban](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-azure-batch"></a>Az Azure Batch-kapacitásnövelés
* [-Kapacitásnövelés a HPC Pack az Azure Batch](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>MPI munkaterhelések RDMA-fürtök
* [HPC Packet tartalmazó Windows RDMA-fürt beállítása MPI-alkalmazások futtatására](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


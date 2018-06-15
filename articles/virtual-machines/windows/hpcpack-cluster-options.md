---
title: A Windows HPC Pack fürt beállítások az Azure-ban |} Microsoft Docs
description: További tudnivalók a Microsoft HPC Pack létrehozása és kezelése a Windows nagy teljesítményű számítástechnikai (HPC) fürt Azure felhőben beállítások
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165757"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>HPC Pack létrehozása és kezelése Windows HPC-munkaterhelések az Azure-ban fürt beállítások
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Ez a cikk a Windows-alkalmazásokat és szolgáltatásokat futtathatnak HPC Pack fürtök létrehozásához Azure beállításait összpontosít. Módon is HPC Pack fürtök létrehozására vonatkozó futtatásához [Linux HPC munkaterhelések](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Az Azure virtuális gépek és Virtuálisgép-méretezési készlet HPC Pack fürt
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
* (GitHub) [HPC Pack 2016 Update 1 fürt sablonok](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2-fürt sablonok](https://github.com/MsHpcPack/HPCPack2012R2)
* (Gyors üzembe helyezés) [HPC Pack 2012 R2-fürt létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Gyors üzembe helyezés) [Egyéni számítási csomópont lemezképpel egy HPC Pack 2012 R2-fürt létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Az Azure Virtuálisgép-rendszerképek
Tallózás [HPC Pack képek az Azure piactéren](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) Ha azt szeretné, ha az Azure-ban a saját HPC Pack fürtöt hozza létre.


### <a name="tutorials"></a>Oktatóanyagok
* [Oktatóanyag: HPC Pack 2016-fürt üzembe helyezése az Azure-ban](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az Azure szolgáltatásban az Azure Active Directory HPC Pack 2016 fürtöt kezelése](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>A klasszikus üzembe helyezési modellel HPC Pack 2012 R2-fürttelepítés
* [A HPC Pack IaaS telepítési parancsfájl a HPC-fürt létrehozása](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Oktatóanyag: Ismerkedés az Azure-, Excel és SOA alkalmazásokat és szolgáltatásokat futtathatnak HPC Pack fürtöt](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az Azure-ban HPC Pack-fürtben lévő számítási csomópontok kezelése](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Növelhető vagy csökkenthető a HPC Pack-fürtben lévő Azure számítási erőforrásokat](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [HPC Packet tartalmazó Windows RDMA-fürt beállítása MPI-alkalmazások futtatására](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>Az Azure-kapacitásnövelés a HPC Pack 2012 R2-ről
* [A Microsoft HPC Pack Azure feldolgozói példány kapacitásnövelés](https://technet.microsoft.com/library/gg481749.aspx)
* [-Kapacitásnövelés a HPC Pack az Azure Batch](https://technet.microsoft.com/library/mt612877.aspx)
* [Oktatóanyag: Az Azure-ban HPC Pack hibrid fürt beállítása](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>Feladat elküldése

* [Az Azure-ban HPC Pack fürthöz feladatok elküldéséhez](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)







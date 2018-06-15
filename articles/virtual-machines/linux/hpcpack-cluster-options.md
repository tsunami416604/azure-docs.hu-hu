---
title: Linux HPC Pack fürt beállításai az Azure-ban |} Microsoft Docs
description: További tudnivalók a Microsoft HPC Pack létrehozása és kezelése a Linux nagy teljesítményű számítástechnikai (HPC) fürt Azure felhőben beállítások
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 281867e30c78c7ed36ac739c8ae1a902463199cd
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166454"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>HPC Pack létrehozását és kezelését egy fürt Linux HPC-munkaterhelések az Azure-beállítások
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Ez a cikk foglalkozik a HPC Pack Linux munkaterhelések futtatásához használandó Azure beállításait. Módon is futtatásához [Windows HPC-munkaterhelések HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Az Azure virtuális gépek és Virtuálisgép-méretezési készlet HPC Pack fürt
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
* (GitHub) [HPC Pack 2016 Update 1 fürt sablonok](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2-fürt sablonok](https://github.com/MsHpcPack/HPCPack2012R2)
* (Gyors üzembe helyezés) [Hozzon létre egy HPC Pack 2012 R2-fürt Linux számítási csomópontok](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Az Azure Virtuálisgép-rendszerképek
Tallózás [HPC Pack képek az Azure piactéren](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) Ha azt szeretné, ha az Azure-ban a saját HPC Pack fürtöt hozza létre.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>Klasszikus üzembe helyezési modellel HPC Pack 2012 R2-fürt
* [Hozzon létre egy Linux HPC-fürtöt a HPC Pack IaaS telepítési parancsfájl](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI-alkalmazások futtatására Linux RDMA fürt beállítása](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Megismerkedés a Linux számítási csomópontok HPC Pack fürtben az Azure-ban](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Futtassa a Microsoft HPC Pack NAMD Linux számítási csomópontok az Azure-ban](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Futtatás OpenFOAM Microsoft HPC Pack Azure Linux RDMA fürtön](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Futtatás csillag-CCM + Microsoft HPC Pack egy Linux RDMA a fürt az Azure-ban](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Feladat submisssion
* [Az Azure-ban HPC Pack fürthöz feladatok elküldéséhez](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)





---
title: Az Azure-ban Linuxos HPC Pack-fürt beállítások |} A Microsoft Docs
description: A Microsoft HPC Packkel hozhat létre és kezelhet egy Linux rendszerű nagy teljesítményű számítástechnika (HPC-) fürt az Azure-felhőben lehetőségek ismertetése
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
ms.openlocfilehash: 79600909387b1876b112219b5b9b1e45ba4054b7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340075"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Lehetőségek a hozhat létre és kezelhet egy fürtöt Linux-alapú HPC számítási feladatok Azure-beli HPC Pack segítségével
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Ez a cikk foglalkozik az Azure-lehetőségek a HPC Pack segítségével Linuxos számítási feladatok futtatásához. Emellett lehetőség van a futó [a HPC Pack segítségével Windows HPC-munkaterhelések](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>HPC Pack-fürthöz az Azure virtuális gépek és Virtuálisgép-méretezési csoportok
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
* (GitHub) [HPC Pack 2016 Update 1 fürt sablonok](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2-fürt sablonok](https://github.com/MsHpcPack/HPCPack2012R2)
* (A rövid útmutató) [-HPC Pack 2012 R2-fürt létrehozása Linuxos számítási csomópontok](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Az Azure Virtuálisgép-rendszerképek
Tallózás [HPC Pack-rendszerképeket az Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) Ha szeretne létrehozni a saját Azure-beli HPC Pack-fürthöz.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>HPC Pack 2012 R2-fürt a klasszikus üzemi modellben
* [Hozzon létre egy Linux-alapú HPC-fürt HPC Pack IaaS telepítési szkripttel](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Ismerkedés a Linuxos számítási csomópontok az Azure-beli HPC Pack-fürthöz](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: A Microsoft HPC packkel NAMD futtatása Linux számítási csomópontok az Azure-ban](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Az OpenFOAM futtatása Linux RDMA-fürt az Azure-ban a Microsoft HPC packkel](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Futtatás STAR-CCM + a Linux rdma-t a Microsoft HPC Pack-fürt az Azure-ban](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Feladat submisssion
* [Az Azure-beli HPC Pack-fürt-feladatok elküldése](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)





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
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: 96e77b739550c935316f7bade57b8aac7e634f02
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>HPC Pack létrehozását és kezelését egy fürt Linux HPC-munkaterhelések az Azure-beállítások
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Ez a cikk foglalkozik Linux alkalmazásokat és szolgáltatásokat futtathatnak HPC Pack használatával a beállítások. Módon is futtatásához [Windows HPC-munkaterhelések HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Az Azure virtuális gépek és Virtuálisgép-méretezési készlet HPC Pack fürt
### <a name="azure-templates"></a>Azure-sablonok
* (GitHub) [HPC Pack 2016 fürt sablonok](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2-fürt sablonok](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [HPC Pack fürt Linux munkaterhelések](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (Gyors üzembe helyezés) [Linux számítási csomópontok HPC-fürt létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>PowerShell telepítési parancsfájlt a HPC Pack 2012 R2 rendszerben
* [Hozzon létre egy Linux HPC-fürtöt a HPC Pack IaaS telepítési parancsfájl](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Oktatóanyagok
* [Oktatóanyag: Megismerkedés a Linux számítási csomópontok HPC Pack fürtben az Azure-ban](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Futtassa a Microsoft HPC Pack NAMD Linux számítási csomópontok az Azure-ban](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Futtatás OpenFOAM Microsoft HPC Pack Azure Linux RDMA fürtön](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Oktatóanyag: Futtatás csillag-CCM + Microsoft HPC Pack egy Linux RDMA a fürt az Azure-ban](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>Fürtkezelés
* [Az Azure-ban HPC Pack fürthöz feladatok elküldéséhez](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack feladatok kezelése](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>MPI munkaterhelések RDMA-fürtök
* [Oktatóanyag: Futtatás OpenFOAM Microsoft HPC Pack Azure Linux RDMA fürtön](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI-alkalmazások futtatására Linux RDMA fürt beállítása](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)


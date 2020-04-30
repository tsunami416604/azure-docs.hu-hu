---
title: Megoldási architektúrák a Azure NetApp Files használatával | Microsoft Docs
description: Az Azure NetApp Filest használó megoldási architektúrák ajánlott eljárásaira mutató hivatkozásokat tartalmaz.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: b-juche
ms.openlocfilehash: a26e403671a2f69eeb785d92fa0c1d37a19e3ac5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085076"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Megoldásarchitektúrák az Azure NetApp Filesszal
Ez a cikk az ajánlott eljárásokra mutató hivatkozásokat tartalmaz, amelyek segítségével megismerheti a Azure NetApp Files használatának megoldási architektúráit.  

## <a name="azure-high-performance-computing-hpc-solutions"></a>Azure nagy teljesítményű számítástechnikai (HPC) megoldások

* [A Reservoir szimulációs szoftver futtatása az Azure-ban](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)
* [MPI-munkaterhelések futtatása Azure Batch és Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)

## <a name="azure-kubernetes-service-aks-solutions"></a>Azure Kubernetes szolgáltatás (ak) megoldásai

* [Azure NetApp Files integráció az AK-val](https://github.com/andyzhangx/demo/tree/master/linux/nfs)
* [Azure NetApp Files integrálása az Azure Kubernetes szolgáltatással](https://docs.microsoft.com/azure/aks/azure-netapp-files)

## <a name="oracle-database-solutions"></a>Oracle Database-megoldások

* [Oracle az Azure-beli üzembe helyezéssel kapcsolatos ajánlott eljárási útmutató az Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle VM-rendszerképek és azok üzembe helyezése Microsoft Azure: megosztott tároló konfigurációs beállításai](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Az Azure NetApp Files Oracle Database-szel való használatának előnyei](https://docs.microsoft.com/azure/azure-netapp-files/solutions-benefits-azure-netapp-files-oracle-database)

## <a name="sap-application-solutions"></a>SAP-alkalmazási megoldások 

* [SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával](https://www.netapp.com/us/media/tr-4746.pdf)
* [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver-hez SUSE Linux Enterprise Serveron Azure NetApp Files SAP-alkalmazásokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver-hez Red Hat Enterprise Linuxon Azure NetApp Files SAP-alkalmazásokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Magas rendelkezésre állás az SAP NetWeaver Azure-beli virtuális gépeken Red Hat Enterprise Linux for SAP Applications multi-SID Guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Magas rendelkezésre állás a Windows rendszerű Azure-beli virtuális gépeken futó SAP NetWeaver számára az SAP-alkalmazások Azure NetApp Files (SMB) szolgáltatásával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [SAP HANA az Azure-beli virtuális gépek készenléti csomópontjaival, a Azure NetApp Files a SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [SAP HANA Azure-beli virtuális gépek készenléti csomópontjának kiskálázása Azure NetApp Files a RedHat Enterprise Linux rendszeren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
* [Azure NetApp Files – SAP HANA biztonsági mentés másodpercek alatt](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – a HANA-adatbázis visszaállítása pillanatkép biztonsági másolatból](https://blog.netapp.com/azure-netapp-files-backup-sap-hana/)
* [Azure NetApp Files – SAP HANA a biztonsági mentés kiszervezése a Cloud Sync szolgáltatással](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [A SAP HANA rendszermásolatok felgyorsítása Azure NetApp Files használatával](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Felhőbeli kötetek ONTAP és Azure NetApp Files: SAP HANA rendszer áttelepítése egyszerűen](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/) 

## <a name="global-file-caching-solutions"></a>Globális fájl-gyorsítótárazási megoldások

* [Globálisan elosztott nagyvállalati fájlmegosztás Azure NetApp Files és Talon gyors™](https://www.talonstorage.com/products/azure-netapp-files)

## <a name="windows-virtual-desktopvdi-solutions"></a>Windows rendszerű virtuális asztali/VDI-megoldások

* [FSLogix-profil tárolójának létrehozása a Azure NetApp Files használatával](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)


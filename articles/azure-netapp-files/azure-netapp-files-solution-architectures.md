---
title: Megoldásarchitektúrák az Azure NetApp-fájlok használatával | Microsoft dokumentumok
description: Az Azure NetApp-fájlok használatával a megoldásarchitektúrák kal kapcsolatos gyakorlati tanácsokra mutató hivatkozásokat tartalmaz.
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
ms.date: 03/27/2020
ms.author: b-juche
ms.openlocfilehash: 8eae528c965e599e7adfb546a09b0d5879e7c54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369529"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Megoldásarchitektúrák az Azure NetApp Filesszal
Ez a cikk az azure NetApp-fájlok megoldásarchitektúráinak megismeréséhez ajánlott eljárásokra mutató hivatkozásokat tartalmaz.  

## <a name="azure-high-performance-computing-hpc-solutions"></a>Az Azure nagy teljesítményű számítástechnikai (HPC) megoldásai

* [Tározószimulációs szoftver futtatása az Azure-ban](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)
* [MPI-számítási feladatok futtatása az Azure Batch és az Azure NetApp-fájlokkal](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)

## <a name="azure-kubernetes-service-aks-solutions"></a>Az Azure Kubernetes szolgáltatás (AKS) megoldásai

* [Az Azure NetApp-fájlok integrációja az AKS-sel](https://github.com/andyzhangx/demo/tree/master/linux/nfs)
* [Az Azure NetApp-fájlok integrálása az Azure Kubernetes szolgáltatással](https://docs.microsoft.com/azure/aks/azure-netapp-files)

## <a name="oracle-database-solutions"></a>Oracle adatbázis-megoldások

* [Az Oracle az Azure üzembe helyezéséhez útmutató az Azure NetApp-fájlok használatával](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle virtuálisgép-lemezképek és azok üzembe helyezése a Microsoft Azure-ban: Megosztott tárkonfigurációs beállítások](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)

## <a name="sap-application-solutions"></a>SAP alkalmazásmegoldások 

* [SAP-alkalmazások a Microsoft Azure-ban az Azure NetApp-fájljainak használatával](https://www.netapp.com/us/media/tr-4746.pdf)
* [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [Az SAP NetWeaver magas rendelkezésre állása az Azure virtuális gépeken a SUSE Linux Enterprise Server en az SAP-alkalmazásokhoz készült Azure NetApp-fájlokkal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken a Red Hat Enterprise Linuxon az SAP-alkalmazásokhoz készült Azure NetApp-fájlokkal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken a Red Hat Enterprise Linux sap-alkalmazásokhoz több SID-útmutatóban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken Windows rendszeren, SAP-alkalmazásokhoz készült Azure NetApp-fájlokkal (SMB)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [SAP HANA horizontális felskálázás készenléti csomókkal az Azure virtuális gépein az Azure NetApp-fájlokkal a SUSE Linux Enterprise Server rendszeren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [SAP HANA horizontális felskálázás készenléti csomókkal az Azure virtuális gépein az Azure NetApp-fájlokkal A RedHat Enterprise Linuxon](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
* [Azure NetApp-fájlok – SAP HANA biztonsági mentés másodpercek alatt](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp-fájlok – A HANA-adatbázis visszaállítása pillanatkép-biztonsági másolatból](https://blog.netapp.com/azure-netapp-files-backup-sap-hana/)
* [Azure NetApp-fájlok – AZ SAP HANA kiszervezi a biztonsági mentést a Cloud Sync segítségével](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Gyorsítsa fel az SAP HANA rendszer példányait az Azure NetApp-fájlok használatával](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Felhőkötetek ONTAP és Azure NetApp-fájlok: AZ SAP HANA rendszer áttelepítése egyszerűen](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/) 

## <a name="global-file-caching-solutions"></a>Globális fájlgyorsítótárazási megoldások

* [Globálisan elosztott nagyvállalati fájlmegosztás az Azure NetApp Files és a Talon FAST segítségével™](https://www.talonstorage.com/products/azure-netapp-files)

## <a name="windows-virtual-desktopvdi-solutions"></a>Windows virtuális asztal/VDI megoldások

* [FSLogix-profiltároló létrehozása gazdakészlethez az Azure NetApp-fájlok használatával](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)


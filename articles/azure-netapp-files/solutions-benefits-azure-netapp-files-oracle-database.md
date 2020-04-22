---
title: Az Azure NetApp Files oracle-adatbázissal való használatának előnyei | Microsoft dokumentumok
description: Ez a témakör a technológiát ismerteti, és teljesítmény-összehasonlítást biztosít az Oracle Direct NFS (dNFS) és a hagyományos NFS-ügyfél között. A dNFS és az Azure NetApp-fájlok használatának előnyeit jeleníti meg.
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
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: d28f5469174a2659869ebb01ef01653b7190e30e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772079"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Az Azure NetApp-fájlok Oracle Database szolgáltatással való használatának előnyei

Az Oracle Direct NFS (dNFS) lehetővé teszi, hogy nagyobb teljesítményt nyújtson, mint az operációs rendszer saját NFS-illesztőprogramja. Ez a cikk ismerteti a technológiát, és teljesítmény-összehasonlítást biztosít a dNFS és a hagyományos NFS-ügyfél (Kernel NFS) között. Azt is bemutatja az előnyöket és a könnyű használata dNFS Az Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Az Oracle Direct NFS működése

Az Oracle Direct NFS (dNFS) megkerüli az operációs rendszer puffergyorsítótárát. Az adatok gyorsítótárba helyezéséhez csak egyszer kerül sor a felhasználói térben, így a memóriamásolatok többletterhelése kiküszöböli a gyorsítótárat.  

A hagyományos NFS-ügyfél egyetlen hálózati folyamatot használ, ahogy a következő példa mutatja: 

![Hagyományos NFS-ügyfél egyetlen hálózati folyamathasználatával](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

Ezzel szemben az Oracle dNFS javítja a teljesítményt azáltal, hogy több hálózati folyamat on-át terheléselosztási hálózati forgalmat bonyolít. Ez a képesség lehetővé teszi az Oracle-adatbázis számára, hogy dinamikusan hozzon létre jelentős számú 650 különböző hálózati kapcsolatot, amint azt az alábbi példa mutatja:  

![Az Oracle Direct NFS teljesítményének javítása](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

A [Direct NFS Oracle FAQ azt](http://www.orafaq.com/wiki/Direct_NFS) mutatja, hogy az Oracle dNFS optimalizált NFS-ügyfél. Gyors és skálázható hozzáférést biztosít a NAS tárolóeszközökön található NFS-tárolókhoz (TCP/IP-n keresztül érhető el). A dNFS ugyanúgy be van építve az adatbáziskernelbe, mint az ASM, amelyet elsősorban a DAS vagy a SAN storage használ. Mint ilyen, *az iránymutatás a dNFS használata a NAS-tároló és az ASM használata a SAN-tároló megvalósításakor.*

A dNFS az alapértelmezett beállítás az Oracle 18c-ban és az ALAPÉRTELMEZETT a RAC-ban.

A dNFS az Oracle Database 11g-től kezdve érhető el. Az alábbi ábra összehasonlítja a dNFS-t a natív NFS-sel. DNFS használata esetén egy Azure virtuális gépen futó Oracle-adatbázis több I/O-t tud vezetni, mint a natív NFS-ügyfél.

![Oracle és Azure NetApp Files a dNFS és a natív NFS összehasonlítása](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

A dNFS-t két parancs futtatásával és az adatbázis újraindításával engedélyezheti vagy letilthatja.

Az engedélyezéshez:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

A letiltáshoz:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp-fájlok az Oracle Direct NFS-sel kombinálva

Az Azure NetApp Files szolgáltatással növelheti az Oracle dNFS teljesítményét. A szolgáltatás teljes körű vezérlést biztosít az alkalmazás teljesítményéhez. Rendkívül nagy igénybevételt jelentő alkalmazásoknak is megfelel. Az Oracle dNFS és az Azure NetApp Files kombinációja nagy előnyt jelent a számítási feladatok számára.

## <a name="next-steps"></a>További lépések

- [Megoldásarchitektúrák az Azure NetApp Filesszal](azure-netapp-files-solution-architectures.md)
- [Az Oracle alkalmazások és megoldások áttekintése az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)
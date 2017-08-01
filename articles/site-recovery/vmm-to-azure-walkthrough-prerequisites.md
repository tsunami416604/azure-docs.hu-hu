---
title: "A Hyper-V-ről az Azure-ba az Azure Site Recovery használatával (System Center VMM-mel) történő replikáció előfeltételeinek áttekintése | Microsoft Docs"
description: "A cikkből megtudhatja, mik a helyszíni Hyper-V virtuális gépek Azure-ba történő replikálásának, feladatátvételének és helyreállításának előfeltételei a VMM-felhőkben az Azure Site Recovery használatakor."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 87a5d721ca785329b407d31126bd0b211b17ccf3
ms.contentlocale: hu-hu
ms.lasthandoff: 07/26/2017

---



# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>2. lépés: A (VMM-mel felügyelt) Hyper-V-ről az Azure-ba történő replikáció előfeltételeinek áttekintése

Miután áttekintette a [forgatókönyv-architektúrát](vmm-to-azure-walkthrough-architecture.md), olvassa el ezt a cikket, hogy biztosan megértse az üzembe helyezés előfeltételeit. 

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

**Követelmény** | **Részletek**
--- | ---
**Azure-fiók** | Szüksége van egy [Microsoft Azure-fiókra](http://azure.microsoft.com/).
**Azure Storage** | A replikált adatok tárolásához Azure-tárfiókra van szükség.<br/><br/> A tárfióknak és az Azure Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.<br/><br/>[Georedundáns tárolást](../storage/storage-redundancy.md#geo-redundant-storage) és helyileg redundáns tárolást is használhat. A georedundáns tárolást javasoljuk. A georedundáns tárolás akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében is gondoskodik az adatok hibatűréséről.<br/><br/> Standard szintű Azure Storage-tárfiókot vagy Azure [Prémium szintű tárolást](../storage/storage-premium-storage.md) is használhat. A Prémium szintű tárolás képes I/O-igényes számítási feladatok futtatására, és általában olyan virtuális gépekhez használják, amelyek számára elengedhetetlen a folyamatosan magas I/O-teljesítmény és a kis késés. Ha Prémium szintű tárolást használ a replikált adatokhoz, Standard szintű tárfiókra is szüksége van. A Standard szintű tárfiók a helyszíni adatokban bekövetkező folyamatos változásokat rögzítő replikációs naplókat tárolja.
**Azure-hálózat** | Szükség van egy [Azure-hálózatra](../virtual-network/virtual-network-get-started-vnet-subnet.md), amelyhez a feladatátvételt követően a Azure-beli virtuális gépek csatlakozni tudnak. Az Azure-hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
**Helyszíni VMM-kiszolgálók** | Egy vagy több, a System Center 2012 R2 vagy újabb verziójával futó VMM-kiszolgálóra van szüksége.<br/><br/> Minden VMM-kiszolgálón legalább egy magánfelhőnek kell üzemelnie. Mindegyik felhőben legalább egy gazdagépcsoportnak kell lennie.<br/><br/> A VMM-kiszolgálónak internetkapcsolatra van szüksége.
**Helyszíni Hyper-V** | A Hyper-V-kiszolgálókon legalább a Windows Server 2012 R2 verziónak kell futnia engedélyezett Hyper-V szerepkörrel vagy a Microsoft Hyper-V Server 2012 R2 verziónak. Telepíteni kell a legújabb frissítéseket.<br/><br/> A Hyper-V-gazdagépnek egy VMM-gazdagépcsoportban kell elhelyezkednie (egy VMM-felhőben).<br/><br/> A gazdagépeknek egy vagy több olyan virtuális géppel kell rendelkezniük, amelyet replikálni szeretne.<br/><br/> A Hyper-V-gazdagépeknek csatlakozniuk kell az internethez az Azure-ba közvetlenül vagy proxy-n keresztül történő replikációhoz. A Hyper-V kiszolgálóknak rendelkezniük kell a [2961977.](https://support.microsoft.com/kb/2961977) cikkben leírt javításokkal.
**Helyszíni Hyper-V rendszerű virtuális gépek** | A replikálni kívánt virtuális gépeknek [támogatott operációs rendszert](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) kell futtatniuk, valamint meg kell felelniük az [Azure-előfeltételeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). A virtuális gép neve a replikáció engedélyezése után módosítható. 




## <a name="next-steps"></a>Következő lépések

Folytassa a [3. lépés: Kapacitás megtervezése](vmm-to-azure-walkthrough-capacity.md) szakasszal.


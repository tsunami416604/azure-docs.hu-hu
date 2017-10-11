---
title: "Azure Storage használata az Azure Automation kezelése"
description: "Hogyan az Azure Automation szolgáltatás segítségével kezelheti az Azure Storage léptékű megismerése."
services: storage, automation
documentationcenter: 
author: jodoglevy
manager: eamono
editor: 
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="managing-azure-storage-using-azure-automation"></a>Azure Storage használata az Azure Automation kezelése
Az útmutatóból megismerheti az Azure Automation szolgáltatás, és hogyan használható egyszerűbbé teheti a Azure Storage-blobot, táblát és üzenetsort kezelését.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Azure Automation szolgáltatásbeli](https://azure.microsoft.com/services/automation/) egy Azure szolgáltatás felhő kezelésüket folyamatok automatizálása révén. Azure Automation használ, hosszan futó, manuális, hibákhoz vezethet, és gyakran ismétlődő feladatok automatizálható a megbízhatóság és a hatékonyság növelésére, és idejének csökkentése érdekében a szervezet értékre.

Azure Automation szolgáltatásbeli egy magas rendelkezésre állású és nagymértékben megbízható munkafolyamat-végrehajtási motorjának, amely az igényeinek, ha a szervezet növekedésének megfelelően méretezi biztosít. Az Azure Automationben folyamatok is lehet kezdődött el manuálisan, a 3. fél rendszerek vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan, ha szükséges.

Működési munkaterhelés csökkentése és az szabadítson fel informatikai / azáltal, hogy a felhő felügyeleti feladatok automatikusan Azure Automation által futtatandó value DevOps személyzet üzleti hozzáadó munkahelyi összpontosíthat.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Hogyan segíthet az Azure Automation kezelése az Azure Storage?
Az Azure Storage által biztosított PowerShell-parancsmagok használatával kezelhető az Azure Automationben [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure Automation a elérhető Storage PowerShell parancsmagok alapesetben rendelkezik, így a blob, table és várólista felügyeleti feladatok a szolgáltatáson belül végezheti el. Ezek a parancsmagok az Azure Automationben más Azure-szolgáltatások, Azure-szolgáltatások és a 3. fél rendszerek között összetett feladatok automatizálása a parancsmagjaival is párosítható.

A [Azure Automation-runbook gyűjtemény](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) termék csoportját, és a Közösség runbookok Ismerkedés az Azure Storage, más Azure-szolgáltatások és a 3. fél rendszerek felügyeletének automatizálására különböző tartalmaz. Gyűjteményelem forgatókönyvek a következők:

* [Távolítsa el az Azure Storage Blobs, amelyek az egyes napok régi használatával Automation szolgáltatás](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Az Azure Storage Blob letöltése](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Egy Azure virtuális vagy virtuális gépeinek egy felhőalapú szolgáltatás, biztonsági mentés minden lemez](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte az Azure Automation, és hogyan használható az Azure Storage blobot, táblát és üzenetsort kezelése alapjait, az alábbi hivatkozásokból tudhat meg többet az Azure Automation.

Tekintse meg az Azure Automation [létrehozása vagy importálása az Azure Automationben runbook](../../automation/automation-creating-importing-runbook.md).


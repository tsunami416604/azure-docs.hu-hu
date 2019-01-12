---
title: Kezelhető az Azure Storage, Azure Automation használatával
description: Ismerje meg, hogyan az Azure Automation szolgáltatás segítségével kezelhető az Azure Storage nagy mennyiségű információ.
services: storage, automation
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/10/2019
ms.author: tamram
ms.component: common
ms.openlocfilehash: 0cf8a1ee90757b9a89e7659282b2fd52fa436dea
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245072"
---
# <a name="managing-azure-storage-using-azure-automation"></a>Azure Automation használatával az Azure tárolás kezelése
Ez az útmutató vezet be, az Azure Automation szolgáltatást, és hogyan használható az Azure Storage-blobok, táblák és üzenetsorok kezelésének egyszerűsítéséhez.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Az Azure Automation](https://azure.microsoft.com/services/automation/) leegyszerűsíti a felhőfelügyelet a folyamatok automatizálása révén az Azure-szolgáltatások. Az Azure Automationnel, hosszan futó, manuális, sok hibalehetőséget rejtő és gyakran ismételt feladatok automatizálhatók a megbízhatóság és a hatékonyság növelése érdekében, és csökkenteni a piacra érték a szervezet számára.

Az Azure Automation segítségével egy megbízható és magas rendelkezésre állású munkafolyamat-végző összetevőjével, amely az igényeinek, ha a szervezet növekedésével skálázható. Az Azure Automation szolgáltatás folyamatok is lehet kezdődjön manuálisan, harmadik felek rendszereiben, vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan akkor, ha szükséges.

Csökkentheti üzemeltetési terheit és szabadítson fel informatikai fejlesztési és üzemeltetési csapatának munkára, így üzleti érték a felhőfelügyeleti feladatokat automatikusan Azure Automation által futtatandó való váltással.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Hogyan segít az Azure Automation kezelhető az Azure Storage?
Az Azure Storage a PowerShell-parancsmagok segítségével kezelhetők az Azure Automationben [Azure PowerShell-lel](https://msdn.microsoft.com/library/azure/jj156055.aspx). Az Azure Automation a ezek elérhető Storage PowerShell parancsmagjainak, beépített rendelkezik, így akkor is végrehajthatja a blob, table és queue felügyeleti feladatokat a szolgáltatáson belül. Ezek a parancsmagok az Azure Automation parancsmagjaival más Azure-szolgáltatások, az összetett feladatok automatizálása az Azure-szolgáltatások és a külső rendszerek közötti párosítása is történik.

A [Azure Automation forgatókönyv-katalógusában](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) termék csapatával és a Közösség forgatókönyveit automatizálásának kezelése az Azure Storage, más Azure-szolgáltatások és a külső rendszerek tartalmazza. A katalógus forgatókönyveinek a következők:

* [Távolítsa el az Azure Storage-Blobokkal, amelyek az egyes napok régi használatával Automation szolgáltatás](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Letölt egy blobot az Azure Storage-ból](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Biztonsági mentés az összes lemez, egyetlen Azure virtuális gép, vagy az összes virtuális gép egy Cloud Service-ben](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az Azure Automation, és hogyan használható kezelése az Azure Storage-blobok, táblák és üzenetsorok alapjait, az alábbi hivatkozásokból tudhat meg többet az Azure Automationben.

Tekintse meg az Azure Automation oktatóanyag [létrehozása vagy importálása az Azure Automation-runbook](../../automation/automation-creating-importing-runbook.md).


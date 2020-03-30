---
title: Konfigurációk létrehozása meglévő kiszolgálókról - Azure Automation
description: Ismerje meg, hogyan hozhat létre konfigurációkat a meglévő kiszolgálók az Azure Automation.
keywords: dsc,powershell,konfiguráció,beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d888c8fd3bf7cd44c37e7225618bd197f419d275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030122"
---
# <a name="create-configurations-from-existing-servers"></a>Konfigurációk létrehozása meglévő kiszolgálókról

> A következőre vonatkozik: Windows PowerShell 5.1

A konfigurációk meglévő kiszolgálókról történő létrehozása kihívást jelenthet.
Lehet, hogy nem akarja *az összes* beállítást, csak azokat, amelyek érdekel.
Még akkor is tudnia kell, hogy milyen sorrendben kell alkalmazni a beállításokat ahhoz, hogy a konfiguráció sikeresen alkalmazható legyen.

> [!NOTE]
> Ez a cikk a nyílt forráskódú közösség által fenntartott megoldásra hivatkozik.
> A támogatás csak GitHub-együttműködés formájában érhető el, a Microsofttól nem.

## <a name="community-project-reversedsc"></a>Közösségi projekt: ReverseDSC

A SharePoint indításához létrehozták a [ReverseDSC](https://github.com/microsoft/reversedsc) nevű, közösségi karbantartott megoldást.

A megoldás a [SharePointDSC erőforrásra](https://github.com/powershell/sharepointdsc) épül, és kiterjeszti azt a meglévő SharePoint-kiszolgálókról származó [információk összegyűjtésére.](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)
A legújabb verzió több [kinyerési móddal rendelkezik annak meghatározására,](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) hogy milyen szintű információkat tartalmazzon.

A megoldás használatának eredménye a SharePointDSC konfigurációs parancsfájlokkal használandó [konfigurációs adatok](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) létrehozása.

Az adatfájlok létrehozása után a [DSC konfigurációs parancsfájlokkal](/powershell/scripting/dsc/overview/overview) mof-fájlokat hozhat létre, és [feltöltheti a MOF-fájlokat az Azure Automation be.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Ezután regisztrálja a [kiszolgálókat a helyszíni](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) vagy az [Azure-ban](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a konfigurációk lekérése.

A ReverseDSC kipróbálásához látogasson el a [PowerShell-galériába,](https://www.powershellgallery.com/packages/ReverseDSC/) és töltse le a megoldást, vagy kattintson a "Projektwebhely" gombra a [dokumentáció](https://github.com/Microsoft/sharepointDSC.reverse)megtekintéséhez.

## <a name="next-steps"></a>További lépések

- [A Windows PowerShell kívánt állapotkonfigurációja – áttekintés](/powershell/scripting/dsc/overview/overview)
- [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)
- [A helyi konfigurációkezelő konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig)

---
title: Konfigurációk létrehozása meglévő kiszolgálókból – Azure Automation
description: Megtudhatja, hogyan hozhat létre konfigurációkat meglévő kiszolgálókról Azure Automation számára.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b8c39ba6c12d43da1b2311ae4d7d85dd13946f25
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559662"
---
# <a name="create-configurations-from-existing-servers"></a>Konfigurációk létrehozása meglévő kiszolgálókból

> A következőkre vonatkozik: Windows PowerShell 5,1

A meglévő kiszolgálók konfigurációinak létrehozása kihívást jelentő feladat lehet.
Előfordulhat, hogy nem szeretné, hogy *minden* beállítás, csak azokat, amelyeket érdekel.
Még akkor is tudnia kell, hogy milyen sorrendben kell alkalmazni a beállításokat ahhoz, hogy a konfiguráció sikeres legyen.

> [!NOTE]
> Ez a cikk egy olyan megoldásra utal, amelyet a nyílt forráskódú közösség tart fenn.
> A támogatás csak GitHub-együttműködés formájában érhető el, nem a Microsofttól.

## <a name="community-project-reversedsc"></a>Közösségi projekt: ReverseDSC

A [ReverseDSC](https://github.com/microsoft/reversedsc) nevű közösségi karbantartó megoldás azért jött létre, hogy ezen a helyen működjön a SharePoint megkezdése előtt.

A megoldás a [SharePointDSC erőforrásra](https://github.com/powershell/sharepointdsc) épül, és kibővíti a meglévő SharePoint-kiszolgálók összegyűjtési [adatainak](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) összekapcsolására.
A legújabb verzió több kibontási [móddal](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) rendelkezik, amelyekkel meghatározható, hogy milyen szintű információt tartalmazzon.

A megoldás használatával a SharePointDSC konfigurációs parancsfájljaival használandó [konfigurációs adatai](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) hozhatók létre.

Az adatfájlok létrejötte után a [DSC-konfigurációs parancsfájlok](/powershell/dsc/overview/overview) használatával MOF-fájlokat hozhat létre, és [feltöltheti a MOF-fájlokat](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)a Azure Automationba.
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) , akár [Az Azure-ban](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a konfigurációk lekéréséhez.

A ReverseDSC kipróbálásához látogasson el [](https://www.powershellgallery.com/packages/ReverseDSC/) a PowerShell-galériara, és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/Microsoft/sharepointDSC.reverse)megtekintéséhez.

## <a name="next-steps"></a>További lépések

- [A Windows PowerShell kívánt állapotának konfigurálása – áttekintés](/powershell/dsc/overview/overview)
- [DSC-erőforrások](/powershell/dsc/resources/resources)
- [A helyi Configuration Manager konfigurálása](/powershell/dsc/managing-nodes/metaconfig)

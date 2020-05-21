---
title: Konfigurációk konvertálása összetett erőforrásokra Azure Automation állapot-konfigurációhoz
description: Ez a cikk azt ismerteti, hogyan alakíthatja át a konfigurációkat összetett erőforrásokra Azure Automation állapot-konfigurációra.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 99fd7b2d0581b3eda97ec6fadeb43b55a3ad2bf0
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711329"
---
# <a name="convert-configurations-to-composite-resources-for-azure-automation-state-configuration"></a>Konfigurációk konvertálása összetett erőforrásokra Azure Automation állapot-konfigurációhoz

> A következőkre vonatkozik: Windows PowerShell 5,1

A konfigurációk létrehozási lépéseinek megkezdése után gyorsan létrehozhat olyan "forgatókönyveket", amelyek a beállítások csoportjait kezelik.
Ilyenek például a következők:

- webkiszolgáló létrehozása
- DNS-kiszolgáló létrehozása
- SharePoint-kiszolgáló létrehozása
- SQL-fürt konfigurálása
- tűzfalbeállítások kezelése
- jelszavas beállítások kezelése

Ha szeretné megosztani másokkal a munkát, a legjobb megoldás a konfiguráció [összetett erőforrásként](/powershell/scripting/dsc/resources/authoringresourcecomposite)való csomagolása.
Az összetett erőforrások első alkalommal történő létrehozása rendkívül nagy lehet.

> [!NOTE]
> Ez a cikk egy olyan megoldásra utal, amelyet a nyílt forráskódú közösség tart fenn.
> A támogatás csak GitHub-együttműködés formájában érhető el, nem a Microsofttól.

## <a name="community-project-compositeresource"></a>Közösségi projekt: CompositeResource

A probléma megoldásához létrehozott egy [CompositeResource](https://github.com/microsoft/compositeresource) nevű közösségi karbantartó megoldást.

A CompositeResource automatizálja a konfigurációban egy új modul létrehozásának folyamatát.
Első lépésként a (z) (vagy Build kiszolgáló) konfigurációs parancsfájl [beszerzése](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) a munkaállomáson, hogy be legyen töltve a memóriában.
Ezután ahelyett, hogy a konfigurációt futtatja egy MOF-fájl létrehozásához, használja a CompositeResource modul által biztosított funkciót az átalakítás automatizálásához.
A parancsmag betölti a konfiguráció tartalmát, lekéri a paraméterek listáját, és létrehoz egy új modult, amire szüksége van.

Miután létrehozta a modult, megnövelheti a verziót, és hozzáadhat kibocsátási megjegyzéseket minden alkalommal, amikor módosításokat végez, és közzéteszi azt a saját [PowerShellGet-adattárában](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Miután létrehozta a konfigurációját (vagy több konfigurációját) tartalmazó összetett erőforrás-modult, használhatja azokat az Azure-ban a készíthető [authoring Experience](/azure/automation/compose-configurationwithcompositeresources) -ben, vagy hozzáadhatja a [DSC konfigurációs PARANCSFÁJLokhoz](/powershell/scripting/dsc/configurations/configurations) a MOF-fájlok LÉTREHOZÁSához és [a MOF-fájlok Azure Automationba való feltöltéséhez](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) , akár [Az Azure-ban](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) a konfigurációk lekéréséhez.
A projekt legújabb frissítése [runbookok](https://www.powershellgallery.com/packages?q=DscGallerySamples) is közzétett a Azure Automation számára, hogy automatizálja a konfigurációk importálásának folyamatát a PowerShell-galériaból.

A DSC-hez készült összetett erőforrások létrehozásának automatizálásához látogasson el a [PowerShell-galériare](https://www.powershellgallery.com/packages/compositeresource/) , és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/microsoft/compositeresource)megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

- [A Windows PowerShell kívánt állapotának konfigurálása – áttekintés](/powershell/scripting/dsc/overview/overview)
- [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)
- [A helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig)

---
title: Konfigurációk konvertálása összetett erőforrásokká állapotkonfigurációhoz – Azure Automation
description: Ismerje meg, hogyan konvertálhatja a konfigurációkat összetett erőforrásokká az Azure Automationben.
keywords: dsc,powershell,konfiguráció,beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 05f0a81a738688df15ea9060071d9e266b54b7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136655"
---
# <a name="convert-configurations-to-composite-resources"></a>Konfigurációk átalakítása összetett erőforrásokká

> A következőre vonatkozik: Windows PowerShell 5.1

A konfigurációk létrehozásának megkezdését követően gyorsan létrehozhat "forgatókönyveket", amelyek kezelik a beállításcsoportokat.
Példák a következők:

- webkiszolgáló létrehozása
- DNS-kiszolgáló létrehozása
- SharePoint-kiszolgáló létrehozása
- SQL-fürt konfigurálása
- tűzfalbeállítások kezelése
- jelszóbeállítások kezelése

Ha szeretné megosztani ezt a munkát másokkal, a legjobb megoldás az, hogy a csomag a konfiguráció, mint egy [összetett erőforrás](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Az összetett erőforrások első létrehozása elsöprő lehet.

> [!NOTE]
> Ez a cikk a nyílt forráskódú közösség által fenntartott megoldásra hivatkozik.
> A támogatás csak GitHub-együttműködés formájában érhető el, a Microsofttól nem.

## <a name="community-project-compositeresource"></a>Közösségi projekt: CompositeResource

A kihívás megoldásához létrejött egy [CompositeResource](https://github.com/microsoft/compositeresource) nevű, közösségi karbantartott megoldás.

A CompositeResource automatizálja az új modul létrehozásának folyamatát a konfigurációból.
Először [pont szerzi be](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) a konfigurációs parancsfájlt a munkaállomáson (vagy a buildkiszolgálón), így az betöltődik a memóriába.
Ezután ahelyett, hogy a konfigurációt futtatná egy MOF-fájl létrehozásához, használja a CompositeResource modul által biztosított függvényt a konvertálás automatizálásához.
A parancsmag betölti a konfiguráció tartalmát, megkapja a paraméterek listáját, és létrehoz egy új modult, amire szüksége van.

Miután létrehozott egy modult, bővítheti a verziót, és kiadási megjegyzéseket adhat hozzá minden alkalommal, amikor módosításokat hajt végre, és közzéteszi azt a saját [PowerShellGet tárházban.](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)

Miután létrehozott egy összetett erőforrásmodult, amely tartalmazza a konfigurációt (vagy több konfigurációt), használhatja őket az [Azure-ban a Composable Authoring](/azure/automation/compose-configurationwithcompositeresources) Experience-ben, vagy hozzáadhatja őket a [DSC konfigurációs parancsfájljaihoz](/powershell/scripting/dsc/configurations/configurations) a MOF-fájlok létrehozásához és [a MOF-fájlok feltöltéséhez az Azure Automationbe.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Ezután regisztrálja a [kiszolgálókat a helyszíni](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) vagy az [Azure-ban](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a konfigurációk lekérése.
A projekt legújabb frissítése az Azure Automation [számára runbookokat](https://www.powershellgallery.com/packages?q=DscGallerySamples) is közzétett a konfigurációk PowerShell-galériából történő importálásának automatizálásához.

A DSC összetett erőforrásailétrehozásának automatizálásához látogasson el a [PowerShell-galériába,](https://www.powershellgallery.com/packages/compositeresource/) és töltse le a megoldást, vagy kattintson a "Projektwebhely" gombra a [dokumentáció](https://github.com/microsoft/compositeresource)megtekintéséhez.

## <a name="next-steps"></a>További lépések

- [A Windows PowerShell kívánt állapotkonfigurációja – áttekintés](/powershell/scripting/dsc/overview/overview)
- [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)
- [A helyi konfigurációkezelő konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig)

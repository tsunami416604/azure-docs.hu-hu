---
title: A STIG-en alapuló konfiguráció az állapotkonfigurációban való használatra - Azure Automation
description: Ismerje meg a STIG-alapú konfigurációk at Azure Automation.
keywords: dsc,powershell,konfiguráció,beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028274"
---
# <a name="configuration-based-on-stig"></a>STIG-alapú konfiguráció

> A következőre vonatkozik: Windows PowerShell 5.1

A konfigurációs tartalom első alkalommal való létrehozása kihívást jelenthet.
Sok esetben a cél a kiszolgálók konfigurációjának automatizálása egy olyan "alapkonfiguráció" után, amely remélhetőleg egy iparági ajánláshoz igazodik.

> [!NOTE]
> Ez a cikk a nyílt forráskódú közösség által fenntartott megoldásra hivatkozik.
> A támogatás csak GitHub-együttműködés formájában érhető el, a Microsofttól nem.

## <a name="community-project-powerstig"></a>Közösségi projekt: PowerSTIG

A [PowerSTIG](https://github.com/microsoft/powerstig) nevű közösségi projekt célja, hogy ezt a problémát a STIG-ről (Security Technical Implementation Guide) kapott [nyilvános információk](https://public.cyber.mil/stigs/) on alapuló DSC-tartalom létrehozásával oldja meg,

Kezelése alapvonalak bonyolultabb, mint amilyennek hangzik.
Számos szervezetnek dokumentálnia kell a szabályok [alóli kivételeket,](https://github.com/microsoft/powerstig#powerstigdata) és azadatokat nagy méretekben kell kezelnie.
A PowerSTIG úgy oldja meg a problémát, hogy [összetett erőforrásokat](https://github.com/microsoft/powerstig#powerstigdsc) biztosít a konfiguráció egyes területének kezeléséhez, ahelyett, hogy egy nagy fájlban próbálna meg foglalkozni a beállítások teljes tartományával.

A konfigurációk létrehozása után a [DSC konfigurációs parancsfájljai](/powershell/scripting/dsc/configurations/configurations) segítségével mof-fájlokat hozhat létre, és [feltöltheti a MOF-fájlokat az Azure Automationbe.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Ezután regisztrálja a [kiszolgálókat a helyszíni](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) vagy az [Azure-ban](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a konfigurációk lekérése.

A PowerSTIG kipróbálásához látogasson el a [PowerShell-galériába,](https://www.powershellgallery.com) és töltse le a megoldást, vagy kattintson a "Projektwebhely" gombra a [dokumentáció](https://github.com/microsoft/powerstig)megtekintéséhez.

## <a name="next-steps"></a>További lépések

- [A Windows PowerShell kívánt állapotkonfigurációja – áttekintés](/powershell/scripting/dsc/overview/overview)
- [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)
- [A helyi konfigurációkezelő konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig)

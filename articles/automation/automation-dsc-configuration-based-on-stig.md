---
title: A STIG-alapú konfiguráció az állapot-konfigurációban való használatra – Azure Automation
description: Tudnivalók a Azure Automation-beli állapot-konfigurációhoz tartozó STIG-konfigurációkról.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028274"
---
# <a name="configuration-based-on-stig"></a>STIG-alapú konfiguráció

> A következőkre vonatkozik: Windows PowerShell 5,1

A konfigurációs tartalom első alkalommal történő létrehozása kihívást jelenthet.
Sok esetben a cél az, hogy automatizálja a kiszolgálók konfigurációját az "alapterv" után, amely remélhetőleg egy iparági javaslathoz igazodik.

> [!NOTE]
> Ez a cikk egy olyan megoldásra utal, amelyet a nyílt forráskódú közösség tart fenn.
> A támogatás csak GitHub-együttműködés formájában érhető el, nem a Microsofttól.

## <a name="community-project-powerstig"></a>Közösségi projekt: PowerSTIG

A [PowerSTIG](https://github.com/microsoft/powerstig) nevű közösségi projekt célja, hogy a probléma megoldásához a Stig (biztonsági technikai megvalósítási útmutató) által biztosított [nyilvános információk](https://public.cyber.mil/stigs/) alapján DSC-tartalmat generáljon.

Az alapkonfigurációk kezelése bonyolultabb, mint amilyennek hangzik.
Számos szervezetnek [dokumentálnia kell a kivételeket](https://github.com/microsoft/powerstig#powerstigdata) a szabályokhoz, és az adatmennyiséget nagy mértékben kell kezelnie.
A PowerSTIG úgy kezeli a problémát, hogy [összetett erőforrásokat](https://github.com/microsoft/powerstig#powerstigdsc) biztosít a konfiguráció egyes területeinek kezeléséhez ahelyett, hogy egyetlen nagyméretű fájl összes beállítását próbálja meg kezelni.

A konfigurációk létrehozása után a [DSC-konfigurációs parancsfájlok](/powershell/scripting/dsc/configurations/configurations) segítségével létrehozhat MOF-fájlokat, és [feltöltheti a MOF-fájlokat a Azure Automationba](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) , akár [Az Azure-ban](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a konfigurációk lekéréséhez.

A PowerSTIG kipróbálásához látogasson el a [PowerShell-galériara](https://www.powershellgallery.com) , és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/microsoft/powerstig)megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

- [A Windows PowerShell kívánt állapotának konfigurálása – áttekintés](/powershell/scripting/dsc/overview/overview)
- [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)
- [A helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig)

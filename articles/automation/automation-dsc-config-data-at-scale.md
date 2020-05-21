---
title: Az Azure Automation állapot konfigurációjának konfigurálása a skálán
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az adatmennyiséget Azure Automation állapot konfigurációjában.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5f6773d9f2210fd1b0342c9586d89155082bf8ff
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711448"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Az Azure Automation állapot konfigurációjának konfigurálása a skálán

> A következőkre vonatkozik: Windows PowerShell 5,1

Több száz vagy több ezer kiszolgáló kezelése kihívást jelenthet.
Az ügyfelek visszajelzéseket adtak arról, hogy a legnehezebb szempont a [konfigurációs információk](/powershell/scripting/dsc/configurations/configdata)tényleges kezelése.
Információk rendszerezése logikai szerkezetek között, például hely, típus és környezet.

> [!NOTE]
> Ez a cikk egy olyan megoldásra utal, amelyet a nyílt forráskódú közösség tart fenn.
> A támogatás csak GitHub-együttműködés formájában érhető el, nem a Microsofttól.

## <a name="community-project-datum"></a>Közösségi projekt: Datum

A probléma megoldásához egy [Datum](https://github.com/gaelcolas/Datum) nevű közösségi karbantartó megoldás lett létrehozva.
Az Datum nagyszerű ötleteket kínál más konfigurációs felügyeleti platformoktól, és ugyanazt a megoldást alkalmazza a PowerShell DSC-hez.
Az adatokat logikai ötletek alapján [rendezi a rendszer szöveges fájlokba](https://github.com/gaelcolas/Datum#3-intended-usage) .
Ilyenek például a következők:

- Globálisan alkalmazandó beállítások
- A hely összes kiszolgálójára érvényes beállítások
- Az összes adatbázis-kiszolgálóra alkalmazandó beállítások
- Egyedi kiszolgálóbeállítások

Ezeket az információkat a kívánt fájlformátumban (JSON, YAML vagy PSD1) rendezi a rendszer.
Ezután a rendszer parancsmagokat biztosít a konfigurációs adatfájlok létrehozásához azáltal, hogy az egyes fájlokból származó [információkat összevonja](https://github.com/gaelcolas/Datum#datum-tree) egy kiszolgáló vagy kiszolgálói szerepkör egyetlen nézetében.

Az adatfájlok létrejötte után a [DSC-konfigurációs parancsfájlok](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) használatával MOF-fájlokat hozhat létre, és [feltöltheti a MOF-fájlokat a Azure Automationba](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) , akár [Az Azure-ban](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) a konfigurációk lekéréséhez.

Az Datum kipróbálásához látogasson el a [PowerShell-galériara](https://www.powershellgallery.com/packages/datum/) , és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/gaelcolas/Datum#2-getting-started--concepts)megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

- [A Windows PowerShell kívánt állapotának konfigurálása – áttekintés](/powershell/scripting/dsc/overview/overview)
- [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)
- [A helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig)

---
title: Konfigurációs adatmennyiség – Azure Automation
description: Megtudhatja, hogyan konfigurálhatja az adatmennyiséget a Azure Automation állapotának konfigurálásához.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585575"
---
# <a name="configuration-data-at-scale"></a>Konfigurációs adatok nagy mennyiségben

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

## <a name="next-steps"></a>További lépések

- [A Windows PowerShell kívánt állapotának konfigurálása – áttekintés](/powershell/scripting/dsc/overview/overview)
- [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)
- [A helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig)

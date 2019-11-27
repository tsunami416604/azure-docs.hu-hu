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
ms.openlocfilehash: 3e742f18e86c22b2d798eec5f6b715dfb298670a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231707"
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
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) , akár [Az Azure-ban](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a konfigurációk lekéréséhez.

Az Datum kipróbálásához látogasson el a [PowerShell-galériara](https://www.powershellgallery.com/packages/datum/) , és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/gaelcolas/Datum#2-getting-started--concepts)megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

- [A Windows PowerShell kívánt állapotának konfigurálása – áttekintés](/powershell/scripting/dsc/overview/overview)
- [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)
- [A helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig)

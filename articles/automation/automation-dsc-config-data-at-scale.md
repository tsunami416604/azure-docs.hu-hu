---
title: Konfigurációs adatok nagy méretekben - Azure Automation
description: Ismerje meg, hogyan konfigurálhatja az adatokat az Azure Automation állapotkonfigurációjához.
keywords: dsc,powershell,konfiguráció,beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a77a366ee8e06b2d8c47eb3b47eeaf9ae809598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028287"
---
# <a name="configuration-data-at-scale"></a>Konfigurációs adatok nagy mennyiségben

> A következőre vonatkozik: Windows PowerShell 5.1

Több száz vagy több ezer szerver kezelése kihívást jelent.
Az ügyfelek visszajelzést adtak arról, hogy a legnehezebb szempont a [konfigurációs adatok](/powershell/scripting/dsc/configurations/configdata)kezelése.
Az információk rendszerezése logikai konstrukciókban, például a hely, a típus és a környezet között.

> [!NOTE]
> Ez a cikk a nyílt forráskódú közösség által fenntartott megoldásra hivatkozik.
> A támogatás csak GitHub-együttműködés formájában érhető el, a Microsofttól nem.

## <a name="community-project-datum"></a>Közösségi projekt: Datum

A kihívás megoldására létrehozták a [Datum](https://github.com/gaelcolas/Datum) nevű, közösségi encikbe tanévben álló megoldást.
A Datum más konfigurációkezelő platformok nagyszerű ötleteire épül, és ugyanolyan típusú megoldást valósít meg a PowerShell DSC-hez.
Az információk logikai ötletek alapján [vannak rendszerezve a szöveges fájlokba.](https://github.com/gaelcolas/Datum#3-intended-usage)
Példák a következők:

- Globálisan alkalmazandó beállítások
- A hely összes kiszolgálójára alkalmazandó beállítások
- Az összes adatbázis-kiszolgálóra érvényes beállítások
- Egyéni kiszolgálóbeállítások

Ez az információ a kívánt fájlformátumban (JSON, Yaml vagy PSD1) van rendezve.
Ezután parancsmagokkal hozlétre a konfigurációs adatfájlokat az egyes fájlokból származó információk nak a kiszolgáló vagy kiszolgálói szerepkör egyetlen nézetébe [történő összevonásával.](https://github.com/gaelcolas/Datum#datum-tree)

Az adatfájlok létrehozása után a [DSC konfigurációs parancsfájlokkal](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) mof-fájlokat hozhat létre, és [feltöltheti a MOF-fájlokat az Azure Automation be.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Ezután regisztrálja a [kiszolgálókat a helyszíni](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) vagy az [Azure-ban](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a konfigurációk lekérése.

A Datum kipróbálásához látogasson el a [PowerShell-galériába,](https://www.powershellgallery.com/packages/datum/) és töltse le a megoldást, vagy kattintson a "Projektwebhely" gombra a [dokumentáció](https://github.com/gaelcolas/Datum#2-getting-started--concepts)megtekintéséhez.

## <a name="next-steps"></a>További lépések

- [A Windows PowerShell kívánt állapotkonfigurációja – áttekintés](/powershell/scripting/dsc/overview/overview)
- [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)
- [A helyi konfigurációkezelő konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig)

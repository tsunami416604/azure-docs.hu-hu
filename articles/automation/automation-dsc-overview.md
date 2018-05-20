---
title: Azure Automation DSC – áttekintés
description: Egy áttekintés az Azure Automation szükséges konfiguráló (DSC), a feltételek és ismert problémák
keywords: PowerShell dsc, a kívánt állapot konfigurációs, a powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 83d5b89422a0181c06dbfe3b2bd8975ef7214b9d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="azure-automation-dsc-overview"></a>Azure Automation DSC – áttekintés

Az Azure Automation DSC az Azure-szolgáltatások, amely lehetővé teszi írási, kezelése és PowerShell kívánt állapot konfigurációs szolgáltatása (DSC) fordítási [konfigurációk](https://msdn.microsoft.com/powershell/dsc/configurations), importálása [DSC erőforrások](https://msdn.microsoft.com/powershell/dsc/resources), és rendelje hozzá a konfigurációk a célcsomópontokat, mind a felhőben.

## <a name="why-use-azure-automation-dsc"></a>Miért érdemes használni az Azure Automation DSC

Az Azure Automation DSC számos előnye van, mint az Azure-on kívüli DSC.

### <a name="built-in-pull-server"></a>Beépített lekérési kiszolgálójával

Azure Automation szolgáltatásbeli biztosít egy [DSC lekérési kiszolgálójával](https://msdn.microsoft.com/powershell/dsc/pullserver) , hogy a célcsomópontokat automatikusan megjelenik a konfigurációk, felelnek meg a kívánt állapot, és megfelelőségi jelentéseket küldhetnek vissza.
A beépített lekérési kiszolgálójával, az Azure Automationben szükségtelenné teszi beállítása és karbantartása a saját lekérési kiszolgálójával.
Azure Automation szolgáltatásbeli virtuális vagy fizikai Windows vagy Linux rendszerű gépek, a felhőben, vagy a helyszíni célba.

### <a name="management-of-all-your-dsc-artifacts"></a>A DSC-összetevők felügyelete

Az Azure Automation DSC biztosítható az ugyanabban a felügyeleti réteg [PowerShell célállapot-konfiguráció](https://msdn.microsoft.com/powershell/dsc/overview) , az Azure Automation kínál a PowerShell-parancsprogramok.

Azure-portálról, vagy a PowerShell kezelheti az összes a DSC konfigurációk, erőforrások és célcsomópontokat.

![Az Azure Automation panel képernyőfelvétele](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Jelentési adatok importálása a Naplóelemzési

Az Azure Automation DSC Szolgáltatásban felügyelt csomópontok részletes jelentéskészítési állapot adatokat küldeni a a beépített lekérési kiszolgálójával.
Azure Automation DSC ezeket az adatokat küldeni a Naplóelemzési munkaterület konfigurálhatja.
A DSC állapot adatokat küldeni a Naplóelemzési munkaterület, lásd: [előre Azure Automation DSC jelentésadatait szolgáltatáshoz](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Bevezető videó

Inkább néz videót olvasás helyett? Rá egy pillantást az alábbi videó az 2015. május, amikor bejelentette először Azure Automation DSC Szolgáltatásban.

>[!NOTE]
>A fogalmakat, és ez a videó tárgyalt életciklusa helyesek, amíg a Azure Automation DSC sokkal fejlődött, mivel ez a videó lett felvéve.
>Az általánosan elérhető van jóval szélesebb körű felhasználói Felületet az Azure portálon, és számos további képességeket biztosít.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan előkészítésére csomópontokat a felügyelethez az Azure Automation DSC Szolgáltatásban, lásd: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md)
* Azure Automation DSC használatának megkezdéséhez tekintse meg [Ismerkedés az Azure Automation DSC](automation-dsc-getting-started.md)
* A DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat, lásd: [fordítása Azure Automation DSC-konfigurációja](automation-dsc-compile.md)
* PowerShell parancsmag-referencia az Azure Automation DSC Szolgáltatásban, lásd: [Azure Automation DSC-parancsmagok](/powershell/module/azurerm.automation/#automation)
* Díjszabási információkért lásd: [Azure Automation DSC díjszabása](https://azure.microsoft.com/pricing/details/automation/)
* Egy Azure Automation DSC használata a folyamatos üzembe helyezési folyamat példát, olvassa el [IaaS virtuális gépek használata Azure Automation DSC és Chocolatey folyamatos üzembe helyezés](automation-dsc-cd-chocolatey.md)

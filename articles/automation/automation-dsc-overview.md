---
title: "Azure Automation DSC – áttekintés |} Microsoft Docs"
description: "Egy áttekintés az Azure Automation szükséges konfiguráló (DSC), a feltételek és ismert problémák"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "PowerShell dsc, a kívánt állapot konfigurációs, a powershell dsc azure"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: 468321fa6863d78bc0d179fbe5c2ed6195040d50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-dsc-overview"></a>Azure Automation DSC – áttekintés

Az Azure Automation DSC az Azure-szolgáltatások, amely lehetővé teszi írási, kezelése és PowerShell kívánt állapot konfigurációs szolgáltatása (DSC) fordítási [konfigurációk](https://msdn.microsoft.com/powershell/dsc/configurations), importálása [DSC erőforrások](https://msdn.microsoft.com/powershell/dsc/resources), és rendelje hozzá a konfigurációk a célcsomópontokat, mind a felhőben.

## <a name="why-use-azure-automation-dsc"></a>Miért érdemes használni az Azure Automation DSC

Az Azure Automation DSC számos előnye van, mint az Azure-on kívüli DSC.

### <a name="built-in-pull-server"></a>Beépített lekérési kiszolgálójával

Azure Automation szolgáltatásbeli biztosít egy [DSC lekérési kiszolgálójával](https://msdn.microsoft.com/en-us/powershell/dsc/pullserver) , hogy a célcsomópontokat automatikusan megjelenik a konfigurációk, felelnek meg a kívánt állapot, és megfelelőségi jelentéseket küldhetnek vissza.
A beépített lekérési kiszolgálójával, az Azure Automationben szükségtelenné teszi beállítása és karbantartása a saját lekérési kiszolgálójával.
Azure Automation szolgáltatásbeli virtuális vagy fizikai Windows vagy Linux rendszerű gépek, a felhőben, vagy a helyszíni célba.

### <a name="management-of-all-your-dsc-artifacts"></a>A DSC-összetevők felügyelete

Az Azure Automation DSC biztosítható az ugyanabban a felügyeleti réteg [PowerShell célállapot-konfiguráció](https://msdn.microsoft.com/powershell/dsc/overview) , az Azure Automation kínál a PowerShell-parancsprogramok.

Azure-portálról, vagy a PowerShell kezelheti az összes a DSC konfigurációk, erőforrások és célcsomópontokat.

![Az Azure Automation panel képernyőfelvétele](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Jelentési adatok importálása a Naplóelemzési

Az Azure Automation DSC Szolgáltatásban felügyelt csomópontok részletes jelentéskészítési állapot adatokat küldeni a a beépített lekérési kiszolgálójával.
Azure Automation DSC ezeket az adatokat küldeni a Microsoft Operations Management Suite (OMS) Naplóelemzési munkaterület konfigurálhatja.
A DSC állapot adatokat küldeni a Naplóelemzési munkaterület, lásd: [előre Azure Automation DSC jelentésadatait OMS szolgáltatáshoz](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Bevezető videó

Inkább néz videót olvasás helyett? Rá egy pillantást az alábbi videó az 2015. május, amikor bejelentette először Azure Automation DSC Szolgáltatásban.

>[!NOTE]
>A fogalmakat, és ez a videó tárgyalt életciklusa helyesek, amíg a Azure Automation DSC sokkal fejlődött, mivel ez a videó lett felvéve.
>Az általánosan elérhető van jóval szélesebb körű felhasználói Felületet az Azure portálon, és számos további képességeket biztosít.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan előkészítésére csomópontokat a felügyelethez az Azure Automation DSC Szolgáltatásban, lásd: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md)
* Azure Automation DSC használatának megkezdéséhez tekintse meg [Ismerkedés az Azure Automation DSC](automation-dsc-getting-started.md)
* A DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat, lásd: [fordítása Azure Automation DSC-konfigurációja](automation-dsc-compile.md)
* PowerShell parancsmag-referencia az Azure Automation DSC Szolgáltatásban, lásd: [Azure Automation DSC-parancsmagok](/powershell/module/azurerm.automation/#automation)
* Díjszabási információkért lásd: [Azure Automation DSC díjszabása](https://azure.microsoft.com/pricing/details/automation/)
* Egy Azure Automation DSC használata a folyamatos üzembe helyezési folyamat példát, olvassa el [IaaS virtuális gépek használata Azure Automation DSC és Chocolatey folyamatos üzembe helyezés](automation-dsc-cd-chocolatey.md)
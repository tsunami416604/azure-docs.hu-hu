---
title: Az Azure Automation DSC – áttekintés
description: Az Áttekintés az Azure Automation Desired State Configuration (DSC), a használati és ismert problémák
keywords: PowerShell dsc, a kívánt állapot konfigurációs, a powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f6d49ffa59ed53c0a1966a4132fd5fe1689a13ce
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247349"
---
# <a name="azure-automation-dsc-overview"></a>Az Azure Automation DSC – áttekintés

Az Azure Automation DSC egy Azure-szolgáltatás, amely lehetővé teszi, hogy írási, kezelheti és fordítsa le a PowerShell Desired State Configuration (DSC) [konfigurációk](https://msdn.microsoft.com/powershell/dsc/configurations), importálása [DSC-erőforrások](https://msdn.microsoft.com/powershell/dsc/resources), és rendelje hozzá a konfigurációk a célcsomópontokat, mindezt a felhőben.

## <a name="why-use-azure-automation-dsc"></a>Miért érdemes használni az Azure Automation DSC

Az Azure Automation DSC több előnye is van, mint az Azure-on kívül DSC biztosít.

### <a name="built-in-pull-server"></a>Beépített lekéréses kiszolgálón

Az Azure Automation DSC lekéréses kiszolgálón hasonló biztosít a [Windows szolgáltatás DSC-szolgáltatás](/powershell/dsc/pullserver) úgy, hogy a célcsomópontokat automatikusan kap konfigurációkat, a kívánt állapotban megfelel, és a megfelelőségről szóló jelentést.
Az Azure Automationben a beépített lekéréses kiszolgálón szükségtelenné teszi a saját lekérési kiszolgálóval termékeknél.
Az Azure Automation virtuális vagy fizikai Windows vagy Linux rendszerű gépek, a felhőben vagy helyszíni célba.

### <a name="management-of-all-your-dsc-artifacts"></a>A DSC-összetevők kezelése

Az Azure Automation DSC ugyanolyan felügyeleti réteget biztosít [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) , az Azure Automation kínál a PowerShell-parancsprogramok.

Az Azure Portalról, vagy a Powershellből kezelheti az összes a DSC konfigurációk, erőforrások és célcsomópontokat.

![Képernyőfelvétel az Azure Automation panelről](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Jelentési adatok importálása a Log Analytics

Felügyelt Azure Automation DSC-csomópontok részletes jelentési adatokat küldeni a beépített lekéréses kiszolgálón.
Konfigurálhatja az Azure Automation DSC ezeket az adatokat küldhet a Log Analytics-munkaterületre.
DSC-állapot adatainak küldése a Log Analytics-munkaterületre kapcsolatban lásd: [továbbítása Azure Automation DSC jelentési adatok Log Analytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Bevezető videó

Inkább néz videót olvasás helyett? Tekintse meg a következő videó a 2015 május, amikor először bejelentettük az Azure Automation DSC rendelkezik.

>[!NOTE]
>A fogalmakat és a videóban tárgyalt életciklusának megfelelő, amelyek a az Azure Automation DSC sokat fejlődött a videó rögzítése óta.
>Azt már általánosan elérhető, van egy sokkal felhasználói felülete az Azure Portalon, és számos további képességeket is támogat.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan kell felügyelni az Azure Automation DSC előkészítése csomópontokhoz meg [gépek előkészítése kezelésre, amelyet az Azure Automation DSC](automation-dsc-onboarding.md)
* Ismerkedés az Azure Automation DSC használatával, lásd: [Azure Automation DSC – első lépések](automation-dsc-getting-started.md)
* DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat kapcsolatos további információkért lásd: [összeállítása az Azure Automation DSC-konfigurációja](automation-dsc-compile.md)
* PowerShell-parancsmag referencia az Azure Automation DSC, lásd: [Azure Automation DSC-parancsmagok](/powershell/module/azurerm.automation/#automation)
* Díjszabási információkért tekintse meg a [Azure Automation DSC-díjszabás](https://azure.microsoft.com/pricing/details/automation/)
* Azure Automation DSC folyamatos üzembe helyezés folyamatban lévő példa megtekintéséhez lásd: [folyamatos üzembe helyezés a chocolatey-t és az IaaS virtuális gépek használata az Azure Automation DSC](automation-dsc-cd-chocolatey.md)

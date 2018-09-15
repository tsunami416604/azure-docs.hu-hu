---
title: Azure Automation konfiguráló áttekintése
description: Egy áttekintése az Azure Automation állapot Configuration (DSC), a használati és ismert problémák
keywords: PowerShell dsc, a kívánt állapot konfigurációs, a powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ef55e6ca6fc913710bae68a7423369b33f26c009
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45628998"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation konfiguráló áttekintése

Azure Automation konfigurációs egy Azure-szolgáltatás, amely lehetővé teszi, hogy írási, kezelheti és fordítsa le a PowerShell Desired State Configuration (DSC) [konfigurációk](/powershell/dsc/configurations), importálása [DSC-erőforrások](/powershell/dsc/resources), és rendelje hozzá konfigurációk célcsomópontokat, mindezt a felhőben.

## <a name="why-use-azure-automation-state-configuration"></a>Miért érdemes használni az Azure Automation Állapotkonfiguráció

Azure Automation állapota a konfiguráció számos előnnyel jár, mint az Azure-on kívül DSC biztosít.

### <a name="built-in-pull-server"></a>Beépített lekéréses kiszolgálón

Azure Automation Állapotkonfiguráció egy hasonló DSC lekéréses kiszolgálót biztosít a [Windows szolgáltatás DSC-szolgáltatás](/powershell/dsc/pullserver) úgy, hogy a célcsomópontokat automatikusan kap konfigurációkat, felelnek meg a kívánt állapotra, és jelentéseket küldhetnek vissza saját megfelelőségi. Az Azure Automationben a beépített lekéréses kiszolgálón szükségtelenné teszi a saját lekérési kiszolgálóval termékeknél. Az Azure Automation virtuális vagy fizikai Windows vagy Linux rendszerű gépek, a felhőben vagy helyszíni célba.

### <a name="management-of-all-your-dsc-artifacts"></a>A DSC-összetevők kezelése

Azure Automation Állapotkonfiguráció ugyanolyan felügyeleti réteget biztosít [PowerShell Desired State Configuration](/powershell/dsc/overview) , az Azure Automation kínál a PowerShell-parancsprogramok.

Az Azure Portalról, vagy a Powershellből kezelheti az összes a DSC konfigurációk, erőforrások és célcsomópontokat.

![Képernyőfelvétel az Azure Automation panelről](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Jelentési adatok importálása a Log Analytics

Az Azure Automation Állapotkonfiguráció felügyelt csomópontok részletes jelentési állapot adatokat küldeni a a beépített lekéréses kiszolgálón. Azure Automation konfigurációs adatokat lehet küldeni a Log Analytics-munkaterület is beállíthatja. Konfigurációs állapot adatokat küldeni a Log Analytics-munkaterület kezelésével kapcsolatos információkért lásd: [továbbítása Azure Automation Állapotkonfiguráció Log nalytics adatokról szóló jelentéseket](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Bevezető videó

Inkább néz videót olvasás helyett? Tekintse meg a következő videó a 2015 május, amikor először bejelentettük az Azure Automation konfigurációs van.

> [!NOTE]
> A fogalmakat és a videóban tárgyalt életciklusának helyesek, amíg a konfiguráció az Azure Automation sokat fejlődött a videó rögzítése óta. Azt már általánosan elérhető, van egy sokkal felhasználói felülete az Azure Portalon, és számos további képességeket is támogat.

[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>További lépések

- Első lépésként lásd [Ismerkedés az Azure Automation Állapotkonfiguráció](automation-dsc-getting-started.md)
- Megtudhatja, hogyan előkészítheti a csomópontokat, hogy meg [gépek előkészítése kezelésre, az Azure Automation állapot konfigurációja](automation-dsc-onboarding.md)
- DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat kapcsolatos további információkért lásd: [összeállítása az Azure Automation Állapotkonfiguráció konfigurációk](automation-dsc-compile.md)
- PowerShell-parancsmagok leírása, lásd: [konfiguráló Azure Automation-parancsmagok](/powershell/module/azurerm.automation/#automation)
- Díjszabási információkért tekintse meg a [Azure Automation State Configuration díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Folyamatos üzembe helyezés folyamatban lévő Azure Automation Állapotkonfiguráció használatának példájáért lásd: [folyamatos üzembe helyezés használatával az Azure Automation Állapotkonfiguráció és a chocolatey-t](automation-dsc-cd-chocolatey.md)
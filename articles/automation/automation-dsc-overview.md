---
title: Azure Automation konfiguráló áttekintése
description: Egy áttekintése az Azure Automation állapot Configuration (DSC), a használati és ismert problémák
keywords: PowerShell dsc, a kívánt állapot konfigurációs, a powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ca7a1913e94242af46e777be308ef92fc5a5abb3
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427066"
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

![Az Azure Automation oldalát bemutató képernyőkép](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Jelentési adatok importálása a Log Analytics

Az Azure Automation Állapotkonfiguráció felügyelt csomópontok részletes jelentési állapot adatokat küldeni a a beépített lekéréses kiszolgálón. Azure Automation konfigurációs adatokat lehet küldeni a Log Analytics-munkaterület is beállíthatja. Konfigurációs állapot adatokat küldeni a Log Analytics-munkaterület kezelésével kapcsolatos információkért lásd: [továbbítása Azure Automation Állapotkonfiguráció Log Analytics számára](automation-dsc-diagnostics.md).

## <a name="network-planning"></a>A hálózat konfigurálása

A következő portot és az URL-címek szükségesek az állapot Configuration (DSC) való kommunikációhoz az Automation szolgáltatással:

* Port: Csak a TCP 443-as kimenő internet-hozzáférés szükség.
* Global URL: *.azure-automation.net
* USA-beli államigazgatás – Virginia, globális URL: *.azure-automation.us
* Az ügynök szolgáltatás: https://\<munkaterület azonosítója\>.agentsvc.azure-automation.net

Azt javasoljuk, hogy a címek kivételek meghatározásakor. Az IP-címeket, letöltheti a [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). Ez a fájl hetente frissül, és a jelenleg üzembe helyezett tartományokat és minden jövőbeni változtatásokról, az IP tartományokat.

Ha egy Automation-fiókot, amely egy adott régióban van definiálva, korlátozhatja a kommunikációt a regionális adatközpontjának. A következő táblázat tartalmazza a DNS-rekord minden olyan régió esetében:

| **Régió** | **DNS-rekord** |
| --- | --- |
| USA nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Kelet-Japán |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Az Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA-beli államigazgatás – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Régió IP-címek helyett régiónevek listáját, töltse le a [Azure Datacenter IP-cím](https://www.microsoft.com/download/details.aspx?id=41653) tartalmazó XML-fájlt a Microsoft Download Center.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájlt a Microsoft Azure-adatközpontok az által használt IP-címtartományok listája. A fájl a compute, SQL és storage tartományokat tartalmaz.
>
>A frissített hetente tesznek közzé. A fájl a jelenleg üzembe helyezett tartományokat és minden jövőbeni változtatásokról, az IP tartományokat tükrözi. A fájlban megjelenő új tartományokat legalább egy hétig nem használják az adatközpontokban.
>
> Célszerű letölteni az új XML-fájlt minden héten. Ezután frissítse a helyet, hogy helyesen azonosítsa az Azure-ban futó szolgáltatásokat. Az Azure ExpressRoute-felhasználók vegye figyelembe, hogy ez a fájl minden hónap első hetében Azure címterületek a Border Gateway Protocol (BGP) hirdetés frissítésére használatos.

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

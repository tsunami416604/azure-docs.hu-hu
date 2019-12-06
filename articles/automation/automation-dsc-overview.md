---
title: Azure Automation állapot konfigurációjának áttekintése
description: Az Azure Automation állapot-konfiguráció (DSC), a hozzá tartozó feltételek és az ismert problémák áttekintése
keywords: PowerShell DSC, a kívánt állapot konfigurálása, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 787cade13a0636bb25afa1d4043a977f512484f9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850890"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation állapot konfigurációjának áttekintése

Azure Automation állapot-konfiguráció egy olyan Azure-szolgáltatás, amely lehetővé teszi a PowerShell kívánt állapot-konfigurációjának (DSC) [konfigurációjának](/powershell/scripting/dsc/configurations/configurations), a [DSC-erőforrások](/powershell/scripting/dsc/resources/resources)importálásának és a konfigurációk hozzárendelésének a felhőben való társításának megírását, kezelését és összeállítását.

## <a name="why-use-azure-automation-state-configuration"></a>Miért érdemes Azure Automation állapot konfigurációját használni

Azure Automation állapot-konfiguráció számos előnyt biztosít a DSC-t használó Azure-on kívül.

### <a name="built-in-pull-server"></a>Beépített lekéréses kiszolgáló

Azure Automation állapot-konfiguráció olyan DSC lekérési kiszolgálót biztosít, amely hasonló a [Windows-szolgáltatás DSC-Service-](/powershell/scripting/dsc/pull-server/pullserver) hez, hogy a célként megadott csomópontok automatikusan megkapják a konfigurációkat, megfelelnek a kívánt állapotnak, és visszaküldjék a megfelelőségi jelentést. A Azure Automation beépített lekérési kiszolgálója kiküszöböli a saját lekéréses kiszolgáló beállításának és karbantartásának szükségességét. A Azure Automation a felhőben vagy a helyszínen is megcélozhatja a virtuális vagy fizikai Windows vagy Linux rendszerű gépeket.

### <a name="management-of-all-your-dsc-artifacts"></a>Az összes DSC-összetevő kezelése

Azure Automation állapot-konfiguráció ugyanazokat a felügyeleti réteget adja meg a [PowerShell kívánt állapotának konfigurálásához](/powershell/scripting/dsc/overview/overview) , mint Azure Automation a PowerShell-parancsfájlok futtatására szolgáló ajánlatokat.

A Azure Portal vagy a PowerShellből kezelheti az összes DSC-konfigurációt, erőforrást és a cél csomópontokat.

![Képernyőkép a Azure Automation oldalról](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Jelentéskészítési adatgyűjtés importálása Azure Monitor naplókba

Azure Automation állapot-konfigurációval felügyelt csomópontok részletes jelentési állapotinformációkat küldenek a beépített lekérési kiszolgálónak. Konfigurálhatja Azure Automation állapot konfigurációját, hogy ezeket az adatLog Analytics-munkaterületre küldje. Az állapot-konfiguráció állapotának az Log Analytics munkaterületre való küldésével kapcsolatban lásd: [Azure Automation állapot-jelentési információk továbbítása Azure monitor naplókhoz](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Előfeltételek

Azure Automation állapot konfigurációjának (DSC) használatakor vegye figyelembe az alábbi követelményeket.

### <a name="operating-system-requirements"></a>Az operációs rendszerre vonatkozó követelmények

A Windows rendszert futtató csomópontok esetében a következő verziók támogatottak:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7 rendszeren

Az [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) önálló termék SKU nem tartalmazza a kívánt állapot konfigurációja megvalósítását, így nem felügyelhető a PowerShell DSC-vel vagy Azure Automation állapot-konfigurációval.

A Linux rendszerű csomópontok esetében a következő disztribúciók/verziók támogatottak:

A DSC Linux-bővítmény támogatja a [támogatott Linux-disztribúciók](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)alatt felsorolt Linux-disztribúciókat.

### <a name="dsc-requirements"></a>DSC-követelmények

Az Azure-ban futó összes Windows-csomópont esetében a [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) lesz telepítve az előkészítés során.  A Windows Server 2012 és Windows 7 rendszert futtató csomópontok esetén a [WinRM engedélyezve lesz](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

Az Azure-ban futó összes Linux-csomópont esetében a [POWERSHELL DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) lesz telepítve az előkészítés során.

### <a name="network-planning"></a>Privát hálózatok konfigurálása

Ha a csomópontok egy magánhálózaton belül találhatók, a következő portok és URL-címek szükségesek az állapot konfigurálásához (DSC) az automatizálással való kommunikációhoz:

* Port: a kimenő internet-hozzáféréshez csak TCP 443 szükséges.
* Globális URL-cím: *. azure-automation.net
* US Gov Virginia globális URL-címe: *. azure-automation.us
* Ügynök szolgáltatás: https://\<munkaterület azonosítója\>. agentsvc.azure-automation.net

Ez hálózati kapcsolatot biztosít a felügyelt csomópont számára a Azure Automationsal való kommunikációhoz.
Ha olyan DSC-erőforrásokat használ, amelyek a csomópontok között kommunikálnak, például a [waitfor * erőforrásai](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)között, akkor is engedélyeznie kell a csomópontok közötti forgalmat.
A hálózati követelmények megismeréséhez tekintse meg az egyes DSC-erőforrások dokumentációját.

#### <a name="proxy-support"></a>Proxy-támogatás

A DSC-ügynök proxy-támogatása a Windows 1809-es és újabb verzióiban érhető el.
A beállítás konfigurálásához állítsa be a **ProxyURL** és a **ProxyCredential** értéket a csomópontok regisztrálásához használt [metaconfiguration-szkriptben](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) .
A Windows korábbi verziói esetében a proxy nem érhető el a DSC-ben.

Linux-csomópontok esetén a DSC-ügynök támogatja a proxyt, és a http_proxy változót fogja használni az URL-cím meghatározásához.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure State konfigurációs hálózati tartományok és névtér

A kivételek meghatározásakor a felsorolt címek használatát javasoljuk. IP-címek esetén letöltheti az [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653). A fájl hetente frissül, és a jelenleg üzembe helyezett tartományokat és az IP-címtartományok közelgő változásait tartalmazza.

Ha egy adott régióhoz meghatározott Automation-fiókkal rendelkezik, akkor korlátozhatja a kommunikációt az adott regionális adatközpontra. A következő táblázat az egyes régiók DNS-rekordját tartalmazza:

| **Régió** | **DNS-rekord** |
| --- | --- |
| USA nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA keleti régiója   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Kelet-Japán |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

A régiók neveit tartalmazó régió IP-címeinek listáját az Azure- [adatközpont IP-címének](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlját a Microsoft letöltőközpontból töltheti le.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájlja felsorolja az Microsoft Azure adatközpontokban használt IP-címtartományt. A fájl a számítási, az SQL-és a tárolási tartományokat tartalmazza.
>
>A frissített fájlok hetente kerülnek közzétételre. A fájl a jelenleg telepített tartományokat és az IP-címtartományok közelgő változásait tükrözi. A fájlban megjelenő új tartományok legalább egy hétig nem használhatók az adatközpontokban.
>
> Érdemes minden héten letölteni az új XML-fájlt. Ezután frissítse a webhelyet az Azure-ban futó szolgáltatások megfelelő azonosításához. Az Azure ExpressRoute felhasználói számára fontos megjegyezni, hogy ez a fájl az Azure Space Border Gateway Protocol (BGP) hirdetményének frissítésére szolgál minden hónap első hetében.

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md) című témakört.
- A csomópontok előkészítésének megismeréséhez lásd: [bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](automation-dsc-onboarding.md)
- A DSC-konfigurációk fordításának megismeréséhez, hogy hozzá lehessen rendelni azokat a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md) című témakört.
- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfiguráció és a csokoládés használatával](automation-dsc-cd-chocolatey.md) .

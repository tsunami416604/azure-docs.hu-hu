---
title: Azure Automation állapot konfigurációjának áttekintése
description: Ez a cikk áttekintést nyújt a Azure Automation állapotának konfigurálásáról.
keywords: PowerShell DSC, a kívánt állapot konfigurálása, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be0da641a67d532851b8d295065213016c795f01
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713182"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation állapot konfigurációjának áttekintése

Azure Automation állapot-konfiguráció egy [Azure-beli](/powershell/scripting/dsc/configurations/configurations) konfiguráció-felügyeleti szolgáltatás, amely lehetővé teszi a PowerShell kívánt állapot-konfiguráció (DSC) konfigurációjának írását, kezelését és fordítását bármely Felhőbeli vagy helyszíni adatközpontban. A szolgáltatás a [DSC-erőforrásokat](/powershell/scripting/dsc/resources/resources)is importálja, és a konfigurációkat a célként megadott csomópontokhoz rendeli hozzá a felhőben. A Azure Portal Azure Automation állapot konfigurációját az **állapot konfigurálása (DSC)** elemre kattintva érheti el a **konfiguráció**felügyelete alatt. 

A Azure Automation állapot-konfiguráció számos különböző gép kezelésére használható:

- Azure virtuális gépek
- Azure-beli virtuális gépek (klasszikus)
- Fizikai/virtuális Windowsos gépek a helyszínen vagy az Azure-tól eltérő felhőben (beleértve az AWS EC2-példányokat)
- Fizikai/virtuális Linux rendszerű számítógépek a helyszínen, az Azure-ban vagy az Azure-tól eltérő felhőben

Ha nem áll készen a számítógép-konfiguráció felhőből való kezelésére, akkor a Azure Automation állapot konfigurációját csak jelentési végpontként használhatja. Ezzel a funkcióval a DSC-n keresztül állíthatja be (leküldéses) konfigurációkat, és megtekintheti a jelentéskészítés részleteit Azure Automationban.

> [!NOTE]
> Az Azure-beli virtuális gépek Azure Automation állapotú konfigurációval való kezelése külön díj nélkül történik, ha a telepített Azure-beli virtuális gép kívánt állapot-konfigurációs bővítményének verziója nagyobb, mint 2,70. További információ: Automation- [**díjszabási oldal**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Miért érdemes Azure Automation állapot konfigurációját használni

Azure Automation állapot-konfiguráció számos előnnyel jár, mint a DSC használata az Azure-on kívül. Ez a szolgáltatás lehetővé teszi, hogy a központi és biztonságos helyről gyorsan és egyszerűen lehessen méretezni a több ezer gépen. Könnyedén engedélyezheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapottal együtt mutatják.

A Azure Automation állapot-konfigurációs szolgáltatás azt a DSC-t adja meg, amit Azure Automation runbookok a PowerShell-parancsfájlok futtatásához. Más szóval ugyanúgy, ahogy a Azure Automation segíti a PowerShell-parancsfájlok kezelését, a DSC-konfigurációk kezelését is segíti. 

### <a name="built-in-pull-server"></a>Beépített lekéréses kiszolgáló

Azure Automation állapot-konfiguráció egy DSC lekérési kiszolgálót biztosít a Windows-szolgáltatás [DSC-szolgáltatásához](/powershell/scripting/dsc/pull-server/pullserver)hasonló módon. A célként megadott csomópontok automatikusan megkapják a konfigurációkat, megfelelnek a kívánt állapotnak, és jelentést készítenek a megfelelőségéről. A Azure Automation beépített lekérési kiszolgálója kiküszöböli a saját lekéréses kiszolgáló beállításának és karbantartásának szükségességét. A Azure Automation a felhőben vagy a helyszínen is megcélozhatja a virtuális vagy fizikai Windows vagy Linux rendszerű gépeket.

### <a name="management-of-all-your-dsc-artifacts"></a>Az összes DSC-összetevő kezelése

Azure Automation állapot-konfiguráció ugyanazokat a felügyeleti réteget hozza a [PowerShell kívánt állapotának konfigurálásához](/powershell/scripting/dsc/overview/overview) , mint a PowerShell-parancsfájlok futtatásához. A Azure Portal vagy a PowerShellből kezelheti az összes DSC-konfigurációt, erőforrást és a cél csomópontokat.

![Képernyőkép a Azure Automation oldalról](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Jelentéskészítési adathalmazok importálása Azure Monitor naplókba

Azure Automation állapot-konfigurációval felügyelt csomópontok részletes jelentési állapotinformációkat küldenek a beépített lekérési kiszolgálónak. Konfigurálhatja Azure Automation állapot konfigurációját, hogy ezeket az adatLog Analytics-munkaterületre küldje. Lásd: [Azure Automation állapot-konfiguráció jelentési információinak továbbítása Azure monitor naplókhoz](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Azure Automation állapot konfigurációjának használatának előfeltételei

Azure Automation állapot konfigurációjának használatakor vegye figyelembe a jelen szakasz követelményeit.

### <a name="operating-system-requirements"></a>Operációsrendszer-követelmények

A Windows rendszert futtató csomópontok esetében a következő verziók támogatottak:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>A [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) önálló termék SKU nem tartalmazza a DSC implementációját. Így nem felügyelhető a PowerShell DSC-vel vagy Azure Automation állapot-konfigurációval.

A Linux rendszerű csomópontok esetében a DSC linuxos bővítmény a [támogatott Linux-disztribúciók](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)alatt felsorolt összes Linux-disztribúciót támogatja.

### <a name="dsc-requirements"></a>DSC-követelmények

Az Azure-ban futó összes Windows-csomópont esetében a [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) telepítve van, ha a gépek engedélyezve vannak. A Windows Server 2012 és Windows 7 rendszert futtató csomópontok esetén a [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) engedélyezve van.

Az Azure-ban futó összes Linux-csomópont esetében a [POWERSHELL DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) telepítve van, ha a gépek engedélyezve vannak.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Magánhálózatok konfigurálása

Ha a csomópontok egy magánhálózaton találhatók, a következő portok és URL-címek szükségesek. Ezek az erőforrások hálózati kapcsolatot biztosítanak a felügyelt csomópont számára, és lehetővé teszik a DSC számára a Azure Automation való kommunikációt.

* Port: csak TCP 443 szükséges a kimenő internet-hozzáféréshez
* Globális URL-cím: ***. Azure-Automation.net**
* US Gov Virginia globális URL-címe: ***. Azure-Automation.us**
* Ügynök szolgáltatás: **https:// \< munkaterület azonosítója \> . agentsvc.Azure-Automation.net**

Ha olyan DSC-erőforrásokat használ, amelyek a csomópontok között kommunikálnak, például a [waitfor * erőforrásai](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)között, akkor is engedélyeznie kell a csomópontok közötti forgalmat. A hálózati követelmények megismeréséhez tekintse meg az egyes DSC-erőforrások dokumentációját.

#### <a name="proxy-support"></a>Proxy-támogatás

A DSC-ügynök proxy-támogatása a Windows 1809-es és újabb verzióiban érhető el. Ez a beállítás a `ProxyURL` `ProxyCredential` csomópontok regisztrálásához használt [metaconfiguration-szkript](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) értékeinek és tulajdonságainak beállításával engedélyezhető. 

>[!NOTE]
>Azure Automation állapot-konfiguráció nem biztosít DSC-proxy támogatást a Windows korábbi verzióihoz.

Linux-csomópontok esetén a DSC-ügynök támogatja a proxyt, és a `http_proxy` változó használatával határozza meg az URL-címet. További információ a proxyk támogatásáról: a [DSC-metaconfigurations előállítása](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure Automation állapot-konfiguráció hálózati tartományai és névtere

A kivételek meghatározásakor ajánlott az alább felsorolt címeket használni. IP-címek esetén letöltheti az [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653). A fájl hetente frissül, és a jelenleg üzembe helyezett tartományokat és az IP-címtartományok közelgő változásait tartalmazza.

Ha egy adott régióhoz meghatározott Automation-fiókkal rendelkezik, akkor korlátozhatja a kommunikációt az adott regionális adatközpontra. A következő táblázat az egyes régiók DNS-rekordját tartalmazza:

| **Régió** | **DNS-rekord** |
| --- | --- |
| USA nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA keleti régiója    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
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

A régiók neveit tartalmazó régió IP-címeinek listáját az Azure- [adatközpont IP-címének](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlját a Microsoft letöltőközpontból töltheti le.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájlja felsorolja az Microsoft Azure adatközpontokban használt IP-címtartományt. A fájl a számítási, az SQL-és a tárolási tartományokat tartalmazza.
>
>A frissített fájlok hetente kerülnek közzétételre. A fájl a jelenleg telepített tartományokat és az IP-címtartományok közelgő változásait tükrözi. A fájlban megjelenő új tartományok legalább egy hétig nem használhatók az adatközpontokban. Érdemes minden héten letölteni az új XML-fájlt. Ezután frissítse webhelyét, hogy megfelelően azonosítsa az Azure-ban futó szolgáltatásokat. 

Az Azure ExpressRoute felhasználói számára fontos megjegyezni, hogy ez a fájl az Azure Space Border Gateway Protocol (BGP) hirdetményének frissítésére szolgál minden hónap első hetében.

## <a name="next-steps"></a>Következő lépések

- A DSC Azure Automation állapot konfigurációban való használatának megkezdéséhez tekintse meg [a Azure Automation állapot konfigurációjának első lépései](automation-dsc-getting-started.md)című témakört.
- A csomópontok engedélyezésével kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának engedélyezése a gépek számára a felügyelethez](automation-dsc-onboarding.md).
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- A PowerShell-parancsmagok leírása: [az. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfigurációval és a csokoládéval](automation-dsc-cd-chocolatey.md).

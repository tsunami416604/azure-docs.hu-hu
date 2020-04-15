---
title: Az Azure Automation állapotkonfigurációja – áttekintés
description: Az Azure Automation állapotkonfigurációjának (DSC), a feltételeinek és az ismert problémáknak a áttekintése
keywords: powershell dsc, kívánt állapotkonfiguráció, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: afceb11180662416aa4953b8b58ef03ffaa70eec
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383184"
---
# <a name="state-configuration-overview"></a>Állapotkonfiguráció áttekintése

Az Azure Automation State Configuration egy Azure-szolgáltatás, amely lehetővé teszi a PowerShell kívánt állapotkonfigurációs (DSC) [konfigurációinak írását,](/powershell/scripting/dsc/configurations/configurations)kezelését és fordítását. A szolgáltatás importálja [a DSC-erőforrásokat](/powershell/scripting/dsc/resources/resources)is, és a felhőben lévő célcsomópontokhoz rendeli hozzá a konfigurációkat.

## <a name="why-use-azure-automation-state-configuration"></a>Miért érdemes használni az Azure Automation állapotkonfigurációját?

Az Azure Automation State Configuration számos előnnyel rendelkezik a DSC Azure-on kívüli használatával szemben.

### <a name="built-in-pull-server"></a>Beépített lekéréses kiszolgáló

Az Azure Automation state configuration a Windows [Feature DSC-szolgáltatáshoz](/powershell/scripting/dsc/pull-server/pullserver)hasonló DSC lekéréses kiszolgálót biztosít. A célcsomópontok automatikusan fogadhatják a konfigurációkat, megfelelnek a kívánt állapotnak, és jelentést tehetnek a megfelelőségükről. Az Azure Automation beépített lekéréses kiszolgálója szükségtelenné teszi a saját lekéréses kiszolgáló beállítását és karbantartását. Az Azure Automation virtuális vagy fizikai Windows- vagy Linux-gépeket célozhat meg a felhőben vagy a helyszínen.

### <a name="management-of-all-your-dsc-artifacts"></a>Az összes DSC-műtermék kezelése

Az Azure Automation state configuration ugyanazt a felügyeleti réteget hozza a [PowerShell kívánt állapotkonfigurációjához,](/powershell/scripting/dsc/overview/overview) mint a PowerShell-parancsfájlok. Az Azure Portalon vagy a PowerShell, kezelheti az összes DSC-konfigurációk, erőforrások és a célcsomópontok.

![Képernyőkép az Azure Automation lapról](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Jelentési adatok importálása az Azure Monitor naplóiba

Az Azure Automation állapotkonfigurációval kezelt csomópontok részletes jelentési állapotadatokat küldenek a beépített lekéréses kiszolgálónak. Konfigurálhatja az Azure Automation state configuration-t, hogy küldje el ezeket az adatokat a Log Analytics-munkaterületre. Lásd: [Az Azure Automation állapotkonfigurációs jelentéskészítési adatok továbbítása az Azure Monitor naplóiba.](automation-dsc-diagnostics.md)

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Az Azure Automation állapotkonfigurációjának előfeltételei

Kérjük, vegye figyelembe az alábbi követelményeket az Azure Automation State Configuration for DSC használatakor.

### <a name="operating-system-requirements"></a>Operációsrendszer-követelmények

A Windows rendszert futtató csomópontok esetében a következő verziók támogatottak:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>A [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) önálló terméktermék-termékváltozata nem tartalmazza a DSC implementációját. Így nem kezelhető a PowerShell DSC vagy az Azure Automation állapotkonfiguráció.

A Linuxot futtató csomópontok esetében a DSC Linux kiterjesztés támogatja a Támogatott Linux disztribúciók alatt felsorolt összes Linux [disztribúciót.](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)

### <a name="dsc-requirements"></a>DSC-követelmények

Az Azure-ban futó összes Windows-csomópont esetében a [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) a bevezetés során települ. Windows Server 2012 és Windows 7 rendszert futtató csomópontok esetén a [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) engedélyezve van.

Az Azure-ban futó összes Linux-csomópont esetében a [PowerShell DSC linuxos](https://github.com/Microsoft/PowerShell-DSC-for-Linux) telepítése a bevezetés során történik.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Magánhálózatok konfigurálása

Ha a csomópontok magánhálózatban találhatók, a következő portra és URL-címekre van szükség. Ezek az erőforrások hálózati kapcsolatot biztosítanak a felügyelt csomópont számára, és lehetővé teszik a DSC számára az Azure Automation-szel való kommunikációt.

* Port: Csak tcp 443 szükséges kimenő internet-hozzáférés
* Globális URL: ***.azure-automation.net**
* Globális URL amerikai gov Virginia: ***.azure-automation.us**
* Ügynökszolgáltatás: **\<https:// munkaterület-azonosító\>.agentsvc.azure-automation.net**

Ha olyan DSC-erőforrásokat használ, amelyek kommunikálnak a csomópontok között, például a [WaitFor* erőforrásokat,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)engedélyeznie kell a csomópontok közötti forgalmat is. Tekintse meg az egyes DSC-erőforrások dokumentációját a hálózati követelmények megértéséhez.

#### <a name="proxy-support"></a>Proxy támogatása

A DSC-ügynök proxytámogatása a Windows 1809-es és újabb verziójában érhető el. Ez a beállítás a csomópontok `ProxyURL` `ProxyCredential` regisztrálásához használt [metakonfigurációs parancsfájl](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) értékeinek beállításával érhető el.

>[!NOTE]
>Az Azure Automation state configuration nem nyújt DSC-proxytámogatást a Windows korábbi verzióihoz.

Linux-csomópontok esetén a DSC-ügynök támogatja a `http_proxy` proxyt, és a változó segítségével határozza meg az URL-címet.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Az Azure Automation állapotkonfigurációs hálózati tartományai és névtere

A kivételek definiálásakor ajánlott az alább felsorolt címek használata. IP-címek esetén letöltheti a [Microsoft Azure Datacenter IP-tartományait.](https://www.microsoft.com/download/details.aspx?id=41653) Ez a fájl hetente frissül, és rendelkezik az aktuálisan telepített tartományokkal és az IP-tartományok közelgő módosításokkal.

Ha egy adott régióhoz definiált Automation-fiókkal rendelkezik, korlátozhatja a kommunikációt az adott regionális adatközponttal. Az alábbi táblázat az egyes régiók DNS-rekordját tartalmazza:

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

A régió IP-címek listájához a régiónevek helyett töltse le az [Azure Datacenter IP-cím](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlját a Microsoft letöltőközpontból.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájl felsorolja a Microsoft Azure adatközpontokban használt IP-címtartományokat. A fájl számítási, SQL és tárolási tartományokat tartalmaz.
>
>A frissített fájl felkerül hetente. A fájl az aktuálisan telepített tartományokat és az IP-tartományok közelgő módosításait tükrözi. A fájlban megjelenő új tartományoklegalább egy hétig nem használatosak az adatközpontokban. Érdemes minden héten letölteni az új XML-fájlt. Ezután frissítse a webhelyet az Azure-ban futó szolgáltatások megfelelő azonosításához. 

Az Azure ExpressRoute-felhasználóknak meg kell jegyezniük, hogy ez a fájl az Azure-tárhely Border Gateway Protocol (BGP) hirdetésének frissítésére szolgál minden hónap első hetében.

## <a name="next-steps"></a>További lépések

- A DSC használatának első lépései az Azure Automation állapotkonfigurációjában az [Azure Automation állapotkonfiguráció használatának első lépései.](automation-dsc-getting-started.md)
- A csomópontok alaplapi állapotáról a [Bevezetési gépek az Azure Automation állapotkonfigurációáltali kezelésről](automation-dsc-onboarding.md)olvashat.
- A DSC-konfigurációk összeállításáról a célcsomópontokhoz való hozzárendelésről a [Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)témakörben olvashat.
- A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Díjszabási információkért lásd: [Azure Automation state configuration fortaring.](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation State Configuration folyamatos üzembe helyezési folyamatban való használatára példa: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és csokoládéhasználatával.](automation-dsc-cd-chocolatey.md)

---
title: Az Azure Automation állapotkonfigurációja – áttekintés
description: Ez a cikk az Azure Automation state configuration (DSC), a kifejezések és az ismert problémák áttekintése.
keywords: powershell dsc, kívánt állapotkonfiguráció, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1166f5a1d7586c54255120a656b060c93f842fd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406184"
---
# <a name="state-configuration-overview"></a>Állapotkonfiguráció áttekintése

Az Azure Automation State Configuration egy Azure-szolgáltatás, amely lehetővé teszi a PowerShell kívánt állapotkonfigurációs (DSC) [konfigurációinak írását,](/powershell/scripting/dsc/configurations/configurations)kezelését és fordítását. A szolgáltatás [dsc-erőforrásokat](/powershell/scripting/dsc/resources/resources) is importál, és konfigurációkat rendel a célcsomópontokhoz, mindezt a felhőben.

## <a name="why-use-azure-automation-state-configuration"></a>Miért érdemes az Azure Automation State Configurationt használni?

Az Azure Automation State Configuration számos előnnyel rendelkezik a DSC Azure-on kívüli használatával szemben.

### <a name="built-in-pull-server"></a>Beépített lekéréses kiszolgáló

Az Azure Automation state configuration a Windows [Feature DSC-szolgáltatáshoz](/powershell/scripting/dsc/pull-server/pullserver)hasonló DSC lekéréses kiszolgálót biztosít. A célcsomópontok automatikusan fogadhatják a konfigurációkat, megfelelnek a kívánt állapotnak, és jelentést tehetnek a megfelelőségükről. Az Azure Automation beépített lekéréses kiszolgálója szükségtelenné teszi a saját lekéréses kiszolgáló beállítását és karbantartását. Az Azure Automation virtuális vagy fizikai Windows- vagy Linux-gépeket célozhat meg a felhőben vagy a helyszínen.

### <a name="manage-all-your-dsc-artifacts"></a>Az összes DSC-műtermék kezelése

Az Azure Automation state configuration ugyanazt a felügyeleti réteget hozza a [PowerShell kívánt állapotkonfigurációjához,](/powershell/scripting/dsc/overview/overview) mint a PowerShell-parancsfájlok. Az Azure Portalon vagy a PowerShell, kezelheti az összes DSC-konfigurációk, erőforrások és a célcsomópontok.

![Képernyőkép az Azure Automation lapról](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Jelentési adatok importálása az Azure Figyelő naplóiba

Az Azure Automation állapotkonfigurációval kezelt csomópontok részletes jelentési állapotadatokat küldenek a beépített lekéréses kiszolgálónak. Konfigurálhatja az Azure Automation state configuration-t, hogy küldje el ezeket az adatokat a Log Analytics-munkaterületre. További információ: [Forward Azure Automation State Configuration reporting data to Azure Monitor Logs](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe az alábbi követelményeket az Azure Automation State Configuration for DSC használatakor.

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
>Mivel a [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) önálló termékterméktermék-termékváltozat nem tartalmaz dsc-implementációt, nem kezelhető a PowerShell DSC vagy az Azure Automation State Configuration.

A Linuxot futtató csomópontok esetében a DSC Linux kiterjesztés támogatja a Támogatott Linux disztribúciók alatt felsorolt összes Linux [disztribúciót.](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)

### <a name="dsc-requirements"></a>DSC-követelmények

Az Azure-ban futó összes Windows-csomópont esetében a [Windows Management Framework 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) a bevezetés során települ. Windows Server 2012 és Windows 7 rendszert futtató csomópontok esetén a [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) engedélyezve van.

Az Azure-ban futó összes Linux-csomópont esetében a [PowerShell DSC linuxos](https://github.com/Microsoft/PowerShell-DSC-for-Linux) telepítése a bevezetés során történik.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Magánhálózatok konfigurálása

Ha a csomópontok magánhálózatban találhatók, a következő portra és URL-címekre van szükség. Ezek az erőforrások hálózati kapcsolatot biztosítanak a felügyelt csomópont számára, és lehetővé teszik a DSC számára az Azure Automation-szel való kommunikációt.

* Port: Csak tcp 443 szükséges kimenő internet-hozzáférés
* Globális URL: ***.azure-automation.net**
* Globális URL amerikai gov Virginia: ***.azure-automation.us**
* Ügynökszolgáltatás: **\<https:// munkaterület-azonosító\>.agentsvc.azure-automation.net**

Ha olyan DSC-erőforrásokat használ, amelyek kommunikálnak a csomópontok között, például a [WaitFor* erőforrásokat,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)engedélyeznie kell a csomópontok közötti kommunikációt is. Ezeknek a hálózati követelményeknek a megismeréséhez tekintse meg az egyes DSC-erőforrások dokumentációját.

#### <a name="proxy-support"></a>Proxy támogatása

A DSC-ügynök proxytámogatása a Windows 1809-es és újabb verziójában érhető el. Ezt a beállítást a csomópontok `ProxyURL` `ProxyCredential` regisztrálásához használt [metakonfigurációs parancsfájl](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) értékeinek beállításával engedélyezheti.

>[!NOTE]
>Az Azure Automation state configuration nem nyújt DSC-proxy támogatást a Windows korábbi verzióihoz.

Linux-csomópontok esetén a DSC-ügynök támogatja a proxykiszolgálót, és a `http_proxy` változó segítségével adja meg az URL-címet.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Az Azure Automation állapotkonfigurációs hálózati tartományai és névtere

Kivételek definiálásakor azt javasoljuk, hogy az alábbi táblázatban felsorolt IP-címeket használja. IP-címek esetén letöltheti a [Microsoft Azure Datacenter IP-tartományok](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlját a Microsoft letöltőközpontból. Ez a fájl tartalmazza az aktuálisan telepített tartományokat és az IP-tartományok közelgő módosításait. Hetente frissül.

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

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájl felsorolja a Microsoft Azure adatközpontokban használt IP-címtartományokat. A fájl számítási, SQL és tárolási tartományokat tartalmaz.
>
>A frissített fájl felkerül hetente. A fájl az aktuálisan telepített tartományokat és az IP-tartományok közelgő módosításait tükrözi. A fájlban megjelenő új tartományoklegalább egy hétig nem használatosak az adatközpontokban. Érdemes minden héten letölteni egy új XML-fájlt. Ezután frissítheti a webhelyet, hogy megfelelően azonosíthassa az Azure-ban futó szolgáltatásokat. 

Ha Ön Azure ExpressRoute-felhasználó, vegye figyelembe, hogy ez a fájl az Azure Space Border Gateway Protocol (BGP) hirdetésének frissítésére szolgál minden hónap első hetében.

## <a name="next-steps"></a>További lépések

- A DSC használatának első lépései az Azure Automation állapotkonfigurációjában az [Azure Automation állapotkonfiguráció használatának első lépései.](automation-dsc-getting-started.md)
- A csomópontok alaplapi elrendezéséről az [Alaplapi gépek az Azure Automation állapotkonfigurációja szolgáltatással való kezelés ről](automation-dsc-onboarding.md)olvashat.
- A DSC-konfigurációk összeállításáról a célcsomópontokhoz való hozzárendelésről az [Azure Automation állapotkonfigurációja konfigurációk fordítása témakörben](automation-dsc-compile.md)olvashat.
- A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Díjszabási információkért lásd: [Azure Automation state configuration fortaring.](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation State Configuration folyamatos üzembe helyezési folyamatban való használatának például az [Azure Automation állapotkonfigurációját és a Chocolatey szolgáltatást használó virtuális gépekfolyamatos üzembe helyezése című témakört.](automation-dsc-cd-chocolatey.md)

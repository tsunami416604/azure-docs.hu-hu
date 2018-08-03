---
title: Adatgyűjtés Azure Log Analytics-ügynököket, a hibrid környezetben |} A Microsoft Docs
description: Ez a témakör segít megérteni az adatok gyűjtéséhez és a helyszíni vagy más felhőalapú környezetben a Log Analytics használatával üzemeltetett számítógépek figyelése.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: fa1d86bade0981a000d9310c4734b1e93d50944d
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480863"
---
# <a name="collect-data-in-a-hybrid-environment-with-log-analytics-agent"></a>Adatgyűjtés a Log Analytics-ügynököket, a hibrid környezetben

Az Azure Log Analytics gyűjtenek, majd a futó Windows vagy Linux operációs rendszert futtató számítógépekről az adatokkal műveleteket végezni:

* [Az Azure virtual machines](log-analytics-quick-collect-azurevm.md) a Log Analytics Virtuálisgép-bővítménnyel 
* Az Adatközpont fizikai kiszolgálóként vagy virtuális gépek
* Virtuális gépek egy felhőben üzemeltetett szolgáltatásban, mint az Amazon Web Services (AWS)

Saját környezetben futtatott számítógépek kapcsolódhat közvetlenül egymáshoz a Log Analytics, vagy ha már figyelni ezeket a számítógépeket, és a System Center Operations Manager 2012 R2 vagy újabb, a kezelése az Operations management csoport is integrálhatja a Log Analytics használatával és továbbra is fenntartja az informatikai szolgáltatás működési folyamatok.  

## <a name="overview"></a>Áttekintés

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Mielőtt elemzése, és az összegyűjtött adatokat működő, meg kell telepítése és csatlakozás ügynökök összes a számítógép, amelyet szeretne adatokat küldeni a Log Analytics szolgáltatásnak. Telepíthet ügynököket a helyszíni számítógépeken telepítővel, parancssor, vagy a Desired State Configuration (DSC) az Azure Automationben. 

Az ügynök Linux és Windows 443-as TCP-porton keresztül kommunikál a Log Analytics szolgáltatással kimenő, és ha a számítógép csatlakozik egy tűzfalon vagy proxykiszolgálón való kommunikációhoz az interneten keresztül, tekintse át a [az Előfeltételek szakaszban](#prerequisites) , Ismerje meg, a hálózati konfiguráció szükséges.  Ha az informatikai biztonsági szabályzatok nem engedélyezik a számítógépek a hálózat csatlakozik az internethez, beállíthat egy [OMS-átjáró](log-analytics-oms-gateway.md) , majd konfigurálja az ügynök csatlakoztatása a Log Analytics-átjárón keresztül. Az ügynök ezután fogadni a konfigurációs adatokat, és attól függően, mely adatok gyűjtési szabályok és a megoldások engedélyezte az összegyűjtött adatok küldése. 

Ha a számítógép és a System Center Operations Manager 2012 R2 vagy újabb, az adatok gyűjtéséhez és a szolgáltatás továbbítja, és továbbra is figyeli a Log Analytics szolgáltatással többhelyű lehet [az Operations Manager](log-analytics-om-agents.md). Az Operations Manager felügyeleti csoport integrálva van a Log Analytics által figyelt Linux rendszerű számítógépek konfigurációját az adatforrásokat és a továbbítás összegyűjtött adatokat a felügyeleti csoporton nem jelenik meg. A Windows-ügynök legfeljebb négy munkaterületeket, jelentheti a közben csak támogatja a Linux-ügynök, egy egyetlen-munkaterületre jelentő.  

A Linux és Windows-ügynököt nem csak a Log Analyticshez való kapcsolódás, Azure Automation gazdagép a hibrid forgatókönyv-feldolgozói szerepkör és a például a változáskövetési és frissítéskezelési megoldásokat is támogatja.  A hibrid forgatókönyv-feldolgozói szerepkör kapcsolatos további információkért lásd: [Azure Automation hibrid Runbook-feldolgozó](../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>A támogatott Windows operációs rendszerek
A Windows-ügynök hivatalosan támogatott a Windows operációs rendszer következő verziói:

* A Windows Server 2008 Service Pack 1 (SP1) vagy újabb
* Windows 7 SP1 és újabb verziók.

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek
A következő Linux-disztribúciók hivatalosan támogatott.  A Linux-ügynök azonban más disztribúció nem szerepel a listán is futhat.  Ha másként nincs jelezve, az összes kisebb kiadásokban minden felsorolt főverzió támogatottak.  

* Amazon Linux 2012.09 való 2015.09 (x86/x64)
* CentOS Linux 5, 6 és 7 (x86/x64)  
* Oracle Linux 5, 6 és 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5, 6 és 7 (x86/x64)
* Debian GNU/Linux 6, 7, és 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 – 12 (x86/x64)

## <a name="tls-12-protocol"></a>A TLS 1.2 protokoll
A Log Analytics az átvitt adatok biztonságának biztosítása érdekében, erősen javasoljuk, hogy legalább az ügynök konfigurálása Transport Layer Security (TLS) 1.2-es. Biztonsági rés található régebbi verziói a TLS/Secure Sockets Layer (SSL), és jelenleg továbbra is működnek, hogy a visszamenőleges kompatibilitás, amíg azok **nem ajánlott**.  További információkért tekintse át a [biztonságosan a TLS 1.2 használatával az adatok küldésének](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Hálózati tűzfalra vonatkozó követelmények
Az alábbi listában a proxy- és tűzfalbeállítások szükséges konfigurációs információkat, a Linux és Windows-ügynök kommunikáljon a Log Analytics információkat.  

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |443-as port |Bejövő és kimenő|Igen |  
|*.oms.opinsights.azure.com |443-as port |Bejövő és kimenő|Igen |  
|*.blob.core.windows.net |443-as port |Bejövő és kimenő|Igen |  
|*.azure-automation.net |443-as port |Bejövő és kimenő|Igen |  


Ha azt tervezi, használja az Azure Automation hibrid Runbook-feldolgozó kapcsolódni és regisztrálni az Automation szolgáltatással a runbookok használatához a környezetben, azt a portszámot és a leírt URL-címek hozzáféréssel kell rendelkeznie [a hálózat beállítása a Hibrid Runbook-feldolgozó](../automation/automation-hybrid-runbook-worker.md#network-planning). 

A Windows és Linux-ügynök támogatja keresztül, ha a proxy- vagy OMS-átjárót a Log Analytics szolgáltatás a HTTPS protokoll használatával kommunikál.  Névtelen és alapszintű hitelesítés (felhasználónév és jelszó) támogatottak.  A Windows-ügynök közvetlenül csatlakozik a szolgáltatáshoz, a proxy konfigurációs van megadva a telepítés során vagy [üzembe helyezés után](log-analytics-agent-manage.md#update-proxy-settings) a Vezérlőpultot vagy a PowerShell használatával.  

A Linux-ügynök a proxykiszolgáló van megadva a telepítés során vagy [a telepítés után](/log-analytics-agent-manage.md#update-proxy-settings) a proxy.conf konfigurációs fájljának módosításával.  A Linuxos ügynök proxykonfiguráció értékének szintaxisa a következő:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Ha a proxykiszolgáló nem igényel hitelesítést, a Linux-ügynök továbbra is szükséges egy pszeudo-felhasználó/jelszó megadása. Ez lehet bármely felhasználónév vagy jelszó.

|Tulajdonság| Leírás |
|--------|-------------|
|Protokoll | https |
|Felhasználó | A proxy hitelesítése nem kötelező felhasználónév |
|jelszó | Nem kötelező jelszót proxyhitelesítés |
|proxyhost | Cím vagy teljes Tartománynevét a proxy server/OMS átjáró |
|port | A proxy server/OMS átjáró opcionális port számát |

Például:`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Ha például a speciális karakterek használata "\@" meg a jelszót, egy proxy kapcsolat hibaüzenetet kapja, mert az érték nem megfelelően elemzi.  A probléma megkerüléséhez kódolása a jelszót az URL-címet, például egy eszközzel [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Telepítse és konfigurálja az ügynököt 
A helyszíni számítógépek csatlakoztatása közvetlenül a Log Analytics követelményektől függően különböző módszerekkel lehet elvégezni. Az alábbi táblázat minden egyes metódus határozza meg, amely leginkább megfelel a szervezet emeli ki.

|Forrás | Módszer | Leírás|
|-------|-------------|-------------|
| Windows rendszerű számítógép|- [Manuális telepítés](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Az Azure Stack Resource Manager-sablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |A Microsoft Monitoring agent telepítése a parancssorból vagy egy Azure Automation DSC, például automatikus módszer használatával [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), vagy ha telepítette a Microsoft Azure Resource Manager-sablonnal Az Azure Stack az adatközpontban.| 
|Linux rendszerű számítógép| [Manuális telepítés](log-analytics-quick-collect-linux-computer.md)|Telepítse az ügynököt a Linux rendszerre, a Githubon található burkoló-parancsfájl hívása során. | 
| System Center Operations Manager|[Az Operations Manager integrálása a Log Analytics használatával](log-analytics-om-agents.md) | Az Operations Manager és a Log Analytics továbbítani közötti integráció beállítása összegyűjti az adatokat a felügyeleti csoportnak jelentő Linux és Windows-számítógépekről.|  

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [adatforrások](log-analytics-data-sources.md) az adatok gyűjtését a Windows vagy Linux rendszert elérhető adatforrásokkal kapcsolatos tudnivalók. 

* Ismerje meg [naplókereséseket](log-analytics-log-searches.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 

* Ismerje meg [megoldások](log-analytics-add-solutions.md) , amelyek további funkciókkal bővítik a Log Analytics és a is gyűjthet adatokat az OMS-tárházba.
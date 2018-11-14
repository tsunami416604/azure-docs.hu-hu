---
title: Az Azure Log Analytics-ügynököket a naplóadatok gyűjtése |} A Microsoft Docs
description: Ez a témakör segít megérteni az adatok gyűjtéséhez és az Azure-ban üzemeltetett számítógépek figyelése a helyszíni vagy más Log Analytics-környezetet.
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
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 3160e8252ca8a5bed63d0fe9a9cdf84b1b5c6158
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629070"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>Az Azure Log Analytics-ügynököket a naplóadatok gyűjtése

Az Azure Log Analytics (OMS) ügynök, a Microsoft Monitoring Agent (MMA) vagy az OMS Linux-ügynök, mint korábban említett kifejlesztett átfogó felügyeletét nyújtja a helyszíni gépeken által felügyelt számítógépek [System Center Operations Kezelő](https://docs.microsoft.com/system-center/scom/), és a virtuális gépek bármilyen felhőben. A Windows és Linux-ügynökök csatlakoztatása a Log Analytics-munkaterület különböző forrásokból, valamint minden olyan egyedi naplók adatainak összegyűjtése, illetve metrikák, meghatározott figyelési megoldásban. 

Ez a cikk részletes áttekintést nyújt az ügynök, a rendszer és a hálózati követelmények és a különböző központi telepítési módszer.   

## <a name="overview"></a>Áttekintés

![Log Analytics ügynök kommunikációs diagramja](./media/log-analytics-agent-overview/log-analytics-agent-01.png)

Mielőtt elemzése, és az összegyűjtött adatokat működő, meg kell telepítése és csatlakozás ügynökök összes adatokat küldeni a Log Analytics szolgáltatás kívánt gépek. Ügynökök telepíthető Azure virtuális gépeken az Azure Log Analytics Virtuálisgép-bővítmény használata a Windows és Linux rendszereken, valamint a gépek telepítővel, parancssor, hibrid környezetben, vagy a Desired State Configuration (DSC) az Azure Automationben. 

Az ügynök Linux és Windows 443-as TCP-porton keresztül kommunikál a Log Analytics szolgáltatás kimenő, és ha való kommunikációhoz az interneten keresztül, egy tűzfalon vagy proxykiszolgálón keresztül csatlakozik a gép tudni, hogy a hálózati konfigurációt az alábbi követelmények áttekintése szükséges. Ha az informatikai biztonsági szabályzatok nem engedélyezik a számítógépek a hálózat csatlakozik az internethez, beállíthat egy [Log Analytics-átjáró](log-analytics-oms-gateway.md) , majd konfigurálja az ügynök csatlakoztatása a Log Analytics-átjárón keresztül. Az ügynök ezután fogadni a konfigurációs adatokat, és attól függően, hogy milyen adatokat gyűjtött adatok gyűjtési szabályok és figyelési megoldások engedélyezte. 

Ha a számítógép és a System Center Operations Manager 2012 R2 vagy újabb, az adatok gyűjtéséhez és a szolgáltatás továbbítja, és továbbra is figyeli a Log Analytics szolgáltatással többhelyű lehet [az Operations Manager](log-analytics-om-agents.md). Az Operations Manager felügyeleti csoport integrálva van a Log Analytics által figyelt Linux rendszerű számítógépek konfigurációját az adatforrásokat és a továbbítás összegyűjtött adatokat a felügyeleti csoporton nem jelenik meg. A Windows-ügynök legfeljebb négy Log Analytics-munkaterületek, jelentheti a közben csak támogatja a Linux-ügynök, egy egyetlen-munkaterületre jelentő.  

A Linux és Windows-ügynököt nem csak a Log Analyticshez való kapcsolódás, azt is támogatja a hibrid forgatókönyv-feldolgozói szerepkör és más szolgáltatások üzemeltetésére, mint például az Azure Automation [Change Tracking](../automation/automation-change-tracking.md) és [kezelése](../automation/automation-update-management.md). A hibrid forgatókönyv-feldolgozói szerepkör kapcsolatos további információkért lásd: [Azure Automation hibrid Runbook-feldolgozó](../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>A támogatott Windows operációs rendszerek
A Windows-ügynök hivatalosan támogatott a Windows operációs rendszer következő verziói:

* A Windows Server 2008 Service Pack 1 (SP1) vagy újabb
* Windows 7 SP1 és újabb verziók.

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek
Ez a szakasz ismerteti a támogatott Linux-disztribúciók részleteit.    

Kiadás dátuma: 2018 augusztus után verzióval kezdődően igyekszünk a következő módosításokat a támogatási modell:  

* Csak a kiszolgáló-verziók támogatottak, nem az ügyfél.  
* Új verzióinak [Azure Linux által támogatott disztribúciók](../virtual-machines/linux/endorsed-distros.md) mindig támogatottak.  
* Összes kisebb kiadások támogatottak egyes felsorolt főverzió.
* Verziók, amelyek megfeleltek a gyártó támogatása befejezési dátum nem támogatottak.  
* AMI új verziói nem támogatottak.  
* Csak az SSL futtató verziók 1.x alapértelmezés szerint támogatott.

Ha a disztribúció vagy a verziója, amely jelenleg nem támogatott, és a támogatási modell nem igazodnak használ, javasoljuk, hogy először ágaztatnia ebben a tárházban, bosszankodnak, hogy a Microsoft támogatási nem nyújt segítséget az elágaztatott ügynök verziók.

* Amazon Linux 2017.09 (x 64)
* CentOS Linux 6 (x86/x64) és 7 (x 64)  
* Oracle Linux 6 és 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) és 7 (x 64)
* Debian GNU/Linux 8. és 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) és 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x 64)

>[!NOTE]
>OpenSSL 1.1.0-s csak a x86_x64 platformok (64 bites) és a támogatott OpenSSL-esnél korábbi 1.x bármilyen platformon nem támogatott.
>

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

A Windows és Linux-ügynök támogatja a proxykiszolgáló vagy a Log Analytics-átjáró a Log Analytics szolgáltatás a HTTPS protokoll használatával keresztül kommunikál.  Névtelen és alapszintű hitelesítés (felhasználónév és jelszó) támogatottak.  A Windows-ügynök közvetlenül csatlakozik a szolgáltatáshoz, a proxy konfigurációs van megadva a telepítés során vagy [üzembe helyezés után](log-analytics-agent-manage.md#update-proxy-settings) a Vezérlőpultot vagy a PowerShell használatával.  

A Linux-ügynök a proxykiszolgáló van megadva a telepítés során vagy [a telepítés után](log-analytics-agent-manage.md#update-proxy-settings) a proxy.conf konfigurációs fájljának módosításával.  A Linuxos ügynök proxykonfiguráció értékének szintaxisa a következő:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Ha a proxykiszolgáló nem igényel hitelesítést, a Linux-ügynök továbbra is szükséges egy pszeudo-felhasználó/jelszó megadása. Ez lehet bármely felhasználónév vagy jelszó.

|Tulajdonság| Leírás |
|--------|-------------|
|Protokoll | https |
|Felhasználó | A proxy hitelesítése nem kötelező felhasználónév |
|jelszó | Nem kötelező jelszót proxyhitelesítés |
|proxyhost | Cím vagy teljes Tartománynevét a proxy server/Log Analytics-átjáró |
|port | A proxy server/Log Analytics-átjáró opcionális port száma |

Például:`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Ha például a speciális karakterek használata "\@" meg a jelszót, egy proxy kapcsolat hibaüzenetet kapja, mert az érték nem megfelelően elemzi.  A probléma megkerüléséhez kódolása a jelszót az URL-címet, például egy eszközzel [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Telepítse és konfigurálja az ügynököt 
Az Azure-előfizetés vagy hibrid környezetben gépek csatlakoztatása közvetlenül az Azure Log Analytics követelményektől függően különböző módszerekkel lehet elvégezni. Az alábbi táblázat minden egyes metódus határozza meg, amely leginkább megfelel a szervezet emeli ki.

|Forrás | Módszer | Leírás|
|-------|-------------|-------------|
|Azure VM| -Log Analytics Virtuálisgép-bővítmény [Windows](../virtual-machines/extensions/oms-windows.md) vagy [Linux](../virtual-machines/extensions/oms-linux.md) Azure CLI-vel vagy egy Azure Resource Manager-sablonnal<br>- [Az Azure Portalról manuálisan](log-analytics-quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | A bővítmény a Log Analytics-ügynököket telepíti az Azure-beli virtuális gépeken, és regisztrálja őket egy meglévő Azure Monitor-munkaterületet.|
| Windows rendszerű hibrid számítógép|- [Manuális telepítés](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Az Azure Stack Resource Manager-sablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |A Microsoft Monitoring agent telepítése a parancssorból vagy egy Azure Automation DSC, például automatikus módszer használatával [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), vagy ha telepítette a Microsoft Azure Resource Manager-sablonnal Az Azure Stack az adatközpontban.| 
| Linux rendszerű hibrid számítógép| [Manuális telepítés](log-analytics-quick-collect-linux-computer.md)|Telepítse az ügynököt a Linux rendszerre, a Githubon található burkoló-parancsfájl hívása során. | 
| System Center Operations Manager|[Az Operations Manager integrálása a Log Analytics használatával](log-analytics-om-agents.md) | Az Operations Manager és a Log Analytics továbbítani közötti integráció beállítása összegyűjti az adatokat a felügyeleti csoportnak jelentő Linux és Windows-számítógépekről.|  

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [adatforrások](log-analytics-data-sources.md) az adatok gyűjtését a Windows vagy Linux rendszert elérhető adatforrásokkal kapcsolatos tudnivalók. 

* Ismerje meg [lekérdezések naplózását](log-analytics-queries.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 

* Ismerje meg [figyelési megoldások](../monitoring/monitoring-solutions.md) , amelyek további funkciókkal bővítik a Azure Monitor és is gyűjthet adatokat a Log Analytics-munkaterületre.

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
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: magoedte
ms.openlocfilehash: 66f3a9ae1f29d863170dcb4bc43e38b648602eed
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695030"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>A Log Analytics ügynökkel gyűjti a naplózási adatokat

A korábban Microsoft monitoring Agent (MMA) vagy OMS Linux Agent néven ismert Azure Log Analytics-ügynök a helyszíni gépek és a [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)által figyelt számítógépek teljes körű felügyeletére lett kifejlesztve, valamint virtuális gépek bármilyen felhőben. A Windows-és Linux-ügynökök csatolva vannak egy Azure Monitorhoz, és az összegyűjtött naplózási adatokat a Log Analytics munkaterületen található különböző forrásokból, valamint a figyelési megoldásban meghatározott egyedi naplókból vagy mérőszámokból tárolják. 

Ez a cikk részletes áttekintést nyújt az ügynök, a rendszer és a hálózati követelmények és a különböző központi telepítési módszer.

## <a name="overview"></a>Áttekintés

![Log Analytics ügynök kommunikációs diagramja](./media/log-analytics-agent/log-analytics-agent-01.png)

Az összegyűjtött adatok elemzése előtt először telepítenie és csatlakoznia kell az ügynököket az összes olyan géphez, amelyhez adatokat szeretne küldeni a Azure Monitor szolgáltatásnak. Ügynökök telepíthető Azure virtuális gépeken az Azure Log Analytics Virtuálisgép-bővítmény használata a Windows és Linux rendszereken, valamint a gépek telepítővel, parancssor, hibrid környezetben, vagy a Desired State Configuration (DSC) az Azure Automationben. 

A Linux-és Windows-ügynök a 443-as TCP-porton keresztül kommunikál a Azure Monitor szolgáltatással, és ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztül, tekintse át az alábbi követelményeket a hálózati konfiguráció megismeréséhez. szükséges. Ha az IT-biztonsági szabályzatok nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást, beállíthat egy [log Analytics-átjárót](gateway.md) , majd beállíthatja az ügynököt, hogy az átjárón keresztül kapcsolódjon Azure monitor naplókhoz. Az ügynök ezután fogadhatja a konfigurációs adatokat, és az összegyűjtött adatokat attól függően, hogy milyen adatgyűjtési szabályok és figyelési megoldások vannak engedélyezve a munkaterületen. 

Ha a Log Analytics ügynököket használja az adatok gyűjtésére, az ügynök üzembe helyezésének megtervezéséhez a következőket kell megismernie:

* A Windows-ügynököktől származó adatok összegyűjtéséhez beállíthatja, hogy az [egyes ügynökök egy vagy több munkaterületnek jelentsenek](agent-windows.md), még akkor is, ha System Center Operations Manager felügyeleti csoportnak jelentenek jelentést. A Windows-ügynök legfeljebb négy munkaterületet tud jelenteni.
* A Linux-ügynök nem támogatja a többsoros vezérlést, és csak egyetlen munkaterületre tud jelentést készíteni.
* A Windows-ügynök támogatja a [FIPS 140 szabványt](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), míg a Linux-ügynök nem támogatja azt.  

Ha System Center Operations Manager 2012 R2 vagy újabb verziót használ:

* Az egyes Operations Manager felügyeleti csoportok [csak egy munkaterülethez csatlakoztathatók](om-agents.md).
* A felügyeleti csoportnak jelentést küldő linuxos számítógépeket úgy kell konfigurálni, hogy közvetlenül egy Log Analytics munkaterületre jelentsenek. Ha a Linux rendszerű számítógépek már közvetlenül egy munkaterületre vannak bejelentve, és Operations Manager szeretné figyelni őket, kövesse az alábbi lépéseket [egy Operations Manager felügyeleti csoportnak való jelentéskészítéshez](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Telepítheti a Log Analytics Windows-ügynököt a Windows rendszerű számítógépre, és jelentést készíthet mind a munkaterülettel integrált Operations Manager, mind pedig egy másik munkaterületről.

A Linux és a Windows rendszerhez készült ügynök nem csak a Azure Monitorhoz való csatlakozáshoz használható, Azure Automation a hibrid Runbook-feldolgozói szerepkör és egyéb szolgáltatások, például a [change Tracking](../../automation/change-tracking.md), a [Update Management](../../automation/automation-update-management.md)és a Azure Security Center üzemeltetéséhez. [ ](../../security-center/security-center-intro.md). A hibrid forgatókönyv-feldolgozói szerepkör kapcsolatos további információkért lásd: [Azure Automation hibrid Runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>A támogatott Windows operációs rendszerek

A Windows-ügynök hivatalosan támogatott a Windows operációs rendszer következő verziói:

* A Windows Server 2019
* Windows Server 2008 R2, 2012, 2012 R2, 2016, Version 1709 és 1803
* Windows 7 SP1, Windows 8 Enterprise és Pro, valamint Windows 10 Enterprise és Pro

>[!NOTE]
>Noha a Windows Log Analytics ügynökének célja a kiszolgáló figyelési forgatókönyvének támogatása, a Windows-ügyfél a kiszolgálói operációs rendszerhez konfigurált és optimalizált munkaterhelések támogatásához is futtatható. Az ügynök támogatja a Windows-ügyfelet, azonban a megfigyelési megoldások nem az ügyfél-figyelési forgatókönyvekre összpontosítanak, kivéve, ha explicit módon van megadva.

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek

Ez a szakasz ismerteti a támogatott Linux-disztribúciók részleteit.

Kiadás dátuma: 2018 augusztus után verzióval kezdődően igyekszünk a következő módosításokat a támogatási modell:  

* Csak a kiszolgáló-verziók támogatottak, nem az ügyfél.  
* Új verzióinak [Azure Linux által támogatott disztribúciók](../../virtual-machines/linux/endorsed-distros.md) mindig támogatottak.  
* Összes kisebb kiadások támogatottak egyes felsorolt főverzió.
* Verziók, amelyek megfeleltek a gyártó támogatása befejezési dátum nem támogatottak.  
* AMI új verziói nem támogatottak.  
* Csak az SSL futtató verziók 1.x alapértelmezés szerint támogatott.

>[!NOTE]
>Ha a disztribúció vagy a verziója, amely jelenleg nem támogatott, és a támogatási modell nem igazodnak használ, javasoljuk, hogy először ágaztatnia ebben a tárházban, bosszankodnak, hogy a Microsoft támogatási nem nyújt segítséget az elágaztatott ügynök verziók.

* Amazon Linux 2017.09 (x 64)
* CentOS Linux 6 (x86/x64) és 7 (x 64)  
* Oracle Linux 6 és 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) és 7 (x 64)
* Debian GNU/Linux 8. és 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) és 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) és 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0-s csak a x86_x64 platformok (64 bites) és a támogatott OpenSSL-esnél korábbi 1.x bármilyen platformon nem támogatott.
>

### <a name="agent-prerequisites"></a>Ügynök előfeltételei

A következő táblázat a támogatott Linux-disztribúciók számára szükséges csomagokat mutatja be, amelyekre az ügynököt telepíteni fogja.

|Szükséges csomag |Leírás |Minimális verzió |
|-----------------|------------|----------------|
|Glibc |    GNU C könyvtár | 2.5-12 
|Openssl    | OpenSSL-kódtárak | 1.0. x vagy 1.1. x |
|Curl | cURL webes ügyfél | 7.15.5 |
|Python – ctypes | | 
|PAM | Csatlakoztatható hitelesítési modulok | | 

>[!NOTE]
>A syslog-üzenetek összegyűjtéséhez a rsyslog vagy a syslog-ng szükséges. Red Hat Enterprise Linux, CentOS és Oracle Linux-verzió (sysklog) 5-ös verzióját az alapértelmezett syslog démon nem támogatott a syslog-események gyűjtése. A rendszernapló-adatok ezen disztribúciók ezen verziójából való összegyűjtéséhez a rsyslog démont telepíteni és konfigurálni kell a sysklog lecserélése érdekében.

## <a name="tls-12-protocol"></a>A TLS 1.2 protokoll

Annak érdekében, hogy az adatforgalom biztonsága Azure Monitor naplókba kerüljön, nyomatékosan javasoljuk, hogy konfigurálja az ügynököt legalább Transport Layer Security (TLS) 1,2 használatára. Biztonsági rés található régebbi verziói a TLS/Secure Sockets Layer (SSL), és jelenleg továbbra is működnek, hogy a visszamenőleges kompatibilitás, amíg azok **nem ajánlott**.  További információkért tekintse át a [biztonságosan a TLS 1.2 használatával az adatok küldésének](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Hálózati tűzfalra vonatkozó követelmények

Az alábbi információk a Linux és a Windows-ügynök által Azure Monitor naplókkal való kommunikációhoz szükséges proxy-és tűzfal-konfigurációs információkat felsorolják.  

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.oms.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.blob.core.windows.net |443-as port |Kimenő|Igen |  
|*.azure-automation.net |443-as port |Kimenő|Igen |  

A Azure Governmentához szükséges tűzfal-információk: [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Ha azt tervezi, használja az Azure Automation hibrid Runbook-feldolgozó kapcsolódni és regisztrálni az Automation szolgáltatással a runbookok használatához a környezetben, azt a portszámot és a leírt URL-címek hozzáféréssel kell rendelkeznie [a hálózat beállítása a Hibrid Runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

A Windows-és Linux-ügynök támogatja a proxykiszolgáló vagy a Log Analytics átjáró közötti kommunikációt a HTTPS protokoll használatával történő Azure Monitor.  Névtelen és alapszintű hitelesítés (felhasználónév és jelszó) támogatottak.  A Windows-ügynök közvetlenül csatlakozik a szolgáltatáshoz, a proxy konfigurációs van megadva a telepítés során vagy [üzembe helyezés után](agent-manage.md#update-proxy-settings) a Vezérlőpultot vagy a PowerShell használatával.  

A Linux-ügynök a proxykiszolgáló van megadva a telepítés során vagy [a telepítés után](agent-manage.md#update-proxy-settings) a proxy.conf konfigurációs fájljának módosításával.  A Linuxos ügynök proxykonfiguráció értékének szintaxisa a következő:

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

Az Azure-előfizetésben vagy a hibrid környezetben lévő gépek közvetlen csatlakoztatása Azure Monitor naplók használatával különböző módszerekkel végezhető el a követelményektől függően. Az alábbi táblázat minden egyes metódus határozza meg, amely leginkább megfelel a szervezet emeli ki.

|Forrás | Módszer | Leírás|
|-------|-------------|-------------|
|Azure VM| -Log Analytics Virtuálisgép-bővítmény [Windows](../../virtual-machines/extensions/oms-windows.md) vagy [Linux](../../virtual-machines/extensions/oms-linux.md) Azure CLI-vel vagy egy Azure Resource Manager-sablonnal<br>@no__t – 0[, manuálisan a Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)<br>@no__t – 0[Azure Security Center automatikus kiépítés](../../security-center/security-center-enable-data-collection.md)| – A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és regisztrálja őket egy meglévő Azure Monitor-munkaterületen.<br>– Azure Security Center kiépítheti a Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra, ha engedélyezi a biztonsági rések és fenyegetések figyelését. Ha engedélyezve van, a telepített ügynök nélküli új vagy meglévő virtuális gépek kiépítve lesznek.|
| Windows rendszerű hibrid számítógép|- [Manuális telepítés](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Az Azure Stack Resource Manager-sablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |A Microsoft Monitoring agent telepítése a parancssorból vagy egy Azure Automation DSC, például automatikus módszer használatával [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), vagy ha telepítette a Microsoft Azure Resource Manager-sablonnal Az Azure Stack az adatközpontban.| 
| Linux rendszerű hibrid számítógép| [Manuális telepítés](../../azure-monitor/learn/quick-collect-linux-computer.md)|Telepítse az ügynököt a Linux rendszerre, a Githubon található burkoló-parancsfájl hívása során. | 
| System Center Operations Manager|[Az Operations Manager integrálása a Log Analytics használatával](../../azure-monitor/platform/om-agents.md) | Konfigurálja a Operations Manager és Azure Monitor naplók közötti integrációt, hogy továbbítsa az összegyűjtött adatokat a felügyeleti csoportnak jelentő Windows rendszerű számítógépekről.|  

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [adatforrások](../../azure-monitor/platform/agent-data-sources.md) az adatok gyűjtését a Windows vagy Linux rendszert elérhető adatforrásokkal kapcsolatos tudnivalók. 

* Ismerje meg [lekérdezések naplózását](../../azure-monitor/log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 

* Ismerje meg [figyelési megoldások](../../azure-monitor/insights/solutions.md) , amelyek további funkciókkal bővítik a Azure Monitor és is gyűjthet adatokat a Log Analytics-munkaterületre.

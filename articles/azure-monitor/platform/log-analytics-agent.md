---
title: Naplóbejegyzések gyűjtése az Azure Log Analytics agenttel | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogyan gyűjthet adatokat és figyelheti az Azure-ban, a helyszínen vagy más felhőalapú környezetben üzemeltetett számítógépeket Log Analytics használatával.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: 58d6c8d18e03ab248cfbebcf910ae13c5fee439e
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530969"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>A Log Analytics ügynökkel gyűjti a naplózási adatokat

Az Azure Log Analytics ügynök, amelyet korábban Microsoft monitoring Agent (MMA) vagy OMS Linux-ügynökként fejlesztettek ki a helyszíni gépek, a [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)által figyelt számítógépek és a Felhőbeli virtuális gépek teljes körű felügyeletére. A Windows-és Linux-ügynökök a Log Analytics munkaterületen lévő különböző forrásokból, valamint a figyelési megoldásban meghatározott egyedi naplókból és mérőszámokból Azure Monitor és tárolhatják az összegyűjtött naplózási adatokat. 

Ez a cikk részletes áttekintést nyújt az ügynökről, a rendszerről és a hálózati követelményekről, valamint a különböző üzembe helyezési módszerekről.

## <a name="overview"></a>Áttekintés

![Log Analytics-ügynök kommunikációs diagramja](./media/log-analytics-agent/log-analytics-agent-01.png)

Az összegyűjtött adatok elemzése előtt először telepítenie és csatlakoznia kell az ügynököket az összes olyan géphez, amelyhez adatokat szeretne küldeni a Azure Monitor szolgáltatásnak. Az Azure-beli virtuális gépekre az Azure Log Analytics virtuálisgép-bővítmény használatával telepítheti az ügynököket a Windows és a Linux rendszerhez, valamint a hibrid környezetű számítógépekhez a telepítő, a parancssor vagy a Azure Automation kívánt állapot-konfiguráció (DSC) használatával. 

A Linux-és Windows-ügynök a 443-as TCP-porton keresztül kommunikál a Azure Monitor szolgáltatással, és ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztül, tekintse át az alábbi követelményeket a hálózati konfiguráció megismeréséhez. szükséges. Ha az IT-biztonsági szabályzatok nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást, beállíthat egy [log Analytics-átjárót](gateway.md) , majd beállíthatja az ügynököt, hogy az átjárón keresztül kapcsolódjon Azure monitor naplókhoz. Az ügynök ezután fogadhatja a konfigurációs adatokat, és az összegyűjtött adatokat attól függően, hogy milyen adatgyűjtési szabályok és figyelési megoldások vannak engedélyezve a munkaterületen. 

Ha a Log Analytics ügynököket használja az adatok gyűjtésére, az ügynök üzembe helyezésének megtervezéséhez a következőket kell megismernie:

* A Windows-ügynököktől származó adatok összegyűjtéséhez beállíthatja, hogy az [egyes ügynökök egy vagy több munkaterületnek jelentsenek](agent-windows.md), még akkor is, ha System Center Operations Manager felügyeleti csoportnak jelentenek jelentést. A Windows-ügynök legfeljebb négy munkaterületet tud jelenteni.
* A Linux-ügynök nem támogatja a többsoros vezérlést, és csak egyetlen munkaterületre tud jelentést készíteni.
* A Windows-ügynök támogatja a [FIPS 140 szabványt](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), míg a Linux-ügynök nem támogatja azt.  

Ha System Center Operations Manager 2012 R2 vagy újabb verziót használ:

* Az egyes Operations Manager felügyeleti csoportok [csak egy munkaterülethez csatlakoztathatók](om-agents.md).
* A felügyeleti csoportnak jelentést küldő linuxos számítógépeket úgy kell konfigurálni, hogy közvetlenül egy Log Analytics munkaterületre jelentsenek. Ha a Linux rendszerű számítógépek már közvetlenül egy munkaterületre vannak bejelentve, és Operations Manager szeretné figyelni őket, kövesse az alábbi lépéseket [egy Operations Manager felügyeleti csoportnak való jelentéskészítéshez](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Telepítheti a Log Analytics Windows-ügynököt a Windows rendszerű számítógépre, és jelentést készíthet mind a munkaterülettel integrált Operations Manager, mind pedig egy másik munkaterületről.

A Linux és a Windows rendszerhez készült ügynök nem csak a Azure Monitorhoz való csatlakozáshoz használható, Azure Automation a hibrid Runbook-feldolgozói szerepkör és más szolgáltatások, például a [change Tracking](../../automation/change-tracking.md), a [Update Management](../../automation/automation-update-management.md)és a [Azure Security Center](../../security-center/security-center-intro.md)üzemeltetéséhez. További információ a hibrid Runbook feldolgozói szerepkörről: [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Támogatott Windows operációs rendszerek

A Windows-ügynök a Windows operációs rendszer következő verzióit támogatja hivatalosan:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, Version 1709 és 1803
* Windows 7 SP1, Windows 8 Enterprise és Pro, valamint Windows 10 Enterprise és Pro

>[!NOTE]
>Noha a Windows Log Analytics ügynökének célja a kiszolgáló figyelési forgatókönyvének támogatása, a Windows-ügyfél a kiszolgálói operációs rendszerhez konfigurált és optimalizált munkaterhelések támogatásához is futtatható. Az ügynök támogatja a Windows-ügyfelet, azonban a megfigyelési megoldások nem az ügyfél-figyelési forgatókönyvekre összpontosítanak, kivéve, ha explicit módon van megadva.

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek

Ez a szakasz részletesen ismerteti a támogatott Linux-disztribúciókat.

A 2018 augusztusa után kiadott verzióktól kezdve a következő módosításokat végezjük a támogatási modellen:  

* Csak a kiszolgálói verziók támogatottak, nem az ügyfél.  
* Az [Azure Linux által támogatott disztribúciók](../../virtual-machines/linux/endorsed-distros.md) új verziói mindig támogatottak.  
* Minden másodlagos kiadás a felsorolt főbb verziók esetében támogatott.
* Nem támogatottak azok a verziók, amelyek átadták a gyártó támogatásának befejezési dátumát.  
* Az AMI új verziói nem támogatottak.  
* Az alapértelmezés szerint csak az SSL 1. x verziót futtató verziók támogatottak.

>[!NOTE]
>Ha olyan disztribúciót vagy verziót használ, amely jelenleg nem támogatott, és nem a támogatási modellhez igazodik, javasoljuk, hogy a tárházat elágazással lássa el, és tudomásul veszi, hogy a Microsoft támogatási szolgálata nem nyújt segítséget a villás ügynök verzióihoz.

* Amazon Linux 2017,09 (x64)
* CentOS Linux 6 (x86/x64) és 7 (x64)  
* Oracle Linux 6 és 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) és 7 (x64)
* Debian GNU/Linux 8 és 9 (x86/x64)
* Ubuntu 14,04 LTS (x86/x64), 16,04 LTS (x86/x64) és 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) és 15 (x64)

>[!NOTE]
>Az OpenSSL 1.1.0 csak x86_x64 platformokon (64-bites) és 1. x-nél korábbi OpenSSL-platformon támogatott.
>

### <a name="agent-prerequisites"></a>Ügynök előfeltételei

A következő táblázat a támogatott Linux-disztribúciók számára szükséges csomagokat mutatja be, amelyekre az ügynököt telepíteni fogja.

|Szükséges csomag |Leírás |Minimális verzió |
|-----------------|------------|----------------|
|Glibc |    GNU C könyvtár | 2.5-12 
|Openssl    | OpenSSL-kódtárak | 1.0. x vagy 1.1. x |
|Curl | cURL webes ügyfél | 7.15.5 |
|Python – ctypes | | 
|PAM | Cserélhető hitelesítési modulok | | 

>[!NOTE]
>A syslog-üzenetek összegyűjtéséhez a rsyslog vagy a syslog-ng szükséges. A syslog-események gyűjteménye nem támogatja az alapértelmezett syslog démont a Red Hat Enterprise Linux, a CentOS és a Oracle Linux verzió (sysklog) 5. verziójában. A rendszernapló-adatok ezen disztribúciók ezen verziójából való összegyűjtéséhez a rsyslog démont telepíteni és konfigurálni kell a sysklog lecserélése érdekében.

## <a name="tls-12-protocol"></a>TLS 1,2 protokoll

Annak érdekében, hogy az adatforgalom biztonsága Azure Monitor naplókba kerüljön, nyomatékosan javasoljuk, hogy konfigurálja az ügynököt legalább Transport Layer Security (TLS) 1,2 használatára. A TLS/SSL (SSL) régebbi verziói sebezhetőnek találták, és miközben jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak**.  További információkért tekintse át az [adatok biztonságos küldését a TLS 1,2 használatával](data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Hálózati tűzfalra vonatkozó követelmények

Az alábbi információk a Linux és a Windows-ügynök által Azure Monitor naplókkal való kommunikációhoz szükséges proxy-és tűzfal-konfigurációs információkat felsorolják.  

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |443-es port |Kimenő|Igen |  
|*.oms.opinsights.azure.com |443-es port |Kimenő|Igen |  
|*.blob.core.windows.net |443-es port |Kimenő|Igen |  

A Azure Governmentához szükséges tűzfal-információk: [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Ha azt tervezi, hogy a Azure Automation Hybrid Runbook Worker használatával csatlakozik az Automation szolgáltatáshoz, és regisztrálja az runbookok-vagy felügyeleti megoldásokat a környezetben, hozzá kell férnie a portszámhoz és a [hálózat konfigurálása a hibrid Runbook-feldolgozóhoz](../../automation/automation-hybrid-runbook-worker.md#network-planning)című témakörben leírt URL-címekhez. 

A Windows-és Linux-ügynök támogatja a proxykiszolgáló vagy a Log Analytics átjáró közötti kommunikációt a HTTPS protokoll használatával történő Azure Monitor.  A névtelen és az alapszintű hitelesítés (username/Password) is támogatott.  Ahhoz, hogy a Windows-ügynök közvetlenül a szolgáltatáshoz kapcsolódjon, a proxy konfigurációja a telepítés során vagy a Vezérlőpultról vagy a PowerShell-lel való [üzembe helyezés után](agent-manage.md#update-proxy-settings) van megadva.  

A Linux-ügynök esetében a proxykiszolgáló a telepítés során vagy a [telepítés után](agent-manage.md#update-proxy-settings) van megadva a proxy. conf konfigurációs fájl módosításával.  A Linux-ügynök proxyjának konfigurációs értékének szintaxisa a következő:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Ha a proxykiszolgáló nem igényli a hitelesítést, a Linux-ügynöknek továbbra is meg kell adnia egy pszeudo-felhasználót vagy jelszót. Ez lehet bármilyen Felhasználónév vagy jelszó.

|Tulajdonság| Leírás |
|--------|-------------|
|Protocol (Protokoll) | https |
|felhasználó | Opcionális Felhasználónév a proxy hitelesítéséhez |
|jelszó | Opcionális jelszó a proxy hitelesítéséhez |
|proxyhost | A proxykiszolgáló/Log Analytics átjáró címe vagy teljes tartományneve |
|port | A proxykiszolgáló/Log Analytics átjáró nem kötelező portszáma |

Például:`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Ha speciális karaktereket (például "\@") használ a jelszóban, proxy-csatlakozási hibaüzenetet kap, mert a rendszer helytelenül elemzi az értéket.  A probléma megkerüléséhez kódolja a jelszót az URL-címben egy eszköz, például a [URLDecode](https://www.urldecoder.org/)használatával.  

## <a name="install-and-configure-agent"></a>Ügynök telepítése és konfigurálása

Az Azure-előfizetésben vagy a hibrid környezetben lévő gépek közvetlen csatlakoztatása Azure Monitor naplók használatával különböző módszerekkel végezhető el a követelményektől függően. Az alábbi táblázat az egyes módszereket ismerteti, amelyekkel meghatározhatja, hogy melyik működik a legjobban a szervezetében.

|Forrás | Módszer | Leírás|
|-------|-------------|-------------|
|Azure VM| -Log Analytics virtuálisgép-bővítmény Windows vagy [Linux](../../virtual-machines/extensions/oms-linux.md) [rendszerhez](../../virtual-machines/extensions/oms-windows.md) az Azure CLI használatával vagy egy Azure Resource Manager sablonnal<br>- [manuálisan a Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md)<br>- [Azure Security Center automatikus kiépítés](../../security-center/security-center-enable-data-collection.md)| – A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és regisztrálja őket egy meglévő Azure Monitor-munkaterületen.<br>– Azure Security Center kiépítheti a Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra, ha engedélyezi a biztonsági rések és fenyegetések figyelését. Ha engedélyezve van, a telepített ügynök nélküli új vagy meglévő virtuális gépek kiépítve lesznek.|
| Windows rendszerű hibrid számítógép|[manuális telepítés](agent-windows.md) - <br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>[Resource Manager-sablon - Azure stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Telepítse a Microsoft monitoring agentet a parancssorból vagy egy automatizált módszerrel, például Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)vagy egy Azure Resource Manager sablonnal, ha központilag telepítette Microsoft Azure stack az adatközpontjában.| 
| Linux rendszerű hibrid számítógép| [Manuális telepítés](agent-linux.md)|A Linux-ügynök telepítése a GitHubon üzemeltetett burkoló-szkript hívására, illetve az ügynök manuális letöltésére és telepítésére. | 
| System Center Operations Manager|[Operations Manager integrálása Log Analytics](om-agents.md) | Konfigurálja a Operations Manager és Azure Monitor naplók közötti integrációt, hogy továbbítsa az összegyűjtött adatokat a felügyeleti csoportnak jelentő Windows rendszerű számítógépekről.|  

## <a name="next-steps"></a>Következő lépések

* Tekintse át az [adatforrásokat](agent-data-sources.md) , és Ismerje meg, hogy milyen adatforrások érhetők el az adatok Windows vagy Linux rendszerből való gyűjtéséhez. 

* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) . 

* Ismerkedjen meg azokkal a [figyelési megoldásokkal](../insights/solutions.md) , amelyek a Azure monitor funkciókat bővítik, és adatokat gyűjtenek a log Analytics munkaterületen.

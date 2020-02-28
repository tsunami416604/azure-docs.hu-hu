---
title: Log Analytics-ügynök áttekintése
description: Ez a témakör segít megérteni az adatok gyűjtéséhez és az Azure-ban üzemeltetett számítógépek figyelése a helyszíni vagy más Log Analytics-környezetet.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 365c228edd97ffcd02b86508deff4272365447f6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672140"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics-ügynök áttekintése
Az Azure Log Analytics Agent a Felhőbeli, a helyszíni gépeken és a [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)által felügyelt virtuális gépek teljes körű felügyeletére lett kifejlesztve. A Windows-és Linux-ügynökök különböző forrásokból származó összegyűjtött adatokat küldenek a Log Analytics munkaterületre Azure Monitor, valamint a figyelési megoldásban meghatározott egyedi naplókat vagy metrikákat. A Log Analytics ügynök az Azure Monitor, például a [Azure monitor for VMS](../insights/vminsights-enable-overview.md), a [Azure Security Center](/azure/security-center/)és a [Azure Automation](../../automation/automation-intro.md)által nyújtott bepillantást és egyéb szolgáltatásokat is támogatja.

Ez a cikk részletes áttekintést nyújt az ügynök, a rendszer és a hálózati követelmények és a különböző központi telepítési módszer.

> [!NOTE]
> Azt is megteheti, hogy a Microsoft monitoring Agent (MMA) vagy a OMS Linux Agent néven ismert Log Analytics ügynök.

> [!NOTE]
> Azure Diagnostics bővítmény az egyik rendelkezésre álló ügynök, amely a számítási erőforrások vendég operációs rendszeréről gyűjti a figyelési adatokat. Lásd: [a Azure monitor ügynökök áttekintése](agents-overview.md) a különböző ügynökök leírására, valamint a megfelelő ügynökök kiválasztására vonatkozó útmutatást.

## <a name="comparison-to-azure-diagnostics-extension"></a>Összehasonlítás az Azure Diagnostics bővítménnyel
Az [Azure Diagnostics bővítmény](diagnostics-extension-overview.md) a Azure monitorban is használható a figyelési adatok gyűjtésére az Azure-beli virtuális gépek vendég operációs rendszeréről. Dönthet úgy is, hogy a követelményektől függően vagy mindkettőt használja. A Azure Monitor-ügynökök részletes összehasonlítását lásd [a Azure monitor ügynökök áttekintésében](agents-overview.md) . 

A figyelembe venni kívánt fő különbségek a következők:

- Azure Diagnostics bővítmény csak az Azure Virtual Machines használatával használható. Az Log Analytics-ügynök használható az Azure-ban, a többi felhőkben és a helyszínen lévő virtuális gépekkel.
- Azure Diagnostics a bővítmény adatokat küld az Azure Storage-ba, [Azure monitor metrikákat](data-platform-metrics.md) (csak Windows) és Event Hubs. A Log Analytics ügynök adatokat gyűjt [Azure monitor naplókhoz](data-platform-logs.md).
- A Log Analytics ügynök szükséges a [megoldások](../monitor-reference.md#insights-and-core-solutions), [Azure monitor for VMS](../insights/vminsights-overview.md)és egyéb szolgáltatások, például a [Azure Security Center](/azure/security-center/)esetében.

## <a name="costs"></a>Költségek
Log Analytics ügynöknek nincs díja, de a betöltött adatokért díjat számítunk fel. A Log Analytics munkaterületen összegyűjtött adatok díjszabásával kapcsolatos részletes információkért lásd: a [használat és a költségek kezelése Azure monitor naplókkal](manage-cost-storage.md) .

## <a name="data-collected"></a>Összegyűjtött adatok
A következő táblázat felsorolja azokat az adattípusokat, amelyekkel a Log Analytics munkaterületek összegyűjthetők az összes csatlakoztatott ügynökből. Tekintse meg a [Azure monitor által figyelt adatokat?](../monitor-reference.md) az elemzések, megoldások és egyéb olyan megoldások listáját, amelyek a log Analytics-ügynököt használják más típusú adatok gyűjtésére.

| Adatforrás | Leírás |
| --- | --- |
| [Windows-eseménynaplók](data-sources-windows-events.md) | A Windows eseménynaplózási rendszernek eljuttatott információk. |
| [Syslog](data-sources-syslog.md)                     | A Linux-eseménynaplózási rendszernek eljuttatott információk. |
| [Teljesítmény](data-sources-performance-counters.md)  | Az operációs rendszer és a számítási feladatok különböző szempontjainak teljesítményét mérő numerikus értékek. |
| [IIS-naplók](data-sources-iis-logs.md)                 | A vendég operációs rendszeren futó IIS-webhelyek használati adatai. |
| [Egyéni naplók](data-sources-custom-logs.md)           | A Windows és Linux rendszerű számítógépeken található szövegfájlok eseményei. |

## <a name="data-destinations"></a>Adatelérési helyek
A Log Analytics ügynök adatokat küld egy Log Analytics munkaterületre Azure Monitor. A Windows-ügynök többkiszolgálós lehet, hogy több munkaterületre és System Center Operations Manager felügyeleti csoportokra küldje az adatküldést. A Linux-ügynök csak egyetlen célhelyre tud küldeni.

## <a name="other-services"></a>Egyéb szolgáltatások
A Linux és a Windows rendszerhez készült ügynök nem csak a Azure Monitorhoz való csatlakozáshoz használható, Azure Automation a hibrid Runbook-feldolgozói szerepkör és más szolgáltatások, például a [change Tracking](../../automation/change-tracking.md), a [Update Management](../../automation/automation-update-management.md)és a [Azure Security Center](../../security-center/security-center-intro.md)üzemeltetéséhez. További információ a hibrid Runbook feldolgozói szerepkörről: [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Telepítés és konfigurálás

Ha a Log Analytics ügynököket használja az adatok gyűjtésére, az ügynök üzembe helyezésének megtervezéséhez a következőket kell megismernie:

* A Windows-ügynököktől származó adatok összegyűjtéséhez beállíthatja, hogy az [egyes ügynökök egy vagy több munkaterületnek jelentsenek](agent-windows.md), még akkor is, ha System Center Operations Manager felügyeleti csoportnak jelentenek jelentést. A Windows-ügynök legfeljebb négy munkaterületet tud jelenteni.
* A Linux-ügynök nem támogatja a többsoros vezérlést, és csak egyetlen munkaterületre tud jelentést készíteni.
* A Windows-ügynök támogatja a [FIPS 140 szabványt](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), míg a Linux-ügynök nem támogatja azt.  

Ha System Center Operations Manager 2012 R2 vagy újabb verziót használ:

* Az egyes Operations Manager felügyeleti csoportok [csak egy munkaterülethez csatlakoztathatók](om-agents.md).
* A felügyeleti csoportnak jelentést küldő linuxos számítógépeket úgy kell konfigurálni, hogy közvetlenül egy Log Analytics munkaterületre jelentsenek. Ha a Linux rendszerű számítógépek már közvetlenül egy munkaterületre vannak bejelentve, és Operations Manager szeretné figyelni őket, kövesse az alábbi lépéseket [egy Operations Manager felügyeleti csoportnak való jelentéskészítéshez](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Telepítheti a Log Analytics Windows-ügynököt a Windows rendszerű számítógépre, és jelentést készíthet mind a munkaterülettel integrált Operations Manager, mind pedig egy másik munkaterületről.


Több módszerrel is telepítheti a Log Analytics-ügynököt, és a saját igényeinek megfelelően Azure Monitor csatlakozhat a számítógéphez. Az alábbi táblázat minden egyes metódus határozza meg, amely leginkább megfelel a szervezet emeli ki.

|Forrás | Módszer | Leírás|
|-------|-------------|-------------|
|Azure VM| [Manuálisan a Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | A Log Analytics munkaterületről telepítendő virtuális gépek meghatározása. |
| | Log Analytics virtuálisgép-bővítmény Windows vagy [Linux](../../virtual-machines/extensions/oms-linux.md) [rendszerhez](../../virtual-machines/extensions/oms-windows.md) az Azure CLI használatával vagy egy Azure Resource Manager sablonnal | A bővítmény a Log Analytics-ügynököket telepíti az Azure-beli virtuális gépeken, és regisztrálja őket egy meglévő Azure Monitor-munkaterületet. |
| | [Azure Monitor for VMs](../insights/vminsights-enable-overview.md) | Ha engedélyezi a figyelést a Azure Monitor for VMs segítségével, akkor telepíti a Log Analytics bővítményt és az ügynököt. |
| | [Automatikus kiépítés Azure Security Center](../../security-center/security-center-enable-data-collection.md) | A Azure Security Center kiépítheti a Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra, ha engedélyezi a biztonsági rések és fenyegetések figyelését. Ha engedélyezve van, a telepített ügynök nélküli új vagy meglévő virtuális gépek kiépítve lesznek. |
| Windows rendszerű hibrid számítógép| [Manuális telepítés](agent-windows.md) | Telepítse a Microsoft monitoring agentet a parancssorból. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | A telepítés automatizálása Azure Automation DSC-vel. |
| | [Resource Manager-sablon Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Ha az adatközpontban Microsoft Azure Stack üzembe helyezett, használjon Azure Resource Manager sablont.| 
| Linux rendszerű hibrid számítógép| [Manuális telepítés](../../azure-monitor/learn/quick-collect-linux-computer.md)|Telepítse az ügynököt a Linux rendszerre, a Githubon található burkoló-parancsfájl hívása során. | 
| System Center Operations Manager|[Operations Manager integrálása Log Analytics](../../azure-monitor/platform/om-agents.md) | Konfigurálja a Operations Manager és Azure Monitor naplók közötti integrációt, hogy továbbítsa az összegyűjtött adatokat a felügyeleti csoportnak jelentő Windows rendszerű számítógépekről.|  


## <a name="supported-windows-operating-systems"></a>A támogatott Windows operációs rendszerek

A Windows-ügynök hivatalosan támogatott a Windows operációs rendszer következő verziói:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, Version 1709 és 1803
* Windows 7 SP1, Windows 8 Enterprise és Pro, valamint Windows 10 Enterprise és Pro

>[!NOTE]
>Noha a Windows Log Analytics ügynökének célja a kiszolgáló figyelési forgatókönyvének támogatása, a Windows-ügyfél a kiszolgálói operációs rendszerhez konfigurált és optimalizált munkaterhelések támogatásához is futtatható. Az ügynök támogatja a Windows-ügyfelet, azonban a megfigyelési megoldások nem az ügyfél-figyelési forgatókönyvekre összpontosítanak, kivéve, ha explicit módon van megadva.

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek

Ez a szakasz ismerteti a támogatott Linux-disztribúciók részleteit.

Kiadás dátuma: 2018 augusztus után verzióval kezdődően igyekszünk a következő módosításokat a támogatási modell:  

* Csak a kiszolgáló-verziók támogatottak, nem az ügyfél.  
* Az [Azure Linux által támogatott disztribúciók](../../virtual-machines/linux/endorsed-distros.md) új verziói mindig támogatottak.  
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

Annak érdekében, hogy az adatforgalom biztonsága Azure Monitor naplóba kerüljön, nyomatékosan javasoljuk, hogy az ügynököt legalább Transport Layer Security (TLS) 1,2 használatára konfigurálja. A TLS/SSL (SSL) régebbi verziói sebezhetőnek találták, és miközben jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak**.  További információkért tekintse át az [adatok biztonságos küldését a TLS 1,2 használatával](data-security.md#sending-data-securely-using-tls-12). 


## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
A Linux-és Windows-ügynök a 443-as TCP-porton keresztül kommunikál a Azure Monitor szolgáltatással, és ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztül, tekintse át az alábbi követelményeket a hálózati konfiguráció megismeréséhez. szükséges. Ha az IT-biztonsági szabályzatok nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást, beállíthat egy [log Analytics-átjárót](gateway.md) , majd beállíthatja az ügynököt, hogy az átjárón keresztül kapcsolódjon Azure monitor naplókhoz. Az ügynök ezután fogadhatja a konfigurációs adatokat, és az összegyűjtött adatokat attól függően, hogy milyen adatgyűjtési szabályok és figyelési megoldások vannak engedélyezve a munkaterületen.

![Log Analytics ügynök kommunikációs diagramja](./media/log-analytics-agent/log-analytics-agent-01.png)


## <a name="network-firewall-requirements"></a>Hálózati tűzfalra vonatkozó követelmények
Az alábbi információk a Linux és a Windows-ügynök által Azure Monitor naplókkal való kommunikációhoz szükséges proxy-és tűzfal-konfigurációs információkat felsorolják.  

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.oms.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.blob.core.windows.net |443-as port |Kimenő|Igen |  

A Azure Governmentához szükséges tűzfal-információk: [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Ha azt tervezi, hogy a Azure Automation Hybrid Runbook Worker használatával csatlakozik az Automation szolgáltatáshoz, és regisztrálja az runbookok-vagy felügyeleti megoldásokat a környezetben, hozzá kell férnie a portszámhoz és a [hálózat konfigurálása a hibrid Runbook-feldolgozóhoz](../../automation/automation-hybrid-runbook-worker.md#network-planning)című témakörben leírt URL-címekhez. 

A Windows-és Linux-ügynök támogatja a proxykiszolgáló vagy a Log Analytics átjáró közötti kommunikációt a HTTPS protokoll használatával történő Azure Monitor.  Névtelen és alapszintű hitelesítés (felhasználónév és jelszó) támogatottak.  Ahhoz, hogy a Windows-ügynök közvetlenül a szolgáltatáshoz kapcsolódjon, a proxy konfigurációja a telepítés során vagy a Vezérlőpultról vagy a PowerShell-lel való [üzembe helyezés után](agent-manage.md#update-proxy-settings) van megadva.  

A Linux-ügynök esetében a proxykiszolgáló a telepítés során vagy a [telepítés után](agent-manage.md#update-proxy-settings) van megadva a proxy. conf konfigurációs fájl módosításával.  A Linuxos ügynök proxykonfiguráció értékének szintaxisa a következő:

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
> Ha speciális karaktereket (például "\@") használ a jelszóban, proxy-csatlakozási hibaüzenetet kap, mert a rendszer helytelenül elemzi az értéket.  A probléma megkerüléséhez kódolja a jelszót az URL-címben egy eszköz, például a [URLDecode](https://www.urldecoder.org/)használatával.  



## <a name="next-steps"></a>Következő lépések

* Tekintse át az [adatforrásokat](agent-data-sources.md) , és Ismerje meg, hogy milyen adatforrások érhetők el az adatok Windows vagy Linux rendszerből való gyűjtéséhez. 
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) . 
* Ismerkedjen meg azokkal a [figyelési megoldásokkal](../insights/solutions.md) , amelyek a Azure monitor funkciókat bővítik, és adatokat gyűjtenek a log Analytics munkaterületen.


---
title: Log Analytics-ügynök áttekintése
description: Ebből a témakörből megtudhatja, hogyan gyűjthet adatokat és figyelheti az Azure-ban, a helyszínen vagy más felhőalapú környezetben üzemeltetett számítógépeket Log Analytics használatával.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: e60fa73af9bd33c22f8489520b683f244dc46302
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655950"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics-ügynök áttekintése
Az Azure Log Analytics Agent a Felhőbeli, a helyszíni gépeken és a [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)által felügyelt virtuális gépek teljes körű felügyeletére lett kifejlesztve. A Windows-és Linux-ügynökök különböző forrásokból származó összegyűjtött adatokat küldenek a Log Analytics munkaterületre Azure Monitor, valamint a figyelési megoldásban meghatározott egyedi naplókat vagy metrikákat. A Log Analytics ügynök az Azure Monitor, például a [Azure monitor for VMS](../insights/vminsights-enable-overview.md), a [Azure Security Center](/azure/security-center/)és a [Azure Automation](../../automation/automation-intro.md)által nyújtott bepillantást és egyéb szolgáltatásokat is támogatja.

Ez a cikk részletes áttekintést nyújt az ügynökről, a rendszerről és a hálózati követelményekről, valamint a különböző üzembe helyezési módszerekről.

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

| Adatforrás | Description |
| --- | --- |
| [Windows-eseménynaplók](data-sources-windows-events.md) | A Windows eseménynaplózási rendszernek eljuttatott információk. |
| [Rendszernapló](data-sources-syslog.md)                     | A Linux-eseménynaplózási rendszernek eljuttatott információk. |
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


Több módszerrel is telepítheti a Log Analytics-ügynököt, és a saját igényeinek megfelelően Azure Monitor csatlakozhat a számítógéphez. Az alábbi táblázat az egyes módszereket ismerteti, amelyekkel meghatározhatja, hogy melyik működik a legjobban a szervezetében.

|Forrás | Metódus | Description|
|-------|-------------|-------------|
|Azure VM| [Manuálisan a Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | A Log Analytics munkaterületről telepítendő virtuális gépek meghatározása. |
| | Log Analytics virtuálisgép-bővítmény Windows vagy [Linux](../../virtual-machines/extensions/oms-linux.md) [rendszerhez](../../virtual-machines/extensions/oms-windows.md) az Azure CLI használatával vagy egy Azure Resource Manager sablonnal | A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és egy meglévő Azure Monitor-munkaterületre regisztrálja őket. |
| | [Azure Monitor virtuális gépekhez](../insights/vminsights-enable-overview.md) | Ha engedélyezi a figyelést a Azure Monitor for VMs segítségével, akkor telepíti a Log Analytics bővítményt és az ügynököt. |
| | [Automatikus kiépítés Azure Security Center](../../security-center/security-center-enable-data-collection.md) | A Azure Security Center kiépítheti a Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra, ha engedélyezi a biztonsági rések és fenyegetések figyelését. Ha engedélyezve van, a telepített ügynök nélküli új vagy meglévő virtuális gépek kiépítve lesznek. |
| Windows rendszerű hibrid számítógép| [Manuális telepítés](agent-windows.md) | Telepítse a Microsoft monitoring agentet a parancssorból. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | A telepítés automatizálása Azure Automation DSC-vel. |
| | [Resource Manager-sablon Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Ha az adatközpontban Microsoft Azure Stack üzembe helyezett, használjon Azure Resource Manager sablont.| 
| Linux rendszerű hibrid számítógép| [Manuális telepítés](../../azure-monitor/learn/quick-collect-linux-computer.md)|A Linux-ügynök telepítése a GitHubon üzemeltetett burkoló parancsfájl meghívásával. | 
| System Center Operations Manager|[Operations Manager integrálása Log Analytics](../../azure-monitor/platform/om-agents.md) | Konfigurálja a Operations Manager és Azure Monitor naplók közötti integrációt, hogy továbbítsa az összegyűjtött adatokat a felügyeleti csoportnak jelentő Windows rendszerű számítógépekről.|  


## <a name="supported-windows-operating-systems"></a>Támogatott windowsos operációs rendszerek

A Windows-ügynök a Windows operációs rendszer következő verzióit támogatja hivatalosan:

* Windows Server 2019
* Windows Server 2016, 1709-es és 1803-es verzió
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (több munkamenetet is beleértve) és Pro
* Windows 8 Enterprise és Pro 
* Windows 7 SP1

>[!NOTE]
>Noha a Windows Log Analytics ügynökének célja a kiszolgáló figyelési forgatókönyvének támogatása, a Windows-ügyfél a kiszolgálói operációs rendszerhez konfigurált és optimalizált munkaterhelések támogatásához is futtatható. Az ügynök támogatja a Windows-ügyfelet, azonban a megfigyelési megoldások nem az ügyfél-figyelési forgatókönyvekre összpontosítanak, kivéve, ha explicit módon van megadva.

## <a name="supported-linux-operating-systems"></a>Támogatott linuxos operációs rendszerek

Ez a szakasz részletesen ismerteti a támogatott Linux-disztribúciókat.

A 2018 augusztusa után kiadott verzióktól kezdve a következő módosításokat végezjük a támogatási modellen:  

* Csak a kiszolgálói verziók támogatottak, nem az ügyfél.  
* Koncentráljon az [Azure Linux által támogatott disztribúciók](../../virtual-machines/linux/endorsed-distros.md)bármelyikére. Vegye figyelembe, hogy az Azure Linux új disztribúciója/verziója esetében némi késéssel rendelkezik, és a Log Analytics Linux-ügynök támogatja.
* Minden másodlagos kiadás a felsorolt főbb verziók esetében támogatott.
* Nem támogatottak azok a verziók, amelyek átadták a gyártó támogatásának befejezési dátumát.  
* Az AMI új verziói nem támogatottak.  
* Az alapértelmezés szerint csak az SSL 1. x verziót futtató verziók támogatottak.

>[!NOTE]
>Ha olyan disztribúciót vagy verziót használ, amely jelenleg nem támogatott, és nem a támogatási modellhez igazodik, javasoljuk, hogy a tárházat elágazással lássa el, és tudomásul veszi, hogy a Microsoft támogatási szolgálata nem nyújt segítséget a villás ügynök verzióihoz.

* Amazon Linux 2017,09 (x64)
* CentOS Linux 6 (x64) és 7 (x64)  
* Oracle Linux 6 és 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64), 7 (x64) és 8 (x64)
* Debian GNU/Linux 8 és 9 (x64)
* Ubuntu 14,04 LTS (x86/x64), 16,04 LTS (x64) és 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) és 15 (x64)

>[!NOTE]
>Az OpenSSL 1.1.0 csak x86_x64 platformokon (64-bites) és 1. x-nél korábbi OpenSSL-platformon támogatott.
>

### <a name="agent-prerequisites"></a>Ügynök előfeltételei

A következő táblázat a támogatott Linux-disztribúciók számára szükséges csomagokat mutatja be, amelyekre az ügynököt telepíteni fogja.

|Szükséges csomag |Description |Minimális verzió |
|-----------------|------------|----------------|
|Glibc |    GNU C könyvtár | 2.5-12 
|Openssl    | OpenSSL-kódtárak | 1.0. x vagy 1.1. x |
|Curl | cURL webes ügyfél | 7.15.5 |
|Python – ctypes | | 
|PAM | Cserélhető hitelesítési modulok | | 

>[!NOTE]
>A syslog-üzenetek összegyűjtéséhez a rsyslog vagy a syslog-ng szükséges. A syslog-események gyűjteménye nem támogatja az alapértelmezett syslog démont a Red Hat Enterprise Linux, a CentOS és a Oracle Linux verzió (sysklog) 5. verziójában. A rendszernapló-adatok ezen disztribúciók ezen verziójából való összegyűjtéséhez a rsyslog démont telepíteni és konfigurálni kell a sysklog lecserélése érdekében.

## <a name="tls-12-protocol"></a>TLS 1,2 protokoll

Annak érdekében, hogy az adatforgalom biztonsága Azure Monitor naplóba kerüljön, nyomatékosan javasoljuk, hogy az ügynököt legalább Transport Layer Security (TLS) 1,2 használatára konfigurálja. A TLS/SSL (SSL) régebbi verziói sebezhetőnek találták, és miközben jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak**.  További információkért tekintse át az [adatok biztonságos küldését a TLS 1,2 használatával](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>SHA-2 kód aláírásának támogatási követelménye Windows esetén
A Windows-ügynök a 2020-as augusztus 17-én kizárólag az SHA-2 aláírást fogja használni. Ez a változás hatással lesz az ügyfelekre az Azure-szolgáltatások (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP) részeként egy örökölt operációs rendszert használó Log Analytics ügynökkel. A módosítás nem követeli meg az ügyfelek beavatkozását, kivéve, ha az ügynököt örökölt operációsrendszer-verzióra (Windows 7, Windows Server 2008 R2 és Windows Server 2008) futtatja. Az örökölt operációsrendszer-verzión futó ügyfeleknek a következő műveleteket kell végrehajtaniuk a gépen, mielőtt augusztus 17-én, 2020-es vagy az ügynökük leállítja az adatok küldését az Log Analytics munkaterületekre:

1. Telepítse az operációs rendszerének legújabb szervizcsomagját. A szervizcsomag szükséges verziói a következők:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Telepítse az operációs rendszer SHA-2 aláírására vonatkozó Windows-frissítéseket az [2019-es SHA-2 kód-aláírás támogatási követelménye a Windows és a WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus) szolgáltatáshoz.
3. Frissítsen a Windows-ügynök legújabb verziójára (10.20.18029-verzió).
4. Javasoljuk, hogy az ügynököt a [TLS 1,2 használatára](agent-windows.md#configure-agent-to-use-tls-12)konfigurálja. 


## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
A Linux-és Windows-ügynök a 443-as TCP-porton keresztül kommunikál a Azure Monitor szolgáltatással, és ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztül, tekintse át az alábbi követelményeket a szükséges hálózati konfiguráció megismeréséhez. Ha az IT-biztonsági szabályzatok nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást, beállíthat egy [log Analytics-átjárót](gateway.md) , majd beállíthatja az ügynököt, hogy az átjárón keresztül kapcsolódjon Azure monitor naplókhoz. Az ügynök ezután fogadhatja a konfigurációs adatokat, és az összegyűjtött adatokat attól függően, hogy milyen adatgyűjtési szabályok és figyelési megoldások vannak engedélyezve a munkaterületen.

![Log Analytics-ügynök kommunikációs diagramja](./media/log-analytics-agent/log-analytics-agent-01.png)

A következő táblázat a Linux-és Windows-ügynökök Azure Monitor naplókkal való kommunikációhoz szükséges proxy-és tűzfal-konfigurációs információit sorolja fel.

### <a name="firewall-requirements"></a>A tűzfalra vonatkozó követelmények

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |443-es port |Bejövő és kimenő|Igen |  
|*.oms.opinsights.azure.com |443-es port |Bejövő és kimenő|Igen |  
|*.blob.core.windows.net |443-es port |Bejövő és kimenő|Igen |
|*.azure-automation.net |443-es port |Bejövő és kimenő|Igen |

A Azure Governmentához szükséges tűzfal-információk: [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Ha azt tervezi, hogy a Azure Automation Hybrid Runbook Worker használatával csatlakozik az Automation szolgáltatáshoz, és regisztrálja az runbookok-vagy felügyeleti megoldásokat a környezetben, hozzá kell férnie a portszámhoz és a [hálózat konfigurálása a hibrid Runbook-feldolgozóhoz](../../automation/automation-hybrid-runbook-worker.md#network-planning)című témakörben leírt URL-címekhez. 

### <a name="proxy-configuration"></a>Proxy konfigurálása

A Windows-és Linux-ügynök támogatja a proxykiszolgáló vagy a Log Analytics átjáró közötti kommunikációt a HTTPS protokoll használatával történő Azure Monitor.  A névtelen és az alapszintű hitelesítés (username/Password) is támogatott.  Ahhoz, hogy a Windows-ügynök közvetlenül a szolgáltatáshoz kapcsolódjon, a proxy konfigurációja a telepítés során vagy a Vezérlőpultról vagy a PowerShell-lel való [üzembe helyezés után](agent-manage.md#update-proxy-settings) van megadva.  

A Linux-ügynök esetében a proxykiszolgáló a telepítés során vagy a [telepítés után](agent-manage.md#update-proxy-settings) van megadva a proxy. conf konfigurációs fájl módosításával.  A Linux-ügynök proxyjának konfigurációs értékének szintaxisa a következő:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Ha a proxykiszolgáló nem igényli a hitelesítést, a Linux-ügynöknek továbbra is meg kell adnia egy pszeudo-felhasználót vagy jelszót. Ez lehet bármilyen Felhasználónév vagy jelszó.

|Tulajdonság| Description |
|--------|-------------|
|Protokoll | https |
|felhasználó! | Opcionális Felhasználónév a proxy hitelesítéséhez |
|jelszó | Opcionális jelszó a proxy hitelesítéséhez |
|proxyhost | A proxykiszolgáló/Log Analytics átjáró címe vagy teljes tartományneve |
|port | A proxykiszolgáló/Log Analytics átjáró nem kötelező portszáma |

Például:`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Ha a jelszóban speciális karaktereket (például " \@ ") használ, a rendszer proxy-csatlakozási hibát kap, mert az érték helytelenül van elemezve.  A probléma megkerüléséhez kódolja a jelszót az URL-címben egy eszköz, például a [URLDecode](https://www.urldecoder.org/)használatával.  



## <a name="next-steps"></a>További lépések

* Tekintse át az [adatforrásokat](agent-data-sources.md) , és Ismerje meg, hogy milyen adatforrások érhetők el az adatok Windows vagy Linux rendszerből való gyűjtéséhez. 
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) . 
* Ismerkedjen meg azokkal a [figyelési megoldásokkal](../insights/solutions.md) , amelyek a Azure monitor funkciókat bővítik, és adatokat gyűjtenek a log Analytics munkaterületen.


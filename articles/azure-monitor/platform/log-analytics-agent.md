---
title: Log Analytics-ügynök – áttekintés
description: Ez a témakör segít megérteni, hogyan gyűjthet adatokat és figyelheti az Azure-ban, a helyszíni vagy más felhőalapú környezetben üzemeltetett számítógépeket a Log Analytics szolgáltatással.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: d52d8e6d0f6e3325b5c5cdc9a2e21654e6a2b621
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520726"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics-ügynök – áttekintés
Az Azure Log Analytics-ügynök a virtuális gépek teljes körű felügyeletére lett kifejlesztve bármely felhőben, helyszíni gépben és a [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)által figyelt gépeken. A Windows- és Linux-ügynökök különböző forrásokból származó összegyűjtött adatokat küldenek a Log Analytics-munkaterületre az Azure Monitorban, valamint a figyelési megoldásban meghatározott egyedi naplókba vagy metrikákba. A Log Analytics-ügynök az Azure Monitor ban is támogatja az elemzéseket és más szolgáltatásokat, például [az Azure Monitor virtuális gépekhez,](../insights/vminsights-enable-overview.md)az Azure Security [Centert](/azure/security-center/)és az [Azure Automationszolgáltatást.](../../automation/automation-intro.md)

Ez a cikk részletes áttekintést nyújt az ügynökről, a rendszer- és hálózati követelményekről, valamint a különböző telepítési módszerekről.

> [!NOTE]
> A Log Analytics-ügynök a Microsoft Monitoring Agent (MMA) vagy AZ OMS Linux-ügynök néven is megjelenhet.

> [!NOTE]
> Az Azure Diagnostics bővítmény az egyik olyan ügynök, amely a számítási erőforrások vendég operációs rendszeréből figyelési adatokat gyűjthet. Tekintse [meg az Azure Monitor-ügynökök áttekintése](agents-overview.md) a különböző ügynökök leírását és útmutatást a megfelelő ügynökök kiválasztásához a követelményeknek.

## <a name="comparison-to-azure-diagnostics-extension"></a>Összehasonlítás az Azure diagnosztikai bővítményéhez
Az [Azure-diagnosztikai bővítmény](diagnostics-extension-overview.md) az Azure Monitorban is használható figyelési adatok gyűjtésére az Azure virtuális gépek vendég operációs rendszeréből. Az igényeitől függően választhatja az egyik vagy mindkettő használatát. Az Azure Monitor-ügynökök részletes összehasonlítása [az Azure Monitor-ügynökök áttekintése című témakörben olvashat.](agents-overview.md) 

A legfontosabb figyelembe vemandó különbségek a következők:

- Az Azure Diagnostics Extension csak azure-beli virtuális gépekkel használható. A Log Analytics-ügynök az Azure-ban, más felhőkben és a helyszíni virtuális gépekkel is használható.
- Az Azure Diagnostics bővítmény adatokat küld az Azure Storage, [az Azure Monitor metrikák](data-platform-metrics.md) (csak Windows) és az Event Hubs. A Log Analytics-ügynök adatokat gyűjt az [Azure Monitor naplók](data-platform-logs.md).
- A Log Analytics-ügynök szükséges [a megoldásokhoz](../monitor-reference.md#insights-and-core-solutions), az [Azure Monitor virtuális gépekhez](../insights/vminsights-overview.md)és más szolgáltatásokhoz, például az [Azure Security Centerhez.](/azure/security-center/)

## <a name="costs"></a>Költségek
Nincs költség a Log Analytics-ügynök, de előfordulhat, hogy a bevitt adatok at. A Log Analytics-munkaterületen gyűjtött adatok díjszabásáról az [Azure Monitor naplókkal](manage-cost-storage.md) című részletes információkért tekintse meg a használat és a költségek kezelése című részt.

## <a name="data-collected"></a>Összegyűjtött adatok
Az alábbi táblázat felsorolja, hogy milyen típusú adatokat konfigurálhat úgy, hogy a Log Analytics-munkaterület et az összes csatlakoztatott ügynöktől összegyűjtse. A Log Analytics-ügynök segítségével más típusú adatok gyűjtésére szolgáló elemzési adatok, megoldások és egyéb megoldások listáját olvassa [el.](../monitor-reference.md)

| Adatforrás | Leírás |
| --- | --- |
| [Windows eseménynaplók](data-sources-windows-events.md) | A Windows eseménynaplózási rendszerének küldött adatok. |
| [Rendszernapló](data-sources-syslog.md)                     | A Linux eseménynaplózási rendszernek küldött információk. |
| [Teljesítmény](data-sources-performance-counters.md)  | Az operációs rendszer és a munkaterhelések különböző aspektusainak teljesítményét mérő numerikus értékek. |
| [IIS-naplók](data-sources-iis-logs.md)                 | A vendég operációs rendszeren futó IIS-webhelyek használati adatai. |
| [Egyéni naplók](data-sources-custom-logs.md)           | Szöveges fájlok eseményei Windows és Linux rendszerű számítógépeken egyaránt. |

## <a name="data-destinations"></a>Adatcélok
A Log Analytics-ügynök adatokat küld egy Log Analytics-munkaterületre az Azure Monitorban. A Windows-ügynök többhelyű, így több munkaterületre és a System Center Operations Manager felügyeleti csoportokba is küldhet adatokat. A Linux-ügynök csak egyetlen célhelyre küldhet.

## <a name="other-services"></a>Egyéb szolgáltatások
A Linux és windows ügynök nem csak az Azure Monitorhoz való csatlakozáshoz szolgál, hanem támogatja az Azure Automation-t is a hibrid Runbook-feldolgozószerepkör és más szolgáltatások, például [a változáskövetés,](../../automation/change-tracking.md) [az Update Management](../../automation/automation-update-management.md)és az Azure Security Center [üzemeltetéséhez.](../../security-center/security-center-intro.md) A hibrid runbook-feldolgozói szerepkörről az [Azure Automation hibrid runbook-feldolgozója](../../automation/automation-hybrid-runbook-worker.md)című témakörben talál további információt.  

## <a name="installation-and-configuration"></a>Telepítés és konfigurálás

Amikor a Log Analytics-ügynökök et használja az adatok gyűjtésére, az ügynök telepítésének megtervezéséhez meg kell értenie a következőket:

* Ha adatokat szeretne gyűjteni a Windows-ügynököktől, [beállíthatja, hogy az egyes ügynökök egy vagy több munkaterületnek jelentsenek,](agent-windows.md)még akkor is, ha a System Center Operations Manager felügyeleti csoportjának jelent. A Windows-ügynök legfeljebb négy munkaterületet jelenthet.
* A Linux-ügynök nem támogatja a több-homing, és csak jelentést egyetlen munkaterületre.
* A Windows-ügynök támogatja a [FIPS 140 szabványt,](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)míg a Linux-ügynök nem támogatja azt.  

Ha a System Center Operations Manager 2012 R2 vagy újabb verziót használja:

* Minden Operations Manager felügyeleti csoport [csak egy munkaterülethez csatlakoztatható.](om-agents.md)
* A felügyeleti csoportnak jelentést tevő Linux-számítógépeket úgy kell konfigurálni, hogy közvetlenül a Log Analytics-munkaterületnek jelentsenek. Ha a Linux-számítógépek már közvetlenül egy munkaterületre jelentenek, és az Operations Manager segítségével szeretné figyelni őket, az alábbi lépésekkel [jelentheti az Operations Manager felügyeleti csoportjának.](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)
* Telepítheti a Log Analytics Windows-ügynök a Windows számítógépen, és azt jelentést mind Operations Manager integrált munkaterületet, és egy másik munkaterületet.


Több módszer is rendelkezésre áll a Log Analytics-ügynök telepítéséhez, és a számítógép csatlakoztatása az Azure Monitorhoz a követelményektől függően. Az alábbi táblázat kiemeli az egyes módszereket annak meghatározására, hogy melyik működik a legjobban a szervezetben.

|Forrás | Módszer | Leírás|
|-------|-------------|-------------|
|Azure VM| [Manuálisan az Azure Portalról](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Adja meg a virtuális gépeket a Log Analytics-munkaterületről való üzembe helyezéshez. |
| | Log Analytics virtuálisgép-bővítmény [Windows](../../virtual-machines/extensions/oms-windows.md) vagy [Linux](../../virtual-machines/extensions/oms-linux.md) rendszeren az Azure CLI vagy egy Azure Resource Manager-sablon használatával | A bővítmény telepíti a Log Analytics-ügynököt az Azure virtuális gépeken, és beállítja őket egy meglévő Azure Monitor-munkaterületre. |
| | [Azure Monitor virtuális gépekhez](../insights/vminsights-enable-overview.md) | Ha engedélyezi a figyelést az Azure Monitor virtuális gépekhez, telepíti a Log Analytics bővítményt és ügynököt. |
| | [Az Azure Security Center automatikus kiépítése](../../security-center/security-center-enable-data-collection.md) | Az Azure Security Center kiépítheti a Log Analytics-ügynököt az összes támogatott Azure-beli virtuális gépre és bármely új, ha engedélyezi a biztonsági rések és fenyegetések figyelését. Ha engedélyezve van, minden új vagy meglévő virtuális gép telepített ügynök nélkül lesz kiépítve. |
| Windows rendszerű hibrid számítógép| [Kézi telepítés](agent-windows.md) | Telepítse a Microsoft Monitoring ügynököt a parancssorból. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatizálja a telepítést az Azure Automation DSC segítségével. |
| | [Erőforrás-kezelő sablon az Azure Stackkel](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Használjon Egy Azure Resource Manager-sablont, ha üzembe helyezte a Microsoft Azure Stacket az adatközpontjában.| 
| Linux rendszerű hibrid számítógép| [Kézi telepítés](../../azure-monitor/learn/quick-collect-linux-computer.md)|Telepítse a Linux-ügynök hívása a GitHubon üzemeltetett wrapper-script. | 
| System Center Operations Manager|[Az Operations Manager integrálása a Log Analytics szolgáltatással](../../azure-monitor/platform/om-agents.md) | Konfigurálja az Operations Manager és az Azure Monitor naplói közötti integrációt, hogy a Windows-számítógépekről gyűjtött adatokat továbbítsa egy felügyeleti csoportnak.|  


## <a name="supported-windows-operating-systems"></a>Támogatott windowsos operációs rendszerek

A Windows operációs rendszer következő verziói hivatalosan támogatottak a Windows-ügynök számára:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, 1709-es és 1803-as verzió
* Windows 7 SP1, Windows 8 Enterprise és Pro, valamint Windows 10 Enterprise és Pro

>[!NOTE]
>Bár a Windows Log Analytics-ügynökét a kiszolgálófigyelési forgatókönyvek támogatására tervezték, tisztában vagyunk azzal, hogy a Windows-ügyfél futtatható a kiszolgáló operációs rendszeréhez konfigurált és optimalizált munkaterhelések támogatására. Az ügynök támogatja a Windows-ügyfelet, azonban a figyelési megoldásaink nem összpontosítanak az ügyfélfigyelési forgatókönyvekre, kivéve, ha kifejezetten meg van jelentve.

## <a name="supported-linux-operating-systems"></a>Támogatott linuxos operációs rendszerek

Ez a rész a támogatott Linux-disztribúciók részleteit tartalmazza.

A 2018 augusztusa után kiadott verziókkal kezdve a következő módosításokat hajtjuk végre támogatási modellünkön:  

* Csak a kiszolgálóverziók támogatottak, az ügyfél nem.  
* Fókuszban támogatást bármely [Az Azure Linux által támogatott disztribúciók](../../virtual-machines/linux/endorsed-distros.md). Vegye figyelembe, hogy előfordulhat, hogy némi késés között egy új disztribúció/verzió, hogy az Azure Linux által jóváhagyott, és támogatja a Log Analytics Linux-ügynök.
* Minden kisebb kiadás támogatott minden felsorolt főverzióhoz.
* Azok a verziók, amelyek a gyártó támogatási dátumának lejárta korukat múlták alá, nem támogatottak.  
* Az AMI új verziói nem támogatottak.  
* Alapértelmezés szerint csak az SSL 1.x-et futtató verziók támogatottak.

>[!NOTE]
>Ha olyan leválasztót vagy verziót használ, amely jelenleg nem támogatott, és nem igazodik a támogatási modellünkhöz, javasoljuk, hogy elágazása legyen ennek a tárháznak, elismerve, hogy a Microsoft támogatási támogatása nem nyújt segítséget a villás ügynökverziókhoz.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) és 7 (x64)  
* Oracle Linux 6 és 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) és 7 (x64)
* Debian GNU/Linux 8 és 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) és 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) és 15 (x64)

>[!NOTE]
>Az OpenSSL 1.1.0 csak x86_x64 platformokon (64 bites) támogatott, és az 1.x-nél korábbi OpenSSL egyetlen platformon sem támogatott.
>

### <a name="agent-prerequisites"></a>Ügynök előfeltételei

Az alábbi táblázat kiemeli a támogatott Linux-disztribúciókhoz szükséges csomagokat, amelyekre az ügynök telepítve lesz.

|Szükséges csomag |Leírás |Minimális verzió |
|-----------------|------------|----------------|
|Glibc között |    GNU C Könyvtár | 2.5-12 
|Openssl    | OpenSSL-könyvtárak | 1.0.x vagy 1.1.x |
|Curl | cURL webes ügyfél | 7.15.5 |
|Python-ctypes | | 
|PAM | Cserélhető hitelesítési modulok | | 

>[!NOTE]
>Vagy rsyslog vagy syslog-ng szükséges a syslog üzenetek gyűjtéséhez. A Red Hat Enterprise Linux, CentOS és Oracle Linux verzió (sysklog) 5-ös verziójának alapértelmezett syslog démonja nem támogatott a syslog eseménygyűjteményesetében. A disztribúciók ezen verziójából származó syslog adatok gyűjtéséhez a rsyslog démont telepíteni kell, és úgy kell konfigurálni, hogy cserélje ki a sysklog-ot.

## <a name="tls-12-protocol"></a>TLS 1.2 protokoll

Az Azure Monitor naplóiba történő átvitel során az adatok biztonságának biztosítása érdekében javasoljuk, hogy konfigurálja az ügynököt legalább transport layer security (TLS) 1.2 használatára. A TLS/Secure Sockets Layer (SSL) régebbi verziói sebezhetőnek bizonyultak, és bár jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak.**  További információkért tekintse át [az Adatok biztonságos küldése a TLS 1.2 használatával](data-security.md#sending-data-securely-using-tls-12)című, című további című információt. 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Az SHA-2 kódaláíró támogatási követelménye a Windows rendszerhez
A Windows-ügynök 2020. Ez a módosítás hatással lesz az ügyfelek segítségével a Log Analytics ügynök egy örökölt operációs rendszer részeként bármely Azure-szolgáltatás (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP). A módosítás csak akkor igényel ügyfélműveletet, ha az ügynököt örökölt operációsrendszer-verzión (Windows 7, Windows Server 2008 R2 és Windows Server 2008) futtatja. Az örökölt operációsrendszer-verzión futó ügyfeleknek 2020.

1. Telepítse az operációs rendszer hez szükséges legújabb szervizcsomagot. A szervizcsomag szükséges verziói a következők:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Telepítse az Operációs rendszer Windows-frissítéseit aláíró SHA-2-t a [Windows és a WSUS 2019 SHA-2 kódaláíró támogatási követelményében](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus) leírtak szerint
3. Frissítés a Windows-ügynök legújabb verziójára (10.20.18029.)
4. Javasoljuk, hogy az ügynököt a [TLS 1.2 használatára](agent-windows.md#configure-agent-to-use-tls-12)állítsa be. 


## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
A Linux és a Windows ügynök kommunikál az Azure Monitor szolgáltatás tcp porton keresztül 443, és ha a gép csatlakozik egy tűzfal vagy proxy kiszolgáló n keresztül kommunikálni az interneten keresztül, tekintse át az alábbi követelményeket, hogy a szükséges hálózati konfiguráció. Ha az informatikai biztonsági házirendek nem teszik lehetővé a hálózati számítógépek az internethez való csatlakozást, beállíthatja a [Log Analytics-átjárót,](gateway.md) majd beállíthatja az ügynököt, hogy az átjárón keresztül csatlakozzon az Azure Monitor naplóihoz. Az ügynök ezután fogadhatkonfigurációs információkat, és elküldheti az összegyűjtött adatokat attól függően, hogy milyen adatgyűjtési szabályokat és figyelési megoldásokat engedélyezett a munkaterületen.

![Log Analytics-ügynök kommunikációs diagramja](./media/log-analytics-agent/log-analytics-agent-01.png)

Az alábbi táblázat felsorolja a proxy- és tűzfal-konfigurációs információkat, amelyek szükségesek ahhoz, hogy a Linux és a Windows-ügynökök kommunikálhassanak az Azure Monitor naplóival.

### <a name="firewall-requirements"></a>Tűzfalra vonatkozó követelmények

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |443-as port |Bejövő és kimenő|Igen |  
|*.oms.opinsights.azure.com |443-as port |Bejövő és kimenő|Igen |  
|*.blob.core.windows.net |443-as port |Bejövő és kimenő|Igen |
|*.azure-automation.net |443-as port |Bejövő és kimenő|Igen |
|*.azure.com |443-as port|Bejövő és kimenő|Igen |

Az Azure Government számára szükséges tűzfalinformációkat az [Azure Government felügyeleti témakörben talál.](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs) 

Ha azt tervezi, hogy az Azure Automation hybrid Runbook Worker használatával csatlakozik az Automation szolgáltatáshoz, és regisztráljon az Automation szolgáltatással a runbookok vagy felügyeleti megoldások használatához a környezetben, akkor hozzá kell férnie a portszámhoz és a [Hálózat konfigurálása a hibrid runbook-feldolgozóhoz](../../automation/automation-hybrid-runbook-worker.md#network-planning)című részben leírt URL-címekhez. 

### <a name="proxy-configuration"></a>Proxy konfigurálása

A Windows és Linux ügynök támogatja a kommunikációt egy proxykiszolgálón vagy a Log Analytics átjárón keresztül az Azure Monitor a HTTPS protokoll használatával.  Mind a névtelen, mind az alapfokú hitelesítés (felhasználónév/jelszó) támogatott.  A közvetlenül a szolgáltatáshoz csatlakoztatott Windows-ügynök esetében a proxykonfiguráció a telepítés során vagy a Vezérlőpultról vagy a PowerShellen [történő telepítés után](agent-manage.md#update-proxy-settings) van megadva.  

A Linux-ügynök esetében a proxykiszolgáló a telepítés során vagy a [telepítés után](agent-manage.md#update-proxy-settings) a proxy.conf konfigurációs fájl módosításával van megadva.  A Linux-ügynök proxykonfigurációs értéke a következő szintaxissal rendelkezik:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Ha a proxykiszolgáló nem igényli a hitelesítést, a Linux-ügynök nek továbbra is meg kell adnia egy pszeudo felhasználót/jelszót. Ez bármilyen felhasználónév vagy jelszó lehet.

|Tulajdonság| Leírás |
|--------|-------------|
|Protocol (Protokoll) | https |
|felhasználó! | Nem kötelező felhasználónév a proxyhitelesítéshez |
|jelszó | Választható jelszó a proxyhitelesítéshez |
|proxyhost | A proxykiszolgáló/Log Analytics átjáró címe vagy teljes tartományszáma |
|port | A proxykiszolgáló/Log Analytics átjáró választható portszáma |

Például:`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Ha speciális karaktereket használ, például "\@" a jelszóban, proxycsatlakozási hibát kap, mert az érték helytelenül van elemezve.  A probléma kerülő megoldásához kódolja a jelszót az URL-címben egy olyan eszközzel, mint például az [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>További lépések

* Tekintse át az [adatforrásokat,](agent-data-sources.md) hogy megértse a Windows vagy Linux rendszerből származó adatok gyűjtéséhez rendelkezésre álló adatforrásokat. 
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez. 
* Ismerje meg [az](../insights/solutions.md) okat, amelyek funkciókat adnak hozzá az Azure Monitorhoz, és adatokat gyűjtenek a Log Analytics-munkaterületre.


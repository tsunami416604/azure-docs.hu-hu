---
title: Adatforrások összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az adatforrások az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: 9d2d1985b23e1c7f5e0f7d9fd2795bd85e28ace0
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668766"
---
# <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

Az Azure Sentinelhez először csatlakoznia kell az adatforrásokhoz. Az Azure Sentinel számos, a Microsoft-megoldások számára elérhető összekötővel rendelkezik, és valós idejű integrációt biztosít, beleértve a Microsoft veszélyforrások elleni védelmi megoldásait és Microsoft 365 forrásait, beleértve az Office 365, az Azure AD, az Azure ATP és a Microsoft Cloud App Security és így tovább. Emellett beépített összekötők találhatók a nem Microsoft-megoldások szélesebb körű biztonsági ökoszisztémájában. Az adatforrások az Azure Sentinel szolgáltatással való összekapcsolásához használhatja a Common Event Format, a syslog vagy a REST-API-t is.  

1. A menüben válassza az **adatösszekötők**lehetőséget. Ezen a lapon megtekintheti az Azure Sentinel által biztosított összekötők teljes listáját, valamint azok állapotát. Válassza ki a csatlakoztatni kívánt összekötőt, és válassza az **összekötő lap megnyitása**lehetőséget. 

   ![Adatgyűjtők](./media/collect-data/collect-data-page.png)

1. Az adott összekötő lapon győződjön meg arról, hogy teljesítette az összes előfeltételt, és kövesse az utasításokat az adat Azure Sentinelhez való csatlakozásához. Eltarthat egy ideig, amíg a naplók megkezdik a szinkronizálást az Azure Sentinel használatával. A csatlakozást követően megjelenik a **kapott** adatmennyiség és az adattípusok kapcsolati állapota.

   ![Gyűjtők összekapcsolása](./media/collect-data/opened-connector-page.png)
  
1. A **következő lépések** lapon lekérheti, hogy az Azure Sentinel milyen adattípust biztosít a beépített tartalomhoz.

   ![Adatgyűjtők](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Adatkapcsolatok módszerei

Az Azure Sentinel a következő adatkapcsolási módszereket támogatja:

- **Szolgáltatás-szolgáltatás integrációja**:<br> Egyes szolgáltatások natív módon csatlakoznak, mint például az AWS és a Microsoft Services, ezek a szolgáltatások kihasználják az Azure Foundation-t a Box-integrációhoz, a következő megoldások néhány kattintással csatlakoztathatók:
    - [Amazon Web Services – CloudTrail](connect-aws.md)
    - [Azure-tevékenység](connect-azure-activity.md)
    - [Azure AD-naplók és-bejelentkezések](connect-azure-active-directory.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure komplex veszélyforrások elleni védelem](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Tartománynév-kiszolgáló](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Microsoft webalkalmazási tűzfal](connect-microsoft-waf.md)
    - [Windows tűzfal](connect-windows-firewall.md)
    - [Windows biztonsági események](connect-windows-security-events.md)

- **Külső megoldások API-n keresztül**: egyes adatforrások a csatlakoztatott adatforrás által biztosított API-k használatával kapcsolódnak egymáshoz. A legtöbb biztonsági technológia jellemzően olyan API-kat biztosít, amelyeken keresztül az eseménynaplók kérhetők le. Az API-k az Azure Sentinelhez csatlakoznak, és konkrét adattípusokat gyűjtenek, és elküldhetik azokat az Azure Log Analyticsba. Az API-n keresztül csatlakoztatott berendezések a következők:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen tűzfal](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (biztonság)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies integrálása](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Külső megoldások az ügynökön keresztül**: az Azure Sentinel minden más olyan adatforráshoz csatlakoztatható, amely valós idejű naplózást végez a syslog protokoll használatával egy ügynökön keresztül. <br>A legtöbb készülék a syslog protokollt használja az olyan események küldésére, amelyek magukban foglalják a naplót és a naplóval kapcsolatos információkat. A naplók formátuma változó, de a legtöbb készülék támogatja a Common Event Format (CEF) alapú formázást a naplók adataihoz. <br>Az Log Analytics ügynökön alapuló Azure Sentinel-ügynök átalakítja a CEF formázott naplókat olyan formátumba, amelyet a Log Analytics betölt. A készülék típusától függően az ügynököt közvetlenül a készülékre vagy egy dedikált Linux-kiszolgálóra telepíti. A Linux-ügynök a syslog démontól érkező eseményeket fogad UDP-n keresztül, de ha egy Linux rendszerű gépen nagy mennyiségű syslog-eseményt kell gyűjteni, a rendszer a syslog démonból az ügynököt és onnan Log Analytics.
    - Tűzfalak, proxyk és végpontok:
        - [Ellenőrzési pont](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop-megjelenítés (x)](connect-extrahop.md)
        - [F5 billentyűt](connect-f5.md)
        - [Forcepoint termékek](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto hálózatok](connect-paloalto.md)
        - [Az egyik identitás védelme](connect-one-identity.md)
        - [Egyéb CEF készülékek](connect-common-event-format.md)
        - [Egyéb syslog-készülékek](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - DLP-megoldások
    - [Veszélyforrások elleni intelligencia szolgáltatók](connect-threat-intelligence.md)
    - [DNS-gépek](connect-dns.md) – közvetlenül a DNS-gépen telepített ügynök
    - Linux-kiszolgálók
    - Egyéb felhők
    
## Ügynök-csatlakoztatási beállítások<a name="agent-options"></a>

Ahhoz, hogy a külső berendezést az Azure Sentinelhez lehessen kapcsolni, az ügynököt egy dedikált gépen (virtuális gépen vagy helyszínen) kell telepíteni a készülék és az Azure Sentinel közötti kommunikáció támogatásához. Az ügynököt automatikusan vagy manuálisan is üzembe helyezheti. Az automatikus központi telepítés csak akkor érhető el, ha a dedikált számítógép egy új, az Azure-ban létrehozott virtuális gép. 


![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Azt is megteheti, hogy manuálisan telepítheti az ügynököt egy meglévő Azure-beli virtuális gépre, egy másik felhőben lévő virtuális gépre vagy egy helyszíni gépre.

![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Adattípusok leképezése az Azure Sentinel-kapcsolatok beállításaival


| **Adattípus** | **Kapcsolódás** | **Adatösszekötő?** | **Megjegyzések** |
|------|---------|-------------|------|
| AWSCloudTrail | [Az AWS összekötése](connect-aws.md) | V | |
| AzureActivity | Az Azure-tevékenység és a Tevékenységnaplók [összekapcsolása](connect-azure-activity.md) [– Áttekintés](../azure-monitor/platform/platform-logs-overview.md)| V | |
| AuditLogs | [Az Azure AD összekötése](connect-azure-active-directory.md)  | V | |
| SigninLogs | [Az Azure AD összekötése](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Azure Diagnostics](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Jelentések Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Azure Information Protection összekötése](connect-azure-information-protection.md)  | V | Ez általában az adattípuson kívül a **InformationProtectionEvents** függvényt használja. További információ: [a jelentések módosítása és egyéni lekérdezések létrehozása](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Traffic analitikai séma](../network-watcher/traffic-analytics.md) [Traffic Analytics](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [CEF összekötése](connect-common-event-format.md)  | V | |
| OfficeActivity | [Az Office 365 összekötése](connect-office-365.md) | V | |
| SecurityEvents | [Windows biztonsági események összekötése](connect-windows-security-events.md)  | V | A nem biztonságos protokollok-munkafüzetek esetében lásd: nem [biztonságos protokollok beállítása](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Rendszernapló: | [A syslog összekötése](connect-syslog.md) | V | |
| Microsoft webalkalmazási tűzfal (WAF) – (AzureDiagnostics) |[A Microsoft webalkalmazási tűzfal összekapcsolása](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [A Symantec összekötése](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [A fenyegetés intelligenciának összekapcsolása](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Monitor szolgáltatás térképe](../azure-monitor/insights/service-map.md)<br>[Azure Monitor a virtuális gépek bevezetését](../azure-monitor/insights/vminsights-onboard.md) <br> [Azure Monitor VM-alapú adatfelismerés engedélyezése](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Egyetlen virtuális gép használata a fedélzeten](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [A szabályzaton keresztüli beszállás használata](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | VM-alapú adatáttekintési munkafüzet  |
| DnsEvents | [DNS összekötése](connect-dns.md) | V | |
| W3CIISLog | [IIS-naplók összekötése](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| WireData | [Vezetékes adatkapcsolatok](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall | [A Windows tűzfal összekapcsolása](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [Az Azure AD Identity Protection csatlakoztatása](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [Az Azure ATP összekötése](connect-azure-atp.md) | V | |
| ASC SecurityAlert  | [Azure Security Center összekötése](connect-azure-security-center.md)  | V | |
| MCAS SecurityAlert  | [Microsoft Cloud App Security összekötése](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Sysmon (esemény) | [Sysmon összekötése](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Windows-események összekötése](../azure-monitor/platform/data-sources-windows-events.md) <br> [A Sysmon-elemző beszerzése](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | A Sysmon-gyűjtemény alapértelmezés szerint nincs telepítve a virtuális gépeken. A Sysmon-ügynök telepítésével kapcsolatos további információkért lásd: [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [VM-leltár automatizálása](../automation/automation-vm-inventory.md)| X | |
| Konfigurációváltozás  | [VIRTUÁLIS gépek követésének automatizálása](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Az F5 BIG-IP összekötése](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [Barracuda-kapcsolat](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Következő lépések

- Az Azure Sentinel megkezdéséhez szüksége lesz egy előfizetésre Microsoft Azure. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan hozhatja be [adatait az Azure sentinelbe](quickstart-onboard.md), és hogyan tekintheti [meg az adatait és a lehetséges fenyegetéseket](quickstart-get-visibility.md).

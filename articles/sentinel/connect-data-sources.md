---
title: Adatforrások összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az adatforrások, például a Microsoft Threat Protection, a Microsoft 365 és az Office 365, az Azure AD, az ATP és a Cloud App Security az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: a2b9c1602ead56b35c46508ef4d414145eb07432
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555513"
---
# <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

Ha engedélyezte az Azure Sentinelt, először össze kell kapcsolni az adatforrásokat. Az Azure Sentinel számos összekötőt kínál a Microsoft-megoldások számára, és lehetővé teszi a valós idejű integrációt, beleértve a Microsoft Threat Protection-megoldásokat, Microsoft 365 forrásait (beleértve az Office 365-et), az Azure AD-t, az Azure ATP-t, a Microsoft Cloud App Security és egyebeket. Emellett beépített összekötők találhatók a nem Microsoft-megoldások szélesebb körű biztonsági ökoszisztémájában. A Common Event Format (CEF), a syslog vagy a REST-API használatával is összekapcsolhatók az adatforrások az Azure Sentinel szolgáltatással.

1. A menüben válassza az **adatösszekötők**lehetőséget. Ezen a lapon megtekintheti az Azure Sentinel által biztosított összekötők teljes listáját, valamint azok állapotát. Válassza ki a csatlakoztatni kívánt összekötőt, és válassza az **összekötő lap megnyitása**lehetőséget. 

   ![Adatgyűjtők](./media/collect-data/collect-data-page.png)

1. Az adott összekötő lapon győződjön meg arról, hogy teljesítette az összes előfeltételt, és kövesse az utasításokat az adat Azure Sentinelhez való csatlakozásához. Eltarthat egy ideig, amíg a naplók megkezdik a szinkronizálást az Azure Sentinel használatával. A csatlakozást követően megjelenik a **kapott** adatmennyiség és az adattípusok kapcsolati állapota.

   ![Gyűjtők összekapcsolása](./media/collect-data/opened-connector-page.png)
  
1. A **következő lépések** lapon lekérheti, hogy az Azure Sentinel milyen adattípust biztosít a beépített tartalomhoz.

   ![Adatgyűjtők](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Adatkapcsolati módszerek

Az Azure Sentinel a következő adatkapcsolási módszereket támogatja:

- **Szolgáltatás-szolgáltatás integrációja**:<br> Egyes szolgáltatások natív módon csatlakoznak, mint például az AWS és a Microsoft Services, ezek a szolgáltatások kihasználják az Azure Foundation-t a Box-integrációhoz, a következő megoldások néhány kattintással csatlakoztathatók:
    - [Amazon Web Services – CloudTrail](connect-aws.md)
    - [Azure-tevékenység](connect-azure-activity.md)
    - [Azure Active Directory](connect-azure-active-directory.md) – naplózási naplók és bejelentkezési naplók
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Komplex veszélyforrások elleni védelem](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Tartományi névkiszolgáló](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Microsoft webalkalmazási tűzfal](connect-microsoft-waf.md)
    - [Windows tűzfal](connect-windows-firewall.md)
    - [A Windows biztonsági eseményei](connect-windows-security-events.md)

- **Külső megoldások API-n keresztül**: egyes adatforrások a csatlakoztatott adatforrás által biztosított API-k használatával kapcsolódnak egymáshoz. A legtöbb biztonsági technológia jellemzően olyan API-kat biztosít, amelyeken keresztül az eseménynaplók kérhetők le. Az API-k az Azure Sentinelhez csatlakoznak, és konkrét adattípusokat gyűjtenek, és elküldhetik azokat az Azure Log Analyticsba. Az API-n keresztül csatlakoztatott berendezések a következők:
    
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen tűzfal](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Perimeter 81-naplók](connect-perimeter-81-logs.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Külső megoldások ügynökön keresztül**: az Azure Sentinel bármely más olyan adatforráshoz csatlakoztatható, amely a syslog protokoll használatával valós idejű naplózást végezhet.

    A legtöbb készülék a syslog protokollt használja az olyan események küldésére, amelyek magukban foglalják a naplót és a naplóval kapcsolatos információkat. A naplók formátuma változó, de a legtöbb készülék támogatja a CEF-alapú formázást. 

    Az Azure Sentinel-ügynök, amely valójában a Log Analytics ügynök, átalakítja a CEF formátumú naplókat olyan formátumba, amelyet a Log Analytics betölt. A készülék típusától függően az ügynök közvetlenül a készülékre, vagy egy dedikált Linux-alapú napló-továbbítóra van telepítve. A Linux-ügynök a syslog démontól érkező eseményeket fogad UDP-n keresztül, de ha egy Linux rendszerű gépen nagy mennyiségű syslog-eseményt kell gyűjteni, a rendszer a syslog démonból az ügynököt és onnan Log Analytics.

    - **Tűzfalak, proxyk és végpontok:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Ellenőrzőpont](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Forcepoint-termékek](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Egyéb CEF készülékek](connect-common-event-format.md)
        - [Egyéb syslog-készülékek](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - DLP-megoldások
    - [Veszélyforrások elleni intelligencia szolgáltatók](connect-threat-intelligence.md)
    - [DNS-gépek](connect-dns.md) – közvetlenül a DNS-gépen telepített ügynök
    - Linux-kiszolgálók
    - Egyéb felhők
    
## <a name="agent-connection-options"></a>Ügynökkapcsolat-beállítások<a name="agent-options"></a>

Ahhoz, hogy a külső berendezést az Azure Sentinelhez lehessen kapcsolni, az ügynököt egy dedikált gépen (virtuális gépen vagy helyszínen) kell telepíteni a készülék és az Azure Sentinel közötti kommunikáció támogatásához. Az ügynököt automatikusan vagy manuálisan is üzembe helyezheti. Az automatikus központi telepítés csak akkor érhető el, ha a dedikált számítógép egy új, az Azure-ban létrehozott virtuális gép. 


![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Azt is megteheti, hogy manuálisan telepítheti az ügynököt egy meglévő Azure-beli virtuális gépre, egy másik felhőben lévő virtuális gépre vagy egy helyszíni gépre.

![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Adattípusok leképezése Azure Sentinel-kapcsolatbeállításokkal


| **Adattípus** | **Csatlakozás** | **Adatösszekötő?** | **Megjegyzések** |
|------|---------|-------------|------|
| AWSCloudTrail | [Az AWS csatlakoztatása](connect-aws.md) | &#10003; | |
| AzureActivity | Az Azure-tevékenység és a Tevékenységnaplók [összekapcsolása](connect-azure-activity.md) [– Áttekintés](../azure-monitor/platform/platform-logs-overview.md)| &#10003; | |
| AuditLogs | [Az Azure AD csatlakoztatása](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Az Azure AD csatlakoztatása](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Azure Diagnostics](../firewall/tutorial-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Jelentések Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Az Azure Information Protection csatlakoztatása](connect-azure-information-protection.md)  | &#10003; | Ez általában az adattípuson kívül a **InformationProtectionEvents** függvényt használja. További információ: [a jelentések módosítása és egyéni lekérdezések létrehozása](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Traffic analitikai séma](../network-watcher/traffic-analytics.md) [Traffic Analytics](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [CEF összekötése](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [Az Office 365 csatlakoztatása](connect-office-365.md) | &#10003; | |
| SecurityEvents | [A Windows biztonsági eseményeinek csatlakoztatása](connect-windows-security-events.md)  | &#10003; | A nem biztonságos protokollok-munkafüzetek esetében lásd: nem [biztonságos protokollok beállítása](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Rendszernapló | [A Syslog csatlakoztatása](connect-syslog.md) | &#10003; | |
| Microsoft webalkalmazási tűzfal (WAF) – (AzureDiagnostics) |[A Microsoft webalkalmazási tűzfal összekapcsolása](connect-microsoft-waf.md) | &#10003; | |
| SymantecICDx_CL | [A Symantec összekötése](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [Fenyegetésfelderítés csatlakoztatása](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Monitor szolgáltatás térképe](../azure-monitor/insights/service-map.md)<br>[Azure Monitor a virtuális gépek bevezetését](../azure-monitor/insights/vminsights-onboard.md) <br> [Azure Monitor VM-alapú adatfelismerés engedélyezése](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Egyetlen virtuális gép használata a fedélzeten](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [A szabályzaton keresztüli beszállás használata](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| &#10007; | VM-alapú adatáttekintési munkafüzet  |
| DnsEvents | [DNS összekötése](connect-dns.md) | &#10003; | |
| W3CIISLog | [IIS-naplók összekötése](../azure-monitor/platform/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [Vezetékes adatkapcsolatok](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [A Windows tűzfal összekapcsolása](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [Az Azure AD Identity Protection csatlakoztatása](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Az Azure ATP csatlakoztatása](connect-azure-atp.md) | &#10003; | |
| ASC SecurityAlert  | [Az Azure Security Center csatlakoztatása](connect-azure-security-center.md)  | &#10003; | |
| MCAS SecurityAlert  | [Microsoft Cloud App Security összekötése](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (esemény) | [Sysmon összekötése](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Windows-események összekötése](../azure-monitor/platform/data-sources-windows-events.md) <br> [A Sysmon-elemző beszerzése](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | A Sysmon-gyűjtemény alapértelmezés szerint nincs telepítve a virtuális gépeken. A Sysmon-ügynök telepítésével kapcsolatos további információkért lásd: [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [VM-leltár automatizálása](../automation/automation-vm-inventory.md)| &#10007; | |
| Konfigurációváltozás  | [VIRTUÁLIS gépek követésének automatizálása](../automation/change-tracking.md) | &#10007; | |
| F5 BIG-IP | [Az F5 BIG-IP csatlakoztatása](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [A Barracuda csatlakoztatása](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>További lépések

- Az Azure Sentinel megkezdéséhez szüksége lesz egy előfizetésre Microsoft Azure. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan hozhatja be [adatait az Azure sentinelbe](quickstart-onboard.md), és hogyan tekintheti [meg az adatait és a lehetséges fenyegetéseket](quickstart-get-visibility.md).

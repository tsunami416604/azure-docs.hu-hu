---
title: Adatforrások csatlakoztatása az Azure Sentinelhez | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathat adatforrásokat az Azure Sentinelhez.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240085"
---
# <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

A beépített Azure Sentinel, először kell csatlakoznia az adatforrásokhoz. Az Azure Sentinel számos, a microsoftos megoldásokhoz elérhető összekötőt kínál, amelyek azonnal elérhetők, és valós idejű integrációt biztosítanak, beleértve a Microsoft Threat Protection megoldásokat és a Microsoft 365-forrásokat, beleértve az Office 365-öt, az Azure AD-t, az Azure ATP-t és a Microsoft Cloud App Security, és így tovább. Emellett a nem Microsoft-megoldások szélesebb biztonsági ökoszisztémájához beépített összekötők is találhatók. A közös eseményformátum, a Syslog vagy a REST-API segítségével is csatlakoztathatja az adatforrásokat az Azure Sentinelhez.  

1. A menüben válassza az **Adatösszekötők**lehetőséget. Ezen a lapon megtekintheti az Azure Sentinel által biztosított összekötők teljes listáját és azok állapotát. Jelölje ki a csatlakoztatni kívánt összekötőt, és válassza **az Összekötő lap megnyitása**lehetőséget. 

   ![Adatgyűjtők](./media/collect-data/collect-data-page.png)

1. Az adott összekötő oldalon győződjön meg arról, hogy teljesítette az összes előfeltételt, és kövesse az utasításokat az adatok azure Sentinel csatlakoztatásához. Eltarthat egy ideig, amíg a naplók szinkronizálást kezdenek el az Azure Sentinel. A csatlakozás után megjelenik az adatok összegzése az **Adatok fogadott** grafikonon, és az adattípusok kapcsolati állapota.

   ![Gyűjtők csatlakoztatása](./media/collect-data/opened-connector-page.png)
  
1. Kattintson a **Következő lépések** fülre az Azure Sentinel által az adott adattípushoz biztosított, használatra szolgáló tartalom listájának lekérni.

   ![Adatgyűjtők](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Adatkapcsolati módszerek

Az Azure Sentinel a következő adatkapcsolati módszereket támogatja:

- **Szolgáltatás-szolgáltatás integráció**:<br> Egyes szolgáltatások natív módon kapcsolódnak egymáshoz, például az AWS és a Microsoft-szolgáltatások, ezek a szolgáltatások az Azure-alapot használják a beépített integrációhoz, a következő megoldások néhány kattintással csatlakoztathatók:
    - [Amazon Web Services - CloudTrail](connect-aws.md)
    - [Azure-tevékenység](connect-azure-activity.md)
    - [Az Azure AD naplózási naplói és bejelentkezései](connect-azure-active-directory.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Komplex veszélyforrások elleni védelem](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Tartományi névkiszolgáló](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Windows Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Microsoft webalkalmazási tűzfal](connect-microsoft-waf.md)
    - [Windows tűzfal](connect-windows-firewall.md)
    - [A Windows biztonsági eseményei](connect-windows-security-events.md)

- **Külső megoldások API-n keresztül:** Egyes adatforrások a csatlakoztatott adatforrás által biztosított API-k használatával kapcsolódnak. A legtöbb biztonsági technológia általában api-kkészletét biztosítja, amelyeken keresztül az eseménynaplók lekérdezhetők. Az API-k az Azure Sentinelhez csatlakoznak, és összegyűjtik az adott adattípusokat, és elküldik őket az Azure Log Analytics szolgáltatásnak. Az API-n keresztül csatlakoztatott készülékek a következők:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen tűzfal](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Külső megoldások ügynökön keresztül:** Az Azure Sentinel csatlakoztatható minden más adatforráshoz, amely a Syslog protokoll használatával valós idejű naplóstreamelést hajthat végre egy ügynökön keresztül. <br>A legtöbb készülék a Syslog protokollt használja olyan eseményüzenetek küldésére, amelyek magáról a naplóról és a naplóadatairól is tartalmaznak. A naplók formátuma változó, de a legtöbb készülék támogatja a közös eseményformátum (CEF) alapú formázást a naplóadatokhoz. <br>Az Azure Sentinel-ügynök, amely a Log Analytics-ügynök, a CEF-formázott naplók at a Log Analytics által bevitt formátumba konvertálja. A készülék típusától függően az ügynök vagy közvetlenül a készülékre, vagy egy dedikált Linux-kiszolgálóra van telepítve. A Linux-ügynök eseményeket kap a Syslog démon udp-n keresztül, de ha egy Linux-gép várhatóan nagy mennyiségű Syslog események gyűjtése, a Rendszer elküldi a TCP-n keresztül a Syslog démon az ügynök, és onnan a Log Analytics.
    - Tűzfalak, proxyk és végpontok:
        - [Ellenőrzőpont](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Forcepoint-termékek](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Egyéb CEF készülékek](connect-common-event-format.md)
        - [Egyéb Syslog készülékek](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - DLP megoldások
    - [Fenyegetésfelderítési szolgáltatók](connect-threat-intelligence.md)
    - [DNS-gépek](connect-dns.md) - közvetlenül a DNS-gépen telepített ügynök
    - Linux szerverek
    - Egyéb felhők
    
## <a name="agent-connection-options"></a>Ügynökkapcsolat-beállítások<a name="agent-options"></a>

A külső készülék csatlakoztatásához az Azure Sentinel, az ügynök kell telepíteni egy dedikált gépen (virtuális gép vagy a helyszínen) a készülék és az Azure Sentinel közötti kommunikáció támogatása érdekében. Az ügynök automatikusan vagy manuálisan is telepíthető. Az automatikus üzembe helyezés csak akkor érhető el, ha a dedikált gép egy új virtuális gép, amelyet az Azure-ban hoz létre. 


![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Azt is megteheti, hogy manuálisan telepíti az ügynököt egy meglévő Azure-beli virtuális gépen, egy másik felhőben lévő virtuális gépen vagy egy helyszíni gépen.

![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Adattípusok leképezése Azure Sentinel-kapcsolatbeállításokkal


| **Adattípus** | **Csatlakozás** | **Adatcsatlakozó?** | **Megjegyzések** |
|------|---------|-------------|------|
| AWSCloudTrail | [Az AWS csatlakoztatása](connect-aws.md) | V | |
| AzureActivity | [Az Azure-tevékenység-](connect-azure-activity.md) és [tevékenységnaplók összekapcsolása – áttekintés](../azure-monitor/platform/platform-logs-overview.md)| V | |
| AuditLogok | [Az Azure AD csatlakoztatása](connect-azure-active-directory.md)  | V | |
| Bejelentkezések | [Az Azure AD csatlakoztatása](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Azure Diagnostics](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Az Azure Information Protection jelentései](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Az Azure Information Protection csatlakoztatása](connect-azure-information-protection.md)  | V | Ez általában az Adattípus mellett az **InformationProtectionEvents** függvényt is használja. További információ: [A jelentések módosítása és egyéni lekérdezések létrehozása](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Forgalomelemzés ima forgalmi elemzési analitikája](../network-watcher/traffic-analytics.md) [Traffic analytics](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Cef csatlakoztatása](connect-common-event-format.md)  | V | |
| OfficeActivity tevékenység | [Az Office 365 csatlakoztatása](connect-office-365.md) | V | |
| SecurityEvents (Biztonsági események) | [A Windows biztonsági eseményeinek csatlakoztatása](connect-windows-security-events.md)  | V | A Nem biztonságos protokollok munkafüzetei ről a [Nem biztonságos protokollok beállítása című témakörben](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks) található  |
| Rendszernapló | [A Syslog csatlakoztatása](connect-syslog.md) | V | |
| Microsoft webalkalmazás-tűzfal (WAF) – (AzureDiagnostics) |[A Microsoft webalkalmazás-tűzfal csatlakoztatása](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Csatlakozás a Symantec-hez](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator (Fenyegetésintelligencia-mutató)  | [Fenyegetésfelderítés csatlakoztatása](connect-threat-intelligence.md)  | V | |
| Virtuális gépkapcsolat <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Az Azure Monitor szolgáltatástérképe](../azure-monitor/insights/service-map.md)<br>[Az Azure Monitor virtuálisgép-elemzési adatai bevezetésre](../azure-monitor/insights/vminsights-onboard.md) <br> [Az Azure Monitor virtuálisgép-elemzési adatainak engedélyezése](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Egyetlen virtuális gép beszállása](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [A beszállási szabályzat használata](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | Virtuálisgép-elemzési munkafüzet  |
| DnsEsemények | [Dns csatlakoztatása](connect-dns.md) | V | |
| W3CIISLog | [IIS-naplók csatlakoztatása](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| WireData (Vezetékadatok) | [Vezetékadatok csatlakoztatása](../azure-monitor/insights/wire-data.md) | X | |
| WindowsTűzfal | [Csatlakozás Windows tűzfalhoz](connect-windows-firewall.md) | V | |
| AADIP biztonsági riasztás  | [Az Azure AD Identity Protection csatlakoztatása](connect-azure-ad-identity-protection.md)  | V | |
| AATP biztonsági riasztás  | [Az Azure ATP csatlakoztatása](connect-azure-atp.md) | V | |
| ASC biztonsági riasztás  | [Az Azure Security Center csatlakoztatása](connect-azure-security-center.md)  | V | |
| MCAS biztonsági riasztás  | [A Microsoft Cloud App Security összekapcsolása](connect-cloud-app-security.md)  | V | |
| SecurityAlert (Biztonsági riasztás) | | | |
| Sysmon (Esemény) | [Sysmon csatlakoztatása](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Windows-események csatlakoztatása](../azure-monitor/platform/data-sources-windows-events.md) <br> [Szerezd meg a Sysmon Parser](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | A Sysmon-gyűjtemény alapértelmezés szerint nincs telepítve a virtuális gépeken. A Sysmon-ügynök telepítéséről a [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon)című témakörben talál további információt. |
| Konfigurációs adatok  | [Virtuálisgép-készlet automatizálása](../automation/automation-vm-inventory.md)| X | |
| ConfigurationChange (Konfigurációmódosítása)  | [Virtuális gépek követésének automatizálása](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Az F5 BIG-IP csatlakoztatása](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [A Barracuda csatlakoztatása](connect-barracuda.md) | V | |


## <a name="next-steps"></a>További lépések

- Az Azure Sentinel első lépéseihez előfizetésre van szüksége a Microsoft Azure-ra. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan [integrálhatja adatait az Azure Sentinelbe,](quickstart-onboard.md)és [hogyan láthatja az adatokat és a potenciális fenyegetéseket.](quickstart-get-visibility.md)

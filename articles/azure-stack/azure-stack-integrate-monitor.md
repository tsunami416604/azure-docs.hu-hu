---
title: Külső figyelési megoldást integrálása Azure verem |} Microsoft Docs
description: 'Útmutató: Azure verem integrálni az adatközpontban lévő külső felügyeleti megoldással.'
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: d7c8520602132722fd0c7138de4a276b9ac2208a
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Külső figyelési megoldást integrálása Azure verem

Az Azure-verem infrastruktúra külső figyelésre, az Azure-verem szoftver, a fizikai számítógépek és a fizikai hálózati kapcsolók figyelnie kell. Ezek a területek nyújt egy metódusának segítéségével lekérheti a rendszerállapot és a riasztási adatokat:

- Azure verem szoftverfrissítési állapot és riasztások lekérdezni egy REST-alapú API kínál. Szoftver-meghatározott technológiák, például a tárolóhelyek közvetlen, tároló állapotát és riasztások szoftver figyelési részét képezik.
- Fizikai számítógépek is elérhetővé állapotának és riasztási adatokat az alaplapi felügyeleti vezérlővel (bmc) keresztül.
- Fizikai hálózati eszközöket is elérhetővé állapotának és riasztási adatokat az SNMP protokollon keresztül.

Minden Azure verem megoldás részét képező hardver életciklus gazdagépet. Ezen a gazdagépen fut a számítógépgyártó (OEM) hardver gyártója által biztosított felügyeleti szoftver a fizikai kiszolgálók és a hálózati eszközök. Szükség esetén ezeket a figyelési megoldások megkerülése, és az Adatközpont meglévő figyelési megoldások közvetlenül integrálni.

> [!IMPORTANT]
> A külső figyelési megoldást használja az ügynök nélkül kell lennie. Külső ügynökök belül Azure verem összetevő nem telepíthető.

Az alábbi ábrán egy integrált Azure verem rendszer, a hardver életciklus állomás, egy külső figyelési megoldást és egy külső jegykezelési/adatgyűjtési rendszer közötti adatforgalom.

![Azure-vermet, figyelés és megoldást jegykezelési közötti forgalom ábrázoló diagram.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Ez a cikk azt ismerteti, hogyan Azure verem integrálása külső figyelési megoldások, például a System Center Operations Manager és a Nagios. Hogyan működnek a riasztások programozott módon PowerShell használatával vagy a REST API-hívások is tartalmaz.

## <a name="integrate-with-operations-manager"></a>Az Operations Manager integrálása

Az Operations Manager használható Azure verem külső figyelését. A System Center felügyeleti csomag a Microsoft Azure verem lehetővé teszi az Operations Manager egypéldányos több Azure verem telepítésének figyelése. A felügyeleti csomag az erőforrás-szolgáltató állapota és a frissítés erőforrás-szolgáltató REST API-k használ Azure verem folytatott kommunikációhoz. Ha azt tervezi, a figyelés a hardver életciklus gazdagépen futó OEM elkerülésére, gyártói felügyeleti csomagok fizikai kiszolgálók figyelésére is telepítheti. Az Operations Manager hálózati eszköz felderítési segítségével is figyelheti a hálózati kapcsolók.

A felügyeleti csomag Azure verem a következő lehetőségeket biztosítja:

- Kezelheti a több Azure verem központi telepítések
- Azure Active Directory (Azure AD) és Active Directory összevonási szolgáltatások (AD FS) támogatása
- Beolvashatja és riasztások bezárása
- A rendszerállapot és a kapacitás irányítópult
- Amikor javítási és frissítési (P & U) folyamatban van az automatikus karbantartás mód észlelési tartalmazza
- Magában foglalja a telepítésre és a régió kényszerített frissítési feladatok
- Egyéni információkat is hozzáadhat egy régió
- Támogatja az értesítések és jelentések

A System Center felügyeleti csomagját letöltheti a Microsoft Azure verem és a társított [felhasználói útmutató](https://www.microsoft.com/en-us/download/details.aspx?id=55184), vagy közvetlenül az Operations Manager alkalmazásból.

Jegykiadási megoldás integrálhatja az Operations Manager a System Center Service Manager. Az integrált termékcsatlakoztató lehetővé teszi, hogy a kétirányú kommunikációt, amely lehetővé teszi az Azure-vermet, és az Operations Manager riasztás bezárása egy szolgáltatási kérelmet a Service Manager megoldása után.

Az alábbi ábrán látható Azure verem integrációja meglévő System Center-telepítés. Service Manager további a System Center Orchestrator, vagy a Service Management Automation (SMA) operations futtatható Azure verem automatizálható.

![Integráció az Operations Manager, a Service Manager és az SMA ábrázoló diagram.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Nagios integrálása

A beépülő modul figyelési Nagios jött létre a partnermegoldások Cloudbase együtt a megengedő szabad szoftverlicenc – MIT (Massachusetts Institute of Technology) alatt elérhető.

A beépülő modul pythonban írt, és lehetővé teszi az egészségügyi erőforrás-szolgáltató REST API-t. Lekérdezéséhez és riasztások bezárása az Azure-verem alapvető funkciókat kínál. Például a System Center felügyeleti csomagja lehetővé teszi a több Azure verem-telepítés hozzáadása és az értesítések küldéséhez.

A beépülő modul Nagios vállalati és Nagios Core működik. Letöltheti a [Itt](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). A letöltési hely telepítésével és konfigurálásával adatokat is tartalmaz.

### <a name="plugin-parameters"></a>Beépülő modul Paraméterek

A beépülőmodul-fájlt "Azurestack_plugin.py" konfigurálása a következő paraméterekkel:

| Paraméter | Leírás | Példa |
|---------|---------|---------|
| *arm_endpoint* | Az Azure Resource Manager (rendszergazda) végpont |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Az Azure Resource Manager (rendszergazda) végpont  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Felügyeleti előfizetés-azonosító | A felügyeleti portál vagy PowerShell beolvasása |
| *User_name* | Operátor előfizetés felhasználónév | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Operátor előfizetés jelszó | SajátJelszó |
| *Client_id* | Ügyfél | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Az Azure verem régió neve | helyi |
|  |  |

* A megadott PowerShell GUID univerzális. Használhatja az egyes központi telepítések.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>A figyelő állapotát és a riasztások a PowerShell használatával

Ha nem használja az Operations Manager, Nagios, illetve a Nagios-alapú megoldás, a PowerShell használatával széles skálájával figyelési megoldásoknak integrálása az Azure-verem engedélyezése.

1. A PowerShell segítségével, győződjön meg arról, hogy rendelkezik [PowerShell telepítése és konfigurálása](azure-stack-powershell-configure-quickstart.md) Azure verem üzemeltető környezetben. Telepítse a PowerShell helyi számítógépre, amely képes elérni az erőforrás-kezelő (rendszergazda) végpont (https://adminmanagement. [régió]. [External_FQDN]).

2. A következő parancsokat a Azure verem környezet Azure verem kezelőként való csatlakozáshoz:

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. A riasztások kezelése például az alábbi példák parancsokat használja:
   ```PowerShell
    #Retrieve all alerts
    Get-AzsAlert

    #Filter for active alerts
    $Active=Get-AzsAlert | Where {$_.State -eq "active"}
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    Get-AzsRPHealth

    #Retrieve infrastructure role instance health
    $FRPID=Get-AzsRPHealth|Where-Object {$_.DisplayName -eq "Capacity"}
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId

    ```

## <a name="learn-more"></a>Részletek

További információ a beépített állapotfigyelés: [állapotának és az Azure-készletben riasztások figyelése](azure-stack-monitor-health.md).

## <a name="next-steps"></a>További lépések

[Biztonsági integráció](azure-stack-integrate-security.md)

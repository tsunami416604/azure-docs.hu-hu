---
title: "Külső figyelési megoldást integrálása Azure verem |} Microsoft Docs"
description: "Útmutató: Azure verem integrálni az adatközpontban lévő külső felügyeleti megoldással."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: a7f6d3691410711fcae692007b08977a93961845
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Külső figyelési megoldást integrálása Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Az Azure-verem infrastruktúra külső figyelésre, az Azure-verem szoftver, a fizikai számítógépek és a fizikai hálózati kapcsolók figyelnie kell. Ezek a területek kínál egy metódust az egészségügyi és riasztási adatokat beolvasni.

- Azure verem szoftverfrissítési állapot és riasztások lekérdezni egy REST-alapú API kínál. (Szoftver által meghatározott technológiákat, például a közvetlen tárolóhelyek használatával, tárolási állapot és riasztások részei szoftver figyelési.)
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

- Kezelheti a több Azure verem központi telepítését.
- A rendszer támogatja az Azure Active Directory (Azure AD) és Active Directory összevonási szolgáltatások (AD FS).
- Kérje le, majd zárja be a riasztásokat.
- A rendszerállapot és a kapacitás irányítópult van.
- Amikor javítási és frissítési (P & U) folyamatban van az automatikus karbantartás mód észlelési tartalmazza.
- A központi telepítés és a régió kényszerített frissítési feladatokat tartalmazza.
- Egyéni adatok adhat hozzá egy régiót.
- Támogatja az értesítések és jelentések.

Letöltheti a Microsoft Azure verem és a kapcsolódó felhasználói útmutató a System Center felügyeleti csomag [Itt](https://www.microsoft.com/en-us/download/details.aspx?id=55184), vagy közvetlenül az Operations Manager alkalmazásból.

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
 
1. A PowerShell segítségével, győződjön meg arról, hogy rendelkezik [PowerShell telepítése és konfigurálása](azure-stack-powershell-configure-quickstart.md) Azure verem üzemeltető környezetben. Telepítse a PowerShell helyi számítógépre, amely képes elérni az erőforrás-kezelő (rendszergazda) végpont (https://adminmanagement [régió].[External_FQDN]).

2. A következő parancsokat a Azure verem környezet Azure verem kezelőként való csatlakozáshoz:

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Váltson arra a könyvtárra, amelybe telepítette a [Azure verem eszközök](https://github.com/Azure/AzureStack-Tools) a PowerShell telepítése, például c:\azurestack-tools-master részeként. Ezután váltson az infrastruktúra és az infrastruktúra modul importálása a következő parancsot:

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. A riasztások kezelése például az alábbi példák parancsokat használja:
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>A figyelő állapotát és az értesítések a REST API használata

REST API-hívások segítségével riasztásokat kaphat, riasztások bezárása és erőforrás-szolgáltatók állapotának beolvasása.

### <a name="get-alert"></a>Riasztás beolvasása

**Kérés**

A kérelem lekérdezi az alapértelmezett szolgáltató előfizetés az összes aktív és lezárt riasztások. Nem található kérelemtörzs van.


|Módszer  |Kérés URI-ja  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**Argumentumok**

|Argumentum  |Leírás  |
|---------|---------|
|armendpoint     |  Az Azure-verem környezethez, a formátum https://adminmanagement az Azure Resource Manager végpontja. {RegionName}. {Külső FQDN}. Például, ha a külső FQDN-je *azurestack.external* régió neve pedig *helyi*, akkor a Resource Manager-végpont https://adminmanagement.local.azurestack.external.       |
|subid     |   A felhasználó, aki a hívás előfizetés-azonosítója. Ez az API lekérdezés csak olyan felhasználó, aki jogosult az alapértelmezett szolgáltató előfizetéshez használható.      |
|RegionName     |    Az Azure Alkalmazásveremben üzembe régió neve.     |
|API-verzió     |  Az ilyen kérést használt protokoll verziója. 2016-05-01 kell használnia.      |
|     |         |

**Válasz**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Válasz adatai**


|  Argumentum  |Leírás  |
|---------|---------|
|*id*     |      A riasztás egyedi azonosítója.   |
|*name*     |     A riasztás belső nevét.   |
|*típusa*     |     Erőforrás-definícióban.    |
|*hely*     |       A régió neve.     |
|*címkét*     |   Az erőforráscímkék.     |
|*closedtimestamp*    |  UTC idő, amikor a riasztás be lett zárva.    |
|*createdtimestamp*     |     A riasztás létrehozásának UTC-ideje.   |
|*description*     |    A riasztás leírása.     |
|*faultid*     | Érintett összetevőt.        |
|*alertid*     |  A riasztás egyedi azonosítója.       |
|*faulttypeid*     |  Hibás összetevő egyedi típusa.       |
|*lastupdatedtimestamp*     |   UTC idő riasztási információkat utolsó frissítésekor.    |
|*healthstate*     | Általános állapot.        |
|*name*     |   Neve az adott riasztásra.      |
|*fabricname*     |    A hibás összetevő regisztrált háló neve.   |
|*description*     |  A regisztrált összetevőjének leírása.   |
|*servicetype*     |   A regisztrált fabric-szolgáltatás típusú.   |
|*szervizkiszolgáló*     |   Ajánlott javítási lépéseket.    |
|*típusa*     |   Riasztási típus.    |
|*resourceRegistrationid*    |     A regisztrált erőforrás-azonosítója.    |
|*resourceProviderRegistrationID*   |    A regisztrált erőforrás-szolgáltató az érintett összetevő azonosítója.  |
|*serviceregistrationid*     |    A regisztrált szolgáltatás azonosítója.   |
|*severity*     |     Riasztás súlyossága.  |
|*state*     |    A riasztás állapota.   |
|*cím*     |    Riasztás címét.   |
|*impactedresourceid*     |     Érintett erőforrás azonosítója.    |
|*ImpactedresourceDisplayName*     |     Az érintett erőforrás nevét.  |
|*closedByUserAlias*     |   Riasztás lezáró felhasználó.      |

### <a name="close-alert"></a>A riasztás lezárása

**Kérés**

A kérelem egyedi azonosítójú riasztás bezárása után

|Módszer    |Kérés URI-ja  |
|---------|---------|
|A PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**Argumentumok**


|Argumentum  |Leírás  |
|---------|---------|
|*armendpoint*     |   Az Azure-verem környezethez, a formátum https://adminmanagement az erőforrás-kezelő végpontját. {RegionName}. {Külső FQDN}. Például, ha a külső FQDN-je *azurestack.external* régió neve pedig *helyi*, akkor a Resource Manager-végpont https://adminmanagement.local.azurestack.external.      |
|*subid*     |    A felhasználó, aki a hívás előfizetés-azonosítója. Ez az API lekérdezés csak olyan felhasználó, aki jogosult az alapértelmezett szolgáltató előfizetéshez használható.     |
|*RegionName*     |   Az Azure Alkalmazásveremben üzembe régió neve.      |
|*api-version*     |    Az ilyen kérést használt protokoll verziója. 2016-05-01 kell használnia.     |
|*alertid*     |    A riasztás egyedi azonosítója.     |

**Törzs**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Válasz**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Válasz adatai**


|  Argumentum  |Leírás  |
|---------|---------|
|*id*     |      A riasztás egyedi azonosítója.   |
|*name*     |     A riasztás belső nevét.   |
|*típusa*     |     Erőforrás-definícióban.    |
|*hely*     |       A régió neve.     |
|*címkét*     |   Az erőforráscímkék.     |
|*closedtimestamp*    |  UTC idő, amikor a riasztás be lett zárva.    |
|*createdtimestamp*     |     A riasztás létrehozásának UTC-ideje.   |
|*Leírás*     |    A riasztás leírása.     |
|*faultid*     | Érintett összetevőt.        |
|*alertid*     |  A riasztás egyedi azonosítója.       |
|*faulttypeid*     |  Hibás összetevő egyedi típusa.       |
|*lastupdatedtimestamp*     |   UTC idő riasztási információkat utolsó frissítésekor.    |
|*healthstate*     | Általános állapot.        |
|*name*     |   Neve az adott riasztásra.      |
|*fabricname*     |    A hibás összetevő regisztrált háló neve.   |
|*description*     |  A regisztrált összetevőjének leírása.   |
|*servicetype*     |   A regisztrált fabric-szolgáltatás típusú.   |
|*szervizkiszolgáló*     |   Ajánlott javítási lépéseket.    |
|*típusa*     |   Riasztási típus.    |
|*resourceRegistrationid*    |     A regisztrált erőforrás-azonosítója.    |
|*resourceProviderRegistrationID*   |    A regisztrált erőforrás-szolgáltató az érintett összetevő azonosítója.  |
|*serviceregistrationid*     |    A regisztrált szolgáltatás azonosítója.   |
|*severity*     |     Riasztás súlyossága.  |
|*state*     |    A riasztás állapota.   |
|*cím*     |    Riasztás címét.   |
|*impactedresourceid*     |     Érintett erőforrás azonosítója.    |
|*ImpactedresourceDisplayName*     |     Az érintett erőforrás nevét.  |
|*closedByUserAlias*     |   Riasztás lezáró felhasználó.      |

### <a name="get-resource-provider-health"></a>Erőforrás-szolgáltató állapota beolvasása

**Kérés**

A kérés állapotát az összes regisztrált erőforrás-szolgáltató lekérdezi.


|Módszer  |Kérés URI-ja  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**Argumentumok**


|Argumentumok  |Leírás  |
|---------|---------|
|*armendpoint*     |    Az Azure-verem környezethez, a formátum https://adminmanagement az erőforrás-kezelő végpontja. {RegionName}. {Külső FQDN}. Például, ha a külső FQDN-je azurestack.external, és helyi régió nevét, majd a Resource Manager-végpont esetében https://adminmanagement.local.azurestack.external.     |
|*subid*     |     A felhasználó, aki a hívás előfizetés-azonosítója. Ez az API lekérdezés csak olyan felhasználó, aki jogosult az alapértelmezett szolgáltató előfizetéshez használható.    |
|*RegionName*     |     Az Azure Alkalmazásveremben üzembe régió neve.    |
|*api-version*     |   Az ilyen kérést használt protokoll verziója. 2016-05-01 kell használnia.      |


**Válasz**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Válasz adatai**


|Argumentum  |Leírás  |
|---------|---------|
|*Azonosító*     |   A riasztás egyedi azonosítója.      |
|*name*     |  A riasztás belső nevét.       |
|*típusa*     |  Erőforrás-definícióban.       |
|*hely*     |  A régió neve.       |
|*címkét*     |     Az erőforráscímkék.    |
|*registrationId*     |   Az erőforrás-szolgáltató regisztrálása egyedi.      |
|*displayName*     |Erőforrás adatszolgáltató megjelenített neve.        |
|*namespace*     |   Az erőforrás-szolgáltató API névteret valósít meg.       |
|*routePrefix*     |    Az erőforrás-szolgáltató együttműködhet URI.     |
|*serviceLocation*     |   Az régió, az erőforrás-szolgáltató regisztrálva van.      |
|*infraURI*     |   URI-azonosítója megadva, az infrastruktúra-szerepkör erőforrás-szolgáltató.      |
|*alertSummary*     |   Az erőforrás-szolgáltató társított kritikus és a figyelmeztető riasztás összegzését.      |
|*healthState*     |    Az erőforrás-szolgáltató állapota.     |


### <a name="get-resource-health"></a>Erőforrás állapotának beolvasása

A kérelem egy adott regisztrált erőforrás-szolgáltató állapot beolvasása.

**Kérés**

|Módszer  |Kérés URI-ja  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**Argumentumok**

|Argumentumok  |Leírás  |
|---------|---------|
|*armendpoint*     |    Az Azure-verem környezethez, a formátum https://adminmanagement az erőforrás-kezelő végpontja. {RegionName}. {Külső FQDN}. Például, ha a külső FQDN-je azurestack.external, és helyi régió nevét, majd a Resource Manager-végpont esetében https://adminmanagement.local.azurestack.external.     |
|*subid*     |A felhasználó, aki a hívás előfizetés-azonosítója. Ez az API lekérdezés csak olyan felhasználó, aki jogosult az alapértelmezett szolgáltató előfizetéshez használható.         |
|*RegionName*     |  Az Azure Alkalmazásveremben üzembe régió neve.       |
|*api-version*     |  Az ilyen kérést használt protokoll verziója. 2016-05-01 kell használnia.       |
|*RegistrationID* |Egy adott erőforrás-szolgáltató regisztrációs azonosítója. |

**Válasz**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Válasz adatai**

|Argumentum  |Leírás  |
|---------|---------|
|*Azonosító*     |   A riasztás egyedi azonosítója.      |
|*name*     |  A riasztás belső nevét.       |
|*típusa*     |  Erőforrás-definícióban.       |
|*hely*     |  A régió neve.       |
|*címkét*     |     Az erőforráscímkék.    |
|*registrationId*     |   Az erőforrás-szolgáltató regisztrálása egyedi.      |
|*resourceType*     |Az erőforrás típusát.        |
|*resourceName*     |   Erőforrás neve.   |
|*usageMetrics*     |    Erőforrás kihasználtsága metrikát.     |
|*resourceLocation*     |   A régió nevét, ahol telepítve.      |
|*resourceURI*     |   Az erőforrás URI.   |
|*alertSummary*     |   Kritikus összegzése és figyelmeztető riasztások állapotát.     |

## <a name="learn-more"></a>Részletek

További információ a beépített állapotfigyelés: [állapotának és az Azure-készletben riasztások figyelése](azure-stack-monitor-health.md).


## <a name="next-steps"></a>További lépések

[Biztonsági integráció](azure-stack-integrate-security.md)
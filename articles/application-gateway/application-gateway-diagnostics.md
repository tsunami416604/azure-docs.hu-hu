---
title: Az Alkalmazásátjáró belépési naplók, a Teljesítménynaplók, a háttér-állapotának és a metrikák figyelése
description: Megtudhatja, hogyan engedélyezheti és kezelheti a belépési naplók és a Teljesítménynaplók az Alkalmazásátjáró
services: application-gateway
author: amitsriva
manager: rossort
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/2/2018
ms.author: amitsriva
ms.openlocfilehash: 982ae712320cb390b1822de6a7a3980ebfb6251e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30314048"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Háttér-állapot, a diagnosztikai naplók és a metrikák az Alkalmazásátjáró

Azure Application Gateway használatával figyelheti erőforrások a következő módon:

* [Háttér-állapotfigyelő](#back-end-health): Application Gateway biztosítja a kiszolgálók az Azure portálon keresztül, és a PowerShell segítségével a háttér-készletek állapotának figyelésére. A háttér-készletek a diagnosztikai Teljesítménynaplók keresztül állapotát is tájékozódhat.

* [Naplók](#diagnostic-logging): a teljesítmény, hozzáférési és egyéb adatok mentése vagy egy erőforráshoz, ellenőrzési célból felhasznált naplóihoz.

* [Metrikák](#metrics): Application Gateway tartoznak a hét mérni kívánt teljesítményszámlálóinak megtekintése.

## <a name="back-end-health"></a>Háttér-állapota

Alkalmazásátjáró lehetővé teszi az egyes tagok a háttér-készletek a portálon, PowerShell és a parancssori felület (CLI) keresztül állapotának figyelésére. Is megkeresheti az összesített állapotát összesítő a háttér-címkészletek diagnosztikai Teljesítménynaplók keresztül. 

A háttér-állapotjelentés tükrözi az Alkalmazásátjáró állapotmintáihoz a háttér-példányokhoz kimenetét. Ha az ellenőrzés sikeres és a hátteret forgalom fogadására, akkor tekinthető kifogástalan. Ellenkező esetben azt nem megfelelő állapotúnak számít.

> [!IMPORTANT]
> Ha egy alkalmazás átjáró-alhálózatot a hálózati biztonsági csoport (NSG), nyissa meg az alkalmazás átjáróalhálózatot, a bejövő forgalom 65503-65534 porttartományok. Ezeket a portokat a háttér-állapotfigyelő API működéséhez szükségesek.


### <a name="view-back-end-health-through-the-portal"></a>Háttér-állapotának megtekintése a portálon keresztül

A portál háttér-állapotfigyelő valósul meg automatikusan. Válassza ki egy meglévő Alkalmazásátjáró **figyelés** > **háttér állapotfigyelő**. 

Minden tag a háttér-készlet szerepel ezen a lapon (hogy-e a hálózati adapter, IP vagy FQDN Formátumban). Háttér-alkalmazáskészlet neve, a port, a háttér HTTP beállítások neve és az állapot látható. Érvényes állapot értékei **kifogástalan**, **nem kifogástalan állapotú**, és **ismeretlen**.

> [!NOTE]
> Ha megjelenik egy háttér-állapotát **ismeretlen**, győződjön meg arról, hogy a háttér elérésére ne tiltsa le az NSG-szabályok, a felhasználó által megadott útvonal (UDR) vagy egy egyéni DNS-beli virtuális hálózaton.

![Háttér-állapota][10]

### <a name="view-back-end-health-through-powershell"></a>Háttér-állapotának megtekintése a PowerShell segítségével

A következő PowerShell-kód bemutatja, hogyan a háttér-állapotának megtekintéséhez a `Get-AzureRmApplicationGatewayBackendHealth` parancsmagot:

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli-20"></a>Azure CLI 2.0 keresztül háttér-állapotának megtekintése

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Results (Eredmények)

A következő kódrészletet a válasz példáját mutatja be:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Diagnosztikai naplók

Különböző típusú naplók az Azure-ban kezelésére és hibaelhárítására alkalmazásátjárót használja. Néhányat ezek a naplók a portálon keresztül érheti el. Összes napló kinyert Azure Blob Storage tárolóban, és a különböző eszközök, például a megtekintett [Naplóelemzési](../log-analytics/log-analytics-azure-networking-analytics.md), az Excel és a Power bi-ban. Ön többet is megtudhat a különböző típusú naplók az alábbi listából:

* **Tevékenységnapló**: használható [Azure tevékenységi naplóit](../monitoring-and-diagnostics/insights-debugging-with-events.md) (korábbi nevén műveleti naplókat, és a vizsgálati naplók ismert) összes művelet, amely elküldi az Azure-előfizetéshez, és azok állapotának megtekintése. Tevékenység naplóbejegyzések alapértelmezett által gyűjtött, és megtekintheti azokat az Azure portálon.
* **Hozzáférési napló**: Ez a napló segítségével Alkalmazásátjáró hozzáférési minták megtekintése és elemzése a fontos információk, például a hívó IP, kért URL-cím, válasz késés, visszatérési kód, és bájt növelésére és csökkentésére. A hozzáférési napló gyűjtése 300 másodpercenként. Ez a napló Application Gateway-példányonként egy bejegyzést tartalmaz. Az alkalmazás átjárópéldány meghatározható az instanceId tulajdonság.
* **Teljesítménynapló**: Ez a napló segítségével megtekintheti, hogyan Alkalmazásátjáró példányok hajtja végre. Ez a napló minden egyes előfordulás esetén kérelmek teljes száma a kiszolgált, beleértve az átviteli sebesség bájtban teljesítmény információit rögzíti, kiszolgált kérelmek teljes száma, sikertelen kérelmek száma, és a megfelelő és nem megfelelő a háttér-példányok száma. A Teljesítménynapló 60 másodpercenként gyűjti.
* **Tűzfal napló**: Ez a napló segítségével megtekintheti a kérelmeket, amelyeket a rendszer keresztül, amelynek része a webalkalmazási tűzfal Alkalmazásátjáró észlelési vagy megelőzési módját.

> [!NOTE]
> Csak az Azure Resource Manager üzembe helyezési modellben telepített erőforrások esetén érhetők el naplók. A klasszikus üzembe helyezési modellel erőforrások naplók nem használható. Szeretné jobban megismerni a két modell, tekintse meg a [Understanding Resource Manager üzembe helyezési és a klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md) cikk.

A naplók tárolásához három lehetőség közül választhat:

* **A tárfiók**: tárfiókok használata leginkább a naplók Ha naplók hosszabb ideig tárolja, és szükség esetén tekintse át.
* **Az Event hubs**: az Event hubs pedig integrálása az egyéb biztonsági adatokat, és eseményt (SEIM) kezelőeszközök megkapja az értesítéseket a erőforrástól függ.
* **Naplófájl Analytics**: Naplóelemzési használható általános valós idejű figyelés az alkalmazás, vagy megtekint trendeket.

### <a name="enable-logging-through-powershell"></a>A PowerShell segítségével naplózásának engedélyezése

Minden erőforrás-kezelő erőforrás naplózása automatikusan engedélyezve van. Engedélyeznie kell a hozzáférési és naplózási lesznek a naplók keresztül elérhető adatok gyűjtésének elindítása a teljesítményt. A naplózás engedélyezéséhez használja az alábbi lépéseket:

1. Vegye figyelembe a tárfiók erőforrás-azonosító, a naplózási adatok tárolására. Az értéke a következő formátumban: /subscriptions/\<subscriptionId\>/resourceGroups/\<erőforráscsoport-név\>/providers/Microsoft.Storage/storageAccounts/\<tárfióknév\>. Használhatja a tárfiók az előfizetésben. Az Azure portál segítségével előkeresheti ezt az információt.

    ![Portál: tárfiók erőforrás-azonosító](./media/application-gateway-diagnostics/diagnostics1.png)

2. Vegye figyelembe az Alkalmazásátjáró erőforrás-azonosító, amelynek a naplózás engedélyezve van. Az értéke a következő formátumban: /subscriptions/\<subscriptionId\>/resourceGroups/\<erőforráscsoport-név\>/providers/Microsoft.Network/applicationGateways/\<átjáró alkalmazásnév \>. A portál segítségével előkeresheti ezt az információt.

    ![Portál: az Alkalmazásátjáró erőforrás-azonosító](./media/application-gateway-diagnostics/diagnostics2.png)

3. Diagnosztikai naplózás engedélyezése a következő PowerShell-parancsmag segítségével:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Tevékenység naplók nem igényelnek külön tárfiókot. A hozzáférés- és teljesítménynaplózás tárolás használatát szolgáltatás terhel.

### <a name="enable-logging-through-the-azure-portal"></a>Az Azure portálon keresztül naplózásának engedélyezése

1. Az Azure portálon, az erőforrás található, és kattintson a **diagnosztikai naplók**.

   Az Alkalmazásátjáró három érhetők el naplók:

   * Hozzáférési napló
   * Teljesítmény-napló
   * Tűzfal-napló

2. Adatgyűjtés elindításához kattintson **a diagnosztika bekapcsolásához**.

   ![Diagnosztika bekapcsolása][1]

3. A **diagnosztikai beállítások** panel biztosít a diagnosztikai naplók beállításait. Ebben a példában a Naplóelemzési tárolja a naplókat. Kattintson a **konfigurálása** alatt **Naplóelemzési** a munkaterület konfigurálásához. Az event hubs és a storage-fiók használatával is menteni a diagnosztikai naplók.

   ![A konfigurációs folyamat elindítása][2]

4. Válassza ki egy meglévő Naplóelemzési munkaterületet, vagy hozzon létre egy újat. A példa egy meglévőt.

   ![A Naplóelemzési munkaterület beállítások][3]

5. Ellenőrizze a beállításokat, majd kattintson a **mentése**.

   ![Diagnosztikai beállítások panel választása esetén][4]

### <a name="activity-log"></a>Tevékenységnapló

Alapértelmezés szerint az Azure létrehoz a műveletnapló. A naplók Azure eseménynaplók tárolójában 90 napig megőrződnek. További információ a naplók olvasásával a [események és tevékenységnapló](../monitoring-and-diagnostics/insights-debugging-with-events.md) cikk.

### <a name="access-log"></a>Hozzáférési napló

A hozzáférési napló jön létre, csak akkor, ha engedélyezte az összes Application Gateway-példányt, ahogy az az előző lépésben. A tárfiók, ha engedélyezte a naplózást a megadott tárolja az adatokat. Minden egyes hozzáférés Alkalmazásátjáró JSON formátumban jegyzi fel az alábbi példában látható módon:


|Érték  |Leírás  |
|---------|---------|
|instanceId     | A kérés átjáró alkalmazáspéldányt.        |
|clientIP     | A kérelem származási IP.        |
|clientPort     | A kérelem származási port.       |
|httpMethod     | A kérelem által használt HTTP-metódus.       |
|requestUri     | URI-címe, a kérelem érkezett.        |
|RequestQuery     | **Kiszolgáló irányított**: háttér-készlet-példányt, a kérelem lett elküldve.</br>**X-AzureApplicationGateway-napló-ID**: a kérelemhez használt korrelációs azonosítója. A háttér-kiszolgálókon forgalom problémák hibaelhárításához használható. </br>**KISZOLGÁLÓ-állapota**: Application Gateway kapott a háttér HTTP válaszkódot.       |
|UserAgent     | Felhasználói ügynök a HTTP-kérelem fejléc.        |
|httpStatus     | HTTP-állapotkód küld vissza az ügyfélnek az Alkalmazásátjáró.       |
|httpVersion     | A kérelem HTTP-verzió.        |
|receivedBytes     | Csomag érkezett, bájtban kifejezett mérete.        |
|SentBytes| Küldött bájtok a csomag mérete.|
|TimeTaken| (Ezredmásodpercben), hogy mennyi ideig tart a feldolgozandó kérelmet, és a válasz küldését. Ez az az idő, amikor az Alkalmazásátjáró megkapja az idő, amikor a válasz küldése művelet befejezésekor HTTP-kérelem első bájtját időszakaként kiszámítása. Fontos megjegyezni, hogy a Time-Taken mező általában tartalmazza az idő, amely a kérelem-válasz csomagok utazás a hálózaton keresztül. |
|sslEnabled| Hogy a kommunikáció a háttér-címkészletek SSL használható. Érvényes értékei a be- és kikapcsolható.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off"
    }
}
```

### <a name="performance-log"></a>Teljesítmény-napló

A teljesítmény napló jön létre, csak akkor, ha engedélyezte az összes Application Gateway-példányt, ahogy az az előző lépésben. A tárfiók, ha engedélyezte a naplózást a megadott tárolja az adatokat. A Teljesítménynapló-adatok 1 perces időközönként jön létre. A rendszer a következő adatokat naplózza:


|Érték  |Leírás  |
|---------|---------|
|instanceId     |  Átjáró alkalmazáspéldányt amelyek esetében az adatok létrehozása. Az egy több-példány Alkalmazásátjáró soronként egy példány van.        |
|healthyHostCount     | A háttér-készletben található megfelelő gazdagép száma.        |
|unHealthyHostCount     | A háttér-készlet nem megfelelő állapotú gazdagépek száma.        |
|requestCount     | Kiszolgált kérelmek száma.        |
|késés | A háttérben, a kérelmek látja, hogy a példány érkező kéréseket késése (ezredmásodpercben). |
|failedRequestCount| Sikertelen kérelmek száma.|
|Átviteli sebesség| Átlagos átviteli sebessége a legutóbbi naplóban másodpercenként bájtban mért óta.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Késleltetés a időpontban, amikor a HTTP-kérelem első bájtját a HTTP-válasz utolsó bájtját rendszer mikor küldjön idő kiszámítása. Az Alkalmazásátjáró feldolgozási időt és a háttérben, valamint az, hogy a háttér feldolgozni a kérelmet mennyi idő a hálózati költsége összege.

### <a name="firewall-log"></a>Tűzfal-napló

A tűzfal napló jön létre, csak akkor, ha engedélyezte az egyes Alkalmazásátjáró, ahogy az az előző lépésben. Ez a napló is szükséges, hogy a webalkalmazási tűzfal van-e konfigurálva az Alkalmazásátjáró. A tárfiók, ha engedélyezte a naplózást a megadott tárolja az adatokat. A rendszer a következő adatokat naplózza:


|Érték  |Leírás  |
|---------|---------|
|instanceId     | Átjáró alkalmazáspéldányt mely tűzfal adatok létrehozása folyamatban van. Az egy több-példány Alkalmazásátjáró soronként egy példány van.         |
|clientIp     |   A kérelem származási IP.      |
|clientPort     |  A kérelem származási port.       |
|requestUri     | A kérelem érkezett URL-CÍMÉT.       |
|ruleSetType     | A szabály típusának beállítása. A rendelkezésre álló értéke OWASP.        |
|ruleSetVersion     | A szabálykészlet használt verzió. Lehetséges értékek a következők: program 2.2.9-es és 3.0-s.     |
|ruleId     | A kiváltó eseményt Szabályazonosító.        |
|üzenet     | A kiváltó eseményt felhasználóbarát üzenetet. További részletek a részletes adatait tartalmazó részben szerepelnek.        |
|művelet     |  A kérésre végrehajtott műveletet. Lehetséges értékek a következők: letiltott és engedélyezett.      |
|hely     | A hely, amelynek a napló jött létre. Jelenleg csak globális mert szabályok globális szerepel.|
|részletek     | Az eseményindító esemény részleteit.        |
|details.message     | A szabály leírása.        |
|details.data     | A szabály kérelemhez található meghatározott adatok.         |
|details.file     | A szabályt tartalmazó konfigurációs fájlt.        |
|details.line     | A konfigurációs fájlban, az eseményt kiváltó sorszámot.       |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Megtekintése és elemzése a műveletnapló

Megtekintheti és tevékenység napló adatok elemzése az alábbi módszerek egyikével sem:

* **Azure-eszközök**: adatok lekérését a műveletnapló Azure PowerShell, az Azure parancssori felület, az Azure REST API vagy az Azure-portálon keresztül. Részletes útmutatás az egyes módszerek részletes leírást talál az [tevékenység műveletek a Resource Manager](../azure-resource-manager/resource-group-audit.md) cikk.
* **A Power BI**: Ha még nem rendelkezik egy [Power BI](https://powerbi.microsoft.com/pricing) fiók, próbálkozzon az ingyenes. Használatával a [tevékenységi naplóit Azure-csomag a Power BI tartalmat](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), elemezheti az adatokat, előre konfigurált irányítópult közül választhat, vagy testre szabhatja.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Megtekintése és elemzése a hozzáférést, a teljesítmény és a tűzfalon naplók

Azure [Naplóelemzési](../log-analytics/log-analytics-azure-networking-analytics.md) a számláló és az Eseménynapló fájlokat is gyűjthet a Blob storage-fiók. Ez magában foglalja a képi megjelenítések és hatékony keresési lehetőségeket a naplók elemzése.

Is csatlakozni a tárfiókhoz és a JSON naplóbejegyzéseket a hozzáférés és a teljesítmény-naplók beolvasása. Miután letöltötte a JSON-fájlokat, CSV alakíthatja át őket, és megtekinthetők az Excel-, Power bi-ban, vagy bármely más adatábrázolási eszközt.

> [!TIP]
> Ha ismeri a Visual Studio és állandók és a C# változók értékeinek módosítása alapvető fogalmait, használhatja a [konverter eszközök jelentkezzen](https://github.com/Azure-Samples/networking-dotnet-log-converter) elérhető a Githubon.
> 
> 

## <a name="metrics"></a>Mérőszámok

Metrikák egyik újdonsága az egyes Azure-erőforrások ahol teljesítményszámlálók megtekintheti a portálon. Az Alkalmazásátjáró a következő mérőszámokat érhetők el:

- **Jelenlegi kapcsolatok száma**
- **Sikertelen kérelmek**
- **Kifogástalan állapotú gazdagép száma**

   Végezhet egy háttér címkészletet alapján egy adott háttérkészlet megfelelő vagy nem megfelelő gazdagépek megjelenítendő száma.


- **Válaszállapot**

   A válasz állapot kód terjesztési 2xx, 3xx, 4xx és 5xx kategóriák válaszok megjelenítendő további csoportosíthatók.

- **Átviteli sebesség**
- **Kérelmek teljes száma**
- **A nem megfelelő gazdagép száma**

   Végezhet egy háttér címkészletet alapján egy adott háttérkészlet megfelelő vagy nem megfelelő gazdagépek megjelenítendő száma.

Az Alkalmazásátjáró, tallózással **figyelés** kattintson **metrikák**. Válassza ki, ha az elérhető értékek a **METRIKA** legördülő listából.

Az alábbi ábrán egy példa a három metrikák jelennek meg az elmúlt 30 perc lásd:

[![](media/application-gateway-diagnostics/figure5.png "Metrika megtekintése")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Metrikák aktuális listájának megtekintéséhez lásd: [támogatott Azure-figyelő metrikák](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

### <a name="alert-rules"></a>Riasztási szabályok

Megkezdheti a riasztási szabályok alapján egy erőforrás metrikáit. Például egy riasztás hívható meg olyan webhook vagy a rendszergazda e-mail, ha az alkalmazás-átjáró átviteli fent, alatt vagy küszöbértékkel van a megadott idő.

A következő példa bemutatja, hogyan létrehozása, amely egy e-mailt küld a rendszergazda után átviteli megszegése küszöbérték a riasztási szabályt:

1. Kattintson a **metrika riasztás hozzáadása** megnyitásához a **Hozzáadás szabály** panelen. Ezen a panelen a metrikák paneljéről is elérhető.

   !["Metrika riasztás felvétele" gombra][6]

2. Az a **Hozzáadás szabály** panelen, töltse ki a nevét, a feltételt, és értesíti a szakaszok, majd kattintson **OK**.

   * Az a **feltétel** választó, válasszon ki egy négy eleme: **nagyobb, mint**, **nagyobb vagy egyenlő**, **kisebb, mint**, vagy **Kisebb vagy egyenlő, mint**.

   * Az a **időszak** választó, jelöljön ki egy időszakot öt perc hat órán keresztül.

   * Ha **E-mail-tulajdonosok, közreműködőknek és olvasóknak**, az e-mailt dinamikus lehet az erőforráshoz hozzáféréssel rendelkező felhasználók alapján. Ellenkező esetben a felhasználók vesszővel elválasztott listája biztosíthat a **további rendszergazda email(s)** mezőbe.

   ![A szabály panel hozzáadása][7]

Ha a küszöb áldozata ilyen illetéktelen behatolásnak, egy e-mailt, az alábbi képen egy hasonló érkezik:

![E-mailt a megszegéshez tartozó küszöbérték][8]

A riasztások listája egy metrika riasztás létrehozása után jelenik meg. A riasztási szabályok áttekintést biztosít.

![Riasztások és a szabályok listája][9]

Riasztási értesítések kapcsolatos további információkért lásd: [riasztási értesítéseket](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

További információ a webhookok, és hogyan használhatók a riasztások ismertetése, látogasson el a [olyan webhook konfigurálása Azure metrika riasztást](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="next-steps"></a>További lépések

* A számláló és az eseménynaplók színtartományok használatával [Naplóelemzési](../log-analytics/log-analytics-azure-networking-analytics.md).
* [Megjelenítheti a Power bi Azure tevékenységnapló](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogbejegyzést.
* [Megtekintése és elemzése a Power bi-ban és több Azure tevékenységi naplóit](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogbejegyzést.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png

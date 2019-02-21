---
title: Hozzáférés a naplókhoz, a Teljesítménynaplók, a háttérrendszer állapota és a metrikák figyelése az Azure Application Gateway számára
description: Ismerje meg, hogyan engedélyezheti és kezelheti a naplók elérése és a Teljesítménynaplók az Azure Application Gateway számára
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/11/2019
ms.author: amitsriva
ms.openlocfilehash: c93434f060525f2f53f24c511bfa748a31d1fd61
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453298"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway számára

Az Azure Application Gateway használatával figyelemmel kísérheti az erőforrásokat a következőképpen:

* [Háttér-állapotfigyelő](#back-end-health): Az Application Gateway lehetővé teszi, hogy a háttér-készleteket az Azure Portalon keresztül, és a PowerShell lévő kiszolgálók állapotának monitorozásához. A háttér-készleteket, a teljesítmény-diagnosztikai naplók keresztül állapotát is megtalálhatja.

* [Naplók](#diagnostic-logging): A teljesítmény, a hozzáférési és egyéb adatok mentése vagy figyelési célból az erőforrásból felhasznált naplóihoz.

* [Metrikák](#metrics): Az Application Gateway jelenleg rendelkezik hét metrikák megtekintéséhez a teljesítményszámlálókat.

## <a name="back-end-health"></a>Háttér-health

Az Application Gateway lehetővé teszi, hogy a háttér-készletek a portal, PowerShell és a parancssori felület (CLI) egyes tagjai állapotának monitorozásához. Az összesített állapotát is megtalálhatja a teljesítmény-diagnosztikai naplók keresztül háttérkészlet összefoglalása. 

A háttér-állapotjelentés tükrözi az Application Gateway állapotmintát a háttérpéldányok kimenetét. Ha a tesztelés sikeres és a háttérrendszer képes forgalom fogadására, kifogástalan állapotú számít. Ellenkező esetben ez nem megfelelő állapotúnak számít.

> [!IMPORTANT]
> Ha egy Application Gateway-alhálózatot a hálózati biztonsági csoport (NSG), nyissa meg a bejövő forgalmat az Application Gateway alhálózatának 65503 – 65534 alkalmazásport-tartományok. Ezen a porttartományon szükség Azure-infrastruktúra kommunikációjához. A portokat Azure-tanúsítványok védik (zárják le). Megfelelő tanúsítványok nélkül a külső entitások – például az ügyfelek átjárók nem lesz a végpontokra módosításokat kezdeményezhetnek.


### <a name="view-back-end-health-through-the-portal"></a>Háttér-állapotának megtekintése a portálon keresztül

A portál háttérrendszer állapota automatikusan nyújtja. Válassza ki egy meglévő application gateway **figyelés** > **háttérkiszolgáló állapotadatainak**. 

A háttérkészlet minden tagjának szerepel ezen a lapon (akár egy hálózati adapter, IP vagy FQDN szó). Háttérkészlet neve, a port, a háttér-HTTP-beállítások nevét és az állapot jelenik meg. Állapot érvényes értékei a következők **kifogástalan**, **nem kifogástalan**, és **ismeretlen**.

> [!NOTE]
> Ha megjelenik egy háttér-állapotát **ismeretlen**, győződjön meg arról, hogy a háttérbe való hozzáférés nem blokkolja egy NSG-szabályt, a felhasználó által megadott útvonal (UDR) vagy egy egyéni DNS, a virtuális hálózatban.

![Háttér-health][10]

### <a name="view-back-end-health-through-powershell"></a>Háttér-állapotának megtekintése a PowerShell-lel

A következő PowerShell-kód bemutatja, hogyan háttér-állapotának megtekintéséhez használja a `Get-AzureRmApplicationGatewayBackendHealth` parancsmagot:

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Háttér-állapotának megtekintése az Azure CLI-n keresztül

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Results (Eredmények)

Az alábbi kódrészletben látható egy példa a válasz:

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

Segítségével különböző típusú naplók az Azure-ban felügyelet és hibaelhárítás az application Gateway átjárókkal. Ezen naplók egy része a portálról érhető el. Az összes napló ki kell olvasni az Azure Blob storage-ból, és a különböző eszközök, például a megtekintett [naplózza az Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), Excel és a Power bi-ban. További információ a különböző típusú naplók az alábbi listából:

* **Tevékenységnapló**: Használhat [Azure-Tevékenységnaplók](../monitoring-and-diagnostics/insights-debugging-with-events.md) (nevén műveleti naplók és a vizsgálati naplók) minden művelet, amely elküldi az Azure-előfizetéshez, és azok állapotának megtekintése. A tevékenységnaplók bejegyzéseit alapértelmezés szerint gyűjti a rendszer, ezeket az Azure Portalon tekintheti meg.
* **Hozzáférési napló**: Ez a napló használatával az Application Gateway hozzáférési minták megtekintése és elemzése a fontos információkat. Ez magában foglalja a hívó IP, kért URL-cím, válasz késése, visszatérési kód és bájt be és ki. Egy hozzáférési napló 300 másodpercenként gyűjti. Ez a napló az Application Gateway-példányonként több rekordot tartalmaz. Az Application Gateway-példány a instanceId tulajdonság alapján azonosítja.
* **Teljesítménynaplóban**: Ez a napló segítségével megtekintheti, hogyan Application Gateway-példány működését. Ez a napló rögzíti minden példány esetében, beleértve, kiszolgált kérések teljes átviteli sebesség (bájt) teljesítményadatok, a kiszolgált kérések teljes, a sikertelen kérések száma, és a megfelelő és nem kifogástalan állapotú háttér-példányok száma. A teljesítménynaplóban gyűjtött minden 60 másodpercben.
* **Tűzfal naplója**: Ez a napló segítségével, amelynek része a webalkalmazási tűzfallal rendelkező application gateway az észlelésük vagy a megelőzési módban a rendszer naplózza a kérések megtekintését.

> [!NOTE]
> Naplók csak az Azure Resource Manager-alapú üzemi modellben üzembe helyezett erőforrásokhoz érhetők el. Naplók az erőforrások a klasszikus üzemi modellben nem használhat. Szeretné jobban megismerni a két modell, tekintse meg a [Understanding Resource Manager-alapú és klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md) cikk.

A naplók tárolásához három lehetőség közül választhat:

* **Storage-fiók**: Storage-fiókok legjobb használatosak a naplókhoz, amikor a naplók hosszabb ideig tárolja, és szükség esetén tekintse át.
* **Az Event hubs**: Az Event hubs olyan értesítéseket kaphat az erőforrások más biztonsági biztonságiadat- és eseménykezelés (SEIM) felügyeleti eszközökkel való integrálásának remek megoldást.
* **Az Azure Monitor naplóira**: Az Azure Monitor naplóira legjobban használható általános valós idejű figyelést az alkalmazás, vagy tekintse meg a trendeket.

### <a name="enable-logging-through-powershell"></a>PowerShell-lel naplózás engedélyezése

A tevékenységnaplózás automatikusan engedélyezve van minden Resource Manager-erőforráshoz. Elérhetőség és teljesítmény érdekében ezeket a naplókat keresztül elérhető adatok gyűjtésének megkezdéséhez naplózását engedélyezni kell. A naplózás engedélyezéséhez használja az alábbi lépéseket:

1. Jegyezze fel azon Storage-fiók erőforrás-azonosítóját, ahol a naplóadatokat tárolja. Ez az érték a következő formában van: /subscriptions/\<subscriptionId\>/resourceGroups/\<erőforráscsoport-név\>/providers/Microsoft.Storage/storageAccounts/\<tárfiókneve\>. Az előfizetés bármilyen tárfiókját használhatja. Ezeket az információkat az Azure Portalon találhatja meg.

    ![Portál: storage-fiók erőforrás-azonosító](./media/application-gateway-diagnostics/diagnostics1.png)

2. Vegye figyelembe az application gateway-erőforrás azonosítója, amelyhez a naplózás engedélyezve van. Ez az érték a következő formában van: /subscriptions/\<subscriptionId\>/resourceGroups/\<erőforráscsoport-név\>/providers/Microsoft.Network/applicationGateways/\<application gateway neve \>. Ezeket az információkat a portálon találhatja meg.

    ![Portál: az application gateway erőforrás-azonosító](./media/application-gateway-diagnostics/diagnostics2.png)

3. Engedélyezze a diagnosztikai naplózást az alábbi PowerShell-parancsmaggal:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Tevékenységnaplók nem igényel külön tárfiókot. A Storage hozzáférés- és teljesítménynaplózásra való használata szolgáltatási díjjal jár.

### <a name="enable-logging-through-the-azure-portal"></a>Naplózás engedélyezése az Azure Portalon

1. Az Azure Portalon keresse meg az erőforrást, és kattintson **diagnosztikai naplók**.

   Az Application Gateway három naplók érhetők el:

   * Hozzáférési napló
   * Teljesítmény-napló
   * Tűzfal naplója

2. Az adatok gyűjtésének elkezdéséhez kattintson a **Diagnosztika bekapcsolása** elemre.

   ![Diagnosztika bekapcsolása][1]

3. A **diagnosztikai beállítások** panel biztosít a diagnosztikai naplók beállításait. Ebben a példában a Log Analytics tárolja a naplókat. Kattintson a **konfigurálása** alatt **Log Analytics** a munkaterület konfigurálásához. Eseményközpontot és tárfiókot is használhat a diagnosztikai naplók mentésére.

   ![A konfigurációs folyamat indítása][2]

4. Válasszon egy meglévő Log Analytics-munkaterületet, vagy hozzon létre egy újat. Ebben a példában egy már meglévőt.

   ![Log Analytics-munkaterületek lehetőségei][3]

5. Hagyja jóvá a beállításokat, és kattintson a **mentése**.

   ![Diagnosztikai beállítások panelen választása esetén][4]

### <a name="activity-log"></a>Tevékenységnapló

Az Azure alapértelmezés szerint létrehozza a tevékenységnaplóban. A naplók az Azure-eseménynaplók áruházban 90 napig megőrződnek. További információ a naplók olvassa el a [megtekintése, események és a tevékenységnaplóhoz](../monitoring-and-diagnostics/insights-debugging-with-events.md) cikk.

### <a name="access-log"></a>Hozzáférési napló

A hozzáférési napló jön létre, csak akkor, ha engedélyezte az egyes Application Gateway-példányokon, az előző lépésekben leírtaknak megfelelően. Az adatok a storage-fiók, ha engedélyezte a naplózást a megadott tárolódik. Minden egyes hozzáférés az Application Gateway JSON formátumban rögzíti, az alábbi példában látható módon:


|Érték  |Leírás  |
|---------|---------|
|instanceId     | Application Gateway-példány a kérés.        |
|clientIP     | A kérés eredeti IP-cím.        |
|clientPort     | A kérés eredeti port.       |
|httpMethod     | A kérelem által használt HTTP-metódus.       |
|requestUri     | A fogadott kérelem URI azonosítója.        |
|RequestQuery     | **Kiszolgáló útválasztásos**: Háttérkészlet-példányt, amely a kérés érkezett.</br>**X-AzureApplicationGateway-LOG-ID**: Korrelációs azonosító kérelmében. Hibaelhárítás forgalmat a háttér-kiszolgálókon is használható. </br>**A KISZOLGÁLÓÁLLAPOT**: HTTP-válaszkód, az Application Gateway a háttérrendszer cégtől kapott.       |
|UserAgent     | A HTTP-kérelem fejléce a felhasználói ügynök.        |
|httpStatus     | HTTP-állapotkódot küld vissza az ügyfélnek, az Application Gateway.       |
|httpVersion     | A kérelem HTTP-verzióját.        |
|receivedBytes     | Csomag érkezett, a memória méretét.        |
|sentBytes| Küldött bájtok csomag mérete.|
|timeTaken| (Ezredmásodpercben), hogy mennyi ideig tart a feldolgozandó kérelmet, és a válasz küldésének. Ezt az időközt, amikor megkapja az Application Gateway a HTTP-kérés, hogy az idő, amikor a válasz küldése a művelet végeztével az első bájtig eltelt idő szerint számítjuk ki. Fontos megjegyezni, hogy a Time-Taken mező általában tartalmazza az idő, amely a kérések és válaszok csomagok utazás a hálózaton keresztül. |
|sslEnabled| A háttérkészlet-kommunikációt használja-e SSL. Érvényes értékek: kapcsolja ki.|
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

A teljesítmény napló jön létre, csak akkor, ha engedélyezte, az összes Application Gateway-példányt, az előző lépésekben leírtaknak megfelelően. Az adatok a storage-fiók, ha engedélyezte a naplózást a megadott tárolódik. A Teljesítménynapló-adatok 1 perces időközönként jön létre. A következő adatokat naplózza:


|Érték  |Leírás  |
|---------|---------|
|instanceId     |  Application Gateway példány amelyek esetében az adatok létrehozása. A többpéldányos application Gateway egy példány egy sor van.        |
|healthyHostCount     | A háttér-készlet kifogástalan állapotú gazdagépek száma.        |
|unHealthyHostCount     | A háttér-készlet nem megfelelő állapotú gazdagépek száma.        |
|requestCount     | Kiszolgált kérések száma.        |
|késés | Átlagos késése (ezredmásodpercben), amely a kérések szolgál a háttér a példány érkező kérelmeket. |
|failedRequestCount| Sikertelen kérelmek száma.|
|Átviteli sebesség| Átlagos átviteli bájt / másodpercben mért utolsó naplóban óta.|

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
> Késés az idő az első bájtig eltelt HTTP-kérés fogadásakor, amikor az utolsó bájtig eltelt HTTP-válasz küldésének idejét kiszámítása. Az Application Gateway feldolgozási idő plusz a háttérben, valamint a háttér a kérelem feldolgozásához szükséges idő a hálózati költség összege.

### <a name="firewall-log"></a>Tűzfal naplója

A tűzfal napló jön létre, csak akkor, ha engedélyezte az összes application gateway az előző lépésekben leírtaknak megfelelően. Ez a napló azt is megköveteli, hogy a webalkalmazási tűzfallal egy alkalmazásátjárón van konfigurálva. Az adatok a storage-fiók, ha engedélyezte a naplózást a megadott tárolódik. A következő adatokat naplózza:


|Érték  |Leírás  |
|---------|---------|
|instanceId     | Alkalmazásátjárókhoz melyik tűzfal adatokat generált. A többpéldányos application Gateway egy példány egy sor van.         |
|clientIp     |   A kérés eredeti IP-cím.      |
|clientPort     |  A kérés eredeti port.       |
|requestUri     | A fogadott kérelem URL-címe.       |
|ruleSetType     | A szabály típusának beállítása. Elérhető érték OWASP.        |
|ruleSetVersion     | A szabálykészlet használt verziója. Elérhető értékek a következők: 2.2.9-es és 3.0 verziót.     |
|ruleId     | A riasztást kiváltó esemény szabály azonosítója.        |
|message     | A riasztást kiváltó esemény felhasználóbarát üzenet. További részletek találhatók a Részletek területen.        |
|action     |  A kérésre végrehajtott műveletet. Elérhető értékek a következők: letiltott és engedélyezett.      |
|hely     | A hely, amelyhez a napló jött létre. Jelenleg csak globális szabályokat mivel globális szerepel.|
|részletek     | A riasztást kiváltó esemény részletei.        |
|details.message     | A szabály leírását.        |
|details.data     | Adott adatok találhatók. kérés, amely megfelel a szabálynak.         |
|details.file     | A szabályt tartalmazó konfigurációs fájlt.        |
|details.line     | A sor száma, amely kiváltotta az eseményt a konfigurációs fájlban.       |

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

### <a name="view-and-analyze-the-activity-log"></a>A tevékenységnapló megtekintése és elemzése

A tevékenységnaplók adatainak megtekintéséhez és elemzéséhez használja az alábbi módszerek bármelyikét:

* **Azure-eszközök**: A tevékenységnapló használatával az Azure PowerShell-lel, az Azure CLI, az Azure REST API vagy az Azure Portalon lévő információk lekéréséhez. Az egyes módszerek részletes útmutatóit a [Resource Managerrel végzett tevékenységművelet](../azure-resource-manager/resource-group-audit.md) című cikkben találja.
* **Power BI**: Ha még nem rendelkezik egy [Power BI](https://powerbi.microsoft.com/pricing) fiók próbálhatja ki ingyenesen. A [Power BI-hoz készült Azure Activity Logs-tartalomcsomaggal](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) olyan előre konfigurált irányítópultokkal elemezheti az adatokat, amelyeket eredeti formájukban vagy testre szabva is használhat.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Megtekintheti és elemezheti a hozzáférést, a teljesítmény és a tűzfalnaplók

[Az Azure Monitor naplóira](../azure-monitor/insights/azure-networking-analytics.md) a számláló és Eseménynapló fájlokat képes gyűjteni a Blob storage-fiók. A Log Analytics vizualizációkat és hatékony keresési lehetőségeket is tartalmaz a naplók elemzéséhez.

A Storage-fiókjához is csatlakozhat, és lekérheti a hozzáférés- és teljesítménynaplók JSON-naplóbejegyzéseit. A letöltött JSON-fájlokat átalakíthatja CSV-fájlokká, és ezeket megtekintheti az Excelben, Power BI-ban vagy bármely más adatvizualizációs eszközben.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>GoAccess – hozzáférési naplók elemzése

Microsoft közzétette a Resource Manager-sablonnal, amely telepíti és futtatja a népszerű [GoAccess](https://goaccess.io/) analyzer keresse meg a Application Gateway-hozzáférési naplók. GoAccess értékes olyan HTTP-forgalom statisztikáit, például egyedi látogatóinak, a kért fájlokat, a gazdagépek, a operációs rendszerek, a böngészők, HTTP-állapotkódok és egyéb biztosít. További részletekért tekintse meg a [a Resource Manager-sablon mappája a Githubon található információs fájlt](https://aka.ms/appgwgoaccessreadme).

## <a name="metrics"></a>Mérőszámok

Metrikák egyik újdonsága az egyes Azure-erőforrásokhoz, ahol megtekintheti teljesítményszámlálók a portálon. Az Application Gateway a következő metrikák érhetők el:

- **Jelenlegi kapcsolatok száma**
- **Sikertelen kérelmek**
- **Kifogástalan állapotú gazdagépek száma**

   Már szűrhet erre a háttérrendszer készlet alapon történik egy adott háttérkészlet megfelelő vagy nem megfelelő gazdagépek megjelenítése.


- **Válasz állapota**

   Az állapot válaszkódok eloszlása további csoportosíthatók válaszok 2xx, 3xx, 4xx és 5xx kategóriák megjelenítéséhez.

- **Átviteli sebesség**
- **Kérelmek teljes száma**
- **Nem kifogástalan állapotú gazdagépek száma**

   Már szűrhet erre a háttérrendszer készlet alapon történik egy adott háttérkészlet megfelelő vagy nem megfelelő gazdagépek megjelenítése.

Keresse meg az application gateway alatt **figyelés** kattintson **metrikák**. Az elérhető értékeket a **METRIKÁK** legördülő listában találja.

Az alábbi képen egy példa három metrikákkal jelenik meg az elmúlt 30 percben lásd:

[![](media/application-gateway-diagnostics/figure5.png "Metrika megtekintése")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Metrikák aktuális listáját, olvassa el [az Azure monitorban támogatott mérőszámok](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules"></a>Riasztási szabályok

Riasztási szabályok alapján egy erőforrás metrikáit is elindítható. Egy riasztás például webhook meghívása vagy e-mail rendszergazda, ha az application Gateway átviteli fent, alatt vagy küszöbértékkel van a megadott időszakra.

Az alábbi példa végigvezeti egy riasztási szabályt, amely e-mailt küld egy rendszergazda után átviteli szabályok megsértése esetén alkalmazandók egy küszöbérték létrehozása:

1. Kattintson a **metrikariasztás hozzáadása** megnyitásához a **szabály felvétele** panelen. Ezen a panelen, a metrikák paneljén is elérheti.

   !["Metrikariasztás hozzáadása" gomb][6]

2. Az a **szabály felvétele** panelen töltse ki a nevét, a feltétel, és a szakaszok értesítése, és kattintson **OK**.

   * Az a **feltétel** választó, válasszon egyet a négy értékek közül: **Nagyobb, mint**, **nagyobb vagy egyenlő**, **kevesebb mint**, vagy **kisebb vagy egyenlő**.

   * Az a **időszak** választó, jelöljön ki egy időszakot öt perc alatt hat óra.

   * Ha **E-mail-tulajdonosoknak, közreműködőknek és olvasóknak**, az e-mailben dinamikus lehet az adott erőforráshoz hozzáféréssel rendelkező felhasználók száma alapján. Ellenkező esetben megadhatja a felhasználók vesszővel elválasztott listája a **további rendszergazdai email(s)** mezőbe.

   ![Adja hozzá a szabály panel][7]

Ha a küszöbértékének érkezik egy e-mailt, amely egy, az alábbi képen hasonló:

![E-mailben szegve küszöbérték][8]

Metrikariasztás létrehozása után megjelenik a riasztások listája. A riasztási szabályok áttekintést biztosít.

![Riasztások és a szabályok listája][9]

Riasztási értesítések kapcsolatos további információkért lásd: [riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Ismerje meg jobban webhookok, és hogyan használhatja azokat a riasztásokat, a Microsoft [webhook konfigurálása az Azure metrikariasztás](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>További lépések

* Számláló és eseménynaplóinak megjelenítése használatával [naplózza az Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [A Power bi-ban az Azure tevékenységnapló megjelenítése](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogbejegyzést.
* [Megtekintheti és elemezheti a Power BI és egyéb Azure-Tevékenységnaplók](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogbejegyzést.

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

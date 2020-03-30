---
title: Az Azure webalkalmazás-tűzfal naplóinak figyelése
description: A naplók és az Azure webalkalmazás-tűzfal engedélyezésének és kezelésének megismerése
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1b807908c9fb54ecf15de6d44a04760659196a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980969"
---
# <a name="diagnostic-logs-for-azure-web-application-firewall"></a>Diagnosztikai naplók az Azure webalkalmazás tűzfalához

A webalkalmazás-tűzfal erőforrásait naplók használatával figyelheti. A teljesítmény, a hozzáférés és más adatok menthetők, illetve figyelési célokra felhasználhatja az okat egy erőforrásból.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Az Azure-ban különböző típusú naplókat használhat az alkalmazásátjárók kezeléséhez és hibaelhárításához. Ezen naplók egy része a portálról érhető el. Az összes napló kinyerhető az Azure Blob storage-ból, és megtekintheti a különböző eszközök, például [az Azure Monitor naplók,](../../azure-monitor/insights/azure-networking-analytics.md)Excel és a Power BI. A különböző típusú naplókról az alábbi listából tudhat meg többet:

* **Tevékenységnapló:** Az [Azure-tevékenységnaplók](../../azure-resource-manager/management/view-activity-logs.md) (korábbi nevén működési naplók és naplónaplók) segítségével megtekintheti az Azure-előfizetésbe küldött összes műveletet és azok állapotát. A tevékenységnaplók bejegyzéseit alapértelmezés szerint gyűjti a rendszer, ezeket az Azure Portalon tekintheti meg.
* **Hozzáférési napló**: Ezzel a naplóval megtekintheti az Application Gateway hozzáférési mintáit, és elemezheti a fontos információkat. Ez magában foglalja a hívó IP-címét, a kért URL-címet, a válasz késését, a visszatérési kódot, valamint a be- és ki- és be- és ki- és bájtokat. A hozzáférési napló t300 másodpercenként gyűjti. Ez a napló az Application Gateway példányonként egy rekordot tartalmaz. Az Application Gateway-példányt a instanceId tulajdonság azonosítja.
* **Teljesítménynapló**: Ezzel a naplóval megtekintheti az Application Gateway-példányok teljesítményét. Ez a napló rögzíti az egyes példányok teljesítményadatait, beleértve az összes kiszolgált kérelmeket, az átviteli teljesítményt bájtban, a kiszolgált összes kérelmet, a sikertelen kérelmek számát, valamint a kifogástalan és nem megfelelő háttérpéldányok számát. A teljesítménynapló 60 másodpercenként történik. A teljesítménynapló csak a v1 termékváltozathoz érhető el. A v2 termékváltozat hoz [a metrikák](../../application-gateway/application-gateway-metrics.md) teljesítményadatok.
* **Tűzfalnapló**: Ezzel a naplóval megtekintheti a webalkalmazás tűzfalával konfigurált alkalmazásátjáró észlelési vagy megelőzési módján keresztül naplózott kérelmeket.

> [!NOTE]
> A naplók csak az Azure Resource Manager üzembe helyezési modellben üzembe helyezett erőforrásokhoz érhetők el. A klasszikus központi telepítési modellben lévő erőforrásokhoz nem használhat naplókat. A két modell jobb megértését az [Erőforrás-kezelő telepítésének és a klasszikus üzembe helyezési cikknek](../../azure-resource-manager/management/deployment-models.md) a megértése című cikkben talál.

A naplók tárolásához három lehetőség közül választhat:

* **Storage-fiók**: A Storage-fiókok akkor a legmegfelelőbbek a naplók tárolására, ha a naplókat hosszabb ideig tárolják, és szükség esetén áttekintik őket.
* **Eseményközpontok:** Az eseményközpontok nagyszerű lehetőséget kínálnak más biztonsági információkkal és eseménykezelési (SIEM) eszközökkel való integrációra az erőforrásokriasztásletételhez.
* **Azure Monitor naplók:** Az Azure Monitor naplók a legjobb általános valós idejű figyelése az alkalmazás, vagy a trendek.

### <a name="enable-logging-through-powershell"></a>Naplózás engedélyezése a PowerShellen keresztül

A tevékenységnaplózás automatikusan engedélyezve van minden Resource Manager-erőforráshoz. Engedélyeznie kell a hozzáférés és a teljesítmény naplózását, hogy megkezdhesse az ezeken a naplókon keresztül elérhető adatok gyűjtését. A naplózás engedélyezéséhez kövesse az alábbi lépéseket:

1. Jegyezze fel azon Storage-fiók erőforrás-azonosítóját, ahol a naplóadatokat tárolja. Ez az érték a következő formát követi: /előfizetések/\<előfizetésazonosító\>/erőforráscsoportok/\<erőforráscsoport neve\>/szolgáltatások/Microsoft.Storage/storage-fiókok/\<storage-fiók neve\>. Az előfizetés bármilyen tárfiókját használhatja. Ezeket az információkat az Azure Portalon találhatja meg.

    ![Portál: erőforrásazonosító a tárfiókhoz](../media/web-application-firewall-logs/diagnostics1.png)

2. Vegye figyelembe az alkalmazásátjáró erőforrás-azonosítóját, amelyhez engedélyezve van a naplózás. Ez az érték a következő formában\<érhető\>el: /subscriptions/ subscriptionId\</resourceGroups/ erőforráscsoport neve\>/providers/Microsoft.Network/applicationGateways/\<application gateways name\>. Ezeket az információkat a portálon találhatja meg.

    ![Portál: az alkalmazásátjáró erőforrásazonosítója](../media/web-application-firewall-logs/diagnostics2.png)

3. Engedélyezze a diagnosztikai naplózást az alábbi PowerShell-parancsmaggal:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>A tevékenységnaplókhoz nincs szükség külön tárfiókra. A Storage hozzáférés- és teljesítménynaplózásra való használata szolgáltatási díjjal jár.

### <a name="enable-logging-through-the-azure-portal"></a>Naplózás engedélyezése az Azure Portalon

1. Az Azure Portalon keresse meg az erőforrást, és válassza **a Diagnosztikai beállítások lehetőséget.**

   Az Application Gateway esetében három napló érhető el:

   * Hozzáférési napló
   * Teljesítménynapló
   * Tűzfal naplója

2. Az adatgyűjtés megkezdéséhez válassza **a Diagnosztika bekapcsolása**lehetőséget.

   ![A diagnosztika bekapcsolása][1]

3. A **Diagnosztikai beállítások** lap megadja a diagnosztikai naplók beállításait. Ebben a példában a Log Analytics tárolja a naplókat. Eseményközpontot és tárfiókot is használhat a diagnosztikai naplók mentésére.

   ![A konfigurációs folyamat indítása][2]

5. Írja be a beállítások nevét, erősítse meg a beállításokat, és válassza a **Mentés gombot.**

### <a name="activity-log"></a>Tevékenységnapló

Az Azure alapértelmezés szerint létrehozza a tevékenységnaplót. A naplók 90 napig megmaradnak az Azure eseménynaplók tárolójában. Ezekről a naplókról az Események megtekintése és a [tevékenységnapló](../../azure-resource-manager/management/view-activity-logs.md) cikk elolvasásával tudhat meg többet.

### <a name="access-log"></a>Hozzáférési napló

A hozzáférési napló csak akkor jön létre, ha az előző lépésekben részletezett en engedélyezte azt az egyes Application Gateway-példányokon. Az adatok a naplózás engedélyezésekor megadott tárfiókban tárolódnak. Az Application Gateway minden hozzáférése JSON formátumban van naplózva, ahogy az a v1 következő példájában látható:

|Érték  |Leírás  |
|---------|---------|
|instanceId     | A kérést kiszolgáló Alkalmazásátjáró-példány.        |
|ügyfélIP     | A kérelem hez származó IP.Origining IP for the request.        |
|clientPort     | A kérelem hez származó port.       |
|httpMódszer     | A kérelem által használt HTTP-módszer.       |
|requestUri     | A fogadott kérelem URI-ja.        |
|RequestQuery     | **Kiszolgáló-routed:** a kérelemelt háttérkészlet-példány.</br>**X-AzureApplicationGateway-LOG-ID**: A kérelemhez használt korrelációs azonosító. A háttérkiszolgálókon felmerülő forgalmi problémák elhárítására használható. </br>**KISZOLGÁLÓ-ÁLLAPOT**: HTTP-válaszkód, amelyet az Application Gateway a háttérrendszertől kapott.       |
|Useragent     | Felhasználói ügynök a HTTP-kérelem fejlécéből.        |
|httpÁllapot     | Az alkalmazásátjáróból visszaadott HTTP-állapotkód az ügyfélnek.       |
|httpVersion     | A kérelem HTTP-verziója.        |
|fogadott bájt     | A fogadott csomag mérete bájtban.        |
|sentBytes| Az elküldött csomag mérete bájtban.|
|időtaken| A kérelem feldolgozásához és válaszának elküldéséhez szükséges időtartam (ezredmásodpercben). Ezt a rendszer aHTTP-kérelem első bájtjának fogadása korától addig az időpontig számítja ki, amikor a válaszküldési művelet befejeződik. Fontos megjegyezni, hogy az Időtaken mező általában azt az időt tartalmazza, amikor a kérési és válaszcsomagok a hálózaton keresztül haladnak. |
|sslEnabled| Azt jelzi, hogy a háttér-készletekkel való kommunikáció ssl-t használt-e. Az érvényes értékek be- és kivannak kapcsolva.|
|gazda| Az a állomásnév, amellyel a kérés t a háttérkiszolgálónak lett elküldve. Ha a háttér-állomásnév felülbírálva van, ez a név ezt fogja tükrözni.|
|originalHost| Az állomásnév, amellyel az alkalmazásátjáró az ügyféltől megkapta a kérelmet.|
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
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Az Application Gateway és a WAF v2 esetében a naplók egy kicsit több információt jelenítnek meg:

|Érték  |Leírás  |
|---------|---------|
|instanceId     | A kérést kiszolgáló Alkalmazásátjáró-példány.        |
|ügyfélIP     | A kérelem hez származó IP.Origining IP for the request.        |
|clientPort     | A kérelem hez származó port.       |
|httpMódszer     | A kérelem által használt HTTP-módszer.       |
|requestUri     | A fogadott kérelem URI-ja.        |
|Useragent     | Felhasználói ügynök a HTTP-kérelem fejlécéből.        |
|httpÁllapot     | Az alkalmazásátjáróból visszaadott HTTP-állapotkód az ügyfélnek.       |
|httpVersion     | A kérelem HTTP-verziója.        |
|fogadott bájt     | A fogadott csomag mérete bájtban.        |
|sentBytes| Az elküldött csomag mérete bájtban.|
|időtaken| A kérelem feldolgozásához és válaszának elküldéséhez szükséges időtartam (ezredmásodpercben). Ezt a rendszer aHTTP-kérelem első bájtjának fogadása korától addig az időpontig számítja ki, amikor a válaszküldési művelet befejeződik. Fontos megjegyezni, hogy az Időtaken mező általában azt az időt tartalmazza, amikor a kérési és válaszcsomagok a hálózaton keresztül haladnak. |
|sslEnabled| Azt jelzi, hogy a háttér-készletekkel való kommunikáció ssl-t használt-e. Az érvényes értékek be- és kivannak kapcsolva.|
|sslCipher között| Az SSL-kommunikációhoz használt titkosítási csomag (ha az SSL engedélyezve van).|
|sslprotokoll| SSL protokoll t használ (ha az SSL engedélyezve van).|
|kiszolgálórouteszerint| Az alkalmazásátjárót használó háttérkiszolgáló a kérést irányítja.|
|kiszolgáló állapota| A háttérkiszolgáló HTTP-állapotkódja.|
|kiszolgálóResponseLatency| A háttérkiszolgáló válaszának késése.|
|gazda| A kérelem gazdagépfejlécében felsorolt cím.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Teljesítménynapló

A teljesítménynapló csak akkor jön létre, ha az előző lépésekben részletezett en engedélyezte az egyes Application Gateway-példányokon. Az adatok a naplózás engedélyezésekor megadott tárfiókban tárolódnak. A teljesítménynapló adatai 1 perces időközönként jönnek létre. Csak a v1 Termékváltozat esetén érhető el. A v2 termékváltozat hoz [a metrikák](../../application-gateway/application-gateway-metrics.md) teljesítményadatok. A következő adatok at naplózza a rendszer:


|Érték  |Leírás  |
|---------|---------|
|instanceId     |  Alkalmazásátjáró-példány, amelyhez teljesítményadatok jönnek létre. Többpéldányos alkalmazásátjáró esetén példányonként egy sor van.        |
|healthyHostCount (egészségeshostcount)     | A háttérkészletben lévő kifogástalan állapotú gazdagépek száma.        |
|nem healthyhostcount     | A háttérkészletben lévő nem megfelelő állapotú állomások száma.        |
|requestCount (kérésszáma)     | A kézbesített kérelmek száma.        |
|Késleltetés | A kéréseket kiszolgáló példány és a háttértartalék kérelmek átlagos késése (ezredmásodpercben). |
|failedRequestCount| Sikertelen kérelmek száma.|
|korlátozások| Az utolsó napló óta mért átlagos átviteli érték bájt/másodpercben.|

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
> A késés kiszámítása aTTÓL az időponttól kezdve történik, amikor a HTTP-kérelem első bájtja beérkezik, addig az időpontig, amikor a HTTP-válasz utolsó bájtját elküldi. Ez az összeg az Application Gateway feldolgozási idő, valamint a hálózati költségek a háttérrendszer, valamint az időt, hogy a háttérrendszer a kérelem feldolgozásához szükséges.

### <a name="firewall-log"></a>Tűzfal naplója

A tűzfalnapló csak akkor jön létre, ha az előző lépésekben részletezett módon engedélyezte az egyes alkalmazásátjárókhoz. Ez a napló azt is megköveteli, hogy a webalkalmazás tűzfala egy alkalmazásátjárón van konfigurálva. Az adatok a naplózás engedélyezésekor megadott tárfiókban tárolódnak. A következő adatok at naplózza a rendszer:


|Érték  |Leírás  |
|---------|---------|
|instanceId     | Alkalmazásátjáró-példány, amelyhez tűzfaladatok at hoznak létre. Többpéldányos alkalmazásátjáró esetén példányonként egy sor van.         |
|clientIp     |   A kérelem hez származó IP.Origining IP for the request.      |
|clientPort     |  A kérelem hez származó port.       |
|requestUri     | a fogadott kérelem URL-címe.       |
|ruleSetType     | Szabálykészlet típusa. A rendelkezésre álló érték OWASP.        |
|ruleSetVersion     | A szabálykészlet verziója használatban van. A rendelkezésre álló értékek a következők: 2.2.9.     |
|szabály     | Az eseményindító esemény szabályazonosítója.        |
|message     | Felhasználóbarát üzenet az esemény indítóeseményhez. További részletek a részletek részben találhatók.        |
|action     |  A kéréssel kapcsolatos intézkedés. A rendelkezésre álló értékek le vannak tiltva és engedélyezettek.      |
|Oldalon     | Az a hely, amelyhez a naplót létrehozták. Jelenleg csak a Global szerepel a listán, mert a szabályok globálisak.|
|Részletek     | A kiváltó esemény részletei.        |
|részletek.üzenet     | A szabály leírása.        |
|részletek.adatok     | A kérelemben található, a szabálynak megfelelő konkrét adatok.         |
|részleteket.file     | A szabályt tartalmazó konfigurációs fájl.        |
|részletek.vonal     | Az eseményt kiváltó konfigurációs fájl ban lévő sorszám.       |
|Hostname   | Az Alkalmazásátjáró állomásneve vagy IP-címe.    |
|tranzakcióazonosító  | Egyedi azonosító egy adott tranzakcióhoz, amely segít csoportosítani az ugyanazon a kérésen belül történt több szabálysértést.   |
|policyId   | Az alkalmazásátjáróhoz, figyelőhöz vagy elérési úthoz társított tűzfalházirend egyedi azonosítója.   |
|policyScope    | A házirend helye - az értékek lehetnek "Globális", "Figyelő" vagy "Hely".   |
|policyScopeName   | Annak az objektumnak a neve, ahol a házirend et alkalmazza.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>A tevékenységnapló megtekintése és elemzése

A tevékenységnaplók adatainak megtekintéséhez és elemzéséhez használja az alábbi módszerek bármelyikét:

* **Azure-eszközök**: Információkat kérhet le a tevékenységnaplóból az Azure PowerShell-lel, az Azure CLI-vel, az Azure REST API-val vagy az Azure Portallal. Az egyes módszerek részletes útmutatóit a [Resource Managerrel végzett tevékenységművelet](../../azure-resource-manager/management/view-activity-logs.md) című cikkben találja.
* **Power BI**: Ha még nem rendelkezik [Power BI](https://powerbi.microsoft.com/pricing)-fiókkal, ingyenesen kipróbálhatja. A [Power BI sablonalkalmazásokkal](https://docs.microsoft.com/power-bi/service-template-apps-overview)elemezheti az adatokat.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>A hozzáférési, teljesítmény- és tűzfalnaplók megtekintése és elemzése

[Az Azure Monitor naplói gyűjthetik](../../azure-monitor/insights/azure-networking-analytics.md) a számláló és az eseménynapló-fájlokat a Blob storage-fiókból. A Log Analytics vizualizációkat és hatékony keresési lehetőségeket is tartalmaz a naplók elemzéséhez.

A Storage-fiókjához is csatlakozhat, és lekérheti a hozzáférés- és teljesítménynaplók JSON-naplóbejegyzéseit. A letöltött JSON-fájlokat átalakíthatja CSV-fájlokká, és ezeket megtekintheti az Excelben, Power BI-ban vagy bármely más adatvizualizációs eszközben.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Access-naplók elemzése a GoAccess-en keresztül

Közzétettünk egy Resource Manager sablont, amely telepíti és futtatja a népszerű [GoAccess](https://goaccess.io/) naplóelemzőt az Application Gateway Access Naplókhoz. A GoAccess értékes HTTP-forgalmi statisztikákat kínál, például egyedi látogatókat, kért fájlokat, állomásokat, operációs rendszereket, böngészőket, HTTP-állapotkódokat és egyebeket. További részletekért tekintse meg a [GitHub Resource Manager sablonmappájában található Fontos fájl fájlját.](https://aka.ms/appgwgoaccessreadme)

## <a name="next-steps"></a>További lépések

* Az [Azure Monitor naplóinak](../../azure-monitor/insights/azure-networking-analytics.md)használatával vizualizálhatja a számláló- és eseménynaplókat.
* [Vizualizálja azure-tevékenységnaplóját a Power BI blogbejegyzésével.](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)
* [Megtekintheti és elemezheti az Azure-tevékenységnaplókat a Power BI-ban és egy több](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogbejegyzésben.

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png

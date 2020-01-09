---
title: Azure Automation runbook indítása webhooktal
description: Webhook, amely lehetővé teszi, hogy az ügyfél HTTP-hívással indítson el egy runbook a Azure Automationban.  Ez a cikk bemutatja, hogyan hozhat létre webhookot, és hogyan hívhat meg egyet egy runbook elindításához.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: fbf3a48d1e7cb3dd80b6c418d7c916184756b6fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418974"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Azure Automation runbook indítása webhooktal

A *webhookok* lehetővé teszik egy adott runbook elindítását Azure Automation egyetlen http-kéréssel. Ez lehetővé teszi az olyan külső szolgáltatások használatát, mint az Azure DevOps Services, a GitHub, a Azure Monitor-naplók vagy az egyéni alkalmazások a runbookok elindításához anélkül, hogy teljes megoldást kellene implementálni a Azure Automation API használatával.
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

A webhookokat összehasonlíthatja más módszerekkel, hogy runbook indítson el [egy runbook a Azure Automation](automation-starting-a-runbook.md)

> [!NOTE]
> Egy webhook használata a Python-runbook elindításához nem támogatott.

## <a name="details-of-a-webhook"></a>Webhook részletei

A következő táblázat ismerteti azokat a tulajdonságokat, amelyeket egy webhookhoz kell konfigurálni.

| Tulajdonság | Leírás |
|:--- |:--- |
| Név |Bármilyen nevet megadhat egy webhook számára, mivel ez nem teszi elérhetővé az ügyfelet. A rendszer csak a runbook azonosítására szolgál Azure Automationban. <br> Ajánlott eljárásként a webhooknak az azt használó ügyféllel kapcsolatos nevet kell adnia. |
| URL-cím |A webhook URL-címe az az egyedi cím, amelyet az ügyfél HTTP-BEJEGYZÉSsel hív meg, hogy elindítsa a webhookhoz csatolt runbook. A webhook létrehozásakor automatikusan létrejön. Nem adhat meg egyéni URL-címet. <br> <br> Az URL-cím olyan biztonsági jogkivonatot tartalmaz, amely lehetővé teszi, hogy a runbook egy harmadik féltől származó rendszer meghívja a további hitelesítés nélkül. Emiatt a jelszóhoz hasonló módon kell kezelni. Biztonsági okokból a webhook létrehozásakor csak a Azure Portal URL-címét tekintheti meg. A jövőbeli használatra biztonságos helyen jegyezze fel az URL-címet. |
| Lejárat dátuma |A tanúsítványhoz hasonlóan minden webhook lejárati dátummal rendelkezik, amikor már nem használható. Ez a lejárati dátum a webhook létrehozása után módosítható, feltéve, hogy a webhook nem járt le. |
| Engedélyezve |A webhook alapértelmezés szerint engedélyezve van a létrehozásakor. Ha a beállítás letiltva értékre van állítva, akkor az ügyfél nem használhatja azt. Az **enabled (engedélyezve** ) tulajdonságot beállíthatja a webhook létrehozásakor vagy bármikor, ha létrehozták. |

### <a name="parameters"></a>Paraméterek

A webhookok meghatározhatják azon runbook-paraméterek értékeit, amelyek akkor használatosak, amikor az adott webhook elindítja a runbook. A webhooknak tartalmaznia kell a runbook kötelező paramétereinek értékeit, és a választható paraméterek értékeit is tartalmazhatja. A webhookhoz konfigurált paraméterérték a webhook létrehozása után is módosítható. Egy runbook összekapcsolt webhookok mindegyike eltérő paramétereket használhat.

Amikor egy ügyfél egy webhook használatával indít el egy runbook, az nem bírálhatja felül a webhookban definiált paraméterek értékét. Az ügyféltől érkező adatok fogadásához a runbook egyetlen, **$WebhookData**nevű paramétert is elfogad. Ez a paraméter egy [Object] típusú, amely tartalmazza az ügyfél által a POST kérelemben foglalt adatmennyiséget.

![Webhookdata tulajdonságai](media/automation-webhooks/webhook-data-properties.png)

A **$WebhookData** objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| WebhookName |A webhook neve. |
| RequestHeader |A beérkező POST kérelem fejléceit tartalmazó kivonatoló tábla. |
| RequestBody |A beérkező POST kérelem törzse. Ez megőrzi az összes formázást, például a karakterláncot, a JSON-t, az XML-t vagy az űrlap kódolású adatát. A runbook a várt adatformátummal való együttműködésre kell írni. |

Nincs szükség a webhook konfigurálására a **$WebhookData** paraméter támogatásához, és a runbook nem kell elfogadnia. Ha a runbook nem határozza meg a paramétert, a rendszer figyelmen kívül hagyja az ügyféltől érkező kérések részleteit.

> [!NOTE]
> Webhook meghívásakor mindig tárolja az összes paraméter értékét abban az esetben, ha a hívás sikertelen. Ha hálózati leállás vagy kapcsolódási probléma merül fel, nem fogja tudni beolvasni a sikertelen webhook-hívásokat.

Ha a webhook létrehozásakor $WebhookData értéket ad meg, akkor ez az érték felül lesz írva, amikor a webhook elindítja a runbook az ügyfél POST-kérelméről származó adatokkal, még akkor is, ha az ügyfél nem tartalmaz semmilyen, a kérelem törzsében szereplő adatforrást. Ha olyan runbook indít el, amely a webhooktól eltérő módszerrel $WebhookData, megadhatja a runbook által felismert $Webhookdata értékét. Ennek az értéknek olyan objektumnak kell lennie, amely ugyanazokkal a [tulajdonságokkal](#details-of-a-webhook) rendelkezik, mint a $Webhookdata, hogy a runbook megfelelően működjön vele, mintha egy webhook által átadott tényleges Webhookdata működjenek.

Ha például a következő runbook indítja el a Azure Portalból, és egy minta WebhookData szeretne átadni tesztelésre, mert a WebhookData egy objektum, akkor azt JSON-ként kell átadni a felhasználói felületen.

![WebhookData paraméter a felhasználói felületen](media/automation-webhooks/WebhookData-parameter-from-UI.png)

A következő runbook esetén, ha a WebhookData paraméterhez a következő tulajdonságokkal rendelkezik:

* WebhookName: *MyWebhook*
* RequestBody: *[{"ResourceGroup": "myResourceGroup", "Name": "vm01"}, {"ResourceGroup": "myResourceGroup", "Name": "vm02"}]*

Ezután átadja a következő JSON-értéket a WebhookData paraméter felhasználói felületén. A következő példa a kocsivissza és a sortörés karakterrel egyezik meg a webhookból átadott formátummal.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![A WebhookData paraméter elindítása a felhasználói felületen](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Az összes bemeneti paraméter értékeit a rendszer a runbook feladattal naplózza. Ez azt jelenti, hogy az ügyfél által a webhook-kérelemben megadott összes bemenet naplózásra kerül, és bárki számára elérhető lesz, aki hozzáfér az Automation-feladathoz.  Emiatt körültekintően kell megjelennie a webhook-hívásokban lévő bizalmas adatokkal kapcsolatban.

## <a name="security"></a>Biztonság

A webhook biztonsága az URL-címének védelmére támaszkodik, amely egy olyan biztonsági jogkivonatot tartalmaz, amely lehetővé teszi az informatikai rendszer meghívását. A Azure Automation nem hajt végre hitelesítést a kérelemben, amíg a megfelelő URL-címre került. Emiatt a webhookok nem használhatók olyan runbookok, amelyek nagy mértékben bizalmas funkciókat hajtanak végre anélkül, hogy alternatív módszert kellene alkalmazni a kérés érvényesítésére.

A runbook belül megadhatja a logikát annak megállapításához, hogy egy webhook meghívta-e a $WebhookData paraméter **WebhookName** tulajdonságának ellenőrzésével. A runbook további ellenőrzéseket végezhet, ha a **RequestHeader** vagy a **RequestBody** tulajdonságban meghatározott adatokat keres.

Egy másik stratégia az, hogy a runbook végre kell hajtania egy külső feltétel érvényesítését, amikor webhook-kérést kapott. Vegyünk például egy GitHub által meghívott runbook, amikor új véglegesíti a GitHub-tárházat. A runbook csatlakozhat a GitHubhoz annak ellenőrzéséhez, hogy új véglegesítés történt a folytatás előtt.

## <a name="creating-a-webhook"></a>Webhook létrehozása

A következő eljárással hozhat létre egy új webhookot, amely egy runbook kapcsolódik a Azure Portal.

1. A Azure Portal **runbookok lapján** kattintson arra a runbook, amelyet a webhook elkezd megtekinteni a részletes oldalát. Győződjön meg arról, hogy a runbook **állapota** **közzé van téve**.
2. Kattintson a **webhook** elemre az oldal tetején a **webhook hozzáadása** lap megnyitásához.
3. Kattintson az **új webhook létrehozása** elemre a **webhook létrehozása lap**megnyitásához.
4. Adja meg a webhook **nevét**, **lejárati dátumát** és azt, hogy engedélyezve legyen-e. A tulajdonságok részletes ismertetését a [webhook részleteiben](#details-of-a-webhook) tekintheti meg.
5. Kattintson a másolás ikonra, és nyomja le a CTRL + C billentyűkombinációt a webhook URL-címének másolásához. Ezt követően rögzítse egy biztonságos helyen. **A webhook létrehozása után nem kérheti le újra az URL-címet.**

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. Kattintson a **Parameters (paraméterek** ) elemre a runbook paramétereinek értékének megadásához. Kötelező paraméterekkel rendelkező runbook esetén csak akkor lehet a webhookot létrehozni, ha az értékeket is megadja.
1. A webhook létrehozásához kattintson a **Létrehozás** elemre.

## <a name="using-a-webhook"></a>Webhook használata

Ha a létrehozása után webhookot szeretne használni, az ügyfélalkalmazás egy HTTP-BEJEGYZÉST kell kiadnia a webhook URL-címével. A webhook szintaxisa a következő formátumban van:

```http
http://<Webhook Server>/token?=<Token Value>
```

Az ügyfél a következő visszatérési kódok egyikét fogadja a POST kérelemből.

| Kód | Szöveg | Leírás |
|:--- |:--- |:--- |
| 202 |Elfogadva |A kérést elfogadták, és a runbook sikeresen várólistára került. |
| 400 |Hibás kérés |A kérelmet a következő okok egyike miatt nem fogadták el: <ul> <li>A webhook lejárt.</li> <li>A webhook le van tiltva.</li> <li>Az URL-címben szereplő jogkivonat érvénytelen.</li>  </ul> |
| 404 |Nem található |A kérelmet a következő okok egyike miatt nem fogadták el: <ul> <li>A webhook nem található.</li> <li>A runbook nem található.</li> <li>A fiók nem található.</li>  </ul> |
| 500 |Belső kiszolgálóhiba |Az URL-cím érvénytelen, de hiba történt. Küldje el újra a kérelmet. |

Ha a kérelem sikeres, a webhook válasza a következő módon tartalmazza a feladattípus JSON formátumban való megírását. Egyetlen AZONOSÍTÓJÚ feladatot fog tartalmazni, a JSON formátum azonban lehetővé teszi a lehetséges jövőbeli fejlesztéseket.

```json
{"JobIds":["<JobId>"]}
```

Az ügyfél nem tudja meghatározni, hogy mikor fejeződött be a runbook-feladatok, vagy a befejezési állapota a webhookban. Ezt az információt a feladatok AZONOSÍTÓjának használatával határozhatja meg egy másik módszerrel, például a [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) -lel vagy a [Azure Automation API](/rest/api/automation/job)-val.

## <a name="renew-webhook"></a>Webhook megújítása

Webhook létrehozásakor az érvényességi ideje egy év. Az év után a webhook automatikusan lejár. A webhook lejártát követően nem aktiválható újra, ezért el kell távolítani, majd újra létre kell hozni. Ha egy webhook nem érte el a lejárati idejét, kiterjeszthető lehet.

Webhook kibővítéséhez navigáljon a webhookot tartalmazó runbook. Válassza a **webhookok** lehetőséget az **erőforrások**területen. Kattintson a terjeszteni kívánt webhookra, ez a művelet megnyitja a **webhook** lapot.  Válasszon új lejárati dátumot és időpontot, majd kattintson a **Mentés**gombra.

## <a name="sample-runbook"></a>Minta runbook

A következő minta-runbook fogadja a webhook-adatkapcsolatot, és elindítja a kérés törzsében megadott virtuális gépeket. A runbook teszteléséhez az Automation-fiók **runbookok**területén kattintson a **+ runbook hozzáadása**lehetőségre. Ha nem tudja, hogyan hozhat létre runbook, tekintse meg [a Runbook létrehozását](automation-quickstart-create-runbook.md)ismertető témakört.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Példa tesztelése

Az alábbi példa a Windows PowerShellt használja egy webhooktal rendelkező runbook elindításához. Bármely olyan nyelv, amely HTTP-kéréseket tehet, használhat webhookot; Példaként a Windows PowerShellt használjuk.

A runbook a kérelem törzsében a JSON-ban formázott virtuális gépek listáját várja. A runbook ellenőrzi, hogy a fejlécek tartalmaznak-e egy meghatározott üzenetet a webhook-hívó érvényességének ellenőrzéséhez.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

A következő példa a runbook **RequestBody** tulajdonságában elérhető kérelem törzsét mutatja be a **WebhookData**. Ez az érték JSON formátumú, mert a kérelem törzsében szereplő formátum volt.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Az alábbi képen látható a Windows PowerShellből küldött kérelem és az eredményül kapott válasz. A rendszer kinyeri a feladatot a válaszból, és karakterlánccá alakítja át.

![Webhookok gomb](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan használhatja a Azure Automationt az Azure-riasztásokkal kapcsolatos műveletek elvégzéséhez, tekintse meg a [riasztások használata Azure Automation runbook](automation-create-alert-triggered-runbook.md).


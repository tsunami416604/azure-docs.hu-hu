---
title: Egy Azure Automation-runbook kezdődő, és olyan webhook
description: A webhook, amely lehetővé teszi az ügyfél elindít egy forgatókönyvet az Azure Automation egy HTTP-hívás.  Ez a cikk ismerteti a webhook létrehozása, és hogyan hívhatja meg egy runbook indítása.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e047dffa86915b0cd6e8829ea27e0335e7f88cb2
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757156"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Egy Azure Automation-runbook kezdődő, és olyan webhook

A *webhook* lehetővé teszi az adott forgatókönyv indítása az Azure Automationben egyetlen HTTP-kérelem keresztül. Ez lehetővé teszi, hogy a külső szolgáltatások, például a Visual Studio Team Services, GitHub, Azure Naplóelemzés vagy egy Azure Automation API használatával teljes megoldás megvalósításának nélküli runbookok elindítását egyéni alkalmazások.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Összehasonlíthatja az egyéb módszerek runbook indítása webhook [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>A webhook részleteit

A következő táblázat ismerteti a tulajdonságokat, amelyeket konfigurálnia kell egy webhook.

| Tulajdonság | Leírás |
|:--- |:--- |
| Name (Név) |Megadhat egy nevet, egy webhook óta ez nincs felfedve, az ügyfélnek. Azt csak az Ön azonosítására szolgál a runbook az Azure Automationben. <br> Ajánlott eljárásként adjon a webhook nevét, ami azt használja az ügyfél kapcsolódik. |
| URL-cím |A webhook URL-címe az ügyfelek egy HTTP POST a webhook csatolva a runbook elindításához hívja a egyedi cím. A webhook létrehozásakor automatikusan történik. Egy egyéni URL-címe nem adható meg. <br> <br> Az URL-cím egy biztonsági jogkivonatot tartalmaz, amely lehetővé teszi a forgatókönyv további hitelesítés nélküli külső rendszer által meghívandó. Ezért azt kell kezelni, például a jelszó. Biztonsági okokból csak megtekintheti az URL-cím az Azure portálon, a rendszer a webhook létrehozása során. Megjegyzés: az URL-címet egy biztonságos helyre későbbi használatra. |
| Lejárati dátum |Például egy tanúsítványt egyes webhook van ekkor már nem használható lejárati dátuma. A lejárati dátumot a webhook létrehozása után módosítható. |
| Engedélyezve |A webhook alapértelmezés szerint engedélyezve van, ha létrehozták. Ha beállította azt le van tiltva, akkor nincs ügyfél használni tudja. Beállíthatja a **engedélyezve** tulajdonság a webhook, vagy bármikor egyszer létrehozásakor jön létre. |

### <a name="parameters"></a>Paraméterek

A webhook runbook paramétereket, illetve ha a runbook indítja el, hogy a webhook értékeket határozhat meg. A webhook tartalmaznia kell a runbook minden kötelező paraméter értékét, és nem kötelező paraméter értékét is járhatott. A paraméter értékét, úgy konfigurálva, hogy a webhook webhook létrehozása után is módosíthatja. Kapcsolódó egyetlen runbook több webhook egyes eltérő értékek használhatók.

Amikor egy ügyfél elindítja a runbookot egy webhook, nem bírálhatja felül a webhook definiált paraméterértékek. Az adatok fogadásához az ügyfél a runbook neve egyetlen paramétert fogadhat **$WebhookData** típusú [objektum], amely az ügyfél a POST kérelemben tartalmazó adatokat tartalmaz.

![Webhookdata properties](media/automation-webhooks/webhook-data-properties.png)

A **$WebhookData** objektum tulajdonságai a következők:

| Tulajdonság | Leírás |
|:--- |:--- |
| WebhookName |A webhook nevét. |
| RequestHeader |A kivonattábla a fejléceket a bejövő POST kérelem tartalmazó. |
| requestBody |A bejövő POST kérelem törzsét. Ez megőrzi a formázási karakterlánc, JSON, XML, például vagy űrlap kódolású adatot. A runbook kell írni a várt adatformátum dolgozni. |

A webhook támogatásához szükséges konfiguráció hiányában az **$WebhookData** paraméter, és a runbook nem szükséges elfogadását. A runbook nem határoz meg a paramétert, ha az ügyfél által küldött kérelem részleteinek figyelmen kívül hagyja.

Ha megad egy értéket a $WebhookData a webhook létrehozásakor, hogy érték felülbírálja a webhook elindítja a runbookot az adatokat az ügyfél POST-kérelmet, ha akkor is, ha az ügyfél nem vesz be semmilyen adatot a kérés törzsében. Ha elindít egy forgatókönyvet, amely eltérő a webhook módszerrel $WebhookData rendelkezik, a értéket adja meg a runbook által felismert $Webhookdata. Ennek az értéknek kell lennie egy objektum ugyanezzel [tulajdonságok](#details-of-a-webhook) $Webhookdata, így a runbook megfelelően dolgozhat, mintha volt az olyan webhook által átadott tényleges WebhookData használata.

Például ha az Azure-portálon a következő runbook fizet, és hogy bizonyos minta WebhookData tesztelési, mivel WebhookData objektum, azt kell átadni a felhasználói felületen JSON-ként.

![A felhasználói Felületről WebhookData paraméter](media/automation-webhooks/WebhookData-parameter-from-UI.png)

A következő runbookhoz, ha a WebhookData paraméter a következő tulajdonságokkal:

* WebhookName: *MyWebhook*
* RequestBody: *[{"Erőforráscsoport": "contoso.com", "Name": "vm01"}, {"Erőforráscsoport": "contoso.com", "Name": "vm02"}]*

A következő JSON-érték akkor akkor továbbítja a felhasználói felületen a WebhookData paraméter. A következő példa a kocsivissza és értéket ad vissza, és soremelés karaktereket a webhook az átadott formátuma megegyezik.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Kezdő WebhookData paraméter a felhasználói Felületről](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> A runbook-feladat az összes bemeneti paraméterek naplózása. Ez azt jelenti, hogy minden a webhook kérelmet az ügyfél által megadott naplózott és az automation-feladat hozzáféréssel rendelkező bárki számára elérhető lesz.  Emiatt a webhook hívások a bizalmas adatokat is beleértve elővigyázatosnak kell lennie.

## <a name="security"></a>Biztonság

A webhook biztonságát az URL-CÍMÉT, amely egy biztonsági jogkivonatot tartalmaz, amely lehetővé teszi, hogy meg kell hívni az adatvédelmi támaszkodik. Azure Automation szolgáltatásbeli nem végzi el a hitelesítési a kérelem mindaddig, amíg a helyes URL-címet teszi. Emiatt webhookok nem használható a runbookok, amelyek a szigorúan bizalmas valamilyen alternatív, a kérelem ellenőrzése használata nélkül.

Megadhatja, hogy meghívták volna a webhook által úgy, hogy a runbookban logika a **WebhookName** tulajdonság a $WebhookData paraméter. A runbook volt az ellenőrzéshez további az adott információkat keres a **RequestHeader** vagy **RequestBody** tulajdonságait.

Egy másik olyan stratégia, hogy a runbook egy külső állapot néhány ellenőrzés végrehajtása, ha webhook kérelmet kapott. Tegyük fel, ha egy új véglegesítés egy GitHub-tárházban GitHub hívja runbook. A runbook kapcsolódását ellenőrzése, hogy egy új véglegesítési a folytatás előtt történt volna a Githubon.

## <a name="creating-a-webhook"></a>A webhook létrehozása

A következő eljárással hozhat létre egy új webhook csatolva egy runbookot, az Azure portálon.

1. Az a **Runbookok lap** az Azure portálon kattintson a runbook a webhook elinduló megtekintéséhez annak információs lapját.
1. Kattintson a **Webhook** nyissa meg a lap tetején a **hozzáadása Webhook** lap.
1. Kattintson a **hozzon létre új webhook** megnyitásához a **létrehozása webhook oldal**.
1. Adjon meg egy **neve**, **lejárati dátum** a webhook, és hogy azt engedélyezni kell. Lásd: [olyan webhook részleteit](#details-of-a-webhook) további információt ezeket a tulajdonságokat.
1. A Másolás ikonra, majd nyomja meg a Ctrl + C billentyűkombinációval a webhook URL-címét. Biztonságos helyen, majd rögzítse azt. **A webhook létrehozása után újra az URL-cím nem lehet beolvasni.**

   ![Webhook URL-címe](media/automation-webhooks/copy-webhook-url.png)

1. Kattintson a **paraméterek** a runbook-paraméterek értékének megadására. Ha a runbook kötelező paraméterekkel rendelkezik, majd megtörténik nem hozza létre a webhook, ha az értékek.
1. Kattintson a **létrehozása** a webhook létrehozása.

## <a name="using-a-webhook"></a>A webhook használatával

A webhook létrehozása után használatához az ügyfélalkalmazás egy HTTP POST a webhook URL-címmel kell kiadnia. A webhook szintaxisa a következő formátumban:

```http
http://<Webhook Server>/token?=<Token Value>
```

Az ügyfél a következő visszatérési kódok a POST-kérelmet kap.

| Kód | Szöveg | Leírás |
|:--- |:--- |:--- |
| 202 |Elfogadva |Elfogadta a kérést, és a runbook sikeresen várólistára került. |
| 400 |Hibás kérelem |A kérelem nem fogadták a következő okok egyikéből adódóan: <ul> <li>A webhook érvényessége lejárt.</li> <li>A webhook le van tiltva.</li> <li>A lexikális elem szerepel az URL-cím érvénytelen.</li>  </ul> |
| 404 |Nem található |A kérelem nem fogadták a következő okok egyikéből adódóan: <ul> <li>A webhook nem található.</li> <li>A runbook nem található.</li> <li>A fiók nem található.</li>  </ul> |
| 500 |Belső kiszolgálóhiba |Az URL-cím érvénytelen volt, de hiba történt. Küldje el a kérelmet. |

Feltéve, hogy a kérelem sikeres, a webhook válasz tartalmazza a feladatazonosítót JSON formátumban az alábbiak szerint. Egyetlen feladatazonosító fogja tartalmazni, de lehetséges jövőbeli fejlesztések lehetővé teszi a JSON formátumban.

```json
{"JobIds":["<JobId>"]}
```

Az ügyfél nem tudja megállapítani a runbook-feladat befejezését vagy a webhook a befejezési állapotát. Ezt az információt a feladatazonosító egy másik módszerrel például meg tudja határozni [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) vagy a [Azure Automation API](/rest/api/automation/job).

## <a name="sample-runbook"></a>Minta runbook

Az alábbi példában a runbook elfogadja a webhook adatokat fogad, és elindítja a virtuális gépek a kérés törzsében megadott. Ez a runbook tesztelése az Automation-fiók alatt az **Runbookok**, kattintson a **+ Hozzáadás runbook**. Ha egy runbook létrehozása nem tudja, lásd: [runbook létrehozása](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

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

## <a name="test-the-example"></a>A példa tesztelése

Az alábbi példa runbook indítása a webhook a Windows Powershellt használja. Egyetlen nyelven, amelyekkel HTTP-kérelem használhatja a webhook; A Windows PowerShell használata példa.

A runbook által várt paraméterekkel a kérelem törzsében JSON-formázott virtuális gépek listáját.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms

$response = Invoke-RestMethod -Method Post -Uri $uri -Body $body
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

A következő példa bemutatja a runbook számára elérhető a kérelem törzse a **RequestBody** tulajdonsága **WebhookData**. A formátum a kérelem törzsében található, mert ez van formázva JSON-ként.

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

A következő kép bemutatja a Windows PowerShell és az eredményül kapott válasz küldését a kérelmet. A feladat azonosítója a válasz kivonjuk és karakterlánccá alakítja át.

![Webhook gomb](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>További lépések

* Azure Automation műveletet végrehajthat Azure riasztások használatával kapcsolatban lásd: [riasztást használatával indul el, egy Azure Automation-runbook](automation-create-alert-triggered-runbook.md).

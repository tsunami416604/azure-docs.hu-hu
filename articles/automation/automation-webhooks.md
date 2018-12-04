---
title: Azure Automation-runbook kezdve egy webhook
description: Egy webhookot, amely lehetővé teszi, hogy az ügyfél az Azure Automation runbook indítása egy HTTP-hívással.  Ez a cikk bemutatja, hogyan hozhat létre egy webhook, és hogyan hívhat meg egy runbook indítása.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3da4ecb1193959fcc8782f8aa5fdf32c130ee238
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840146"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Azure Automation-runbook kezdve egy webhook

A *webhook* lehetővé teszi, hogy indíthat adott runbookokat az Azure Automation egyetlen HTTP-kérés. Ez lehetővé teszi, hogy a külső szolgáltatásokkal, például az Azure DevOps-szolgáltatásokkal, GitHub, Azure Log Analytics vagy egyéni alkalmazások runbookok elindításához egy Azure Automation API használatával teljes körű megoldás implementálása nélkül.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Egyéb módszerek runbook elindítása webhookok összehasonlíthatja [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Webhook részletei

A következő táblázat ismerteti a tulajdonságokat, amelyeket konfigurálnia kell egy webhook.

| Tulajdonság | Leírás |
|:--- |:--- |
| Name (Név) |Megadhat egy nevet, egy webhook mivel ez az ügyfél nem lesz közzétéve. Ez csak az Ön számára azonosítására szolgál a runbookot, az Azure Automationben. <br> Ajánlott eljárásként meg kell nevezze el a webhook az azokat használó ügyfél kapcsolatos. |
| URL-cím |A webhook URL-címe az az egyedi cím, egy HTTP POST-indítani a runbookot, a webhook csatolva a meghívó ügyfél. A webhook létrehozásakor automatikusan létrejön. Egy egyéni URL-cím nem adható meg. <br> <br> Az URL-címet tartalmaz egy biztonsági jogkivonatot, amely lehetővé teszi a runbook által egy külső rendszer további hitelesítésre. Ezért azt kell kezelni, mint egy jelszó. Biztonsági okokból csak megtekintheti az URL-cím az Azure Portalon a webhook létrehozásakor. Megjegyzés: az URL-címet egy biztonságos helyre későbbi használat céljából. |
| Lejárati dátum |Egy tanúsítványt, mint például az egyes webhookok van ekkor már nem használható lejárati dátuma. A webhook létrehozása után módosíthatja a lejárati dátum. |
| Engedélyezve |Webhook alapértelmezés szerint engedélyezve van, a létrehozásakor. Ha beállította azt le van tiltva, nem az ügyfél nem használhatja azt. Beállíthatja a **engedélyezve** tulajdonság a webhookot, vagy bármikor egyszer létrehozásakor jön létre. |

### <a name="parameters"></a>Paraméterek

Webhook runbook paraméterei a runbook elindításakor a webhookok által használt értékeket határozhat meg. A webhook tartalmaznia kell a runbook minden kötelező paraméterek értékeit, és az opcionális paraméterek értékeinek tartalmazhatnak. A paraméter értékét, úgy konfigurálva, hogy egy webhookot a webhook létrehozása után módosítható. Kapcsolódó egyetlen runbook több webhookok egyes használható különböző paraméterértékekkel.

Amikor egy ügyfél elindít egy runbookot egy webhook használatával, nem bírálhatják felül a webhook a megadott paraméter értékét. Az adatok fogadásához az ügyfél a runbook egy egyetlen paraméter elfogadhat **$WebhookData** típusa [objektum], amely tartalmazza az ügyfél a POST-kérés adatokat tartalmaz.

![Webhookdata properties](media/automation-webhooks/webhook-data-properties.png)

A **$WebhookData** objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| WebhookName |A webhook neve. |
| RequestHeader |A bejövő POST-kérelem a fejléceket tartalmazó kivonattáblát. |
| RequestBody |A bejövő POST-kérés törzse. Ez megőrzi a karakterlánc, JSON, XML, például szöveg formázása vagy űrlap kódolású adatot. A runbook kell megírni, hogy a várt adatformátum dolgozhat. |

Nem tartozik a webhook támogatásához szükséges a konfiguráció a **$WebhookData** paraméterhez, és a runbook nem szükséges az elfogadását. A runbook nem határoz meg a paramétert, ha az ügyfél a kérelem részletes figyelmen kívül hagyja.

Ha megad egy értéket a $WebhookData a webhook létrehozásakor, hogy érték felülíródik a webhook elindítja a runbookot az ügyfél POST-kérés esetén az adatokat még akkor is, ha az ügyfél nem tartalmaz semmilyen adatot a kérelem törzsében. Ha elindít egy runbookot egy webhook eltérő módszerrel $WebhookData rendelkező, a értéket adja meg a runbook által felismert $Webhookdata. Ennek az értéknek kell lennie egy objektum ugyanezzel [tulajdonságok](#details-of-a-webhook) $Webhookdata szerint, hogy a runbook megfelelően dolgozhat, mintha azt a tényleges WebhookData-webhookok által átadott dolgozott.

Például ha vannak a következő forgatókönyv indítása az Azure Portalról, és adja át a mintául szolgáló WebhookData tesztelési, mivel a WebhookData objektum szeretne, akkor kell átadni a felhasználói felületen JSON-fájlként.

![A felhasználói felület WebhookData paraméter](media/automation-webhooks/WebhookData-parameter-from-UI.png)

A következő runbookhoz, ha a WebhookData paraméter a következő tulajdonságokkal rendelkezik:

* WebhookName: *MyWebhook*
* RequestBody: *[{"ResourceGroup": "myResourceGroup", "Name": "vm01"}, {"ResourceGroup": "myResourceGroup", "Name": "vm02"}]*

Majd kellene átadnia az alábbi JSON-érték a WebhookData paraméter a felhasználói felület. A következő példában a a szállítás adja vissza, és soremelés karaktert megfelel-e a formátum, amely átadott webhookból.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Indítsa el a WebhookData paraméter felhasználói felületről](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Összes bemeneti paraméterek naplózza a runbook-feladathoz. Ez azt jelenti, hogy bármelyik megadott információ a webhook-kérelmet az ügyfél által naplózott és az automation-feladat-hozzáféréssel rendelkező mindenki számára elérhető lesz.  Emiatt többek között a bizalmas adatokat a webhook-hívások elővigyázatosnak kell lennie.

## <a name="security"></a>Biztonság

Webhook biztonságát az adatvédelmet, az URL-CÍMÉT, amely tartalmaz egy biztonsági jogkivonatot, amely lehetővé teszi, hogy hívható támaszkodik. Az Azure Automation hajtsa végre semmilyen hitelesítést a kérelem mindaddig, amíg a helyes URL-címre történik. Ebből kifolyólag webhookok nem használható a runbookok, amelyek a szigorúan bizalmas valamilyen alternatív, a kérés érvényesítése használata nélkül.

Hozzáadhatja a logikát meghatározni, hogy azt hívták egy webhook ellenőrzésével a runbookon belül a **WebhookName** $WebhookData paraméter tulajdonság. A runbook sikerült végez, és további ellenőrzés az adott tájékozódni kívánó felhasználóinak a **RequestHeader** vagy **RequestBody** tulajdonságait.

Egy másik olyan stratégia, hogy a runbook egy külső feltétel néhány ellenőrzés végrehajtása, ha a webhook-kérelmet kapott. Vegyük példaként egy runbookot, amely a Githubról hívja meg, amikor egy új véglegesítés egy GitHub-adattárra mutató van. Elképzelhető, hogy a runbook a Githubra, és ellenőrizze, hogy egy új véglegesítés következett a folytatás előtt.

## <a name="creating-a-webhook"></a>Webhook létrehozása

A következő eljárással hozzon létre egy új webhook társított a runbookhoz, az Azure Portalon.

1. Az a **Runbookok oldal** az Azure Portalon kattintson a runbookot, amely a webhook elindítja megtekintéséhez annak információs lapját. Győződjön meg, hogy a runbook **állapot** van **közzétett**.
2. Kattintson a **Webhook** nyissa meg a lap tetején a **Webhook hozzáadása** lapot.
3. Kattintson a **új webhook létrehozása** megnyitásához a **létrehozása webhook lap**.
4. Adjon meg egy **neve**, **lejárati dátum** a webhook, és hogy szabad engedélyezni. Lásd: [webhook részletei](#details-of-a-webhook) bővebben ezeket a tulajdonságokat.
5. A Másolás ikonra, és nyomja le a Ctrl + C billentyűkombinációt a webhook URL-címét. Majd rögzítse azt egy biztonságos helyre. **Miután létrehozta a webhookot, újra nem sikerült beolvasni az URL-címet.**

   ![Webhook URL-címe](media/automation-webhooks/copy-webhook-url.png)

1. Kattintson a **paraméterek** a runbook-paraméterek értékének megadására. Ha a runbook rendelkezik a kötelező paraméterekhez, majd, nem lesznek létre tudja hozni a webhookot, kivéve, ha az értékek.
1. Kattintson a **létrehozás** a webhook létrehozása.

## <a name="using-a-webhook"></a>Egy webhook használatával

Használható egy webhook létrehozása után, az ügyfélalkalmazás egy HTTP POST a webhook URL-címet kell kiadnia. A webhook szintaxisa a következő formátumban:

```http
http://<Webhook Server>/token?=<Token Value>
```

Az ügyfél a következő visszatérési kódok a POST-kérelmet fogad.

| Kód | Szöveg | Leírás |
|:--- |:--- |:--- |
| 202 |Elfogadva |A kérést elfogadták, és a runbook sikeresen várólistára került. |
| 400 |Hibás kérelem |A kérés nem lett elfogadva a következő okok valamelyike: <ul> <li>A webhook lejárt.</li> <li>A webhook le van tiltva.</li> <li>A jogkivonat az URL-cím érvénytelen.</li>  </ul> |
| 404 |Nem található |A kérés nem lett elfogadva a következő okok valamelyike: <ul> <li>A webhook nem található.</li> <li>A runbook nem található.</li> <li>A fiók nem található.</li>  </ul> |
| 500 |Belső kiszolgálóhiba |Az URL-cím érvénytelen volt, de hiba történt. Küldje el újból a kérelmet. |

Feltételezve, hogy a kérelem sikeres, a webhook válaszát feladat Azonosítóját tartalmazza JSON formátumban módon. Egyetlen Feladatazonosító tartalmazni fog, de lehetséges jövőbeli fejlesztések lehetővé teszi a JSON-formátumban.

```json
{"JobIds":["<JobId>"]}
```

Az ügyfél nem tudja megállapítani, ha a runbook-feladat befejeződik, vagy a webhook befejezési állapotát. Ezt az információt a Feladatazonosító használjon egy másik módszerrel, például képes meghatározni [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) vagy a [Azure Automation API](/rest/api/automation/job).

## <a name="sample-runbook"></a>Minta runbook

Az alábbi példában a runbook a webhook-adatokat fogad, és elindítja a virtuális gépek, a kérelem törzsében megadott. Ez a runbook tesztelése alatt az Automation-fiók az **Runbookok**, kattintson a **+ forgatókönyv hozzáadása**. Ha nem ismeri egy runbook létrehozása, [runbook létrehozása](automation-quickstart-create-runbook.md).

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

## <a name="test-the-example"></a>A példában tesztelése

Az alábbi példa a Windows PowerShell használatával indítson el egy runbookot egy webhook. Bármely nyelven, amelyekkel HTTP-kérést használható egy webhook; Windows PowerShell használt példa.

A runbook, a várt a kérelem törzsében található JSON-formátumú virtuális gépek listáját. A runbookot, hogy a fejlécek tartalmaznak egy pontosabban meghatározott is ellenőrzi a webhook hívó érvényesítése üzenet érvénytelen.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-RestMethod -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

Az alábbi példa bemutatja a runbook számára elérhető a kérelem törzsében a **RequestBody** tulajdonsága **WebhookData**. A formátum, a kérelem törzsében található, mert ez van formázva JSON-fájlként.

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

Az alábbi képen látható a kérelmet küld a rendszer a Windows PowerShell és az eredményül kapott választ. A feladat azonosítója a válasz kinyert és karakterlánccá alakítja.

![Webhookok gomb](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>További lépések

* Azure Automation műveletet végrehajthat az Azure Alerts használatával kapcsolatban lásd: [használjon olyan riasztást, egy Azure Automation-runbook elindítása](automation-create-alert-triggered-runbook.md).

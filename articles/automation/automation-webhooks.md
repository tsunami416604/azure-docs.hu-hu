---
title: Azure Automation-runbook indítása webhookkal
description: Egy webhook, amely lehetővé teszi, hogy egy ügyfél elindítson egy runbookot az Azure Automationben http-hívásból.  Ez a cikk ismerteti, hogyan hozhat létre egy webhook, és hogyan lehet hívni egy runbook indításához.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367023"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Azure Automation-runbook indítása webhookkal

A webhook lehetővé teszi, hogy egy külső szolgáltatás egyetlen HTTP-kérelemen keresztül elindítson egy adott runbookot az Azure Automationben. A külső szolgáltatások közé tartozik az Azure DevOps-szolgáltatások, a GitHub, az Azure Monitor-naplók és az egyéni alkalmazások. Egy ilyen szolgáltatás egy webhook használatával elindíthat egy runbookot az Azure Automation API használatával teljes körű megoldás megvalósítása nélkül. Összehasonlíthatja a webhookokat más módszerekkel a runbook [indítása az Azure Automation ben runbook indítása kor.](automation-starting-a-runbook.md)

> [!NOTE]
> Egy webhook használata a Python runbook indításához nem támogatott.

![Webhookokáttekintése](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="webhook-properties"></a>Webhook tulajdonságai

Az alábbi táblázat ismerteti a webhook konfigurálása által konfigurált tulajdonságokat.

| Tulajdonság | Leírás |
|:--- |:--- |
| Név |A webhook neve. Bármilyen nevet megadhat, mivel az nincs kitéve az ügyfélnek. Csak az Azure Automation ben a runbook azonosítására használható. Ajánlott eljárásként meg kell adnia a webhook az ügyfélhez kapcsolódó nevet, amely azt használja. |
| URL-cím |A webhook URL-címe. Ez az az egyedi cím, amelyet egy ügyfél http-postóval hív meg a webhookhoz kapcsolódó runbook elindításához. A webhook létrehozásakor automatikusan létrejön. Nem adhat meg egyéni URL-címet. <br> <br> Az URL-cím tartalmaz egy biztonsági jogkivonatot, amely lehetővé teszi, hogy egy külső rendszer további hitelesítés nélkül meghívja a runbookot. Ezért az URL-t jelszóként kell kezelnie. Biztonsági okokból csak az URL-cím az Azure Portalon, amikor létrehozza a webhook. Jegyezze fel az URL-címet egy biztonságos helyen későbbi használatra. |
| Lejárat dátuma | A webhook lejárati dátuma, amely után már nem használható. A webhook létrehozása után módosíthatja a lejárati dátumot, feltéve, hogy a webhook még nem járt le. |
| Engedélyezve | Beállítás, amely jelzi, hogy a webhook alapértelmezés szerint engedélyezve van-e létrehozáskor. Ha ezt a tulajdonságot letiltotta, egyetlen ügyfél sem használhatja a webhookot. Ezt a tulajdonságot beállíthatja a webhook létrehozásakor vagy bármely más alkalommal annak létrehozása után. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Runbook indításakor használt paraméterek

A webhook értékeket definiálhat a runbook indításakor használt runbook-paraméterekhez. A webhook nak tartalmaznia kell a kötelező runbook-paraméterek értékeit, és a választható paraméterek hez is tartalmazhat értékeket. A webhook-ra konfigurált paraméterérték a webhook létrehozása után is módosítható. Egy runbookhoz kapcsolt több webhook is használhat különböző runbook paraméterértékeket. Amikor egy ügyfél elindítja a runbook egy webhook használatával, nem bírálhatja felül a webhookban definiált paraméterértékeket.

Az ügyféltől történő adatok fogadásához a runbook egyetlen paramétert `WebhookData`támogat. Ez a paraméter olyan objektumot határoz meg, amely olyan adatokat tartalmaz, amelyeket az ügyfél a POST-kérelemben tartalmaz.

![WebhookData tulajdonságai](media/automation-webhooks/webhook-data-properties.png)

A `WebhookData` paraméter a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| `WebhookName` | A webhook neve. |
| `RequestHeader` | A bejövő POST-kérelem fejléceit tartalmazó kivonattáblázat. |
| `RequestBody` | A bejövő POST-kérelem törzse. Ez a törzs megőrzi az adatformázást, például a karakterláncot, a JSON-t, az XML-t vagy az űrlapkódolást. A runbookot meg kell írni, hogy a várt adatformátummal dolgozva működjön. |

Nincs konfiguráció a webhook támogatja a `WebhookData` paramétert, és a runbook nem szükséges, hogy elfogadja azt. Ha a runbook nem határozza meg a paramétert, az ügyféltől küldött kérelem részleteit figyelmen kívül hagyja.

> [!NOTE]
> Webhook hívásakor az ügyfélnek mindig tárolnia kell a paraméterértékeket, ha a hívás sikertelen lesz. Ha hálózati kimaradás vagy csatlakozási probléma merül fel, az alkalmazás nem tudja beolvasni a sikertelen webhook-hívásokat.

Ha megad `WebhookData` egy értéket a webhook létrehozása, felülírja, amikor a webhook elindítja a runbook az ügyfél POST-kérelem ből származó adatokkal. Ez akkor is megtörténik, ha az alkalmazás nem tartalmaz adatokat a kérelem törzsében. 

Ha olyan runbookot indít `WebhookData` el, amely nem webhook-mechanizmushasználatával definiál, megadhat egy értéket, amelyet `WebhookData` a runbook felismer. Ennek az értéknek a `WebhookData` [paraméterrel](#webhook-properties) megegyező tulajdonságokkal rendelkező objektumnak kell lennie, `WebhookData` hogy a runbook ugyanúgy működjön vele, mint a webhook által átadott tényleges objektumokkal.

Ha például a következő runbookot indítja el az Azure Portalról, és néhány webhook-mintaadatot szeretne átadni tesztelésre, át kell adnia az adatokat a JSON-ban a felhasználói felületen.

![WebhookData paraméter a felhasználói felületről](media/automation-webhooks/WebhookData-parameter-from-UI.png)

A következő runbook példában definiáljuk a `WebhookData`következő tulajdonságokat:

* **WebhookName**: SajatWebhook
* **RequestBody :**`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Most átadjuk a következő JSON objektumot `WebhookData` a felhasználói felületen a paraméterhez. Ez a példa kocsivissza és newline karakterekkel megegyezik a webhookból átadott formátummal.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![WebhookData paraméter indítása a felhasználói felületről](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Az Azure Automation naplózza az összes bemeneti paraméter értékeit a runbook-feladattal. Így az ügyfél által a webhook-kérelemben megadott bemenetek naplózva lesznek, és bárki számára elérhetők, akik hozzáférnek az automatizálási feladathoz. Emiatt óvatosnak kell lennie a bizalmas információk webhook-hívásokban való belefoglalásával kapcsolatban.

## <a name="webhook-security"></a>Webhook biztonság

A webhook biztonsága az URL-cím adatvédelmére támaszkodik, amely egy biztonsági jogkivonatot tartalmaz, amely lehetővé teszi a webhook meghívását. Az Azure Automation nem hajt végre hitelesítést a kérelem, amíg a megfelelő URL-címet. Emiatt az ügyfelek ne használja webhooks a runbookok, amelyek rendkívül bizalmas műveleteket anélkül, hogy egy másik eszköz a kérelem érvényesítése.

A runbookon belül logika is szerepelhet annak meghatározásához, hogy webhook nevezi-e meg. A runbook ellenőrizze `WebhookName` a `WebhookData` paraméter tulajdonságát. A runbook további érvényesítést hajthat `RequestHeader` végre, ha bizonyos információkat keres a és `RequestBody` a tulajdonságokban.

Egy másik stratégia az, hogy a runbook végre egy külső feltétel, amikor egy webhook-kérelmet kap. Például fontolja meg egy runbook, amely a GitHub által hívott minden alkalommal, amikor egy új véglegesítésegy GitHub-tárház. Előfordulhat, hogy a runbook csatlakozik a GitHubhoz annak ellenőrzéséhez, hogy egy új véglegesítés történt-e a folytatás előtt.

## <a name="creating-a-webhook"></a>Webhook létrehozása

Az alábbi eljárással hozzon létre egy új webhook ot egy runbookhoz az Azure Portalon.

1. Az Azure Portalon a Runbookok lapján kattintson a runbook, amely a webhook elindul a runbook részleteinek megtekintéséhez. Győződjön meg arról, hogy a runbook **állapota** mező **published**.
2. Kattintson a lap tetején található **Webhook** gombra a Webhook hozzáadása lap megnyitásához.
3. Kattintson **az Új webhook létrehozása gombra** a Webhook létrehozása lap megnyitásához.
4. Töltse ki a **webhook név** és **lejárati dátum** mezőit, és adja meg, hogy engedélyezni kell-e. Ezekről a tulajdonságokról a [Webhook-tulajdonságok](#webhook-properties) című témakörben talál további információt.
5. Kattintson a másolás ikonra, és nyomja le a Ctrl+C billentyűkombinációt a webhook URL-címének másolásához. Akkor jegyezd fel egy biztonságos helyen. 

    > [!NOTE]
    > Miután létrehozta a webhookot, nem tudja újra beolvasni az URL-címet.

   ![Webhook URL-címe](media/automation-webhooks/copy-webhook-url.png)

1. Kattintson **a Paraméterek gombra** a runbook-paraméterek értékeinek megadásához. Ha a runbook kötelező paraméterekkel rendelkezik, nem hozhat létre a webhook, kivéve, ha értékeket ad meg.
1. A webhook létrehozásához kattintson a **Létrehozás** elemre.

## <a name="using-a-webhook"></a>Webhook használata

Webhook használatához a létrehozás után az ügyfélnek `POST` http-kérelmet kell kiadnia a webhook URL-címével. A szintaxis a következő:

```http
http://<Webhook Server>/token?=<Token Value>
```

Az ügyfél az alábbi visszaküldési `POST` kódok egyikét kapja meg a kérelemből.

| Kód | Szöveg | Leírás |
|:--- |:--- |:--- |
| 202 |Elfogadva |A kérelem elfogadásra került, és a runbook várólistára került. |
| 400 |Hibás kérés |A kérelmet a következő okok miatt nem fogadták el: <ul> <li>A webhook lejárt.</li> <li>A webhook le van tiltva.</li> <li>Az URL-címben lévő token érvénytelen.</li>  </ul> |
| 404 |Nem található |A kérelmet a következő okok miatt nem fogadták el: <ul> <li>A webhook nem található.</li> <li>A runbook nem található.</li> <li>A fiók nem található.</li>  </ul> |
| 500 |Belső kiszolgálóhiba |Az URL-cím érvényes volt, de hiba történt. Kérjük, küldje el újra a kérelmet. |

Feltéve, hogy a kérelem sikeres, a webhook válasz tartalmazza a feladat azonosítójának JSON formátumban az alábbiak szerint. Egyetlen feladatazonosítót tartalmaz, de a JSON formátum lehetővé teszi a lehetséges jövőbeli fejlesztéseket.

```json
{"JobIds":["<JobId>"]}
```

Az ügyfél nem tudja megállapítani, hogy a runbook-feladat befejeződött-e, vagy a webhook-ból kifejeződött.The client can't determine when the runbook job completes or its completion status from the webhook. Ezt az információt a feladatazonosító használatával egy másik mechanizmussal, például a [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) vagy az [Azure Automation API](/rest/api/automation/job)használatával találhatja meg.

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>Webhook megújítása

Amikor egy webhook jön létre, tíz éves érvényességi idővel rendelkezik, amely után automatikusan lejár. A webhook lejárta után nem aktiválhatja újra. Csak eltávolíthatja, majd újra létrehozhatja azt. 

Kiterjeszthet egy webhookot, amely még nem érte el a lejárati idejét. Webhook kibővítése:

1. Keresse meg a webhookot tartalmazó runbookot. 
2. Válassza a **Webhooks (Webhooks)** **lehetőséget az Erőforrások csoportban.** 
3. Kattintson a kiterjeszteni kívánt webhookra. 
4. A Webhook lapon válasszon egy új lejárati dátumot és időt, majd kattintson a **Mentés gombra.**

## <a name="sample-runbook"></a>Minta runbook

A következő minta runbook elfogadja a webhook-adatokat, és elindítja a kérelem törzsében megadott virtuális gépeket. A runbook teszteléséhez az Automation-fiókrunbookok területén kattintson **a Runbook létrehozása**elemre. **Runbooks** Ha nem tudja, hogyan hozhat létre runbookot, olvassa el a Runbook létrehozása című [témakört.](automation-quickstart-create-runbook.md)

> [!NOTE]
> Nem grafikus PowerShell-runbookok esetén, `Add-AzAccount` és `Add-AzureRMAccount` a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)aliasai. Használhatja ezeket a parancsmagokat, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verziókra. Előfordulhat, hogy frissítenie kell a modulokat, még akkor is, ha nemrég létrehozott egy új Automation-fiókot.

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

    # Retrieve VMs from Webhook request body
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
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>A minta tesztelése

A következő példa a Windows PowerShell segítségével indít egy runbook egy webhook. Bármely nyelv, amely képes egy HTTP-kérelmet használhat webhook. A Windows PowerShell példaként szolgál.

A runbook a json-ban formázott virtuális gépek listáját várja a kérelem törzsében. A runbook ellenőrzi azt is, hogy a fejlécek tartalmaznak egy meghatározott üzenetet, hogy ellenőrizze, hogy a webhook hívó érvényes.

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

A következő példa a törzsa a kérelem, amely `RequestBody` elérhető `WebhookData`a runbook a tulajdonságában. Ez az érték JSON-ban van formázva, hogy kompatibilis legyen a kérelem törzsében található formátummal.

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

Az alábbi képen látható a Windows PowerShelltől küldött kérelem és az eredményül kapott válasz. A feladat azonosítóját a program kinyeri a válaszból, és karakterlánccé alakítja.

![Webhookok gomb](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>További lépések

* Ha meg szeretné tudni, hogyan használhatja az Azure Automation használatával műveleteket az Azure-riasztásokon, olvassa el a [Riasztás használata az Azure Automation-runbook aktiválásához című témakört.](automation-create-alert-triggered-runbook.md)

---
title: "Egy Azure Automation-runbook kezdődő, és olyan webhook |} Microsoft Docs"
description: "A webhook, amely lehetővé teszi az ügyfél elindít egy forgatókönyvet az Azure Automation egy HTTP-hívás.  Ez a cikk ismerteti a webhook létrehozása, és hogyan hívhatja meg egy runbook indítása."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 9b20237c-a593-4299-bbdc-35c47ee9e55d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: magoedte;bwren;sngun
ms.openlocfilehash: d384a1f6e0f6bf49cf94020265fe5675ffc0029d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Egy Azure Automation-runbook kezdődő, és olyan webhook
A *webhook* lehetővé teszi az adott forgatókönyv indítása az Azure Automationben egyetlen HTTP-kérelem keresztül. Ez lehetővé teszi, hogy a külső szolgáltatások, például a Visual Studio Team Services, GitHub, a Microsoft Operations Management Suite Naplóelemzési vagy egy Azure Automation API használatával teljes megoldás megvalósításának nélküli runbookok elindítását egyéni alkalmazások.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Összehasonlíthatja az egyéb módszerek runbook indítása webhook [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>A webhook részleteit
A következő táblázat ismerteti a tulajdonságokat, amelyeket konfigurálnia kell egy webhook.

| Tulajdonság | Leírás |
|:--- |:--- |
| Név |Megadhat egy nevet, egy webhook óta ez nincs felfedve, az ügyfélnek.  Azt csak az Ön azonosítására szolgál a runbook az Azure Automationben. <br>  Ajánlott eljárásként adjon a webhook kapcsolódik az ügyfél által használt nevet. |
| URL-CÍME |A webhook URL-címe az ügyfelek egy HTTP POST a webhook csatolva a runbook elindításához hívja a egyedi cím.  A webhook létrehozásakor automatikusan történik.  Egy egyéni URL-címe nem adható meg. <br> <br>  Az URL-cím egy biztonsági jogkivonatot, amely lehetővé teszi a forgatókönyv további hitelesítés nélküli külső rendszer által meghívandó tartalmaz. Ezért azt kell kezelni, például a jelszó.  Biztonsági okokból csak megtekintheti az URL-cím az Azure portálon, a rendszer a webhook létrehozása során. Vegye figyelembe a jövőbeli használatra egy biztonságos helyre az URL-címet. |
| Lejárat dátuma |Például egy tanúsítványt egyes webhook van ekkor már nem használható lejárati dátuma.  A lejárati dátumot a webhook létrehozása után módosítható. |
| Engedélyezve |A webhook alapértelmezés szerint engedélyezve van, ha létrehozták.  Ha beállította azt le van tiltva, akkor nincs ügyfél lesz használni tudja.  Beállíthatja a **engedélyezve** tulajdonság a webhook, vagy bármikor egyszer létrehozásakor jön létre. |

### <a name="parameters"></a>Paraméterek
A webhook runbook paramétereket, illetve ha a runbook indítja el, hogy a webhook értékeket határozhat meg. A webhook tartalmaznia kell a runbook minden kötelező paraméter értékét, és nem kötelező paraméter értékét is járhatott. A paraméter értékét, úgy konfigurálva, hogy a webhook még a webhoook létrehozása után módosítható. Kapcsolódó egyetlen runbook több webhook egyes eltérő értékek használhatók.

Amikor egy ügyfél elindítja a runbookot egy webhook, nem bírálhatja felül a webhook definiált paraméterértékek.  Az adatok fogadásához az ügyfél a runbook neve egyetlen paramétert fogadhat **$WebhookData** típusú [objektum], amely az ügyfél a POST kérelemben tartalmazó adatok fogja tartalmazni.

![Webhookdata tulajdonságai](media/automation-webhooks/webhook-data-properties.png)

A **$WebhookData** objektum lesz a következő jellemzőkkel rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| WebhookName |A webhook nevét. |
| RequestHeader |A kivonattábla a fejléceket a bejövő POST kérelem tartalmazó. |
| requestBody |A bejövő POST kérelem törzsét.  Ez megőrzi a formázási karakterlánc, JSON, XML, például vagy űrlap kódolású adatot. A runbook kell írni a várt adatformátum dolgozni. |

A webhook támogatásához szükséges konfiguráció hiányában az **$WebhookData** paraméter, és a runbook nem szükséges elfogadását.  A runbook nem határoz meg a paramétert, ha az ügyfél által küldött kérelem részleteinek figyelmen kívül hagyja.

Ha megad egy értéket a $WebhookData a webhook létrehozásakor, hogy értéket fogja felülbírálva a webhook elindítja a runbookot az adatokat az ügyfél POST-kérelmet, ha akkor is, ha az ügyfél nem vesz be semmilyen adatot a kérés törzsében.  Ha elindít egy forgatókönyvet, amely rendelkezik egy webhook eltérő módszerrel $WebhookData, biztosíthatja, hogy a runbook felismer $Webhookdata értéket.  Ennek az értéknek kell lennie egy objektum ugyanezzel [tulajdonságok](#details-of-a-webhook) $Webhookdata, így a runbook megfelelően dolgozhat, mintha volt az olyan webhook által átadott tényleges WebhookData használata.

Például ha vannak a következő runbook indítása az Azure portálról, és hogy bizonyos minta WebhookData tesztelési, mivel WebhookData objektum, azt kell átadni a felhasználói felületen JSON-ként.

![A felhasználói Felületről WebhookData paraméter](media/automation-webhooks/WebhookData-parameter-from-UI.png)

A fenti runbookhoz, ha a WebhookData paraméter a következő tulajdonságokkal:

1. WebhookName: *MyWebhook*
2. RequestHeader: *= teszt felhasználó*
3. RequestBody: *["VM1", "Vm2 virtuális gépnek"]*

A felhasználói felületen a WebhookData paraméter majd volna adja át a következő JSON-érték:  

* {"WebhookName": "MyWebhook", "RequestHeader": {"Feladó": "Felhasználói teszt"}, "RequestBody": "[\"VM1\",\"vm2 virtuális gépnek\"]"}

![Kezdő WebhookData paraméter a felhasználói Felületről](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> A runbook-feladat az összes bemeneti paraméterek naplózása.  Ez azt jelenti, hogy minden a webhook kérelmet az ügyfél által megadott naplózott és az automation-feladat hozzáféréssel rendelkező bárki számára elérhető lesz.  Emiatt a webhook hívások a bizalmas adatokat is beleértve elővigyázatosnak kell lennie.
>

## <a name="security"></a>Biztonság
A webhook biztonságát az URL-CÍMÉT, amely egy biztonsági jogkivonatot tartalmaz, amely lehetővé teszi, hogy meg kell hívni az adatvédelmi támaszkodik. Azure Automation szolgáltatásbeli nem végzi el a hitelesítési a kérelem mindaddig, amíg a helyes URL-címet teszi. Emiatt webhookok nem használható a runbookok, amelyek a szigorúan bizalmas valamilyen alternatív, a kérelem ellenőrzése használata nélkül.

Megadhatja, hogy meghívták volna a webhook által úgy, hogy a runbookban logika a **WebhookName** tulajdonság a $WebhookData paraméter. A runbook volt az ellenőrzéshez további az adott információkat keres a **RequestHeader** vagy **RequestBody** tulajdonságait.

Egy másik olyan stratégia, hogy a runbook egy külső állapot néhány ellenőrzés végrehajtása, ha webhook kérelmet kapott.  Tegyük fel, ha egy új véglegesítés egy GitHub-tárházban GitHub hívja runbook.  A runbook kapcsolódását GitHub ellenőrzése, hogy egy új véglegesítési valójában történt volna a folytatás előtt.

## <a name="creating-a-webhook"></a>A webhook létrehozása
A következő eljárással hozhat létre egy új webhook csatolva egy runbookot, az Azure portálon.

1. Az a **Runbookok panel** a runbookot elindító a webhook megtekintéséhez a részletek panelen kattintson az Azure-portálon.
2. Kattintson a **Webhook** lehetőségre a panel tetején a **hozzáadása Webhook** panelen. <br>
   ![Webhook gomb](media/automation-webhooks/webhooks-button.png)
3. Kattintson a **hozzon létre új webhook** megnyitásához a **létrehozás webhook panel**.
4. Adjon meg egy **neve**, **lejárati dátum** a webhook, és hogy azt engedélyezni kell. Lásd: [olyan webhook részleteit](#details-of-a-webhook) további információt ezeket a tulajdonságokat.
5. A Másolás ikonra, majd nyomja meg a Ctrl + C billentyűkombinációval a webhook URL-címét.  Biztonságos helyen, majd rögzítse azt.  **A webhook létrehozása után újra az URL-cím nem lehet beolvasni.** <br>
   ![Webhook URL-CÍMÉT](media/automation-webhooks/copy-webhook-url.png)
6. Kattintson a **paraméterek** a runbook-paraméterek értékének megadására.  Ha a runbook kötelező paraméterekkel rendelkezik, majd csak akkor hozhat létre a webhook, kivéve, ha az értékek.
7. Kattintson a **létrehozása** a webhook létrehozása.

## <a name="using-a-webhook"></a>A webhook használatával
A webhook létrehozása után használatához az ügyfélalkalmazás egy HTTP POST a webhook URL-címmel kell kiadnia.  A webhook szintaxisa a következő formátumban lesz.

    http://<Webhook Server>/token?=<Token Value>

Az ügyfél kap a következő visszatérési kódok a POST-kérelmet.  

| Kód | Szöveg | Leírás |
|:--- |:--- |:--- |
| 202 |Elfogadva |Elfogadta a kérést, és a runbook sikeresen várólistára került. |
| 400 |Helytelen kérelem |A kérelem nem fogadták a következő okok valamelyike miatt. <ul> <li>A webhook érvényessége lejárt.</li> <li>A webhook le van tiltva.</li> <li>A lexikális elem szerepel az URL-cím érvénytelen.</li>  </ul> |
| 404 |Nem található |A kérelem nem fogadták a következő okok valamelyike miatt. <ul> <li>A webhook nem található.</li> <li>A runbook nem található.</li> <li>A fiók nem található.</li>  </ul> |
| 500 |Belső kiszolgálóhiba. |Az URL-cím érvénytelen volt, de hiba történt.  Küldje el a kérelmet. |

Feltéve, hogy a kérelem sikeres, a webhook válasz tartalmazza a feladatazonosítót JSON formátumban az alábbiak szerint. Egyetlen feladatazonosító fogja tartalmazni, de lehetséges jövőbeli fejlesztések lehetővé teszi a JSON formátumban.

    {"JobIds":["<JobId>"]}  

Az ügyfél nem tudja megállapítani a runbook-feladat befejezését vagy a webhook a befejezési állapotát.  Ezt az információt a feladatazonosító egy másik módszerrel például meg tudja határozni [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) vagy a [Azure Automation API](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### <a name="example"></a>Példa
Az alábbi példa runbook indítása a webhook a Windows Powershellt használja.  Vegye figyelembe, hogy bármilyen nyelvet használhat, amelyek HTTP-kérelem használhatja a webhook; Windows PowerShell csak használt példa.

A runbook által várt paraméterekkel a kérelem törzsében JSON-formázott virtuális gépek listáját. Azt is vannak adatai, például ki elindítja a runbookot és a dátum és idő alatt a kérelem fejlécében elindul.      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


A következő kép bemutatja a fejléc-információ (használatával egy [Fiddler](http://www.telerik.com/fiddler) nyomkövetési) a kérelemből. Ez magában foglalja a szabványos fejlécek mellett az egyéni dátum és a hozzáadott fejléceket a HTTP-kérések.  Ezek az értékek egy a runbook számára elérhető a **RequestHeaders** tulajdonsága **WebhookData**.

![Webhook gomb](media/automation-webhooks/webhook-request-headers.png)

A következő kép bemutatja a kérelem törzse (használatával egy [Fiddler](http://www.telerik.com/fiddler) nyomkövetési), amely a runbookot a rendelkezésére áll a **RequestBody** tulajdonsága **WebhookData**. A formátum a kérelem törzsében található, mert ez van formázva JSON-ként.     

![Webhook gomb](media/automation-webhooks/webhook-request-body.png)

A következő kép bemutatja a Windows PowerShell és az eredményül kapott válasz küldését a kérelmet.  A feladat azonosítója a válasz kivonjuk és karakterlánccá alakítja át.

![Webhook gomb](media/automation-webhooks/webhook-request-response.png)

Az alábbi példában a runbook az előző példa kérést fogad, és elindítja a virtuális gépek a kérés törzsében megadott.

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param (
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {

            # Collect properties of WebhookData
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."

            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred

            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook."
        }
    }


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>Runbookok elindítása Azure riasztás
A runbookok Webhook-kompatibilis használható reagálni [Azure riasztások](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Az Azure-erőforrások a statisztikai adatokat, például a teljesítmény, rendelkezésre állását és használati Azure riasztások segítségével-készítés révén figyelhető. Figyelési metrikákat alapuló riasztást kaphat, vagy eseményeket az Azure-erőforrások, jelenleg az Automation-fiókok csak metrikák támogatja. Ha egy megadott metrika értéke meghaladja a küszöbértéket, hozzárendelt, vagy ha a beállított esemény akkor váltódik ki, akkor a rendszer értesítést küld a szolgáltatás-rendszergazdák vagy a társadminisztrátoroknak a riasztás feloldásához jelenítse meg, további információ a metrikák és események olvassa el [ Az Azure riasztások](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Használata Azure riasztások értesítési rendszert, mellett is is indítsa el a runbookok riasztás. Azure Automation szolgáltatásbeli lehetővé teszi runbookok webhook-kompatibilis Azure riasztások futtatásához. Ha egy metrika meghaladja a beállított küszöbértéknél majd a riasztási szabály válik aktívvá, és elindítja az automation-webhook, amely ezután végrehajtja a runbookot.

![webhook](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>Riasztás környezete
Érdemes lehet például egy virtuális gépet egy Azure-erőforrás, a CPU-felhasználás gép egyik legfontosabb teljesítményi metrikát. Ha a Processzor kihasználtsága 100 %-os vagy bizonyos egynél hosszú időn keresztül, érdemes próbálja megoldani a problémát a virtuális gép újraindításához. Ez megoldható egy riasztási szabály, amely a virtuális gép konfigurálása, és ez a szabály tart, mint a metrika processzorszázaléka. Itt processzorszázaléka csak példaként lesz végrehajtva, de nincsenek sok más metrikákkal konfigurálható az Azure-erőforrások és a virtuális gép újraindítása egy műveletet, amelyekre szükség van, a probléma megoldásához, konfigurálhatja a forgatókönyvet, hogy más műveletek.

Ha ez a riasztási szabály válik aktívvá, és elindítja a webhook-kompatibilis runbook küld a riasztási környezetben a runbookot. [Riasztás környezete](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) tartalmaz információt talál többek között **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** és **időbélyeg** a forgatókönyvet, hogy az erőforrás, amely a művelet azt véve azonosításához szükséges. Riasztási környezet a szervezet részét beágyazott a **WebhookData** elküldi a runbook és az objektum az elérhető **Webhook.RequestBody** tulajdonság

### <a name="example"></a>Példa
Hozzon létre egy Azure virtuális gép előfizetését és társítható egy [CPU százalékos metrika figyelése riasztás](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Riasztás létrehozása során győződjön meg arról, hogy feltölti a webhook mező a webhook létrehozása során létrehozó webhook URL-CÍMÉT.

Az alábbi példában a runbook lesz kiváltva, ha a riasztási szabály válik aktívvá, és összegyűjti a riasztás környezete paraméterek, amelyek szükségesek a forgatókönyvet, hogy az erőforrás, amely a művelet azt véve azonosítása.

    workflow Invoke-RunbookUsingAlerts
    {
        param (      
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData.
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookBody    =   $WebhookData.RequestBody
            $WebhookHeaders =   $WebhookData.RequestHeader

            # Outputs information on the webhook name that called This
            Write-Output "This runbook was started from webhook $WebhookName."


            # Obtain the WebhookBody containing the AlertContext
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody)
            Write-Output "`nWEBHOOK BODY"
            Write-Output "============="
            Write-Output $WebhookBody

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information
            Write-Output "`nALERT CONTEXT DATA"
            Write-Output "==================="
            Write-Output $AlertContext.name
            Write-Output $AlertContext.subscriptionId
            Write-Output $AlertContext.resourceGroupName
            Write-Output $AlertContext.resourceName
            Write-Output $AlertContext.resourceType
            Write-Output $AlertContext.resourceId
            Write-Output $AlertContext.timestamp

            # Act on the AlertContext data, in our case restarting the VM.
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine.
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential"
            Add-AzureAccount -Credential $cred
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN"

            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        }
        else  
        {
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }



## <a name="next-steps"></a>Következő lépések
* A runbook-indítási különböző módokon részletekért lásd: [Runbook indítása](automation-starting-a-runbook.md).
* A Runbook-feladatok állapotának megtekintése a tudnivalókat lásd a [a Runbook végrehajtása az Azure Automationben](automation-runbook-execution.md).
* Azure Automation műveletet végrehajthat Azure riasztások használatával kapcsolatban lásd: [javítása Azure virtuális gép riasztások Automation-Runbook](automation-azure-vm-alert-integration.md).

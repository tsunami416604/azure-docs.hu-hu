---
title: " Automation-Runbook riasztások Azure virtuális gép javítása |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan lehet Azure virtuális gép riasztások integrálása az Azure Automation-forgatókönyv és a problémák automatikus javítása"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: csand;magoedte
ms.openlocfilehash: 18cccc88ab74235722e2f4886671fc483ab67da8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Azure Automation-forgatókönyv - javítása Azure virtuális gép riasztások
Azure Automation és Azure virtuális gépek kiadott lehet konfigurálni a virtuális gép (VM) riasztások automatizálási runbookok futtatására új szolgáltatása. Ezen új szolgáltatás lehetővé teszi, hogy automatikusan szervizelés szabványos VM riasztás, például a virtuális gép leállítaná.

Korábban, a Virtuálisgép-riasztási szabály létrehozása során létrehozott és [adja meg az Automation-webhook](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) egy runbook a runbook futtatásához, amikor a figyelmeztetés. Azonban ez szükséges a runbook létrehozása, a runbook webhook létrehozása másolása és beillesztése a webhook riasztási szabály létrehozása során munkájának elvégzésére. Új ezzel a kiadással a folyamat nem sokkal könnyebben mert közvetlenül választhat egy runbook listáját riasztási szabály létrehozása során, és válasszon egy Automation-fiók, amely futtatja a runbookot, vagy egyszerűen hozzon létre egy fiókot.

Ebben a cikkben azt mutatja, milyen egyszerűen beállítása az Azure virtuális gép riasztást, és egy Automation-runbook lefusson, amikor elindítja a riasztás konfigurálása. Példaforgatókönyvek közé tartozik a virtuális gép újraindítása, ha a memóriahasználat meghaladja a virtuális Géphez a memóriavesztés alkalmazás miatt egyes küszöbértéket, vagy egy virtuális gép leállítása, ha a Processzor felhasználói idő alatt 1 % az elmúlt egy órában, és nincs használatban. Azt is ismertetjük, hogyan egy szolgáltatásnév az Automation-fiók automatikus létrehozása egyszerűbbé teszi a runbookok Azure riasztási szervizelés használatát.

## <a name="create-an-alert-on-a-vm"></a>Riasztás létrehozása a virtuális gép
A következő lépésekkel, konfigurálhat egy riasztást az küszöböt teljesülésekor runbook elindítására.

> [!NOTE]
> Ebben a kiadásban csak támogatjuk V2 virtuális gép és a támogatás klasszikus virtuális gépek hamarosan megjelenik.  
> 
> 

1. Jelentkezzen be az Azure-portálon, majd kattintson a **virtuális gépek**.  
2. A virtuális gépek közül.  
3. A virtuális gép képernyőn a a **figyelés** kattintson **riasztási szabályok**.
4. Az a **riasztási szabályok** ablaktáblán kattintson a **riasztás hozzáadása**.

Ezzel megnyílik a **riasztási szabály felvétele** lap, ahol konfigurálhatja a riasztás feltételeit, és egy vagy több ezek a lehetőségek közül választhat: valaki e-mailt küldeni, a webhook használatával továbbítja a riasztást egy másik rendszerre, és/vagy futtassa egy Automation-runbook válaszul próbálja meg kijavítani a problémát.

## <a name="configure-a-runbook"></a>A runbook konfigurálása
Egy runbook futtatására, ha a virtuális gép riasztási küszöbérték elérése konfigurálásához jelölje ki **Automation-Runbook**. Az a **runbook konfigurálása** panelen kiválaszthatja a runbook futtatását és az Automation-fiók a runbook futtatását.

![Az Automation-runbook konfigurálása és új Automation-fiók létrehozása](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> Ebben a kiadásban választhat három runbookokat, a szolgáltatás biztosítja – indítsa újra a virtuális gép, állítsa le a virtuális gép vagy távolítsa el a virtuális gép (törölni).  Választhatók ki az egyéb forgatókönyvek vagy a saját runbookok egy későbbi kiadásban elérhető lesz.
> 
> 

![Választhat a Runbookok](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

Miután kiválasztotta a három rendelkezésre álló runbookok egyikét a **Automation-fiók** legördülő lista jelenik meg, és kiválaszthatja, hogy fut a runbook automation-fiók. Runbookok környezetében futtatnia kell egy [Automation-fiók](automation-security-overview.md) szerepel az Azure-előfizetéshez. Kiválaszthatja, hogy már létrehozta, vagy beállíthatja, hogy a hozott létre az Ön új Automation-fiók Automation-fiók.

A runbookok által biztosított hitelesítésre egyszerű szolgáltatás használatával. Ha a runbook futtatásához a meglévő Automation-fiókok egyikével, azt automatikusan a szolgáltatás egyszerű hozza létre. Ha egy új Automation-fiók létrehozása mellett dönt, majd azt automatikusan hozza létre a fiókot, és a szolgáltatás egyszerű. Mindkét esetben két eszközök is létrejönnek az Automation-fiók – a tanúsítvány eszköz nevű **AzureRunAsCertificate** és a kapcsolódási eszköz nevű **AzureRunAsConnection**. A runbookjai **AzureRunAsConnection** hitelesítéséhez az Azure-ral ahhoz, hogy a virtuális gép elleni felügyeleti művelet végrehajtása.

> [!NOTE]
> A szolgáltatás egyszerű az előfizetési hatókört jön létre, és hozzá van rendelve a közreműködő szerepkört. Ez a szerepkör szükséges ahhoz, hogy a fiók automatizálási runbookok futtatását Azure virtuális gépek kezeléséhez.  Egy Automaton fiók és/vagy az egyszerű szolgáltatás létrehozása az egyszeri esemény. A létrehozásuk után a fiók segítségével más Azure virtuális gép riasztások runbookot futtatni.
> 
> 

Amikor rákattint **OK** a riasztás van konfigurálva, és ha az új Automation-fiók létrehozása lehetőséget választotta, létrejön az egyszerű szolgáltatás együtt.  Ez eltarthat néhány másodpercig is.  

![A Runbook konfigurálva](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

A konfiguráció befejezése után megjelenik a runbook nevét a **riasztási szabály felvétele** lap.

![A Runbook konfigurálva](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

Kattintson a **OK** a a **riasztási szabály felvétele** lap.  A riasztási szabály jön létre, és ha a virtuális gép futó állapotban van.

### <a name="enable-or-disable-a-runbook"></a>Engedélyezheti vagy tilthatja le a runbookot
Ha riasztást beállított runbookok, bármikor letilthatja azt a runbook-konfiguráció eltávolítása nélkül. Ez lehetővé teszi a riasztás fut, és lehet, hogy egyes a riasztási szabályok tesztelése és majd később újra engedélyezheti a runbook.

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>Hozzon létre egy runbookot, amely az Azure riasztás
Ha egy runbook Azure riasztási szabály részeként, a runbook a hozzá riasztási adatok kezelésére logika rendelkeznie kell.  Amikor egy runbook a riasztási szabály van beállítva, a webhook jön létre a runbook; a webhook ezután elindítja a runbookot minden alkalommal, amikor elindítja a riasztás segítségével történik.  A tényleges hívás a runbook futtatására egy HTTP POST-kérelmet a webhook URL-cím. A POST kérelem törzse a riasztással kapcsolatban hasznos tulajdonságokat tartalmazó JSON-formátumú objektumot tartalmaz.  Mint az alább látható, a riasztási adatokat tartalmaz adatait, például az előfizetés-azonosító, erőforráscsoport-név, resourceName és resourceType.

### <a name="example-of-alert-data"></a>Riasztási adatokat – példa
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

Amikor az Automation-webhook szolgáltatás megkapja a HTTP POST bontja ki a riasztási adatokat, és átadja a runbook a runbook WebhookData bemeneti paraméter.  Alább a minta-runbookhoz, amely bemutatja, hogyan használja a WebhookData paramétert, és bontsa ki a riasztási adatokat, majd a riasztást kiváltó Azure-erőforrás kezeléséhez van.

### <a name="example-runbook"></a>Példa runbook
```
#  This runbook restarts an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>Összefoglalás
Ha olyan riasztást konfigurálhat egy Azure virtuális gépen, hogy könnyen állítson be egy Automation-runbook automatikusan végrehajtani a javítási művelet, ha a riasztás Elindítja most már rendelkezik. Ebben a kiadásban közül választhat a runbookok, állítsa le, vagy törölje a virtuális gépek, a riasztás forgatókönyvtől függően. Ez az imént elejére forgatókönyvek, ahol megadhatja a műveleteket (értesítés, hibaelhárítási, szervizelés) automatikusan megtörténik, amikor elindítja a riasztás engedélyezése.

## <a name="next-steps"></a>Következő lépések
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)


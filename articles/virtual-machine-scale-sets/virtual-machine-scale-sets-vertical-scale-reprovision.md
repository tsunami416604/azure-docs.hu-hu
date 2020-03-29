---
title: Az Azure virtuálisgép-méretezési készletei függőleges méretezése
description: A virtuális gépek függőleges méretezése az Azure Automation figyelési riasztásaira adott válaszként
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: fa1dda2907e8400491c8d18897bb41fb9cff49fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274435"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Függőleges automatikus skálázás virtuálisgép-méretezési készletekkel

Ez a cikk ismerteti, hogyan függőlegesen skálázható Az Azure [virtuálisgép-méretezési készletek](https://azure.microsoft.com/services/virtual-machine-scale-sets/) újbóli kiépítéssel vagy anélkül. 

Függőleges skálázás, más néven *vertikális felskálázás* és *vertikális leskálázás*, azt jelenti, hogy növeli vagy csökkenti a virtuális gép (VM) méretek válaszul a számítási feladatok. Hasonlítsa össze ezt a viselkedést a [horizontális skálázással](virtual-machine-scale-sets-autoscale-overview.md), más néven *horizontális felskálázással* és *skálázással,* ahol a virtuális gépek száma a munkaterheléstől függően módosul.

A reprovisioning azt jelenti, hogy eltávolít egy meglévő virtuális gép, és helyette egy újat. Ha növeli vagy csökkenti a virtuális gépek méretét egy virtuális gép méretezési csoportban, bizonyos esetekben szeretné átméretezni a meglévő virtuális gépeket, és megőrizni az adatokat, míg más esetekben új méretű virtuális gépeket kell telepítenie. Ez a dokumentum mindkét esetet lefedi.

A függőleges méretezés akkor lehet hasznos, ha:

* A virtuális gépekre épített szolgáltatás (például hétvégén) nem hasznosul. A virtuális gép méretének csökkentése csökkentheti a havi költségeket.
* A virtuális gép méretének növelése a nagyobb igények kielégítéséhez további virtuális gépek létrehozása nélkül.

Beállíthatja a függőleges skálázás, hogy a virtuális gép méretezési készletből származó metrika alapú riasztások alapján aktiválódik. Amikor a riasztás aktiválva van, egy webhookot indít el, amely elindítja a runbookot, amely skálázási beállítása felfelé vagy lefelé skálázható. A vertikális skálázás az alábbi lépésekkel konfigurálható:

1. Hozzon létre egy Azure Automation-fiókot run-as képességgel.
2. Importálja az Azure Automation vertikális skálázású runbookokat az előfizetésbe a virtuálisgép-méretezési készletekhez.
3. Vegyen fel egy webhookot a runbookhoz.
4. Adjon hozzá egy riasztást a virtuális gép méretezési készlet egy webhook értesítés használatával.

> [!NOTE]
> Az első virtuális gép mérete miatt a méreteket lehet méretezni, korlátozott lehet a fürt jelenlegi virtuális gép más méreteinek rendelkezésre állása miatt. Ebben a cikkben használt közzétett automatizálási runbookokban gondoskodunk ebben az esetben, és csak az alábbi virtuális gép méretpárok on belül skálázódik. Ez azt jelenti, hogy a Standard_D1v2 virtuális gép nem lesz hirtelen Standard_G5-re méretezve, vagy Basic_A0 lesz méretezve. A virtualmachine-méretek méretezése fel/le méretezése is nem támogatott. A következő méretpárok között méretezhető:
> 
> | Virtuálisgép-méretek méretezési párja |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Azure Automation-fiók létrehozása run-as képességgel
Az első dolog, amit meg kell tennie, hogy hozzon létre egy Azure Automation-fiókot, amely a virtuális gép méretezési példányok méretezéséhez használt runbookok. A közelmúltban az [Azure Automation](https://azure.microsoft.com/services/automation/) bevezette a "Futtatás fiókként" funkciót, amely leállítja a szolgáltatásnév beállítását a runbookok automatikus futtatásához a felhasználó nevében. További információkért lásd:

* [Forgatókönyvek hitelesítése Azure-beli futtató fiókkal](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Azure Automation vertical scale runbookok importálása az előfizetésbe

A virtuális gép méretezési csoportjainak vertikális méretezéséhez szükséges runbookok már közzé vannak téve az Azure Automation Runbook Gallery-ben. Ha importálni szeretné őket az előfizetésébe, kövesse a cikkben leírt lépéseket:

* [Runbook- és modulkatalógusok az Azure Automationhöz](../automation/automation-runbook-gallery.md)

Válassza a Galéria tallózása lehetőséget a Runbooks menüből:

![Importálandó runbookok][runbooks]

Az importálandó runbookok jelennek meg. Válassza ki a runbookot attól függően, hogy a függőleges skálázást újrakiépítéssel vagy anélkül szeretné-e választani:

![Runbookok gyűjteménye][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Webhook hozzáadása a runbookhoz

Miután importálta a runbookok, adjon hozzá egy webhook a runbook, így egy virtuális gép méretezési csoportból származó riasztást. A Runbook webhook-jának létrehozásáról a következő cikk ismerteti a következő cikket:

* [Az Azure Automation webhookjai](../automation/automation-webhooks.md)

> [!NOTE]
> Győződjön meg róla, hogy másolja a webhook URI bezárása előtt a webhook párbeszédablak, mert szüksége lesz erre a címre a következő szakaszban.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Riasztás hozzáadása a virtuálisgép-méretezési csoporthoz

Az alábbiakban egy PowerShell-parancsfájl, amely bemutatja, hogyan adhat hozzá egy riasztást egy virtuális gép méretezési csoport. Tekintse meg a következő cikket a metrika nevének lenyomásához a riasztás tüzét: [Az Azure Monitor automatikus skálázás gyakori metrikák.](../azure-monitor/platform/autoscale-common-metrics.md)

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Javasoljuk, hogy konfigurálja a riasztás ésszerű időablakot, hogy elkerülje a függőleges skálázás és a kapcsolódó szolgáltatás megszakításának túl gyakran. Vegyünk egy ablak legalább 20-30 perc vagy több. Ha el kell kerülnie a megszakítást, fontolja meg a vízszintes méretezést.
> 
> 

A riasztások létrehozásáról az alábbi cikkekben talál további információt:

* [Azure Monitor PowerShell-gyorsindítási minták](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Az Azure Monitor platformfüggetlen CLI-rövidútmutatós mintái](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Összefoglalás

Ez a cikk egyszerű függőleges méretezési példákat mutatott be. Ezekkel az építőelemekkel – Automation-fiók, runbookok, webhookok, riasztások – számos eseményt összekapcsolhat testre szabott műveletekkel.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png

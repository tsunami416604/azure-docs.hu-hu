---
title: Vertikális skálázása az Azure-beli virtuálisgép-méretezési csoportok |} A Microsoft Docs
description: Függőleges méretezése egy virtuális gépet a figyelési riasztásokat az Azure Automation szolgáltatással a választ
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: manayar
ms.openlocfilehash: 8080cdf78333eed9541311ba67221c713341a21a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741572"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Vertikális automatikus méretezés a virtuálisgép-méretezési csoportok
Ez a cikk bemutatja, hogyan vertikális skálázása az Azure [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) vagy reprovisioning nélkül. Virtuális gépek, amelyek nem szerepelnek a méretezési csoportok vertikális skálázás, tekintse meg [vertikális skálázása az Azure virtuális gépen az Azure Automation](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Vertikális skálázás, más néven *vertikális felskálázás* és *vertikális leskálázás*, az azt jelenti, hogy növelésével vagy csökkentésével válaszul, munkaterhelés számára virtuális gépek (VM) méretét. Hasonlítsa össze ezt a viselkedést az [horizontális skálázást](virtual-machine-scale-sets-autoscale-overview.md), más néven *horizontális felskálázása* és *alatt*, ahol a virtuális gépek száma úgy módosul, a terheléstől függően.

Egy meglévő virtuális gép eltávolítása és cseréje a egy új reprovisioning azt jelenti. Ha növeli vagy csökkenti a virtuálisgép-méretezési csoportot a virtuális gépek méretének beállítása, bizonyos esetekben a meglévő virtuális gépek átméretezése, és az adatok megőrzése, míg más esetben meg kell üzembe helyeznie a új virtuális gépeket az új méretű. Ez a dokumentum áttekint mindkét esetben.

Vertikális skálázás esetekben lehet hasznos:

* Egy szolgáltatás, amely a virtuális gépek a kevésbé használt (például a hétvégéket). Virtuális gép méretének csökkentését, csökkentheti a havi költségeket.
* Növelje a virtuális gép méretét méretezhetők a nagyobb igény szerint további virtuális gépek létrehozása nélkül.

Beállíthat is vertikális skálázás aktivált alapján alapján metrikariasztásokat kell a virtuális gép méretezési csoportból. A riasztás aktiválásakor, aktiválódik egy webhook adott eseményindítók egy runbookot, amely a méretezési csoport méretezhetők felfelé vagy lefelé beállítása. Vertikális skálázás konfigurálható az alábbi lépéseket:

1. Hozzon létre egy Azure Automation-fiók futtató képesség.
2. Virtuálisgép-méretezési csoportokhoz az Azure Automation függőleges méretezés runbookok importálhatók az előfizetés.
3. Webhook hozzáadása a forgatókönyvhöz.
4. A virtuálisgép-méretezési csoportot egy webhook értesítést a riasztás hozzáadásához.

> [!NOTE]
> Vertikális automatikus méretezés csak akkor kerül sor Virtuálisgép-méretek egyes tartományokon belül. Hasonlítsa össze az egyes méretének előírásait akár több, a másikra, mielőtt (Ha nagyobb nem mindig látható, nagyobb méretű virtuális gép mérete). Választhat, hogy skálázni a következő párok méretek között:
> 
> | Pár skálázás Virtuálisgép-méretek |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard G5 |
> | Standard_GS1 |Például a Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Hozzon létre egy Azure Automation-fiók futtató képesség
Először is szüksége, hozzon létre egy Azure Automation-fiók, amely futtatja a runbookokat, a virtuális gép méretezési csoport példányaihoz skálázására. Nemrég [Azure Automation](https://azure.microsoft.com/services/automation/) a "Futtatás mint fiók" gondoskodunk, amely beállítás mentése az egyszerű szolgáltatás révén a runbookok automatikus futtatása egy felhasználó nevében. További információkért lásd:

* [Runbookok hitelesítése Azure-beli futtató fiókkal](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Azure Automation függőleges méretezés runbookok importálása az előfizetés
A runbookok szükséges a virtuális gép méretezési csoportjai vertikális skálázása az Azure Automation forgatókönyv-katalógusában a már közzétett. Importálja őket az előfizetés kövesse a cikkben ismertetett lépések:

* [Azure Automation forgatókönyv- és katalógusok](../automation/automation-runbook-gallery.md)

Válassza a Tallózás a katalógusban a beállítás a Runbookok menüjében:

![A Runbookok importálhatók][runbooks]

A runbookokat, amelyek importálandó jelennek meg. Válassza ki a runbookot, hogy a vertikális skálázás vagy anélkül reprovisioning alapján:

![Runbookkatalógus][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Webhook hozzáadása a forgatókönyvhöz
Runbookok importálása után hozzáadása egy webhookot a runbookhoz, úgy is elindítható a figyelőről érkező egy virtuálisgép-méretezési csoportot. Ebben a cikkben ismertetett egy webhookot hoz létre a Runbook részletei:

* [Az Azure Automation-webhookok](../automation/automation-webhooks.md)

> [!NOTE]
> Ellenőrizze, hogy a webhook párbeszédpanel bezárása, mert szüksége lesz a következő szakaszban Ez a cím előtt másolja a webhook URI-t.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Riasztás hozzáadása a virtuálisgép-méretezési csoporton
Alább egy PowerShell-parancsprogram bemutatja, hogyan riasztás hozzáadása egy virtuálisgép-méretezési csoport beállítása. A mérőszám a riasztás triggerfuttatáskor aktiválandó nevének lekérése a következő cikkben: [Azure Monitor automatikus skálázás gyakori metrikák](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
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
> Javasoljuk, hogy a riasztás ésszerű időn időkeretet konfigurálni ahhoz, hogy kerülje a vertikális skálázás, és minden kapcsolódó szolgáltatás megszakadása, túl gyakran elindítása. Fontolja meg egy legalább 20 – 30 perc vagy több ablakot. Fontolja meg a horizontális skálázás, ha bármely megszakadásának elkerülése érdekében van szüksége.
> 
> 

Riasztások létrehozásával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az Azure Monitor PowerShell rövid minták](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Az Azure platformfüggetlen parancssori figyelő rövid minták](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Összegzés
Ez a cikk bemutatta, egyszerű vertikális méretezési példákat. Ezek építőelemei – Automation-fiók, runbookok, a webhookok, riasztások - az egyéni műveletek egy csoportját, gazdag különféle eseményekre is kapcsolódni.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png

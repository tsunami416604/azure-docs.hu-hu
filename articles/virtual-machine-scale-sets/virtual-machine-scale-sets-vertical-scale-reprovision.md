---
title: "Függőleges skálázása az Azure virtuálisgép-méretezési csoportok |} Microsoft Docs"
description: "Függőleges méretezése a figyelési riasztásokhoz adható az Azure Automation szolgáltatásban válaszul egy virtuális gép"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: madhana
editor: 
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: guybo
ms.openlocfilehash: 9159a5a9041864fe06785829121233379c46bb03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok függőleges skálázva
Ez a cikk ismerteti, hogyan függőleges méretezési Azure [virtuálisgép-méretezési csoportok](https://azure.microsoft.com/services/virtual-machine-scale-sets/) vagy reprovisioning nélkül. Virtuális gépek, amelyek nem szerepelnek a méretezési készlet függőleges méretezését, tekintse meg [függőleges skálázása az Azure virtuális gép az Azure Automation szolgáltatásban](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Függőleges skálázás, más néven *vertikális felskálázás* és *csökkentheti*, azt jelenti, hogy bővítése vagy csökkentése a munkaterhelés válaszul virtuális gépek (VM) méretét. Hasonlítsa össze a [horizontális skálázás](virtual-machine-scale-sets-autoscale-overview.md), más néven *horizontális felskálázás* és *méretezése*, ahol a virtuális gépek száma attól függően, hogy a munkaterhelés módosul.

Reprovisioning azt jelenti, hogy egy meglévő virtuális gép eltávolításakor, és cserélje le a egy újat. Ha növeli vagy csökkenti a Virtuálisgép-méretezési csoportban lévő virtuális gépek méretét, egyes esetekben kívánt méretezze át a meglévő virtuális gépek és az adatok megőrzése mellett, amíg más esetekben kell telepítenie az új méretének új virtuális gépek. Ez a dokumentum ismerteti a mindkét esetben.

Függőleges skálázás esetekben lehet hasznos:

* Egy szolgáltatás, amely a virtuális gépek szükség szerint (például a hétvégéket). Virtuális gép méretének csökkentését, csökkentheti a havi költségeket.
* Virtuálisgép-méretet a nagyobb igény szerint további virtuális gépek létrehozása nélkül folyamatosan növekvő adatmennyiségnek növelését.

Beállíthat függőleges skálázás kiváltott alapján metrika alapuló figyelmeztetések lennie a Virtuálisgép-méretezési csoportban. A riasztás aktiválásakor akkor következik be egy webhook, hogy a skála méretezhető, amely runbook beállítása felfelé vagy lefelé eseményindítók. Függőleges skálázás konfigurálhatja ezeket a lépéseket követve:

1. Hozzon létre egy Azure Automation-fiók futtató funkció.
2. Azure Automation függőleges méretezési runbookok a Virtuálisgép-méretezési készlet importálja az előfizetéshez.
3. A webhook hozzáadása a runbookhoz.
4. Riasztás hozzáadása a Virtuálisgép-méretezési csoportban webhook értesítést használ.

> [!NOTE]
> Függőleges automatikus skálázás csak akkor kerül sor a VM-méretek egyes tartományokon belül. Hasonlítsa össze az egyes méretű paramétereknek egy másik méretezhető, mielőtt (nagyobb szám nem mindig utalhat, hogy nagyobb virtuális gép mérete). Méretezési méretű a következő párok között lehet választani:
> 
> | Virtuálisgép-méretek pár skálázás |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard szintű, G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Hozzon létre egy Azure Automation-fiók futtató funkció
Először is végre kell hajtani, hozzon létre egy Azure Automation-fiók, amely a Virtuálisgép-méretezési csoportban példányok méretezési használt runbookok tárolni fogja. Nemrég [Azure Automation](https://azure.microsoft.com/services/automation/) rendszerben jelent meg a "Futtatás mint fiók" szolgáltatás, amely lehetővé teszi a beállítás a szolgáltatás egyszerű fel automatikusan a runbookok futtatásáért egy felhasználó nevében nagyon egyszerű. További információt az alábbi cikkben:

* [Runbookok hitelesítése Azure-beli futtató fiókkal](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Azure Automation függőleges méretezési runbookok importálnia kell az előfizetéshez
A runbookok függőleges méretezése a Virtuálisgép-méretezési készlet szükség van már közzé van téve az Azure Automation-Runbook katalógus. Importálja őket az előfizetés kövesse a cikkben található lépéseket:

* [Az Azure Automation forgatókönyv- és minták](../automation/automation-runbook-gallery.md)

Kattintson a Tallózás beállítása a Runbookok menüjének:

![Runbookok, importálandók][runbooks]

A runbookokat, importálandók kell jelennek meg. Válassza ki a runbookot, akkor függőleges vagy anélkül reprovisioning skálázás alapján:

![Runbookok gyűjteménye][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>A webhook hozzáadása a runbookhoz
Miután importált a kell runbookokat hozzáadása egy webhook a runbookhoz úgy is elindítható a Virtuálisgép-méretezési csoportban a riasztások alapján. A webhook létrehozása a runbook részletes adatait ebben a cikkben ismertetett:

* [Azure Automation-webhook](../automation/automation-webhooks.md)

> [!NOTE]
> Ellenőrizze, hogy a webhook párbeszédpanel bezárása, szüksége lesz a következő szakaszban előtt másolja át a webhook URI.
> 
> 

## <a name="add-an-alert-to-your-vm-scale-set"></a>Riasztás hozzáadása a Virtuálisgép-méretezési csoportban
Az alábbiakban van egy PowerShell-parancsfájlt, amely bemutatja, hogyan riasztást hozzáadása a Virtuálisgép-méretezési csoportban. A nevét, a metrika az érvényesítést a riasztás a következő cikkben: [Azure figyelő automatikus skálázás közös metrikák](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

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
> Javasoljuk, hogy egy ésszerű időkerete a riasztás konfigurálása függőleges skálázás, és minden kapcsolódó szolgáltatás megszakadása túl gyakran kiváltó elkerülése érdekében. Fontolja meg egy legalább 20-30 perc vagy több ablakot. Vegye figyelembe a horizontális skálázás, ha bármely megszakadásának elkerülése érdekében van szüksége.
> 
> 

További információ a riasztások létrehozásához tekintse meg a következő cikkeket:

* [A figyelő PowerShell Azure gyors üzembe helyezési-minták](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Figyelő platformfüggetlen parancssori felület Azure gyors üzembe helyezési-minták](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Összefoglalás
Ez a cikk bemutatta egyszerű függőleges méretezési példákat. Ezeket az építőelemeket - Automation-fiók, a runbookok, webhookokkal, riasztások - események gazdag számos műveletek testreszabott számú csatlakoztathatja.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png

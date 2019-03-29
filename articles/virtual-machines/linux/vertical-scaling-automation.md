---
title: Az Azure Automation szolgáltatással az Azure virtuális gépek vertikális skálázása |} A Microsoft Docs
description: Függőleges méretezése a Linux rendszerű virtuális gép a figyelési riasztásokat az Azure Automation szolgáltatással a választ
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/29/2016
ms.author: singhkay
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d58a9df519ea886372258dd0c7b012df8d8d3ae
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579393"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Vertikális skálázása az Azure-beli Linuxos virtuális gép az Azure Automationnel
Vertikális skálázás az a folyamat növelésével vagy csökkentésével az erőforrásokat a gép számítási feladatokra válaszul. Az Azure-ban a lehet elvégezni a virtuális gép méretének módosításával. Ez segít a következő esetekben

* A virtuális gép nem gyakran használja, átméretezheti, a havi költségek csökkentése érdekében kisebb méretet lefelé
* Ha a virtuális gép a csúcsterhelés között kapja, átméretezhetők egy nagyobb méretű a kapacitás növelése érdekében

A körvonal ehhez további van, mint alatt

1. A telepítő az Azure Automation eléréséhez a virtuális gépek
2. Az előfizetés az Azure Automation függőleges méretezés runbookok importálása
3. Webhook hozzáadása a forgatókönyvhöz
4. Riasztás hozzáadása a virtuális géphez

> [!NOTE]
> Az első virtuális gépen, az méretezhetők, méretek mérete miatt előfordulhat, hogy korlátozva lesz, mert a rendelkezésre állási, a további méretek a fürt jelenlegi virtuális gép üzemel. A cikk ezt használja a közzétett automation-runbookok hozunk ebben az esetben a gondoskodik, és csak méretezésre a virtuális gép mérete párok alatt. Ez azt jelenti, hogy Standard_D1v2 virtuális gép fog hirtelen nem lehet Standard_G5 skálázható vertikálisan leskálázni Basic_A0 való. Korlátozott virtuálisgép-méretek felfelé és lefelé méretezését is nem támogatott. Választhat, hogy skálázni a következő párok méretek között:
> 
> | Pár skálázás Virtuálisgép-méretek |  |
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
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard G5 |
> | Standard_GS1 |Például a Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard m8ms  |Standard m 128 MS |
> | Standard m32ls  |Standard m64ls |
> | Standard m64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>A telepítő az Azure Automation eléréséhez a virtuális gépek
Először is szüksége, hozzon létre egy Azure Automation-fiók, amely üzemelteti a runbookokat, a méretezés a Virtuálisgép-méretezési példányra használt. Az Automation szolgáltatás vezettünk be az "Futtató fiók" funkció, amely lehetővé teszi a beállítás mentése az egyszerű szolgáltatás automatikusan a runbookok futtatásáért a felhasználó nevében nagyon egyszerű. Tudjon meg többet erről az alábbi cikkben szerepel:

* [Runbookok hitelesítése Azure-beli futtató fiókkal](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Az előfizetés az Azure Automation függőleges méretezés runbookok importálása
A runbookokat, amelyek a szükséges ahhoz, hogy a virtuális gépek függőleges méretezése az Azure Automation forgatókönyv-katalógusában a már közzétett. Szüksége lesz az előfizetés importálja azokat. Runbookok importálása a következő cikk elolvasásával tudhat meg.

* [Runbook- és modulkatalógusok az Azure Automationhöz](../../automation/automation-runbook-gallery.md)

A runbookokat, amelyek importálni kell az alábbi képen látható

![Runbookok importálása](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Webhook hozzáadása a forgatókönyvhöz
Importálása után kell a runbookok hozzáadása egy webhookot a runbookhoz úgy is elindítható a virtuális gépről egy riasztást. Egy webhookot hoz létre a Runbook részletei itt olvashatók

* [Az Azure Automation-webhookok](../../automation/automation-webhooks.md)

Ellenőrizze, hogy a webhook a webhook párbeszédpanel bezárása, szüksége lesz a következő szakaszban előtt másolja.

## <a name="add-an-alert-to-your-virtual-machine"></a>Riasztás hozzáadása a virtuális géphez
1. Válassza ki a virtuális gép beállításai
2. Válassza ki a "Riasztási szabályok"
3. "Riasztás hozzáadása" kiválasztása
4. A riasztás triggerfuttatáskor aktiválandó metrika kijelölése
5. Válassza ki azt a feltételt, amikor teljesül fog riasztást üzenetszám
6. Válassza ki a feltétel egy küszöbértéket 5. lépés. teljesítendő
7. Válassza ki egy időszak, amely fölött a figyelési szolgáltatás ellenőrzi, a feltétel és a küszöbérték a lépések 5 és 6
8. Illessze be a webhookot, az előző szakaszban kimásolt.

![Riasztás hozzáadása virtuális géphez 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Riasztás hozzáadása a virtuális gép 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)


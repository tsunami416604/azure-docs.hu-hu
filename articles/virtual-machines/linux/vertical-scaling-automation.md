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
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7190f13f9d41afffcbbc1104f533b0d0586a0d6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486035"
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

## <a name="scale-limitations"></a>Skálázási korlátozások

Az első virtuális gépen, az méretezhetők, méretek mérete miatt előfordulhat, hogy korlátozva lesz, mert a rendelkezésre állási, a további méretek a fürt jelenlegi virtuális gép üzemel. A cikk ezt használja a közzétett automation-runbookok hozunk ebben az esetben a gondoskodik, és csak méretezésre a virtuális gép mérete párok alatt. Ez azt jelenti, hogy Standard_D1v2 virtuális gép fog hirtelen nem lehet Standard_G5 skálázható vertikálisan leskálázni Basic_A0 való. Korlátozott virtuálisgép-méretek felfelé és lefelé méretezését is nem támogatott. 

Választhat, hogy skálázni a következő párok méretek között:

* [Az a sorozat](#a-series)
* [B-Series](#b-series)
* [A D-sorozat](#d-series)
* [E sorozat](#e-series)
* [F sorozat](#f-series)
* [G-Series](#g-series)
* [H-Series](#h-series)
* [Az L sorozat](#l-series)
* [M-Series](#m-series)
* [Az N-sorozat](#n-series)

### <a name="a-series"></a>A sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>B sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>D sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard d32 v3 |
| Standard d32 v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>E sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>F sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>G sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Például a Standard_GS5 |

### <a name="h-series"></a>H-sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>L sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>M sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Standard m8ms | Standard_M16ms |
| Standard_M16ms | Standard m32ms |
| Standard m32ms | Standard_M64ms |
| Standard_M64ms | Standard m 128 MS |
| Standard m32ls | Standard m64ls |
| Standard m64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>N-sorozat

| Kezdeti méret | Vertikális felskálázás mérete | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |

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


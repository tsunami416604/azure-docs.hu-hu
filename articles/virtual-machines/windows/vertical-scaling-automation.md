---
title: A Windows rendszerű virtuális gépek vertikális skálázása a Azure Automation használatával | Microsoft Docs
description: A Windows rendszerű virtuális gépek vertikális skálázása a riasztások figyelése Azure Automation
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5d255662f7db12537365f57eb71355ca2e11cc51
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947252"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Windows rendszerű virtuális gépek vertikális skálázása Azure Automation

A vertikális skálázás az a folyamat, amellyel a gép erőforrásainak növelése vagy csökkentése a számítási feladatokra reagál. Az Azure-ban ez a virtuális gép méretének módosításával végezhető el. Ez a következő helyzetekben nyújt segítséget.

* Ha a virtuális gépet nem gyakran használják, a havi költségek csökkentése érdekében átméretezheti kisebb méretre.
* Ha a virtuális gép eléri a maximális terhelést, nagyobb méretre lehet méretezni, hogy növelje a kapacitását

A megvalósításához szükséges lépések vázlata az alábbi:

1. A telepítő Azure Automation a Virtual Machines eléréséhez
2. A Azure Automation vertikális skálázási runbookok importálása az előfizetésbe
3. Webhook hozzáadása a runbook
4. Riasztás hozzáadása a virtuális géphez


## <a name="scale-limitations"></a>Skálázási korlátozások

Az első virtuális gép mérete miatt a méretezhető méretre korlátozható, mert a fürt aktuális virtuális gépe más méreteinek rendelkezésre állása is a-ben települ. A cikkben használt közzétett Automation-runbookok ezt az esetet vesszük figyelembe, és csak az alábbi virtuálisgép-méret párokon belül méretezhetők. Ez azt jelenti, hogy a Standard_D1v2 virtuális gépeket nem lehet hirtelen méretezni a standard G5, vagy a Basic_A0-ra méretezni. A virtuálisgép-méretek vertikális fel-és leskálázása is korlátozott, és nem támogatott. 

A következő méretek közül választhat:

* [A sorozat](#a-series)
* [B-Series](#b-series)
* [D sorozat](#d-series)
* [E sorozat](#e-series)
* [F sorozat](#f-series)
* [G-Series](#g-series)
* [H-Series](#h-series)
* [L sorozat](#l-series)
* [M sorozat](#m-series)
* [N sorozat](#n-series)

### <a name="a-series"></a>A sorozat

| Kezdeti méret | Vertikális Felskálázási méret | 
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

| Kezdeti méret | Vertikális Felskálázási méret | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>D sorozat

| Kezdeti méret | Vertikális Felskálázási méret | 
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

| Kezdeti méret | Vertikális Felskálázási méret | 
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

| Kezdeti méret | Vertikális Felskálázási méret | 
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

| Kezdeti méret | Vertikális Felskálázási méret | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>H-sorozat

| Kezdeti méret | Vertikális Felskálázási méret | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>L sorozat

| Kezdeti méret | Vertikális Felskálázási méret | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>M sorozat

| Kezdeti méret | Vertikális Felskálázási méret | 
| --- | --- |
| Standard m8ms | Standard_M16ms |
| Standard_M16ms | Standard m32ms |
| Standard m32ms | Standard_M64ms |
| Standard_M64ms | Standard m128ms |
| Standard m32ls | Standard m64ls |
| Standard m64s | Standard m128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>N-sorozat

| Kezdeti méret | Vertikális Felskálázási méret | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard nd6 | Standard nd12 |
| Standard nd12 | Standard nd24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |
| Standard_NV12s_v3 |Standard_NV48s_v3 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>A telepítő Azure Automation a Virtual Machines eléréséhez
Először létre kell hoznia egy Azure Automation fiókot, amely a virtuális gép skálázásához használt runbookok fogja tárolni. A közelmúltban az Automation szolgáltatás bemutatta a "futtató fiók" funkciót, amely lehetővé teszi az egyszerű szolgáltatás beállítását a runbookok automatikus futtatásához a felhasználó nevében. Erről további információt az alábbi cikkben talál:

* [Runbookok hitelesítése Azure-beli futtató fiókkal](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>A Azure Automation vertikális skálázási runbookok importálása az előfizetésbe
A virtuális gép vertikális skálázásához szükséges runbookok már közzé van téve a Azure Automation Runbook-galériában. Ezeket importálnia kell az előfizetésbe. A runbookok importálásáról a következő cikkben olvashat bővebben.

* [Runbook- és modulkatalógusok az Azure Automationhöz](../../automation/automation-runbook-gallery.md)

Az importálni kívánt runbookok az alábbi képen láthatók.

![Runbookok importálása](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Webhook hozzáadása a runbook
A runbookok importálása után fel kell vennie egy webhookot a runbook, hogy egy virtuális gépről származó riasztás aktiválható legyen. A Runbook webhook létrehozásával kapcsolatos részletek itt olvashatók

* [Webhookok Azure Automation](../../automation/automation-webhooks.md)

Győződjön meg arról, hogy a webhookot a webhook párbeszédpanel bezárása előtt másolja, mivel erre a következő szakaszban lesz szükség.

## <a name="add-an-alert-to-your-virtual-machine"></a>Riasztás hozzáadása a virtuális géphez
1. Virtuális gép beállításainak kiválasztása
2. "Riasztási szabályok" kiválasztása
3. Válassza a "riasztás hozzáadása" lehetőséget.
4. Válassza ki a metrikát a riasztás bekapcsolásához
5. Válasszon ki egy olyan feltételt, amely akkor jelenik meg, ha a beteljesülés során a riasztás tüzet okoz
6. Válasszon küszöbértéket az 5. lépésben feltételhez. teljesítendő
7. Válassza ki azt az időszakot, ameddig a figyelési szolgáltatás ellenőrizni fogja a feltételt és a küszöbértéket az 5 & 6. lépésben
8. Illessze be az előző szakaszból másolt webhookot.

![Riasztás hozzáadása a virtuális géphez 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Riasztás hozzáadása a virtuális géphez 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)


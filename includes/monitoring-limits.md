---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 050d3314345e64e3d69a07367a0e9acc318fa106
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66271576"
---
| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Az automatikus méretezési beállítások |100 régiónként és előfizetésenként. | Ugyanaz, mint az alapértelmezett. |
| Metrikákhoz kapcsolódó riasztások (klasszikus) |100 aktív riasztási szabályok száma előfizetésenként. | Az ügyfélszolgálat. |
| Metrikákhoz kapcsolódó riasztások |1000 aktív riasztási szabályok az előfizetéshez (nyilvános felhőkhöz) és 100 aktív riasztási szabályok az Azure China és az Azure Government előfizetésenként. | Az ügyfélszolgálat. |
| Tevékenységnapló-riasztások | 100 aktív riasztási szabályok száma előfizetésenként. | Ugyanaz, mint az alapértelmezett. |
| Naplóriasztások | 512 | Az ügyfélszolgálat. |
| Műveletcsoportok |előfizetésenként 2000 Műveletcsoportok. | Az ügyfélszolgálat. |

**Műveleti csoportokra vonatkozó korlátozások**

| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Az Azure app-leküldés | 10 azure app-műveletek száma műveletcsoport. | Az ügyfélszolgálat. |
| Email | Műveletcsoport 1000 e-mail műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| ITSM | 10 ITSM-műveletek műveletcsoport. | Az ügyfélszolgálat. | 
| Logikai alkalmazás | 10 logic app-műveletek a műveletcsoport. | Az ügyfélszolgálat. |
| Forgatókönyv | Műveletcsoport 10 runbook műveletek. | Az ügyfélszolgálat. |
| SMS | Műveletcsoport 10 SMS műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| Hang | Műveletcsoport 10 hangalapú műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| Webhook | 10 webhook-műveletek műveletcsoport.  Webhook-hívások maximális száma érték az 1500 előfizetésenként percenként. Egyéb korlátok esetén érhető el [művelet jellemző](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Az ügyfélszolgálat. |

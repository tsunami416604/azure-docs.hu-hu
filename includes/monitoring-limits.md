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
ms.openlocfilehash: 4360196a33381d1a9c8316430339b9a80cb0dba3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58016483"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Az automatikus méretezési beállítások |100 régiónként és előfizetésenként. | Ugyanaz, mint az alapértelmezett. |
| Metrikákhoz kapcsolódó riasztások (klasszikus) |100 aktív riasztási szabályok száma előfizetésenként. | Az ügyfélszolgálat. |
| Metrikákhoz kapcsolódó riasztások |100 aktív riasztási szabályok száma előfizetésenként. | Az ügyfélszolgálat. |
| Műveletcsoportok |előfizetésenként 2000 Műveletcsoportok. | Az ügyfélszolgálat. |

**Műveleti csoportokra vonatkozó korlátozások**

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Leküldés Azure-alkalmazásból | 10 azure app-műveletek száma műveletcsoport. | Az ügyfélszolgálat. |
| E-mail | Műveletcsoport 1000 e-mail műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| ITSM | 10 ITSM-műveletek műveletcsoport. | Az ügyfélszolgálat. | 
| Logikai alkalmazás | 10 logic app-műveletek a műveletcsoport. | Az ügyfélszolgálat. |
| Forgatókönyv | Műveletcsoport 10 runbook műveletek. | Az ügyfélszolgálat. |
| SMS | Műveletcsoport 10 SMS műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| Hang | Műveletcsoport 10 hangalapú műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| Webhook | 10 webhook-műveletek műveletcsoport. Egyéb korlátok esetén érhető el [művelet jellemző](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Az ügyfélszolgálat. |

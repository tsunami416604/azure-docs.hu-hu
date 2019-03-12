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
ms.openlocfilehash: c608389136bca2c6add7edfb1ae4eaca218f2b99
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553524"
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

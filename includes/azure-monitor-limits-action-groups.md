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
ms.openlocfilehash: 6626be3627341724559182a95ab9d06694dd2c2e
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295936"
---
| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Az Azure app-leküldés | 10 azure app-műveletek száma műveletcsoport. | Az ügyfélszolgálat. |
| E-mail | Műveletcsoport 1000 e-mail műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| ITSM | 10 ITSM-műveletek műveletcsoport. | Az ügyfélszolgálat. | 
| Logikai alkalmazás | 10 logic app-műveletek a műveletcsoport. | Az ügyfélszolgálat. |
| Forgatókönyv | Műveletcsoport 10 runbook műveletek. | Az ügyfélszolgálat. |
| SMS | Műveletcsoport 10 SMS műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| Hang | Műveletcsoport 10 hangalapú műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| Webhook | 10 webhook-műveletek műveletcsoport.  Webhook-hívások maximális száma érték az 1500 előfizetésenként percenként. Egyéb korlátok esetén érhető el [művelet jellemző](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Az ügyfélszolgálat. |


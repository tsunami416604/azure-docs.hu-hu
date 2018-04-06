---
title: Azure CDN használati minták elemzése |} Microsoft Docs
description: Ez a cikk ismerteti a különböző típusú elemzési jelentések Azure CDN termék érhető el.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: rli; v-deasim
ms.openlocfilehash: 3f475c5cc9b766ea9aa5bd39d4a378e8deed5e35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Az Azure CDN használati mintáinak elemzése

Miután engedélyezte a CDN az alkalmazáshoz, CDN megfigyeléséhez, ellenőrizze, a kézbesítés állapotát, és lehetséges problémák elhárítása. Az Azure CDN további olyan funkciókat kínál az alábbi módokon: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók keresztül egyszerűsített analitika

Egyszerűsített analitika Akamai (normál) CDN-profilok és Verizon (Standard és prémium) tartozó összes CDN-végpontok érhető el. Az Azure diagnosztikai naplóihoz egyszerűsített analitika az Azure storage, az event hubs vagy Naplóelemzési exportálni. A Naplóelemzési egy grafikonon, amelyek a felhasználó által konfigurálható és testre szabható megoldást kínál. További információkért lásd: [Azure diagnosztikai naplók](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon core jelentések

Az Azure CDN-felhasználóként egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profil, Verizon core jelentések megtekintéséhez a Verizon kiegészítő portálon. Verizon core jelentések keresztül érhető el a **kezelése** lehetőséget az Azure-portálon és diagramokat és a nézetek számos. További információkért lásd: [Core jelentések verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon egyéni jelentések

Az Azure CDN-felhasználóként egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profil, Verizon egyéni jelentések megtekintéséhez a Verizon kiegészítő portálon. Verizon egyéni jelentések keresztül érhető el a **kezelése** lehetőséget az Azure portálról. A szegély Azure CDN-profilhoz tartozó CName az Verizon egyéni jelentések lapon látható minden egyes átvitt találatok vagy adatok száma. Az adatok HTTP-válasz kódja vagy gyorsítótár állapot szerint csoportosíthatók bármely meghatározott időtartamra vonatkozóan. További információkért lásd: [egyéni jelentések verizon](cdn-verizon-custom-reports.md).

## <a name="verizon-premium-reports"></a>Verizon premium jelentések

A **verizon Azure CDN Premium**, az alábbi jelentések is elérhető:
   * [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)
   * [Valós idejű statisztikák](cdn-real-time-stats.md)
   * [Határcsomópont teljesítménye](cdn-edge-performance.md)


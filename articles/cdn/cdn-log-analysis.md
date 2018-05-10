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
ms.openlocfilehash: 61fbe6e29df787048a9694138d3c9095f5cba76b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Az Azure CDN használati mintáinak elemzése

Miután engedélyezte a CDN az alkalmazáshoz, CDN megfigyeléséhez, ellenőrizze, a kézbesítés állapotát, és lehetséges problémák elhárítása. Az Azure CDN további olyan funkciókat kínál az alábbi módokon: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók keresztül egyszerűsített analitika

Egyszerűsített analitika összes tarifacsomagok CDN-végpontok érhető el. Az Azure diagnosztikai naplóihoz egyszerűsített analitika az Azure storage, az event hubs vagy Azure Naplóelemzés exportálni. Az Azure Naplóelemzés egy grafikonon, amelyek a felhasználó által konfigurálható és testre szabható megoldást kínál. Az Azure diagnosztikai naplók kapcsolatos további információkért lásd: [Azure diagnosztikai naplók](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon core jelentések

Az Azure CDN-felhasználóként egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profil, Verizon core jelentések megtekintéséhez a Verizon kiegészítő portálon. Verizon core jelentések keresztül érhető el a **kezelése** lehetőséget az Azure-portálon és diagramokat és a nézetek számos. További információkért lásd: [Core jelentések verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon egyéni jelentések

Az Azure CDN-felhasználóként egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profil, Verizon egyéni jelentések megtekintéséhez a Verizon kiegészítő portálon. Verizon egyéni jelentések keresztül érhető el a **kezelése** lehetőséget az Azure portálról. A szegély Azure CDN-profilhoz tartozó CName az Verizon egyéni jelentések lapon látható minden egyes átvitt találatok vagy adatok száma. Az adatok HTTP-válasz kódja vagy gyorsítótár állapot szerint csoportosíthatók bármely meghatározott időtartamra vonatkozóan. További információkért lásd: [egyéni jelentések verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Verizon Azure CDN Premium jelentések

A **verizon Azure CDN Premium**, az alábbi jelentések is elérhető:
   * [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)
   * [Valós idejű statisztikák](cdn-real-time-stats.md)
   * [Határcsomópont teljesítménye](cdn-edge-performance.md)


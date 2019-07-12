---
title: Az Azure CDN használati mintáinak elemzése |} A Microsoft Docs
description: Ez a cikk ismerteti a különböző típusú problémaelemző jelentések az Azure CDN termék érhető el.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 238dea3c136daf13d3db7be41bed103a0cbf7636
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593684"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Az Azure CDN használati mintáinak elemzése

Miután engedélyezte a CDN-t az alkalmazáshoz, a CDN használatának figyelése, ellenőrizze a kézbesítési, állapotát és esetleges problémáinak. Az Azure CDN olyan funkciókat kínál, a következő módon: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Egyszerűsített analitika keresztül az Azure diagnosztikai naplók

Egyszerűsített analitika CDN-végpontok minden árképzési szint esetében érhető el. Azure-beli diagnosztikai naplók engedélyezése exportálhatók az Azure storage, az event hubs, a core analytics, vagy az Azure Monitor naplózza. Az Azure Monitor naplóira a diagramok, amelyek a felhasználó által konfigurálható, és testre szabható megoldást kínál. Az Azure diagnosztikai naplók kapcsolatos további információkért lásd: [Azure diagnosztikai naplók](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon alapvető jelentések

Az Azure CDN felhasználóként egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profil, Verizon alapvető jelentések a Verizontól kiegészítő portálon is megtekintheti. Verizon alapvető jelentések keresztül érhető el a **kezelés** lehetőség az Azure Portalról, és számos olyan diagramokat és nézeteket. További információkért lásd: [alapvető jelentések a Verizontól](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Egyéni jelentések – verizon

Az Azure CDN felhasználóként egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profilt, Verizon egyéni jelentések a Verizontól kiegészítő portálon is megtekintheti. Egyéni jelentések verizon keresztül érhető el a **kezelés** lehetőség az Azure Portalról. A Verizon egyéni jelentések lap megjeleníti átvitt találatok vagy adatok száma az egyes Azure CDN-profil tartozó CName él. Az adatok HTTP-válasz kódja vagy gyorsítótár állapot szerint csoportosíthatók bármely időszakon belül. További információkért lásd: [egyéni jelentések a Verizontól](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Jelentések az Azure CDN Premiumhoz a Verizontól

A **verizon Azure CDN Premium**, az alábbi jelentések is elérhető:
   * [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)
   * [Valós idejű statisztikák](cdn-real-time-stats.md)
   * [Határcsomópont teljesítménye](cdn-edge-performance.md)


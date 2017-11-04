---
title: "Azure CDN használati minták elemzése |} Microsoft Docs"
description: "Ügyfél engedélyezheti a webhelynapló elemzése Azure CDN szolgáltatás használata."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: 04e5499011e72dfcc20dff370d5d837227ed29b6
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Azure CDN használati minták elemzése

Miután engedélyezte a CDN az alkalmazáshoz, CDN megfigyeléséhez, ellenőrizze, a kézbesítés állapotát, és lehetséges problémák elhárítása. Az Azure CDN két lehetőséget biztosít ezen képességek a következő: 

## <a name="verizon-core-reports"></a>Verizon Core jelentések

– Egy Verizon standard vagy Verizon premium profil Azure CDN felhasználóként Verizon Core-jelentések megtekintéséhez a Verizon kiegészítő portálon. Verizon Core jelentések keresztül érhető el a **kezelése** lehetőséget az Azure-portálon és diagramokat és a nézetek számos. További információkért lásd: [Core jelentések verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon egyéni jelentések

– Egy Verizon standard vagy Verizon premium profil Azure CDN felhasználóként Verizon egyéni jelentések megtekintéséhez a Verizon kiegészítő portálon. Verizon egyéni jelentések keresztül érhető el a **kezelése** lehetőséget az Azure portálról. A szegély Azure CDN-profilhoz tartozó CName az Verizon egyéni jelentések lapon látható minden egyes átvitt találatok vagy adatok száma. Az adatok HTTP-válasz kódja vagy gyorsítótár állapot szerint csoportosíthatók bármely meghatározott időtartamra vonatkozóan. További információkért lásd: [egyéni jelentések verizon](cdn-verizon-custom-reports.md).

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók keresztül egyszerűsített analitika

Egyszerűsített analitika Akamai (normál) CDN-profilok és Verizon (Standard és prémium) tartozó összes CDN-végpontok érhető el. Az Azure diagnosztikai naplóihoz egyszerűsített analitika exportálhatók a az Azure storage, az event hubs vagy az Operations Management Suite (OMS) szolgáltatáshoz. OMS Naplóelemzési egy grafikonon, amelyek a felhasználó által konfigurálható és testre szabható megoldást kínál. További információkért lásd: [Azure diagnosztikai naplók](cdn-azure-diagnostic-logs.md).


---
title: Lekérdezési követelmények
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 518865f78c170f1fbe4e65b96dc149c1b449a88b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631366"
---
A lekérdezésben használja a `@StartTime` paramétert a metrikai adatok egyetlen időbélyeghez való lekéréséhez. A metrikai tanácsadó a paramétert a `yyyy-MM-ddTHH:mm:ss` lekérdezés futtatásakor formázó karakterlánccal helyettesíti.

> [!IMPORTANT]
> A lekérdezésnek az egyes dimenzió-kombinációk esetében legfeljebb egy rekordot kell visszaadnia minden időbélyegnél. A lekérdezés által visszaadott összes rekordnak azonos időbélyegzővel kell rendelkeznie. A metrikai tanácsadó minden időbélyeg esetében futtatja ezt a lekérdezést az adatok betöltéséhez. További információért és példákért tekintse [meg a gyakori kérdések szakaszt](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 
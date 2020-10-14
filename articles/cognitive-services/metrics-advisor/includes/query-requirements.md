---
title: Lekérdezési követelmények
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043201"
---
A lekérdezésben használja a `@StartTime` paramétert a metrikai adatok egyetlen időbélyeghez való lekéréséhez. A metrikai tanácsadó a paramétert a `yyyy-MM-ddTHH:mm:ss` lekérdezés futtatásakor formázó karakterlánccal helyettesíti.

> [!IMPORTANT]
> A lekérdezésnek az egyes dimenzió-kombinációk esetében legfeljebb egy rekordot kell visszaadnia minden időbélyegnél. A lekérdezés által visszaadott összes rekordnak azonos időbélyegzővel kell rendelkeznie. A metrikai tanácsadó minden időbélyeg esetében futtatja ezt a lekérdezést az adatok betöltéséhez. További információért és példákért tekintse [meg a gyakori kérdések szakaszt](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 
---
title: API HTTP-válaszkódot – QnA Maker
titleSuffix: Azure Cognitive Services
description: Ismerje meg, milyen HTTP-válaszkódot ad vissza a QnA Maker API-kat. Ez segít a hibák megoldásához.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: cdfa5212f321cc6ec976ea9df301243acc54a23f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794865"
---
# <a name="qna-maker-api-http-response-codes"></a>A QnA Maker API HTTP-válaszkódot
A [felügyeleti](https://go.microsoft.com/fwlink/?linkid=2092179) és az előrejelzési API-k HTTP-válaszkódot adja vissza. Parancsválasz-üzeneteket egy kérelem-re vonatkozó információk közé tartozik, míg a HTTP-válaszként kapott állapotkód általános. 

### <a name="management"></a>Kezelés

|Kód|Magyarázat|
|:--|--|
|2xx|Sikeres|
|400|kérés paraméterei helytelen, ami azt jelenti, a szükséges paraméterek: hiányzó, hibás vagy túl nagy|
|400|kérelem törzse nem megfelelő, ami azt jelenti, a JSON-ja hiányzik, hibás vagy túl nagy|
|401|Érvénytelen kulcs|
|403|-Tiltott nem rendelkezik megfelelő engedélyekkel|
|404|KB nem létezik.|
|410|Ez az API elavult, és már nem érhető el|

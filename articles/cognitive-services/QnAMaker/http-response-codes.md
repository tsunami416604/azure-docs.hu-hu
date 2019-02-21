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
ms.openlocfilehash: 319623487f37308b5b8fe3fd107b01733825184d
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454718"
---
# <a name="qna-maker-api-http-response-codes"></a>A QnA Maker API HTTP-válaszkódot
A [felügyeleti](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) és az előrejelzési API-k HTTP-válaszkódot adja vissza. Parancsválasz-üzeneteket egy kérelem-re vonatkozó információk közé tartozik, míg a HTTP-válaszként kapott állapotkód általános. 

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

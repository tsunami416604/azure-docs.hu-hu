---
title: API HTTP-válaszkódot – QnA Maker
titleSuffix: Azure Cognitive Services
description: Ismerje meg, milyen HTTP-válaszkódot ad vissza a QnA Maker API-kat. Ez segít a hibák megoldásához.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8a39e8ee783d999bf3851ef1ba564e8f24de7910
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726952"
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
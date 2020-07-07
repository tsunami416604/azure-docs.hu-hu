---
title: fájlbefoglalás
description: fájlbefoglalás
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: a6e6c89c34723fb9e11b0c7e4ab8c9bedb8aa9ca
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959029"
---
A **kezelés** szakaszban (a jobb felső menüben) az **Azure-erőforrások** lapon (bal oldali menü) másolja a **példaként szolgáló lekérdezési** URL-címet, majd illessze be egy új böngésző lapra.

A végpont URL-címének formátuma a következő: a saját egyéni altartománya, az alkalmazás azonosítója és a végpont kulcsa az APP-ID és a KEY-ID helyett:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```
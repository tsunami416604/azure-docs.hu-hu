---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375148"
---
A **kezelés** szakaszban (a jobb felső menüben) az **Azure-erőforrások** lapon (bal oldali menü) másolja a **példaként szolgáló lekérdezési** URL-címet, majd illessze be egy új böngésző lapra.

A végpont URL-címének formátuma a következő: a saját egyéni altartománya, az alkalmazás azonosítója és a végpont kulcsa az APP-ID és a KEY-ID helyett:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```
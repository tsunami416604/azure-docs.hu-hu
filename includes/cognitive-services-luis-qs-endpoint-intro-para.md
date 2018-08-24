---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 07336184179aed1d32ee217bdbc9378b196e68f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42820255"
---
Ez a rövid útmutatóban elérhető nyilvános LUIS alkalmazás használatával határozza meg a felhasználó szándékának természetes nyelvi szövegből. A felhasználó szándékának szövegként küldeni a nyilvános app előrejelzési végpontjához. A végpont a LUIS a természetes nyelvű szöveget, ami azt jelenti, meghatározásakor általános szándékot, és az alkalmazás tulajdonosának tartományhoz kapcsolódó adatok kinyeréséhez elemzése a nyilvános app modell vonatkozik. 

Ez a rövid útmutató a végpont REST API-t használja. További információkért lásd: a [végpont API-dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

Ez a cikk szüksége lesz egy ingyenes [LUIS](http://www.luis.ai) fiókot. 
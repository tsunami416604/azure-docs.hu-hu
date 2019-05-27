---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 00ffb8f4777fd458c92a9e996c18cd6bfadbc45e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124032"
---
Ebben a rövid útmutatóban elérhető nyilvános LUIS-alkalmazással határozza meg egy felhasználó szándékát egy beszélgetés szövegéből. Küldje el szövegként a felhasználó szándékát a nyilvános alkalmazás HTTP-előrejelzési végpontjára. A LUIS a végpontnál a nyilvános alkalmazás modelljét alkalmazza a természetes nyelvű szövegen a jelentés elemzése érdekében, amellyel meghatározza az általános szándékot, valamint kinyeri az alkalmazás témájában releváns adatokat. 

Ez a rövid útmutató a végponti REST API-t használja. További információkért tekintse meg a [végponti API-dokumentációját](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

Ehhez a cikkhez egy ingyenes [LUIS](https://www.luis.ai)-fiókra van szüksége. 

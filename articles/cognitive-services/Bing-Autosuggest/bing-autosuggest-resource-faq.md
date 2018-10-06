---
title: Gyakori kérdések (GYIK) – a Bing Autosuggest API
titlesuffix: Azure Cognitive Services
description: A Bing Autosuggest API kapcsolatos gyakori kérdésekre adott válaszok.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 84f1b0555922119e9de4addc3d51ac233e7bae65
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831364"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>Gyakori kérdések (GYIK) tudnivalók a Bing Autosuggest API
 
 Kapcsolatos fogalmakat, a kód és a forgatókönyvek az Azure Cognitive Services az automatikus kiegészítési API-val kapcsolatos gyakori kérdésekre adott válaszok.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Hogyan kaphatok a választható ügyféltanúsítvány-fejléceket, a Bing Autosuggest API hívása JavaScript-alapú során?

A következő fejlécek nem kötelező, de javasoljuk, hogy szükség szerint kezelje. Ezek a fejlécek segítenek a Bing Autosuggest API pontosabb eredményeket adja vissza.

- A hely keresése X
- X-MSEdge-ClientID
- X-MSEdge-Ügyfélip

Azonban amikor a Bing Autosuggest API hívása JavaScript, a böngésző beépített biztonsági funkciókat előfordulhat, hogy megakadályozzák a fér hozzá a következő fejlécek értékeit.

A probléma megoldásához, a Bing Autosuggest API-kérelem CORS proxyn keresztül teheti meg. Ilyen proxyn válasza rendelkezik egy `Access-Control-Expose-Headers` fejléc adott listáinak válaszfejlécek, és elérhetővé teszi azokat a JavaScript.

Egyszerű, hogy a CORS-proxy telepítéséhez a [oktatóanyag alkalmazása](tutorials/autosuggest.md) eléréséhez a választható ügyféltanúsítvány-fejléceket. Ha még nem rendelkezik, először [a Node.js telepítéséhez](https://nodejs.org/en/download/). Majd adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Autosuggest API-végpont lévő a HTML-fájl:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Végezetül indítsa el a CORS-proxy a következő parancsot:

    cors-proxy-server

Hagyja meg a parancssori ablakban nyissa meg az oktatóanyag alkalmazása; használata közben az ablak bezárása leállítja a proxy. A bővíthető HTTP-fejlécek szakaszban alább a keresési eredmények között, most már megtekintheti a `X-MSEdge-ClientID` fejléc (többek között), és ellenőrizze, hogy minden egyes kérés esetében azonos.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatás vagy funkció a kérdése van? Kérő vagy szavaz, fontolja meg a [felhasználói visszajelzési webhelyen](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Lásd még

- [A stack Overflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)

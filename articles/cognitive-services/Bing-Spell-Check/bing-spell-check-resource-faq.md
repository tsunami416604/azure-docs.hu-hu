---
title: Gyakori kérdések a Bing Spell Check API-RÓL
titlesuffix: Azure Cognitive Services
description: A Bing Spell Check API kapcsolatos gyakori kérdésekre választ kaphat az Azure-ban.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: e6662ffcbab9ea274a67bc4437ca1600f1625ff1
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801505"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Gyakori kérdések a Bing Spell Check API-RÓL

 Kapcsolatos fogalmakat, a kód és a forgatókönyvek az Azure-ban a Microsoft Cognitive Services a Bing Spell Check API kapcsolatos gyakori kérdésekre adott válaszok.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Hogyan kaphatok a választható ügyféltanúsítvány-fejléceket, a Bing Spell Check API hívása JavaScript-alapú során?

A következő fejlécek nem kötelező, de javasoljuk, hogy szükség szerint kezelje. Ezek a fejlécek a Bing Spell Check API visszatérési pontosabb eredmények érdekében.

- A hely keresése X
- X-MSEdge-ClientID
- X-MSEdge-Ügyfélip

Azonban amikor a Bing Spell Check API hívása JavaScript, a böngésző beépített biztonsági funkciókat előfordulhat, hogy megakadályozzák a fér hozzá a következő fejlécek értékeit.

A probléma megoldásához, győződjön meg, a Bing Spell Check API-kérelem CORS proxyn keresztül. Ilyen proxyn válasza rendelkezik egy `Access-Control-Expose-Headers` fejléc adott listáinak válaszfejlécek, és elérhetővé teszi azokat a JavaScript.

Egyszerű, hogy a CORS-proxy telepítéséhez a [oktatóanyag alkalmazása](tutorials/spellcheck.md) eléréséhez a választható ügyféltanúsítvány-fejléceket. Ha még nem rendelkezik, először [a Node.js telepítéséhez](https://nodejs.org/en/download/). Majd adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Spell Check API-végpont lévő a HTML-fájl:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Végezetül indítsa el a CORS-proxy a következő parancsot:

    cors-proxy-server

Hagyja meg a parancssori ablakban nyissa meg az oktatóanyag alkalmazása; használata közben az ablak bezárása leállítja a proxy. A bővíthető HTTP-fejlécek szakaszban alább a keresési eredmények között, most már megtekintheti a `X-MSEdge-ClientID` fejléc (többek között), és ellenőrizze, hogy minden egyes kérés esetében azonos.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatás vagy funkció a kérdése van? Kérő vagy szavaz, fontolja meg a [UserVoice webhelyen](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Lásd még

 [StackOverflow: A Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)

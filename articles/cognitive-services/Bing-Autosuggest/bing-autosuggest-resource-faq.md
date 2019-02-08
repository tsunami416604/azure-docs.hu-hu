---
title: Gyakori kérdések (GYIK) – a Bing Autosuggest API
titlesuffix: Azure Cognitive Services
description: A Bing Autosuggest API kapcsolatos gyakori kérdésekre adott válaszok.
services: cognitive-services
author: HeidiSteen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: cc14ceafaf0d9dd913cdef3864b0391f05d721e4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882250"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>Gyakori kérdések (GYIK) tudnivalók a Bing Autosuggest API
 
 Kapcsolatos fogalmakat, a kód és a forgatókönyvek az Azure Cognitive Services az automatikus kiegészítési API-val kapcsolatos gyakori kérdésekre adott válaszok.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Hogyan kaphatok a választható ügyféltanúsítvány-fejléceket, a Bing Autosuggest API hívása JavaScript-alapú során?

A következő fejlécek nem kötelező, de javasoljuk, hogy szükség szerint kezelje. Ezek a fejlécek segítenek a Bing Autosuggest API pontosabb eredményeket adja vissza.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Azonban amikor a Bing Autosuggest API hívása JavaScript, a böngésző beépített biztonsági funkciókat előfordulhat, hogy megakadályozzák a fér hozzá a következő fejlécek értékeit.

A probléma megoldásához, a Bing Autosuggest API-kérelem CORS proxyn keresztül teheti meg. Ilyen proxyn válasza rendelkezik egy `Access-Control-Expose-Headers` fejléc adott listáinak válaszfejlécek, és elérhetővé teszi azokat a JavaScript.

Egyszerű, hogy a CORS-proxy telepítéséhez a [oktatóanyag alkalmazása](tutorials/autosuggest.md) eléréséhez a választható ügyféltanúsítvány-fejléceket. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Majd adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Autosuggest API-végpont lévő a HTML-fájl:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatás vagy funkció a kérdése van? Kérő vagy szavaz, fontolja meg a [felhasználói visszajelzési webhelyen](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Lásd még

- [A stack Overflow: A cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)

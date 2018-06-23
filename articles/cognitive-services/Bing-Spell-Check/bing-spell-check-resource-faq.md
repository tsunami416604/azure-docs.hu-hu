---
title: Gyakran feltett kérdésekre a Bing helyesírás-ellenőrzés API - Azure kognitív szolgáltatások |} Microsoft Docs
description: A Bing helyesírás ellenőrizze API-val kapcsolatos gyakori kérdéseket válaszok az Azure-on.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 87b1f3ed3e0aaa9f3c3c804dc9eac3ee60b4a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349250"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Gyakran feltett kérdésekre a Bing helyesírás ellenőrizze API

 Válaszok a fogalmakat, kód és a Bing helyesírás-ellenőrzése API kapcsolódik a Microsoft Azure kognitív Services forgatókönyvek gyakran feltett kérdésekre.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Hogyan szerezhetek a választható ügyféltanúsítvány fejlécek, a Bing helyesírás-ellenőrzése API a JavaScript meghívásakor?

A következő fejlécek nem kötelező, de javasoljuk, hogy szükség szerint kezelje. Ezek a fejlécek segítenek a Bing helyesírás ellenőrizze API pontosabb eredménye.

- X keresése
- X-MSEdge-ClientID
- X-MSEdge-Ügyfélip

Azonban a Bing helyesírás-ellenőrzése API a JavaScript hívja, a böngésző beépített biztonsági szolgáltatások megakadályozhatja ezek a fejlécek értékének elérésekor.

A probléma megoldásához, hogy a Bing helyesírás ellenőrizze API-kérelem a CORS-proxyn keresztül történő. Ilyen proxy válaszát rendelkezik egy `Access-Control-Expose-Headers` fejléc adott whitelists válaszfejlécek és elérhetővé válnak a JavaScript nyelvvel.

Engedélyezi a CORS-proxy telepítéséhez egyszerűen a [oktatóanyag app](tutorials/spellcheck.md) a választható ügyféltanúsítvány fejlécek eléréséhez. Ha még nem rendelkezik, először [telepítse a Node.js-](https://nodejs.org/en/download/). Adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing helyesírás ellenőrizze API-végpont a HTML-fájlban:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Végezetül indítsa el a CORS-proxy a következő paranccsal:

    cors-proxy-server

A parancssori ablakban nyitva hagyja az oktatóanyag alkalmazás; használatakor az ablak bezárása leállítja a proxy. A bővíthető HTTP-fejlécek című szakaszt a keresési eredmények között, a most már megtekintheti a `X-MSEdge-ClientID` fejléc (többek között), és győződjön meg arról, hogy minden kérelem esetén.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatást és funkciót a kérdése van? A kért, vagy szavazott a [UserVoice webhelyén](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Lásd még

 [StackOverflow: Kognitív szolgáltatások](http://stackoverflow.com/questions/tagged/microsoft-cognitive)

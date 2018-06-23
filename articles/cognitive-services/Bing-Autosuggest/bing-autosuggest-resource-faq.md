---
title: Gyakran ismételt kérdések (GYIK) kapcsolatos Azure automatikus kiegészítési API |} Microsoft Docs
description: Válaszok Azure kognitív szolgáltatások automatikus kiegészítési API-val kapcsolatos gyakori kérdéseket az Azure-on.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 00b91728bcfec52ff30697f080d5c2619bab79a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348934"
---
# <a name="frequently-asked-questions-faq-about-autosuggest-api-cognitive-services"></a>Gyakran ismételt kérdések (GYIK) kapcsolatos automatikus kiegészítési API (kognitív szolgáltatások)
 
 Válaszok a fogalmakat, kódot és az automatikus kiegészítési API kognitív Azure-szolgáltatásokhoz kapcsolódó forgatókönyvek gyakran feltett kérdésekre.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Hogyan szerezhetek a választható ügyféltanúsítvány fejlécek, a Bing automatikus kiegészítési API a JavaScript meghívásakor?

A következő fejlécek nem kötelező, de javasoljuk, hogy szükség szerint kezelje. Ezek a fejlécek segítenek a Bing automatikus kiegészítési API pontosabb eredményeket adjon.

- X keresése
- X-MSEdge-ClientID
- X-MSEdge-Ügyfélip

Azonban a Bing automatikus kiegészítési API a JavaScript hívja, a böngésző beépített biztonsági szolgáltatások megakadályozhatja ezek a fejlécek értékének elérésekor.

A probléma megoldásához, hogy a Bing automatikus kiegészítési API-kérelem a CORS-proxyn keresztül történő. Ilyen proxy válaszát rendelkezik egy `Access-Control-Expose-Headers` fejléc adott whitelists válaszfejlécek és elérhetővé válnak a JavaScript nyelvvel.

Engedélyezi a CORS-proxy telepítéséhez egyszerűen a [oktatóanyag app](tutorials/autosuggest.md) a választható ügyféltanúsítvány fejlécek eléréséhez. Ha még nem rendelkezik, először [telepítse a Node.js-](https://nodejs.org/en/download/). Adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing automatikus kiegészítési API-végpont a HTML-fájlban:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Végezetül indítsa el a CORS-proxy a következő paranccsal:

    cors-proxy-server

A parancssori ablakban nyitva hagyja az oktatóanyag alkalmazás; használatakor az ablak bezárása leállítja a proxy. A bővíthető HTTP-fejlécek című szakaszt a keresési eredmények között, a most már megtekintheti a `X-MSEdge-ClientID` fejléc (többek között), és győződjön meg arról, hogy minden kérelem esetén.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatást és funkciót a kérdése van? A kért, vagy szavazott a [User Voice webhely](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Lásd még

- [Stack overflow webhelyre: Kognitív szolgáltatások](http://stackoverflow.com/questions/tagged/microsoft-cognitive)

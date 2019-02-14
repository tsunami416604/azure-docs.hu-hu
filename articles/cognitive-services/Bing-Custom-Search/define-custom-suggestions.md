---
title: Adja meg az egyéni automatikus kiegészítés javaslatok – Bing egyéni keresés
titlesuffix: Azure Cognitive Services
description: Ismerteti, hogyan lehet egyéni automatikus kiegészítés vonatkozó egyéni javaslatok konfigurálása
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: bbad72b41a177bdbafd6cf98bfd2025190d98b16
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237637"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Az egyéni automatikus kiegészítés felhasználói beállítása

Egyéni automatikus kiegészítés, amelyek megfelelnek a keresési funkciót a javasolt lekérdezési karakterláncok listáját adja vissza. A javasolt lekérdezési karakterláncok egy részleges lekérdezési karakterláncot, a felhasználó által a keresőmezőbe alapulnak. A lista egy legfeljebb 10 javaslatokat tartalmazza. 

Megadhatja, hogy csak egyéni javaslatokat ad vissza, vagy a Bing javaslatokat is tartalmazza. Ha adja meg a Bing-javaslatokkal, mielőtt a Bing-javaslatokkal vonatkozó egyéni javaslatok jelennek meg. Ha elegendő vonatkozó javaslatokat, lehetséges, hogy a visszaadott lista elemeit nem tartalmazza a Bing javaslatokat. A Bing-javaslatokkal mindig az egyéni keresési példány környezetében találhatók. 

A példány keresése lekérdezési javaslatok engedélyezéséhez kattintson a **automatikus kiegészítési** fülre.  

> [!NOTE]
> Ez a funkció használatához elő kell fizetnie a megfelelő szintű Custom Search (lásd: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

A javaslatok megjelennek a kiszolgáló-végpont (API-t vagy üzemeltetett felhasználói felület) akár 24 órát is igénybe vehet.

## <a name="enable-bing-suggestions"></a>Bing – javaslatok engedélyezéséhez

Ahhoz, hogy a Bing-javaslatokkal, váltsa át a **automatikus Bing-javaslatokkal** csúszkát a helyére. A csúszka kék válik.

## <a name="add-your-own-suggestions"></a>Adja hozzá a saját javaslatok

Adja hozzá a saját lekérdezési karakterlánc javaslatok, vegye fel őket a listához területen **felhasználói javaslatokat**. A felvett egy javaslatot a listában, nyomja le az enter billentyűt, vagy kattintson a **+** ikonra. Megadhatja a javaslat, bármilyen nyelven. Legfeljebb 5000 lekérdezési karakterlánc javaslatok is hozzáadhat.

## <a name="upload-suggestions"></a>Töltse fel a javaslatok

Szükség esetén is feltölthet egy fájlt a javaslatok listáját. A fájl minden sorában egy keresése lekérdezési karakterlánc kell tartalmaznia. A fájl feltöltése a Feltöltés ikonra, és válassza ki a feltölteni kívánt fájlt. A szolgáltatás kibontja a javaslatok a fájlt, és hozzáadja őket a listához.

## <a name="remove-suggestions"></a>Távolítsa el a javaslatok

Egy lekérdezési karakterlánc javaslatot eltávolításához kattintson a el kívánja távolítani a javaslat melletti remove ikonra.

## <a name="block-suggestions"></a>Javaslatok letiltása

Bing javaslatokat is, ha nem szeretné, hogy vissza a Bing search lekérdezési karakterláncok listáját is hozzáadhat. Letiltott lekérdezési karakterláncok hozzáadásához kattintson **megjelenítése letiltva javaslatok**. A lekérdezési karakterlánc hozzáadását a listához, és nyomja le az enter billentyűt, vagy kattintson a **+** ikonra. Legfeljebb 50 letiltott lekérdezési karakterláncokat is hozzáadhat.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Egyéni automatikus kiegészítés konfigurációs módosítások érvénybe léptetéséhez akár 24 óráig is eltarthat.


## <a name="enabling-autosuggest-in-hosted-ui"></a>A központi felhasználói felületen Autosuggest engedélyezése

A központi felhasználói felülethez a lekérdezési karakterlánc javaslatok engedélyezéséhez kattintson **üzemeltetett felhasználói felület**. Görgessen le a **további konfigurációs** szakaszban. A **webes keresés**válassza **a** a **engedélyezése automatikus kiegészítés**. Ahhoz, hogy az automatikus kiegészítés, jelöljön ki egy egy keresőmezőt tartalmazó elrendezésre.


## <a name="calling-the-autosuggest-api"></a>Hívása az automatikus kiegészítés API

Első javasolt lekérdezési karakterláncok használatával a Bing Custom Search API, küldjön egy `GET` kérelem a következő végpont.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

A válasz a listáját tartalmazza `SearchAction` a javasolt lekérdezési karakterláncokat tartalmazó objektumok.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Minden egyes javaslatot tartalmaz egy `displayText` és `query` mező. A `displayText` mező tartalmazza a javasolt lekérdezési karakterláncot, amely tölti ki a legördülő listából a keresőmezőbe.

Ha a felhasználó javasolt a lekérdezési karakterlánc választja a legördülő listából, a lekérdezési karakterláncot használhat a `query` hívásakor mezőt a [Bing Custom Search API](overview.md).


## <a name="next-steps"></a>További lépések

- [Egyéni javaslatok kérése](./get-custom-suggestions.md)
- [Az egyéni példánya keresése](./search-your-custom-view.md)
- [Konfigurálja, és egyéni üzemeltetett felhasználói felület használata](./hosted-ui.md)

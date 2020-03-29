---
title: Egyéni automatikus javaslatjavaslatok megadása – Bing egyéni keresés
titleSuffix: Azure Cognitive Services
description: Az egyéni automatikus javaslat a keresési élmény szempontjából releváns javasolt keresési lekérdezési karakterláncok listáját adja vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 5b2b8871d868e827532f23c7ef4f14fb00afb5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072808"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Az egyéni automatikus javaslatélmény konfigurálása

Az egyéni automatikus javaslat a keresési élmény szempontjából releváns javasolt keresési lekérdezési karakterláncok listáját adja vissza. A javasolt lekérdezési karakterláncok a felhasználó által a keresőmezőben megadott részleges lekérdezési karakterláncon alapulnak. A lista legfeljebb 10 javaslatot tartalmaz. 

Megadhatja, hogy csak egyéni javaslatokat adjon vissza, vagy bingjavaslatokat is tartalmazzon. Ha bingjavaslatokat is tartalmaz, az egyéni javaslatok a Bing-javaslatok előtt jelennek meg. Ha elegendő releváns javaslatot ad meg, lehetséges, hogy a visszaadott javaslatok listája nem tartalmaz Bing-javaslatokat. A Bing-javaslatok mindig az egyéni keresési példány környezetében találhatók. 

A keresési lekérdezési javaslatok konfigurálásához kattintson az **Automatikus javaslat** fülre.  

> [!NOTE]
> A funkció használatához a megfelelő szinten kell előfizetnie az Egyéni keresés szolgáltatásra (lásd: [árképzés).](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)

Akár 24 órát is igénybe vehet, amíg a javaslatok megjelennek a kiszolgáló végpontban (API vagy üzemeltetett felhasználói felület).

## <a name="enable-bing-suggestions"></a>Bing-javaslatok engedélyezése

A Bing-javaslatok engedélyezéséhez kapcsolja be az **Automatikus Bing-javaslatok csúszkát** a bekapcsolási pozícióra. A csúszka kékre vált.

## <a name="add-your-own-suggestions"></a>Saját javaslatok hozzáadása

Saját lekérdezési karakterlánc-javaslatok hozzáadásához vegye fel őket a **Felhasználó által definiált javaslatok**listájába. Miután felkerült egy javaslat a listába, **+** nyomja le az Enter billentyűt, vagy kattintson az ikonra. A javaslatot bármely nyelven megadhatja. Legfeljebb 5000 lekérdezési karakterlánc-javaslatot adhat hozzá.

## <a name="upload-suggestions"></a>Javaslatok feltöltése

Lehetőségként feltöltheti a javaslatok listáját egy fájlból. A fájlnak soronként egy keresési lekérdezési karakterláncot kell tartalmaznia. A fájl feltöltéséhez kattintson a feltöltésikonra, és jelölje ki a feltöltendő fájlt. A szolgáltatás kinyeri a javaslatokat a fájlból, és hozzáadja őket a listához.

## <a name="remove-suggestions"></a>Javaslatok eltávolítása

Lekérdezési karakterlánc-javaslat eltávolításához kattintson az eltávolítani kívánt javaslat melletti eltávolítási ikonra.

## <a name="block-suggestions"></a>Javaslatok blokkolása

Ha bingjavaslatokat is tartalmaz, hozzáadhatja azoknak a keresési lekérdezési karakterláncoknak a listáját, amelyeket nem szeretne, hogy a Bing visszaadjon. Blokkolt lekérdezési karakterláncok hozzáadásához kattintson **a Letiltott javaslatok megjelenítése gombra.** Adja hozzá a lekérdezési karakterláncot a listához, és nyomja le az Enter billentyűt, vagy kattintson az **+** ikonra. Legfeljebb 50 letiltott lekérdezési karakterláncot adhat hozzá.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Az egyéni automatikus javaslat konfigurációs módosításai érvénybe lépéséhez akár 24 óra is igénybe vehet.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Automatikus javaslat engedélyezése a üzemeltetett felhasználói felületen

Ha engedélyezni szeretné a lekérdezési karakterlánc-javaslatokat a központi felhasználói felülethez, kattintson a **Hosztatolt felhasználói felületre.** Görgessen le a **További konfiguráció** szakaszhoz. A **Webes keresés**csoportban válassza a **Be** lehetőséget az Automatikus javaslat **engedélyezése mezőben.** Az Automatikus javaslat engedélyezéséhez ki kell választania egy keresőmezőt tartalmazó elrendezést.


## <a name="calling-the-autosuggest-api"></a>Az automatikus javaslat API hívása

Ha a Bing egyéni keresési API-val szeretne `GET` lekérni a javasolt lekérdezési karakterláncokat, küldjön egy kérést a következő végpontra.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

A válasz a `SearchAction` javasolt lekérdezési karakterláncokat tartalmazó objektumok listáját tartalmazza.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Minden javaslat `displayText` tartalmaz `query` egy és mező. A `displayText` mező tartalmazza a keresőmező legördülő listájának feltöltésére használt javasolt lekérdezési karakterláncot.

Ha a felhasználó egy javasolt lekérdezési karakterláncot választ a legördülő listából, használja a `query` mezőben lévő lekérdezési karakterláncot a Bing Egyéni keresési [API](overview.md)hívásakor.


## <a name="next-steps"></a>További lépések

- [Egyéni javaslatok lekérése](./get-custom-suggestions.md)
- [Keresés az egyéni példányban](./search-your-custom-view.md)
- [Egyéni tárolt felhasználói felület konfigurálása és felhasználása](./hosted-ui.md)

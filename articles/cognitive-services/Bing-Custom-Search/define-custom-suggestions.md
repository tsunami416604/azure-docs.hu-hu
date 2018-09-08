---
title: 'Bing Custom Search: Adja meg az egyéni automatikus kiegészítés javaslatok |} A Microsoft Docs'
description: Ismerteti, hogyan lehet egyéni automatikus kiegészítés vonatkozó egyéni javaslatok konfigurálása
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: e7a62a79bdc2e486fb6bfca34eb4addeba2bde0e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158313"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Az egyéni automatikus kiegészítés felhasználói beállítása
Ha egyéni keresés megfelelő szinten előfizetett (lásd a [díjszabási lapjait](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), testre szabhatja a keresési javaslatok végzett az egyéni keresési funkciót. Egyéni automatikus kiegészítés alapján egy részleges lekérdezési karakterláncot, a felhasználó által javasolt lekérdezések listáját adja vissza. Az egyéni automatikus kiegészítés adja meg a keresési élmény vonatkozó egyéni keresési javaslatok. Megadhatja, hogy csak egyéni javaslatokat ad vissza, vagy a Bing javaslatokat is tartalmazza. Ha adja meg a Bing-javaslatokkal, mielőtt a Bing-javaslatokkal vonatkozó egyéni javaslatok jelennek meg. Az egyéni keresőpéldányok kontextusában a Bing-javaslatokkal korlátozódnak.

## <a name="configure-custom-autosuggest"></a>Konfigurálja az egyéni automatikus kiegészítés
Az alábbi utasítások segítségével konfigurálhatja az egyéni automatikus kiegészítés a egyéni keresési példány.

1.  Jelentkezzen be a [egyéni keresési](https://customsearch.ai).
2.  Kattintson egy egyéni keresési példány. Hozzon létre egy példányt, lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).
3.  Kattintson a **automatikus kiegészítési** fülre.

## <a name="enable-bing-suggestions"></a>Bing – javaslatok engedélyezéséhez
Ahhoz, hogy a Bing-javaslatokkal, váltsa át a **automatikus Bing-javaslatokkal** csúszkát a helyére. A csúszka kék válik.

## <a name="add-suggestions"></a>Adja hozzá a javaslatok
Javaslat hozzáadásához adja meg azt a szövegmezőben. Nyomja le az enter billentyűt, vagy kattintson a **+** ikonra. Vonatkozó egyéni javaslatok bármilyen nyelven lehet, és a Bing-javaslatokkal előtt fog megjelenni.

## <a name="upload-suggestions"></a>Töltse fel a javaslatok
Feltölthet egy fájlt a javaslatok listáját. Minden javaslat helyezze külön sorba. A Feltöltés ikonra, és válassza ki a fájlt.

## <a name="remove-suggestions"></a>Távolítsa el a javaslatok
Javaslat eltávolításához kattintson a el kívánja távolítani a javaslat melletti remove ikonra.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Egyéni automatikus kiegészítés konfigurációs módosítások érvénybe léptetéséhez akár 24 óráig is eltarthat.

## <a name="next-steps"></a>További lépések

- [Egyéni javaslatok kérése](./get-custom-suggestions.md)
- [Az egyéni példánya keresése](./search-your-custom-view.md)
- [Konfigurálja, és egyéni üzemeltetett felhasználói felület használata](./hosted-ui.md)
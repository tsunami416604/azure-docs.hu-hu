---
title: 'Bing egyéni keresés: Adja meg az egyéni automatikus kiegészítési javaslatok |} Microsoft Docs'
description: Ismerteti, hogyan állítson be egyéni automatikus kiegészítési egyéni javaslatok
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347850"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Az egyéni automatikus javaslatokba felhasználói beállítása
Egyéni keresés megfelelő szinten előfizetett (lásd a [lapok árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), testre szabhatja a keresési javaslatok végzett az egyéni keresési élményt biztosít. Egyéni automatikus javaslatokba egy részleges lekérdezési karakterlánc, amely a felhasználó alapján javasolt lekérdezések listáját adja vissza. Az egyéni automatikus kiegészítési adja meg a keresési élményt vonatkozó egyéni keresési javaslatok. Megadhatja, hogy csak egyéni javaslatok vissza vagy így magába foglalja a Bing javaslatokat. Bing javaslatok is, ha egyéni javaslatok a Bing javaslatok előtt jelennek meg. Bing javaslatok a egyéni keresési példányát keretében korlátozódnak.

## <a name="configure-custom-autosuggest"></a>Egyéni konfigurálása automatikus kiegészítési
A következő utasításokat követve egyéni automatikus kiegészítési egyéni keresés-példány konfigurálása.

1.  Jelentkezzen be [egyéni keresési](https://customsearch.ai).
2.  Kattintson egy egyéni keresési példányát. Példány létrehozására, lásd: [hozza létre az első Bing egyéni keresési példányát](quick-start.md).
3.  Kattintson a **automatikus javaslatokba** fülre.

## <a name="enable-bing-suggestions"></a>Bing javaslatok engedélyezése
Ahhoz, hogy a Bing javaslatok, váltása a **automatikus Bing javaslatok** csúszkát a lévő helyre. A csúszka kék válik.

## <a name="add-suggestions"></a>Adja hozzá a javaslatok
Javaslat hozzáadásához adja meg azt a szövegmezőbe. Nyomja le az enter billentyűt, vagy kattintson a **+** ikonra. Egyéni javaslatok bármilyen nyelven lehet, és a Bing javaslatok előtt fog megjelenni.

## <a name="upload-suggestions"></a>Töltse fel a javaslatok
Feltöltheti fájlból javaslatok listája. Minden javaslat helyezze külön sorban. Kattintson a Feltöltés ikonra, és válassza ki a fájlt.

## <a name="remove-suggestions"></a>Távolítsa el a javaslatok
Javaslat, kattintson az eltávolítani kívánt javaslatot eltávolítása ikonra.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Egyéni automatikus kiegészítési konfigurációs módosítások érvénybe lépéséhez akár 24 óráig is eltarthat.

## <a name="next-steps"></a>További lépések

- [Egyéni javaslatok kérése](./get-custom-suggestions.md)
- [Az egyéni példány keresése](./search-your-custom-view.md)
- [Konfigurálja és felhasználhatják a egyéni központi felhasználói felületen](./hosted-ui.md)
---
title: 'Bing Custom Search: Keresés webhelyen |} A Microsoft Docs'
description: Ismerteti, hogyan konfigurálhatja a központi felhasználói felületen
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 7f2b97479ffcdb7ec8b3a1a635562d1fe68c3269
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158419"
---
# <a name="configure-your-hosted-ui-experience"></a>A központi felhasználói felület konfigurálása
Miután az egyéni keresőpéldányok, meghívhatja az egyéni keresési API a keresési eredmények között, és megjeleníti őket az alkalmazásban. Vagy, ha az alkalmazás egy webalkalmazást, a központi felhasználói felület által biztosított egyéni keresés is használhatja.   

## <a name="configure-custom-hosted-ui"></a>Egyéni üzemeltetett felhasználói felület konfigurálása
Az alábbi utasítások segítségével konfigurálhatja a webalkalmazás foglalandó üzemeltetett felhasználói Felületet.
1.  Jelentkezzen be egyéni keresés [portál](https://customsearch.ai).
2.  Kattintson egy egyéni keresési példány. Hozzon létre egy példányt, lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).
3.  Kattintson a **üzemeltetett felhasználói felület** fülre.
4.  Válasszon egy elrendezést.
    - Keresősáv és az eredményeket (alapértelmezett) &mdash; megjelenítése egy keresőmezőt tartalmazó és a keresési eredmények
    - Csak eredmény &mdash; nem egy keresőmező megjelenítendő, csak eredmény megjelenítése
    - POP-over &mdash; nem egy keresőmező megjelenítendő, egy mozgó területre csak eredmények megjelenítése
    
   > [!IMPORTANT]
   > Így feltétlenül foglalja bele a customConfig lekérdezési paraméter, amikor kiválasztja a **csak eredmény** elrendezés, lásd: [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  A **további konfigurációs beállítások**, adja meg az alkalmazás megfelelő értékeket. Ezek a beállítások opcionálisak. Alkalmazza vagy eltávolítja azokat a hatását, olvassa el az előnézeti ablaktáblában láthatja a jobb oldalon.  Elérhető konfigurációs lehetőségek közül választhat:
    - Webes keresés konfigurációk:
        - Webes eredmények engedélyezve &mdash; határozza meg, ha a webes keresés eredménye
        - Enable autosuggest &mdash; meghatározza egyéni automatikus kiegészítés engedélyezve van-e
        - Webes eredmények száma oldalanként &mdash; egyszerre megjeleníthető webes találatok száma
        - Képfelirat &mdash; határozza meg, ha a lemezképek jelennek meg a keresési eredmények
        - Jelölje ki a szavak &mdash; határozza meg, ha eredmények jelennek meg a keresési feltételeket a félkövérrel szedett
    - Konfigurációk a rendszerkép keresése:
        - Kép engedélyezve eredmények &mdash; határozza meg, ha a kép keresési eredményeket ad vissza
    - Egyéb konfigurációk:
        - Lap címe &mdash; az oldal címe területen megjelenő szöveg
        - Eszköztár téma &mdash; meghatározza, hogy az oldal címe területen háttérszíne
        - Keresés mező szöveg helyőrzője &mdash; jelenik meg a keresési mezőbe megelőzően a bemeneti szöveg
        - Cím hivatkozás URL-címe &mdash; célja a odkaz nA nadpis
        - Embléma URL-cím &mdash; címe mellett megjelenő kép 
        - Favicon URL-cím &mdash; a böngésző címsorában megjelenő ikon

   > [!IMPORTANT]
   > Legalább egy kép vagy a webes keresés engedélyezve kell lennie.

6.  Adja meg a keresési előfizetési kulcs, vagy válasszon egyet a legördülő listából. A listában megjelenik a fiókhoz tartozó Azure-előfizetésekből kulcsok. Lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Ha engedélyezte az automatikus kiegészítés, adja meg az automatikus kiegészítési előfizetési kulcsot, vagy válasszon egyet a legördülő listából. A listában megjelenik a fiókhoz tartozó Azure-előfizetésekből kulcsok. Egyéni automatikus kiegészítés csatolva előfizetési szinten van szükség, tekintse meg a [díjszabási lapjait](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Az egyéni üzemeltetett felhasználói felületi konfigurációs módosítások, a jobb oldali panelen végzett módosításokat vizuális referenciaként biztosít. A megjelenített keresési eredmények között nem állnak a tényleges eredmények a példány

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Egyéni felhasználói felület használata
A központi felhasználói felületén, vagy használhat: 

- A parancsfájl tartalmazza a weblap
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- A megadott URL-cím használata `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > A lap nem jeleníthető meg, az adatvédelmi nyilatkozat vagy más értesítések és a feltételeket. Az alkalmazhatósági eltérőek lehetnek.  

További információt, beleértve az egyéni konfiguráció azonosítója, látogasson el **végpontok** alatt a **éles** fülre.

## <a name="next-steps"></a>További lépések
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)
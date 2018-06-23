---
title: 'Bing egyéni keresés: Hely keresési |} Microsoft Docs'
description: Központi felhasználói felület konfigurálása
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 593ea4d23f8ddcec8efc4be632afa2aab1a5210f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347870"
---
# <a name="configure-your-hosted-ui-experience"></a>A központi felhasználói felületi élmény konfigurálása
A keresési eredmények között, és az alkalmazás megjeleníti azokat az egyéni Search API hívása után az egyéni keresési-példány konfigurálása. Vagy, ha az alkalmazás egy webalkalmazást, használhatja a központi felhasználói felület, amely egyéni keresés biztosít.   

## <a name="configure-custom-hosted-ui"></a>Egyéni központi felhasználói felület konfigurálása
A webalkalmazás foglalandó üzemeltetett felhasználói Felületet konfigurálásához kövesse az alábbi utasításokat.
1.  Jelentkezzen be egyéni keresés [portal](https://customsearch.ai).
2.  Kattintson egy egyéni keresési példányát. Példány létrehozására, lásd: [hozza létre az első Bing egyéni keresési példányát](quick-start.md).
3.  Kattintson a **tárolt felhasználói felület** fülre.
4.  Olyan elrendezést választ.
    - Keresősáv és az eredmények (alapértelmezett) &mdash; jeleníti meg a keresési mezőbe, és a keresési eredmények
    - Annak az eredménye csak &mdash; nem jeleníti meg a keresési mezőbe, csak eredmények megjelenítése
    - POP-over &mdash; nem jeleníti meg a keresési mezőbe, egy mozgó területre csak eredmények megjelenítése
    
   > [!IMPORTANT]
   > Ne felejtse el feltüntetni a customConfig lekérdezésparaméter lehetőséget választva a **eredmények csak** elrendezés, lásd: [lekérdezési paramétert](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  A **további konfigurációs**, adja meg az alkalmazás megfelelő értékeket. Ezek a beállítások opcionálisak. Alkalmazzon, vagy távolítsa el a hatását, olvassa el az előnézeti ablaktáblában láthatja a jobb oldali.  Elérhető konfigurációs lehetőségek állnak rendelkezésére:
    - Webes keresés konfigurációk:
        - Webalkalmazás-eredmények engedélyezett &mdash; határozza meg, ha a webes keresés eredménye
        - Enable automatikus kiegészítési &mdash; megállapítja egyéni automatikus kiegészítési engedélyezve van-e
        - Webalkalmazás-eredmények laponként &mdash; webes keresési eredmények egyszerre megjelenítendő száma
        - Képfelirat &mdash; határozza meg, ha lemezképek jelennek meg a keresési eredmények között
        - Jelölje ki a szavakat &mdash; határozza meg, ha eredmények jelennek meg a keresési feltételek félkövérrel szedett
    - Kép keresési konfigurációk:
        - Kép engedélyezve eredmények &mdash; határozza meg, ha a lemezkép keresés eredményeinek
    - Vegyes konfigurációkat:
        - Lap címe &mdash; a lap címterület megjelenő szöveg
        - Eszköztár téma &mdash; meghatározza, hogy a lap címterület háttérszíne
        - Keresés mezőbe helyőrző &mdash; adjon meg a keresési mező előtt megjelenő szöveg
        - Cím hivatkozás URL-címe &mdash; a cím hivatkozás célja
        - Embléma URL-cím &mdash; címe mellett megjelenített kép 
        - Favicon URL-cím &mdash; a böngésző címsorában megjelenő ikon

   > [!IMPORTANT]
   > Legalább egy kép keresés vagy webes engedélyezve kell lennie.

6.  Adja meg az előfizetés keresési kulcs, vagy válasszon egyet a legördülő listán. A legördülő lista a fiók az Azure-előfizetések kulcsokat a telepítéskor. Lásd: [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Ha engedélyezte az automatikus javaslatokba, adja meg az automatikus javaslatokba előfizetés kulcsot, vagy válasszon egyet a legördülő listán. A legördülő lista a fiók az Azure-előfizetések kulcsokat a telepítéskor. Egyéni automatikus javaslatokba csak csatolva előfizetés használható, tekintse meg a [lapok árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> A központi felhasználói felület egyéni konfigurációs módosításokat, a jobb oldali ablaktáblán vizuális referenciaként végzett módosítások biztosít. A megjelenített keresési eredmények között nincsenek a példány tényleges eredményei

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Egyéni felhasználói Felületüket felhasználása
A központi felhasználói felületén, vagy felhasználásához: 

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
  > A lap nem jeleníthető meg, az adatvédelmi nyilatkozat vagy más értesítéseket és feltételeket. Az alkalmazhatósági változhat.  

További információt, beleértve az egyéni konfigurációs Azonosítót, látogasson el **végpontok** alatt a **éles** fülre.

## <a name="next-steps"></a>További lépések
- [Jelölje ki a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Lap szolgáltatásának konfigurálása](./page-webpages.md)
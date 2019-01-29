---
title: Bing Custom Search egy üzemeltetett felhasználói felület konfigurálása |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Ez a cikk segítségével a konfigurálása és a egy üzemeltetett felhasználói felület integrálása a Bing Custom Search.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: 0336cc922e8f86792814196719e659d33a943bb8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193607"
---
# <a name="configure-your-hosted-ui-experience"></a>A központi felhasználói felület konfigurálása

Bing Custom Search, amely egy JavaScript kódrészletet, könnyen integrálható a weblapok és webes alkalmazások üzemeltetett felhasználói Felületet biztosít. A Bing Custom Search portál használata esetén konfigurálhatja az elrendezést, a színt és a keresési beállítások a felhasználói felület.



## <a name="configure-the-custom-hosted-ui"></a>Az egyéni üzemeltetett felhasználói felület konfigurálása

Egy üzemeltetett a webes alkalmazások felhasználói felületén konfigurálásához kövesse az alábbi lépéseket. A módosítások, a jobb oldali panelen kap a felhasználói felület előnézetét. A megjelenített keresési eredmények között nem a tényleges eredmények a példány el.

1. Jelentkezzen be a Bing Custom Search [portál](https://customsearch.ai).  
  
2. Válassza ki a Bing Custom Search-példányt.

3. Kattintson a **Hosted UI** (Üzemeltetett felhasználói felület) lapra.  
  
4. Válasszon ki egy elrendezést.

    |  |  |
    |---------|---------|
    |Keresősáv és az eredményeket (alapértelmezett)    | Megjeleníti a találatok alatt egy keresőmező található.         |
    |Csak az eredmények     | Megjeleníti a keresési eredmények csak, egy keresőmezőt tartalmazó nélkül. Ez az elrendezés használatakor meg kell adnia a keresési lekérdezés (`&q=<query string>`). A lekérdezési paraméter hozzáadása a kérelem URL-CÍMÉT a JavaScript-kódrészletet, vagy a HTML-végpont hivatkozás.        |
    |POP-over     | Egy keresőmezőt tartalmaz, és megjeleníti a keresési eredmények között egy mozgó területre.        |
    
5. Válasszon ki egy színtémát. Testre szabhatja a színeket az alkalmazás megfelelően kattintva **testreszabás téma**. Ha módosítani szeretné egy színt, vagy adja meg a szín HEXADECIMÁLIS RGB-értéke (például `#366eb8`), vagy kattintson a szín előzetes.

  Megtekintheti a módosításokat a portál jobb oldalán. Kattintson a **visszaállítás alapértelmezettre** visszaáll az alapértelmezett színeket a kiválasztott téma a módosításokat.

  > [!NOTE]
  > Vegye figyelembe a kisegítő lehetőségek, amikor kiválasztja a színeket.

6. A **további konfigurációs beállítások**, adja meg az alkalmazás megfelelő értékeket. Ezek a beállítások opcionálisak. Alkalmazza vagy eltávolítja azokat a hatását, olvassa el az előnézeti ablaktáblában láthatja a jobb oldalon. Elérhető konfigurációs lehetőségek közül választhat:  

7. Adja meg a keresési előfizetési kulcs, vagy válasszon egyet a legördülő listából. Az Azure-fiók előfizetés-kulcsok megjelenik a legördülő listából. Lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Ha engedélyezte az automatikus kiegészítés, adja meg az automatikus kiegészítési előfizetési kulcsot, vagy válasszon egyet a legördülő listából. Az Azure-fiók előfizetés-kulcsok megjelenik a legördülő listából. Egyéni automatikus kiegészítés van szükség egy adott előfizetés-szintű, tekintse meg a [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Egyéni felhasználói felület használata

A központi felhasználói felületén, vagy használhat: 

- A parancsfájl tartalmazza a weblap  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Vagy használja a következő URL-címet egy webböngészőben.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Az URL-cím, igény szerint adja hozzá a következő lekérdezési paraméterek. Ezek a paraméterekkel kapcsolatos további információkért lásd: [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) hivatkozást.
  >
  > - válaszok
  > - mkt
  > - biztonságos keresés
  > - setlang

  > [!IMPORTANT]
  > A lap nem jeleníthető meg, az adatvédelmi nyilatkozat vagy más értesítések és a feltételeket. Az alkalmazhatósági eltérőek lehetnek.  

További információt, beleértve az egyéni konfiguráció azonosítója, látogasson el **végpontok** alatt a **éles** fülre.

## <a name="configuration-options"></a>Beállítási lehetőségek

A központi felhasználói felület viselkedését konfigurálhatja kattintva **további konfigurációs beállítások**, és az értékek megadásával. Ezek a beállítások opcionálisak. Alkalmazza vagy eltávolítja azokat a hatását, olvassa el az előnézeti ablaktáblában láthatja a jobb oldalon. 

### <a name="web-search-configurations"></a>Webalkalmazás-konfigurációk keresése

|  |  |
|---------|---------|
|Webes találatokat engedélyezve    | Azt határozza meg, ha engedélyezve van-e a webes keresés (látni fogja a webes fülre az oldal tetején)        |
|Enable automatikus kiegészítés     | Azt határozza meg, ha egyéni automatikus kiegészítés engedélyezve van (lásd: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) további költségek mellett).        |
|Webes eredmények száma oldalanként    | (A maximális hossz 50 eredmények száma oldalanként) egyszerre megjeleníthető a webes keresési eredmények száma.        |
|Képfelirat   | Azt határozza meg, ha a lemezképek jelennek meg a keresési eredmények között.|


A következő konfigurációk láthatók, ha rákattint **speciális konfigurációk megjelenítése**:


|  | |
|---------|---------|
|Szavak kijelölése     | Azt határozza meg, ha eredmények jelennek meg a félkövérrel szedett keresési feltételeket.         |
|Hivatkozás célja    |  Azt határozza meg, ha a weblap nyílik meg egy új böngészőlapon (üres) vagy az azonos böngészőlapon (saját) amikor a felhasználó rákattint egy keresési eredményben.        |

### <a name="image-search-configurations"></a>Lemezkép-konfigurációk keresése

| | |
|---------|---------|
|Képtalálatok engedélyezve     | Azt határozza meg, ha engedélyezve van-e a képkeresési (megjelenik egy képeket fülre az oldal tetején).            |
|Kép eredmények száma oldalanként     | (A maximális érték 150 eredmények száma oldalanként) egyszerre megjelenítendő kép keresési eredmények száma.          |

A következő konfiguráció látható, ha rákattint **speciális konfigurációk megjelenítése**.  
  
| | |
|---------|---------|
| Szűrők engedélyezése     | Hozzáadja a szűrőket, amelyeket a felhasználó segítségével szűrheti a képeket, a Bing adja vissza. A felhasználó például szűrheti az eredményeket az animált GIF-csak.|

### <a name="video-search-configurations"></a>Videókeresési konfigurációk

|  | |
|---------|---------|
|Videó engedélyezve     | Azt határozza meg, ha engedélyezve van-e a videókeresési (látni fogja a videók fülre az oldal tetején).           |
|Videó eredmények száma oldalanként   | Videókeresési eredményeket (a maximális érték 150 eredmények száma oldalanként) egyszerre megjeleníthető száma.        |

A következő konfiguráció látható, ha rákattint **speciális konfigurációk megjelenítése**.  
  
|  | |
|---------|---------|
|Szűrők engedélyezése    | Hozzáadja a szűrőket, amelyeket a felhasználó használhatja a videókat a Bing visszaadó szűréséhez. Például a felhasználó szűrheti az eredményeket a videókat, amelyekhez egy adott feloldási vagy az elmúlt 24 órában észlelt videókat.          |

### <a name="miscellaneous-configurations"></a>Egyéb konfigurációk


| |  |
|---------|---------|
|Lap címe   | (Nem a pop-over elrendezés), a keresési eredmények oldalának címe területén megjelenő szöveg.        |
|Eszköztár téma    | Meghatározza, hogy a keresési eredmények oldalának címterülete hátterének színét. |

A következő konfigurációk láthatók, ha rákattint **speciális konfigurációk megjelenítése**.  

|1. oszlop  |Column2  |
|---------|---------|
|Keresési mező szöveg helyőrzője   | Megjelenik a keresési mezőbe megelőzően a bemeneti szöveg.        |
|Cím hivatkozás URL-címe    |A cím hivatkozás céljához.         |
|Logo URL     | A kép címe mellett jelenik meg.         |
|Favicon    | A böngésző címsorában megjelenő ikon.          |

Az alábbi konfigurációk lehetségesek, csak ha a tárolt felhasználói felület (ezek a JavaScript-kódrészletet használatakor nem alkalmazhatók) HTML-végponton keresztül.

- Lap címe
- Eszköztár téma
- Cím hivatkozás URL-címe
- Logo URL
- Faviicon URL  

## <a name="next-steps"></a>További lépések

- [Díszítő jelölők használata szövegkiemeléshez](./hit-highlighting.md)
- [Weboldalak lapozása](./page-webpages.md)

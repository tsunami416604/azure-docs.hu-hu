---
title: A Bing egyéni kereséshez rendezett felhasználói felület konfigurálása | Microsoft dokumentumok
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben konfigurálhatja és integrálhatja a Bing egyéni kereséshez használt felhasználói felületét.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 2cc89bf57167db75404c044f58d18ab48edfaf38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854086"
---
# <a name="configure-your-hosted-ui-experience"></a>Üzemeltetett felhasználói felület konfigurálása

A Bing egyéni keresés olyan üzemeltetett felhasználói felületet biztosít, amelyet javaScript-kódrészletként könnyedén integrálhat weblapjaiba és webalkalmazásaiba. A Bing egyéni keresési portál használatával konfigurálhatja a felhasználói felület elrendezését, színét és keresési beállításait.



## <a name="configure-the-custom-hosted-ui"></a>Az egyéni entárolt felhasználói felület konfigurálása

A webalkalmazások üzemeltetett felhasználói felületének konfigurálásához kövesse az alábbi lépéseket. A módosítások végrehajtása közben a jobb oldali ablaktábla előnézetet ad a felhasználói felületről. A megjelenített keresési eredmények nem a példány tényleges eredményei.

1. Jelentkezzen be a Bing egyéni keresési [portáljára.](https://customsearch.ai)  
  
2. Válassza ki a Bing egyéni keresési példányát.

3. Kattintson a **Hosted UI** (Üzemeltetett felhasználói felület) lapra.  
  
4. Válasszon ki egy elrendezést.

    |  |  |
    |---------|---------|
    |Keresősáv és találatok (alapértelmezett)    | Egy keresőmezőt jelenít meg alatta a keresési eredményekkel.         |
    |Csak eredmények     | Csak keresési eredményeket jelenít meg keresőmező nélkül. Ha ezt az elrendezést használja,`&q=<query string>`meg kell adnia a keresési lekérdezést ( ). Adja hozzá a lekérdezési paramétert a kérelem URL-címéhez a JavaScript-kódrészletben vagy a HTML-végponthivatkozásban.        |
    |Átugratva     | Keresőmezőt biztosít, és a keresési eredményeket csúszó átfedésben jeleníti meg.        |
    
5. Válasszon ki egy színtémát. A téma **testreszabása**gombra kattintva testreszabhatja az alkalmazáshoz illeszkedő színeket. A szín módosításához adja meg a szín RGB HEX `#366eb8`értékét (például ), vagy kattintson a szín előnézetére.

   A módosítások előnézetét a portál jobb oldalán tekintheti meg. Ha az **Alapozás gombra** kattint az alapértelmezett értékre kattintva a módosítások at a kijelölt téma alapértelmezett színeire állítja vissza.

   > [!NOTE]
   > A színek kiválasztásakor vegye figyelembe a kisegítő lehetőségeket.

6. A **További konfigurációk csoportban**adja meg az alkalmazásnak megfelelő értékeket. Ezek a beállítások nem kötelezőek. Az alkalmazásuk vagy eltávolításuk hatásának megtekintéséhez tekintse meg a jobb oldali betekintő ablaktáblát. A rendelkezésre álló konfigurációs lehetőségek a következők:  

7. Írja be a keresési előfizetés imázsát, vagy válasszon egyet a legördülő listából. A legördülő lista feltölti az Azure-fiók előfizetéseiből származó kulcsokat. Lásd: [Cognitive Services API-fiók.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)  

8. Ha engedélyezte az automatikus javaslat, adja meg az automatikus javaslat előfizetési kulcsot, vagy válasszon egyet a legördülő listából. A legördülő lista feltölti az Azure-fiók előfizetéseiből származó kulcsokat. Az egyéni automatikus javaslathoz egy adott előfizetési szint szükséges, lásd a [díjszabást.](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Egyéni felhasználói felület felhasználása

A tárolt felhasználói felület felhasználásához: 

- A parancsfájl felvétele a weblapra  
  
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
  > Szükség szerint adja hozzá a következő lekérdezési paramétereket az URL-címhez. Ezekről a paraméterekről az [Egyéni keresési API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) című témakörben talál további információt.
  >
  > - q
  > - mkt
  > - Safesearch
  > - setlang között

  > [!IMPORTANT]
  > Az oldal nem tudja megjeleníteni az adatvédelmi nyilatkozatot vagy más értesítéseket és feltételeket. Az Ön használatára való alkalmasság eltérő lehet.  

További információkért, beleértve az egyéni konfigurációs azonosítót, nyissa meg a **Végpontok** az **Éles környezetben** lap.

## <a name="configuration-options"></a>Beállítási lehetőségek

A központi felhasználói felület működését a **További konfigurációk**gombra kattintva és értékek megadásával konfigurálhatja. Ezek a beállítások nem kötelezőek. Az alkalmazásuk vagy eltávolításuk hatásának megtekintéséhez tekintse meg a jobb oldali betekintő ablaktáblát. 

### <a name="web-search-configurations"></a>Webes keresési konfigurációk

|  |  |
|---------|---------|
|Webes eredmények engedélyezve    | Azt határozza meg, hogy engedélyezve van-e a webes keresés (a lap tetején megjelenik egy Web lap)        |
|Automatikus javaslat engedélyezése     | Azt határozza meg, hogy engedélyezve van-e az egyéni automatikus javaslat (lásd a további költségek [díjszabását).](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)        |
|Webes eredmények oldalanként    | Az egyszerre megjelenítendő webes keresési eredmények száma (a maximális érték oldalanként 50 találat).        |
|Képaláírás   | Azt határozza meg, hogy a képek megjelenjenek-e a keresési eredményekkel.|


Ha a **Speciális konfigurációk megjelenítése gombra**kattint, a következő konfigurációk jelennek meg:


|  | |
|---------|---------|
|Szavak kiemelése     | Azt határozza meg, hogy az eredmények félkövér rel kettecelő keresési kifejezésekkel jelenjenek-e meg.         |
|Hivatkozás i    |  Azt határozza meg, hogy a weblap új böngészőlapon (Üres) vagy ugyanabban a böngészőlapon (saját) nyílik-e meg, amikor a felhasználó a keresési eredményre kattint.        |

### <a name="image-search-configurations"></a>Képkeresési konfigurációk

| | |
|---------|---------|
|Képeredmények engedélyezve     | Azt határozza meg, hogy engedélyezve van-e a képkeresés (az oldal tetején megjelenik egy Képek lap).            |
|Képeredmények oldalanként     | Az egyszerre megjelenítendő képkeresési eredmények száma (a maximális érték oldalanként 150 találat).          |

Ha a **Speciális konfigurációk megjelenítése gombra**kattint, a következő konfiguráció jelenik meg.  
  
| | |
|---------|---------|
| Szűrők engedélyezése     | Szűrőket ad hozzá, amelyek segítségével a felhasználó szűrheti a Bing által visszaadott képeket. A felhasználó például szűrheti az eredményeket csak animált GIF-ekre.|

### <a name="video-search-configurations"></a>Videokeresési konfigurációk

|  | |
|---------|---------|
|A videó eredmények engedélyezve vannak     | Azt határozza meg, hogy engedélyezve van-e a videókeresés (az oldal tetején megjelenik a Videók fül).           |
|Videóeredmények oldalanként   | Az egyszerre megjelenítendő videókeresési találatok száma (a maximális érték oldalanként 150 találat).        |

Ha a **Speciális konfigurációk megjelenítése gombra**kattint, a következő konfiguráció jelenik meg.  
  
|  | |
|---------|---------|
|Szűrők engedélyezése    | Szűrőket ad hozzá, amelyek segítségével a felhasználó szűrheti a Bing által visszaadott videókat. A felhasználó szűrheti például az elmúlt 24 órában felfedezett, adott felbontású videók vagy videók eredményeit.          |

### <a name="miscellaneous-configurations"></a>Egyéb konfigurációk


| |  |
|---------|---------|
|Oldalcím   | A keresési eredményoldal címterületén megjelenő szöveg (nem előugró elrendezés esetén).        |
|Eszköztár téma    | A keresési eredményoldal címterületének háttérszínét határozza meg. |

Ha a **Speciális konfigurációk megjelenítése gombra**kattint, a következő konfigurációk jelennek meg.  

|1. oszlop  |2. oszlop  |
|---------|---------|
|Keresőmező szöveghelyőrzője   | A bevitel előtt a keresőmezőben megjelenő szöveg.        |
|Címhivatkozás url-címe    |A címhivatkozás célzása.         |
|Embléma URL-címe     | A cím mellett megjelenő kép.         |
|Favicon    | A böngésző címsorában megjelenő ikon.          |

A következő konfigurációk csak akkor érvényesek, ha a szolgáltatott felhasználói felületet a HTML-végponton keresztül használja fel (nem érvényesek, ha a JavaScript-kódrészletet használja).

- Oldalcím
- Eszköztár téma
- Címhivatkozás URL-címe
- Embléma URL-címe
- Faviicon URL-címe  

## <a name="next-steps"></a>További lépések

- [Díszítő jelölők használata szövegkiemeléshez](../bing-web-search/hit-highlighting.md)
- [Weboldalak lapozása](./page-webpages.md)

---
title: Keresési helyet, a központi felhasználói felületen a Bing egyéni keresés
titlesuffix: Azure Cognitive Services
description: Ismerteti, hogyan konfigurálhatja a Bing Custom Search tárolt felhasználói felületén.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 77845cb60dac707326acdb08b0198f8725a36f62
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813974"
---
# <a name="configure-your-hosted-ui-experience"></a>A központi felhasználói felület konfigurálása

Miután az egyéni keresőpéldányok, meghívhatja az egyéni keresési API a keresési eredmények között, és megjeleníti őket az alkalmazásban. Vagy, ha az alkalmazás egy webalkalmazást, a központi felhasználói felület által biztosított egyéni keresés is használhatja.   

## <a name="configure-custom-hosted-ui"></a>Egyéni üzemeltetett felhasználói felület konfigurálása

Központi felhasználói felületen a webalkalmazás konfigurálásához kövesse az alábbi lépéseket:

1. Jelentkezzen be egyéni keresés [portál](https://customsearch.ai).  
  
2. Kattintson egy egyéni keresési példány. Hozzon létre egy példányt, lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).  

3. Kattintson a **üzemeltetett felhasználói felület** fülre.  
  
4. Válasszon egy elrendezést.
  
  - Keresősáv és az eredményeket (alapértelmezett) &mdash; Ez az elrendezés a hagyományos keresése oldal, a keresőmezőbe, és a keresési eredmények.
  - Csak eredmény &mdash; ebben az elrendezésben csak a keresési eredményeket jeleníti meg. Ez az elrendezés nem jeleníti meg a keresési mezőbe. Meg kell adnia a keresési lekérdezést a lekérdezési paraméter hozzáadásával (& q =\<lekérdezési karakterlánc >) a kérés URL-címét a JavaScript-kódrészletet vagy HTML-végpont hivatkozásra.
  - POP-over &mdash; Ez az elrendezés egy keresőmezőt tartalmaz, és megjeleníti a keresési eredmények között egy mozgó területre.
      
5. Válassza ki a színtémát. A lehetséges témák a következők: 
  
  - Klasszikus
  - Sötét
  - Látképéről kék

  Kattintson az egyes témák témát működik a legjobban a webalkalmazással megtekintéséhez. Ha szeretné jobban integrálhatja a webalkalmazással, kattintson a színtémát finomhangolása **testreszabás téma**. Nem minden szín hatással van minden elrendezés téma. Ha módosítani szeretné egy színt, a megfelelő mezőben adja meg a színt HEXADECIMÁLIS RGB-értéke (például #366eb8). Vagy, a color gombra, majd a shade az Önnek legmegfelelőbb. 
  
  Miután megváltoztatta egy színt, ismerje meg hogyan a módosítás hatással van az előzetes verzió példa a jobb oldalon. Mindig kattinthat **visszaállítás alapértelmezettre** , hogy a kiválasztott téma alapértelmezett színét.

  > [!NOTE]
  > Amikor módosítja a színtémát, színek kiválasztásakor vegye figyelembe a kisegítő.

5. A **további konfigurációs beállítások**, adja meg az alkalmazás megfelelő értékeket. Ezek a beállítások opcionálisak. Alkalmazza vagy eltávolítja azokat a hatását, olvassa el az előnézeti ablaktáblában láthatja a jobb oldalon. Elérhető konfigurációs lehetőségek közül választhat:  
  
  - Webes keresés konfigurációk:
    - Webes eredmények engedélyezve &mdash; határozza meg, ha engedélyezve van-e a webes keresés (látni fogja a webes fülre az oldal tetején).
    - Enable autosuggest &mdash; meghatározza egyéni automatikus kiegészítés engedélyezve van-e (lásd: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) további költségek mellett).
    - Webes eredmények száma oldalanként &mdash; (a maximális hossz 50 eredmények száma oldalanként) egyszerre megjeleníthető a webes keresési eredmények számát.
    - Képfelirat &mdash; határozza meg, ha a lemezképek jelennek meg a keresési eredmények között.
  
    A következő konfigurációk láthatók, ha rákattint **speciális konfigurációk megjelenítése**.  
  
    - Jelölje ki a szavak &mdash; határozza meg, ha eredmények jelennek meg a félkövérrel szedett keresési feltételeket. 
    - Hivatkozáscéllal &mdash; határozza meg, ha a weblap nyílik meg egy új böngészőlapon (üres) vagy az azonos böngészőlapon (saját) amikor a felhasználó rákattint egy keresési eredményben. 

  - Konfigurációk a rendszerkép keresése:
    - Kép engedélyezve eredmények &mdash; határozza meg, ha engedélyezve van-e a képkeresési (megjelenik egy képeket fülre az oldal tetején).   
    - Eredmények száma oldalanként kép &mdash; (a maximális érték 150 eredmények száma oldalanként) egyszerre a megjelenítendő kép keresési eredmények számát.  
  
    A következő konfiguráció látható, ha rákattint **speciális konfigurációk megjelenítése**.  
  
    - Engedélyezze a szűrők &mdash; hozzáadja a szűrőket, amelyeket a felhasználó segítségével szűrheti a képeket, a Bing adja vissza. A felhasználó például szűrheti az eredményeket az animált GIF-csak.

  - Videókeresési konfigurációk:
    - Videó engedélyezve &mdash; határozza meg, ha engedélyezve van-e a videókeresési (látni fogja a videók fülre az oldal tetején).  
    - Videó eredmények száma oldalanként &mdash; videókeresési eredményeket (a maximális érték 150 eredmények száma oldalanként) egyszerre megjeleníthető száma.
  
    A következő konfiguráció látható, ha rákattint **speciális konfigurációk megjelenítése**.  
  
    - Engedélyezze a szűrők &mdash; hozzáadja a szűrőket, amelyeket a felhasználó használhatja a videókat a Bing visszaadó szűréséhez. Például a felhasználó szűrheti az eredményeket a videókat, amelyekhez egy adott feloldási vagy az elmúlt 24 órában észlelt videókat.

  - Egyéb konfigurációk:
    - Lap címe &mdash; (nem a pop-over elrendezés), a keresési eredmények oldalának címe területén megjelenő szöveg.
    - Eszköztár téma &mdash; határozza meg a keresési eredmények oldalának címterülete hátterének színét.  
  
    A következő konfigurációk láthatók, ha rákattint **speciális konfigurációk megjelenítése**.  
  
    - Keresés mező szöveg helyőrzője &mdash; jelenik meg a keresési mezőbe megelőzően a bemeneti szöveg.
    - Cím hivatkozás URL-címe &mdash; a cím hivatkozás céljához.
    - Embléma URL-cím &mdash; kép címe mellett jelenik meg. 
    - Favicon URL-cím &mdash; ikon jelenik meg a böngésző címsorában.  

    Az alábbi konfigurációk lehetségesek, csak ha a tárolt felhasználói felület (ezek a JavaScript-kódrészletet használatakor nem alkalmazhatók) HTML-végponton keresztül.
    
    - Lap címe
    - Eszköztár téma
    - Cím hivatkozás URL-címe
    - Embléma URL-címe
    - Faviicon URL-címe  
  
6. Adja meg a keresési előfizetési kulcs, vagy válasszon egyet a legördülő listából. Az Azure-fiók előfizetés-kulcsok megjelenik a legördülő listából. Lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

7. Ha engedélyezte az automatikus kiegészítés, adja meg az automatikus kiegészítési előfizetési kulcsot, vagy válasszon egyet a legördülő listából. Az Azure-fiók előfizetés-kulcsok megjelenik a legördülő listából. Egyéni automatikus kiegészítés van szükség egy adott előfizetés-szintű, tekintse meg a [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Az egyéni üzemeltetett felhasználói felületi konfigurációs módosítások, a jobb oldali panelen végzett módosításokat vizuális referenciaként biztosít. A megjelenített keresési eredmények között nem a tényleges eredmények a példány el.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

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

## <a name="next-steps"></a>További lépések

- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)
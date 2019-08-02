---
title: Üzemeltetett felhasználói felület konfigurálása Bing Custom Searchhoz | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Bing Custom Search üzemeltetett felhasználói felületének konfigurálására és integrálására használható.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: ae073e10331f07d9863da1d4ed97533f95b87c86
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405061"
---
# <a name="configure-your-hosted-ui-experience"></a>Az üzemeltetett felhasználói felületi élmény konfigurálása

A Bing Custom Search egy üzemeltetett felhasználói felületet biztosít, amellyel JavaScript-kódrészletként könnyedén integrálhatja weblapjait és webalkalmazásait. A Bing Custom Search portál használatával konfigurálhatja a felhasználói felület elrendezését, színét és keresési beállításait.



## <a name="configure-the-custom-hosted-ui"></a>Az egyéni tárolt felhasználói felület konfigurálása

A webalkalmazásokhoz tartozó üzemeltetett felhasználói felület konfigurálásához kövesse az alábbi lépéseket. A módosítások elvégzése után a jobb oldali ablaktábla a felhasználói felület előnézetét jeleníti meg. A megjelenített keresési eredmények nem tényleges eredmények a példányhoz.

1. Jelentkezzen be Bing Custom Search [portálra](https://customsearch.ai).  
  
2. Válassza ki Bing Custom Search példányát.

3. Kattintson a **Hosted UI** (Üzemeltetett felhasználói felület) lapra.  
  
4. Válasszon ki egy elrendezést.

    |  |  |
    |---------|---------|
    |Keresési sáv és eredmények (alapértelmezett)    | Megjeleníti az alatta lévő keresési eredményeket tartalmazó keresőmezőt.         |
    |Csak eredmények     | Csak a keresési eredményeket jeleníti meg, keresési mező nélkül. Az elrendezés használatakor meg kell adnia a keresési lekérdezést (`&q=<query string>`). Adja hozzá a lekérdezési paramétert a kérelem URL-címéhez a JavaScript-kódrészletben, vagy a HTML-végpont hivatkozását.        |
    |Előugró ablak     | Egy keresőmezőt biztosít, és megjeleníti a keresési eredményeket egy csúszó átfedésben.        |
    
5. Válasszon ki egy színtémát. Testreszabhatja a színeket úgy, hogy illeszkedjen az alkalmazáshoz a **téma testreszabása**lehetőségre kattintva. A szín módosításához adja meg a szín RGB hexadecimális értékét (például `#366eb8`), vagy kattintson a szín előnézetére.

   A módosításokat a portál jobb oldalán tekintheti meg. Ha az Alaphelyzetbe állítás gombra kattint **,** a rendszer visszaállítja a módosításokat a kiválasztott téma alapértelmezett színeire.

   > [!NOTE]
   > A színek kiválasztásánál érdemes megfontolni a kisegítő lehetőségeket.

6. A **további konfigurációk**területen adja meg a megfelelő értékeket az alkalmazáshoz. Ezek a beállítások nem kötelezőek. Ha szeretné megtekinteni, hogy milyen hatással van a alkalmazásra vagy eltávolításra, tekintse meg a jobb oldali előnézet ablaktáblát. Az elérhető konfigurációs lehetőségek a következők:  

7. Adja meg a keresési előfizetés kulcsát, vagy válasszon egyet a legördülő listából. A legördülő lista az Azure-fiók előfizetéséhez tartozó kulcsokkal van feltöltve. Lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Ha engedélyezte az automatikus kiegészítést, adja meg az automatikus javaslat előfizetési kulcsát, vagy válasszon egyet a legördülő listából. A legördülő lista az Azure-fiók előfizetéséhez tartozó kulcsokkal van feltöltve. Az egyéni automatikus kiegészítéshez egy adott előfizetési szintet kell [](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)megadni, lásd a díjszabást.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Egyéni felhasználói felület használata

Az üzemeltetett felhasználói felületet a következő módokon használhatja: 

- A szkript belefoglalása a weblapra  
  
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

- Vagy használja a következő URL-címet egy böngészőben.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Szükség szerint adja hozzá a következő lekérdezési paramétereket az URL-címhez. További információ ezekről a paraméterekről: [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) -hivatkozás.
  >
  > - válaszok
  > - MKT
  > - biztonságos keresési
  > - setlang

  > [!IMPORTANT]
  > A lap nem jelenítheti meg az adatvédelmi nyilatkozatát vagy más megjegyzéseit és feltételeit. A használatra való alkalmassága eltérő lehet.  

Ha további információkat szeretne, például az egyéni konfiguráció AZONOSÍTÓját,  ugorjon a végpontok elemre az **éles** lapon.

## <a name="configuration-options"></a>Beállítási lehetőségek

Az üzemeltetett felhasználói felület működésének konfigurálásához kattintson a **további konfigurációk**lehetőségre, és adjon meg értékeket. Ezek a beállítások nem kötelezőek. Ha szeretné megtekinteni, hogy milyen hatással van a alkalmazásra vagy eltávolításra, tekintse meg a jobb oldali előnézet ablaktáblát. 

### <a name="web-search-configurations"></a>Webes keresési konfigurációk

|  |  |
|---------|---------|
|Webes eredmények engedélyezve    | Meghatározza, hogy engedélyezve van-e a webes keresés (a lap tetején megjelenik egy weblap)        |
|Automatikus kiegészítés engedélyezése     | Meghatározza, hogy engedélyezve van-e az egyéni [](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) automatikus kiegészítés (a további díjak díjszabása).        |
|Webes eredmények/oldal    | Az egyszerre megjelenítendő webes keresési eredmények száma (a maximum 50 találat/oldal).        |
|Képfelirat   | Meghatározza, hogy megjelenjenek-e a képek a keresési eredmények között.|


A következő konfigurációk láthatók, ha a **speciális konfigurációk megjelenítése**lehetőségre kattint:


|  | |
|---------|---------|
|Szavak kiemelése     | Meghatározza, hogy az eredmények félkövérrel jelennek-e meg keresési kifejezésekkel.         |
|Hivatkozás célja    |  Meghatározza, hogy a weblap megjelenik-e egy új böngészőablakban (üres) vagy ugyanazon böngésző lapon (saját), amikor a felhasználó egy keresési eredményre kattint.        |

### <a name="image-search-configurations"></a>Képkeresési konfigurációk

| | |
|---------|---------|
|Képeredmények engedélyezve     | Meghatározza, hogy engedélyezve van-e a képkeresés (a lap tetején megjelenik egy images lap).            |
|Képtalálatok száma oldalanként     | A képkeresési találatok száma egyszerre megjeleníthető (a maximális érték 150, oldalanként).          |

A következő konfiguráció jelenik meg, ha a **speciális konfigurációk megjelenítése**lehetőségre kattint.  
  
| | |
|---------|---------|
| Szűrők engedélyezése     | Szűrők hozzáadásával a felhasználó a Bing által visszaadott rendszerképek szűrésére használható. A felhasználó például csak animált GIF-fájlok esetében szűrheti az eredményeket.|

### <a name="video-search-configurations"></a>Videós keresési konfigurációk

|  | |
|---------|---------|
|Videós eredmények engedélyezve     | Meghatározza, hogy engedélyezve van-e a videó keresése (a lap tetején megjelenik egy videó lap).           |
|Videós eredmények/oldal   | A videó keresési eredményeinek száma, amely egyszerre jeleníthető meg (a maximális érték 150, oldalanként).        |

A következő konfiguráció jelenik meg, ha a **speciális konfigurációk megjelenítése**lehetőségre kattint.  
  
|  | |
|---------|---------|
|Szűrők engedélyezése    | Szűrők hozzáadásával a felhasználó a Bing által visszaadott videók szűrésére használható. A felhasználó például szűrheti a videók eredményeit az elmúlt 24 órában felderített adott felbontással vagy videókkal.          |

### <a name="miscellaneous-configurations"></a>Egyéb konfigurációk


| |  |
|---------|---------|
|Lap címe   | A keresési eredmények lap title (cím) területén megjelenő szöveg (nem az előugró elrendezéshez).        |
|Eszköztár témája    | Meghatározza a keresési eredmények oldal cím területének háttérszínét. |

A következő konfigurációk láthatók, ha a **speciális konfigurációk megjelenítése**lehetőségre kattint.  

|1\. oszlop  |Column2  |
|---------|---------|
|Keresőmező szövegének helyőrzője   | A beviteli mezőben a bevitel előtt megjelenő szöveg.        |
|Cím hivatkozásának URL-címe    |A cím hivatkozásának célja         |
|Embléma URL-címe     | A cím mellett megjelenő rendszerkép         |
|Favicon    | Ikon jelenik meg a böngésző címsorában.          |

A következő konfigurációk csak akkor érvényesek, ha a futtatott felhasználói felületet a HTML-végponton keresztül használja (nem érvényesek a JavaScript-kódrészlet használatakor).

- Lap címe
- Eszköztár témája
- Cím hivatkozásának URL-címe
- Embléma URL-címe
- Faviicon URL-címe  

## <a name="next-steps"></a>További lépések

- [Díszítő jelölők használata szövegkiemeléshez](./hit-highlighting.md)
- [Weboldalak lapozása](./page-webpages.md)

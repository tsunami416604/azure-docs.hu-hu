---
title: 'Bing Custom Search: Hozzon létre egy egyéni keresési weblap |} A Microsoft Docs'
description: Ismerteti, hogyan konfigurálhat egy egyéni keresési példány, és integrálhatja azt webes
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: 8bc1520325afc256ac62cc1f1dfaf24c53da4b83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979998"
---
# <a name="build-a-custom-search-web-page"></a>Custom Search-webhely létrehozása

Bing Custom Search lehetővé teszi, hogy a témakörök, amelyek az Ön számára személyre szabott keresési funkciókkal. Például ha saját keresési funkciókat biztosító Harcművészet webhely, megadhatja a tartományok, alhelyek és a Bing keresési weblapok. A tartalom helyett előfordulhat, hogy irreleváns tartalmat tartalmazó általános keresési eredmények átlapozva érdeklő szabott keresési eredmények megjelennek a felhasználók számára. 

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja egy egyéni keresési példány, és integrálnia kell az új weblap.

A kezelt feladatok a következők:

> [!div class="checklist"]
> - Hozzon létre egy egyéni keresési példány
> - Aktív bejegyzés hozzáadása
> - Letiltott bejegyzés hozzáadása
> - Rögzített bejegyzés hozzáadása
> - Egyéni keresési integrálása egy weblap

## <a name="prerequisites"></a>Előfeltételek

- Követheti az oktatóanyagot, szükség van egy előfizetési kulcsot a Bing Custom Search API.  A kulcs lekéréséhez lásd: [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t.

## <a name="create-a-custom-search-instance"></a>Hozzon létre egy egyéni keresési példány

Bing Custom Search-példány létrehozása:

1. Nyisson meg egy webböngészőt.  
  
2. Keresse meg az egyéni keresési [portál](https://customsearch.ai).  
  
3. Jelentkezzen be Microsoft-fiókkal (MSA) a portálon. Ha az MSA nem rendelkezik, kattintson a **Microsoft-fiók létrehozásához**. Ha most először a portál használatával, kérni fogja a engedélyekkel az adatok eléréséhez. Kattintson a **Yes** (Igen) gombra.  
  
4. Miután bejelentkezett, kattintson a **új egyéni keresés**. Az a **hozzon létre egy új egyéni keresési példány** ablakban adjon meg egy nevet, amely jelentéssel bíró, és ismerteti a visszaadott tartalom típusát. A név bármikor módosíthatja.  
  
  ![Képernyőkép a hozzon létre egy új egyéni keresési példány használata](../media/newCustomSrch.png)  
  
5. Kattintson az OK gombra, adja meg egy URL-címet, valamint az URL-cím mindegyik lapot tünteti tartalmazza.  
  
  ![Definíció lap képernyőképe az URL-címe](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Aktív bejegyzés hozzáadása

Adott webhelyekhez vagy URL-címek eredményeinek történő felvételéhez adja hozzá őket a **aktív** fülre.

1.  Az a **konfigurációs** lap, kattintson a **aktív** lapra, és adjon meg egy vagy több webhely fel szeretne venni a Keresés URL-CÍMÉT.

    ![A definíció szerkesztő aktív lap képernyőképe](../media/customSrchEditor.png)

2.  Győződjön meg arról, hogy a példány eredményeket ad vissza, adjon meg egy lekérdezést az előnézeti ablaktáblában láthatja a jobb oldalon. Bing – csak a nyilvános webhelyekhez, rendelkezik indexelt eredményeket adja vissza.

## <a name="add-blocked-entries"></a>Letiltott bejegyzés hozzáadása

Szeretné kizárni az eredményeket az egyes webhelyek vagy URL-címek, adja hozzá őket a **letiltott** fülre.

1. Az a **konfigurációs** lap, kattintson a **letiltott** lapra, és adja meg a Keresés a kizárni kívánt egy vagy több webhely URL-CÍMÉT.

    ![A definíció szerkesztő letiltott lap képernyőképe](../media/blockedCustomSrch.png)


2. Győződjön meg arról, hogy a példány nem eredményeinek visszaadása a letiltott webhelyek, adjon meg egy lekérdezést az előnézeti ablaktáblában láthatja a jobb oldalon. 

## <a name="add-pinned-entries"></a>Rögzített bejegyzés hozzáadása

Egy adott weboldal a keresési eredmények tetejére rögzít, adja hozzá a weblapon és a lekérdezési kifejezés a **Pinned** fülre. A **Pinned** lapon adja meg a weblap, egy adott lekérdezésre vonatkozó felső eredményként megjelenő weblap és a lekérdezési kifejezés párok listáját tartalmazza. A weblap rögzítve van, csak akkor, ha a felhasználó lekérdezési karakterlánc megfelel a PIN-kód lekérdezési karakterlánc PIN kód egyezés feltétel alapján. [További információk](../define-your-custom-view.md#pin-to-top).

1. Az a **konfigurációs** lap, kattintson a **Pinned** lapra, és adja meg a weblap, amelyen a felső eredményezi lekérdezni kívánt weblap és a lekérdezési időszak.  
  
2. Alapértelmezés szerint a felhasználói lekérdezési karakterlánc pontosan meg kell egyeznie a PIN-kód lekérdezési karakterláncot a Bing, a képernyőn látható weblapon visszaadandó felső eredményezi. Az egyezési feltétellel módosításához szerkessze a PIN-kód (kattintson a ceruza ikonra), kattintson a Exact a **lekérdezés az egyezési feltétellel** oszlopra, és válassza a megfelelő az alkalmazás az egyezési feltétellel.  
  
    ![Képernyőkép a definíció szerkesztő rögzített lap](../media/pinnedCustomSrch.png)
  
3. Győződjön meg arról, hogy ha a példány a megadott weblapra adja vissza a felső eredményezi, adja meg a lekérdezési kifejezés rögzítette az előnézeti ablaktáblában láthatja a jobb oldalon.

## <a name="configure-hosted-ui"></a>Központi felhasználói felület konfigurálása

Egyéni keresési renderelni a JSON-válasz az egyéni keresési példány üzemeltetett felhasználói Felületet biztosít. A felhasználói felület meghatározása:

1. Kattintson a **üzemeltetett felhasználói felület** fülre.  
  
2. Válasszon egy elrendezést.  
  
  ![A tárolt felhasználói felület képernyőképe válassza ki az elrendezés lépés](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Válassza ki a színtémát.  
  
  ![A tárolt felhasználói felület képernyőképe színtémát kiválasztása](./media/custom-search-hosted-ui-select-color-theme.png)  

  Ha szeretné jobban integrálhatja a webalkalmazással, kattintson a színtémát finomhangolása **testreszabás téma**. Nem minden szín hatással van minden elrendezés téma. Ha módosítani szeretné egy színt, a megfelelő mezőben adja meg a színt HEXADECIMÁLIS RGB-értéke (például #366eb8). Vagy, a color gombra, majd a shade az Önnek legmegfelelőbb. Mindig úgy gondolja, hogy kisegítő lehetőségek a színek kiválasztásánál.
  
  ![A tárolt felhasználói felület képernyőképe színtémát testreszabása](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Adja meg a további konfigurációs beállításokat.  
  
  ![A felhasználói felület üzemeltetett további konfigurációs lépés képernyőképe](./media/custom-search-hosted-ui-additional-configurations.png)  
  
  Speciális konfigurációk, kattintson a **speciális konfigurációk megjelenítése**. Ezzel hozzáad konfigurációk például *hivatkozáscéllal* a webes keresési beállítások, *szűrők engedélyezése* képi és a beállítások, és *keresési szöveg helyőrző* a vegyes beállítások.

  ![A felhasználói felület üzemeltetett speciális konfigurációk lépés képernyőképe](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. A legördülő listákból válassza ki az előfizetési kulcsok. Vagy megadhatja manuálisan az előfizetési kulcsot. További információ a kulcsainak beolvasásakor: [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
  ![A felhasználói felület üzemeltetett további konfigurációs lépés képernyőképe](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Felhasználó központi felhasználói felületen

Két módon felhasználását a központi felhasználói felületen.  

- 1. lehetőség: A megadott JavaScript-kódrészletet beágyazása az alkalmazásba.
- 2. lehetőség: A HTML-végpontot használja, a megadott.

Ez az oktatóanyag további részében látható **1. lehetőség: Javascript-kódrészletet**.  

## <a name="set-up-your-visual-studio-solution"></a>A Visual Studio megoldás beállítása

1. Nyissa meg a **Visual Studiót** a számítógépén.  
  
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.  
  
3. Az a **új projekt** ablakban válassza **Visual C# vagy webes és az ASP.NET Core-webalkalmazás**, nevezze el a projektet, és kattintson **OK**.  
  
  ![Képernyőkép az új projekt ablakról](./media/custom-search-new-project.png)  
  
4. Az a **új ASP.NET Core-webalkalmazás** ablakban válassza **webalkalmazás** kattintson **OK**.  
  
  ![Képernyőkép az új projekt ablakról](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>Index.cshtml szerkesztése

1. Az a **Megoldáskezelőben**, bontsa ki a **oldalak** , és kattintson duplán a **index.cshtml** megnyitni a fájlt.  
  
  ![A megoldáskezelőben kibontva oldalak és a kiválasztott index.cshtml képernyőképe](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. Index.cshtml törölje minden alatt és a 7. sorban.  
  
  ```razor
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }    
  ```  
  
3. Adjon hozzá egy sor/csere típusú elem és a egy div tárolóként.  
  
  ```html
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }
  <br />
  <div id="customSearch"></div>
  ```  
  
4. Az a **üzemeltetett felhasználói felület** lapon görgessen le a részre **használ a felhasználói felület**. Kattintson a *végpontok* eléréséhez a JavaScript-kódrészletet. Akkor is elérhető a kódrészlet kattintva **éles** , majd a **üzemeltetett felhasználói felület** lapon.
  
  <!-- Get new screenshot after prod gets new bits
  ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
  -->
  
5. Illessze be a parancsfájl elem a tároló adott hozzá.  
  
  ``` html
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }
  <br />
  <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
  </div>
  ```  
  
6. Az a **Megoldáskezelőben**, kattintson a jobb gombbal **wwwroot** kattintson **Megtekintés böngészőben**.  
  
  ![Képernyőkép a nézet kijelölése a böngészőben a wwwroot helyi menüből a megoldáskezelőben](./media/custom-search-webapp-view-in-browser.png)  

Az új egyéni keresési weblap ehhez hasonlóan kell kinéznie:

![Egyéni keresési webes oldalát bemutató képernyőkép](./media/custom-search-webapp-browse-index.png)

Keresés végrehajtása ehhez hasonló eredmény jelenik meg:

![Képernyőfelvétel a egyéni keresési eredmények](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hívást a Bing Custom Search végpont (C#)](../call-endpoint-csharp.md)
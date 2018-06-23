---
title: 'Bing egyéni keresés: Egy egyéni keresési weblap |} Microsoft Docs'
description: Ismerteti, hogyan lehet konfigurálni egy egyéni keresési példányt, és integrálja azt egy weblap
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: c1431ec852cab943e00d3933ef4f0500a4fdb151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347882"
---
# <a name="build-a-custom-search-web-page"></a>Egyéni keresés weblap létrehozása
Bing egyéni keresés szabott keresési lép témakörök, amelyek az Ön számára legfontosabb létrehozását teszi lehetővé. Például ha Ön a tulajdonosa a Harcművészet webhelyet, ahol a keresési élményt biztosít, megadhatja a tartományok, alwebhelyek és a Bing keres a weblapokat. A tartalom helyett általános keresési eredmények között, előfordulhat, hogy irreleváns tartalmat tartalmazó lapozást érdeklő igazított találatok jelennek meg a felhasználók. 

Ez az oktatóanyag bemutatja, hogyan kell konfigurálni egy egyéni keresési példányt, és integrálnia kell az új weblap.

Tartozó feladatok a következők:

> [!div class="checklist"]
> - Egy egyéni keresési példányának létrehozása
> - Az aktív hozzáadása
> - Letiltott bejegyzés hozzáadása
> - Rögzített bejegyzés hozzáadása
> - Egyéni keresés integrálja a weblap

## <a name="prerequisites"></a>Előfeltételek
- Kövesse az oktatóanyag együtt, rendelkeznie kell egy előfizetés kulcs vonatkozó egyéni a Bing keresési API.  Ahhoz, hogy a kulcs, lásd: [kognitív szolgáltatások próbálja](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t.

## <a name="create-a-custom-search-instance"></a>Egy egyéni keresési példányának létrehozása
Bing egyéni keresés példány létrehozása:

1.  Nyisson meg egy böngészőprogramot.
2.  Keresse meg az egyéni keresési [portal](https://customsearch.ai).
3.  Jelentkezzen be a portálra a Microsoft-fiókkal (msa-t). Ha még nem rendelkezik az msa-t, kattintson a **Microsoft-fiók létrehozása**. Ha most először a portál használatával, a program kéri a engedélyekkel az adatok eléréséhez. Kattintson a **Yes** (Igen) gombra.
4.  Kattintson a bejelentkezést követően a **új egyéni keresési**. Az a **hozzon létre egy új egyéni keresési példányát** ablak, adjon meg egy nevet, kifejező, és ismerteti a tartalomtípushoz, a keresés adja vissza. Bármikor módosíthatja a nevét.
 
    ![Hozzon létre egy új egyéni példány keresőmezőbe képernyőfelvétele](../media/newCustomSrch.png)

5.  Kattintson az OK gombra, adja meg egy URL-címet, valamint hogy tartalmazzák a kiinduló lap mindegyik lapot tünteti:

    ![Képernyőfelvétel az URL-cím definíciója lap](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Az aktív hozzáadása
Az egyes helyekre vagy URL-címek eredmények belefoglalása, vegye fel őket a **aktív** fülre.

1.  Az a **Definition szerkesztő**, kattintson a **aktív** fülre, és adja meg a keresés szerepeltetni kívánt egy vagy több hely URL-CÍMÉT.

    ![A definíció szerkesztő aktív lap](../media/customSrchEditor.png)

2.  Győződjön meg arról, hogy a példány eredményeket ad vissza, adjon meg egy lekérdezést a betekintő ablaktáblában kattintson a jobb. Bing csak a nyilvános helyeken, akkor rendelkezik indexelt eredményeket ad vissza.

## <a name="add-blocked-entries"></a>Letiltott bejegyzés hozzáadása
Kizárni az eredményeket a meghatározott webhelyeinek vagy URL-címek, vegye fel őket a **zárolva** fülre.

1. Az a **Definition szerkesztő**, kattintson a **zárolva** fülre, és adja meg a Keresés a kizárni kívánt egy vagy több hely URL-CÍMÉT.

    ![Képernyőfelvétel a definíció szerkesztő blokkolva lap](../media/blockedCustomSrch.png)


2. Győződjön meg arról, hogy a példány nem eredmények visszaadásának a letiltott webhelyek, adjon meg egy lekérdezést a betekintő ablaktáblában kattintson a jobb. 

## <a name="add-pinned-entries"></a>Rögzített bejegyzés hozzáadása
PIN-kód a keresési eredmények elejére egy adott weboldal, vegye fel a weblapok és a lekérdezési kifejezés a **Pinned** fülre.  A **Pinned** lapon adja meg a képernyőn látható weblapon egy adott lekérdezés felső eredményeként megjelenő weblapok és a lekérdezési kifejezés értékpárok listáját tartalmazza. A felhasználó lekérdezés kifejezés pontosan egyeznie kell a weboldal a felső rögzítve rögzített lekérdezési kifejezés.

1. Az a **Definition szerkesztő**, kattintson a **Pinned** fülre, és adjon meg a weblap és lekérdezés feltételt, a képernyőn látható weblapon, amely a felső eredményként kell visszaadnia.

    ![Képernyőfelvétel a definíció szerkesztő rögzített lap](../media/pinnedCustomSrch.png)

2. Győződjön meg arról, hogy ha a példány a megadott weblap adja vissza a felső eredményezi, adja meg a lekérdezési kifejezés rögzített az előnézeti ablaktáblában láthatja a jobb oldali.

## <a name="configure-hosted-ui"></a>Központi felhasználói felület konfigurálása
Egyéni keresési a JSON-választ a egyéni keresési példány leképezése központi felhasználói Felületet biztosít.  A felhasználói felületi élmény meghatározása:

1. Kattintson a **tárolt felhasználói felület** fülre.
2. Olyan elrendezést választ.

    ![Képernyőfelvétel a tárolt felhasználói felület elrendezés lépés kiválasztása](./media/custom-search-hosted-ui-select-layout.png)

3. Válassza ki a stílust.

    ![Képernyőfelvétel a tárolt felhasználói felület elrendezés lépés kiválasztása](./media/custom-search-hosted-ui-select-color-theme.png)

4. Adja meg a további konfigurációs beállításait.

    ![Képernyőfelvétel a tárolt felhasználói felület további konfigurációs lépés](./media/custom-search-hosted-ui-additional-configurations.png)

5. Beillesztés a **előfizetés kulcs**. Lásd: [kognitív-szolgáltatás kipróbálására](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Képernyőfelvétel a tárolt felhasználói felület további konfigurációs lépés](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Felhasználó központi felhasználói felületen
Kétféleképpen felhasználását a központi felhasználói felületen.  

- 1. lehetőség: A megadott JavaScript-részlet integrálja az alkalmazást.
- 2. lehetőség: Használja a HTML-végpontot, feltéve.

A hátralévő részét ez az oktatóanyag bemutatja, **1. lehetőség: Javascript részlet**.  

## <a name="set-up-your-visual-studio-solution"></a>A Visual Studio megoldás beállítása
1. Nyissa meg a **Visual Studiót** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. Az a **új projekt** ablakban válassza ki **Visual C# / webes / ASP.NET Core webalkalmazás**, nevezze el a projektet, és kattintson a **OK**.
   
    ![Képernyőfelvétel az új projekt ablakról](./media/custom-search-new-project.png)

4. Az a **új ASP.NET Core webalkalmazás** ablakban válassza ki **webalkalmazás** kattintson **OK**.
    
    ![Képernyőfelvétel az új projekt ablakról](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Index.cshtml szerkesztése
1. A a **Megoldáskezelőben**, bontsa ki a **lapok** duplán **index.cshtml** megnyitni a fájlt.

    ![Bővített lapok és a kiválasztott index.cshtml megoldáskezelő képernyőfelvétele](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. A index.cshtml törölje a sor 7 és az alacsonyabb mindent.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Adjon hozzá egy sor break és a tárolók nevében járhasson el egy div.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. Az a **tárolt felhasználói felület** lapon görgessen le a című **fel a felhasználói felület**. Másolja a JavaScript részlet.

    ![Képernyőfelvétel a tárolt felhasználói felületén a Mentés gombra](./media/custom-search-hosted-ui-consuming-ui.png)

5. A parancsfájl elem illessze be a tárolóhoz, hozzáadott.

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
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. Az a **Megoldáskezelőben**, kattintson a jobb gombbal **wwwroot** kattintson **böngészőben nézet**.

    ![Nézet kijelölése a böngészőben a helyi menüből wwwroot megoldáskezelő képernyőfelvétele](./media/custom-search-webapp-view-in-browser.png)

Új egyéni keresési weblap ehhez hasonlóan kell kinéznie:

![Képernyőfelvétel az egyéni keresési weblap](./media/custom-search-webapp-browse-index.png)

Keresés végrehajtása ehhez hasonló eredményeket jeleníti meg.

![Képernyőfelvétel az egyéni keresési eredmények](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> - Egy egyéni keresési példányt hozott létre
> - A hozzáadott aktív bejegyzések
> - Letiltott bejegyzések felvételekor
> - Rögzített bejegyzések felvételekor
> - Egyéni keresési integrálva weblap

Most már folytathatja a Bing egyéni keresés végpontok programozott módon hívja.

> [!div class="nextstepaction"]
> [Hívás Bing egyéni keresés végpont (C#)](../call-endpoint-csharp.md)
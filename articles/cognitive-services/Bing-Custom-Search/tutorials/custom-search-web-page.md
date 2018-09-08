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
ms.openlocfilehash: 1f9b689ac6127bc2f7d1e810356ae9a23b8e0996
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162393"
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

1.  Nyisson meg egy webböngészőt.
2.  Keresse meg az egyéni keresési [portál](https://customsearch.ai).
3.  Jelentkezzen be Microsoft-fiókkal (MSA) a portálon. Ha az MSA nem rendelkezik, kattintson a **Microsoft-fiók létrehozásához**. Ha most először a portál használatával, kérni fogja a engedélyekkel az adatok eléréséhez. Kattintson a **Yes** (Igen) gombra.
4.  Miután bejelentkezett, kattintson a **új egyéni keresés**. Az a **hozzon létre egy új egyéni keresési példány** ablakban adjon meg egy nevet, amely jelentéssel bíró, és ismerteti a visszaadott tartalom típusát. A név bármikor módosíthatja.
 
    ![Képernyőfelvétel a hozzon létre egy új egyéni keresési példány használata](../media/newCustomSrch.png)

5.  Kattintson az OK gombra, adja meg egy URL-címet, valamint a kiinduló lap mindegyik lapot tünteti tartalmazza:

    ![Képernyőfelvétel a definíció lap URL-címe](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Aktív bejegyzés hozzáadása
Az egyes helyekre vagy URL-címek származó eredmények jelenjenek meg, adja hozzá őket a **Active** fülre.

1.  Az a **definíció szerkesztő**, kattintson a **aktív** lapra, és adjon meg egy vagy több hely fel szeretne venni a Keresés URL-CÍMÉT.

    ![A definíció szerkesztő aktív lap](../media/customSrchEditor.png)

2.  Győződjön meg arról, hogy a példány eredményeket ad vissza, adjon meg egy lekérdezést az előnézeti ablaktáblában láthatja a jobb oldalon. Bing – csak a nyilvános helyeken, rendelkezik indexelt eredményeket ad vissza.

## <a name="add-blocked-entries"></a>Letiltott bejegyzés hozzáadása
Szeretné kizárni az eredményeket az egyes helyekre vagy URL-címek, adja hozzá őket a **letiltott** fülre.

1. Az a **definíció szerkesztő**, kattintson a **letiltott** lapra, és adja meg a Keresés a kizárni kívánt egy vagy több hely URL-CÍMÉT.

    ![Képernyőfelvétel a definíció szerkesztő blokkolt lap](../media/blockedCustomSrch.png)


2. Győződjön meg arról, hogy a példány nem eredményeinek visszaadása a letiltott webhelyek, adja meg egy lekérdezést az előnézeti ablaktáblában láthatja a jobb oldalon. 

## <a name="add-pinned-entries"></a>Rögzített bejegyzés hozzáadása
Egy adott weboldal a keresési eredmények tetejére rögzít, adja hozzá a weblapon és a lekérdezési kifejezés a **Pinned** fülre.  A **Pinned** lapon adja meg a weblap, egy adott lekérdezésre vonatkozó felső eredményként megjelenő weblap és a lekérdezési kifejezés párok listáját tartalmazza. A felhasználó keresési kifejezésre pontosan egyeznie kell a weboldal a felső rögzítve a gyors lekérdezési kifejezés.

1. Az a **definíció szerkesztő**, kattintson a **Pinned** lapra, és adja meg a weblapot, és a lekérdezési időszak végéig a weblap, amelyen a felső eredményezi kell visszaadnia.

    ![Képernyőfelvétel a definíció szerkesztő rögzített lap](../media/pinnedCustomSrch.png)

2. Győződjön meg arról, hogy ha a példány a megadott weblapra adja vissza a felső eredményezi, adja meg a lekérdezési kifejezés rögzítette az előnézeti ablaktáblában láthatja a jobb oldalon.

## <a name="configure-hosted-ui"></a>Központi felhasználói felület konfigurálása
Egyéni keresési renderelni a JSON-válasz az egyéni keresési példány üzemeltetett felhasználói Felületet biztosít.  A felhasználói felület meghatározása:

1. Kattintson a **üzemeltetett felhasználói felület** fülre.
2. Válasszon egy elrendezést.

    ![Képernyőfelvétel a tárolt felhasználói felületén válassza ki a elrendezés lépés](./media/custom-search-hosted-ui-select-layout.png)

3. Válassza ki a színtémát.

    ![Képernyőfelvétel a tárolt felhasználói felületén válassza ki a elrendezés lépés](./media/custom-search-hosted-ui-select-color-theme.png)

4. Adja meg a további konfigurációs beállításokat.

    ![Képernyőfelvétel a felhasználói felület üzemeltetett további konfigurációs lépés](./media/custom-search-hosted-ui-additional-configurations.png)

5. Illessze be a **előfizetési kulcs**. Lásd: [a Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Képernyőfelvétel a felhasználói felület üzemeltetett további konfigurációs lépés](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

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
   
    ![Képernyőfelvétel az új projekt ablakról](./media/custom-search-new-project.png)

4. Az a **új ASP.NET Core-webalkalmazás** ablakban válassza **webalkalmazás** kattintson **OK**.
    
    ![Képernyőfelvétel az új projekt ablakról](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Index.cshtml szerkesztése
1. Az a **Megoldáskezelőben**, bontsa ki a **oldalak** , és kattintson duplán a **index.cshtml** megnyitni a fájlt.

    ![Képernyőfelvétel a megoldáskezelőben kibontva oldalak és a kiválasztott index.cshtml](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. Index.cshtml törölje minden alatt és a 7. sorban.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Adjon hozzá egy sor/csere típusú elem és a egy div tárolóként.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. Az a **üzemeltetett felhasználói felület** lapon görgessen le a részre **használ a felhasználói felület**. Másolja a JavaScript-kódrészletet.

    ![Képernyőfelvétel a tárolt felhasználói felület a Mentés gomb](./media/custom-search-hosted-ui-consuming-ui.png)

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
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. Az a **Megoldáskezelőben**, kattintson a jobb gombbal **wwwroot** kattintson **Megtekintés böngészőben**.

    ![Képernyőfelvétel a nézet kijelölése a böngészőben a wwwroot helyi menüből a megoldáskezelőben](./media/custom-search-webapp-view-in-browser.png)

Az új egyéni keresési weblap ehhez hasonlóan kell kinéznie:

![Képernyőfelvétel a weblap egyéni keresés](./media/custom-search-webapp-browse-index.png)

Keresés végrehajtása ehhez hasonló eredményeket jeleníti meg.

![Képernyőfelvétel a egyéni keresési eredmények](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> - Létrehozott egy egyéni keresési példány
> - A hozzáadott aktív bejegyzések
> - Új blokkolt bejegyzések felvételekor
> - A hozzáadott rögzített bejegyzés
> - Egyéni keresési integrálva weblap

Most már folytathatja a Bing Custom Search-végpontok hívása programozott módon.

> [!div class="nextstepaction"]
> [Hívást a Bing Custom Search végpont (C#)](../call-endpoint-csharp.md)
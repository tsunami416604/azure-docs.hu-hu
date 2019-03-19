---
title: 'Oktatóanyag: Egy weblap egyéni keresés – Bing egyéni keresés'
titlesuffix: Azure Cognitive Services
description: Ez a cikk ismerteti, hogyan konfigurálható és integrálható egy weboldalba egy Custom Search-példány.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6f44d4e6e5a0ee940394e09ceff8a425700d34e1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100734"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Oktatóanyag: Custom Search-webhely létrehozása

A Bing Custom Search szolgáltatással személyre szabott keresési funkciókat hozhat létre olyan témakörökhöz kapcsolódóan, amelyek jelentőséggel bírnak az Ön számára. Ha például egy keresési funkciót is biztosító, küzdősportokról szóló webhellyel rendelkezik, megadhatja azokat a tartományokat, alwebhelyeket és weblapokat, amelyeken a Bingnek keresnie kell. A felhasználók az érdeklődési körükhöz igazított keresési eredményeket látnak ahelyett, hogy több oldalnyi általános találatot kellene átnézniük, amelyben irreleváns tartalom is lehet. 

Az oktatóanyag bemutatja, hogyan állíthat be egy Custom Search-példányt, és hogyan integrálhatja egy új weboldalba.

Az oktatóanyag az alábbi feladatokat tárgyalja:

> [!div class="checklist"]
> - Egyéni keresési példány létrehozása
> - Aktív bejegyzések hozzáadása
> - Blokkolt bejegyzések hozzáadása
> - Rögzített bejegyzések hozzáadása
> - Egyéni keresés integrálása egy weboldalba

## <a name="prerequisites"></a>Előfeltételek

- Ahhoz, hogy követni tudja az oktatóanyagot, szüksége lesz egy előfizetői azonosítóra a Bing Custom Search API-hoz.  Az előfizetői azonosító beszerzéséhez lásd [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) témakört.
- Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t.

## <a name="create-a-custom-search-instance"></a>Egyéni keresési példány létrehozása

Bing Custom Search-példány létrehozása:

1. Nyisson meg egy internetböngészőt.  
  
2. Navigáljon a Custom Search [portálra](https://customsearch.ai).  
  
3. Egy Microsoft-fiók (MSA) használatával jelentkezzen be a portálra. Ha nincs MSA-fiókja, kattintson a **Microsoft-fiók létrehozása** elemre. A portál az első használat során engedélyeket fog kérni, hogy hozzáférhessen az adataihoz. Kattintson a **Yes** (Igen) gombra.  
  
4. A bejelentkezés után kattintson a **New custom search** (Új egyéni keresés) elemre. A **Create a new custom search instance** (Új Custom Search-példány létrehozása) ablakban adjon meg egy jelentéssel bíró nevet, amely leírja a visszaadott tartalom típusát. A nevet bármikor módosíthatja.  
  
   ![A Create a new custom search (Új Custom Search-példány) mező képernyőképe](../media/newCustomSrch.png)  
  
5. Kattintson az OK gombra, adja meg az URL-címet, és hogy az URL aloldalait is figyelembe kívánja-e venni.  
  
   ![Az URL-meghatározó oldal képernyőképe](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Aktív bejegyzések hozzáadása

Ha adott webhelyekről vagy URL-címekről származó eredményeket szeretne figyelembe venni, adja hozzá a címeket az **Active** (Aktív) laphoz.

1.  A **Configuration** (Konfiguráció) oldalon kattintson az **Active** (Aktív) lapra, és adja meg a keresésbe belefoglalni kívánt egy vagy több webhely URL-címét.

    ![A Definíciószerkesztő aktív lapjának képernyőképe](../media/customSrchEditor.png)

2.  Ha meg kíván győződni arról, hogy a példány visszaad eredményeket, akkor adjon meg egy lekérdezést a jobb oldalon található előnézet panelen. A Bing csak az általa indexelt nyilvános webhelyekről jelenít meg eredményeket.

## <a name="add-blocked-entries"></a>Blokkolt bejegyzések hozzáadása

Ha szeretné kizárni egyes webhelyek vagy URL-címek eredményeit, adja hozzá a címeket a **Blocked** (Letiltott) laphoz.

1. A **Configuration** (Konfiguráció) oldalon kattintson a **Blocked** (Letiltva) lapra, és adja meg a keresésből kihagyni kívánt egy vagy több webhely URL-címét.

    ![A Definíciószerkesztő letiltva lapjának képernyőképe](../media/blockedCustomSrch.png)


2. Ha meg kíván győződni arról, hogy a példány nem ad vissza eredményeket a blokkolt webhelyekről, akkor adjon meg egy lekérdezést a jobb oldalon található előnézet panelen. 

## <a name="add-pinned-entries"></a>Rögzített bejegyzések hozzáadása

Ha egy adott webhelyet rögzíteni kíván a keresési találatok első helyén, adja hozzá a webhelyet és a lekérdezési kifejezést a **Pinned** (Rögzített) laphoz. A **Pinned** (Rögzített) lap a webhelyek és lekérdezési kifejezések olyan párosainak listáját tartalmazza, amely meghatározza, hogy egy adott lekérdezésnél melyik webhely jelenjen meg első eredményként. A webhely csak akkor rögzített, ha a felhasználó lekérdezési sztringje egyezik a rögzített elem egyezési állapotának rögzített lekérdezési sztringjével. Csak az indexelt webhelyek jelennek meg a keresésekben. További információkért lásd az [egyéni nézet meghatározásával](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results) foglalkozó témakört.

1. A **Configuration** (Konfiguráció) lapon kattintson a **Pinned** (Rögzített) lapra, és adja meg az első helyen megjeleníteni kívánt weblapot és a hozzá tartozó lekérdezési kifejezést.  
  
2. Ahhoz, hogy a Bing az első helyen jelenítse meg a webhelyet alapértelmezés szerint a felhasználói lekérdezési sztringnek pontosan egyeznie kell a rögzített elem lekérdezési sztringjével. Az egyezési feltétel módosításához szerkessze a rögzített elemet (kattintson a ceruza ikonra), a **Query match condition** (Lekérdezés egyezési feltétele) oszlopban kattintson az Exact (Pontos) elemre, és válassza ki az alkalmazása számára megfelelő egyezési állapotot.  
  
    ![A Definíciószerkesztő rögzített lapjának képernyőképe](../media/pinnedCustomSrch.png)
  
3. Ha meg szeretne győződni arról, hogy a példány a megadott weblapot adja vissza az első helyen, a jobb oldalon található előnézet panelen adja meg a rögzített lekérdezési kifejezést.

## <a name="configure-hosted-ui"></a>Üzemeltetett felhasználói felületi konfigurálása

A Custom Search üzemeltetett felhasználói felületet biztosít, amelyen megjelenítheti a Custom Search-példány JSON-válaszát. A felhasználói felület testreszabása:

1. Kattintson a **Hosted UI** (Üzemeltetett felhasználói felület) lapra.  
  
2. Válasszon ki egy elrendezést.  
  
   ![A Hosted UI (Üzemeltetett felhasználói felület) elrendezéskiválasztó lépésének képernyőképe](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Válasszon ki egy színtémát.  
  
   ![A Hosted UI (Üzemeltetett felhasználói felület) színtémaválasztásának képernyőképe](./media/custom-search-hosted-ui-select-color-theme.png)  

   Ha a webalkalmazással való jobb integráció érdekében szeretné még részletesebben beállítani a színtémát, kattintson a **Customize theme** (Téma testreszabása) elemre. Nem minden színbeállítás alkalmazható minden elrendezési témára. Ha módosítani szeretné egy színt, a megfelelő mezőben adja meg a szín HEXADECIMÁLIS RGB-értékét (például #366eb8). Vagy kattintson a szín gombra, majd a megfelelő árnyalatra. A színek kiválasztásánál mindig gondoljon a kisegítő lehetőségekre.
  
   ![Képernyőkép az üzemeltetett felhasználói felület színtéma-testreszabásáról](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Adja meg a további beállítási lehetőségeket.  
  
   ![Képernyőkép az üzemeltetett felhasználói felület további beállítási lépéséről](./media/custom-search-hosted-ui-additional-configurations.png)  
  
   A speciális konfigurációkat a **Show advanced configurations** (Speciális konfigurációk megjelenítése) elemre kattintva érheti el. Ezzel olyan beállításokat adhat hozzá, mint például a *Link target* (Hivatkozási cím) a webkeresési beállításokhoz, az *Enable filters* (Szűrők engedélyezése) a kép- és videobeállításokhoz, valamint a *Search box text placeholder* (Keresőmező szöveges helyőrzője) az egyéb beállításokhoz.

   ![Képernyőkép az üzemeltetett felhasználói felület speciális beállítási lépéséről](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Válassza ki előfizetési kulcsait a legördülő listákból. Másik lehetőségként manuálisan is megadhatja az előfizetési kulcsot. További információ az előfizetői kulcsok beszerzéséről: [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
   ![Képernyőkép az üzemeltetett felhasználói felület további beállítási lépéséről](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Üzemeltetett felhasználói felületi felhasználása

Az üzemeltetett felhasználói felület kétféleképpen használható fel.  

- Option 1: A megadott JavaScript-kódrészletet beágyazása az alkalmazásba.
- Option 2: A megadott, használja a HTML-végpontot.

Ez az oktatóanyag további részében látható **1. lehetőség: JavaScript-kódrészletet**.  

## <a name="set-up-your-visual-studio-solution"></a>A Visual Studio-megoldás beállítása

1. Nyissa meg a **Visual Studiót** a számítógépén.  
  
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.  
  
3. A **New Project** (Új projekt) párbeszédpanelen válassza a **Visual C# / Web / ASP.NET Core Web Application** (Visual C#- / Web / ASP.NET Core-webalkalmazás) elemet, nevezze el a projektet, majd kattintson az **OK** gombra.  
  
   ![Az új projekt ablak képernyőképe](./media/custom-search-new-project.png)  
  
4. A **New ASP.NET Core Web Application** (Új ASP.NET Core-webalkalmazás) ablakban válassza a **Web Application** (Webalkalmazás) lehetőséget, majd kattintson az **OK** gombra.  
  
   ![Az új projekt ablak képernyőképe](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>Az index.cshtml szerkesztése

1. A **Megoldáskezelőben** bontsa ki a **Pages** (Oldalak) elemet, és kattintson duplán az **index.cshtml** fájlra a megnyitásához.  
  
   ![Képernyőkép a megoldáskezelőről kibontott oldalakkal és a kiválasztott index.cshtml fájllal](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. Az index.cshtml fájlban töröljön mindent a 7. sortól kezdve.  
  
   ```razor
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }    
   ```  
  
3. Adjon hozzá egy sörtörés elemet és egy tárolóként működő div elemet.  
  
   ```html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch"></div>
   ```  
  
4. A **Hosted UI** (Üzemeltetett felhasználói felület) oldalon görgessen le a **Consuming the UI** (Felhasználói felület felhasználása) szakaszhoz. A JavaScript-kódrészlet eléréséhez kattintson az *Endpoints* (Végpontok) elemre. A kódrészletet úgy is elérheti, ha először a **Production** (Termelés), majd a **Hosted UI** (Üzemeltetett felhasználói felület) lapra kattint.
  
   <!-- Get new screenshot after prod gets new bits
   ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
   -->
  
5. A szkriptrészletet illessze be a hozzáadott tárolóba.  
  
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
  
6. A **Megoldáskezelőben** kattintson a jobb gombbal a **wwwroot** elemre, majd a **View in Browser** (Megtekintés böngészőben) parancsra.  
  
   ![Képernyőkép a megoldáskezelőről, a wwwroot helyi menü View in Browser (Megtekintés böngészőben) lehetőségének kiválasztásával](./media/custom-search-webapp-view-in-browser.png)  

Az új Custom Search-weboldal a következőhöz hasonlóan néz ki:

![Képernyőkép a Custom Search-weboldalról](./media/custom-search-webapp-browse-index.png)

A keresés végrehajtásakor a következőkhöz hasonló eredmények jelennek meg:

![Képernyőkép az egyéni keresés eredményeiről](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing Custom Search-végpont hívása (C#)](../call-endpoint-csharp.md)

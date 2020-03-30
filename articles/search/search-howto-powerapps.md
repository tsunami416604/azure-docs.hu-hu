---
title: Az Azure Cognitive Search lekérdezése a Power Apps alkalmazásból
titleSuffix: Azure Cognitive Search
description: Lépésről lépésre bemutatja, hogyan hozhat létre egyéni összekötőt a Cognitive Search szolgáltatáshoz, és hogyan jelenítheti meg azt egy Power Appból
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385112"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Kognitív keresési index lekérdezése a Power Apps alkalmazásból

Ez a dokumentum bemutatja, hogyan hozhat létre egyéni Power Apps-összekötőt, hogy keresési eredményeket olvashasson le a keresési indexből. Azt is bemutatja, hogyan adhat ki keresési lekérdezést, és hogyan jelenítheti meg az eredményeket egy Power Appból. 

## <a name="prerequisites"></a>Előfeltételek
*    A Power Apps-fiókhoz való hozzáférés egyéni összekötők létrehozásának lehetőségével.
*    Feltételezzük, hogy már létrehozott egy Azure Search Indexet.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Egyéni összekötő létrehozása az Azure Search lekérdezéséhez

Két fő lépés, hogy egy PowerApp, amely az Azure Cognitive Search eredményeit jeleníti meg. Először hozzon létre egy összekötőt, amely lekérdezheti a keresési indexet. A [következő szakaszban](#visualize-results-from-the-custom-connector) frissítjük a Power Apps-alkalmazást, hogy megjelenítsük az összekötő által visszaadott eredményeket.

1. Nyissa meg [a make.powerapps.com](http://make.powerapps.com) és **a Bejelentkezés**t.

1. **Adategyéni** > **összekötők** keresése
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Egyéni összekötő menü" border="true":::

1. Kattintson **a + Új egyéni összekötő** elemre, majd válassza a **Létrehozás üres ből**lehetőséget.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Létrehozás üres menüből" border="true":::

1. Adjon nevet az egyéni összekötőnek. (azaz *az AzureSearchQuery),* majd kattintson a **Folytatás gombra.** Ekkor megjelenik egy varázsló az új összekötő létrehozásához.

1. Adja meg az adatokat az általános oldalon.

    - Ikon háttérszíne (például #007ee5)
    - Leírás (például "Az Azure Cognitive Search összekötője")
    - A Gazdagépben meg kell adnia a keresési `<yourservicename>.search.windows.net`szolgáltatás URL-címét (például)
    - Az Alap URL-címhez egyszerűen írja be a "/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Általános tájékoztatási párbeszéd" border="true":::

1. A Biztonsági lapon állítsa be az *API-kulcsot* **hitelesítési típusként,** állítsa be a paramétercímkét, a paraméternév mezőket pedig *api-kulcsként.* A **Paraméter helye**területen válassza a *Fejléc* lehetőséget az alábbi módon.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Hitelesítéstípus beállítás" border="true":::

1. A Definíciók lapon válassza a **+ Új művelet** lehetőséget, ha olyan műveletet szeretne létrehozni, amely lekérdezi az indexet. Adja meg az összegzés "Lekérdezés" értékét és a műveletazonosító nevét. Írjon be egy olyan leírást, mint *"Lekérdezések a keresési index"*.
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Új cselekvési lehetőségek" border="true":::


1. A paraméterek és a fejlécek meghatározásához nyomja meg a **+ Importálás mintából** gombot. Ezután definiálja a lekérdezési kérelmet.  

    * Az ige kijelölése`GET`
    * Az URL-címhez adjon meg egy mintalekérdezést a keresési indexhez, például:
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=*&api-version=2019-05-06-Preview
    

    **A Power Apps** a szintaxissegítségével bontja ki a paramétereket a lekérdezésből. Figyelje meg, hogy kifejezetten meghatároztuk a keresési mezőt. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importálás mintából" border="false":::

1.  Kattintson **az Importálás gombra** a Kérés párbeszédpanel automatikus előzetes kitöltéséhez.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importálás mintapárbeszédből" border="false":::


1. A paraméter metaadatainak beállítása a **...** gombra kattintva az egyes paraméterek mellett.

    - *Keresés :* `*` Állítsa be **az alapértelmezett értéket**, állítsa be a **szükséges** *hamis* értéket, és állítsa a **láthatóságot** *nincs*értékre. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Keresési paraméter metaadatai" border="true":::

    - *Api-version*esetén: `2019-05-06-Preview` Állítsa be **az alapértelmezett értéket,** állítsa *True*a **láthatóságot** belsőként, és állítsa true **értékre.**  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Verzióparaméter metaadatai" border="true":::

    - Hasonlóképpen, az *api-key*, állítsa be, ha **szükséges**, *a belső* **láthatóság**. Adja meg a keresési szolgáltatás API-kulcsát **alapértelmezett értékként.**
    
    Miután elvégzette ezeket a módosításokat, váltson a **Swagger Szerkesztő** nézetre. A paraméterek szakaszban a következő konfigurációt kell látnia:    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. A Válasz csoportban kattintson az **"Alapértelmezett válasz hozzáadása"** gombra. Ez azért fontos, mert segít a **Power Apps-nek** megérteni a válasz sémáját. Mintaválasz beillesztése.

    > [!TIP] 
    > A Beírható JSON-válasznak van egy karakterkorlátja, ezért a beillesztés előtt egyszerűsítheti a JSON-t. A válasz fontos szempontja. A mintaválasz tényleges értékei kevésbé fontosak, és egyszerűsíthetők a karakterszám csökkentése érdekében.
    

1.    A tesztelés előtt kattintson az **összekötő létrehozása** gombra a képernyő jobb felső részén.

1.  A Teszt lapon kattintson a **+ Új kapcsolat gombra,** és adja meg a keresési szolgáltatás lekérdezési kulcsát az *api-kulcs*értékeként.

    Ezzel a lépéssel a varázslóból ki, majd a Kapcsolatok lapra léphet. Előfordulhat, hogy vissza szeretne menni az Egyéni kapcsolatok szerkesztőbe, hogy ténylegesen tesztelje a kapcsolatot. Ugrás **az Egyéni összekötő** > Válassza ki az újonnan létrehozott összekötő > *...* > **Tulajdonságok** > megtekintése**Szerkesztés** > **4. Tesztelje,** hogy visszatérjen a tesztoldalra.

1.    Most kattintson **a Teszt művelet** elemre, és győződjön meg arról, hogy az indexből kap eredményeket. Ha sikeres volt, 200-as állapotot kell látnia, és a válasz törzsében a JSON-t kell látnia, amely leírja a keresési eredményeket.




## <a name="visualize-results-from-the-custom-connector"></a>Az egyéni összekötő ből származó eredmények megjelenítése
A bemutató célja nem az, hogy megmutassa, hogyan hozhat létre puccos felhasználói élményt a power alkalmazásoksegítségével, így a felhasználói felület elrendezése minimalista lesz. Hozzunk létre egy PowerApp-ot keresőmezővel, egy keresőgombbal, és jelenítsük meg az eredményeket egy galériavezérlőben.  A PowerApp csatlakozik a nemrég létrehozott egyéni összekötőhöz, hogy levegye az adatokat az Azure Searchből.

1. Hozzon létre új Power Appot. Nyissa meg az **Alkalmazások szakaszt,** kattintson a **+ Új alkalmazás**elemre, és válassza a **Vászon**lehetőséget.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Vászon alkalmazás létrehozása" border="true":::

1. Válassza ki a kívánt alkalmazás típusát. Ehhez a bemutatóhoz hozzon létre egy **üres alkalmazást** a **telefon elrendezésével**. Megjelenik a **Power Apps Studio.**

1. Miután a stúdióban, válassza ki az **Adatforrások** fülre, és kattintson az új összekötő az imént létrehozott. A mi esetünkben, ez az úgynevezett *AzureSearchQuery*. Kattintson **a Kapcsolat hozzáadása gombra.**

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="összekötő csatlakoztatása" border="true":::

    Most *az AzureSearchQuery* egy olyan adatforrás, amely az alkalmazásból használható.
    
1. Keresse meg a **Beszúrás lapot,** hogy néhány vezérlőt is hozzáadhassunk az űrlapunkhoz.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Vezérlők beszúrása" border="true":::

1.  Szúrja be a következő elemeket:
    -   "Lekérdezés:" értékkel rendelkező szöveges címke
    -   Szövegbeviteli elem (nevezzük *txtQuery-nek*, alapértelmezett érték: "*")
    -   Egy gomb a "Keresés" szöveggel 
    -   A Függőleges Galéria nevű (nevezzük *galleryResults*)
    
    Az űrlapnak valahogy így kell kinéznie:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Vezérlők elrendezése" border="true":::

1. Ha azt szeretné, hogy a **Keresés gomb** lekérdezést adjon ki, jelölje ki a gombot, és illessze be a következő műveletet **az OnSelect művelethez:**

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Gomb a kijelöléshez" border="true":::
 
    Ez a művelet hatására a gomb egy *új, azResult* nevű gyűjteményt frissít a keresési lekérdezés eredményével, a *txtQuery* szövegmezőben lévő szöveget használva lekérdezési kifejezésként.
    
1.  Következő lépésként összekapcsoljuk az általunk létrehozott függőleges galériát az *azResult* gyűjteményével. Jelölje ki a gyűjteményvezérlőt, és hajtsa végre a következő műveleteket a tulajdonságok ablaktábláján.

    -  **Állítsa a DataSource-t** *azResult beállításra.*
    
    -  Válassza ki **azt az elrendezést,** amely az indexben lévő adatok típusa alapján működik. Ebben az esetben a *cím,* a felirat és a test elrendezését használtuk.
    
    -  **A Mezők szerkesztése**és a megjelenítendő mezők kiválasztása.

    Mivel az összekötő definiálásakor mintaeredményt adtunk meg, az alkalmazás tisztában van az indexben elérhető mezőkkel.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Katalógusmezők" border="true":::   
 
1.  Nyomja **le az F5 billentyűt** az alkalmazás előnézetének megtekintéséhez.  

    Ne feledje, hogy a mezők számított értékekre állíthatók be.      
    A példában a *"Kép, cím és felirat"* elrendezés t, valamint a *Kép* függvény tanmenetének összefűzéseként `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`az adatok és a fájlnév (például ) beállításával az alábbi eredményt adja.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="A kész alkalmazás" border="true":::        

## <a name="next-steps"></a>További lépések

További információ és online oktatás a [Power Apps tanulási katalógusában.](https://docs.microsoft.com/powerapps/learning-catalog/get-started)


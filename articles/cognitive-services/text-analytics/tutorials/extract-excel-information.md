---
title: Információk kinyerése az Excelben a Szövegelemzés és a Power Automate használatával
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan bonthatja ki az Excel szövegét kód írása nélkül a Szövegelemzés és a Power Automate használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201187"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Információk kinyerése az Excelben a Szövegelemzés és a Power Automate használatával 

Ebben az oktatóanyagban power automate folyamatot hozhat létre, amely kód írása nélkül nyeri ki az Excel-számolótábla szövegét. 

Ez az áramlás egy apartmankomplexumról jelentett problémák táblázatát veszi fel, és két kategóriába sorolja őket: vízvezeték és egyéb. Azt is kivonat nevét és telefonszámát a bérlők, akik küldték őket. Végül a folyamat hozzáfűzi ezt az információt az Excel-laphoz. 

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Folyamat létrehozása a Power Automate segítségével
> * Excel-adatok feltöltése a OneDrive Vállalati verzióból
> * Szöveg kinyerése az Excelből, és elküldés a Text Analytics API-ba 
> * Excel-lap frissítéséhez használja az API-ból származó információkat.

## <a name="prerequisites"></a>Előfeltételek

- Egy Microsoft Azure-fiók. [Kezdjen egy ingyenes próbaidőszakot](https://azure.microsoft.com/free/), vagy [jelentkezzen be](https://portal.azure.com/).
- Text Analytics-erőforrás. Ha nem rendelkezik ilyen, [létrehozhat egyet az Azure Portalon,](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) és az ingyenes szint használatával elvégezheti ezt az oktatóanyagot.
- A [kulcs és a végpont,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) amely a regisztráció során jött létre.
- Bérlői problémákat tartalmazó számolótábla. A példaadatok a GitHubon
- Office 365, a OneDrive Vállalati verzióval.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Az Excel-fájl felvétele a OneDrive Vállalati verzióba

Töltse le a példa Excel fájlt a [GitHubról.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx) Ezt a fájlt a OneDrive Vállalati verziós fiókban kell tárolni.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Példák az Excel-fájlból.":::

A problémák nyers szövegben vannak jelentve. A Text Analytics API megnevezett entitásfelismerését használjuk a személy nevének és telefonszámának kinyeréséhez. Ezután az áramlás a "vízvezeték" szót keresi a leírásban a problémák kategorizálásához. 

## <a name="create-a-new-power-automate-workflow"></a>Új Power Automate-munkafolyamat létrehozása

Nyissa meg a [Power Automate webhelyet,](https://preview.flow.microsoft.com/)és jelentkezzen be. Ezután kattintson **a Létrehozás** és **ütemezett folyamat gombra.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="A folyamat létrehozási képernyő.":::


Az **Ütemezett folyamat létrehozása** lapon inicializálja a folyamatot a következő mezőkkel:

|Mező |Érték  |
|---------|---------|
|**Folyamat neve**     | **Ütemezett ellenőrzés** vagy más név.         |
|**Kezdő**     |  Adja meg az aktuális dátumot és időpontot.       |
|**Minden ismétlés**     | **1 óra**        |

## <a name="add-variables-to-the-flow"></a>Változók hozzáadása a folyamathoz

> [!NOTE]
> Ha meg szeretné tekinteni a befejezett folyamat képét, letöltheti azt a [GitHubról.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams) 

Az Excel-fájlba kerülő adatokat jelölő változók létrehozása. Kattintson **az Új lépés gombra,** és keresse meg az **Inicializálás változót**. Ehhez négyszer, hozzon létre négy változót.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Változók inicializálása.":::

Adja hozzá a következő információkat a létrehozott változókhoz. Az Excel-fájl oszlopait jelölik. Ha a változók össze vannak csukva, rájuk kattintva kibonthatja őket.

| Műveletek |Név   | Típus | Érték |
|---------|---------|---|---|
| Változó inicializálása | var_person | Sztring | Személy |
| 2. változó inicializálása | var_phone | Sztring | Phone_Number |
| Változó inicializálása | var_plumbing | Sztring | Vízvezeték |
| 4. változó inicializálása | var_other | Sztring | egyéb | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="a folyamatváltozókban szereplő információk":::

## <a name="read-the-excel-file"></a>Az excel fájl olvasása

Kattintson **az Új lépés gombra,** és írja be az **Excel**parancsot, majd a műveletek listájából válassza a **táblázatban jelen lévő sorok listázása** lehetőséget.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="excel sorok hozzáadása.":::

Adja hozzá az Excel-fájlt a folyamathoz a művelet mezőinek kitöltésével. Ez az oktatóanyag megköveteli, hogy a fájlt feltöltötték a OneDrive Vállalati verzióra.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="excel sorok hozzáadása.":::

Kattintson **az Új lépés gombra,** és adjon hozzá **minden művelethez egy Kiegyenlítést.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="adjon hozzá egy apply parancsot.":::

Kattintson **a Kimenet kiválasztása az előző lépésből**elemre. A megjelenő Dinamikus tartalom mezőben válassza az **értéket.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Válassza ki a kimenetet az Excel-fájlból.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Kérelem küldése a Text Analytics API-nak

Ha még nem tette meg, létre kell hoznia egy [Text Analytics-erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) az Azure Portalon.

### <a name="create-a-text-analytics-connection"></a>Szövegelemzési kapcsolat létrehozása

Az **Alkalmazás az egyes re**területen kattintson a **Művelet hozzáadása gombra.** Nyissa meg a Text Analytics-erőforrás **kulcs- és végpontoldalát** az Azure Portalon, és lekéri a Szövegelemzési erőforrás kulcsát és végpontját.

A folyamat, adja meg a következő információkat, hogy hozzon létre egy új Text Analytics-kapcsolat.

> [!NOTE]
> Ha már létrehozott egy Text Analytics-kapcsolatot, és módosítani szeretné a kapcsolat részleteit, kattintson a jobb felső sarokban lévő három pontra, és kattintson az **+ Új kapcsolat hozzáadása**parancsra.

| Mező           | Érték                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Kapcsolat neve | A Szövegelemzési erőforráshoz való kapcsolat neve. Például: `TAforPowerAutomate`. |
| Fiókkulcs     | A Szövegelemzési erőforrás kulcsa.                                                                                   |
| Webhely URL-címe        | A szövegelemzési erőforrás végpontja.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

## <a name="extract-the-excel-content"></a>Az excel tartalmának kinyerése 

A kapcsolat létrehozása után keresse meg a **Text Analytics kifejezést,** és válassza **az Entitások**lehetőséget. Ez információkat nyer ki a probléma leírás oszlopából.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

Kattintson a **Szöveg** mezőre, és a megjelenő Dinamikus tartalom ablakokközött válassza a **Leírás** lehetőséget. Írja `en` be a nyelvet. (Kattintson a Speciális beállítások megjelenítése gombra, ha nem látja a Nyelvet)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::


## <a name="extract-the-person-name"></a>A személy nevének kibontása

Ezután megtaláljuk a személy entitás típusát a Text Analytics kimenetben. Az **Alkalmazás minden műveletre**területen kattintson a **Művelet hozzáadása**gombra, és hozzon létre egy másik **at minden műveletre.** Kattintson a szövegdobozba, és válassza az **Entitások** lehetőséget a megjelenő Dinamikus tartalom ablakban.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

Az újonnan létrehozott **Alkalmazás minden 2 műveletre** kattintson a **Művelet hozzáadása**gombra, és adjon hozzá egy **Feltétel** vezérlőt.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

A Feltétel ablakban kattintson az első szövegmezőre. A Dinamikus tartalom ablakban keresse meg az **Entitások típusa kifejezést,** és jelölje ki.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

Győződjön meg arról, hogy a második mező **beállítása megegyezik a .** Ezután jelölje ki a `var_person` harmadik mezőt, és keressen rá a Dinamikus tartalom ablakban. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

Az **If yes** feltétel mezőbe írja be a következőt az Excelbe, majd válassza **a Sor frissítése**lehetőséget.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

Adja meg az Excel adatait, és frissítse a **Kulcsoszlop**, **a Kulcsérték** és a **Személynév** mezőket. Ez hozzáfűzi az API által észlelt nevet az Excel-laphoz. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

## <a name="get-the-phone-number"></a>A telefonszám beszerezése

Minimalizálja az **Alkalmazás minden 2** műveletre a névre kattintva. Ezután adjon hozzá egy másik **apply minden** művelet, mint korábban. a neve **Minden 3-as ra jelentkezik.** Jelölje ki a szövegdobozt, és adjon hozzá **entitásokat** a művelet kimeneteként. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

Belül **Minden 3**alkalmazás , adjon hozzá egy **feltétel** vezérlőt. Ez lesz a **neve 2.** Az első szövegmezőben keresse meg és vegye fel az **Entitások típusát** a Dinamikus tartalom ablakból. Ügyeljen arra, hogy a középső mező **egyenlő legyen a beállítással.** Ezután a jobb oldali `var_phone`szövegmezőbe írja be a be írt értéket. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

Az **If yes** feltételben adjon hozzá **egy sorművelet frissítése** műveletet. Ezután adja meg az információkat, mint a fentieket, az Excel lap telefonszámoszlopához. Ez hozzáfűzi az API által érzékelt telefonszámot az Excel-laphoz. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::


## <a name="get-the-plumbing-issues"></a>Szerezd meg a vízvezeték kérdések

Minimalizálása **Alkalmazza minden 3** kattintva a nevét. Ezután hozzon létre egy másik **apply minden** a szülő művelet. Jelölje ki a szövegmezőt, és a dinamikus tartalomablakból adja hozzá az **Entitásokat** a művelet kimeneteként. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::


Ezután a folyamat ellenőrzi, hogy az Excel táblázatsorból származó problémaleírás tartalmazza-e a "vízvezeték" szót. Ha igen, akkor a IssueType oszlopban hozzáad "vízvezetéket". Ha nem, akkor beírjuk a "másik"-ot.

Az **Alkalmazás minden 4 műveletre** belül adjon hozzá egy **feltételvezérlőt.** Ez lesz a **neve Condition 3**. Az első szövegmezőben a Dinamikus tartalom ablakban keressen rá a Cikk az Excel-fájlba, és adja hozzá a **Leírást** az Excel-fájlból. Győződjön meg róla, hogy a középső **mezőben az látható, hogy tartalmazza-e**a . Ezután a jobb oldali szövegmezőben keresse meg és válassza a lehetőséget. `var_plumbing` 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::


Az **If yes** feltételben kattintson a **Művelet hozzáadása**gombra, és válassza a **Sor frissítése**lehetőséget. Ezután adja meg az adatokat, mint korábban. A IssueType oszlopban `var_plumbing`válassza a lehetőséget. Ez egy "vízvezeték" címkét alkalmaz a sorra.

A **Ha nincs** feltétel csoportban kattintson a **Művelet hozzáadása**gombra, és válassza a **Sor frissítése**lehetőséget. Ezután adja meg az adatokat, mint korábban. A IssueType oszlopban `var_other`válassza a lehetőséget. Ez egy "egyéb" címkét alkalmaz a sorra.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Adja hozzá a Szövegelemzés hitelesítő adatait a folyamathoz.":::

## <a name="test-the-workflow"></a>A munkafolyamat tesztelése

A képernyő jobb felső sarkában kattintson a **Mentés**gombra, majd a **Teszt gombra.** Válassza **az Eseményindító végrehajtása**lehetőséget. Kattintson **& teszt**mentése, **folyamat futtatása**, majd **a Kész gombra.**

Az Excel-fájl frissül a OneDrive-fiókjában. Úgy fog kinézni, mint az alábbi.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="A frissített excel táblázatkezelő.":::

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További megoldások felfedezése](../text-analytics-user-scenarios.md)

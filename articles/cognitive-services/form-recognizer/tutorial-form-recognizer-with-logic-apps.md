---
title: 'Oktatóanyag: űrlap-felismerő használata a Azure Logic Apps a számlák elemzéséhez – űrlap felismerő'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban az űrlap-felismerőt fogja használni a Azure Logic Apps használatával olyan munkafolyamat létrehozásához, amely automatizálja a modellek betanításának folyamatát, és teszteli a mintaadatok használatával.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: nitinme
ms.openlocfilehash: 7e155df62d05045ceed4479643ed13a8ba6fa06d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958300"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Oktatóanyag: űrlap-felismerő használata a Azure Logic Apps a számlák elemzéséhez

Ebben az oktatóanyagban egy munkafolyamatot hoz létre a Azure Logic Appsban, amely az Azure Cognitive Services Suite részét képező űrlap-felismerőt használja az adatok számlákból való kinyeréséhez. Az űrlap-felismerő használatával a modell első betanítása egy minta adatkészlettel, majd a modell tesztelése egy másik adatkészlettel. Az oktatóanyagban használt mintaadatok az Azure Storage blob-tárolókban tárolódnak.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Űrlap-felismerő hozzáférésének kérése
> * Azure Storage blob-tároló létrehozása
> * Mintaadatok feltöltése az Azure Blob-tárolóba
> * Azure logikai alkalmazás létrehozása
> * A logikai alkalmazás konfigurálása űrlap-felismerő erőforrás használatára
> * A munkafolyamat tesztelése a logikai alkalmazás futtatásával

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

## <a name="request-access-for-form-recognizer"></a>Űrlap-felismerő hozzáférésének kérése

Az űrlap-felismerő korlátozott hozzáférésű előzetes verzióban érhető el. Az előzetes verzió eléréséhez töltse ki és küldje el az [űrlap-felismerő hozzáférési kérelmének](https://aka.ms/FormRecognizerRequestAccess) űrlapját. Ha az Azure Cognitive Services csapata jóváhagyta a kérést, a szolgáltatás elérésére vonatkozó utasításokat tartalmazó e-mailt fog kapni.

## <a name="understand-the-invoice-to-be-analyzed"></a>Az elemzett számla értelmezése

A modell betanítására és tesztelésére használt mintaadatok készlete. zip-fájlként érhető el a [githubon](https://go.microsoft.com/fwlink/?linkid=2090451). Töltse le és bontsa ki a. zip fájlt, és nyisson meg egy számla PDF-fájlt a **/Train** mappában. Figyelje meg, hogy a tábla tartalmazza a számla számát, a számla dátumát stb. 

> [!div class="mx-imgBorder"]
> ![minta számla](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

Ez az oktatóanyag azt ismerteti, hogyan lehet az ilyen táblázatokból származó adatokat JSON formátumba kibontani a Azure Logic Apps és az űrlap felismerő használatával létrehozott munkafolyamatok használatával.

## <a name="create-an-azure-storage-blob-container"></a>Azure Storage blob-tároló létrehozása

Ezt a tárolót használja a modell betanításához szükséges mintaadatok feltöltésére.

1. A Storage-fiók létrehozásához kövesse az [Azure Storage-fiók létrehozása](../../storage/common/storage-quickstart-create-account.md) című témakör utasításait. Használja a **formrecostorage** nevet a Storage-fiók neveként.
1. Az Azure Storage-fiókban lévő tároló létrehozásához kövesse az [Azure Blob-tároló létrehozása](../../storage/blobs/storage-quickstart-blobs-portal.md) című témakör utasításait. A **formrecocontainer** használja a tároló neveként. Győződjön meg arról, hogy a nyilvános hozzáférési szintet **tárolóra állítja (névtelen olvasási hozzáférés tárolók és Blobok számára)** .

    > [!div class="mx-imgBorder"]
    > BLOB-tároló létrehozása ![](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Mintaadatok feltöltése az Azure Blob-tárolóba

Töltse le a [githubon](https://go.microsoft.com/fwlink/?linkid=2090451)elérhető mintaadatok letöltését. Bontsa ki az adatok mappát egy helyi mappába, és töltse fel a **/Train** mappa tartalmát a korábban létrehozott **formrecocontainer** . Az adatok tárolóba való feltöltéséhez kövesse az alábbi utasításokat: [Block blob feltöltése](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) .

Másolja a tároló URL-címét. Erre az oktatóanyag későbbi részében szüksége lesz. Ha létrehozta a Storage-fiókot és a tárolót az ebben az oktatóanyagban felsorolt névvel, akkor az URL-cím *https://formrecostorage.blob.core.windows.net/formrecocontainer/* .

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

A Azure Logic Apps a feladatok és munkafolyamatok automatizálására és összehangolására használható. Ebben az oktatóanyagban egy olyan logikai alkalmazást hoz létre, amelyet egy e-mail-mellékletként elemezni kívánt számla fogadásával indít el. Ebben a munkafolyamatban a következő feladatokat hajtja végre:
* Beállíthatja, hogy a logikai alkalmazás automatikusan induljon el, ha e-mailt kap egy csatolt számlával.
* Konfigurálja úgy a logikai alkalmazást, hogy az űrlap-felismerő **betanítási** művelettel a modell betanításához használja az Azure Blob Storage-ba feltöltött mintaadatok használatával.
* Konfigurálja úgy a logikai alkalmazást, hogy a már betanított modell használatára űrlap-felismerőt **elemezzen** . Ez az összetevő elemzi a logikai alkalmazáshoz megadott számlát, amely a korábban betanított modell alapján történik.

Kezdjük! A munkafolyamat beállításához kövesse az alábbi lépéseket.

1. Az Azure fő menüjében válassza az **erőforrás létrehozása** > **integráció** > **logikai alkalmazás**lehetőséget.

1. A **Logikai alkalmazás létrehozása** területen adja meg a logikai alkalmazás részleteit az itt látható módon. Ha elkészült, válassza a **Létrehozás**lehetőséget.

   | Tulajdonság | Value (Díj) | Leírás |
   |----------|-------|-------------|
   | **Name (Név)** | <*Logic-app-name*> | A logikai alkalmazás neve, amely csak betűket, számokat, kötőjeleket (`-`), aláhúzást (`_`), zárójeleket (`(`, `)`) és pontokat (`.`) tartalmazhat. Ez a példa a "My-First-Logic-app" kifejezést használja. |
   | **Előfizetés** | <*Azure-előfizetés-neve*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | <*Azure-Erőforrás-csoport neve* > | A kapcsolódó erőforrások rendszerezéséhez használt [Azure-erőforráscsoport](./../../azure-resource-manager/resource-group-overview.md) neve. Ez a példa a "My-First-LA-RG" kifejezést használja. |
   | **Hely** | <*Azure-régió*> | A logikai alkalmazás adatainak tárolására szolgáló régió. Ez a példa a "West US"-t használja. |
   | **Log Analytics** | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Miután az Azure üzembe helyezte az alkalmazást, az Azure eszköztárán válassza az **értesítések**  >  a telepített logikai alkalmazás**erőforrásának keresése** lehetőséget. Vagy megkeresheti és kiválaszthatja a logikai alkalmazást úgy, hogy beírja a nevet a keresőmezőbe.

   Megnyílik a Logikaialkalmazás-tervező, és egy bemutató videót és a gyakran használt triggereket tartalmazó oldalt jelenít meg. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   > [!div class="mx-imgBorder"]
   > ![válassza ki az üres sablont a logikai alkalmazáshoz](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>A logikai alkalmazás konfigurálása a munkafolyamat elindításához e-mail érkezésekor

Ebben az oktatóanyagban a munkafolyamatot indítja el, amikor egy csatolt számlával rendelkező e-mailt kap. Ebben az oktatóanyagban az Office 365-as e-mail-szolgáltatást választjuk, de bármilyen más használni kívánt e-mail-szolgáltatót használhat.

1. A lapokon válassza az összes lehetőséget, válassza az **Office 365 Outlook**lehetőséget, majd az **Eseményindítók**területen válassza ki, **hogy mikor érkezik új e-mail**.

    ![Logikai alkalmazás elindítása egy bejövő e-mailben](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. Az **office 365 Outlook** ablakban kattintson a **Bejelentkezés**elemre, és adja meg az Office 365-fiókba való bejelentkezéshez szükséges adatokat.

1. A következő párbeszédpanelen hajtsa végre a következő lépéseket.
    1. Válassza ki az új e-mailekhez figyelni kívánt mappát.
    1. A **mellékletek** esetében válassza az **Igen**lehetőséget. Ez biztosítja, hogy csak a mellékleteket tartalmazó e-mailek aktiválja a munkafolyamatot.
    1. A **mellékletek belefoglalása** területen válassza az **Igen**lehetőséget. Ez biztosítja, hogy a melléklet tartalma az alárendelt feldolgozásban legyen használatban.

        > [!div class="mx-imgBorder"]
        > ![logikai alkalmazás e-mail-triggerének konfigurálása](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Kattintson a felső eszköztár **Mentés** elemére.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>A logikai alkalmazás konfigurálása az űrlap-felismerő betanítási modell műveletének használatára

Ahhoz, hogy az űrlap-felismerő szolgáltatással elemezze a számlákat, be kell tanítania egy modellt úgy, hogy az a modell által elemezhető és megtanulni kívánt minta-számlákat is biztosít.

1. Válassza az **új lépés**lehetőséget, majd a **művelet választása**területen keressen az **űrlap-felismerő**kifejezésre. A megjelenített eredmények közül válassza ki az **űrlap-felismerő**elemet, majd az űrlap-felismerőhöz elérhető műveletek alatt válassza a **betanítási modell**lehetőséget.

    > [!div class="mx-imgBorder"]
    > Űrlap-felismerő modell ![betanítása](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Az űrlap-felismerő párbeszédpanelen adja meg a kapcsolatok nevét, és adja meg a végpont URL-címét és az űrlap-felismerő erőforráshoz beolvasott kulcsot.

    > [!div class="mx-imgBorder"]
    > az űrlap-felismerő ![jának neve](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Kattintson a  **Create** (Létrehozás) gombra.

1. A **betanítási modell** párbeszédpanel **forrás**mezőjébe írja be annak a tárolónak az URL-címét, ahová a mintaadatok feltöltése megtörténjen.

    > [!div class="mx-imgBorder"]
    > ![Storage-tároló a mintavételi számlákhoz](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Kattintson a felső eszköztár **Mentés** elemére.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>A logikai alkalmazás konfigurálása az űrlap-felismerő elemzési űrlap-műveletének használatára

Ebben a szakaszban az elemzési **űrlap** műveletet adja hozzá a munkafolyamathoz. Ez a művelet a már betanított modellt használja a logikai alkalmazáshoz megadott új számla elemzéséhez.

1. Válassza az **új lépés**lehetőséget, majd a **művelet választása**területen keressen az **űrlap-felismerő**kifejezésre. A megjelenített eredmények közül válassza az **űrlap-felismerő**lehetőséget, majd az űrlap-felismerőhöz elérhető műveletek alatt válassza az **elemzés űrlap**lehetőséget.

    > [!div class="mx-imgBorder"]
    > Űrlap-felismerő modell ![elemzése](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Az **elemzés az űrlapon** párbeszédpanelen tegye a következőket:

    1. Kattintson a **modell azonosítója** szövegmezőre, és a megnyíló párbeszédpanelen a **dinamikus tartalom** lapon válassza a **modelId**lehetőséget. Ezzel megadja a flow-alkalmazást az utolsó szakaszban betanított modell azonosítójának a modell-azonosítójával.

        > [!div class="mx-imgBorder"]
        > ![használja az ModelID az űrlap-felismerőhöz](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Kattintson a **dokumentum** szövegmezőre, és a megnyíló párbeszédpanelen a **dinamikus tartalom** lapon válassza a **mellékletek tartalom**elemet. Ezzel konfigurálja a folyamatot úgy, hogy a munkafolyamatot elindító e-mailben csatolt minta számlázási fájlt használja.

        > [!div class="mx-imgBorder"]
        > ![e-mail-melléklet használatával elemezze a számlákat](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Kattintson a felső eszköztár **Mentés** elemére.

### <a name="extract-the-table-information-from-the-invoice"></a>A tábla adatainak kibontása a számlából

Ebben a szakaszban a logikai alkalmazást úgy konfigurálja, hogy kibontsa az adatokat a táblán belül a számlákon.

1. Válassza a **művelet hozzáadása**lehetőséget, és a **válasszon műveletet**területen keressen az **összeállítás** elemre, és az elérhető műveletek területen válassza az **összeállítás** újra lehetőséget.
    ![Kinyeri a tábla adatait a számlából](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. Az **összeállítás** párbeszédpanelen kattintson a **bemenetek** szövegmezőre, és a felugró párbeszédpanelen válassza a **táblák**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Kinyeri a tábla adatait a számlából](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Kattintson a **Save** (Mentés) gombra.

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

A logikai alkalmazás teszteléséhez használja a **/test** mappában található minta számlákat a [githubról](https://go.microsoft.com/fwlink/?linkid=2090451)letöltött minta-adatkészletből. Hajtsa végre a következő lépéseket:

1. A Azure Logic Apps Designer alkalmazásban válassza a **Futtatás** lehetőséget a felső eszköztáron. A munkafolyamat mostantól aktív, és egy e-mailt vár a csatolt számlával.
1. Küldjön e-mailt a logikai alkalmazás létrehozásakor megadott e-mail-címre. Győződjön meg arról, hogy az e-mailt a rendszer a logikai alkalmazás konfigurálása során megadott mappába továbbítja.
1. Amint az e-mail el van látva a mappába, a Logic Apps Designer megjeleníti az egyes szakaszok előrehaladását jelző képernyőt. Az alábbi képernyőképen láthatja, hogy a rendszer a melléklettel rendelkező e-mailt fogadja, és a munkafolyamat folyamatban van.

    > [!div class="mx-imgBorder"]
    > ![elindíthatja a munkafolyamatot e-mail küldésével](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. A munkafolyamat összes szakaszának futtatása után a Logic Apps Designer minden fázisban egy zöld jelölőnégyzetet jelenít meg. A tervező ablakban válassza ki **a 2**. elemet, majd válassza az **összeállítás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![munkafolyamat befejeződött](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    A **kimenetek** mezőben másolja ki a kimenetet, és illessze be bármely szövegszerkesztőbe.

1. Hasonlítsa össze a JSON-kimenetet az e-mailben mellékletként elküldött minta-számlával. Ellenőrizze, hogy a JSON-adatmennyiség megfelel-e a számlán belüli táblában szereplő értékeknek.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    Sikeresen elvégezte az oktatóanyagot.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Logic Apps munkafolyamatot állít be az űrlap-felismerő használatával a modell betanításához és a számla tartalmának kibontásához. Következő lépésként megtudhatja, hogyan hozhat létre egy betanítási adatkészletet, így létrehozhat egy hasonló forgatókönyvet a saját űrlapjaival.

> [!div class="nextstepaction"]
> [Betanítási adatkészlet létrehozása](build-training-data-set.md)
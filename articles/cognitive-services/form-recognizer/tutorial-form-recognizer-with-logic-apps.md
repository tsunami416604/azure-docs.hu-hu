---
title: 'Oktatóanyag: Az Azure Logic Apps űrlapfelismerőjével elemezheti a számlákat – Űrlapfelismerő'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban a Form Recognizer és az Azure Logic Apps használatával hozhat létre egy munkafolyamatot, amely automatizálja a modell betanításának folyamatát, és mintaadatok használatával teszteli azt.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118266"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Oktatóanyag: Az Azure Logic Apps űrlapfelismerő jének használata a számlák elemzéséhez

Ebben az oktatóanyagban egy munkafolyamatot hoz létre az Azure Logic Apps alkalmazásban, amely az Azure Cognitive Services-csomag részét képező Form Recognizer szolgáltatást használja az adatok számlákból való kinyeréséhez. Először betanítegy űrlapfelismerő modellt egy mintaadatkészlet használatával, majd tesztelje a modellt egy másik adatkészleten.

Itt van, amit ez a bemutató kiterjed:

> [!div class="checklist"]
> * Hozzáférés kérése az űrlapfelismerőhöz
> * Azure Storage blobtároló létrehozása
> * Mintaadatok feltöltése az Azure blobtárolóba
> * Azure Logic-alkalmazás létrehozása
> * A logikai alkalmazás konfigurálása űrlapfelismerő erőforrás használatára
> * A munkafolyamat tesztelése a logikai alkalmazás futtatásával

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/)

## <a name="understand-the-invoice-to-be-analyzed"></a>Az elemzendő számla ismertetése

A modell betanításához és teszteléséhez használt mintaadatkészlet .zip fájlként érhető el a [GitHubról.](https://go.microsoft.com/fwlink/?linkid=2090451) Töltse le és bontsa ki a .zip fájlt, és nyisson meg egy számla PDF-fájlt a **/Train** mappában. Figyelje meg, hogy van egy táblája a számlaszámmal, a számla dátumával és így tovább. 

> [!div class="mx-imgBorder"]
> ![Mintaszámla](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Logic Apps-munkafolyamatokat az ilyen táblákból származó információk JSON formátumba történő kinyeréséhez.

## <a name="create-an-azure-storage-blob-container"></a>Azure Storage blobtároló létrehozása

Ezzel a tárolóval töltheti fel a modell betanításához szükséges mintaadatokat.

1. Kövesse az [Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című, tárfiók létrehozásához című, az utasításokat. Használja **a formrecostorage-t** a tárfiók neveként.
1. Kövesse az [Azure blobtároló létrehozása](../../storage/blobs/storage-quickstart-blobs-portal.md) című, az Azure Storage-fiókon belüli tároló létrehozásához című útmutató utasításait. Használja **a formrecocontainer-t** a tároló neveként. Győződjön meg arról, hogy a nyilvános hozzáférési szintet **a Tároló (névtelen olvasási hozzáférés tárolók és blobok)** értékre állította.

    > [!div class="mx-imgBorder"]
    > ![Blobtároló létrehozása](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Mintaadatok feltöltése az Azure blobtárolóba

Töltse le a [GitHubon](https://go.microsoft.com/fwlink/?linkid=2090451)elérhető mintaadatokat. Bontsa ki az adatokat egy helyi mappába, és töltse fel a **/Train** mappa tartalmát a korábban létrehozott **formrecocontainerfájlba.** Kövesse a [blokkblob feltöltése](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) című útmutatóutasításait, és töltsön fel adatokat egy tárolóba.

Másolja a tároló URL-címét. Szüksége lesz erre az URL-re később az oktatóanyagban. Ha a tárfiókot és a tárolót az oktatóanyagban felsoroltakkal megegyező nevekkel hozta létre, az URL-cím *https\/lesz: /formrecostorage.blob.core.windows.net/formrecocontainer/*.

## <a name="create-a-form-recognizer-resource"></a>Űrlapfelismerő erőforrás létrehozása

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

Az Azure Logic Apps segítségével automatizálhatja és vezényelheti a feladatokat és a munkafolyamatokat. Ebben az oktatóanyagban egy logikai alkalmazást hoz létre, amelyet egy e-mail mellékletként elemezni kívánt számla fogadása indít el. Ebben a munkafolyamatban a következő feladatokat hajthatja végre:
* Állítsa be a logikai alkalmazást, hogy automatikusan aktiválódjon, ha e-mailt kap egy számlával együtt.
* Konfigurálja a logikai alkalmazást egy űrlapfelismerő **betanítási modell** művelet használatával a modell betanításához az Azure blob storage-ba feltöltött mintaadatok használatával.
* Állítsa be a logikai alkalmazást úgy, hogy egy **űrlapfelismerő elemelemzési műveletet** használjon a már betanított modell használatához. Ez az összetevő elemzi a logikai alkalmazásnak megadott számlát a korábban betanított modell alapján.

A munkafolyamat beállításához kövesse az alábbi lépéseket.

1. Az Azure főmenüjében válassza az > **Erőforrás-integrációs** > **logikai alkalmazás** **létrehozása**lehetőséget.

1. A **Logikai alkalmazás létrehozása** területen adja meg a logikai alkalmazás részleteit az itt látható módon. Miután elkészült, válassza a **Létrehozás gombot.**

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*logikai alkalmazás neve*> | A logikai alkalmazás neve, amely csak betűket,`-`számokat,`_`kötőjeleket (`(`), `)`aláhúzásjeleket`.`( ), zárójeleket ( , és pontokat ( tartalmazhat. Ez a példa a "My-First-Logic-App" (Saját első logikai alkalmazás) alkalmazást használja. |
   | **Előfizetés** | <*Azure-előfizetés-név*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | <*Azure-erőforrás-csoport név*> | A kapcsolódó erőforrások rendszerezéséhez használt [Azure-erőforráscsoport](./../../azure-resource-manager/management/overview.md) neve. Ez a példa a "My-First-LA-RG" értéket használja. |
   | **Helyen** | <*Azure-régió*> | Az a régió, ahol a logikai alkalmazás adatait tárolhatja. Ez a példa a "West USA" (USA nyugati) |
   | **Log Analytics** | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Miután az Azure üzembe helyezte az alkalmazást, az Azure eszköztárán válassza **az Értesítések** > **ugrás az erőforráshoz** lehetőséget az üzembe helyezett logikai alkalmazáshoz. Vagy megkeresheti és kiválaszthatja a logikai alkalmazást a név beírásával a keresőmezőbe.

   Megnyílik a Logikaialkalmazás-tervező, és egy bemutató videót és a gyakran használt triggereket tartalmazó oldalt jelenít meg. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   > [!div class="mx-imgBorder"]
   > ![Üres sablon kijelölése a logikai alkalmazáshoz](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>A logikai alkalmazás konfigurálása a munkafolyamat elindításához, amikor egy e-mail megérkezik

Ebben az oktatóanyagban akkor indítja el a munkafolyamatot, amikor egy csatolt számlával érkező e-mail érkezik. Ez az oktatóanyag az Office 365-öt használja e-mail szolgáltatásként, de bármely más e-mail szolgáltatót is használhat, amelyet használni szeretne.

1. A lapokon válassza az Összes lehetőséget, válassza az **Office 365 Outlook**lehetőséget, majd az **Eseményindítók**csoportban válassza **az Új e-mail érkezésekor**lehetőséget.

    ![Logikai alkalmazás aktiválása bejövő e-mailen keresztül](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. Az **Office 365 Outlook** párbeszédpanelen kattintson a **Bejelentkezés**gombra, és adja meg a részleteket az Office 365-fiókba való bejelentkezéshez.

1. A következő párbeszédpanelen hajtsa végre a következő lépéseket.
    1. Válassza ki azt a mappát, amelyet figyelni kell az új e-maileknél.
    1. **A Has mellékletek**esetén válassza az **Igen**lehetőséget. Ez biztosítja, hogy csak a mellékleteket tartalmazó e-mailek váltsák ki a munkafolyamatot.
    1. A **Mellékletekkel együtt**területen válassza az **Igen**lehetőséget. Ez biztosítja, hogy a melléklet tartalmát felhasználják a későbbi feldolgozássorán.

        > [!div class="mx-imgBorder"]
        > ![Logikai alkalmazás e-mail eseményindítójának konfigurálása](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. **Kattintson** a mentés gombra a felső eszköztáron.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>A logikai alkalmazás konfigurálása a Form Recognizer Train Model művelet használatára

Mielőtt az Űrlapfelismerő szolgáltatássegítségével elemezheti a számlákat, be kell képeznie egy modellt, és meg kell adnia neki néhány mintaszámlaadatot, amelyből a modell elemezheti és tanulhat.

1. Válassza az **Új lépés**lehetőséget, és a **Művelet kiválasztása**csoportban keresse meg **az Űrlapfelismerő elemet.** A megjelenő eredmények közül válassza az **Űrlapfelismerő**lehetőséget, majd az Űrlapfelismerő számára elérhető műveletek alatt válassza a **Modell betanítása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Űrlapfelismerő modell betanítása](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Az Űrlapfelismerő párbeszédpanelen adja meg a kapcsolat nevét, és adja meg a végpont URL-címét és az Űrlapfelismerő erőforráshoz beolvasott kulcsot.

    > [!div class="mx-imgBorder"]
    > ![Az űrlapfelismerő kapcsolatneve](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Kattintson **a Létrehozás gombra.**

1. A **Modell betanítása** párbeszédpanel **Forrás**mezőjébe írja be annak a tárolónak az URL-címét, amelyben a mintaadatokat feltöltötte.

    > [!div class="mx-imgBorder"]
    > ![Tárolótároló mintaszámlákhoz](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. **Kattintson** a mentés gombra a felső eszköztáron.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>A logikai alkalmazás konfigurálása az Űrlapfelismerő űrlapelemzési űrlap művelethasználatára

Ebben a szakaszban **hozzáadhatja** az Űrlap elemzése műveletet a munkafolyamathoz. Ez a művelet a már betanított modell t használja egy új számla elemzéséhez, amely a logikai alkalmazás számára biztosított.

1. Válassza az **Új lépés**lehetőséget, és a **Művelet kiválasztása**csoportban keresse meg **az Űrlapfelismerő elemet.** A megjelenő eredmények közül válassza az **Űrlapfelismerő**lehetőséget, majd az Űrlapfelismerő számára elérhető műveletek alatt válassza az **Űrlap elemzése**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Űrlapfelismerő modell elemzése](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Az **Űrlap elemzése** párbeszédpanelen tegye a következő lépéseket:

    1. Kattintson a **Modellazonosító** szövegmezőre, és a megnyíló párbeszédpanel **Dinamikus tartalom** lapján válassza a **modelId lehetőséget.** Ezzel biztosítja a folyamatalkalmazás a modellazonosítóa a modell az utolsó szakaszban betanított.

        > [!div class="mx-imgBorder"]
        > ![A Modellazonosító használata űrlapfelismerőhez](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Kattintson a **Dokumentum** szövegmezőre, és a megnyíló párbeszédpanel **Dinamikus tartalom** lapján válassza a **Mellékletek tartalma lehetőséget.** Ez úgy konfigurálja a folyamatot, hogy a munkafolyamatot kiváltó e-mailben csatolt mintaszámlafájlt használja.

        > [!div class="mx-imgBorder"]
        > ![Számlák elemzése e-mail melléklettel](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. **Kattintson** a mentés gombra a felső eszköztáron.

### <a name="extract-the-table-information-from-the-invoice"></a>A táblaadatok kinyerése a számláról

Ebben a szakaszban konfigurálja a logikai alkalmazást, hogy kinyerje az adatokat a táblából a számlákon belül.

1. Válassza **a Művelet hozzáadása lehetőséget,** és a Művelet **kiválasztása**csoportban keresse meg az **Írás elemet,** és a rendelkezésre álló műveletek alatt válassza a **Újra írás** lehetőséget.
    ![Táblaadatok kinyerése a számláról](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. A **Közösség** párbeszédpanelen kattintson a **Bemenetek** szövegmezőre, majd a megjelenő párbeszédpanelen jelölje ki a **táblázatokat.**

    > [!div class="mx-imgBorder"]
    > ![Táblaadatok kinyerése a számláról](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Kattintson a **Mentés** gombra.

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

A logikai alkalmazás teszteléséhez használja a [gitHubról](https://go.microsoft.com/fwlink/?linkid=2090451)letöltött mintaadatkészlet **/Test** mappájában található mintaszámlákat. Kövesse az alábbi lépéseket:

1. Az Azure Logic Apps-tervező az alkalmazáshoz, válassza a **Futtatás** az eszköztár tetején. A munkafolyamat most már aktív, és arra vár, hogy e-mailt kapjon a számlával együtt.
1. Küldjön egy e-mailt egy mintaszámlával csatolva az e-mail cím, amely a logikai alkalmazás létrehozása során megadott. Győződjön meg arról, hogy az e-mail a logikai alkalmazás konfigurálása során megadott mappába kerül.
1. Amint az e-mail kézbesíti a mappába, a Logic Apps Designer egy képernyőt jelenít meg az egyes fázisok előrehaladásával. Az alábbi képernyőképen láthatja, hogy egy mellékletet tartalmazó e-mail érkezik, és a munkafolyamat folyamatban van.

    > [!div class="mx-imgBorder"]
    > ![A munkafolyamat indítása e-mail küldésével](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Miután a munkafolyamat összes szakasza befejeződött, a Logic Apps Designer minden szakaszban zöld jelölőnégyzetet jelenít meg. A tervezőablakban válassza a **Minden 2 területen**lehetőséget, majd az **Írás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Munkafolyamat befejeződött](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    A **KIMENETEK** mezőből másolja a kimenetet, és illessze be bármelyik szövegszerkesztőbe.

1. Hasonlítsa össze a JSON-kimenetet az e-mailmellékletként küldött mintaszámlával. Ellenőrizze, hogy a JSON-adatok megfelelnek-e a számlán belüli táblában szereplő adatoknak.

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
    Sikeresen befejezte ezt az oktatóanyagot!

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Logic Apps-munkafolyamatot állít be, amely a Formafelismerő használatával betanítja a modellt, és kibontja a számla tartalmát. Ezután megtudhatja, hogyan hozhat létre egy betanítási adatkészletet, így létrehozhat egy hasonló forgatókönyvet a saját űrlapokkal.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](build-training-data-set.md)
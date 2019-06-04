---
title: Több kapcsolja beszélgetések
titleSuffix: Azure Cognitive Services
description: Kérések és a környezet használatával kezelheti a több bekapcsolja, több kapcsolja be, egy másikra egy kérdést a robotja néven. Több kapcsolja rendszer azon képessége, hogy a biztonsági és a hitech beszélgetés, ahol az előző kérdésre adott környezet befolyásolja a következő kérdést és választ.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 3ca166b287858b3e42aeda1421d1733fe24c81ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479723"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Hozzon létre egy témakör több hellyé követő utasításokat használatával

Követő utasításokat és a környezet használata kezelheti a több bekapcsolja, más néven _több kapcsolja_, egy másikra egy kérdést a robotja.

Tekintse meg a következő bemutató videó megtekintéséhez, hogyan történik.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Mi az a többszörös kapcsolja beszélgetés?

Egyetlen menetben nem lehet válaszolni a feltett kérdésekre. Amikor az ügyfél-alkalmazás (csevegőrobot) beszélgetések, a felhasználó előfordulhat, hogy kérdés feltevése, amely a szűrt vagy is, hogy megállapítsuk a megfelelő választ kell. Ez a folyamat a kérdések keresztül lehetséges rendelkező felhasználó szabályzatkérelem **követő kéri**.

Ha a felhasználó kéri a kérdést, QnA Maker adja vissza a választ _és_ a további utasításokat. Ez lehetővé teszi lehetővé a választható lehetőségként követő kérdéseket. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Példa a többszörös kapcsolja beszélgetést csevegőrobot

Csevegőrobotot kezeli a beszélgetést a felhasználóval, kérdést a kérdés, a végső választ meghatározásához.

![A természetes nyelvi flowban beszélgetés állapot kezelése egy több kapcsolja párbeszédpanel-rendszerben azáltal, hogy folytatja a beszélgetés lehetőségeket mutatják be, a válaszok belül utasításokat.](../media/conversational-context/conversation-in-bot.png)

Az előző képen, a felhasználó által megadott `My account`. A Tudásbázis 3 társított kérdés-válasz párt tartalmaz. A felhasználó közül kell választania a három lehetőségek, amellyel pontosíthatja a választ. A kérdés (1), a Tudásbázis van három követő utasításokat, mint három lehetőség (2) a csevegőrobot jelenik meg. 

Amikor a felhasználó kiválasztja az egyik lehetőséget (3), majd pontosítását lehetőségek (4) következő listája jelennek meg. Ez is folytassa az eljárást (5) a megfelelő és egyben utolsó választ (6) határozza meg.

Az előző képen rendelkezik **engedélyezése több kapcsolja** kijelölni a megjelenő utasításokat. 

### <a name="using-multi-turn-in-a-bot"></a>Több kapcsolja be a bot használatával

Az ügyfélalkalmazás kezelheti a környezetfüggő beszélgetés módosítani szeretné. Hozzá kell adnia [kódot, hogy a robot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting) megtekintéséhez az utasításokat.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Hozzon létre több kapcsolja beszélgetés egy dokumentum szerkezete

Tudásbázis létrehozása, ha egy nem kötelező – a jelölőnégyzetet, több kapcsolja kinyerési jelenik meg. 

![Tudásbázis létrehozása, ha egy nem kötelező – a jelölőnégyzetet, több kapcsolja kinyerési jelenik meg.](../media/conversational-context/enable-multi-turn.png)

Ha ezt a beállítást, amikor importálja egy dokumentum, a több kapcsolja beszélgetés is implicit a struktúrát a. Ha létezik struktúra, QnA Maker az Ön hozza létre a követő parancssor QnA párok. 

Több kapcsolja struktúra csak URL-címek, PDF vagy DOCX következtetni lehet fájlokat. 

Az alábbi képen egy Microsoft Surface- [PDF-fájl](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) célja, hogy manuális használható. Miatt a PDF-fájl mérete, az Azure QnA Maker erőforrás van szükség a B tarifacsomag keresés (15 indexek) vagy nagyobb. 

![! [Ha importálja egy dokumentum, környezetfüggő beszélgetés is felelősségét a struktúrát a. Ha létezik struktúra, QnA Maker hoz létre a követő parancssor QnA párok, a dokumentum importálás részeként.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

A PDF-dokumentum importálásakor a QnA Maker követő utasításokat a struktúrát, természetes nyelvi folyamat létrehozása a határozza meg. 

1. A **1. lépés**válassza **Tudásbázis létrehozása** a felső navigációs sávon.
1. A **2. lépés**, hozzon létre, vagy használjon egy meglévő kérdések és válaszok szolgáltatást. Ügyeljen arra, hogy a kérdések és válaszok szolgáltatást használ egy keresési szolgáltatással a, B (15 indexek) vagy újabb, mert a Surface manuális PDF-fájl túl nagy a egy alacsonyabb szintű csomag.
1. A **3. lépés**, adja meg egy nevet a Tudásbázis, például `Surface manual`.
1. A **4. lépés**válassza **engedélyezése több kapcsolja kinyerési URL-címek, a .pdf vagy a .docx fájlokat.** Válassza ki az URL-címet a Surface manuális

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. Válassza ki a **létrehozása a KB-os** gombra. 

    Az ismeretcikk létrehozása után a kérdés és válasz párok nézetét jeleníti meg.

## <a name="show-questions-and-answers-with-context"></a>Kérdések és válaszok kontextusú megjelenítése

Csökkentse a kérdés és válasz párok korlátoznia környezetfüggő beszélgetések az megjelenik. 

1. Válassza ki **beállítások megtekintéséhez**, majd **Show környezet (előzetes verzió)** . A lista kérdést és választ párokat tartalmazó követő utasításokat tartalmazza. 

    ![Szűrheti a kérdés, és válaszoljon pár környezetfüggő beszélgetések által](../media/conversational-context/filter-question-and-answers-by-context.png)

2. Az első oszlop több kapcsolja összefüggésben jeleníti meg.

    ![! [A PDF-dokumentum importálásakor a QnA Maker meghatározza, hogy a struktúrát, természetes nyelvi folyamat létrehozása a további utasításokat. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    Az előző képen #1 azt jelzi, hogy az az oszlop, amely azt jelzi, hogy az aktuális kérdés félkövérrel. A szülő kérdés legfelső elemére a sor. Az alábbi kérdése olyan a társított kérdést és választ párok. Ezek az elemek választható, így azonnal nyissa meg a környezet más elemeket. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Meglévő kérdés-válasz párt követő kérdés hozzáadása

Az eredeti kérdését `My account` tartozik például a követő utasításokat `Accounts and signing in`. 

![A "Saját fiók" eredeti kérdést helyesen adja vissza a "Fiókokat, és jelentkezzen be" választ, és már rendelkezik az társított követő utasításokat.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Követési kérdés hozzáadása egy meglévő kérdés-válasz párt, amely jelenleg nincs csatolva. A kérdés nem csatolt bármilyen kérdés-válasz párt, mert a jelenlegi nézet beállításait kell módosítani.

1. Egy meglévő QnA pár követő kérdés, mutató hivatkozást, a kérdés és válasz párt sorának kijelölésével. A Surface manuális, keressen rá a `Sign out` csökkentése érdekében a listában.
1. Sorában `Signout`válassza **Hozzáadás követő parancssor** származó a **válasz** oszlop.
1. Az a **követő parancssor (előzetes verzió)** előugró ablak, írja be a következőket:

    |Mező|Érték|
    |--|--|
    |Megjelenítendő szöveg|`Turn off the device`. Ez az egyéni szöveg úgy dönt, hogy a követő parancssor megjelenítéséhez.|
    |Csak helyi|Kijelölt. Ez a válasz csak adható vissza, ha a kérdés határozza meg a környezetben.|
    |Válasz mutató hivatkozás|Adja meg `Use the sign-in screen` található a meglévő kérdés-válasz párt.|


1.  Egy egyezést adja vissza. Válassza ki ezt a választ, az azt követő, majd válassza ki **mentése**. 

    ![Keresse meg a követési rendszer mutató hivatkozást a meglévő válasz, válasz párbeszédpanel használatával a válasz szövegét.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Miután hozzáadta a nyomon követési rendszer kéri, akkor ne felejtse el bejelölni **mentéséhez és a vonat** a felső navigációs.
  
### <a name="edit-the-display-text"></a>A megjelenített szöveg szerkesztése 

Amikor egy utólagos kérés jön létre, és egy meglévő kérdés-válasz párt van kiválasztva a **válasz mutató hivatkozás**, megadhat új **szöveg megjelenítése**. Ez a szöveg nem helyettesíti a meglévő kérdést, és nem ad hozzá egy új másik kérdést. Elkülönül ezeket az értékeket. 

1. A megjelenített szöveg szerkesztéséhez keresse meg és válassza ki a kérdést a **környezet** mező.
1. A kérdés sorban a válasz oszlopban jelölje be a nyomon követési rendszer. 
1. Válassza ki a szerkeszteni, majd válassza ki a megjelenítendő szöveget **szerkesztése**.

    ![Jelölje ki a szerkeszteni kívánt megjelenítendő szöveget, majd válassza ki a szerkesztése.](../media/conversational-context/edit-existing-display-text.png)

1. A **követő parancssor** előugró lehetővé teszi, hogy módosítsa a meglévő megjelenítendő szöveget. 
1. Ha elkészült a megjelenített szöveg szerkesztése, válassza ki **mentése**. 
1. Ne feledje bejelölni **mentéséhez és a vonat** a felső navigációs.


<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Új kérdés-válasz párt követő parancssor hozzáadása

Adja hozzá a Tudásbázis QnA párokat. A QnA pár követő kérdés, kösse össze egy meglévő kérdés.

1. A Tudásbázis eszköztárról, keresse meg és válassza a meglévő kérdés-válasz párt `Accounts and Signing In`. 

1. Az a **válasz** oszlop a kérdésre, válassza ki a **Hozzáadás követő parancssor**. 
1. A **követő parancssor (előzetes verzió)** , hozzon létre egy új utólagos kérés a következő értékek megadásával: 

    |Szövegmező|Érték|
    |--|--|
    |**Megjelenítendő szöveg**|`Create a Windows Account`. Ez az egyéni szöveg úgy dönt, hogy a követő parancssor megjelenítéséhez.|
    |**Csak helyi**|Kijelölt. Ez a válasz csak adható vissza, ha a kérdés határozza meg a környezetben.|
    |**Hivatkozás választ**|A válasznak adja meg a következő szöveget:<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>Mentse, és az adatbázis betanításához, ez a szöveg alakítja át |
    |||

    ![Hozzon létre új parancssor kérdések és válaszok](../media/conversational-context/create-child-prompt-from-parent.png)


1. Válassza ki **új létrehozása** majd **mentése**. 

    Létrehozott egy új kérdés-válasz párt, és a kapcsolódó követési kérdés, a kiválasztott kérdés. A **környezet** mindkét kérdésre-oszlop azt jelzi, hogy követő parancssor kapcsolat. 

1. Módosítsa a **beállításainak megtekintése** való [környezet megjelenítése](#show-questions-and-answers-with-context).

    Az új kérdésnek bemutatja, hogyan kapcsolódik.

    ![Hozzon létre egy új követő parancssor ](../media/conversational-context/new-qna-follow-up-prompt.png)

    A szülő kérdést a választási lehetőségek egyikét jeleníti meg az új kérdést.

    ![! [A környezet oszlop mindkét kérdése van, az azt jelzi, hogy követő parancssor kapcsolat.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Miután hozzáadta a nyomon követési rendszer kéri, akkor ne felejtse el bejelölni **mentéséhez és a vonat** a felső navigációs.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>Ha tesztelési követő kérni fogja a többszörös kapcsolja engedélyezése

Amikor a a kérdés megfogalmazásában ellenőrzési tesztelés kéri a **teszt** ablaktáblán válassza **engedélyezése több kapcsolja**, és írja be a kérdést. A válasz az követő utasításokat tartalmazza.

![Ha teszteli a kérdést a ablaktáblán, a válasz tartalmazza a további utasításokat.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Ha nem engedélyezi több kapcsolja be, a választ adja vissza, de követő utasításokat nem adja vissza.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON-kérelem a kezdeti válasz és követő utasításokat visszaadása

Használja az üres `context` objektumot a felhasználó kérdésre adott válasz kérése és követő utasításokat tartalmazza. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>JSON-választ a kezdeti válasz és követő utasításokat visszaadása

Az előző szakaszban a kért választ, és bármely követő utasításokat követve `Accounts and signing in`. A válasz tartalmazza a kérdés információ található `answers[0].context`, a felhasználónak megjelenítendő szöveget is. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

A `prompts` tömb biztosít a szöveg a `displayText` tulajdonság és a `qnaId` flow értéket, így ezek a válaszok megjelenítheti a következő megjelenített választható lehetőségként a beszélgetést, majd küldése a következő kérelmet a QnA Maker kijelölt értéket. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON-kérelmi adhatja vissza nem eredeti válasz és követő utasításokat

Töltse ki a `context` objektum tartalmazza az előző környezetben.

A következő JSON-kérelemben, az aktuális kérdés az `Use Windows Hello to sign in` és volt az előző kérdésnél `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>A válasz nem kezdeti és követő utasításokat visszaadása JSON-válasz

A QnA Maker _GenerateAnswer_ JSON-válasz tartalmazza a nyomon követési utasításait a `context` első elemének tulajdonságát a `answers` objektum:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>A Tudásbázis a QnA-Azonosítóval rendelkező lekérdezés

A kezdeti kérdésre adott válasz, a további utasításokat és az ahhoz társított `qnaId` adja vissza. Most, hogy az azonosító, a nyomon követési rendszer kérelem törzsében szereplő továbbíthatja ezt. Ha a kérés törzse tartalmazza a `qnaId`, és a környezeti objektumot (amely a korábbi QnA tulajdonságokat tartalmazza), majd GenerateAnswer adja vissza a pontos kérdés azonosítója találja meg a választ a kérdés szövege által a rangsorolási algoritmust használata helyett. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Megjelenítés, üzenetek és a környezet küldését az ügyfélalkalmazásban 

Kérések hozzáadta a Tudásbázis és a folyamat tesztelése a teszt panelt. Most szeretné használni ezeket az utasításokat az ügyfélalkalmazásban. A Bot Framework az utasításokat a rendszer nem indul el automatikusan jelenik meg az ügyfélalkalmazások számára. Megjelenítheti az utasításokat a javasolt műveletek vagy gombok a választ a felhasználó lekérdezés részeként az ügyfél alkalmazások együtt ez [Bot Framework minta](https://aka.ms/qnamakermultiturnsample) a kódban. Az ügyfélalkalmazás az aktuális QnA-Azonosítót és a felhasználó lekérdezése tárolására, és adja meg azokat a a [context objektumot az GenerateAnswer API](#json-request-to-return-non-initial-answer-and-follow-up-prompts) a következő felhasználói lekérdezés. 

## <a name="display-order-supported-in-api"></a>Megjelenítési sorrendjét támogatott API-ban

A [szöveg és sorrendben jelennek meg](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), a JSON-válasz, a Szerkesztés által támogatott a [frissítés API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="next-steps"></a>További lépések

További információ a környezetfüggő beszélgetések a [párbeszédpanel minta](https://aka.ms/qnamakermultiturnsample) vagy tudjon meg többet [fogalmi bot tervezése több kapcsolja beszélgetés](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Tudásbázis áttelepítése](../Tutorials/migrate-knowledge-base.md)

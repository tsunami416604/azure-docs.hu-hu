---
title: Több kapcsolja beszélgetések
titleSuffix: Azure Cognitive Services
description: Kérések és a környezet használatával kezelheti a több bekapcsolja, több kapcsolja be, egy másikra egy kérdést a robotja néven. Több kapcsolja vissza oda-beszélgetés vannak, ahol az előző kérdésre adott környezet befolyásolja a következő kérdést és választ.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508136"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Hozzon létre egy témakör több hellyé követő utasításokat használatával

Követő utasításokat és a környezet használata kezelheti a több bekapcsolja, más néven _több kapcsolja_, egy másikra egy kérdést a robotja.

Több kapcsolja működésének megtekintéséhez a következő bemutató videó megtekintése:

[![A QnA Maker több kapcsolja beszélgetés](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Mi az a többszörös kapcsolja beszélgetés?

Egyetlen menetben nem lehet válaszolni a feltett kérdésekre. Amikor az ügyfél-alkalmazás (csevegőrobot) beszélgetések, a felhasználó előfordulhat, hogy kérdés feltevése, amely a szűrt vagy kifinomultabb lett határozza meg a helyes választ kell. Lehetővé teszik, ez a folyamat segítségével a kérdések a felhasználót az szabályzatkérelem *követő kéri*.

Amikor egy felhasználó kérdést tesz fel, QnA Maker adja vissza a választ _és_ a további utasításokat. A válasz lehetővé teszi lehetővé a nyomon követési kérdések választható lehetőségként. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Példa a többszörös kapcsolja beszélgetést csevegőrobot

A többszörös kapcsolja csevegőrobotot a beszélgetést a felhasználót, hogy a végső válasz meghatározása kezeli, az alábbi képen látható módon:

![Egy több kapcsolja párbeszédpanelen megjelenő utasításokat, amelyek végigvezetik a felhasználót a beszélgetés](../media/conversational-context/conversation-in-bot.png)

Az előző képen, a felhasználó rendelkezik beszélgetést megadásával **fiókom**. A Tudásbázis három társított kérdés-válasz párt tartalmaz. Pontosítsa a választ, a felhasználó kiválasztja a három lehetőségek a Tudásbázis. A kérdés (1), már három követő utasításokat, amely három lehetőség (2) a csevegőrobot a jelennek meg. 

Amikor a felhasználó egy (3) lehetőséget választja, pontosítását lehetőségek (4) következő listája jelenik meg. Ez a feladatütemezési továbbra is (5), amíg a felhasználó meghatározza, hogy a helyes, végső választ (6).

> [!NOTE]
> Az előző képen az **engedélyezése több kapcsolja** jelölőnégyzet be van jelölve, győződjön meg arról, hogy megjelenik a képernyőn megjelenő utasításokat. 

### <a name="use-multi-turn-in-a-bot"></a>Több kapcsolja be a robot a

Kezeli a környezetfüggő beszélgetést, módosítsa az ügyfélalkalmazás által [kód hozzáadása a robot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). A kód hozzáadása lehetővé teszi a felhasználók számára az utasításokat.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Hozzon létre több kapcsolja beszélgetés egy dokumentum szerkezete

Egy Tudásbázis létrehozásakor a **töltse fel a KB-os** szakaszban jeleníti meg egy **engedélyezése több kapcsolja kinyerési URL-címek, a .pdf vagy a .docx fájlokat** jelölőnégyzetet. 

![Több kapcsolja kinyerési jelölőnégyzetet](../media/conversational-context/enable-multi-turn.png)

Ha egy importált dokumentum ezt a beállítást választja, a több kapcsolja beszélgetés is implicit a dokumentumok struktúráját. Ha struktúra, QnA Maker a nyomon követési rendszer adott párok kérdések és válaszok az Ön hozza létre az importálási folyamat részeként. 

Több kapcsolja struktúra következtetni lehet csak az URL-címek, PDF-fájlok, vagy a DOCX-fájlok. Egy vonatkozó példáért struktúra megtekintése képe egy [Microsoft Surface-felhasználó manuális PDF-fájl](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). A PDF-fájl mérete miatt a QnA Maker erőforrás igényel egy **tarifacsomag keresési** , **B** (15 indexek) vagy nagyobb. 

![! [Példa struktúra a felhasználó manuális] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Amikor importálja a PDF-dokumentum, QnA Maker meghatározza, hogy követő kérni fogja a struktúrát a természetes nyelvi folyamat létrehozásához. 

1. A QnA Maker, válassza ki **Tudásbázis létrehozása**.
1. Hozzon létre, vagy használjon egy meglévő QnA Maker szolgáltatást. Az előző példában Microsoft Surface, mivel a PDF-fájl túl nagy a egy alacsonyabb szintű csomag, használja a QnA Maker szolgáltatás, amely egy **keresési szolgáltatás** , **B** (15 indexek) vagy nagyobb.
1. Adja meg egy nevet a Tudásbázis, például **Surface manuális**.
1. Válassza ki a **engedélyezése több kapcsolja kinyerési URL-címek, a .pdf vagy a .docx fájlokat** jelölőnégyzetet. 
1. Válassza ki a Surface manuális URL-címe, **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Válassza ki a **létrehozása a KB-os** gombra. 

    A Tudásbázis létrehozása után a kérdések és válaszok párok nézete jelenik meg.

## <a name="show-questions-and-answers-with-context"></a>Kérdések és válaszok kontextusú megjelenítése

Csökkentheti a megjelenített kérdések és válaszok párok bejegyzésekre, amelyekhez környezeti beszélgetések. 

Válassza ki **beállítások megtekintéséhez**, majd válassza ki **Show környezet (előzetes verzió)** . A lista megjeleníti a kérdések és válaszok párok követő utasításokat tartalmazó. 

![Kérdések és válaszok párok szűrés környezetfüggő beszélgetések](../media/conversational-context/filter-question-and-answers-by-context.png)

A többszörös kapcsolja környezetet az első oszlop jelenik meg.

![! [A "környezet (előzetes verzió)" oszlop] (.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Az előző képen **#1** azt jelzi, hogy az az oszlop, amely azt jelzi, hogy az aktuális kérdés félkövérrel. A szülő kérdés legfelső elemére a sor. Alatta kérdése társított kérdés-válasz párt. Ezek az elemek azért választható, hogy azonnal nyissa meg a környezet más elemeket. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Egy meglévő kérdés-válasz párt követő kérdés hozzáadása

Az eredeti kérdésre **fiókom**, tartozik követő utasításokat, például **fiókokat, és jelentkezzen be**. 

![A "Fiókokat, és jelentkezzen be" válaszokat és követő utasításokat](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Követési kérdés hozzáadása egy meglévő kérdés-válasz párt, amely jelenleg nincs csatolva. A kérdés nem csatolt bármilyen kérdés-válasz párt, mert a jelenlegi nézet beállításait kell módosítani.

1. Egy meglévő kérdés-válasz párt követő kérdés, mutató hivatkozást, a kérdés-válasz párt sorának kijelölésével. A Surface manuális, keressen rá a **Kijelentkezés** csökkentése érdekében a listában.
1. Sorában **Kijelentkezés**, a a **válasz** oszlopában válassza **Hozzáadás követő parancssor**.
1. A mezőket a **követő parancssor (előzetes verzió)** előugró ablak, írja be a következő értékeket:

    |Mező|Érték|
    |--|--|
    |Megjelenítendő szöveg|Adja meg **kapcsolja ki az eszközt**. Ez az egyéni szöveg jelenjen meg a követési parancssorban.|
    |Csak helyi| Válassza ki ezt a jelölőnégyzetet. Válasz csak akkor, ha a kérdés megadja a környezetet adja vissza.|
    |Hivatkozás választ|Adja meg **használt bejelentkezési képernyő** található a meglévő kérdés-válasz párt.|


1.  Egy egyezést adja vissza. Ez a válasz, az azt követő, majd válassza ki és **mentése**. 

    ![A "Követő parancssor (előzetes verzió)" lap](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Miután hozzáadta a nyomon követési rendszer kéri, válassza ki a **mentéséhez és a vonat** a felső navigációs.
  
### <a name="edit-the-display-text"></a>A megjelenített szöveg szerkesztése 

Amikor egy utólagos kérés jön létre, és egy meglévő kérdés-válasz párt van megadva: a **válasz mutató hivatkozás**, megadhat új **szöveg megjelenítése**. Ez a szöveg nem lecseréli a meglévő kérdést, és nem adja hozzá egy új másik kérdést. Elkülönül ezeket az értékeket. 

1. A megjelenített szöveg szerkesztéséhez keresse meg és válassza ki a kérdést a **környezet** mező.
1. A kérdés sorában válassza ki a nyomon követési használatával, a válasz oszlopban. 
1. Válassza ki a szerkesztéséhez, és válassza ki a megjelenítendő szöveget **szerkesztése**.

    ![A Szerkesztés parancs a megjelenített szöveg](../media/conversational-context/edit-existing-display-text.png)

1. Az a **követő parancssor** előugró ablakban módosítsa a meglévő megjelenítendő szöveget. 
1. Ha befejezte a megjelenített szöveg szerkesztése, válassza ki **mentése**. 
1. A felső navigációs sávban **mentéséhez és a vonat**.


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Kérdések és válaszok párokat követő kérdés hozzáadása

Amikor új kérdést és választ kulcspár ad hozzá a Tudásbázis, minden párból követő kérdés, egy meglévő kérdésre adott kösse össze.

1. A Tudásbázis eszköztáron keresse meg és válassza a meglévő kérdés-válasz párt **fiókokat, és jelentkezzen be**. 

1. Az a **válasz** oszlop a kérdésre, válassza ki a **Hozzáadás követő parancssor**. 
1. A **követő parancssor (előzetes verzió)** , hozzon létre egy új utólagos kérés a következő értékek megadásával: 

    |Mező|Érték|
    |--|--|
    |Megjelenítendő szöveg|*Hozzon létre egy Windows-fiókot*. A nyomon követési rendszer megjeleníteni kívánt egyéni szöveg.|
    |Csak helyi|Válassza ki ezt a jelölőnégyzetet. Ez a válasz csak akkor, ha a kérdés megadja a környezetet adja vissza.|
    |Hivatkozás választ|A válasznak adja meg a következő szöveget:<br>*[Hozzon létre](https://account.microsoft.com/) egy Windows-fiókot egy új vagy meglévő e-mail-címmel*.<br>Mentse, és az adatbázis betanításához, ez a szöveg lesz átalakítva. |
    |||

    ![Hozzon létre egy új kérdés és válasz](../media/conversational-context/create-child-prompt-from-parent.png)


1. Válassza ki **új létrehozása**, majd válassza ki **mentése**. 

    Ez a művelet létrehoz egy új kérdés-válasz párt és a hivatkozások a kiválasztott kérdés, nyomon követési kérdés. A **környezet** mindkét kérdésre-oszlop azt jelzi, hogy követő parancssor kapcsolat. 

1. Válassza ki **beállítások megtekintéséhez**, majd válassza ki [ **Show környezet (előzetes verzió)** ](#show-questions-and-answers-with-context).

    Az új kérdésnek bemutatja, hogyan kapcsolódik.

    ![Hozzon létre egy új követő parancssor](../media/conversational-context/new-qna-follow-up-prompt.png)

    A szülő kérdést a választási lehetőségek egyikét jeleníti meg egy másik kérdést.

    ![! [, Ha mindkét kérdése van, a környezet oszlop jelzi, hogy a követő parancssor kapcsolat] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Miután hozzáadta a nyomon követési rendszer kéri, válassza ki a **mentéséhez és a vonat** a felső navigációs sávban.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Eseményt követő utasításokat tesztelése során több kapcsolja engedélyezése

Ha teszteli a kérdés megfogalmazásában követő kérni fogja a a **tesztelése** ablaktáblán válassza **engedélyezése több kapcsolja**, és írja be a kérdést. A válasz az követő utasításokat tartalmazza.

![A válasz tartalmazza a követő utasításokat](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Ha nem engedélyezi több kapcsolja be, a választ adja vissza, de követő utasításokat nem adja vissza.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>A kezdeti választ és követő utasításokat visszaadása egy JSON-kérelmi

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

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>A kezdeti választ és követő utasításokat visszaadása egy JSON-válasz

Az előző szakaszban a kért választ, és bármely követő utasításokat követve **fiókokat, és jelentkezzen be**. A válasz tartalmazza az azonnali adatokat, amelyek a következő helyen található *válaszokat [0] .context*, és a felhasználónak megjelenítendő szöveg. 

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
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
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

A `prompts` tömb biztosít a szöveg a `displayText` tulajdonság és a `qnaId` értéket. Ezek a válaszok megjelenítheti a beszélgetést a következő megjelenített választási folyamat, és küldje el a kiválasztott `qnaId` vissza a QnA Maker, a következő kérésben. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Egy JSON-kérelmi a követő utasításokat és a egy nem eredeti válasz visszaadása

Töltse ki a `context` objektum tartalmazza az előző környezet.

A következő JSON-kérelemben, az aktuális kérdés az *használata Windows Hello bejelentkezni* és volt az előző kérdésnél *fiókokat, és jelentkezzen be*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Egy JSON-választ a követő utasításokat és a egy nem eredeti válasz visszaadása

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>A Tudásbázis a QnA Maker Azonosítóval rendelkező lekérdezés

A kezdeti kérdésre adott válasz, a további utasításokat és az ahhoz társított `qnaId` adja vissza. Most, hogy az azonosító, a nyomon követési rendszer kérelem törzsében szereplő továbbíthatja ezt. Ha a kérés törzse tartalmazza a `qnaId`, és a környezeti objektumot (amely a korábbi QnA Maker tulajdonságokat tartalmazza), majd GenerateAnswer adja vissza a pontos kérdés azonosítója találja meg a választ a kérdés szövege által a rangsorolási algoritmust használata helyett. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Megjeleníteni a képernyőn megjelenő utasításokat, és az ügyfélalkalmazásban környezet küldése 

Kérések hozzáadta a Tudásbázis és a folyamat tesztelése a teszt panelt. Most szeretné használni ezeket az utasításokat az ügyfélalkalmazásban. A Bot Framework a kérések nem automatikusan megjelenik az ügyfélalkalmazások számára. Megjelenítheti az utasításokat a javasolt műveletek vagy gombok a választ a felhasználó lekérdezése az ügyfélalkalmazások részeként úgy, hogy ez többek között [Bot Framework minta](https://aka.ms/qnamakermultiturnsample) a kódban. Az ügyfélalkalmazás az aktuális QnA Maker Azonosítót és a felhasználó lekérdezése tárolására, és adja meg azokat a a [context objektumot az GenerateAnswer API](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) a következő felhasználói lekérdezés. 

## <a name="display-order-is-supported-in-the-update-api"></a>Megjelenítési sorrendjét a frissítés API-ban támogatott

A [szöveg és sorrendben jelennek meg](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), a JSON-válasz, a Szerkesztés által támogatott a [frissítés API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Tudásbázis létrehozása több kapcsolja utasításokat létrehozása API-val

Tudásbázis eset több kapcsolja utasításokat használatával hozhat létre a [QnA Maker API létrehozása](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Az utasításokat az ad hozzá a `context` tulajdonság `prompts` tömb. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Hozzáadhat vagy törölhet több kapcsolja utasításokat a frissítés API-val

Hozzáadhat, vagy törölje a használatával több kapcsolja utasításokat a [QnA Maker API-t frissítés](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Az utasításokat az ad hozzá a `context` tulajdonság `promptsToAdd` tömböt, és a `promptsToDelete` tömb. 


## <a name="next-steps"></a>További lépések

További információ erről környezetfüggő beszélgetések [párbeszédpanel minta](https://aka.ms/qnamakermultiturnsample) , illetve tájékozódjon részletesebben [fogalmi bot tervezése több kapcsolja beszélgetés](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Tudásbázis áttelepítése](../Tutorials/migrate-knowledge-base.md)

---
title: Több-turn beszélgetések – QnA Maker
titleSuffix: Azure Cognitive Services
description: A kérdésekkel és környezettel kezelheti a több fordulatot, más néven a robotot az egyik kérdésből a másikba. A többszörös bekapcsolás lehetősége van arra, hogy olyan háttérbeli beszélgetést lehessen használni, ahol az előző kérdés kontextusa befolyásolja a következő kérdést és választ.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 318df27ebb822f49c1f8881d0bf68ac7167dea36
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351295"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Követő kérések használata több kérdés–válasz párból álló beszélgetések létrehozásához

A nyomon követéssel és a kontextussal kezelheti a több fordulatot, más néven _több bekapcsolást_az egyik kérdésből a másikba.

Ha szeretné megtekinteni, hogyan működik a több funkció, tekintse meg az alábbi bemutató videót:

[![Multi – beszélgetés bekapcsolása QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Mi az a több fordulatos beszélgetés?

Néhány kérdés nem válaszolható meg egyetlen fordulatban sem. Amikor megtervezi az ügyfélalkalmazás (chat bot) beszélgetéseit, a felhasználók megtehetnek egy olyan kérdést, amelyet szűrni vagy finomítani kell a helyes válasz megállapítása érdekében. Ezt a folyamatot a lehetséges kérdések segítségével teheti meg, ha a felhasználót a *követő kérésekkel*mutatja be.

Amikor egy felhasználó kérdést tesz fel, QnA Maker visszaadja a választ _és_ a követő utasításokat. Ez a válasz lehetővé teszi, hogy választási lehetőségként bemutassa a követő kérdéseket. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Multi-turn beszélgetés a csevegő robottal

A többszörös bekapcsolással a csevegési bot a felhasználóval folytatott beszélgetést kezeli a végső válasz meghatározásához, ahogy az az alábbi képen is látható:

![Egy Többablakos párbeszédpanel, amely a felhasználóknak egy beszélgetésen keresztül útmutatást nyújt.](../media/conversational-context/conversation-in-bot.png)

Az előző képen egy felhasználó elindított egy beszélgetést **a fiókom beírásával**. A Tudásbázis három csatolt kérdés-válasz párral rendelkezik. A válasz pontosításához a felhasználó kiválasztja a Tudásbázis három választási lehetőségének egyikét. A kérdés (#1) három utólagos figyelmeztetést tartalmaz, amelyek a csevegési robotban három lehetőségként jelennek meg (#2). 

Ha a felhasználó kiválaszt egy beállítást (#3), a rendszer a finomítási beállítások (#4) következő listáját mutatja be. Ez a folyamat addig folytatódik (#5), amíg a felhasználó nem határozza meg a helyes, végleges választ (#6).

> [!NOTE]
> Az előző képen a **többszörös bekapcsolás engedélyezése** jelölőnégyzet be van jelölve, hogy biztosan megjelenjenek a promptok. 

### <a name="use-multi-turn-in-a-bot"></a>Több bekapcsoló használata a robotban

A környezetfüggő beszélgetés kezeléséhez módosítsa az ügyfélalkalmazás [kódját a robothoz való hozzáadásával](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). A kód hozzáadása lehetővé teszi a felhasználók számára, hogy meglássák az utasításokat.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Többfordulatú beszélgetés létrehozása a dokumentumok struktúrájából

Ha létrehoz egy tudásbázist, a **tudásbáziscikk feltöltése** szakasz megjeleníti az **URL-címek,. PDF vagy. docx fájlok jelölőnégyzetének többszörös kibontásának engedélyezése** beállítást. 

![A többszörös Kibontás engedélyezésének jelölőnégyzete](../media/conversational-context/enable-multi-turn.png)

Ha bejelöli ezt a beállítást egy importált dokumentumhoz, a többfordulatú beszélgetés a dokumentum szerkezetétől is utalhat. Ha ez a struktúra létezik, QnA Maker létrehozza az importálási folyamat részeként megjelenő kérdéseit és válaszait. 

A többszörös kapcsolási struktúra csak URL-címekből, PDF-fájlokból vagy DOCX-fájlokból következtethető ki. A struktúra példájának megtekintéséhez tekintse meg a [Microsoft Surface felhasználói manuális PDF-fájljának](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)képét. A PDF-fájl mérete miatt a QnA Maker erőforrás a (z) **b** (15 index) **keresési árképzési szintet** igényli, vagy nagyobb. 

![! [A felhasználói kézikönyv struktúrájának példája] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Ha importálja a PDF-dokumentumot, QnA Maker meghatározza, hogy a rendszer a struktúra követő figyelmeztetéseit határozza meg a beszélgetési folyamat létrehozásához. 

1. A QnA Maker területen válassza **a Tudásbázis létrehozása**elemet.
1. Meglévő QnA Maker szolgáltatás létrehozása vagy használata. Az előző Microsoft Surface-példában, mivel a PDF-fájl túl nagy méretű a kisebb rétegek számára, használjon egy QnA Maker szolgáltatást **b** (15 Indexes) **keresési szolgáltatással** , vagy nagyobb.
1. Adja meg a Tudásbázis nevét, például a **Surface Manual**nevet.
1. Jelölje be a **többszörös kinyerés engedélyezése URL-címekből,. PDF vagy. docx fájlokból** jelölőnégyzetet. 
1. Válassza ki a felület kézi URL-címét, **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Válassza a **saját kb létrehozása** gombot. 

    A Tudásbázis létrehozása után megjelenik a kérdés-válasz párok nézete.

## <a name="show-questions-and-answers-with-context"></a>Kérdések és válaszok megjelenítése a kontextusban

Csökkentse a megjelenő kérdés-válasz párokat, hogy csak a környezetfüggő beszélgetésekkel rendelkezők jelenjenek meg. 

Válassza a **megtekintési beállítások**, majd a **környezet megjelenítése (előzetes verzió)** lehetőséget. A lista a követő kérdéseket tartalmazó kérdés-válasz párokat jeleníti meg. 

![Kérdés-válasz párok szűrése környezetfüggő beszélgetések alapján](../media/conversational-context/filter-question-and-answers-by-context.png)

A többszörös kapcsolási környezet az első oszlopban jelenik meg.

![! [A "Context (előnézet)" oszlop] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Az előző képen a **#1** félkövér szöveget jelöl az oszlopban, amely az aktuális kérdést jelzi. A szülő kérdés a sorban lévő legfelső elem. Az alábbi kérdések a csatolt kérdés-válasz párok. Ezek az elemek kiválaszthatók, így azonnal átléphet a többi környezeti elemre. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Egy meglévő kérdés-válasz pár hozzáadása követő üzenetként

Az eredeti kérdés, **a fiókom**, a követési kérések, például a **fiókok és a bejelentkezés**. 

![A "fiókok és bejelentkezés" válaszok és követő kérések](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Olyan meglévő kérdés-válasz párokhoz adja hozzá a követési kérést, amely jelenleg nincs összekapcsolva. Mivel a kérdés nem kapcsolódik a kérdés-válasz párokhoz, a jelenlegi nézet beállítást módosítani kell.

1. Ha egy meglévő kérdés-válasz párokat szeretne felvenni egy követő parancssorba, válassza ki a kérdés-válasz párok sorát. A felszín manuális megjelenítéséhez keressen rá a **kijelentkezés** gombra a lista csökkentése érdekében.
1. A **kijelentkezés**sorában, a **Válasz** oszlopban válassza a **követő kérés hozzáadása**lehetőséget.
1. Adja meg a következő értékeket a **követési kérés (előzetes verzió)** előugró ablakának mezőiben:

    |Mező|Value|
    |--|--|
    |Szöveg megjelenítése|Adja meg **az eszköz kikapcsolását**. Ez a követési parancssorban megjelenítendő egyéni szöveg.|
    |Csak kontextus| Jelölje be ezt a jelölőnégyzetet. A rendszer csak akkor adja vissza a választ, ha a kérdés a kontextust adja meg.|
    |Hivatkozás a válaszra|Adja meg a **bejelentkezési képernyőt** a meglévő kérdés-válasz párok megkereséséhez.|


1.  Az egyik egyezést adja vissza. Válassza ki ezt a választ követő műveletként, majd kattintson a **Mentés**gombra. 

    ![A "követő figyelmeztetés (előzetes verzió)" oldal](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Miután hozzáadta a követő parancssort, válassza a **Mentés és betanítás** lehetőséget a felső navigációs sávon.
  
### <a name="edit-the-display-text"></a>A megjelenített szöveg szerkesztése 

Ha a rendszer egy követő kérdést hoz létre, és egy meglévő kérdés-válasz pár jelenik meg a **válaszra mutató hivatkozásként**, megadhat új **megjelenítendő szöveget**. Ez a szöveg nem helyettesíti a meglévő kérdést, és nem ad hozzá új alternatív kérdést. Ezek az értékek eltérnek egymástól. 

1. A megjelenítendő szöveg szerkesztéséhez keresse meg és válassza ki a kérdést a **környezeti** mezőben.
1. A kérdés sorában válassza ki a követő kérdést a válasz oszlopban. 
1. Jelölje ki a szerkeszteni kívánt megjelenítendő szöveget, majd válassza a **Szerkesztés**lehetőséget.

    ![A megjelenített szöveg szerkesztési parancsa](../media/conversational-context/edit-existing-display-text.png)

1. A **követő üzenet** előugró ablakában módosítsa a meglévő megjelenítendő szöveget. 
1. Ha elkészült a megjelenítési szöveg szerkesztésével, válassza a **Mentés**lehetőséget. 
1. A felső navigációs sávban **mentse és betanítása**.


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

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Új kérdés-válasz pár hozzáadása követő üzenetként

Amikor új kérdés-válasz párokat ad hozzá a tudásbázishoz, az egyes párokat nyomon követő rákérdezésként egy meglévő kérdéssel kell összekapcsolni.

1. A Tudásbázis eszköztárán keresse meg és válassza ki a meglévő kérdés-válasz párokat a fiókok számára, és **Jelentkezzen**be. 

1. A kérdéshez tartozó **Válasz** oszlopban válassza a **következő üzenet hozzáadása**lehetőséget. 
1. A követő **üzenet (előzetes verzió)** alatt hozzon létre egy új követő kérdést a következő értékek megadásával: 

    |Mező|Value|
    |--|--|
    |Szöveg megjelenítése|*Hozzon létre egy Windows-fiókot*. A követő parancssorban megjelenítendő egyéni szöveg|
    |Csak kontextus|Jelölje be ezt a jelölőnégyzetet. A rendszer csak akkor adja vissza ezt a választ, ha a kérdés a kontextust adja meg.|
    |Hivatkozás a válaszra|Válaszként adja meg a következő szöveget:<br>*[Hozzon létre](https://account.microsoft.com/) egy Windows-fiókot új vagy meglévő e-mail-fiókkal*.<br>Az adatbázis mentésekor és betanításakor ez a szöveg lesz konvertálva. |
    |||

    ![Új kérdés és válasz létrehozása](../media/conversational-context/create-child-prompt-from-parent.png)


1. Válassza az **új létrehozása**, majd a **Mentés**lehetőséget. 

    Ez a művelet létrehoz egy új kérdés-válasz párokat, és a kiválasztott kérdést követő üzenetként csatolja. A **környezeti** oszlop mindkét kérdés esetében egy követő prompt-kapcsolatot jelez. 

1. Válassza a **megtekintési beállítások**, majd a [**környezet megjelenítése (előzetes verzió)** ](#show-questions-and-answers-with-context)lehetőséget.

    Az új kérdés bemutatja, hogyan kapcsolódik egymáshoz.

    ![Új követő figyelmeztetés létrehozása](../media/conversational-context/new-qna-follow-up-prompt.png)

    A szülő kérdés egy új kérdést jelenít meg az egyik lehetőség közül.

    ![! [A környezeti oszlop mindkét kérdés esetében egy követő kérdés-kapcsolatot jelez] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Miután hozzáadta a követő parancssort, válassza a **Mentés és betanítás** lehetőséget a felső navigációs sávon.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Több bekapcsolás engedélyezése a követő kérések tesztelése során

Ha a **teszt** ablaktáblán a követési kérésekkel kapcsolatos kérdést teszteli, válassza a **többszörös bekapcsolás engedélyezése**lehetőséget, majd adja meg a kérdését. A válasz tartalmazza a követő utasításokat.

![A válasz tartalmazza a követő utasításokat](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Ha nem engedélyezi a többszörös bekapcsolást, a rendszer visszaadja a választ, de a rendszer nem adja vissza a következő utasításokat.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Egy JSON-kérelem, amely egy kezdeti választ ad vissza, és követő kéréseket küld

Az üres `context` objektum használatával kérje meg a választ a felhasználó kérdésére, és vegyen fel követő utasításokat. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Egy kezdeti válasz és követő kérések visszaadására szolgáló JSON-válasz

Az előző szakaszban választ kértek, és a rendszer a **fiókokat és a bejelentkezést**követő utasításokat kér. A válasz tartalmazza a kérdéses adatokat, amelyek a következő *válaszokban találhatók: [0]. Context*, valamint a felhasználónak megjelenítendő szöveg. 

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

A `prompts` tömb szövegeket biztosít a `displayText` tulajdonságban és a `qnaId` értékben. Ezeket a válaszokat a beszélgetési folyamat következő megjelenő lehetőségeiként jelenítheti meg, majd a kijelölt @no__t – 0 visszaküldheti a QnA Makernak a következő kérelemben. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Egy JSON-kérelem, amely nem kezdeti választ ad vissza, és követő kéréseket küld

Töltse ki a `context` objektumot az előző környezet belefoglalásához.

A következő JSON-kérelemben az aktuális kérdés a *Windows Hello használata a bejelentkezéshez* , az előző kérdés pedig a *fiókok és a bejelentkezés*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Egy nem kezdeti válasz és követő kérések visszaadására szolgáló JSON-válasz

Az QnA Maker _GenerateAnswer_ JSON-válasz tartalmazza a követő utasításokat az `answers` objektum első eleme `context` tulajdonságában:

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>A Tudásbázis lekérdezése a QnA Maker azonosítója alapján

Az első kérdés válaszában a rendszer minden követő kérdést és a hozzá tartozó `qnaId` értéket adja vissza. Most, hogy már rendelkezik AZONOSÍTÓval, átadhatja ezt a követési kérés törzsében. Ha a kérelem törzse tartalmazza a `qnaId` és a környezeti objektumot (amely az előző QnA Maker tulajdonságokat tartalmazza), akkor a GenerateAnswer az azonosító alapján a pontos kérdést adja vissza ahelyett, hogy a rangsorolási algoritmus használatával megkeresi a választ a kérdés szövege alapján. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>A promptok megjelenítése és a kontextus küldése az ügyfélalkalmazás számára 

A Tudásbázisban felvette a kérdéseit, és tesztelte a folyamatot a teszt ablaktáblán. Most ezeket az utasításokat kell használnia az ügyfélalkalmazás számára. A bot Framework esetében a promptok nem jelennek meg automatikusan az ügyfélalkalmazások számára. A promptokat javasolt műveletként vagy gombként jelenítheti meg a válasz részeként a felhasználó lekérdezésében az ügyfélalkalmazások számára, ehhez a [bot Framework mintát](https://aka.ms/qnamakermultiturnsample) is beleértve a kódban. Az ügyfélalkalmazás tárolja az aktuális QnA Maker azonosítót és a felhasználói lekérdezést, majd átadja azokat a [GENERATEANSWER API környezeti objektumában](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) a következő felhasználói lekérdezéshez. 

## <a name="display-order-is-supported-in-the-update-api"></a>A megjelenítési sorrend támogatott a frissítési API-ban

A JSON-válaszban visszaadott [szöveg és megjelenítési sorrend](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)is támogatott a [frissítési API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)-val való szerkesztéshez. 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Tudásbázis létrehozása több bekapcsolási kéréssel a Create API használatával

A [QnA Maker Create API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)használatával több bekapcsolási kéréssel is létrehozhat egy tudásbázist. A rendszer a `context` tulajdonság `prompts` tömbben való hozzáadását kéri. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Több bekapcsolási kérés hozzáadása vagy törlése a frissítési API-val

A [QnA Maker Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)használatával több bekapcsolási kérést is hozzáadhat vagy törölhet.  A rendszer a `context` tulajdonság `promptsToAdd` tömbben és a `promptsToDelete` tömbben adja hozzá az utasításokat. 


## <a name="next-steps"></a>További lépések

További információ a [párbeszédpanel](https://aka.ms/qnamakermultiturnsample) környezetfüggő beszélgetésekről, vagy további információ a [fogalmi robot kialakításáról a több-turn beszélgetések esetében](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Tudásbázis migrálása](../Tutorials/migrate-knowledge-base.md)

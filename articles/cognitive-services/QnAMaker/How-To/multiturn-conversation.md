---
title: Többfordulatos beszélgetések - QnA Maker
description: A kérdésekkel és a környezetkörnyezetvel kezelheti a több fordulatot, az úgynevezett többfordulatot, a robotot egyik kérdésből a másikba. Multi-turn az a képesség, hogy egy oda-vissza beszélgetés, ahol az előző kérdés összefüggésben befolyásolja a következő kérdésre és válaszra.
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 8ef244e1b6baf480189bb90ea5ff53138a6f377a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261465"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Követő kérések használata több kérdés–válasz párból álló beszélgetések létrehozásához

A nyomon követési kérdések és a környezet segítségével kezelheti a több fordulatot, az úgynevezett _többfordulatot_a robothoz az egyik kérdésből a másikba.

A többfordulatos működés megtekintéséhez tekintse meg a következő bemutató videót:

[![Többfordulatos beszélgetés a QnA Maker ben](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Mi az a többfordulós beszélgetés?

Néhány kérdésre nem lehet egy menetben válaszolni. Az ügyfélalkalmazással (csevegőrobotokkal) folytatott beszélgetések tervezésekor a felhasználó feltehet egy kérdést, amelyet szűrni vagy finomítani kell a helyes válasz meghatározásához. Ezt a kérdést a felhasználó nyomon követési kérdéseinek bemutatásával teheti *lehetővé.*

Amikor egy felhasználó feltesz egy kérdést, a QnA Maker visszaküldi a választ _és_ a nyomon követési utasításokat. Ez a válasz lehetővé teszi, hogy a nyomon követési kérdéseket választási lehetőségként mutassa be.

> [!CAUTION]
> A többfordulatos kérések nem kerülnek kinyerésre a GYIK-dokumentumokból. Ha többfordulatos kivonásra van szüksége, távolítsa el a QnA-párokat GYIK-ként megjelölt kérdőjeleket.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Példa többfordulatos beszélgetésre a csevegőrobottal

A multi-turn, a chat bot kezeli a beszélgetést a felhasználóval, hogy meghatározza a végső választ, ahogy az a következő képen látható:

![Többfordulatos párbeszédablak, amely nek ikraatta a felhasználót a beszélgetés](../media/conversational-context/conversation-in-bot.png)

Az előző képen a felhasználó a **Saját fiók**elemre lépve indított el beszélgetést. A tudásbázis három összekapcsolt kérdés-válasz párral rendelkezik. A válasz finomításához a felhasználó a tudásbázis három választási lehetősége közül választ. A kérdés (#1), három nyomon követési kéri, amelyek megjelennek a chat bot három lehetőség (#2).

Amikor a felhasználó kiválaszt egy beállítást (#3), megjelenik a finomítási beállítások következő listája (#4). Ez a sorozat addig folytatódik (#5), amíg a felhasználó meg nem határozza a helyes, végső választ (#6).


### <a name="use-multi-turn-in-a-bot"></a>Használja multi-turn egy bot

A tudásbázis közzététele után **kiválaszthatja** a Robot létrehozása gombot a QnA Maker robot azure-beli robotszolgáltatásba való üzembe helyezéséhez. A kérések megjelennek a csevegőklinek, amelyeket a robothoz engedélyezett.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Többfordulatos beszélgetés létrehozása a dokumentum szerkezetéből

Tudásbázis létrehozásakor a **TUDÁSBÁZIS feltöltése** szakasz ban megjelenik a **Többfordulatos kinyerés engedélyezése URL-címekről, .pdf vagy .docx fájlokból** jelölőnégyzet.

![A többfordulatos kihúzás engedélyezéséhez jelölőnégyzet](../media/conversational-context/enable-multi-turn.png)

Ha ezt a beállítást választja, a QnA Maker kinyeri a dokumentumszerkezetben lévő hierarchiát. A hierarchia a következő kérések követésére konvertálódik, és a hierarchia gyökere szülő QnA-ként szolgál. Egyes dokumentumokban a hierarchia gyökerében nincs olyan tartalom, amely válaszként szolgálhatna, megadhatod az "Alapértelmezett válaszszöveget", amelyet helyettesítő válaszszövegként használhataz ilyen hierarchiák kibontásához.

A többfordulatos struktúra csak URL-ekből, PDF-fájlokból vagy DOCX-fájlokból következtethető ki. A szerkezetre vonatkozó példa a [Microsoft Surface felhasználói kézikönyve PDF-fájljának](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)képének megtekintése .

![! [Példa a szerkezet a felhasználói kézikönyv] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Saját többfordulatos dokumentum létrehozása

Ha többfordulatos dokumentumot hoz létre, kérjük, tartsa szem előtt az alábbi irányelveket:

* A hierarchiát a címsorok és alfejlécek segítségével jelölje. Például h1 jelöli a szülő QnA és h2 jelöli a QnA, hogy kell venni, mint a prompt. A későbbi hierarchia kis címsorméretével jelölheti a későbbi hierarchiát. Ne használjon stílust, színt vagy más mechanizmust a dokumentum szerkezetének sugallására, a QnA Maker nem bontja ki a többszörös fordulatos utasításokat.

* A címsor első karakterét nagybetűvel kell elévíteni.

* Ne hagyjon kérdőjellel egy `?`címsort.

* A [mintadokumentum](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) példaként használhatja saját többfordulatos dokumentumának létrehozását.

### <a name="adding-files-to-a-multi-turn-kb"></a>Fájlok hozzáadása többfordulatos KB-hoz

Amikor hierarchikus dokumentumot ad hozzá, a QnA Maker meghatározza a struktúra nyomon követési kéréseit a társalgási folyamat létrehozásához.

1. A QnA Maker programban válasszon ki egy meglévő tudásbázist, amely et az **URL-címek, .pdf vagy .docx fájlok többfordulatos kinyerésének engedélyezése** funkcióval hoztak létre. Engedélyezve.
1. Nyissa meg a **Beállítások** lapot, és jelölje ki a hozzáadni kívánt fájlt vagy URL-címet.
1. **Mentse és tanítsa** be a tudásbázist.

> [!Caution]
> Az exportált TSV- vagy XLS-alapú tudásbázisfájl új vagy üres tudásbázis adatforrásaként való használatának támogatása nem támogatott. A fájltípus **importálásához** a QnA Maker portál **Beállítások** lapjáról kell importálnia, hogy exportált többfordulatos utasításokat adjon a tudásbázishoz.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Tudásbázis létrehozása többfordulatos kérdésekkel a Create API-val

A [QnA Maker Create API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)segítségével többfordulatos kérdésekkel hozhat létre tudásesetet. A kérések hozzáadnak `context` a `prompts` tulajdonság tömbjéhez.

## <a name="show-questions-and-answers-with-context"></a>Kérdések és válaszok megjelenítése kontextusban

Csökkentse a megjelenített kérdés-válasz párokat csak a környezetfüggő beszélgetésekkel rendelkezőkre.

Válassza **a Nézet beállításai lehetőséget,** majd a Környezet **megjelenítése**lehetőséget. A lista azokat a kérdések és válaszpárokat jeleníti meg, amelyek nyomon követési utasításokat tartalmaznak.

![Kérdés-válasz párok szűrése környezetfüggő beszélgetések szerint](../media/conversational-context/filter-question-and-answers-by-context.png)

A többfordulatos környezet az első oszlopban jelenik meg.

![! [A "Környezet (PREVIEW)" oszlop] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Az előző képen **a #1** az oszlopban lévő félkövér szöveget jelöli, ami az aktuális kérdést jelöli. A szülőkérdés a sor legfelső eleme. Az alatta lévő kérdések a kapcsolódó kérdés-válasz párok. Ezek az elemek választhatók, így azonnal átléphet a többi környezeti elemre.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Meglévő kérdés-válasz pár hozzáadása nyomon követési kérdésként

Az eredeti kérdés, **A fiók**, nyomon követési utasításokat, például a Fiókok és **a bejelentkezés.**

![A "Fiókok és bejelentkezés" válaszok és nyomon követési utasítások](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Adjon hozzá egy nyomon követési kérdést egy meglévő kérdés-válasz párhoz, amely jelenleg nincs összekapcsolva. Mivel a kérdés nem kapcsolódik egyetlen kérdés-válasz párhoz sem, az aktuális nézetbeállítást módosítani kell.

1. Ha egy meglévő kérdés-válasz párt szeretne nyomon követési kérdésként összekapcsolni, válassza ki a kérdés-válasz pár sorát. A Surface kézikönyvben keresse meg a **Kijelentkezés** kifejezést a lista csökkentéséhez.
1. A **Kijelentkezés**sorában a **Válasz** oszlopban válassza **a Nyomon követési kérdés hozzáadása**lehetőséget.
1. A **Nyomon követési kérdés** előugró ablakmezőiben adja meg a következő értékeket:

    |Mező|Érték|
    |--|--|
    |Megjelenítendő szöveg|Írja be **Az eszköz kikapcsolása**lehetőséget. Ez az egyéni szöveg jelenik meg a nyomon követési kérdés.|
    |Csak környezet| Jelölje be ezt a jelölőnégyzetet. A válasz csak akkor ad vissza, ha a kérdés kontextust ad meg.|
    |Válasz: válasz|Írja be: A meglévő kérdés-válasz pár megkereséséhez **használja a bejelentkezési képernyőt.**|


1.  Egy találat visszaadása. Válassza ki ezt a választ nyomon követésként, majd válassza a **Mentés gombot.**

    ![A "Nyomon követési kérdés (PREVIEW)" oldal](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Miután hozzáadta a követési kérdést, válassza a **Mentés és betanítás** lehetőséget a felső navigációs sávon.

### <a name="edit-the-display-text"></a>A megjelenített szöveg szerkesztése

Amikor nyomon követési kérdést hoz létre, és egy meglévő kérdés-válasz párt **ad**meg hivatkozásként, új **Megjelenítendő szöveget**is beírhat. Ez a szöveg nem helyettesíti a meglévő kérdést, és nem ad hozzá új alternatív kérdést. Ez elkülönül ezektől az értékektől.

1. A megjelenített szöveg szerkesztéséhez keresse meg és jelölje ki a kérdést a **Környezet** mezőben.
1. A kérdés sorában válassza ki a nyomon követési kérdést a válaszoszlopban.
1. Jelölje ki a szerkesztendő megjelenítendő szöveget, majd válassza a **Szerkesztés**lehetőséget.

    ![A megjelenített szöveg Szerkesztés parancsa](../media/conversational-context/edit-existing-display-text.png)

1. A **Nyomon követési kérdés** előugró ablakában módosítsa a meglévő megjelenítendő szöveget.
1. Ha végzett a megjelenítendő szöveg szerkesztésével, válassza a **Mentés gombot.**
1. A felső navigációs sávon a **Mentés és a betanítás**lehetőség.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Új kérdés-válasz pár hozzáadása nyomon követési kérdésként

Amikor új kérdés-válasz párt ad hozzá a tudásbázishoz, minden párt egy meglévő kérdéshez kell kapcsolni nyomon követési kérdésként.

1. A Tudásbázis eszköztáron keresse meg és jelölje ki a fiókok és a bejelentkezés meglévő kérdés-válasz **párját.**

1. A kérdés **Válasz** oszlopában válassza **a Nyomon követési kérdés hozzáadása**lehetőséget.
1. A **Követési kérdés (PREVIEW)** csoportban hozzon létre egy új követési kérdést a következő értékek megadásával:

    |Mező|Érték|
    |--|--|
    |Megjelenítendő szöveg|*Hozzon létre egy Windows-fiókot*. A nyomon követési kérdésben megjelenítendő egyéni szöveg.|
    |Csak környezet|Jelölje be ezt a jelölőnégyzetet. Ez a válasz csak akkor ad vissza, ha a kérdés kontextust ad meg.|
    |Válasz: válasz|Válaszként írja be a következő szöveget:<br>* [Hozzon létre](https://account.microsoft.com/) egy Windows-fiókot új vagy meglévő e-mail fiókkal.*<br>Az adatbázis mentésekor és betanításakor a program konvertálja ezt a szöveget. |
    |||

    ![Új kérdés-felelés létrehozása](../media/conversational-context/create-child-prompt-from-parent.png)


1. Válassza **az Új létrehozása**lehetőséget, majd a Mentés **gombot.**

    Ez a művelet új kérdés-válasz párt hoz létre, és a kiválasztott kérdést nyomon követési üzenetként kapcsolja össze. A **Környezet** oszlop mindkét kérdésnél egy nyomon követési gyors kapcsolatot jelez.

1. Válassza a **Nézet beállításai lehetőséget,** majd a [**Környezet megjelenítése (PREVIEW)**](#show-questions-and-answers-with-context)lehetőséget.

    Az új kérdés megmutatja, hogyan kapcsolódik egymáshoz.

    ![Új nyomon követési kérdés létrehozása](../media/conversational-context/new-qna-follow-up-prompt.png)

    A szülőkérdés egy új kérdést jelenít meg az egyik választási lehetőségeként.

    ![! [A Környezet oszlop mindkét kérdésnél egy nyomon követési gyors kapcsolatot jelöl] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Miután hozzáadta a követési kérdést, válassza a **Mentés és betanítás** lehetőséget a felső navigációs sávon.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Többfordulatos megfordulás a nyomon követési kérések tesztelése során

Ha a **tesztablakban** nyomon követési kérdésekkel teszteli a kérdést, a válasz tartalmazza a nyomon követési utasításokat.

![A válasz tartalmazza a nyomon követési](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>JSON-kérés az első válasz és nyomon követési kérdések visszaküldésére

Az üres `context` objektum segítségével választ kérhet a felhasználó kérdésére, és nyomon követési utasításokat is tartalmazhat.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>JSON-válasz a kezdeti válasz és nyomon követési utasítások visszaadása érdekében

Az előző szakasz választ kért, és minden nyomon követési kérdést a **Fiókokhoz és a bejelentkezéshez.** A válasz tartalmazza a gyors információkat, amelyek a *válaszokban találhatók[0].context*, és a felhasználó számára megjelenítendő szöveget.

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

A `prompts` tömb szöveget `displayText` ad `qnaId` a tulajdonságban és az értékben. Ezeket a válaszokat a beszélgetési folyamat következő megjelenített `qnaId` választási lehetőségeiként jelenítheti meg, majd a következő kérelemben visszaküldheti a kiválasztottat a QnA Makernek.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>JSON-kérelem nem kezdeti válasz és nyomon követési kérés visszaküldésére

Töltse `context` ki az objektumot az előző környezetben.

A következő JSON-kérelemben az aktuális kérdés a *Bejelentkezés a Windows Hello használata,* az előző kérdés pedig a fiókok és a *bejelentkezés.*

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>JSON-válasz a nem kezdeti válasz és nyomon követési kérdések visszaadására

A QnA Maker _GenerateAnswer_ JSON válasza tartalmazza az `context` `answers` objektum első elemének tulajdonságában található nyomon követési utasításokat:

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
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>A tudásbázis lekérdezése a QnA Maker azonosítójával

Ha egyéni alkalmazást hoz létre a többfordulatos funkcióval. Az első kérdés válasza, minden nyomon követési utasításokat `qnaId` és a kapcsolódó vissza. Most, hogy rendelkezik az azonosítóval, ezt átadhatja a nyomon követési parancs kéréstörzsében. Ha a kérelem `qnaId`törzse tartalmazza a , és a context objektumot (amely tartalmazza az előző QnA Maker tulajdonságokat), akkor generateanswer ad vissza a pontos kérdést azonosító, ahelyett, hogy a rangsor algoritmus, hogy megtalálja a választ a kérdés szövegét.


## <a name="display-order-is-supported-in-the-update-api"></a>A megjelenítési sorrend támogatott az Update API-ban

A JSON-válaszban visszaadott [megjelenítési szöveget és megjelenítési sorrendet](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)az [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)támogatja szerkesztésre.

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Többfordulatos kérések hozzáadása vagy törlése az Update API-val

A [QnA Maker Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)segítségével többfordulatos utasításokat adhat hozzá vagy törölhet.  A kérések hozzáadnak `context` a `promptsToAdd` tulajdonság tömbjéhez és a `promptsToDelete` tömbhöz.

## <a name="export-knowledge-base-for-version-control"></a>Tudásbázis exportálása verziókövetéshez

A QnA Maker támogatja a verziókövetést azáltal, hogy többfordulatos beszélgetési lépéseket is betesz az exportált fájlba.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [párbeszédablakból](https://aka.ms/qnamakermultiturnsample) származó kontextuális beszélgetésekről, vagy tudjon meg többet [a többfordulatos beszélgetések koncepcionális robottervezéséről.](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)

> [!div class="nextstepaction"]
> [Tudásbázis migrálása](../Tutorials/migrate-knowledge-base.md)

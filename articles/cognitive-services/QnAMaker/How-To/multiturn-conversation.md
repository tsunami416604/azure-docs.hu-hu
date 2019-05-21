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
ms.date: 05/14/2019
ms.author: diberry
ms.openlocfilehash: f12b55e9b00e933e13f84832b8cc36267a1da05f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954868"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Hozzon létre egy témakör több hellyé követő utasításokat használatával

Követő utasításokat és a környezet használata kezelheti a több bekapcsolja, más néven _több kapcsolja_, egy másikra egy kérdést a robotja.

Ismerje meg a [bemutató videó](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Mi az a többszörös kapcsolja beszélgetés?

Bizonyos típusú beszélgetés egyetlen menetben nem lehet végrehajtani. Amikor az ügyfél-alkalmazás (csevegőrobot) beszélgetések, a felhasználó előfordulhat, hogy kérdés feltevése, amely a szűrt vagy is, hogy megállapítsuk a megfelelő választ kell. Ez a folyamat a kérdések keresztül lehetséges rendelkező felhasználó szabályzatkérelem **követő kéri**.

Ha a felhasználó kéri a kérdést, QnA Maker adja vissza a választ _és_ a további utasításokat. Ez lehetővé teszi lehetővé a választható lehetőségként követő kérdéseket. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Példa a többszörös kapcsolja beszélgetést csevegőrobot

Csevegőrobotot kezeli a beszélgetést, kérdést a kérdés, a felhasználót, hogy a végső válasz határozza meg.

![A természetes nyelvi flowban beszélgetés állapot kezelése egy több kapcsolja párbeszédpanel-rendszerben azáltal, hogy folytatja a beszélgetés lehetőségeket mutatják be, a válaszok belül utasításokat.](../media/conversational-context/conversation-in-bot.png)

A fenti ábrán a felhasználó kérdését a válasz visszaküldése előtt finomítására van szüksége. A Tudásbázis következő a kérdés (1), négy követő utasításokat, mint négy lehetőségek (2) a csevegőrobot megjelenő van. 

Amikor a felhasználó kiválasztja az egyik lehetőséget (3), majd pontosítását lehetőségek (4) következő listája jelennek meg. Ez is folytassa az eljárást (5) a megfelelő és egyben utolsó választ (6) határozza meg.

Az ügyfélalkalmazás kezelheti a környezetfüggő beszélgetés módosítani szeretné.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>Hozzon létre több kapcsolja beszélgetés dokumentum szerkezete
Tudásbázis létrehozása, ha egy nem kötelező – a jelölőnégyzetet, több kapcsolja kinyerési jelenik meg. Ha ezt a beállítást, amikor importálja egy dokumentum, a több kapcsolja beszélgetés is implicit a struktúrát a. Ha létezik struktúra, QnA Maker az Ön hozza létre a követő parancssor QnA párok. Több kapcsolja struktúra csak URL-címek, PDF vagy DOCX következtetni lehet fájlokat. 

Az alábbi képen egy Microsoft Surface- [PDF-fájl](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) célja, hogy manuális használható. 

![! [Ha importálja egy dokumentum, környezetfüggő beszélgetés is felelősségét a struktúrát a. Ha létezik struktúra, QnA Maker hoz létre a követő parancssor QnA párok, a dokumentum importálás részeként.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

A PDF-dokumentum importálásakor a QnA Maker követő utasításokat a struktúrát, természetes nyelvi folyamat létrehozása a határozza meg. 

![! [A PDF-dokumentum importálásakor a QnA Maker meghatározza, hogy a struktúrát, természetes nyelvi folyamat létrehozása a további utasításokat. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="show-questions-and-answers-with-context"></a>Kérdések és válaszok kontextusú megjelenítése

1. Csökkentse a kérdés és válasz párok korlátoznia környezetfüggő beszélgetések az megjelenik. Válassza ki **beállítások megtekintéséhez**, majd **Show környezet (előzetes verzió)**. A lista üres lesz, amíg nem az első kérdést és választ pár és követési kéri. 

    ![Szűrheti a kérdés, és válaszoljon pár környezetfüggő beszélgetések által](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Új kérdés-válasz párt követő parancssor hozzáadása

1. Válassza ki **hozzáadása kérdés-válasz párt**. 
1. Adja meg a kérdés új szövegét `Give feedback.` , választ `What kind of feedback do you have?`.

1. Az a **válasz** oszlop a kérdésre, válassza ki a **Hozzáadás követő parancssor**. 
1. A **követő parancssor (előzetes verzió)** előugró ablak lehetővé teszi, hogy egy meglévő kérdés kereséséhez, vagy adjon meg egy másik kérdést. Hozzon létre egy új parancssort, írja be a következő értékeket: 

    |Szövegmező|Value|
    |--|--|
    |**Megjelenítendő szöveg**|`Feedback on an QnA Maker service`|
    |**Hivatkozás választ**|`How would you rate QnA Maker??`|
    |||

    ![Hozzon létre új parancssor kérdések és válaszok](../media/conversational-context/create-child-prompt-from-parent.png)

1. Ellenőrizze **csak helyi**. A **csak helyi** beállítás azt jelzi, hogy a felhasználó szöveges érthető lehet _csak_ Ha adja meg az előző kérdésre adott válaszként. Ebben a forgatókönyvben az adatkérési szöveget értelmetlen bármely, egy különálló kérdést, csak logikus az előző kérdésnél környezetéből.
1. Válassza ki **új létrehozása** majd **mentése**. 

    Létrehozott egy új kérdés-válasz párt, és a kapcsolódó követési kérdés, a kiválasztott kérdés. A **környezet** mindkét kérdésre-oszlop azt jelzi, hogy követő parancssor kapcsolat. 

    ![! [A környezet oszlop mindkét kérdése van, az azt jelzi, hogy követő parancssor kapcsolat.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Válassza ki **Hozzáadás követő parancssor** számára a `Give feedback` kérdés hozzáadása egy másik követő parancssor. Ekkor megnyílik a **követő parancssor (előzetes verzió)** előugró ablak.

1. Hozzon létre egy új parancssort, írja be a következő értékeket:

    |Szövegmező|Value|
    |--|--|
    |**Megjelenítendő szöveg**|`Feedback on an existing feature`|
    |**Hivatkozás választ**|`Which feature would you like to give feedback on?`|
    |||

1. Ellenőrizze **csak helyi**. A **csak helyi** beállítás azt jelzi, hogy a felhasználó szöveges érthető lehet _csak_ Ha adja meg az előző kérdésre adott válaszként. Ebben a forgatókönyvben az adatkérési szöveget értelmetlen bármely, egy különálló kérdést, csak logikus az előző kérdésnél környezetéből.

1. Kattintson a **Mentés** gombra. 

    Ezzel létrehozott egy új kérdést, és csatolva a kérdést, követő kérdés feltevése a `Give feedback` kérdést.
    
    Ezen a ponton a legfontosabb kérdés rendelkezik-e az előző kérdésre tetszett két követő utasításokat `Give feedback`.

    ![! [A legfontosabb kérdés van ezen a ponton a tetszését az előző kérdésre a "Visszajelzés" két követő utasításokat is.] (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. Válassza ki **mentéséhez és a vonat** az új kérdése van a Tudásbázis betanításához. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Meglévő kérdés-válasz párt követő kérdés hozzáadása

1. Ha azt szeretné, összekapcsolása egy meglévő QnA pár, nyomon követési kérdés, válassza a sor számára a kérdés és válasz párt.
1. Válassza ki **Hozzáadás követő parancssor** abban a sorban.
1. Az a **követő parancssor (előzetes verzió)** előugró ablak, írja be a válasz szöveget a keresőmezőbe. Minden megfelelő elemet adja vissza. Válassza ki a választ, az azt követő szeretne, majd ellenőrizze **csak helyi**, majd **mentése**. 

    ![Keresse meg a követési rendszer mutató hivatkozást a meglévő válasz, válasz párbeszédpanel használatával a válasz szövegét.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

    Miután hozzáadta a nyomon követési rendszer kéri, akkor ne felejtse el bejelölni **mentéséhez és a vonat**.
  
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

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>A QnA beállítása minden követő első vizsgálat kéri

Amikor a a kérdés megfogalmazásában ellenőrzési tesztelés kéri a **teszt** ablaktáblán válassza **engedélyezése több kapcsolja**, és írja be a kérdést. A válasz az követő utasításokat tartalmazza.

![Ha teszteli a kérdést a ablaktáblán, a válasz tartalmazza a további utasításokat.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON-kérelem a kezdeti válasz és követő utasításokat visszaadása

Használja az üres `context` objektumot a felhasználó kérdésre adott válasz kérése és követő utasításokat tartalmazza. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
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
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way they like. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

A `prompts` tömb biztosít a szöveg a `displayText` tulajdonság és a `qnaId` értékét, így ezek a válaszok megjelenítheti a következő megjelenített választható lehetőségként a beszélgetés folyamatot. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON-kérelmi adhatja vissza nem eredeti válasz és követő utasításokat

Töltse ki a `context` objektum tartalmazza az előző környezetben.

A következő JSON-kérelemben, az aktuális kérdés az `Use Windows Hello to sign in` és volt az előző kérdésnél `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
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
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Megjelenítés, üzenetek és a környezet küldését az ügyfélalkalmazásban 

Ha hozzáadott utasításokat a Tudásbázisban, és a folyamat tesztelése a ablaktáblán, az utasításokat fog nem indul el automatikusan jelenik meg az ügyfélalkalmazások számára. Megjelenítheti az utasításokat a javasolt műveletek vagy gombok a választ a felhasználó lekérdezés részeként az ügyfél alkalmazások együtt ez [Bot Framework minta](https://aka.ms/qnamakermultiturnsample) a kódban. Az ügyfélalkalmazás az aktuális QnA-Azonosítót és a felhasználó lekérdezése tárolására, és adja meg azokat a a [context objektumot az GenerateAnswer API](#json-request-to-return-non-initial-answer-and-follow-up-prompts) a következő felhasználói lekérdezés.

## <a name="display-order-supported-in-api"></a>Megjelenítési sorrendjét támogatott API-ban

A megjelenítési sorrendjét, a JSON-válaszban visszaadott szerkesztésre, csak az API által támogatott. 

## <a name="next-steps"></a>További lépések

További információ a környezetfüggő beszélgetések a [párbeszédpanel minta](https://aka.ms/qnamakermultiturnsample) vagy tudjon meg többet [fogalmi bot tervezése több kapcsolja beszélgetés](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Tudásbázis áttelepítése](../Tutorials/migrate-knowledge-base.md)

---
title: 'Oktatóanyag: létrehozás, közzététel és válasz QnA Maker'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy új tudásbázist egy nyilvános webalapú gyakori kérdésekből származó kérdésekkel és válaszokkal. A Tudásbázis mentése, betanítása és közzététele. Miután közzétette a tudásbázist, küldjön egy kérdést, és kap egy választ egy cURL-paranccsal. Ezután hozzon létre egy robotot, és tesztelje a robotot ugyanazzal a kérdéssel.
ms.topic: tutorial
ms.date: 01/28/2020
ms.openlocfilehash: 07a92b0f24aa95fc4a5188f54c7194fd342409fb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844166"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Oktatóanyag: a QnA Maker portálon hozzon létre egy tudásbázist

Hozzon létre egy új tudásbázist egy nyilvános, webalapú gyakori kérdésekből származó kérdésekkel és válaszokkal. A Tudásbázis mentése, betanítása és közzététele. Miután közzétette a tudásbázist, küldjön egy kérdést, és kap egy választ egy cURL-paranccsal. Ezután hozzon létre egy robotot, és tesztelje a robotot ugyanazzal a kérdéssel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy tudásbázist a QnA Maker portálon.
> * Tekintse át, mentse és tanítsa ki a tudásbázist.
> * Tegye közzé a tudásbázist.
> * Az Tudásbázis lekérdezése a cURL használatával.
> * Hozzon létre egy robotot.


> [!NOTE]
> Az oktatóanyag programozott verziója az [ **Azure-Samples/kognitív-Services-qnamaker-csharp** GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)teljes megoldásával érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz egy meglévő [QnA Maker-szolgáltatásra](../How-To/set-up-qnamaker-service-azure.md) lesz szükség.

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

1. Jelentkezzen be a QnA Maker [portáljára](https://www.qnamaker.ai).

1. A felső menüben válassza a **Create a knowledge base** (Tudásbázis létrehozása) lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a QnA Maker portálról](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Hagyja ki az első lépést, mert a meglévő QnA Maker szolgáltatását fogja használni.

1. Válassza ki a meglévő beállításokat:

    |Beállítás|Rendeltetés|
    |--|--|
    |Microsoft Azure Directory-azonosító|Ez az azonosító a Azure Portal és a QnA Maker portálra való bejelentkezéshez használt fiókhoz van társítva. |
    |Azure-előfizetés neve|Az a Számlázási fiók, amelyben a QnA Maker erőforrást létrehozta.|
    |Azure QnA-szolgáltatás|A meglévő QnA Maker-erőforrás.|
    |Nyelv|A szolgáltatásban található összes tudásbázishoz használt nyelv. Ne keverje össze a nyelveket egyetlen erőforrásban. Ez hátrányosan fogja megválaszolni a minőségi válaszokat.|

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a QnA Maker portálról](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Adja meg a Tudásbázis nevét, `My Tutorial kb`.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a QnA Maker portálról](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Töltse fel tudásbázisát a következő beállításokkal:

    |Beállítás neve|Beállítás értéke|Rendeltetés|
    |--|--|--|
    |Több-turn Kibontás engedélyezése|Bejelölve|Engedélyezi a követő kéréseket.|
    |Alapértelmezett válasz szövege|`No good answer was found.`|Akkor tért vissza, ha nincs egyezés a kérdésben.|
    |URL-cím|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |A megadott URL-címen található GYIK tartalma kérdés-válasz formátumú. A QnA Maker ebből a formátumból tudja kinyerni a kérdéseket és az azokhoz tartozó válaszokat.|
    |File |_ebben az oktatóanyagban nincs használatban_|Itt tölthetők fel a kérdéseket és válaszokat tartalmazó fájlok. |
    |Chit-chat personality (Csevegő személyisége)|Rövid|Ez egy barátságos és hétköznapi [személyiséget](../Concepts/best-practices.md#chit-chat) biztosít a gyakori kérdésekre és válaszokra. A kérdéseket és válaszokat később szerkesztheti. |

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a QnA Maker portálról](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. A létrehozási folyamat befejezéséhez válassza a **Create your KB** (Tudásbázis létrehozása) lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a QnA Maker portálról](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>A tudásbázis ellenőrzése, mentése és betanítása

1. Ellenőrizze a kérdéseket és a válaszokat. Az első oldalon az URL-címről származó kérdések és válaszok szerepelnek.

    > [!div class="mx-imgBorder"]
    > ![képernyőkép a QnA Maker portálról](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. A táblázat alján válassza ki a kérdések és válaszok utolsó oldalát. Az oldalon a Chit-chat personality (Csevegő személyisége) kérdései és válaszai szerepelnek.

1. A kérdések és válaszok listáját a fenti eszköztáron válassza ki a **megtekintési beállítások** ikont, majd válassza a **metaadatok megjelenítése**lehetőséget. Ez az egyes kérdések és válaszok metaadat-címkéit jeleníti meg. A Chit-Chat kérdései a **szerkesztői: Chit-Chat** metaadatok már be vannak állítva. Ezt a metaadatokat a rendszer visszaküldi az ügyfélalkalmazás számára a kiválasztott válasz mellett. Az ügyfélalkalmazás, például egy csevegési robot, ezzel a szűrt metaadatokkal további feldolgozást vagy interakciókat határozhat meg a felhasználóval.

    ![Képernyőkép a QnA Maker-portálról](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. A felső menüsávon válassza a **Save and train** (Mentés és betanítás) elemet.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Közzététel a Tudásbázis-végpontok beszerzéséhez

Kattintson a felső menüben található **Publish** (Közzététel) gombra. A közzétételi oldalon válassza a **Publish** (Közzététel) elemet.

![Képernyőkép a QnA Maker-portálról](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

A Tudásbázis közzétételét követően megjelenik a végpont.

![A végpontok beállításainak képernyőképe](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Ne zárjuk be ezt a **közzétételi** lapot. A robot létrehozásához az oktatóanyag későbbi részében szüksége lesz rá.

## <a name="use-curl-to-query-for-an-faq-answer"></a>A cURL használata a GYIK-válasz lekérdezéséhez

1. Válassza ki a **Curl** lapot.

    ![Képernyőfelvétel a curl lapról](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Másolja ki a **curl** lap szövegét, és futtassa azt egy curl-kompatibilis terminálon vagy parancssorban. Az engedélyezési fejléc értéke tartalmazza a `Endpoint`szöveget, a záró szóközzel és a kulccsal.

1. Cserélje le a `<Your question>` elemet a `How large can my KB be?` kérdésre. Ez hasonlít a `How large a knowledge base can I create?` kérdéshez, de nem pontosan ugyanaz. A QnA Maker természetes nyelvi feldolgozás segítségével határozza meg, hogy a két kérdés azonos-e.

1. Futtassa a cURL parancsot, és fogadja a JSON-választ, beleértve a pontszámot és a választ.

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }

    ```

    A QnA Maker 42,81%-os pontszámmal megközelítőleg biztos a válaszban.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>A cURL használata a Chit-csevegési válaszok lekérdezéséhez

1. A cURL-kompatibilis terminálon cserélje le a `How large can my KB be?`t egy, a felhasználótól származó bot-beszélgetés-záró utasítással, például `Thank you`.

1. Futtassa a cURL parancsot, és fogadja a JSON-választ, beleértve a pontszámot és a választ.

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }

    ```

    Mivel a `Thank you` utasításhoz tartozó kérdés pontosan megegyezik egy csevegési kérdéssel, a QnA Maker 100-as pontszámmal teljesen biztos a válaszban. QnA Maker az összes kapcsolódó kérdést, valamint a Chit-Chat metaadat-címkét tartalmazó metaadat-tulajdonságot is visszaadja.

## <a name="use-curl-to-query-for-the-default-answer"></a>A cURL használata az alapértelmezett válasz lekérdezéséhez

A QnA Maker nem biztos abban, hogy az alapértelmezett választ kapja. Ezt a választ az Azure Portalon kell konfigurálni.

1. A cURL-kompatibilis terminálon cserélje le a `Thank you`t a `x`ra.

1. Futtassa a cURL parancsot, és fogadja a JSON-választ, beleértve a pontszámot és a választ.

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```

    QnA Maker egy `0`pontszámot adott vissza, ami nem jelent megbízhatóságot. Emellett az alapértelmezett választ is visszaadja.

## <a name="create-a-knowledge-base-bot"></a>Tudásbázis-robot létrehozása

További információ: [csevegési robot létrehozása ezzel a tudásbázissal](create-qna-bot.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a Tudásbázis robotjának használatával, távolítsa el az erőforráscsoportot, `my-tutorial-rg`, és távolítsa el a robot-folyamat során létrehozott összes Azure-erőforrást.

Ha elkészült a tudásbázissal, a QnA Maker portálon válassza a **saját tudásbázisok**lehetőséget. Ezután válassza ki a tudásbázist, **az oktatóanyag kb-ot**, és kattintson a jobb szélen található Törlés ikonra a sorban.

## <a name="next-steps"></a>Következő lépések

A támogatott fájlformátumokról további információ a [támogatott adatforrásokat](../Concepts/content-types.md) ismertető szakaszban talál.

Tudjon meg többet a csevegő [személyiségeiről](../Concepts/best-practices.md#chit-chat).

Az alapértelmezett válasszal kapcsolatos további információkat lásd a [Nincs találat](../Concepts/confidence-score.md#no-match-found) című szakaszban.

> [!div class="nextstepaction"]
> [Csevegési robot létrehozása ezzel a tudásbázissal](create-qna-bot.md)
---
title: Létrehozását, közzétételét és válaszoljon a QnA Maker
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy új Tudásbázis kérdések és válaszok a nyilvános webes – gyakori kérdések. Mentse, betanítását és a Tudásbázis közzététele. A Tudásbázis közzététele után küldjön egy kérdést, és kapja meg a választ a cURL paranccsal. Ezután hozzon létre egy robot, és tesztelje a robot a ugyanezt a kérdést.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a13e0cb0e594571344b16d007ef13475b384b73d
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692996"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Oktatóanyag: A QnA Maker Portalról Tudásbázis létrehozása

Hozzon létre egy új Tudásbázis kérdések és válaszok a nyilvános webes – gyakori kérdések. Mentse, betanítását és a Tudásbázis közzététele. A Tudásbázis közzététele után küldjön egy kérdést, és kapja meg a választ a cURL paranccsal. Ezután hozzon létre egy robot, és tesztelje a robot a ugyanezt a kérdést. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Tudásbázis létrehozása a QnA Maker Portal.
> * Tekintse át, mentése és a Tudásbázis betanításához.
> * A Tudásbázis közzététele.
> * A Tudásbázis lekérdezni a cURL használatával.
> * Hozzon létre egy robot.
 

> [!NOTE]
> Ebben az oktatóanyagban programozott verziója érhető el a teljes körű megoldást a [ **Azure-Samples/cognitive-services-qnamaker-csharp** GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz egy meglévő [QnA Maker-szolgáltatásra](../How-To/set-up-qnamaker-service-azure.md) lesz szükség. 

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása 

1. Jelentkezzen be a QnA Maker [portáljára](https://www.qnamaker.ai). 

1. A felső menüben válassza a **Create a knowledge base** (Tudásbázis létrehozása) lehetőséget.

    ![Képernyőfelvétel: a QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Az első lépés, akkor kihagyása, mert a meglévő QnA Maker szolgáltatást használhatja. 

1. Válassza ki a meglévő beállítások:  

    |Beállítás|Cél|
    |--|--|
    |A Microsoft Azure címtár-azonosító|Ez az azonosító társítva a fiók használatával jelentkezzen be az Azure portal és a QnA Maker portálra. |
    |Azure-előfizetés neve|A számlázási fiók, amelyben létrehozta a QnA Maker erőforrás.|
    |Azure QnA-szolgáltatás|A meglévő QnA Maker-erőforrás.|

    ![Képernyőfelvétel: a QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Adja meg a Tudásbázis nevét `My Tutorial kb`.

    ![Képernyőfelvétel: a QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Töltse fel a Tudásbázis a következő beállításokkal:  

    |Beállítás neve|Beállítás értéke|Cél|
    |--|--|--|
    |URL-cím|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |A megadott URL-címen található GYIK tartalma kérdés-válasz formátumú. A QnA Maker ebből a formátumból tudja kinyerni a kérdéseket és az azokhoz tartozó válaszokat.|
    |Fájl |_ebben az oktatóanyagban nincs használatban_|Itt tölthetők fel a kérdéseket és válaszokat tartalmazó fájlok. |
    |Chit-chat personality (Csevegő személyisége)|Rövid|Ez biztosítja, hogy egy rövid és a magasabb [személyiségelemző](../Concepts/best-practices.md#chit-chat) a gyakori kérdéseket és válaszokat. A kérdéseket és válaszokat később szerkesztheti. |

    ![Képernyőfelvétel: a QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. A létrehozási folyamat befejezéséhez válassza a **Create your KB** (Tudásbázis létrehozása) lehetőséget.

    ![Képernyőfelvétel: a QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>A tudásbázis ellenőrzése, mentése és betanítása

1. Ellenőrizze a kérdéseket és a válaszokat. Az első oldalon az URL-címről származó kérdések és válaszok szerepelnek. 

    ![Képernyőfelvétel: a QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. A táblázat alján válassza ki a kérdések és válaszok utolsó oldalát. Az oldalon a Chit-chat personality (Csevegő személyisége) kérdései és válaszai szerepelnek. 

1. Kérdések és válaszok az eszközlista feletti eszköztárán válassza a **beállítások megtekintéséhez** ikonra, és válassza ki **megjelenítése metaadatok**. A metaadat-címkéket az egyes kérdés és válasz tartalmazza. A csevegési Chit kérdése van a **Szerkesztői: chit csevegési** metaadatok már be van állítva. Ezeket a metaadatokat az ügyfélalkalmazás és a kiválasztott választ küld vissza. Az ügyfélalkalmazás egy csevegőrobot, például a szűrt metaadat segítségével határozza meg, további feldolgozási vagy a felhasználói interakció.

    ![Képernyőfelvétel: a QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. A felső menüsávon válassza a **Save and train** (Mentés és betanítás) elemet.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Megszerezni a Tudásbázis végpontok közzététele

Kattintson a felső menüben található **Publish** (Közzététel) gombra. A közzétételi oldalon válassza a **Publish** (Közzététel) elemet.

![Képernyőfelvétel: a QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

A Tudásbázis közzététele után a végpont jelenik meg.

![Képernyőkép a végpont beállításait](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Ne zárja be a **közzététel** lapot. Robotprogramok létrehozásához az oktatóanyag későbbi részében szüksége lesz. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>A lekérdezés a cURL használata a választ a gyakori kérdések

1. Válassza ki a **Curl** lapot. 

    ![Képernyőkép a Curl lap](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Másolja be a szöveget, a **Curl** lapra, és a Terminálszolgáltatások vagy a parancssori Futtatás a cURL-kompatibilis. Az engedélyezési fejléc értéke tartalmazza a szöveg `Endpoint`, záró szóközt, majd a kulcs.

1. Cserélje le a `<Your question>` elemet a `How large can my KB be?` kérdésre. Ez hasonlít a `How large a knowledge base can I create?` kérdéshez, de nem pontosan ugyanaz. A QnA Maker természetes nyelvi feldolgozás segítségével határozza meg, hogy a két kérdés azonos-e.     

1. Futtatás a cURL-parancsot, és a JSON-választ, beleértve a pontszám és a válasz fogadására. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Egy Chit csevegési válasz a lekérdezésre cURL használatával

1. Cserélje le a cURL-kompatibilis terminálban `How large can my KB be?` bot beszélgetés és Befejezés utasítást a felhasználótól például `Thank you`.   

1. Futtatás a cURL-parancsot, és a JSON-választ, beleértve a pontszám és a válasz fogadására. 

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

    Mivel a `Thank you` utasításhoz tartozó kérdés pontosan megegyezik egy csevegési kérdéssel, a QnA Maker 100-as pontszámmal teljesen biztos a válaszban. A QnA Maker is a kapcsolódó kérdések, valamint a csevegési Chit metaadatok címke adatokat tartalmazó metaadat-tulajdonságot adott vissza.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Az alapértelmezett válasz a lekérdezésre cURL használatával

Minden kérdést, amely a QnA Maker nem benne a alapértelmezett választ kap. Ezt a választ az Azure Portalon kell konfigurálni. 

1. Cserélje le a cURL-kompatibilis terminálban `Thank you` a `x`. 

1. Futtatás a cURL-parancsot, és a JSON-választ, beleértve a pontszám és a válasz fogadására. 

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
    
    A QnA Maker egy pontszámát visszaadott `0`, ami azt jelenti, hogy nem megbízható. Emellett a alapértelmezett választ adja vissza. 

## <a name="create-a-knowledge-base-bot"></a>Hozzon létre egy Tudásbázis robotot

További információkért lásd: [hozzon létre egy csevegőrobot a Tudásbázis](create-qna-bot.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor elkészült, a Tudásbázis robottal, távolítsa el az erőforráscsoportot, `my-tutorial-rg`, eltávolítja az összes Azure-erőforrást létrehozni a bot folyamatban.

Ha végzett a Tudásbázis következő, a QnA Maker Portal **saját tudásbázisok**. Válassza ki a Tudásbázis **saját oktatóanyag kb**, és kattintson a Törlés ikonra a jobb szélen abban a sorban.  

## <a name="next-steps"></a>További lépések

A támogatott fájlformátumokról további információ a [támogatott adatforrásokat](../Concepts/data-sources-supported.md) ismertető szakaszban talál. 

Tudjon meg többet a csevegő [személyiségeiről](../Concepts/best-practices.md#chit-chat).

Az alapértelmezett válasszal kapcsolatos további információkat lásd a [Nincs találat](../Concepts/confidence-score.md#no-match-found) című szakaszban. 

> [!div class="nextstepaction"]
> [A Tudásbázis hozzon létre egy csevegőrobot](create-qna-bot.md)
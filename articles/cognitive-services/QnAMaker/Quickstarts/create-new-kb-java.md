---
title: 'Gyors útmutató: Hozzon létre a Tudásbázis - REST, a Java - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ez a Java REST-alapú rövid útmutató végigvezeti egy olyan minta QnA Maker-tudásbázis programozott módon történő létrehozásán, amely a Cognitive Services API-fiók Azure-irányítópultján fog megjelenni.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: diberry
ms.openlocfilehash: 87ba02b6a840d416d54e3129b5720b4f59820eb8
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413438"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Gyors útmutató: Tudásbázis létrehozása a QnA Maker Java használatával

Ez a rövid útmutató végigvezeti egy minta QnA Maker-tudásbázis programozott módon való létrehozásán. A QnA Maker automatikusan nyer ki kérdéseket és válaszokat a félig strukturált tartalmak, például a gyakori kérdések közül, az [adatforrásokból](../Concepts/data-sources-supported.md). A tudásbázis modelljét az API-kérés törzsében küldött JSON definiálja.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>Egy tudásbázisfájl létrehozása

Hozzon létre egy `CreateKB.java` nevű fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `CreateKB.java` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása
A fenti szükséges függőségek után adja hozzá a QnA Maker eléréséhez szükséges konstansokat is a `CreateKB` osztályhoz. Cserélje le a `subscriptionKey` változó értéket a saját QnA Maker-kulcsára. Az osztály befejezéséhez nem kell hozzáadnia a záró kerek zárójelet. Ez a rövid útmutató végén a végső kódrészletben szerepel.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>A tudásbázismodell definícióosztályainak hozzáadása
A konstansok után adja hozzá a következő osztályokat és függvényeket a `CreateKB` osztályhoz, ezzel szerializálja a modelldefiníciós objektumot a JSON-ba.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Támogató függvények hozzáadása

Ezután adja hozzá a következő támogató függvényeket a `CreateKB` osztályhoz.

1. A következő függvények hozzáadásával olvasható formában jelenítheti meg a JSON-fájlt:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. A következő osztály hozzáadásával felügyelheti a HTTP-választ:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Adja hozzá a következő metódust, hogy POST-kérést küldjön a QnA Maker API-knak. Az `Ocp-Apim-Subscription-Key` QnA Maker szolgáltatás kulcsa, amely a hitelesítéshez használható.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Adja hozzá a következő metódust, hogy GET-kérést küldjön a QnA Maker API-knak.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Metódus hozzáadása a tudásbázis létrehozásához
Adja hozzá a következő metódust, hogy a Post metódusba indított hívással létrehozza a tudásbázist.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

Az API egy JSON-választ ad vissza, amely tartalmazza a művelet azonosítóját. A művelet azonosítója alapján megállapíthatja, hogy a tudásbázis sikeresen létrejött-e.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Metódus hozzáadása az állapot lekéréséhez
Adja hozzá a következő metódust a létrehozási állapot lekérdezéséhez.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

Ismételje a hívást, amíg nem sikerül vagy meg nem hiúsul:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Fő metódus hozzáadása
A fő metódus létrehozza a tudásbázist, és kérést indít az állapotellenőrzéshez. A _create_ **műveletazonosítót** a rendszer a POST-válasz **Location** fejlécmezőjében adja vissza, majd a GET-kérésben az útvonal részeként használja. **A `while` hurok újra megpróbálja lekérni az állapotot, ha az nem teljesült.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>A program létrehozása és futtatása

1. Győződjön meg arról, hogy a gson kódtár a `./libs` könyvtárban található. A parancssorban fordítsa le a `CreateKB.java` fájlt:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Írja be a következő parancsot egy parancssorba a program futtatásához. A parancs egy kérést küld a QnA Maker API-ra a tudásbázis létrehozásához, majd 30 másodpercenként lekérdezi az eredményeket. Mindegyik válasz megjelenik a konzolablakban.

    ```base
    java -cp ",;libs/*" CreateKB
    ```

A tudásbázis létrehozása után a QnA Maker portálján, a [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Saját tudásbázisok) lapon tekintheti meg azt.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

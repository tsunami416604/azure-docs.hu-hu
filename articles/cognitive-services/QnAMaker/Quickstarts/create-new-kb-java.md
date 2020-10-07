---
title: 'Rövid útmutató: Tudásbázis létrehozása – REST, Java – QnA Maker'
description: Ez a Java REST-alapú rövid útmutató végigvezeti Önt a Cognitive Services API-fiók Azure-irányítópultján megjelenő példa QnA Maker tudásbázis létrehozásán.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 275b1976e51789d4a458e7bd9f7cfa3240cc81d5
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777668"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Rövid útmutató: Tudásbázis létrehozása a QnA Makerben a Java használatával

Ez a rövid útmutató végigvezeti egy minta QnA Maker-tudásbázis programozott módon való létrehozásán. A QnA Maker automatikusan nyer ki kérdéseket és válaszokat a félig strukturált tartalmak, például a gyakori kérdések közül, az [adatforrásokból](../Concepts/knowledge-base.md). A tudásbázis modelljét az API-kérés törzsében küldött JSON definiálja.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Tudásbázis létrehozása](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Műveletek részleteinek lekérése](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Java-minta](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Go 1.10.1](https://golang.org/dl/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.

A [mintakód](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) elérhető a GitHub-tárházban QnA Maker Javával.

## <a name="create-a-knowledge-base-file"></a>Egy tudásbázisfájl létrehozása

Hozzon létre egy `CreateKB.java` nevű fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `CreateKB.java` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása
A fenti szükséges függőségek után adja hozzá a QnA Maker eléréséhez szükséges konstansokat is a `CreateKB` osztályhoz.

Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs és az erőforrás nevének lekéréséhez **válassza a** gyors üzembe helyezés lehetőséget a QnA Maker erőforrásának Azure Portal.

Állítsa be a következő értékeket:

* `<your-qna-maker-subscription-key>` – A **kulcs** egy 32 karakterből álló karakterlánc, és a Azure Portal QnA Maker erőforrásban, a rövid útmutató lapon érhető el. Ez a kulcs nem egyezik meg az előrejelzési végpont kulcsával.
* `<your-resource-name>` – Az **Erőforrás neve** a szerzői végpont URL-címének létrehozásához használható a következő formátumban: `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Ez az erőforrás neve nem egyezik meg az előrejelzési végpont lekérdezéséhez használt névvel.

Az osztály befejezéséhez nem kell hozzáadnia a záró kerek zárójelet. Ez a rövid útmutató végén a végső kódrészletben szerepel.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>A tudásbázismodell definícióosztályainak hozzáadása
A konstansok után adja hozzá a következő osztályokat és függvényeket a `CreateKB` osztályhoz, ezzel szerializálja a modelldefiníciós objektumot a JSON-ba.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>Támogató függvények hozzáadása

Ezután adja hozzá a következő támogató függvényeket a `CreateKB` osztályhoz.

1. A következő függvények hozzáadásával olvasható formában jelenítheti meg a JSON-fájlt:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. A következő osztály hozzáadásával felügyelheti a HTTP-választ:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. Adja hozzá a következő metódust, hogy POST-kérést küldjön a QnA Maker API-knak. Az `Ocp-Apim-Subscription-Key` QnA Maker szolgáltatás kulcsa, amely a hitelesítéshez használható.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. Adja hozzá a következő metódust, hogy GET-kérést küldjön a QnA Maker API-knak.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>Metódus hozzáadása a tudásbázis létrehozásához
Adja hozzá a következő metódust, hogy a Post metódusba indított hívással létrehozza a tudásbázist.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

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

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

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
A fő metódus létrehozza a tudásbázist, és kérést indít az állapotellenőrzéshez. A rendszer visszaadja a művelet AZONOSÍTÓját a POST Response **fejléc mezőjében**, majd az útvonal részeként használja a Get kérelemben. A `while` hurok újrapróbálkozik az állapottal, ha az nem fejeződött be.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>A program létrehozása és futtatása

1. Győződjön meg arról, hogy a gson kódtár a `./libs` könyvtárban található. A parancssorban fordítsa le a fájlt `CreateKB.java` :

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. A program futtatásához írja be a következő parancsot a parancssorba. A parancs egy kérést küld a QnA Maker API-ra a tudásbázis létrehozásához, majd 30 másodpercenként lekérdezi az eredményeket. Mindegyik válasz megjelenik a konzolablakban.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

A tudásbázis létrehozása után a QnA Maker portálján, a [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Saját tudásbázisok) lapon tekintheti meg azt.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)

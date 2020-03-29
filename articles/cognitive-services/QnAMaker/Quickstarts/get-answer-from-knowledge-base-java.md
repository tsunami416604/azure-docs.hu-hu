---
title: 'Rövid útmutató: Válasz kérése a tudásbázistól - REST, Java - QnA Maker'
description: Ez a Java REST-alapú rövid útmutató végigvezeti a tudásbázistól programozott válasz beszerzésén.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 67f09b6d1e284cdf35825a2e584b372bd2adf70a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851739"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Rövid útmutató: Válaszok egy kérdésre egy java-val rendelkező tudásbázisból

Ez a rövid útmutató végigvezeti a programozott beolvasása egy közzétett QnA Maker tudásbázis. A tudásbázis olyan [adatforrásokból](../Concepts/knowledge-base.md) származó kérdéseket és válaszokat tartalmaz, mint például a gyakori kérdések. A kérdést a [rendszer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) elküldi a QnA Maker szolgáltatásnak. A [válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) tartalmazza a felül előre jelzett választ.

[Referenciadokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Minta](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* Ez a minta az Apache [HTTP-ügyfél](https://hc.apache.org/httpcomponents-client-ga/) http-összetevőkből. A következő Apache HTTP ügyfélkódtárakat kell hozzáadnia a projekthez:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-fakitermelés-1.2.jar
* [Visual Studio kód](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza **a Kulcsok** az **Erőforrás-kezelés** az Azure-irányítópulton a QnA Maker erőforrás.
* **Lapbeállítások közzététele.** Ha nem rendelkezik közzétett tudásbázissal, hozzon létre egy üres tudásbázist, majd importáljon egy tudásbázist a **Beállítások** lapon, majd tegye közzé. Letöltheti és használhatja [ezt az alapvető tudásbázist.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)

    A közzétételi lap beállításai közé tartozik a POST útvonal érték, az Állomás érték és az EndpointKey érték.

    ![Publish settings (Közzétételi beállítások)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Java-fájl létrehozása

Nyissa meg a VSCode-ot, és hozzon létre egy új nevű fájlt, `GetAnswer.java` és adja hozzá a következő osztályt:

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

Ez a rövid útmutató Apache osztályokat használ a HTTP-kérelmekhez. A GetAnswer osztály felett, a `GetAnswer.java` fájl tetején adja hozzá a szükséges függőségeket a projekthez:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

Az `GetAnswer.java` osztály tetején adja hozzá a QnA Maker eléréséhez szükséges állandókat. Ezek az értékek a tudásbázis közzététele után a **Közzététel** lapon találhatók.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Post-kérelem hozzáadása kérdés küldéséhez

A következő kód https-kérelmet küld a QnA Maker API-nak, hogy küldje el a kérdést a tudásbázisnak, és megkapja a választ:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

A `Authorization` fejléc értéke tartalmazza `EndpointKey`a karakterláncot .

További információ a [kérésről](../how-to/metadata-generateanswer-usage.md#generateanswer-request) és a [válaszról.](../how-to/metadata-generateanswer-usage.md#generateanswer-response)

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

A program létrehozása és futtatása a parancssorból. Automatikusan elküldi a kérelmet a QnA Maker API-nak, majd kinyomtatja a konzolablakba.

1. A fájl összeállítása:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Futtassa a fájlt:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
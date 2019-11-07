---
title: 'Gyors útmutató: Tudásbázis közzététele, REST, Java – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez a Java REST-alapú rövid útmutató közzéteszi a tudásbázist, és létrehoz egy végpontot, amely hívható az alkalmazásban vagy a csevegési robotban.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 224afcc4a6301b74ad54064df4b65b7cf447c6c6
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721132"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a Java használatával

Ez a REST-alapú rövid útmutató végigvezeti a Tudásbázis (KB) programozott közzétételének lépésein. A közzététel leküldi a tudásbázis legújabb verzióját egy dedikált Azure Search-indexre, és létrehoz egy, az alkalmazásban vagy csevegőrobotban meghívható végpontot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* Ez a példa az Apache [http-ügyfelet](https://hc.apache.org/httpcomponents-client-ga/) használja a http-összetevőkből. Hozzá kell adnia a következő Apache HTTP-ügyféloldali kódtárakat a projekthez: 
    * httpclient-4.5.3. jar
    * httpcore-4.4.6. jar
    * Commons-Logging-1.2. jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.
* A QnA Maker tudásbázis (KB) azonosítója az URL-címben található a kbid lekérdezésisztring-paraméterben, amint az alább látható.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-csharp.md).

> [!NOTE] 
> A teljes megoldás fájl (ok) az [ **Azure-Samples/kognitív-Services-qnamaker-Java** GitHub-tárházból](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-a-java-file"></a>Java-fájl létrehozása

Nyissa meg a VSCode, és hozzon létre egy `PublishKB.java`nevű új fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

`PublishKB.java`tetején a osztály felett adja hozzá a következő sorokat a szükséges függőségek a projekthez való hozzáadásához:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>PublishKB osztály létrehozása a Main metódussal

A függőségek után adja hozzá a következő osztályt:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A **Main** metódusban adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Cserélje le az értékeket a saját értékeire.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>POST kérelem hozzáadása a Tudásbázis közzétételéhez

A szükséges konstansok után adja hozzá a következő kódot, amely HTTPS-kérést küld a QnA Maker API egy Tudásbázis közzétételéhez, és fogadja a választ:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozza létre és futtassa a programot a parancssorból. A szolgáltatás automatikusan elküldi a kérést a QnA Maker APInak, majd a konzol ablakba fog nyomtatni.

1. Hozza létre a fájlt:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Futtassa a fájlt:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

Miután közzétette a tudásbázist, szüksége lesz a [végpont URL-címére a válasz létrehozásához](../Tutorials/create-publish-answer.md#generating-an-answer).  

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)

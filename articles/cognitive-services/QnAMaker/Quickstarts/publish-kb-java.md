---
title: Tudásbázis, REST, a Java közzététele
titleSuffix: QnA Maker - Azure Cognitive Services
description: Java REST-alapú rövid útmutató végigvezeti közzététele, amely egy dedikált Azure Search-index a közzétett Tudásbázis jelölő leküldi a tesztelt Tudásbázis legújabb verzióját. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 9f91f83ec7aced8dcfc8449f6a3575ecbe539c64
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216727"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Gyors útmutató: Tudásbázis közzététele a QnA Maker Java használatával

A REST-alapú rövid útmutató végigvezeti programozott módon közzététele (KB). A közzététel leküldi a tudásbázis legújabb verzióját egy dedikált Azure Search-indexre, és létrehoz egy, az alkalmazásban vagy csevegőrobotban meghívható végpontot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* Ebben a példában az Apache [HTTP-alapú](http://hc.apache.org/httpcomponents-client-ga/) HTTP Components. Az alábbi Apache HTTP-ügyfélkönyvtárak hozzáadása a projekthez kell: 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * Commons-naplózás – 1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza ki **kulcsok** alatt **erőforrás-kezelés** az Azure irányítópultján a QnA Maker erőforrás. . 
* A QnA Maker tudásbázis (KB) azonosítója az URL-címben található a kbid lekérdezésisztring-paraméterben, amint az alább látható.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha például egy Tudásbázis még nem rendelkezik, a minta használata ebben a rövid útmutatóban egy hozhat létre: [Hozzon létre egy új Tudásbázis](create-new-kb-csharp.md).

> [!NOTE] 
> A teljes megoldás fájl(ok) érhetők el a [ **Azure-minták/cognitive-services-qnamaker – java** GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Hozzon létre egy Java-fájlt

Nyissa meg a VSCode, és hozzon létre egy új fájlt `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

Felső részén `PublishKB.java`, az osztály felett adja hozzá a következő sorokat a szükséges függőségek hozzáadása a projekthez:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Metoda main PublishKB osztály létrehozása

A függőségek után adja hozzá a következő osztályok:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

Az a **fő** metódust, adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Cserélje le az értékeket a saját.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adja hozzá a POST-kérés Tudásbázis közzététele

A szükséges állandókat után adja hozzá a következő kódra, amely egy HTTPS-kérést küld a QnA Maker API Tudásbázis közzététele, és megkapja a választ:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozhat létre, és a program futtatása a parancssorból. A kérelem automatikusan elküldi a QnA Maker API, majd a konzolablakban nyomtatási.

1. A fájl létrehozása:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Futtassa a fájlt:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

A Tudásbázis közzététele után kell a [végponti URL-cím létrehozásához választ](../Tutorials/create-publish-answer.md#generating-an-answer).  

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

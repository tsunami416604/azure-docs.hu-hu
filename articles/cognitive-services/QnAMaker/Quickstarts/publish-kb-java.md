---
title: 'Rövid útmutató: Tudásbázis közzététele, REST, Java – QnA Maker'
description: Ez a Java REST-alapú gyorsindítás közzéteszi a tudásbázist, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegőrobotban.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 149d7963f29bf041cda75fffaac533e0a62ee7a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851684"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a Java használatával

Ez a REST-alapú rövid útmutató végigvezeti a tudásbázis (KB) programozott közzétételén. A közzététel leküldéses a tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search index, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegőrobot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* Ez a minta az Apache [HTTP-ügyfél](https://hc.apache.org/httpcomponents-client-ga/) http-összetevőkből. A következő Apache HTTP ügyfélkódtárakat kell hozzáadnia a projekthez:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-fakitermelés-1.2.jar
* [Visual Studio kód](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs és a végpont (amely tartalmazza az erőforrás nevét) lekéréséhez válassza az erőforrás **gyorsindítását** az Azure Portalon.
* A QnA Maker tudásbázisának (KB) azonosítója megtalálható az URL-címben a lekérdezési karakterlánc paraméterben az `kbid` alábbiak szerint.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-csharp.md).

> [!NOTE]
> A teljes megoldásfájl(ok) az [ **Azure-Samples/cognitive-services-qnamaker-java** GitHub repository-ból](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-a-java-file"></a>Java-fájl létrehozása

Nyissa meg a VSCode `PublishKB.java`programot, és hozzon létre egy új fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

Az osztály `PublishKB.java`felett a következő sorokat adja hozzá a szükséges függőségek hozzáadásához a projekthez:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>PublishKB-osztály létrehozása fő metódussal

A függőségek után adja hozzá a következő osztályt:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A **fő** módszerben adja hozzá a qna készítő eléréséhez szükséges állandókat. Cserélje le az értékeket a sajátjára.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Post-kérelem hozzáadása a tudásbázis közzétételéhez

A szükséges állandók után adja hozzá a következő kódot, amely HTTPS-kérelmet küld a QnA Maker API-hoz egy tudásbázis közzétételéhez, és megkapja a választ:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

A program létrehozása és futtatása a parancssorból. Automatikusan elküldi a kérelmet a QnA Maker API-nak, majd kinyomtatja a konzolablakba.

1. A fájl összeállítása:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Futtassa a fájlt:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

A tudásbázis közzététele után a [végpont URL-címére](./get-answer-from-knowledge-base-java.md)van szükség a válasz létrehozásához.

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
---
title: Modell beszereznie REST-hívással Java-ban
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 92552a9870f037555a6cde9daa67d3af112ccee7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368462"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure Language Understanding – Authoring resource 32 karakterkulcs és szerzői végpont URL-címe. Hozzon létre az [Azure Portalon](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) vagy az [Azure CLI-n](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli)keresztül.
* Importálja a [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) alkalmazást a cognitive-services-language-understanding GitHub-tárházból.
* Az importált TravelAgent alkalmazás LUIS-alkalmazásazonosítója. Az alkalmazásazonosító az alkalmazás irányítópultján látható.
* A kimondott szövegeket fogadó alkalmazáson belüli verzióazonosító. Az alapértelmezett azonosító a „0.1”.
* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* [Visual Studio Kód](https://code.visualstudio.com/) vagy a kedvenc IDE

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Modell módosítása programozott módon

1. Készítsen egy alkönyvtárat, `lib` és másolja a következő java libs-be:

    * [commons-fakitermelés-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Hozzon létre egy új fájlt `Model.java` néven. Adja hozzá a következő kódot:


    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;

    //javac -cp ":lib/*" Model.java
    //java -cp ":lib/*" Model

    public class Model {

        public static void main(String[] args)
        {
            try
            {

                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "YOUR-APP-ID";

                // Add your endpoint key
                String Key = "YOUR-KEY";

                // Add your endpoint, example is your-resource-name.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";

                String Utterance = "[{'text': 'go to Seattle today','intentName': 'BookFlight','entityLabels': [{'entityName': 'Location::LocationTo',"
                    + "'startCharIndex': 6,'endCharIndex': 12}]},{'text': 'a barking dog is annoying','intentName': 'None','entityLabels': []}]";

                String Version = "1.0";

                // Begin endpoint URL string building
                URIBuilder addUtteranceURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/examples");
                URIBuilder trainURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/train");

                // create URL from string
                URI addUtterancesURI = addUtteranceURL.build();
                URI trainURI = trainURL.build();

                // add utterances POST
                HttpClient addUtterancesClient = HttpClients.createDefault();
                HttpPost addutterancesRequest = new HttpPost(addUtterancesURI);
                addutterancesRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                addutterancesRequest.setHeader("Content-type","application/json");
                HttpResponse addutterancesResponse = addUtterancesClient.execute(addutterancesRequest);
                HttpEntity addutterancesEntity = addutterancesResponse.getEntity();
                if (addutterancesEntity != null)
                {
                    System.out.println(EntityUtils.toString(addutterancesEntity));
                }

                // train POST
                HttpClient trainClient = HttpClients.createDefault();
                HttpPost trainRequest = new HttpPost(trainURI);
                trainRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainRequest.setHeader("Content-type","application/json");
                HttpResponse trainResponse = trainClient.execute(trainRequest);
                HttpEntity trainEntity = trainResponse.getEntity();
                if (trainEntity != null)
                {
                    System.out.println(EntityUtils.toString(trainEntity));
                }

                // training status GET
                HttpClient trainStatusClient = HttpClients.createDefault();
                HttpGet trainStatusRequest = new HttpGet(trainURI);
                trainStatusRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainStatusRequest.setHeader("Content-type","application/json");
                HttpResponse trainStatusResponse = trainStatusClient.execute(trainStatusRequest);
                HttpEntity trainStatusEntity = trainStatusResponse.getEntity();
                if (trainStatusEntity != null)
                {
                    System.out.println(EntityUtils.toString(trainStatusEntity));
                }
            }

            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }
    }
    ```

1. Cserélje le a `YOUR-` saját értékeivel kezdődő értékeket.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|A 32 karakteres szerzői kulcs.|
    |`YOUR-ENDPOINT`| A szerzői URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`. Az erőforrás nevét az erőforrás létrehozásakor állította be.|
    |`YOUR-APP-ID`| A LUIS-alkalmazásazonosító. |

    A hozzárendelt kulcsok és erőforrások a LUIS-portálon, a Kezelés szakaszban, az **Azure-erőforrások** lapon láthatók. Az alkalmazásazonosító ugyanabban a Kezelés szakaszban, az **Alkalmazás beállítások** lapján érhető el.

1. Ha a parancssorba ugyanabban a könyvtárban található, ahol a fájlt létrehozta, írja be a következő parancsot a Java fájl fordításához:

    ```console
    javac -cp ":lib/*" Model.java
    ```

1. Futtassa a Java alkalmazást a parancssorból a következő szöveg megadásával a parancssorba:

    ```console
    java -cp ":lib/*" Model
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte ezt a rövid útmutatót, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok egy alkalmazáshoz](../luis-concept-best-practices.md)
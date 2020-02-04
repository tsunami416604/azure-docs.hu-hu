---
title: Modell lekérése REST-hívással Java-ban
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 5214395aa583bfa344f2c8e84066db84edb3939a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966862"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure Language Understanding – erőforrás 32 és a végpont URL-címének létrehozása. Hozzon létre a [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) vagy az [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli)használatával.
* Importálja a [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) alkalmazást a kognitív-Services-Language-Understanding GitHub adattárba.
* Az importált TravelAgent alkalmazás LUIS-alkalmazásának azonosítója. Az alkalmazásazonosító az alkalmazás irányítópultján látható.
* A hosszúságú kimondott szöveg fogadó alkalmazásban található verzióazonosító. Az alapértelmezett azonosító a „0.1”.
* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* A [Visual Studio Code](https://code.visualstudio.com/) vagy a kedvenc ide

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Modell programozott módosítása

A go használatával adjon hozzá egy géppel megtanult entitás [API](https://aka.ms/luis-apim-v3-authoring) -t az alkalmazáshoz.

1. Hozzon `lib` nevű alkönyvtárat, és másolja a következő Java-könyvtárakba:

    * [Commons-Logging-1.2. jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3. jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6. jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

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

1. Cserélje le a `YOUR-`-től kezdődő értékeket a saját értékeire.

    |Információ|Rendeltetés|
    |--|--|
    |`YOUR-KEY`|Az 32 karakteres szerzői kulcs.|
    |`YOUR-ENDPOINT`| Az authoring URL-végpontja. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`. Az erőforrás neve az erőforrás létrehozásakor állítható be.|
    |`YOUR-APP-ID`| A LUIS-alkalmazás azonosítója. |

    A hozzárendelt kulcsok és erőforrások a LUIS portálon láthatók a kezelés szakasz Azure- **erőforrások** lapján. Az alkalmazás-azonosító az **Alkalmazásbeállítások** lapon, ugyanazon kezelés szakaszban érhető el.

1. Ha a parancssor ugyanabban a címtárban található, ahol a fájlt létrehozta, adja meg a következő parancsot a Java-fájl fordításához:

    ```console
    javac -cp ":lib/*" Model.java
    ```

1. Futtassa a Java-alkalmazást a parancssorból a parancssorba írja be a következő szöveget:

    ```console
    java -cp ":lib/*" Model
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ajánlott eljárások az alkalmazásokhoz](../luis-concept-best-practices.md)
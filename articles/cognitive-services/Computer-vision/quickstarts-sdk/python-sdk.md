---
title: 'Gyors útmutató: Python SDK'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Python SDK-t a gyakori feladatokhoz, például a képek elemzéséhez, a Leírás készítéséhez, a szöveg felismeréséhez és a miniatűr létrehozásához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: pafarley
ms.openlocfilehash: bc42edc3e97aa68c5fe9d2b3162913e8925df4ee
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375660"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Azure Cognitive Services Computer Vision SDK a Pythonhoz

A Computer Vision szolgáltatás a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz. Computer Vision algoritmusok különböző módokon elemzik a képek tartalmát, attól függően, hogy milyen vizuális funkciók érdeklik.

* [Kép elemzése](#analyze-an-image)
* [A tulajdonosi tartományok listájának beolvasása](#get-subject-domain-list)
* [Rendszerkép elemzése tartomány szerint](#analyze-an-image-by-domain)
* [Rendszerkép szöveges leírásának beolvasása](#get-text-description-of-an-image)
* [Kézírásos szöveg beolvasása a képből](#get-text-from-image)
* [Miniatűr készítése](#generate-thumbnail)

A szolgáltatással kapcsolatos további információkért tekintse meg a [Mi az Computer Vision?][computervision_docs]című témakört.

További dokumentációt keres?

* [Az SDK dokumentációja](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Cognitive Services Computer Vision dokumentációja](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.6 +][python]
* Ingyenes [Computer Vision kulcs][computervision_resource] és társított végpont. Ezekre az értékekre szüksége lesz, amikor létrehozza a [ComputerVisionClient][ref_computervisionclient] -ügyfél objektumának példányát. Az alábbi módszerek egyikével kérheti le ezeket az értékeket.

### <a name="if-you-dont-have-an-azure-subscription"></a>Ha nem rendelkezik Azure-előfizetéssel

Hozzon létre egy 7 napig érvényes ingyenes kulcsot a Computer Vision szolgáltatáshoz tartozó **[kipróbálási][computervision_resource]** élményben. A kulcs létrehozásakor másolja a kulcs és a végpont nevét. Erre szüksége lesz a- [ügyfél létrehozásához](#create-client).

A kulcs létrehozása után tartsa meg a következőket:

* Kulcs értéke: egy 32 karakteres karakterlánc, amelynek a formátuma`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Kulcs végpontja: az alap végpont URL-\:címe, HTTPS//westcentralus.API.Cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Ha rendelkezik Azure-előfizetéssel

Az előfizetéshez tartozó erőforrás létrehozásának legegyszerűbb módja az alábbi [Azure CLI][azure_cli] -parancs használata. Ez egy kognitív szolgáltatási kulcsot hoz létre, amelyet számos kognitív szolgáltatásban használhat. Ki kell választania a _meglévő_ erőforráscsoport-nevet, például: "My-cogserv-Group" és az új Computer vízió-erőforrás neve, például "My-Computer-vízió-Resource".

```Bash
RES_REGION=westeurope
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind CognitiveServices \
    --sku S0 \
    --yes
```

<!--
## Installation

Install the Azure Cognitive Services Computer Vision SDK with [pip][pip], optionally within a [virtual environment][venv].

### Configure a virtual environment (optional)

Although not required, you can keep your base system and Azure SDK environments isolated from one another if you use a [virtual environment][virtualenv]. Execute the following commands to configure and then enter a virtual environment with [venv][venv], such as `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```
-->

### <a name="install-the-sdk"></a>Az SDK telepítése

Telepítse az Azure Cognitive Services Computer Vision SDK for Python- [csomagot][pypi_computervision] a [pip][pip]használatával:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Miután létrehozta a Computer Vision erőforrást, szüksége lesz a **végpontra**, és az egyik **fiókjának kulcsát** az ügyfél-objektum létrehozásához.

Ezeket az értékeket akkor használja, ha létrehozza a [ComputerVisionClient][ref_computervisionclient] -ügyfél objektumának példányát.

Például a bash terminál használatával állítsa be a környezeti változókat:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>Azure-előfizetéssel rendelkező felhasználók számára a kulcs és a végpont hitelesítő adatainak beolvasása

Ha nem emlékszik a végpontra és a kulcsra, a következő módszer segítségével keresheti meg őket. Ha kulcsot és végpontot kell létrehoznia, használhatja az [Azure-előfizetések tulajdonosait](#if-you-have-an-azure-subscription) , illetve az [Azure-előfizetést nem használó felhasználókra](#if-you-dont-have-an-azure-subscription)vonatkozó módszert.

Az alábbi [Azure CLI][cloud_shell] -kódrészlettel két környezeti változót tölthet fel a Computer Vision fiók **végpontja** és az egyik **kulcsa** alapján (ezeket az értékeket is megtalálhatja a [Azure Portal][azure_portal]). A kódrészlet a bash-rendszerhéjhoz van formázva.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_ENDPOINT=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query endpoint \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Ügyfél létrehozása

Kérje le a végpontot és a kulcsot a környezeti változók közül, majd hozza létre a [ComputerVisionClient][ref_computervisionclient] -ügyfél objektumot.

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get endpoint and key from environment variables
import os
endpoint = os.environ['ACCOUNT_ENDPOINT']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionClient(endpoint, credentials)
```

## <a name="examples"></a>Példák

A következő feladatok bármelyikének használata előtt szüksége lesz egy [ComputerVisionClient][ref_computervisionclient] -ügyfélre.

### <a name="analyze-an-image"></a>Rendszerkép elemzése

A [`analyze_image`][ref_computervisionclient_analyze_image]-ben egyes szolgáltatásokhoz is elemezheti a rendszerképet. [`visual_features`][ref_computervision_model_visualfeatures] A tulajdonsággal állíthatja be a képen végrehajtandó elemzési típusokat. A gyakori értékek `VisualFeatureTypes.tags` a `VisualFeatureTypes.description`következők: és.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>A tulajdonosi tartományok listájának beolvasása

Tekintse át azokat a tulajdonosi tartományokat, [`list_models`][ref_computervisionclient_list_models]amelyek segítségével elemezheti a rendszerképet a használatával. Ezek a tartománynevek a [rendszerkép tartomány szerinti elemzéséhez](#analyze-an-image-by-domain)használatosak. Egy tartomány `landmarks`például:.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Rendszerkép elemzése tartomány szerint

A képeket a tárgy tartománya szerint elemezheti [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]a használatával. A megfelelő tartománynév használatához szerezze be a [támogatott tárgyi tartományok listáját](#get-subject-domain-list) .

```Python
# type of prediction
domain = "landmarks"

# Public domain image of Eiffel tower
url = "https://images.pexels.com/photos/338515/pexels-photo-338515.jpeg"

# English language response
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Rendszerkép szöveges leírásának beolvasása

A rendszerképek nyelvre épülő szöveges leírását a [`describe_image`][ref_computervisionclient_describe_image]segítségével szerezheti be. Ha szöveg-elemzést `max_description` végez a képhez társított kulcsszavakhoz, több leírást is igényelhet a tulajdonsággal. Az alábbi képek `a train crossing a bridge over a body of water` `a large bridge over a body of water`szöveges leírása például a, a és `a train crossing a bridge over a large body of water`a.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Szöveg beolvasása a képből

A képekből bármilyen kézírásos vagy kinyomtatott szöveget is beszerezhet. Ehhez az SDK-nak két hívást kell [`batch_read_file`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) megadnia: és [`get_read_operation_result`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python). Az aszinkron hívás `batch_read_file` . A `get_read_operation_result` hívás eredményében ellenőriznie kell, hogy az első hívás [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] befejeződött-e a szöveges adatok kinyerése előtt. Az eredmények között szerepel a szöveg, valamint a határolókeret koordinátái is a szöveghez.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while True:
    result = client.get_read_operation_result(operationId)
    if result.status not in ['NotStarted', 'Running']:
        break
    time.sleep(1)

# Get data
if result.status == TextOperationStatusCodes.succeeded:
    for textResult in result.recognition_results:
        for line in textResult.lines:
            print(line.text)
            print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Miniatűr készítése

Létrehozhat egy képet tartalmazó miniatűrt (JPG) a [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]következővel:. A miniatűrnek nem kell azonos arányban lennie az eredeti képpel.

Telepítse a **párnát** a példa használatára:

```bash
pip install Pillow
```

A párnak telepítése után a miniatűr rendszerkép létrehozásához használja a következő kódrészletben található csomagot.

```Python
# Pillow package
from PIL import Image

# IO package to create local image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="general"></a>Általános

Ha a Python SDK használatával lép kapcsolatba a [ComputerVisionClient][ref_computervisionclient] -ügyféllel, az osztály [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] a hibák visszaadására szolgál. A szolgáltatás által visszaadott hibák a REST API kérelmek esetében visszaadott HTTP-állapotkódok megfelelnek.

Ha például egy érvénytelen kulccsal rendelkező képet próbál meg elemezni, a `401` rendszer hibát ad vissza. A következő kódrészletben a [hiba][ref_httpfailure] a kivétel kifogásával és a hibával kapcsolatos további információk megjelenítésével van kezelve.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and endpoint are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Átmeneti hibák kezelése újrapróbálkozásokkal

A [ComputerVisionClient][ref_computervisionclient] -ügyféllel való együttműködés során előfordulhat, hogy a szolgáltatás által kikényszerített [díjszabási korlátok][computervision_request_units] vagy más átmeneti problémák, például a hálózati kimaradások okozta átmeneti hibák merülhetnek fel. Az ilyen típusú hibák kezelésével kapcsolatos információkért lásd: [újrapróbálkozási minta][azure_pattern_retry] a Felhőbeli tervezési minták útmutatójában és a kapcsolódó [áramkör-megszakító minta][azure_pattern_circuit_breaker].

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tartalomcímkék alkalmazása képekre](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://azure.microsoft.com/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

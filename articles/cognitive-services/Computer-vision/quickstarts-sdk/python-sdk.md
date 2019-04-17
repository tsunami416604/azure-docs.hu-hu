---
title: 'Gyors útmutató: Python SDK'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutatóban megismerheti, hogyan használható a Python SDK-t a gyakori feladatokhoz az.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/10/2019
ms.author: pafarley
ms.openlocfilehash: c9b30eb89080137e17042feb4458f2601bf48a05
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617971"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Az Azure Cognitive Services számítógépes Látástechnológiai SDK a Pythonhoz

A Computer Vision szolgáltatás a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz. Számítógép Látástechnológiai algoritmus kép tartalma érdekli visual funkcióktól függően különböző módokon elemezheti.

* [Kép elemzése](#analyze-an-image)
* [Tulajdonos tartomány listájának lekérése](#get-subject-domain-list)
* [Tartomány szerint kép elemzése](#analyze-an-image-by-domain)
* [A kép leírását beolvasása](#get-text-description-of-an-image)
* [Kézzel írt szöveg első rendszerképből](#get-text-from-image)
* [Létrehozásához miniatűrön](#generate-thumbnail)

Ezzel a szolgáltatással kapcsolatos további információkért lásd: [Mi az a Computer Vision?] [computervision_docs].

További dokumentáció keres?

* [SDK-forrásdokumentáció](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Cognitive Services – Látástechnológiai dokumentáció](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.6-os +][python]
* Ingyenes [számítógépes Látástechnológiai kulcs] [ computervision_resource] és a kapcsolódó végpont. Ezekre az értékekre szüksége az példány létrehozásakor a [ComputerVisionClient] [ ref_computervisionclient] objektumot. Az alábbi módszerek valamelyikével beolvasni ezeket az értékeket.

### <a name="if-you-dont-have-an-azure-subscription"></a>Ha nem rendelkezik Azure-előfizetéssel

A 7 napig érvényes ingyenes kulcs létrehozása a **[Kipróbálom] [ computervision_resource]** élmény a Computer Vision service a. A kulcs létrehozásakor másolja a kulcs és a végpont nevét. Szüksége lesz a [az ügyfél létrehozása](#create-client).

Tartsa a következő, a kulcs létrehozása után:

* Kulcs értékét: egy 32 karakter hosszúságú karakterlánc formátumban `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Fő végponthoz: a bázisvégpont URL-címe, https\://westcentralus.api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Ha rendelkezik Azure-előfizetéssel

Hozzon létre egy erőforrást az előfizetésében, a legegyszerűbb módszer, hogy használja a következő [Azure CLI-vel] [ azure_cli] parancsot. Ez létrehoz egy Cognitive Services-szolgáltatás-kulcsot, a cognitive services számos is használható. Meg kell adnia a _meglévő_ erőforráscsoport nevét, például "my-cogserv-group" és az új számítógép vision erőforrás nevével, például a "my-számítógép-látás-erőforrás".

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

Telepítse az Azure Cognitive Services számítógép Látástechnológiai SDK Pythonhoz készült [csomag] [ pypi_computervision] a [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Miután a Computer Vision erőforrást hoz létre, meg kell annak **végpont**, és az egyik a **tárfiókkulcsokat** az ügyfél objektumpéldány.

Az példány létrehozásakor használja ezeket az értékeket a [ComputerVisionClient] [ ref_computervisionclient] objektumot.

Például a Bash terminál segítségével beállíthatja a környezeti változókat:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>A felhasználók Azure-előfizetés hitelesítő adatainak lekérése a kulcs és a végpont

Ha nem emlékszik a végpont és a kulcsot, a következő metódust használhatja azokat. Hozzon létre egy kulcsot és a végpontot kell, ha a módszert használhatja [Azure-előfizetés tulajdonosai](#if-you-have-an-azure-subscription) vagy [nem Azure-előfizetéssel rendelkező felhasználók](#if-you-dont-have-an-azure-subscription).

Használja a [Azure CLI-vel] [ cloud_shell] feltölti a Computer Vision fiókkal két környezeti változó az alábbi kódrészlet **végpont** és az egyik a **kulcsok**(is megtalálhatja ezeket az értékeket a [az Azure portal][azure_portal]). A kódrészlet esetében a Bash felületen van formázva.

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

A végpont és -kulcs beszerzéséhez a környezeti változókat, majd hozza létre a [ComputerVisionClient] [ ref_computervisionclient] objektumot.

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

Kell egy [ComputerVisionClient] [ ref_computervisionclient] ügyfélobjektumát használata a következő feladatok közül bármelyik előtt.

### <a name="analyze-an-image"></a>Rendszerkép elemzése

Bizonyos funkciókat a lemezkép elemezheti [ `analyze_image` ] [ ref_computervisionclient_analyze_image]. Használja a [ `visual_features` ] [ ref_computervision_model_visualfeatures] elemzés végrehajtásához a kép típusú beállítandó tulajdonság. Gyakori értékek a következők `VisualFeatureTypes.tags` és `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Tulajdonos tartomány listájának lekérése

Tekintse át a területek, a lemezkép használatával [ `list_models` ] [ ref_computervisionclient_list_models]. A tartománynevek használt amikor [tartományonként kép elemzése](#analyze-an-image-by-domain). A tartomány például `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Tartomány szerint kép elemzése

Elemezheti a tulajdonos tartományonként kép [ `analyze_image_by_domain` ] [ ref_computervisionclient_analyze_image_by_domain]. Első a [támogatott területek listája](#get-subject-domain-list) annak érdekében, hogy a megfelelő tartománynevet használja.

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

### <a name="get-text-description-of-an-image"></a>A kép leírását beolvasása

Egy olyan rendszerképre nyelven alapuló szöveges leírása kap [ `describe_image` ] [ ref_computervisionclient_describe_image]. Több leírásokat a kérelem a `max_description` tulajdonság akkor használatos, ha a lemezképhez hozzárendelt kulcsszavak szövegelemzés. Egy szöveges leírása az alábbi képen például `a train crossing a bridge over a body of water`, `a large bridge over a body of water`, és `a train crossing a bridge over a large body of water`.

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

### <a name="get-text-from-image"></a>Získat text z kép

Kézzel írt vagy nyomtatott szöveg kaphat egy rendszerképből. Ehhez szükséges, hogy az SDK két hívások: [ `batch_read_file` ](https://docs.microsoft.com/en-us/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python#batch-read-file-url--mode--custom-headers-none--raw-false----operation-config-) és [ `get_read_operation_result` ](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python#get-read-operation-result-operation-id--custom-headers-none--raw-false----operation-config-). A hívás `batch_read_file` aszinkron. Az eredményeit a `get_read_operation_result` -hívással kell ellenőrizni, ha az első hívás befejeződött, [ `TextOperationStatusCodes` ] [ ref_computervision_model_textoperationstatuscodes] előtt szöveges adatok kinyeréséhez. Az eredmények tartalmazzák a szöveg, valamint a határolókeret koordinátái meg a szöveget.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextRecognitionMode
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, mode, custom_headers,  raw)

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

### <a name="generate-thumbnail"></a>Létrehozásához miniatűrön

A kép miniatűrjét (JPG) is létrehozhat [ `generate_thumbnail` ] [ ref_computervisionclient_generate_thumbnail]. A miniatűr nem kell az eredeti rendszerkép azonos arányban kell.

Telepítés **párnád** használata ebben a példában:

```bash
pip install Pillow
```

Párnád telepítése után, akkor az alábbi példakód használhatja a csomagot létrehozza a miniatűrt.

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

### <a name="general"></a>Általános kérdések

Amikor dolgozhat a [ComputerVisionClient] [ ref_computervisionclient] ügyfélobjektumot, a Python SDK-val a [ `ComputerVisionErrorException` ] [ ref_computervision_computervisionerrorexception] az osztály az olvasni a hibákat. A szolgáltatás által visszaadott hibák REST API-kérések vissza ugyanazon HTTP-állapotkódok felelnek meg.

Például, ha a kép elemzése érvénytelen kulccsal próbál egy `401` hibát akkor adja vissza. Az alábbi kódrészletben a [hiba] [ ref_httpfailure] szabályosan kezeli a kivétel kölcsönhatásai és megjelenítése a hibával kapcsolatos további információkat.

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

### <a name="handle-transient-errors-with-retries"></a>Újrapróbálkozás átmeneti hibák kezelése

A munka során a [ComputerVisionClient] [ ref_computervisionclient] ügyfél, akkor léphetnek fel átmeneti hibák által okozott [sebességhatárok] [ computervision_request_units] a szolgáltatás, vagy más átmeneti problémák, például a hálózati kimaradások kényszeríti. További információ az ilyen típusú hibák kezelése: [újrapróbálkozási minta] [ azure_pattern_retry] útmutató a tervezési minták Felhőkhöz, és a kapcsolódó [áramkör-megszakító minta] [azure_pattern_circuit_breaker].

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Képek tartalmának címkékkel](../concept-tagging-images.md)

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

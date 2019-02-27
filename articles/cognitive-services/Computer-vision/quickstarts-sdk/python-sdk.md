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
ms.date: 02/26/2019
ms.author: pafarley
ms.openlocfilehash: d14b9c88b447583eedc8b50f4f9acf80ae4e3c75
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889630"
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
* Ingyenes [számítógépes Látástechnológiai kulcs] [ computervision_resource] és társított régió. Ezekre az értékekre szüksége az példány létrehozásakor a [ComputerVisionAPI] [ ref_computervisionclient] objektumot. Az alábbi módszerek valamelyikével beolvasni ezeket az értékeket. 

### <a name="if-you-dont-have-an-azure-subscription"></a>Ha nem rendelkezik Azure-előfizetéssel

A 7 napig érvényes ingyenes kulcs létrehozása a **Kipróbálom** tapasztalható. A kulcs létrehozásakor másolja a kulcs és a régió nevét. Szüksége lesz a [az ügyfél létrehozása](#create-client).

Tartsa a következő, a kulcs létrehozása után:

* Kulcs értékét: egy 32 karakter hosszúságú karakterlánc formátumban `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 
* Kulcsfontosságú terület: az a végpont URL-cím altartomány https://**westcentralus**. api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Ha rendelkezik Azure-előfizetéssel

A Computer Vision API-fiókra van szükség, ha-e a legegyszerűbb módszer, hozzon létre egyet az előfizetésében, használja a következő [Azure CLI-vel] [ azure_cli] parancsot. Válassza ki az erőforráscsoport nevét, például a "my-cogserv-group" és a számítógép vision erőforrás nevét, például a "my-számítógép-látás-erőforrás" kell. 

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind ComputerVision \
    --sku S1 \
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

Miután a Computer Vision erőforrást hoz létre, meg kell annak **régió**, és az egyik a **tárfiókkulcsokat** az ügyfél objektumpéldány.

Az példány létrehozásakor használja ezeket az értékeket a [ComputerVisionAPI] [ ref_computervisionclient] objektumot. 

<!--

For example, use the Bash terminal to set the environment variables:

```Bash
ACCOUNT_REGION=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### For Azure subscription usrs, get credentials for key and region

If you do not remember your region and key, you can use the following method to find them. If you need to create a key and region, you can use the method for [Azure subscription holders](#if-you-have-an-azure-subscription) or for [users without an Azure subscription](#if-you-dont-have-an-azure-subscription).

Use the [Azure CLI][cloud_shell] snippet below to populate two environment variables with the Computer Vision account **region** and one of its **keys** (you can also find these values in the [Azure portal][azure_portal]). The snippet is formatted for the Bash shell.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```
-->

### <a name="create-client"></a>Ügyfél létrehozása

Hozzon létre a [ComputerVisionAPI] [ ref_computervisionclient] objektumot. Módosítsa a régiót és a kulcs értékeit az alábbi példakód a saját értékeire.

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

region = "westcentralus"
key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

credentials = CognitiveServicesCredentials(key)
client = ComputerVisionAPI(region, credentials)
```

Kell egy [ComputerVisionAPI] [ ref_computervisionclient] ügyfélobjektumát használata a következő feladatok közül bármelyik előtt.

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

Kézzel írt vagy nyomtatott szöveg kaphat egy rendszerképből. Ehhez szükséges, hogy az SDK két hívások: [ `recognize_text` ] [ ref_computervisionclient_recognize_text] és [ `get_text_operation_result` ] [ ref_computervisionclient_get_text_operation_result]. A hívás recognize_text aszinkron. Az eredmények között, a get_text_operation_result hívás, ha az első hívás befejeződött, ellenőrizze kell [ `TextOperationStatusCodes` ] [ ref_computervision_model_textoperationstatuscodes] előtt szöveges adatok kinyeréséhez. Az eredmények tartalmazzák a szöveg, valamint a határolókeret koordinátái meg a szöveget. 

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextRecognitionMode
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
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

Amikor dolgozhat a [ComputerVisionAPI] [ ref_computervisionclient] ügyfélobjektumot, a Python SDK-val a [ `ComputerVisionErrorException` ] [ ref_computervision_computervisionerrorexception] osztály szolgál olvasni a hibákat. A szolgáltatás által visszaadott hibák REST API-kérések vissza ugyanazon HTTP-állapotkódok felelnek meg.

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
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Újrapróbálkozás átmeneti hibák kezelése

A munka során a [ComputerVisionAPI] [ ref_computervisionclient] ügyfél, akkor léphetnek fel átmeneti hibák által okozott [sebességhatárok] [ computervision_request_units] kényszerítve a szolgáltatás, vagy más átmeneti problémák, például a hálózati kimaradások. További információ az ilyen típusú hibák kezelése: [újrapróbálkozási minta] [ azure_pattern_retry] útmutató a tervezési minták Felhőkhöz, és a kapcsolódó [áramkör-megszakító minta] [azure_pattern_circuit_breaker].

### <a name="more-sample-code"></a>További mintakód

Több számítógép Vision Python SDK-minták lesznek elérhetők az SDK-k GitHub-adattárában. Ezek a minták kódpéldákat gyakran történt a Computer Vision használata további forgatókönyvek adja meg:

* [recognize_text][recognize-text]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Képek tartalmának címkékkel](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure
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


[computervision_resource]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py


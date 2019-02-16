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
ms.date: 02/15/2019
ms.author: pafarley
ms.openlocfilehash: 3043067f326f782c51be38382070ae0db0e90f4d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314157"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Az Azure Cognitive Services számítógépes Látástechnológiai SDK a Pythonhoz

A Computer Vision szolgáltatás a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz. Számítógép Látástechnológiai algoritmus kép tartalma érdekli visual funkcióktól függően különböző módokon elemezheti. Például Computer Vision is határozza meg, ha kép felnőtt vagy pikáns tartalom található, minden az arcok keresése a képet, első kézzel vagy nyomtatott szöveg. Ez a szolgáltatás népszerű képformátum, például a JPEG, PNG és működik. 

Az alkalmazás használhatja a Computer Vision:

- Az insight képeket elemezhet
- Képek szöveg kinyerése
- Miniatűrök létrehozása

További dokumentáció keres?

* [SDK-forrásdokumentáció](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Cognitive Services – Látástechnológiai dokumentáció](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [ingyenes fiók létrehozása][azure_sub]
* Azure [számítógépes Látástechnológiai erőforrás][computervision_resource]
* [Python 3.6-os +][python]

Ha a Computer Vision API-fiók van szüksége, létrehozhat egyet a [Azure CLI-vel] [ azure_cli] parancsot:

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

## <a name="installation"></a>Telepítés

Telepítse az Azure Cognitive Services számítógép Látástechnológiai SDK- [pip][pip], igény szerint belül egy [virtuális környezet][venv].

### <a name="configure-a-virtual-environment-optional"></a>(Nem kötelező) a virtuális környezet konfigurálása

Nem kötelező, de a alap rendszer és az Azure SDK-környezetek használatakor egy elkülönített megtarthatja a [virtuális környezet][virtualenv]. A következő parancsok futtatásával konfigurálja, majd egy virtuális környezethez, [venv][venv], mint például `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```

### <a name="install-the-sdk"></a>Az SDK telepítése

Telepítse az Azure Cognitive Services számítógép Látástechnológiai SDK Pythonhoz készült [csomag] [ pypi_computervision] a [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Miután a Computer Vision erőforrást hoz létre, meg kell annak **régió**, és az egyik a **tárfiókkulcsokat** az ügyfél objektumpéldány.

Az példány létrehozásakor használja ezeket az értékeket a [ComputerVisionAPI] [ ref_computervisionclient] objektumot. 

### <a name="get-credentials"></a>Hitelesítő adatok beolvasása

Használja a [Azure CLI-vel] [ cloud_shell] feltölti a Computer Vision fiókkal két környezeti változó az alábbi kódrészlet **régió** és az egyik a **kulcsok**(is megtalálhatja ezeket az értékeket a [az Azure portal][azure_portal]). A kódrészlet esetében a Bash felületen van formázva.

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

### <a name="create-client"></a>Ügyfél létrehozása

Miután feltöltötte értékkel, a `ACCOUNT_REGION` és `ACCOUNT_KEY` környezeti változókat is létrehozhat a [ComputerVisionAPI] [ ref_computervisionclient] objektumot.

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

credentials = CognitiveServicesCredentials(key)
client = ComputerVisionAPI(region, credentials)
```

## <a name="usage"></a>Használat

Miután már inicializálva egy [ComputerVisionAPI] [ ref_computervisionclient] ügyfél objektumot is:

* Kép elemzése: Bizonyos szolgáltatások, például az arcok, a színeket, a címkéket egy kép elemezheti.   
* Miniatűrök létrehozása: Hozzon létre az eredeti kép miniatűrjét használandó egyéni JPEG formátumú kép.
* Kérje le a kép leírását: A tulajdonos tartomány alapján a kép leírását beolvasása. 

Ezzel a szolgáltatással kapcsolatos további információkért lásd: [Mi az a Computer Vision?] [computervision_docs].

## <a name="examples"></a>Példák

A következő szakaszok kiterjedő leggyakoribb számítógépes Látástechnológiai feladatokat, beleértve a több kódrészletek:

* [Kép elemzése](#analyze-an-image)
* [Tulajdonos tartomány listájának lekérése](#get-subject-domain-list)
* [Tartomány szerint kép elemzése](#analyze-an-image-by-domain)
* [A kép leírását beolvasása](#get-text-description-of-an-image)
* [Kézzel írt szöveg első rendszerképből](#get-text-from-image)
* [Létrehozásához miniatűrön](#generate-thumbnail)

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
domain = "landmarks"
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"
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

Ez a példa a [párnád] [ pypi_pillow] csomag helyileg az új miniatűr kép mentéséhez.

```Python
from PIL import Image
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

## <a name="next-steps"></a>További lépések

### <a name="more-sample-code"></a>További mintakód

Több számítógép Vision Python SDK-minták lesznek elérhetők az SDK-k GitHub-adattárában. Ezek a minták kódpéldákat gyakran történt a Computer Vision használata további forgatókönyvek adja meg:

* [recognize_text][recognize-text]

### <a name="additional-documentation"></a>További dokumentáció

A Computer Vision Service szélesebb körű dokumentációjáért lásd: a [Azure számítógépes Látástechnológiai dokumentáció] [ computervision_docs] a docs.microsoft.com webhelyen.

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


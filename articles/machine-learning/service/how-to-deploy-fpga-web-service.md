---
title: Modell üzembe helyezése az Azure Machine Learning-FPGA webszolgáltatásként
description: Ismerje meg, hogyan webszolgáltatás üzembe helyezése az Azure Machine Learning-FPGA futó modell.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 09/24/2018
ms.openlocfilehash: ee67585a523ab96b1442d9eee3e9dfd55a758d32
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971484"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Modell üzembe helyezése az Azure Machine Learning-FPGA webszolgáltatásként

Telepíthet egy modellt webszolgáltatásként, amely a [programmable gate arrays (FPGA) mezőben](concept-accelerate-with-fpgas.md).  FPGA-kban használatával biztosít ultramagas közel valós idejű következtetési egyetlen kötegméret mellett is.   

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

- Az Azure Machine Learning-munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Ezekről az előfeltételekről használatával beszerzéséről a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.
 
  - A munkaterület kell lennie a *USA keleti RÉGIÓJA 2* régióban.

  - Telepítse a contrib kiegészítő funkciók:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Hozzon létre, és a modell üzembe helyezése
A bemeneti kép, a szabadkézi előfeldolgozása ResNet-50 használatával egy FPGA a folyamat létrehozása, és futtassa a funkciók egy épít adatkészlet tanított classifer keresztül.

Kövesse az utasításokat:

* A modell folyamat meghatározása
* A modell üzembe helyezése
* A telepített modell feldolgozása
* Telepített szolgáltatások törlése

> [!IMPORTANT]
> Optimalizálható a teljesítmény és a késés, az ügyfél és a végpontot Azure ugyanabban a régióban kell lennie.  Jelenleg az API-k jönnek létre az East US Azure-régióban.

### <a name="get-the-notebook"></a>A notebook beszerzése

Az Ön kényelme érdekében ebben az oktatóanyagban egy Jupyter notebookot, érhető el. Ezek a módszerek segítségével futtathatja a `project-brainwave/project-brainwave-quickstart.ipynb` notebook:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

### <a name="preprocess-image"></a>Kép előfeldolgozása
A folyamat első szakaszában, hogy a képek előfeldolgozása.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```
### <a name="add-featurizer"></a>Featurizer hozzáadása
A modell inicializálása, és töltse le a featurizer használandó ResNet50 kvantált verziójának TensorFlow ellenőrzőpont.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Besorolás hozzáadása
Az osztályozó által igénybe vett rendelkezik betanítva épít adathalmazon.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Szolgáltatás-definíció létrehozása
Most, hogy elvégezhessék a lemezkép előfeldolgozása featurizer és osztályozó, amely a szolgáltatás fut, a szolgáltatás definíciós hozhat létre. A szolgáltatásdefiníció a modellben, amelyre telepítve van a FPGA-szolgáltatás által létrehozott fájlokat. A szolgáltatásdefiníció áll egy folyamat. A folyamat több szakaszra oszlik, amely sorrendben futnak, a rendszer.  TensorFlow-szakaszok Keras-szakaszok és BrainWave szakaszok támogatottak.  A szakasz a a szolgáltatás, amelynek a kimenete a későbbi szakaszában minden egyes fázis a bemenő sorrendben futnak.

Hozzon létre egy TensorFlow szakaszban, adja meg a gráf (ebben az esetben használt alapértelmezett gráf) és a bemeneti tartalmazó munkamenet, és kimeneti tensors erre a szakaszra.  Ez az információ a graph mentéséhez, így futtathatók a szolgáltatásban szolgál.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Modell üzembe helyezése
Létrehoz egy szolgáltatást a szolgáltatás-definícióból.  A munkaterület kell lennie az USA 2. keleti régiója helyen.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Tesztelheti a szolgáltatást
Kép küldése az API-hoz, és tesztelje a választ, adja hozzá a kimeneti Osztályazonosító leképezés épít osztálynév.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Hívják meg a szolgáltatást, és cserélje le a "your image.jpg" fájlnevet alábbi képet, a gép számára. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Szolgáltatás törlése
Törölje a szolgáltatást.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Biztonságos FPGA-webszolgáltatások

FPGA-kban futtat az Azure Machine Learning-modellek adja meg az SSL-támogatás és a kulcs alapú hitelesítés. Ez lehetővé teszi, hogy a szolgáltatás és az ügyfelek által küldött biztonságos adatokat való hozzáférés korlátozása.

> [!IMPORTANT]
> Hitelesítési szolgáltatások, amelyek egy SSL-tanúsítvány és kulcs megadott csak engedélyezve van. 
>
> Ha nem engedélyezi az SSL, az interneten bármely felhasználó tudják a szolgáltatáshoz való meghíváshoz.
>
> Ha engedélyezi az SSL és a hitelesítési kulcs kötelező, ha a szolgáltatás elérésével.

SSL titkosítja az ügyfél és a szolgáltatás között küldött adatokat. Ez a kiszolgáló identitását ellenőrizze az ügyfél által is használt.

Az SSL engedélyezve van a szolgáltatás telepítése vagy frissítése egy már üzembe helyezett szolgáltatás engedélyezéséhez. A lépések ugyanazok:

1. Szerez a tartomány nevét.

2. SSL-tanúsítvány beszerzéséhez.

3. Üzembe helyezése vagy frissítse a szolgáltatást az SSL engedélyezve van.

4. Frissítse a DNS, hogy a szolgáltatás mutasson.

### <a name="acquire-a-domain-name"></a>A tartománynév beszerzése

Ha Ön nem tulajdonosa a tartománynevet, vásárolhat egyet egy __tartományregisztrálóhoz__. A folyamat szolgáltatásfrissítési regisztráló szervezetek, mint a költségek. A regisztráló is használható eszközöket biztosít a tartománynév kezeléséhez. Ezek az eszközök, amelyek a szolgáltatás üzemel, IP-címet (például www.contoso.com) teljesen minősített tartománynév hozzárendelése szolgálnak.

### <a name="acquire-an-ssl-certificate"></a>SSL-tanúsítvány beszerzése

Számos módon SSL-tanúsítvány beszerzése. A leggyakoribb eset az, hogy vásároljon egyet egy __hitelesítésszolgáltató__ (CA). Ha beszerezte a tanúsítványt, függetlenül kell a következő fájlokat:

* A __tanúsítvány__. A tanúsítvány kell tartalmaznia a teljes tanúsítványlánccal, és a PEM-kódolású kell lennie.
* A __kulcs__. A kulcsnak kell lennie a PEM-kódolású.

> [!TIP]
> Ha a hitelesítésszolgáltató nem adja meg a tanúsítvány és kulcs-fájlok PEM-kódolású formájában, a segédprogram használható például [OpenSSL](https://www.openssl.org/) használatával módosíthatja.

> [!IMPORTANT]
> Önaláírt tanúsítványok használandó csakis fejlesztési célokra. Ezek nem használandó éles környezetben.
>
> Ha egy önaláírt tanúsítványt használ, tekintse meg a [önaláírt tanúsítványok a szolgáltatások felhasználásához](#self-signed) adott című fejezetet.

> [!WARNING]
> Amikor tanúsítványt igényel, a cím, a szolgáltatás használatához meg kell adnia a teljesen minősített tartománynevét (FQDN). Például www.contoso.com. A cím a tanúsítványba megjelölve, és az ügyfelek által használt cím képest, a szolgáltatás identitásának érvényesítése során.
>
> A címek nem egyeznek, ha az ügyfelek egy hibaüzenetet fog kapni. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Üzembe helyezése vagy frissítse a szolgáltatást az SSL engedélyezve

Az SSL engedélyezve van a szolgáltatás üzembe helyezéséhez, állítsa be a `ssl_enabled` paramétert `True`. Állítsa be a `ssl_certificate` paraméter értékét a __tanúsítvány__ fájl és a `ssl_key` értékéhez a __kulcs__ fájlt. A következő példa bemutatja, hogy engedélyezve van az SSL-szolgáltatás telepítése:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

Válasza a `create_service` művelet a szolgáltatás IP-címét tartalmazza. Ha a DNS-név leképezése az IP-címet, a szolgáltatás az IP-címet használja.

A válasz is tartalmaz egy __elsődleges kulcs__ és __másodlagos kulcs__ használt a szolgáltatás felhasználásához.

### <a name="update-your-dns-to-point-to-the-service"></a>A szolgáltatás átirányítása a DNS frissítése

Az a tartománynév regisztrálójánál az által biztosított eszközök segítségével frissítse a DNS-rekordot a tartománynév. A rekord a szolgáltatás IP-címre kell mutatnia.

> [!NOTE]
> A regisztráló és az idő függvényében élettartam (TTL) van konfigurálva a tartománynév, akár néhány órát, mielőtt az ügyfelek fel tudják oldani a tartománynév több percet is igénybe vehet.

### <a name="consume-authenticated-services"></a>Hitelesített szolgáltatásainak használata

Az alábbi példák bemutatják, hogyan lehet egy hitelesített service a Python és a C# használata:

> [!NOTE]
> Cserélje le `authkey` az elsődleges vagy másodlagos kulcsot a adja vissza, ha a szolgáltatás létrehozása.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Más gRPC ügyfelek hitelesítése történhet kérelem engedélyeztetési fejléc beállítása. Az általános megközelítés az, hogy hozzon létre egy `ChannelCredentials` objektum, amely egyesíti az `SslCredentials` a `CallCredentials`. Ez hozzáadódik a kérés hitelesítési fejlécéhez. További információk a végrehajtása támogatja a megadott fejlécek,: [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Az alábbi példák bemutatják, hogyan lehet a C# és nyissa meg a fejléc beállítása:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Önaláírt tanúsítványok a szolgáltatások felhasználásához

Az ügyfél hitelesítése egy önaláírt tanúsítvánnyal védett kiszolgálóra két módja van:

* Az ügyfélen, állítsa be a `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` környezeti változót, mutasson a tanúsítványfájlt az ügyfélrendszeren.

* Hozhat létre, amikor egy `SslCredentials` objektumazonosító, illesztheti be a tanúsítvány fájl tartalmát a konstruktor.

Mindkét módszerrel rendelkezik, a tanúsítványt használja, a legfelső szintű tanúsítvány gRPC.

> [!IMPORTANT]
> gRPC nem fogadja el a nem megbízható tanúsítványok. A nem megbízható tanúsítvánnyal meghiúsul egy `Unavailable` állapotkódot. A hiba részleteit tartalmazza `Connection Failed`.

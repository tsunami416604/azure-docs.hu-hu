---
title: Hitelesítés beállítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állíthatja be és konfigurálhatja a hitelesítést különböző erőforrásokhoz és munkafolyamatokhoz az Azure Machine Learningben. A szolgáltatáson belül a hitelesítés konfigurálásának és használatának többmódja van, az egyszerű felhasználói felületen alapuló hitelesítéstől a fejlesztési vagy tesztelési célokat, a teljes Azure Active Directory szolgáltatásegyszerű hitelesítésig.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283536"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Hitelesítés beállítása az Azure Machine Learning-erőforrásokhoz és -munkafolyamatokhoz
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan állíthatja be és konfigurálhatja a hitelesítést az Azure Machine Learning különböző erőforrásaihoz és munkafolyamataihoz. A szolgáltatás hitelesítésének többmódja is van, az egyszerű felhasználói felületen alapuló hitelesítéstől a fejlesztési vagy tesztelési célokat szolgáló egyszerű hitelesítéstől a teljes Azure Active Directory szolgáltatásegyszerű hitelesítésig. Ez a cikk azt is ismerteti, hogy a webszolgáltatás-hitelesítés hogyan működik, valamint az Azure Machine Learning REST API-ban való hitelesítés közötti különbségeket.

Ez az útmutató bemutatja, hogyan kell elvégezni a következő feladatokat:

* Interaktív felhasználói felület-hitelesítés használata teszteléshez/fejlesztéshez
* Egyszerű szolgáltatáshitelesítés beállítása
* Hitelesítés a munkaterületre
* OAuth2.0-beli tulajdonosi típusú tokenek beszereznie az Azure Machine Learning REST API-hoz
* A webszolgáltatás hitelesítésének ismertetése

Tekintse meg a [koncepció cikket](concept-enterprise-security.md) az Azure Machine Learning en belüli biztonság és hitelesítés általános áttekintését.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Machine Learning-munkaterületet.](how-to-manage-workspace.md)
* [Konfigurálja a fejlesztői környezetet](how-to-configure-environment.md) az Azure Machine Learning SDK telepítéséhez, vagy használjon [egy Azure Machine Learning notebook virtuális gépet,](concept-azure-machine-learning-architecture.md#compute-instance) amelyen az SDK már telepítve van.

## <a name="interactive-authentication"></a>Interaktív hitelesítés

A szolgáltatás dokumentációjában szereplő legtöbb példa interaktív hitelesítést használ a Jupyter-jegyzetfüzetekben a tesztelés és a demonstráció egyszerű módjaként. Ez egy könnyű módja annak, hogy tesztelje, amit épít. Két függvényhívás van, amelyek automatikusan rákérdeznek egy felhasználói felületen alapuló hitelesítési folyamatra.

A `from_config()` függvény hívása kiadja a kérdést.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

A `from_config()` függvény a munkaterületi kapcsolat adatait tartalmazó JSON-fájlt keresi. A kapcsolat részleteit explicit módon is `Workspace` megadhatja a konstruktor használatával, amely az interaktív hitelesítést is kéri. Mindkét hívás egyenértékű.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Ha több bérlőhöz is hozzáfér, előfordulhat, hogy importálnia kell az osztályt, és egyértelműen meg kell határoznia, hogy melyik bérlőt célozza meg. A konstruktor hívása `InteractiveLoginAuthentication` a fenti hívásokhoz hasonlóan a bejelentkezést is kéri.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Bár hasznos a teszteléshez és a tanuláshoz, az interaktív hitelesítés nem segít az automatizált vagy fej nélküli munkafolyamatok létrehozásában. Az egyszerű szolgáltatáshitelesítés beállítása a legjobb módszer az SDK-t használó automatizált folyamatokhoz.

## <a name="set-up-service-principal-authentication"></a>Egyszerű szolgáltatáshitelesítés beállítása

Ez a folyamat szükséges egy adott felhasználói bejelentkezéstől leválasztott hitelesítés engedélyezéséhez, amely lehetővé teszi az Azure Machine Learning Python SDK automatikus munkafolyamatokban történő hitelesítését. Az egyszerű szolgáltatás hitelesítése lehetővé teszi [a REST API-ban való hitelesítést](#azure-machine-learning-rest-api-auth)is.

Az egyszerű szolgáltatáshitelesítés beállításához először hozzon létre egy alkalmazásregisztrációt az Azure Active Directoryban, majd adjon hozzáférést az alkalmazás szerepköralapú munkaterületéhez. A telepítés legegyszerűbb módja az [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) az Azure Portalon keresztül. Miután bejelentkezett a portálra, kattintson a `>_` név éhező oldal jobb felső részén található ikonra a rendszerhéj megnyitásához.

Ha még nem használta a felhőrendszerhéjat az Azure-fiókjában, létre kell hoznia egy tárfiók-erőforrást az írott fájlok tárolásához. Általában ez a tárfiók jár elhanyagolható havi költség. Emellett telepítse a gépi tanulási bővítményt, ha korábban nem használta a következő paranccsal.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> A következő lépések végrehajtásához adminisztrátornak kell lennie az előfizetésben.

Ezután futtassa a következő parancsot az egyszerű szolgáltatás létrehozásához. Adjon neki nevet, ebben az esetben **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

A kimenet a következőhöz hasonló JSON lesz. Vegye figyelembe `clientId`a `clientSecret`, `tenantId` , és mezőket, mivel szüksége lesz rájuk a cikk ben található egyéb lépésekhez.

```json
{
    "clientId": "your-client-id",
    "clientSecret": "your-client-secret",
    "subscriptionId": "your-sub-id",
    "tenantId": "your-tenant-id",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com",
    "activeDirectoryGraphResourceId": "https://graph.windows.net",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net"
}
```

Ezután futtassa a következő parancsot az imént `clientId` létrehozott egyszerű szolgáltatás részleteinek leéséhez, a felülről származó értéket használva a `--id` paraméter bemeneteként.

```azurecli-interactive
az ad sp show --id your-client-id
```

A következő egy egyszerűsített példa a JSON kimenet a parancsból. Vegye figyelembe `objectId` a mezőt, mivel szüksége lesz az értékére a következő lépéshez.

```json
{
    "accountEnabled": "True",
    "addIns": [],
    "appDisplayName": "ml-auth",
    ...
    ...
    ...
    "objectId": "your-sp-object-id",
    "objectType": "ServicePrincipal"
}
```

Ezután a következő paranccsal rendelje hozzá a szolgáltatás egyszerű hozzáférést a gépi tanulási munkaterülethez. Szüksége lesz a munkaterület nevére, és az `-w` `-g` erőforráscsoport nevére a és a paraméterekhez. A `--user` paraméterhez használja `objectId` az előző lépés értékét. A `--role` paraméter lehetővé teszi a szolgáltatásnév hozzáférési szerepkörének beállítását, és általában **tulajdonost** vagy **közreműködőt**fog használni. Mindkettő írási hozzáféréssel rendelkezik a meglévő erőforrásokhoz, például a számítási fürtökhöz és adattárakhoz, de csak **a tulajdonos** építheti ki ezeket az erőforrásokat. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Ez a hívás nem hoz létre kimenetet, de most már a munkaterülethez egyszerű szolgáltatáshitelesítés van beállítva.

## <a name="authenticate-to-your-workspace"></a>Hitelesítés a munkaterületen

Most, hogy a szolgáltatás egyszerű hitelesítési engedély engedélyezve van, hitelesítheti magát a munkaterületre az SDK-ban anélkül, hogy fizikailag bejelentkezne felhasználóként. Használja `ServicePrincipalAuthentication` az osztálykonstruktorát, és az előző lépésekből kapott értékeket használja paraméterekként. A `tenant_id` paraméter `tenantId` felülről, `service_principal_id` a `clientId`a `service_principal_password` és `clientSecret`a leképezése a rendszerre.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

A `sp` változó mostmár rendelkezik egy hitelesítési objektummal, amelyet közvetlenül az SDK-ban használ. Általában célszerű a fent használt azonosítókat/titkos kulcsokat a környezeti változókban tárolni, ahogy az a következő kódban látható.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

A Pythonban futó és elsősorban az SDK-t használó automatizált munkafolyamatok esetében ezt az objektumot a legtöbb esetben a hitelesítéshez is használhatja. A következő kód hitelesíti magát a munkaterületen az imént létrehozott hitelesítési objektum használatával.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Az Azure Machine Learning REST API-hitelesítése

A fenti lépésekben létrehozott egyszerű szolgáltatás az Azure Machine Learning [REST API-hoz](https://docs.microsoft.com/rest/api/azureml/)való hitelesítéshez is használható. Az Azure Active [Directory-ügyfél hitelesítő adatok támogatási folyamat,](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)amely lehetővé teszi a szolgáltatás-szolgáltatás hívások fej nélküli hitelesítés automatizált munkafolyamatokban. A példák pythonban és node.js-ben is implementálhatók az [ADAL-kódtárral,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) de bármely open-source-t, amely támogatja az OpenID Connect 1.0-t. 

> [!NOTE]
> Az MSAL.js egy újabb könyvtár, mint az ADAL, de nem lehet szolgáltatás-szolgáltatás hitelesítést végezni az Ügyfél hitelesítő adataival az MSAL.js fájllal, mivel ez elsősorban egy interaktív/felhasználói felületi hitelesítésre szánt ügyféloldali könyvtár, amely egy adott felhasználóhoz van kötve. Javasoljuk, hogy az ADAL használatát az alábbiak szerint használja az automatizált munkafolyamatok rest API-val történő létrehozásához.

### <a name="nodejs"></a>Node.js

Az alábbi lépésekkel hozzon létre egy auth token segítségével Node.js. A környezetében `npm install adal-node`futtassa a fut. Ezután használja `tenantId` `clientId`a `clientSecret` , és a szolgáltatás egyszerű létrehozott a fenti lépésekben, mint értékek a megfelelő változók a következő parancsfájlban.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

A `tokenResponse` változó egy olyan objektum, amely tartalmazza a jogkivonatot és a kapcsolódó metaadatokat, például a lejárati időt. A jogkivonatok 1 óráig érvényesek, és frissíthetők ugyanazzal a hívással egy új jogkivonat lekéréséhez. A következő egy minta válasz.

```javascript
{ 
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id' 
}
```

Használja `accessToken` a tulajdonságot az auth token beolvasásához. Tekintse meg a REST API dokumentációpéldákat, hogyan használhatja a jogkivonatot API-hívások. [REST API documentation](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)

### <a name="python"></a>Python 

A következő lépésekkel hozzon létre egy hitelesítési jogkivonatot a Python használatával. A környezetében `pip install adal`futtassa a fut. Ezután használja `tenantId` `clientId`a `clientSecret` , és a fenti lépésekben létrehozott egyszerű szolgáltatásból a következő parancsfájl megfelelő változóinak értékeként.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

A `token_response` változó egy szótár, amely tartalmazza a jogkivonatot és a kapcsolódó metaadatokat, például a lejárati időt. A jogkivonatok 1 óráig érvényesek, és frissíthetők ugyanazzal a hívással egy új jogkivonat lekéréséhez. A következő egy minta válasz.

```python
{
    'tokenType': 'Bearer', 
    'expiresIn': 3599, 
    'expiresOn': '2019-12-17 19:47:15.150205', 
    'resource': 'https://management.azure.com/', 
    'accessToken': 'random-oauth-token', 
    'isMRRT': True, 
    '_clientId': 'your-client-id', 
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Az `token_response["accessToken"]` auth token beolvasásához használható. Tekintse meg a REST API dokumentációpéldákat, hogyan használhatja a jogkivonatot API-hívások. [REST API documentation](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)

## <a name="web-service-authentication"></a>Webszolgáltatás hitelesítése

Az Azure Machine Learning webszolgáltatásai a fentiektől eltérő hitelesítési mintát használnak. A telepített webszolgáltatások hitelesítésének legegyszerűbb módja a **kulcsalapú hitelesítés**használata, amely statikus tulajdonosi típusú hitelesítési kulcsokat hoz létre, amelyeket nem kell frissíteni. Ha csak egy telepített webszolgáltatásnak kell hitelesítenie magát, nem kell beállítania a szolgáltatáselvi hitelesítést a fentiek szerint.

Az Azure Kubernetes-szolgáltatásban telepített webszolgáltatások kulcsalapú hitelesítési érték alapértelmezés szerint *engedélyezve van.* Az Azure Container Instances üzembe helyezett szolgáltatások kulcsalapú hitelesítési alapértelmezés szerint `auth_enabled=True`le van *tiltva,* de az ACI webszolgáltatás létrehozásakor beállítással engedélyezheti azt. Az alábbi példa egy ACI-telepítési konfiguráció t, kulcsalapú hitelesítési konfigurációt hozhat létre.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Ezután használhatja az egyéni ACI-konfigurációt az osztály használatával történő `Model` központi telepítésben.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Az auth kulcsok beolvasásához használja a használatát. `aci_service.get_keys()` Kulcs újragenerálásához használja `regen_key()` a függvényt, és adja át **az Elsődleges** vagy **a Másodlagos**függvényt.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

A webszolgáltatások is támogatják a jogkivonat-alapú hitelesítést, de csak az Azure Kubernetes-szolgáltatás központi telepítései esetén. A hitelesítésről további információt [a](how-to-consume-web-service.md) webes szolgáltatások kalkulálásáról talál.

### <a name="token-based-web-service-authentication"></a>Tokenalapú webszolgáltatás-hitelesítés

Ha engedélyezi a token hitelesítést egy webszolgáltatáshoz, a felhasználóknak be kell mutatniuk egy Azure Machine Learning JSON webjogkivonatot a webszolgáltatásnak, hogy elérhessek. A jogkivonat egy megadott időkeret után lejár, és frissíteni kell a hívások folytatásához.

* A tokenhitelesítés alapértelmezés szerint le van **tiltva** az Azure Kubernetes szolgáltatásra való üzembe helyezéskor.
* A tokenhitelesítés **nem támogatott,** ha az Azure Container Instances üzembe helyezésekor.

A tokenhitelesítés vezérléséhez `token_auth_enabled` használja a paramétert központi telepítés létrehozásakor vagy frissítésekor.

Ha a tokenhitelesítés engedélyezve `get_token` van, a módszer segítségével lekérheti a JSON webtoken (JWT) és a jogkivonat lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Új jogkivonatot kell kérnie `refresh_by` a jogkivonat ideje után. Ha a Python SDK-n kívüli jogkivonatokat kell frissítenie, az egyik lehetőség a REST `service.get_token()` API szolgáltatás-egyszerű hitelesítéssel való használata a hívás rendszeres időközönként, ahogy azt korábban tárgyalta.
>
> Azt javasoljuk, hogy hozza létre az Azure Machine Learning-munkaterületet ugyanabban a régióban, mint az Azure Kubernetes service-fürt. 
>
> A jogkivonattal történő hitelesítéshez a webszolgáltatás hívást kezdeményez arra a régióra, amelyben az Azure Machine Learning-munkaterület létrejön. Ha a munkaterület régiója nem érhető el, akkor nem fog tudni beolvasni egy jogkivonatot a webszolgáltatáshoz, még akkor sem, ha a fürt a munkaterülettől eltérő régióban található. Az eredmény az, hogy az Azure AD-hitelesítés nem érhető el, amíg a munkaterület régiója újra elérhető. 
>
> Továbbá minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál tovább tart a token beolvasása.

## <a name="next-steps"></a>További lépések

* [Lemezképbesorolási modell betanítása és üzembe helyezése.](tutorial-train-models-with-aml.md)
* [Használja fel a webszolgáltatásként üzembe helyezett Azure Machine Learning-modellt.](how-to-consume-web-service.md)

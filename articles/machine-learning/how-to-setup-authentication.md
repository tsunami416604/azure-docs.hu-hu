---
title: Hitelesítés beállítása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan állíthatja be és konfigurálhatja a hitelesítési módszert a Azure Machine Learning különböző erőforrásaihoz és munkafolyamataihoz. A szolgáltatáson belüli hitelesítés több módon is konfigurálható és használható, az egyszerű, felhasználói felületen alapuló hitelesítéstől kezdve a fejlesztési és tesztelési célokra, a teljes Azure Active Directory a szolgáltatás egyszerű hitelesítése.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283536"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning erőforrások és munkafolyamatok hitelesítésének beállítása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan állíthatja be és konfigurálhatja a különböző erőforrások és munkafolyamatok hitelesítését Azure Machine Learningban. A szolgáltatással való hitelesítés több módon is elvégezhető, az egyszerű, felhasználói felületen alapuló hitelesítéstől kezdve a fejlesztési vagy tesztelési célokra, a teljes Azure Active Directory a szolgáltatás egyszerű hitelesítése. Ez a cikk azt is ismerteti, hogyan működik a webes szolgáltatás hitelesítése, illetve hogyan lehet hitelesíteni a Azure Machine Learning REST API.

Ez az útmutató bemutatja, hogyan végezheti el a következő feladatokat:

* Interaktív felhasználói felületi hitelesítés használata teszteléshez/fejlesztéshez
* Egyszerű szolgáltatás hitelesítésének beállítása
* Hitelesítés a munkaterületen
* OAuth 2.0 tulajdonosi jogkivonatok beszerzése Azure Machine Learning REST API
* A webszolgáltatások hitelesítésének megismerése

A Azure Machine Learningon belüli biztonság és hitelesítés általános áttekintését lásd a [koncepcióról szóló cikkben](concept-enterprise-security.md) .

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Machine learning munkaterületet](how-to-manage-workspace.md).
* [Állítsa be a fejlesztési környezetet](how-to-configure-environment.md) az Azure Machine learning SDK telepítéséhez, vagy használjon olyan [Azure Machine learning notebook virtuális gépet](concept-azure-machine-learning-architecture.md#compute-instance) , amely már telepítve van az SDK-val.

## <a name="interactive-authentication"></a>Interaktív hitelesítés

A szolgáltatás dokumentációjában szereplő példák többsége interaktív hitelesítést használ a Jupyter jegyzetfüzetekben, egyszerű tesztelési és demonstrációs módszerként. Ez egy egyszerű módja annak, hogy tesztelje, amit épít. Kétféle függvény hívása automatikusan rákérdez a felhasználói felület alapú hitelesítési folyamatra.

A `from_config()` függvény meghívásakor a rendszer kiadja a kérdést.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

A `from_config()` függvény megkeresi a munkaterület-kapcsolatok adatait tartalmazó JSON-fájlt. A kapcsolat részleteit explicit módon is megadhatja a `Workspace` konstruktor használatával, amely az interaktív hitelesítést is kéri. Mindkét hívás egyenértékű.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Ha több bérlőhöz fér hozzá, előfordulhat, hogy importálnia kell az osztályt, és explicit módon meg kell határoznia, hogy melyik bérlőt célozza meg. A konstruktor `InteractiveLoginAuthentication` meghívása azt is kéri, hogy a fenti hívásokhoz hasonló módon jelentkezzen be.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

A teszteléshez és a tanuláshoz is hasznos, az interaktív hitelesítés nem segít az automatizált vagy a fej nélküli munkafolyamatok létrehozásában. Az egyszerű szolgáltatás hitelesítésének beállítása az SDK-t használó automatizált folyamatok esetében ajánlott módszer.

## <a name="set-up-service-principal-authentication"></a>Egyszerű szolgáltatás hitelesítésének beállítása

Ez a folyamat egy adott felhasználói bejelentkezéstől leválasztott hitelesítés engedélyezéséhez szükséges, amely lehetővé teszi, hogy hitelesítse a Azure Machine Learning Python SDK-t az automatizált munkafolyamatokban. Az egyszerű szolgáltatás hitelesítése lehetővé teszi [a REST API hitelesítését](#azure-machine-learning-rest-api-auth)is.

Az egyszerű szolgáltatás hitelesítésének beállításához először létre kell hoznia egy alkalmazást a Azure Active Directoryban, majd az alkalmazás szerepköralapú hozzáférését kell adnia az ML-munkaterülethez. A beállítás végrehajtásának legegyszerűbb módja a Azure Portal [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) . Miután bejelentkezett a portálra, kattintson az `>_` oldal jobb felső sarkában található ikonra a Shell megnyitásához.

Ha még nem használta a Cloud shellt az Azure-fiókjában, létre kell hoznia egy Storage-fiók erőforrást az összes megírt fájl tárolásához. Általánosságban elmondható, hogy ez a Storage-fiók egy elhanyagolható havi költséget von maga után. Ha korábban még nem használta a következő paranccsal, telepítse a Machine learning-bővítményt.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Az alábbi lépések végrehajtásához rendszergazdának kell lennie az előfizetésben.

Ezután futtassa a következő parancsot az egyszerű szolgáltatásnév létrehozásához. Adja meg a nevet, ebben az esetben a **ml-Auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

A kimenet az alábbihoz hasonló JSON lesz. Jegyezze fel a, `clientId` `clientSecret`és `tenantId` a mezőket, ahogy a cikk más lépéseire is szüksége lesz.

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

Ezután futtassa a következő parancsot, hogy lekérje az imént létrehozott szolgáltatásnév részleteit, a fentiekben megadott `clientId` értéket használva a `--id` paraméter bemenetének.

```azurecli-interactive
az ad sp show --id your-client-id
```

A következő példa a parancs JSON-kimenetének egyszerűsített példáját szemlélteti. Jegyezze fel a `objectId` mezőt, mivel a következő lépéshez szükséges értéknek kell lennie.

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

Ezután a következő parancs használatával rendeljen hozzá egyszerű szolgáltatásnevet a Machine learning-munkaterülethez. Szüksége lesz a munkaterület nevére és az erőforráscsoport nevére a és `-w` `-g` a paraméterek számára. A `--user` paraméter esetében használja az `objectId` előző lépésben megadott értéket. A `--role` paraméter lehetővé teszi az egyszerű szolgáltatásnév hozzáférési szerepkörének beállítását, és általában a **tulajdonost** vagy a **közreműködőt**fogja használni. Mindkettő rendelkezik írási hozzáféréssel a meglévő erőforrásokhoz, például a számítási fürtökhöz és az adattárolóhoz, de csak a **tulajdonos** tudja kiépíteni ezeket az erőforrásokat. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Ez a hívás nem hoz létre kimenetet, de most már rendelkezik egyszerű szolgáltatásnév-hitelesítéssel a munkaterületen.

## <a name="authenticate-to-your-workspace"></a>Hitelesítés a munkaterületen

Most, hogy engedélyezte az egyszerű szolgáltatásnév hitelesítését, a felhasználóként való fizikai bejelentkezés nélkül is hitelesítheti a munkaterületét az SDK-ban. Használja az `ServicePrincipalAuthentication` osztály konstruktort, és használja az előző lépésekben kapott értékeket paraméterekként. A `tenant_id` `tenantId` paraméter leképezi a fenti `service_principal_id` , `clientId`a és `service_principal_password` a leképezését `clientSecret`a következőre:.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

A `sp` változó most már rendelkezik egy olyan hitelesítési objektummal, amelyet közvetlenül az SDK-ban használ. Általánosságban elmondható, hogy a fent használt azonosítókat/titkokat a következő kódban látható módon tárolja a környezeti változókon.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

A Pythonban futó és az SDK-t használó automatizált munkafolyamatok esetében az adott objektumot a legtöbb esetben használhatja a hitelesítéshez. A következő kód hitelesíti a munkaterületet az imént létrehozott Auth objektum használatával.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API hitelesítés

A fenti lépésekben létrehozott egyszerű szolgáltatásnév a Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/)való hitelesítéshez is használható. A Azure Active Directory ügyfél- [hitelesítő adatok engedélyezési folyamatát](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)használja, amely lehetővé teszi, hogy a szolgáltatások közötti hívás a fej nélküli hitelesítéshez automatizált munkafolyamatokban történjen. A példák a Python és a Node. js [ADAL-könyvtárával](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) valósulnak meg, de az OpenID Connect 1,0-et támogató nyílt forráskódú kódtár is használható. 

> [!NOTE]
> A MSAL. js egy újabb könyvtár, mint a ADAL, de a MSAL. js használatával nem végezhető el a szolgáltatások közötti hitelesítés, mivel ez elsősorban az olyan ügyféloldali függvénytár, amely egy adott felhasználóhoz kötött interaktív vagy felhasználói felületi hitelesítéshez készült. Javasoljuk, hogy az alábbi módon használja az ADAL-t az automatizált munkafolyamatok létrehozásához a REST API használatával.

### <a name="nodejs"></a>Node.js

A Node. js használatával a következő lépésekkel hozhatja végre a hitelesítési tokent. A környezetben futtassa a parancsot `npm install adal-node`. Ezután használja `tenantId` `clientId`a, a és `clientSecret` a parancsot a fenti lépésekben létrehozott szolgáltatásnév alapján a következő parancsfájlban található egyező változók értékeiként.

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

A változó `tokenResponse` egy olyan objektum, amely tartalmazza a jogkivonatot és a társított metaadatokat, például a lejárati időt. A tokenek 1 órára érvényesek, és az új jogkivonat lekéréséhez futtassa újra ugyanezt a hívást. A következő egy példa erre a válaszra.

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

Az Auth `accessToken` token beolvasásához használja a tulajdonságot. Tekintse meg a [REST API dokumentációját](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) , amely bemutatja, hogyan használhatja a tokent API-hívások létrehozásához.

### <a name="python"></a>Python 

A következő lépésekkel hozhatja végre a hitelesítési tokent a Python használatával. A környezetben futtassa a parancsot `pip install adal`. Ezután használja a `tenantId`, `clientId`a és `clientSecret` a parancsot a fenti lépésekben a következő parancsfájlban a megfelelő változók értékeiként létrehozott egyszerű szolgáltatásnév alapján.

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

A változó `token_response` egy olyan szótár, amely tartalmazza a tokent és a kapcsolódó metaadatokat, például a lejárati időt. A tokenek 1 órára érvényesek, és az új jogkivonat lekéréséhez futtassa újra ugyanezt a hívást. A következő egy példa erre a válaszra.

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

Az `token_response["accessToken"]` hitelesítési jogkivonat beolvasására használatos. Tekintse meg a [REST API dokumentációját](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) , amely bemutatja, hogyan használhatja a tokent API-hívások létrehozásához.

## <a name="web-service-authentication"></a>Webes szolgáltatás hitelesítése

A Azure Machine Learning webszolgáltatásai a fent ismertetett módon eltérő hitelesítési mintát használnak. A központilag telepített webszolgáltatások hitelesítésének legegyszerűbb módja, ha **kulcs-alapú hitelesítést**használ, amely statikus tulajdonosi típusú hitelesítési kulcsokat hoz létre, amelyeket nem kell frissíteni. Ha csak a központilag telepített webszolgáltatásokhoz kell hitelesítést végeznie, nem kell beállítania a szolgáltatás elve szerinti hitelesítést a fent látható módon.

Az Azure Kubernetes szolgáltatásban üzembe helyezett webszolgáltatások esetében alapértelmezés szerint *engedélyezve* van a kulcs alapú hitelesítés. Azure Container Instances központilag telepített szolgáltatások alapértelmezés szerint *le vannak tiltva* a kulcs alapú hitelesítéssel, de az ACI `auth_enabled=True`-webszolgáltatások létrehozásakor is engedélyezhető. Az alábbi példa egy ACI üzembe helyezési konfiguráció létrehozására szolgál, amely lehetővé teszi a kulcs alapú hitelesítés használatát.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Ezt követően használhatja az egyéni ACI-konfigurációt az üzembe helyezés `Model` során a osztály használatával.

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

Az Auth kulcsok beolvasásához használja `aci_service.get_keys()`a következőt:. A kulcs újragenerálása érdekében használja a `regen_key()` függvényt, és adja át az **elsődleges** vagy a **másodlagos**műveletet.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

A webszolgáltatások is támogatják a jogkivonat-alapú hitelesítést, de csak az Azure Kubernetes Service-példányok esetében. A hitelesítéssel kapcsolatos további információkért tekintse [meg a webszolgáltatások használatáról](how-to-consume-web-service.md) szóló témakört.

### <a name="token-based-web-service-authentication"></a>Jogkivonat-alapú webszolgáltatások hitelesítése

Ha engedélyezi a jogkivonat-hitelesítést egy webszolgáltatáshoz, a felhasználóknak egy Azure Machine Learning JSON Web Token kell bemutatnia a webszolgáltatásnak az eléréséhez. A jogkivonat egy megadott időkereten belül lejár, és a hívások folytatásához frissíteni kell.

* **Alapértelmezés szerint** a jogkivonat-hitelesítés le van tiltva az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A jogkivonat-hitelesítés **nem támogatott** , ha Azure Container instances telepíti.

A jogkivonat-hitelesítés vezérléséhez használja `token_auth_enabled` a paramétert a központi telepítés létrehozásakor vagy frissítésekor.

Ha engedélyezve van a jogkivonat-hitelesítés, a `get_token` metódus használatával kérhet le egy JSON web token (JWT) és a jogkivonat lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat `refresh_by` időpontját követően új jogkivonatot kell kérnie. Ha a Python SDK-n kívülre kell frissítenie a jogkivonatokat, az egyik lehetőség az, hogy a REST API a szolgáltatás-egyszerű hitelesítéssel rendszeres időközönként a `service.get_token()` hívást a korábban tárgyalt módon használja.
>
> Javasoljuk, hogy az Azure Kubernetes Service-fürttel azonos régióban hozza létre Azure Machine Learning munkaterületét. 
>
> A webszolgáltatások tokenekkel történő hitelesítéséhez a webszolgáltatás meghívja a Azure Machine Learning munkaterület létrehozásához használt régiót. Ha a munkaterület régiója nem érhető el, nem tud beolvasni egy jogkivonatot a webszolgáltatáshoz, még akkor sem, ha a fürt egy másik régióban található a munkaterületen. Ennek eredményeképpen az Azure AD-hitelesítés nem érhető el, amíg a munkaterület régiója újra elérhetővé nem válik. 
>
> Továbbá minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál hosszabb ideig tart a token beolvasása.

## <a name="next-steps"></a>További lépések

* [Rendszerkép-besorolási modell betanítása és üzembe helyezése](tutorial-train-models-with-aml.md).
* [Webszolgáltatásként üzembe helyezett Azure Machine learning modell](how-to-consume-web-service.md)használata.

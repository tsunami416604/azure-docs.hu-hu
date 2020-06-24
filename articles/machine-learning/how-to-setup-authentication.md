---
title: Hitelesítés beállítása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan állíthatja be és konfigurálhatja a hitelesítési módszert a Azure Machine Learning különböző erőforrásaihoz és munkafolyamataihoz. A szolgáltatáson belüli hitelesítés több módon is konfigurálható és használható, az egyszerű, felhasználói felületen alapuló hitelesítéstől kezdve a fejlesztési és tesztelési célokra, a teljes Azure Active Directory a szolgáltatás egyszerű hitelesítése.
services: machine-learning
author: larryfr
ms.author: larryfr
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 34641e7a883f6b07fe63595cf5750df2569640f8
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84974687"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning erőforrások és munkafolyamatok hitelesítésének beállítása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Megtudhatja, hogyan végezhet hitelesítést a Azure Machine Learning munkaterületén és a webszolgáltatásként üzembe helyezett modelleken.

Általában kétféle hitelesítés használható a Azure Machine Learning használatával:

* __Interaktív__: a fiókját a Azure Active Directory közvetlenül a hitelesítéshez, vagy a hitelesítéshez használt token beszerzéséhez használja. Az interaktív hitelesítés a kísérletezés és az ismétlődő fejlesztés során használatos. Vagy az erőforrásokhoz (például webszolgáltatásokhoz) való hozzáférés szabályozása felhasználónkénti alapon.
* __Egyszerű szolgáltatásnév__: egyszerű szolgáltatásnév-fiókot hoz létre a Azure Active Directoryban, és a hitelesítéshez vagy a jogkivonat lekéréséhez használja azt. Az egyszerű szolgáltatásnév akkor használatos, ha automatikus folyamatra van szükség a szolgáltatásban való hitelesítéshez anélkül, hogy felhasználói beavatkozásra lenne szükség. Például egy folyamatos integrációs és üzembe helyezési parancsfájl, amely a modell minden egyes változásakor betanítja és teszteli a modellt. Egy egyszerű szolgáltatás használatával is lekérheti a tokent egy webszolgáltatáshoz való hitelesítéshez, ha nem szeretné, hogy a szolgáltatás végfelhasználója hitelesítse magát. Vagy ha a végfelhasználói hitelesítés nem közvetlenül a Azure Active Directory használatával történik.

A használt hitelesítési típustól függetlenül a szerepköralapú hozzáférés-vezérlés (RBAC) az erőforrásokhoz engedélyezett hozzáférés szintjének hatókörére szolgál. Egy telepített modell hozzáférési jogkivonatának beolvasására használt fióknak például olvasási hozzáféréssel kell rendelkeznie a munkaterülethez. További információ a RBAC: [Azure Machine learning munkaterület hozzáférésének kezelése](how-to-assign-roles.md).

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Machine learning munkaterületet](how-to-manage-workspace.md).
* [Állítsa be a fejlesztési környezetet](how-to-configure-environment.md) az Azure Machine learning SDK telepítéséhez, vagy használjon olyan [Azure Machine learning notebook virtuális gépet](concept-azure-machine-learning-architecture.md#compute-instance) , amely már telepítve van az SDK-val.

## <a name="interactive-authentication"></a>Interaktív hitelesítés

A dokumentációban és a mintákban a legtöbb példa interaktív hitelesítést használ. Az SDK használatakor például két függvényhívás van, amely automatikusan rákérdez a felhasználói felületen alapuló hitelesítési folyamatra:

* A függvény meghívásakor `from_config()` a rendszer kiadja a kérdést.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    A `from_config()` függvény megkeresi a munkaterület-kapcsolatok adatait tartalmazó JSON-fájlt.

* Ha a `Workspace` konstruktort használja az előfizetés, az erőforráscsoport és a munkaterület adatainak megadására, a rendszer az interaktív hitelesítést is kéri.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Ha több bérlőhöz fér hozzá, előfordulhat, hogy importálnia kell az osztályt, és explicit módon meg kell határoznia, hogy melyik bérlőt célozza meg. A konstruktor meghívása azt `InteractiveLoginAuthentication` is kéri, hogy a fenti hívásokhoz hasonló módon jelentkezzen be.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

## <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az egyszerű szolgáltatásnév (SP) hitelesítéséhez először létre kell hoznia az SP-t, és hozzáférést kell biztosítania a munkaterülethez. Ahogy azt korábban említettük, az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozható a hozzáférés, ezért azt is el kell döntenie, hogy milyen hozzáférést biztosít az SP-nek.

> [!IMPORTANT]
> Egyszerű szolgáltatásnév használata esetén adja meg a által használt __feladathoz szükséges minimális hozzáférést__ . Előfordulhat például, hogy nem ad meg egy egyszerű szolgáltatás tulajdonosának vagy közreműködői hozzáférését, ha az az összes használatban van a webes telepítés hozzáférési jogkivonatának beolvasásához.
>
> A legkevesebb hozzáférés megadásának oka az, hogy egy egyszerű szolgáltatásnév jelszót használ a hitelesítéshez, és a jelszót egy Automation-parancsfájl részeként lehet tárolni. Ha a jelszó kiszivárgott, az adott feladatokhoz szükséges minimális hozzáférés minimálisra csökken az SP rosszindulatú használata esetén.

Az [Azure parancssori](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)felületének használatával a legkönnyebben hozhat létre egy SP-t, és hozzáférést biztosíthat a munkaterületéhez. Egyszerű szolgáltatásnév létrehozásához és a munkaterülethez való hozzáférés biztosításához kövesse az alábbi lépéseket:

> [!NOTE]
> Ezen lépések végrehajtásához rendszergazdai jogosultsággal kell rendelkeznie az előfizetésben.

1. Hitelesítés az Azure-előfizetésében:

    ```azurecli-interactive
    az login
    ```

    Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőt, és követnie kell a parancssor utasításait. Az utasítások [https://aka.ms/devicelogin](https://aka.ms/devicelogin) egy engedélyezési kód böngészését és beírását foglalják magukban.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    A hitelesítés egyéb módszereivel kapcsolatban lásd: [Bejelentkezés az Azure CLI-vel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

1. Telepítse a Azure Machine Learning bővítményt:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Hozza létre az egyszerű szolgáltatásnevet. A következő példában egy **ml-Auth** nevű SP jön létre:

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    A kimenet az alábbihoz hasonló JSON lesz. Jegyezze fel a `clientId` , `clientSecret` és a `tenantId` mezőket, ahogy a cikk más lépéseire is szüksége lesz.

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

1. Kérje le az egyszerű szolgáltatásnév adatait az `clientId` előző lépésben visszaadott érték használatával:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    A következő JSON a parancs kimenetének egyszerűsített példája. Jegyezze fel a `objectId` mezőt, mivel a következő lépéshez szükséges értéknek kell lennie.

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

1. Engedélyezi az SP számára a Azure Machine Learning munkaterület elérését. Szüksége lesz a munkaterület nevére és az erőforráscsoport nevére a és a paraméterek számára `-w` `-g` . A `--user` paraméter esetében használja az `objectId` előző lépésben megadott értéket. A `--role` paraméter lehetővé teszi az egyszerű szolgáltatásnév hozzáférési szerepkörének beállítását. A következő példában az SP a **tulajdonosi** szerepkörhöz van rendelve. 

    > [!IMPORTANT]
    > A tulajdonosi hozzáférés lehetővé teszi, hogy az egyszerű szolgáltatás gyakorlatilag bármilyen műveletet hajtson végre a munkaterületen. Ez a dokumentum a hozzáférés megadásának bemutatására szolgál. éles környezetben a Microsoft azt javasolja, hogy a szolgáltatásnak a szükséges szerepkör elvégzéséhez szükséges minimális hozzáférést adja meg. További információt a [Azure Machine learning munkaterület hozzáférésének kezelése](how-to-assign-roles.md)című témakörben talál.

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Ez a hívás nem eredményez sikeres kimenetet.

### <a name="use-a-service-principal-from-the-sdk"></a>Egyszerű szolgáltatásnév használata az SDK-ból

Ha az SDK-ban szeretné hitelesíteni a munkaterületet, használja a szolgáltatásnevet az `ServicePrincipalAuthentication` osztály konstruktorával. Használja a szolgáltató paraméterként való létrehozásakor kapott értékeket. A `tenant_id` paraméter leképezi a `tenantId` fenti, a `service_principal_id` és a `clientId` leképezését a következőre: `service_principal_password` `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

A `sp` változó most már rendelkezik egy olyan hitelesítési objektummal, amelyet közvetlenül az SDK-ban használ. Általánosságban elmondható, hogy a fent használt azonosítókat/titkokat a következő kódban látható módon tárolja a környezeti változókon. A környezeti változókban való tárolás megakadályozza, hogy az adatok véletlenül bekerüljenek egy GitHub-tárházba.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

A Pythonban futó és az SDK-t használó automatizált munkafolyamatok esetében az adott objektumot a legtöbb esetben használhatja a hitelesítéshez. A következő kód a létrehozott Auth objektum használatával hitelesíti a munkaterületet.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Egyszerű szolgáltatásnév használata az Azure CLI-vel

Az Azure CLI-parancsokhoz használhat egyszerű szolgáltatást. További információ: [Bejelentkezés egyszerű szolgáltatásnév használatával](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Egyszerű szolgáltatásnév használata a REST API (előzetes verzió)

Az egyszerű szolgáltatásnév a Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/) (előzetes verzió) hitelesítésére is használható. A Azure Active Directory ügyfél- [hitelesítő adatok engedélyezési folyamatát](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)használja, amely lehetővé teszi, hogy a szolgáltatások közötti hívás a fej nélküli hitelesítéshez automatizált munkafolyamatokban történjen. A példák a Python és a Node.js [ADAL-függvénytárával](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) valósulnak meg, de az OpenID Connect 1,0-et támogató nyílt forráskódú kódtár is használható.

> [!NOTE]
> MSAL.js egy újabb könyvtár, mint a ADAL, de az ügyfél hitelesítő adataival nem végezhető el a szolgáltatás és a szolgáltatás közötti hitelesítés MSAL.js, mivel ez elsősorban olyan ügyféloldali függvénytár, amely egy adott felhasználóhoz kötött interaktív/felhasználói felületi hitelesítéshez készült. Javasoljuk, hogy az alábbi módon használja az ADAL-t az automatizált munkafolyamatok létrehozásához a REST API használatával.

#### <a name="nodejs"></a>Node.js

A következő lépésekkel hozhatja Node.js használatával a hitelesítési tokent. A környezetben futtassa a parancsot `npm install adal-node` . Ezután használja a, `tenantId` a `clientId` és a `clientSecret` parancsot a fenti lépésekben létrehozott szolgáltatásnév alapján a következő parancsfájlban található egyező változók értékeiként.

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

A változó `tokenResponse` egy olyan objektum, amely tartalmazza a jogkivonatot és a társított metaadatokat, például a lejárati időt. A tokenek 1 órára érvényesek, és az új jogkivonat lekéréséhez futtassa újra ugyanezt a hívást. A következő kódrészlet egy mintául szolgáló válasz.

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

Az `accessToken` Auth token beolvasásához használja a tulajdonságot. Tekintse meg a [REST API dokumentációját](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) , amely bemutatja, hogyan használhatja a tokent API-hívások létrehozásához.

#### <a name="python"></a>Python

A következő lépésekkel hozhatja végre a hitelesítési tokent a Python használatával. A környezetben futtassa a parancsot `pip install adal` . Ezután használja a `tenantId` , a `clientId` és a `clientSecret` parancsot a fenti lépésekben a következő parancsfájlban a megfelelő változók értékeiként létrehozott egyszerű szolgáltatásnév alapján.

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

A változó `token_response` egy olyan szótár, amely tartalmazza a tokent és a kapcsolódó metaadatokat, például a lejárati időt. A tokenek 1 órára érvényesek, és az új jogkivonat lekéréséhez futtassa újra ugyanezt a hívást. A következő kódrészlet egy mintául szolgáló válasz.

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

`token_response["accessToken"]`Az hitelesítési jogkivonat beolvasására használatos. Tekintse meg a [REST API dokumentációját](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) , amely bemutatja, hogyan használhatja a tokent API-hívások létrehozásához.

## <a name="web-service-authentication"></a>Webes szolgáltatás hitelesítése

A Azure Machine Learning által létrehozott modell-telepítések két hitelesítési módszert biztosítanak:

* **kulcs-alapú**: a rendszer statikus kulcsot használ a webszolgáltatáshoz való hitelesítéshez.
* **jogkivonat-alapú**: a munkaterületről kell beszerezni egy ideiglenes jogkivonatot, amely a webszolgáltatáshoz való hitelesítéshez használatos. Ez a jogkivonat egy adott idő elteltével lejár, és frissíteni kell a webszolgáltatással folytatott munka folytatásához.

    > [!NOTE]
    > A jogkivonat-alapú hitelesítés csak az Azure Kubernetes szolgáltatásban való üzembe helyezéskor érhető el.

### <a name="key-based-web-service-authentication"></a>Kulcs alapú webszolgáltatás-hitelesítés

Az Azure Kubernetes Service (ak) szolgáltatásban üzembe helyezett webszolgáltatások alapértelmezés szerint *engedélyezve* vannak a kulcs alapú hitelesítéssel. Azure Container Instances (ACI) központilag telepített szolgáltatások esetében a kulcs-alapú hitelesítés alapértelmezés szerint *le van tiltva* , de engedélyezheti `auth_enabled=True` az ACI webszolgáltatások létrehozásakor. A következő kód egy példa arra, hogyan hozható létre egy ACI központi telepítési konfiguráció a kulcs alapú hitelesítéssel.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Ezt követően használhatja az egyéni ACI-konfigurációt az üzembe helyezés során a `Model` osztály használatával.

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

Az Auth kulcsok beolvasásához használja a következőt: `aci_service.get_keys()` . A kulcs újragenerálása érdekében használja a `regen_key()` függvényt, és adja át az **elsődleges** vagy a **másodlagos**műveletet.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Az üzembe helyezett modellekkel való hitelesítéssel kapcsolatos további információkért lásd: [ügyfél létrehozása webszolgáltatásként üzembe helyezett modellhez](how-to-consume-web-service.md).

### <a name="token-based-web-service-authentication"></a>Jogkivonat-alapú webszolgáltatások hitelesítése

Ha engedélyezi a jogkivonat-hitelesítést egy webszolgáltatáshoz, a felhasználóknak egy Azure Machine Learning JSON Web Token kell bemutatnia a webszolgáltatásnak az eléréséhez. A jogkivonat egy megadott időkereten belül lejár, és a hívások folytatásához frissíteni kell.

* **Alapértelmezés szerint** a jogkivonat-hitelesítés le van tiltva az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A jogkivonat-hitelesítés **nem támogatott** , ha Azure Container instances telepíti.
* A jogkivonat-hitelesítés **nem használható a kulcs alapú hitelesítéssel megegyező időpontban**.

A jogkivonat-hitelesítés vezérléséhez használja a (z) `token_auth_enabled` paramétert a központi telepítés létrehozásakor vagy frissítésekor:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Ha engedélyezve van a jogkivonat-hitelesítés, a metódus használatával kérhet `get_token` le egy JSON web token (JWT) és a jogkivonat lejárati idejét:

> [!TIP]
> Ha a jogkivonat lekéréséhez egyszerű szolgáltatásnevet használ, és azt szeretné, hogy a jogkivonat lekéréséhez minimálisan szükséges legyen a hozzáférés, rendelje hozzá a munkaterülethez tartozó **olvasó** szerepkörhöz.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat időpontját követően új jogkivonatot kell kérnie `refresh_by` . Ha a Python SDK-n kívülre kell frissítenie a jogkivonatokat, az egyik lehetőség az, hogy a REST API a szolgáltatás-egyszerű hitelesítéssel rendszeres időközönként a `service.get_token()` hívást a korábban tárgyalt módon használja.
>
> Javasoljuk, hogy az Azure Kubernetes Service-fürttel azonos régióban hozza létre Azure Machine Learning munkaterületét.
>
> A webszolgáltatások tokenekkel történő hitelesítéséhez a webszolgáltatás meghívja a Azure Machine Learning munkaterület létrehozásához használt régiót. Ha a munkaterület régiója nem érhető el, nem tud beolvasni egy jogkivonatot a webszolgáltatáshoz, még akkor sem, ha a fürt egy másik régióban található a munkaterületen. Ennek eredményeképpen az Azure AD-hitelesítés nem érhető el, amíg a munkaterület régiója újra elérhetővé nem válik.
>
> Továbbá minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál hosszabb ideig tart a token beolvasása.

## <a name="next-steps"></a>További lépések

* A [titkok használata a képzésben](how-to-use-secrets-in-runs.md).
* [Rendszerkép-besorolási modell betanítása és üzembe helyezése](tutorial-train-models-with-aml.md).
* [Webszolgáltatásként üzembe helyezett Azure Machine learning modell](how-to-consume-web-service.md)használata.

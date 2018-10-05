---
title: Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások
description: Ismerje meg, hogyan védheti meg az Azure Machine Learning szolgáltatással üzembe helyezett webszolgáltatások. Webes szolgáltatásokhoz való hozzáférés korlátozása, és a biztonságos szoftvercsatorna-réteg (SSL) használatával az ügyfelek által küldött adatok biztonságossá tétele és a kulcs alapú hitelesítés.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 885d867d0733ef923d327d8d6a36fc1588fd4961
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801012"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások

Ebben a cikkben megtudhatja, hogyan védheti meg az Azure Machine Learning szolgáltatással üzembe helyezett webszolgáltatások. Webes szolgáltatásokhoz való hozzáférés korlátozása, és a biztonságos szoftvercsatorna-réteg (SSL) használatával az ügyfelek által küldött adatok biztonságossá tétele és a kulcs alapú hitelesítés.

> [!Warning]
> Ha nem engedélyezi az SSL, az interneten bármely felhasználó tudják a webszolgáltatás-hívások.

SSL titkosítja az ügyfél és a web service között küldött adatokat. Ez a kiszolgáló identitását ellenőrizze az ügyfél által is használt. Hitelesítési szolgáltatások, amelyek egy SSL-tanúsítvány és kulcs megadott csak engedélyezve van.  Ha engedélyezi az SSL, hitelesítési kulcs megadása kötelező a web service elérésekor.

Az SSL engedélyezett webszolgáltatás üzembe, vagy a meglévő üzembe helyezett webszolgáltatáshoz SSL engedélyezése, a lépések ugyanazok:

1. Kérje le egy tartomány nevét.

2. SSL-tanúsítvány beszerzése.

3. Üzembe helyezése, vagy a web service frissíteni az SSL-beállítás engedélyezve van.

4. Frissítse a DNS, a web Service mutasson.

Kisebb különbségek vannak webszolgáltatások között biztosításakor a [telepítési céljainak](how-to-deploy-and-where.md). 

## <a name="get-a-domain-name"></a>A tartománynév beolvasása

Ha Ön nem tulajdonosa a tartománynevet, vásárolhat egyet egy __tartományregisztrálóhoz__. A folyamat szolgáltatásfrissítési regisztráló szervezetek, mint a költségek. A regisztráló is használható eszközöket biztosít a tartománynév kezeléséhez. Ezek az eszközök segítségével (például www.contoso.com) teljesen minősített tartománynév hozzárendelése a webszolgáltatást futtató IP-címet.

## <a name="get-an-ssl-certificate"></a>SSL-tanúsítvány beszerzése

Számos módon SSL-tanúsítvány beszerzése. A leggyakoribb eset az, hogy vásároljon egyet egy __hitelesítésszolgáltató__ (CA). Ha beszerezte a tanúsítványt, függetlenül kell a következő fájlokat:

* A __tanúsítvány__. A tanúsítvány kell tartalmaznia a teljes tanúsítványlánccal, és a PEM-kódolású kell lennie.
* A __kulcs__. A kulcsnak kell lennie a PEM-kódolású.

A tanúsítvány igénylésekor meg kell adni a teljesen minősített tartománynevét (FQDN) a címet, a webszolgáltatások használatát tervezi. Például www.contoso.com. A cím a tanúsítványba megjelölve, és az ügyfelek által használt cím képest, a web service identitásának érvényesítése során. A címek nem egyeznek, ha az ügyfelek egy hibaüzenetet fog kapni. 

> [!TIP]
> Ha a hitelesítésszolgáltató nem adja meg a tanúsítvány és kulcs-fájlok PEM-kódolású formájában, a segédprogram használható például [OpenSSL](https://www.openssl.org/) használatával módosíthatja.

> [!IMPORTANT]
> Önaláírt tanúsítványok használandó csakis fejlesztési célokra. Ezek nem használandó éles környezetben. Ha egy önaláírt tanúsítványt használ, tekintse meg a [önaláírt tanúsítványok webszolgáltatások felhasználása](#self-signed) adott című fejezetet.


## <a name="enable-ssl-and-deploy"></a>SSL engedélyezése és üzembe helyezése

Üzembe helyezése (vagy újratelepítés) SSL engedélyezve van a szolgáltatást, állítsa be a `ssl_enabled` paramétert `True`, bárhol is alkalmazható. Állítsa be a `ssl_certificate` paraméter értékét a __tanúsítvány__ fájl és a `ssl_key` értékéhez a __kulcs__ fájlt. 

+ **Az Azure Kubernetes Service (AKS) üzembe helyezése**
  
  Az AKS-fürt kiépítésekor adja meg az SSL-kapcsolódó paraméterek értékeit a kódrészletben látható módon:

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Az Azure Container Instances (ACI) üzembe helyezése**
 
  Üzembe helyezése az aci Szolgáltatásban, során adja meg az SSL-kapcsolódó paraméterek értékeit a kódrészletben látható módon:

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **A mező Programmable Gate Arrays (FPGA) üzembe helyezése**

  Válasza a `create_service` művelet a szolgáltatás IP-címét tartalmazza. Ha a DNS-név leképezése az IP-címet, a szolgáltatás az IP-címet használja. A válasz is tartalmaz egy __elsődleges kulcs__ és __másodlagos kulcs__ használt a szolgáltatás felhasználásához. Paraméterek értékének megadására az SSL-kapcsolódó kódrészletben látható módon:

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

## <a name="update-your-dns"></a>A DNS frissítése

Ezt követően frissítenie kell a DNS, a web Service mutasson.

+ **Az ACI és az FPGA**:  

  Az a tartománynév regisztrálójánál az által biztosított eszközök segítségével frissítse a DNS-rekordot a tartománynév. A rekord a szolgáltatás IP-címre kell mutatnia.  

  A regisztráló és az idő függvényében élettartam (TTL) van konfigurálva a tartománynév, akár néhány órát, mielőtt az ügyfelek fel tudják oldani a tartománynév több percet is igénybe vehet.

+ **Az aks-ben**: 

  Frissítse a DNS, a "Beállítások" lapon a "nyilvános IP-címe" az AKS-fürtöt a képen látható módon. A nyilvános IP-címet az erőforráscsoportban, amely tartalmazza az AKS-ügynök csomópontok és más hálózati erőforrások létrehozása az erőforrástípusok egyikét találja.

  ![Az Azure Machine Learning szolgáltatás: webszolgáltatások SSL használatával biztonságossá tétele](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="consume-authenticated-services"></a>Hitelesített szolgáltatásainak használata

### <a name="how-to-consume"></a>Hogyan lehet az 
+ **Az ACI és az AKS**: 

  ACI és az AKS webszolgáltatások megtudhatja, hogyan ezekben a cikkekben webszolgáltatások felhasználása:
  + [Az aci Szolgáltatásban üzembe helyezése](how-to-deploy-to-aci.md)

  + [Az aks üzembe helyezése](how-to-deploy-to-aks.md)

+ **A FPGA**:  

  Az alábbi példák bemutatják, hogyan lehet egy hitelesített FPGA-szolgáltatás a Python és a C# használata.
  Cserélje le `authkey` az elsődleges vagy másodlagos kulcsot, a szolgáltatás telepítésekor visszaadott együtt.

  Python-példát:
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  C#-példa:
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>Állítsa be az engedélyeztetési fejléc
Más gRPC ügyfelek hitelesítése történhet kérelem engedélyeztetési fejléc beállítása. Az általános megközelítés az, hogy hozzon létre egy `ChannelCredentials` objektum, amely egyesíti az `SslCredentials` a `CallCredentials`. Ez hozzáadódik a kérés hitelesítési fejlécéhez. További információk a végrehajtása támogatja a megadott fejlécek,: [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Az alábbi példák bemutatják, hogyan lehet a C# és nyissa meg a fejléc beállítása:

+ C# segítségével állítsa be a fejléc:
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Nyissa meg a fejléc beállításához használja:
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

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>Önaláírt tanúsítványok szolgáltatásainak használata

Az ügyfél hitelesítése egy önaláírt tanúsítvánnyal védett kiszolgálóra két módja van:

* Az ügyfélen, állítsa be a `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` környezeti változót, mutasson a tanúsítványfájlt az ügyfélrendszeren.

* Hozhat létre, amikor egy `SslCredentials` objektumazonosító, illesztheti be a tanúsítvány fájl tartalmát a konstruktor.

Mindkét módszerrel rendelkezik, a tanúsítványt használja, a legfelső szintű tanúsítvány gRPC.

> [!IMPORTANT]
> gRPC nem fogadja el a nem megbízható tanúsítványok. A nem megbízható tanúsítvánnyal meghiúsul egy `Unavailable` állapotkódot. A hiba részleteit tartalmazza `Connection Failed`.

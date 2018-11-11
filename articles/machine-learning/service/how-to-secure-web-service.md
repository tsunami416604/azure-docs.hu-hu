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
ms.openlocfilehash: ec7b956f080837b297bac56e6237ac0672601ce7
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344484"
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

> [!WARNING]
> Önaláírt tanúsítványok használandó csakis fejlesztési célokra. Ezek nem használandó éles környezetben. Önaláírt tanúsítványok problémákat okozhat az ügyfél az alkalmazásokat. További információkért lásd: az ügyfélalkalmazásban található használt hálózati könyvtárat dokumentációját.

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

  ![Az Azure Machine Learning szolgáltatás: webszolgáltatások SSL használatával biztonságossá tétele](./media/how-to-secure-web-service/aks-public-ip-address.png)Önkiszolgáló-

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [felhasználás a gépi Tanulási modellek webszolgáltatásként üzembe helyezett](how-to-consume-web-service.md).
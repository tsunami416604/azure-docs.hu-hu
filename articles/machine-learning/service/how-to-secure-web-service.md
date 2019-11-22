---
title: Biztonságos webszolgáltatások SSL használatával
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan engedélyezheti a HTTPS-t a Azure Machine Learning használatával központilag telepített webszolgáltatások biztosításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: f577ac723558ad3772b697922baa624e0047dd21
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276831"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Webszolgáltatások biztonságossá tétele az SSL használatával Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan védheti meg az Azure Machine Learning használatával üzembe helyezett webszolgáltatásokat.

A [https](https://en.wikipedia.org/wiki/HTTPS) használatával korlátozhatja a webszolgáltatásokhoz való hozzáférést, és gondoskodhat az ügyfelek által elküldött adatvédelemről. A HTTPS segíti a kommunikációt az ügyfél és a webszolgáltatás között a kettő közötti kommunikáció titkosításával. A titkosítás [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)protokollt használ. A TLS-t néha *SSL* (SSL) néven is emlegetik, amely a TLS elődje volt.

> [!TIP]
> Az Azure Machine Learning SDK az "SSL" kifejezést használja a biztonságos kommunikációhoz kapcsolódó tulajdonságokhoz. Ez nem jelenti azt, hogy a webszolgáltatás nem használ *TLS*-t. Az SSL csak egy gyakrabban felismert kifejezés.

A TLS és az SSL egyaránt *digitális tanúsítványokra*támaszkodik, amelyek segítenek a titkosítás és az identitások ellenőrzésében. A digitális tanúsítványok működésével kapcsolatos további információkért tekintse meg a wikipedia témakör [nyilvános kulcsokra épülő infrastruktúráját](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Ha nem használja a HTTPS-t a webszolgáltatáshoz, a szolgáltatásba érkező és onnan érkező adatok az interneten mások számára is láthatóvá válnak.
>
> A HTTPS azt is lehetővé teszi az ügyfél számára, hogy ellenőrizze annak a kiszolgálónak a hitelességét, amelyhez csatlakozik. Ez a szolgáltatás megvédi [az](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) ügyfeleket a támadásokkal szemben.

Ez a webszolgáltatás biztonságossá tételének általános folyamata:

1. Kérje le egy tartomány nevét.

2. Digitális tanúsítvány beszerzése.

3. A webszolgáltatás üzembe helyezése vagy frissítése engedélyezve az SSL használatával.

4. Frissítse a DNS, a web Service mutasson.

> [!IMPORTANT]
> Ha az Azure Kubernetes szolgáltatásba (ak) végzi az üzembe helyezést, megvásárolhatja saját tanúsítványát, vagy a Microsoft által biztosított tanúsítványt is használhatja. Ha tanúsítványt használ a Microsofttól, nem kell beszereznie a tartománynevet vagy az SSL-tanúsítványt. További információ: az [SSL és a telepítés engedélyezése](#enable) című rész, jelen cikk.

Kis eltérések vannak, amikor a biztonsági s-t az [üzembe helyezési célok](how-to-deploy-and-where.md)között.

## <a name="get-a-domain-name"></a>A tartománynév beolvasása

Ha még nem rendelkezik tartománynévvel, vásároljon egyet a tartománynév- *regisztrálótól*. A folyamat és az ár eltér a regisztrátorok között. A regisztrátor a tartománynevet kezelő eszközöket biztosít. Ezeknek az eszközöknek a segítségével teljes tartománynevet (például www\.contoso.com) képezhető le a webszolgáltatást futtató IP-címhez.

## <a name="get-an-ssl-certificate"></a>SSL-tanúsítvány beszerzése

Az SSL-tanúsítványok (digitális tanúsítványok) többféleképpen is beszerezhetők. A leggyakoribb a *hitelesítésszolgáltató (CA* ) egyikének megvásárlása. A tanúsítvány lekérésének helyétől függetlenül a következő fájlokra lesz szüksége:

* A **tanúsítvány**. A tanúsítványnak tartalmaznia kell a teljes tanúsítványláncot, és a "PEM-kódolt" értéknek kell lennie.
* A **kulcs**. A kulcsnak PEM-kódolású is kell lennie.

Ha tanúsítványt kér, meg kell adnia a webszolgáltatáshoz használni kívánt címek teljes tartománynevét (például a www\.contoso.com). A rendszer a tanúsítványba pecsételő és az ügyfelek által használt címek összehasonlításával ellenőrzi a webszolgáltatás identitását. Ha ezek a címek nem egyeznek, az ügyfél hibaüzenetet kap.

> [!TIP]
> Ha a hitelesítésszolgáltató nem tudja megadni a tanúsítványt és a kulcsot PEM-kódolású fájlként, használhat egy olyan segédprogramot, mint például az [OpenSSL](https://www.openssl.org/) a formátum megváltoztatásához.

> [!WARNING]
> *Önaláírt* tanúsítványokat csak fejlesztéshez használhat. Ne használja őket éles környezetekben. Önaláírt tanúsítványok problémákat okozhat az ügyfél az alkalmazásokat. További információkért tekintse meg az ügyfélalkalmazás által használt hálózati kódtárak dokumentációját.

## <a id="enable"></a>Az SSL engedélyezése és üzembe helyezése

A szolgáltatás üzembe helyezéséhez (vagy újbóli üzembe helyezéséhez) engedélyezve van az SSL, állítsa a *ssl_enabled* paramétert "true" értékre, ahol alkalmazható. Állítsa a *ssl_certificate* paramétert a *tanúsítványfájl* értékére. Állítsa a *ssl_key* értékét a *kulcsfájl* értékére.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Üzembe helyezés az AK-ban és a Field-programozható Gate array (FPGA)

  > [!NOTE]
  > Az ebben a szakaszban található információk akkor is érvényesek, ha biztonságos webszolgáltatást telepít a tervezőhöz. Ha nem ismeri a Python SDK használatát, tekintse [meg a mi a Pythonhoz készült Azure Machine learning SDK?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)című témakört.

Ha AK-ra végez üzembe helyezést, létrehozhat egy új AK-fürtöt, vagy csatolhat egy meglévőt. A fürtök létrehozásával vagy csatolásával kapcsolatos további információkért lásd: [modell üzembe helyezése Azure Kubernetes Service-fürtön](how-to-deploy-azure-kubernetes-service.md).
  
-  Ha új fürtöt hoz létre, használja a **[AksCompute. provisionining_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none-)** .
- Ha meglévő fürtöt csatlakoztat, használja a **[AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none--load-balancer-type-none-)** programot. Mindkettő olyan konfigurációs objektumot ad vissza, amely **enable_ssl** metódussal rendelkezik.

A **enable_ssl** metódus a Microsoft által biztosított, vagy a megvásárolt tanúsítvánnyal rendelkező tanúsítványt is használhat.

  * Ha tanúsítványt használ a Microsofttól, akkor a *leaf_domain_label* paramétert kell használnia. Ez a paraméter a szolgáltatás DNS-nevét hozza létre. A "contoso" érték például a "contoso\<hat-Random-characters > tartománynevet hozza létre.\<azureregion >. cloudapp. Azure. com ", ahol a \<azureregion > a szolgáltatást tartalmazó régió. Igény szerint a *overwrite_existing_domain* paraméterrel írhatja felül a meglévő *leaf_domain_label*.

    A szolgáltatás üzembe helyezéséhez (vagy újbóli üzembe helyezéséhez) engedélyezve van az SSL, állítsa a *ssl_enabled* paramétert "true" értékre, ahol alkalmazható. Állítsa a *ssl_certificate* paramétert a *tanúsítványfájl* értékére. Állítsa a *ssl_key* értékét a *kulcsfájl* értékére.

    > [!IMPORTANT]
    > Ha tanúsítványt használ a Microsofttól, nem kell megvásárolnia a saját tanúsítványát vagy tartománynevét.

    Az alábbi példa bemutatja, hogyan hozhat létre olyan konfigurációt, amely lehetővé teszi egy SSL-tanúsítvány létrehozását a Microsofttól:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * A *megvásárolt tanúsítvány*használatakor a *ssl_cert_pem_file*, *ssl_key_pem_file*és *ssl_cname* paramétereket kell használnia. Az alábbi példa bemutatja, hogyan használhatók a *. PEM* -fájlok olyan konfigurációk létrehozásához, amelyek egy MEGvásárolt SSL-tanúsítványt használnak:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

További információ a *enable_sslről*: [AksProvisioningConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) és [AksAttachConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Üzembe helyezés Azure Container Instances

Ha Azure Container Instancesre telepít, az SSL-hez kapcsolódó paraméterek értékeit adja meg, az alábbi kódrészlet pedig az alábbiakat mutatja be:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

További információ: [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>A DNS frissítése

Ezt követően frissítenie kell a DNS, a web Service mutasson.

+ **Container Instances esetén:**

  A tartománynév-regisztráló eszközeit használva frissítse a tartománynév DNS-rekordját. A rekordnak a szolgáltatás IP-címére kell mutatnia.

  Perc vagy óra késése is lehet, mielőtt az ügyfelek fel tudják oldani a tartománynevet a regisztrátortól és a tartománynévhez konfigurált "élettartamtól" (TTL) függően.

+ **AK esetében:**

  > [!WARNING]
  > Ha az *leaf_domain_label* használatával hozza létre a szolgáltatást a Microsofttól származó tanúsítvánnyal, ne frissítse manuálisan a fürt DNS-értékét. Az értéket automatikusan kell beállítani.

  Frissítse az AK-fürt nyilvános IP-címének DNS-címét a **konfiguráció** lapon a bal oldali ablaktábla **Beállítások** területén. (Lásd az alábbi ábrát.) A nyilvános IP-cím olyan erőforrástípus, amely az AK-ügynök csomópontjait és egyéb hálózati erőforrásokat tartalmazó erőforráscsoport alatt jön létre.

  [![Azure Machine Learning: webszolgáltatások biztonságossá tétele SSL használatával](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-ssl-certificate"></a>Az SSL-tanúsítvány frissítése

Az SSL-tanúsítványok lejárnak, és meg kell újítani. Ez általában évente történik. A következő részben található információk segítségével frissítheti és újíthatja meg az Azure Kubernetes Service-ben üzembe helyezett modellek tanúsítványát:

### <a name="update-a-microsoft-generated-certificate"></a>Microsoft által generált tanúsítvány frissítése

Ha a tanúsítványt eredetileg a Microsoft hozta létre (ha a *leaf_domain_label* a szolgáltatás létrehozásához használja), az alábbi példák egyikével frissítheti a tanúsítványt:

**Az SDK használata**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**A parancssori felület (CLI) használata**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

További információkért tekintse meg a következő dokumentációs dokumentumokat:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Egyéni tanúsítvány frissítése

Ha a tanúsítványt eredetileg egy hitelesítésszolgáltató hozta létre, kövesse az alábbi lépéseket:

1. A tanúsítvány megújításához használja a hitelesítésszolgáltató által biztosított dokumentációt. Ez a folyamat új tanúsítványfájl-fájlokat hoz létre.

1. Az SDK vagy a CLI használatával frissítse a szolgáltatást az új tanúsítvánnyal:

    **Az SDK használata**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **A parancssori felület (CLI) használata**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

További információkért tekintse meg a következő dokumentációs dokumentumokat:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-ssl"></a>SSL letiltása

Ha le szeretné tiltani az SSL-t az Azure Kubernetes szolgáltatásban üzembe helyezett modellhez, hozzon létre egy `SslConfiguration`t a `status="Disabled"`, majd hajtson végre egy frissítést:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable SSL
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Következő lépések
A webinárium témái:
+ [Webszolgáltatásként üzembe helyezett gépi tanulási modell felhasználása](how-to-consume-web-service.md)
+ [Kísérletek és következtetések biztonságos futtatása Azure-beli virtuális hálózaton belül](how-to-enable-virtual-network.md)

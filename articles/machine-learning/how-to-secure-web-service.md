---
title: Biztonságos webszolgáltatások a TLS használatával
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan engedélyezheti a HTTPS-t az Azure Machine Learningen keresztül üzembe helyezett webszolgáltatás biztonságossá tétele érdekében. Az Azure Machine Learning a TLS 1.2-es verzióját használja a webszolgáltatásként üzembe helyezett modellek védelméhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287339"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Webszolgáltatás biztonságossá tétele az Azure Machine Learning en keresztül a TLS használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan biztonságos sá that's deployed through Azure Machine Learning.

A [HTTPS](https://en.wikipedia.org/wiki/HTTPS) használatával korlátozhatja a webszolgáltatásokhoz való hozzáférést, és biztonságossá teszi az ügyfelek által beküldött adatokat. A HTTPS a kettő közötti kommunikáció titkosításával segíti az ügyfél és a webszolgáltatás közötti kommunikáció biztonságát. A titkosítás [a Transport Layer Security (TLS) (Transport Layer Security) (Transport Layer Security) (Transport Layer Security) (Transport Layer Security) (TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) A TLS-t néha még mindig Secure Sockets Layer (SSL) néven is *emlegetik,* amely a TLS elődje volt.

> [!TIP]
> Az Azure Machine Learning SDK az "SSL" kifejezést használja a biztonságos kommunikációhoz kapcsolódó tulajdonságokhoz. Ez nem jelenti azt, hogy a webszolgáltatás nem használja a *TLS-t.* SSL csak egy általánosan elismert kifejezés.
>
> Pontosabban az Azure Machine Learningen keresztül telepített webszolgáltatások csak a TLS 1.2-es verzióját támogatják.

A TLS és az SSL egyaránt *digitális tanúsítványokra*támaszkodik, amelyek segítenek a titkosításban és az identitás-ellenőrzésben. A digitális tanúsítványok működéséről a Wikipédia [Nyilvános kulcs infrastruktúrája](https://en.wikipedia.org/wiki/Public_key_infrastructure)című témakörben olvashat bővebben.

> [!WARNING]
> Ha nem használja a HTTPS-t a webszolgáltatáshoz, a szolgáltatásba és a szolgáltatásból küldött adatok az interneten mások számára is láthatóak lehetnek.
>
> A HTTPS azt is lehetővé teszi, hogy az ügyfél ellenőrizze annak a kiszolgálónak a hitelességét, amelyhez csatlakozik. Ez a funkció megvédi az ügyfeleket [a köztes](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) támadásoktól.

Ez a webszolgáltatás biztonságossá tétele általános folyamat:

1. Szerezzen be egy tartománynevet.

2. Szerezzen be egy digitális tanúsítványt.

3. Telepítse vagy frissítse a webszolgáltatást úgy, hogy a TLS engedélyezve van.

4. Frissítse a DNS-t, hogy a webszolgáltatásra mutasson.

> [!IMPORTANT]
> Ha az Azure Kubernetes-szolgáltatásra (AKS) telepít, megvásárolhatja saját tanúsítványát, vagy használhatja a Microsoft által biztosított tanúsítványt. Ha a Microsoft tanúsítványát használja, nem kell tartománynevet vagy TLS/SSL tanúsítványt beszereznie. További információt a [cikk TLS engedélyezése és üzembe helyezése](#enable) című szakaszában talál.

Vannak kisebb különbségek, ha biztonságos s között [telepítési célokat.](how-to-deploy-and-where.md)

## <a name="get-a-domain-name"></a>Tartománynév beszereznie

Ha még nem rendelkezik tartománynévvel, vásároljon egyet egy *tartományregisztrálótól.* A folyamat és az ár eltér a regisztrátorok között. A regisztrátor eszközöket biztosít a tartománynév kezeléséhez. Ezekkel az eszközökkel egy teljesen minősített tartománynevet (például\.www contoso.com) rendel a webszolgáltatást tartalmazó IP-címre.

## <a name="get-a-tlsssl-certificate"></a>TLS/SSL tanúsítvány beszerezni

A TLS/SSL tanúsítvány (digitális tanúsítvány) számos módon szerezhető be. A leggyakoribb az, hogy megvásárol egy *hitelesítésszolgáltatótól* (CA). Függetlenül attól, hogy hol kapja meg a tanúsítványt, a következő fájlokra van szüksége:

* A **tanúsítvány**. A tanúsítványnak tartalmaznia kell a teljes tanúsítványláncot, és "PEM-kódoltnak" kell lennie.
* A **kulcs**. A kulcsot PEM-kódolásúnak is kell lennie.

Amikor tanúsítványt kér, meg kell adnia a webszolgáltatáshoz használni kívánt cím teljes tartománynát (például www\.contoso.com). A tanúsítványba bepecsételt címet és az ügyfelek által használt címet a rendszer összehasonlítja a webszolgáltatás identitásának ellenőrzéséhez. Ha ezek a címek nem egyeznek, az ügyfél hibaüzenetet kap.

> [!TIP]
> Ha a hitelesítésszolgáltató nem tudja PEM-kódolású fájlként biztosítani a tanúsítványt és a kulcsot, a formátum módosításához használhat egy segédprogramot, például az [OpenSSL-t.](https://www.openssl.org/)

> [!WARNING]
> Csak *önaláírt* tanúsítványokat használjon fejlesztésre. Ne használja őket éles környezetben. Az önaláírt tanúsítványok problémákat okozhatnak az ügyfélalkalmazásokban. További információt az ügyfélalkalmazás által használt hálózati tárak dokumentációjában talál.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>TLS engedélyezése és üzembe helyezés

A szolgáltatás üzembe helyezéséhez (vagy újratelepítéséhez) a TLS engedélyezve van, állítsa a *ssl_enabled* paramétert "True" értékre, bárhol is legyen alkalmazható. Állítsa be a *ssl_certificate* paramétert a *tanúsítványfájl* értékére. Állítsa be a *ssl_key* a *kulcsfájl* értékére.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Üzembe helyezés AKS-en és mezőprogramozható kaputömbön (FPGA)

  > [!NOTE]
  > Az ebben a szakaszban található információk akkor is érvényesek, ha biztonságos webszolgáltatást telepít a tervező számára. Ha nem ismeri a Python SDK használatát, olvassa el [a Mi az Azure Machine Learning SDK pythonhoz.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

Az AKS-re való üzembe helyezéskor létrehozhat egy új AKS-fürtöt, vagy csatolhat egy meglévőt. A fürt létrehozásáról és csatolásáról a [Modell telepítése egy Azure Kubernetes-szolgáltatásfürthöz című témakörben](how-to-deploy-azure-kubernetes-service.md)talál további információt.
  
-  Ha új fürtöt hoz létre, az **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- Ha meglévő fürtöt csatol, az **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Mindkettő olyan konfigurációs objektumot ad vissza, amely **enable_ssl** metódussal rendelkezik.

A **enable_ssl** módszer a Microsoft által biztosított tanúsítványt vagy a megvásárolt tanúsítványt használhatja.

  * Ha a Microsoft tanúsítványát használja, a *leaf_domain_label* paramétert kell használnia. Ez a paraméter hozza létre a szolgáltatás DNS-nevét. A "contoso" érték például "contoso\<hat-véletlen karakterek> tartománynevet hoz létre. \<azureregion>.cloudapp.azure.com", \<ahol az azureregion> a szolgáltatást tartalmazó régió. Szükség esetén a *overwrite_existing_domain* paraméterrel felülírhatja a meglévő *leaf_domain_label.*

    A szolgáltatás üzembe helyezéséhez (vagy újratelepítéséhez) a TLS engedélyezve van, állítsa a *ssl_enabled* paramétert "True" értékre, bárhol is legyen alkalmazható. Állítsa be a *ssl_certificate* paramétert a *tanúsítványfájl* értékére. Állítsa be a *ssl_key* a *kulcsfájl* értékére.

    > [!IMPORTANT]
    > Ha a Microsoft tól származó tanúsítványt használ, nem kell megvásárolnia a saját tanúsítványát vagy tartománynevét.

    A következő példa bemutatja, hogyan hozhat létre olyan konfigurációt, amely engedélyezi a Microsoft TLS/SSL tanúsítványát:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * A *megvásárolt tanúsítvány*használatakor a *ssl_cert_pem_file,* *a ssl_key_pem_file*és *ssl_cname* paramétereket használja. A következő példa bemutatja, hogyan lehet *a .pem* fájlok használatával olyan konfigurációt létrehozni, amely a megvásárolt TLS/SSL tanúsítványt használja:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

A *enable_ssl*kapcsolatos további tudnivalókért olvassa el az [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) és [az AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)című témakört.

### <a name="deploy-on-azure-container-instances"></a>Üzembe helyezés az Azure Container-példányokon

Amikor üzembe helyezi az Azure Container Instances, adja meg a TLS-sel kapcsolatos paraméterek értékeit, ahogy azt a következő kódrészlet mutatja:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

További információ: [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>A DNS frissítése

Ezután frissítenie kell a DNS-t, hogy a webszolgáltatásra mutasson.

+ **Tárolópéldányok esetén:**

  A tartománynév-regisztráló eszközeivel frissítheti a tartománynév DNS-rekordját. A rekordnak a szolgáltatás IP-címére kell mutatnia.

  A tartománynév feloldása a regisztrálótól és a tartománynévhez konfigurált "time to live" (TTL) beállítástól függően percek vagy órák késleltetése lehet.

+ **AKS esetében:**

  > [!WARNING]
  > Ha *leaf_domain_label* a szolgáltatás microsoftos tanúsítvány használatával történő létrehozásához használta, ne frissítse manuálisan a fürt DNS-értékét. Az értéket automatikusan be kell állítani.

  Frissítse az AKS-fürt nyilvános IP-címének DNS-ét a **bal** oldali ablaktábla **Beállítások lapján.** (Lásd az alábbi képet.) A nyilvános IP-cím egy olyan erőforrástípus, amely az AKS-ügynökcsomópontokat és más hálózati erőforrásokat tartalmazó erőforráscsoport alatt jön létre.

  [![Azure Machine Learning: Webes szolgáltatások védelme a TLS-sel](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>A TLS/SSL tanúsítvány frissítése

A TLS/SSL tanúsítványok lejárnak, és meg kell újítani őket. Jellemzően ez történik minden évben. Az alábbi szakaszokban található információk segítségével frissítse és újítsa meg az Azure Kubernetes-szolgáltatásba telepített modellek tanúsítványát:

### <a name="update-a-microsoft-generated-certificate"></a>Microsoft által létrehozott tanúsítvány frissítése

Ha a tanúsítványt eredetileg a Microsoft hozta létre (amikor a *leaf_domain_label* a szolgáltatás létrehozásához használta, az alábbi példák egyikével frissítse a tanúsítványt:

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

További információt az alábbi hivatkozási dokumentumokban talál:

* [Ssl-konfiguráció](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Egyéni tanúsítvány frissítése

Ha a tanúsítványt eredetileg hitelesítésszolgáltató hozta létre, kövesse az alábbi lépéseket:

1. A tanúsítvány megújításához használja a hitelesítésszolgáltató által biztosított dokumentációt. Ez a folyamat új tanúsítványfájlokat hoz létre.

1. Az SDK vagy a CLI segítségével frissítse a szolgáltatást az új tanúsítvánnyal:

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

További információt az alábbi hivatkozási dokumentumokban talál:

* [Ssl-konfiguráció](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>A TLS letiltása

Az Azure Kubernetes-szolgáltatásra telepített modell TLS `SslConfiguration` letiltásához hozzon létre egy a használatával, `status="Disabled"`majd hajtson végre egy frissítést:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>További lépések
Az alábbiak végrehajtásának módját ismerheti meg:
+ [Webszolgáltatásként üzembe helyezett gépi tanulási modell felhasználása](how-to-consume-web-service.md)
+ [Biztonságosan futtatjon kísérleteket és következtetéseket az Azure virtuális hálózatán belül](how-to-enable-virtual-network.md)

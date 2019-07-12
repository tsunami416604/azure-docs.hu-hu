---
title: Biztonságos webszolgáltatások SSL használatával
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan teheti biztonságossá egy webszolgáltatás, amelyet az HTTPS engedélyezése az Azure Machine Learning szolgáltatás segítségével telepítve. HTTPS-ügyfelek által az adatok transport layer security (TLS), helyettesíti a biztonságos szoftvercsatorna-réteg (SSL) használatával védi. Az ügyfelek HTTPS is használhatja a webes szolgáltatás identitásának ellenőrzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: c176458cfc404a9d55d7fb71a36ea63110b3a6d6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657951"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>SSL használatával biztonságossá tétele révén az Azure Machine Learning webszolgáltatás

Ez a cikk bemutatja, hogyan védheti meg az Azure Machine Learning szolgáltatásban üzembe helyezett webszolgáltatás.

Használhat [HTTPS](https://en.wikipedia.org/wiki/HTTPS) webes szolgáltatásokhoz való hozzáférés korlátozása, és az adatbiztonságot, amely ügyfeleknek nyújt. HTTPS ügyfél és a egy webes szolgáltatás közötti biztonságos kommunikáció lehetővé a kettő közötti kommunikáció titkosításához. Titkosítás [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). A TLS néha továbbra is nevezik *Secure Sockets Layer* (SSL), amely a TLS elődje volt.

> [!TIP]
> Az Azure Machine Learning SDK "SSL" kifejezés, amely biztonságos kommunikációt kapcsolódó tulajdonságok használ. Ez nem jelenti azt, hogy a webszolgáltatás nem használja a *TLS*. Az SSL a leggyakrabban felismert kifejezés.

A TLS és az SSL is támaszkodik *digitális tanúsítványok*, amely titkosítás és identitás-ellenőrzéssel segítségével. A digitális tanúsítványok működésével kapcsolatban tekintse meg a Wikipédia témakört [nyilvános kulcsokra épülő infrastruktúra](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> A webszolgáltatás nem https PROTOKOLLT használja, ha, és a szolgáltatásból küldött adatok is láthatják, mások számára az interneten.
>
> HTTPS is lehetővé teszi az ügyfél, amely az épp csatlakozik a kiszolgáló hitelességének ellenőrzéséhez. Ez a funkció megvédi az ügyfelek ellen [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) támadásokkal szemben.

Ez az általános folyamata a web service védelmét:

1. Kérje le egy tartomány nevét.

2. Digitális igazolást kap.

3. Üzembe helyezése vagy frissítse a web service SSL engedélyezve van.

4. Frissítse a DNS, a web Service mutasson.

> [!IMPORTANT]
> Ha telepíti az Azure Kubernetes Service (AKS), a saját tanúsítvány megvásárlása, vagy a Microsoft által biztosított tanúsítványt használjon. Ha a Microsoft olyan tanúsítványt használ, nem kell a tartomány- vagy SSL-tanúsítvány beszerzése. További információkért lásd: a [SSL engedélyezése és üzembe helyezése](#enable) című szakaszát.

Kisebb különbségek vannak webszolgáltatások között védelmének kialakításakor [telepítési céljainak](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>A tartománynév beolvasása

Ha Ön a tartománynév tulajdonosa már nem, vásároljon egyet egy *tartományregisztrálóhoz*. A folyamat és a díj különböző regisztráló szervezetek között. A regisztráló biztosít eszközöket kezelheti a tartomány nevét. Ezek az eszközök leképezni egy teljesen minősített tartománynevét (FQDN) használja (például a www\.contoso.com) IP-címet, amelyen a webszolgáltatást.

## <a name="get-an-ssl-certificate"></a>SSL-tanúsítvány beszerzése

Számos módon beolvasása egy SSL-tanúsítványt (digitális tanúsítványt). A leggyakoribb eset az, hogy vásároljon egyet egy *hitelesítésszolgáltató* (CA). Függetlenül a tanúsítványt a következő fájlok szükségesek:

* A **tanúsítvány**. A tanúsítványnak tartalmaznia kell a teljes tanúsítványlánccal, és azt kell lennie a "PEM-kódolású."
* A **kulcs**. A kulcs is kell lennie a PEM-kódolású.

Amikor tanúsítványt igényel, meg kell adnia a címet, amely azt tervezi, hogy a webszolgáltatás használata teljes Tartománynevét (például: www\.contoso.com). E-mail cím a tanúsítványba van megjelölve, és a cím, az ügyfelek által használt rendszer összeveti a web service identitásának ellenőrzéséhez. Ha ezek a címek nem egyeznek, hibaüzenetet kapja.

> [!TIP]
> Ha a hitelesítésszolgáltató nem adja meg a tanúsítvány és kulcs-fájlok PEM-kódolású formájában, a segédprogram használható például [OpenSSL](https://www.openssl.org/) használatával módosíthatja.

> [!WARNING]
> Használat *önaláírt* tanúsítványok csakis fejlesztési célokra. Ne használja őket az éles környezetben. Önaláírt tanúsítványok problémákat okozhat az ügyfél az alkalmazásokat. További információkért lásd: a hálózati függvénytárak, amely az ügyfélalkalmazásban használ a dokumentációban.

## <a id="enable"></a> SSL engedélyezése és üzembe helyezése

Üzembe helyezése (vagy ismételt telepítése) SSL engedélyezve van a szolgáltatást, állítsa be a *ssl_enabled* paraméternek "True", bárhol is alkalmazható. Állítsa be a *ssl_certificate* paraméter értékét a *tanúsítvány* fájlt. Állítsa be a *ssl_key* értékéhez a *kulcs* fájlt.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Az AKS és a mező-programmable gate array (FPGA) üzembe helyezése

  > [!NOTE]
  > Ebben a szakaszban található információk is vonatkozik, ha telepít egy biztonságos webes szolgáltatás esetében a vizuális felhasználói felületet. Ha nem ismeri a Python SDK használatával, lásd: [Mi az az Azure Machine Learning SDK Pythonhoz készült?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Amikor helyez üzembe az aks-ben, hozzon létre egy új AKS-fürtöt, vagy csatlakoztasson egy már meglévőt.
  
-  Ha egy új fürtöt hoz létre, használhatja a  **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Ha csatlakoztat egy meglévő fürthöz, használhatja a  **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Mindkét adja vissza egy konfigurációs objektumot, amely rendelkezik egy **enable_ssl** metódust.

A **enable_ssl** módszer használható egy tanúsítványt a Microsoft által biztosított vagy megvásárolt tanúsítványt.

  * Ha a Microsoft olyan tanúsítványt használ, akkor kell használnia a *leaf_domain_label* paraméter. Ez a paraméter állít elő, a szolgáltatás DNS-nevét. Például "myservice" értéket hoz létre a tartomány neve "myservice\<hat véletlenszerű karakter >.\< azureregion >. cloudapp.Azure.com formát követi ", ahol \<azureregion > az a régió, amely tartalmazza a szolgáltatás. Másik lehetőségként használhatja a *overwrite_existing_domain* paraméterrel írhatja felül a meglévő *leaf_domain_label*.

    Üzembe helyezése (vagy ismételt telepítése) SSL engedélyezve van a szolgáltatást, állítsa be a *ssl_enabled* paraméternek "True", bárhol is alkalmazható. Állítsa be a *ssl_certificate* paraméter értékét a *tanúsítvány* fájlt. Állítsa be a *ssl_key* értékéhez a *kulcs* fájlt.

    > [!IMPORTANT]
    > A Microsoft a tanúsítvány használatakor nem kell vásárolni a saját tanúsítvánnyal vagy tartománynév számára.

    A következő példa bemutatja, hogyan hozhat létre olyan konfigurációt, amely lehetővé teszi, hogy a Microsoft SSL-tanúsítvány:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * Használata esetén *vásárolt tanúsítványt*, használja a *ssl_cert_pem_file*, *ssl_key_pem_file*, és *ssl_cname* a paraméterek. A következő példa bemutatja, hogyan használható *.pem* fájlok által használt SSL-tanúsítvány vásárolt konfiguráció létrehozásához:

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

További információ *enable_ssl*, lásd: [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) és [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Az Azure Container Instances üzembe helyezése

Azure Container Instancesben való telepítésekor, paraméterek értékének megadására SSL kapcsolatos, az alábbi kód kódrészletben látható módon:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

További információkért lásd: [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>A DNS frissítése

Ezt követően frissítenie kell a DNS, a web Service mutasson.

+ **Container Instances:**

  A tartománynév regisztrálójánál az eszközeit használja a tartománynévhez tartozó DNS-rekordot frissíteni. A rekord a szolgáltatás IP-címre kell mutatnia.

  A percek vagy órák ügyfelek fel tudják oldani a tartománynevet, attól függően, a regisztráló és a "time to live" (TTL) van konfigurálva, a tartománynév előtt késés lehet.

+ **Az aks-ben:**

  > [!WARNING]
  > Ha használt *leaf_domain_label* szolgáltatás létrehozásához a Microsoft-tanúsítvány használatával, manuálisan nem frissíti a DNS-értéket, a fürt számára. Az érték automatikusan kell beállítani.

  A DNS frissítése az **konfigurációs** a nyilvános IP-címét az AKS-fürt lapján. (Lásd az alábbi képen). A nyilvános IP-cím az ügynök az AKS-csomópontok és más hálózati erőforrásokat tartalmazó erőforráscsoport alatt létrehozott erőforrástípus.

  ![Azure Machine Learning service: Webszolgáltatások SSL használatával biztonságossá tétele](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>További lépések
Az alábbiak végrehajtásának módját ismerheti meg:
+ [Machine learning-webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md)
+ [Biztonságos futtatására kísérletek tanuláshoz és következtetésekhez egy Azure virtuális hálózaton belül](how-to-enable-virtual-network.md)

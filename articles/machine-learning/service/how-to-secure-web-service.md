---
title: Webszolgáltatások biztonságossá tétele SSL használatával
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan teheti biztonságossá a HTTPS engedélyezésével az Azure Machine Learning szolgáltatással telepített webes szolgáltatás. HTTPS védi a transport layer security (TLS), helyettesíti a biztonságos szoftvercsatorna-réteg (SSL) használó ügyfelek által küldött adatokat. Azt is szolgál az ügyfelek által a web service identitásának ellenőrzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: ece32754ae51bde5db52d20ab44f0d748bf46533
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943944"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>SSL használatával biztonságossá tétele az Azure Machine Learning szolgáltatás a webszolgáltatások

Ebben a cikkben megtudhatja, hogyan védheti meg az Azure Machine Learning szolgáltatással üzembe helyezett webszolgáltatások. Webes szolgáltatásokhoz való hozzáférés korlátozása, és segítségével az ügyfelek által küldött adatok biztonságossá [Hypertext Transfer Protocol biztonságos (HTTPS)](https://en.wikipedia.org/wiki/HTTPS).

Ügyfél és a webszolgáltatás közötti kommunikáció biztonságossá tételéhez a kettő közötti kommunikáció titkosítása HTTPS segítségével. Titkosítás használatával kezeli [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Néha TLS továbbra is hivatkozik, Secure Sockets Layer (SSL), amely a TLS elődje volt.

> [!TIP]
> Az Azure Machine Learning SDK "SSL" Tulajdonságok biztonságos kommunikáció kapcsolódó kifejezést használja. Ez nem jelenti azt, hogy a webszolgáltatás nem használja a TLS, csak az adott SSL számos olvasók több felismerhető kifejezés.

A TLS és az SSL is támaszkodik __digitális tanúsítványok__, titkosítás és identitás-ellenőrzés végrehajtásához használt. Digitális tanúsítványok működésével kapcsolatban tekintse meg a Wikipédia-bejegyzés [nyilvános kulcsokra épülő infrastruktúrájú (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Ha nem engedélyezi, és a webszolgáltatást a HTTPS PROTOKOLLT használja, előfordulhat, hogy, és a szolgáltatásból küldött adatok jelennek meg mások be az interneten.
>
> HTTPS is lehetővé teszi az ügyfél, amely az épp csatlakozik a kiszolgáló hitelességének ellenőrzéséhez. Ez védelmet biztosít az ügyfelek ellen [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) támadásokkal szemben.

Új webszolgáltatásként, vagy egy meglévő biztonságossá általános folyamata a következőképpen történik:

1. Kérje le egy tartomány nevét.

2. Digitális igazolást kap.

3. Üzembe helyezése, vagy a web service frissíteni az SSL-beállítás engedélyezve van.

4. Frissítse a DNS, a web Service mutasson.

> [!IMPORTANT]
> Helyez üzembe az Azure Kubernetes Service (AKS), adja meg a tanúsítvány, vagy a Microsoft által biztosított tanúsítványt használjon. Ha a Microsoft által biztosított tanúsítványt használ, nem kell a tartomány- vagy SSL-tanúsítvány beszerzése. További információkért lásd: a [SSL engedélyezése és üzembe helyezése](#enable) szakaszban.

Kisebb különbségek vannak webszolgáltatások között biztosításakor a [telepítési céljainak](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>A tartománynév beolvasása

Ha Ön nem tulajdonosa a tartománynevet, vásárolhat egyet egy __tartományregisztrálóhoz__. A folyamat szolgáltatásfrissítési regisztráló szervezetek, mint a költségek. A regisztráló is használható eszközöket biztosít a tartománynév kezeléséhez. Ezek az eszközök használhatók teljesen minősített tartománynév hozzárendelése (például a www\.contoso.com) a webszolgáltatást futtató IP-címet.

## <a name="get-an-ssl-certificate"></a>SSL-tanúsítvány beszerzése

Számos módon beolvasása egy SSL-tanúsítványt (digitális tanúsítványt). A leggyakoribb eset az, hogy vásároljon egyet egy __hitelesítésszolgáltató__ (CA). Ha beszerezte a tanúsítványt, függetlenül kell a következő fájlokat:

* A __tanúsítvány__. A tanúsítvány kell tartalmaznia a teljes tanúsítványlánccal, és a PEM-kódolású kell lennie.
* A __kulcs__. A kulcsnak kell lennie a PEM-kódolású.

A tanúsítvány igénylésekor meg kell adni a teljesen minősített tartománynevét (FQDN) a címet, a webszolgáltatások használatát tervezi. Például: www\.contoso.com. A cím a tanúsítványba megjelölve, és az ügyfelek által használt cím képest, a web service identitásának érvényesítése során. A címek nem egyeznek, ha az ügyfelek egy hibaüzenetet fog kapni.

> [!TIP]
> Ha a hitelesítésszolgáltató nem adja meg a tanúsítvány és kulcs-fájlok PEM-kódolású formájában, a segédprogram használható például [OpenSSL](https://www.openssl.org/) használatával módosíthatja.

> [!WARNING]
> Önaláírt tanúsítványok használandó csakis fejlesztési célokra. Ezek nem használandó éles környezetben. Önaláírt tanúsítványok problémákat okozhat az ügyfél az alkalmazásokat. További információkért lásd: az ügyfélalkalmazásban található használt hálózati könyvtárat dokumentációját.

## <a id="enable"></a> SSL engedélyezése és üzembe helyezése

Üzembe helyezése (vagy ismételt telepítése) SSL engedélyezve van a szolgáltatást, állítsa be a `ssl_enabled` paramétert `True`, bárhol is alkalmazható. Állítsa be a `ssl_certificate` paraméter értékét a __tanúsítvány__ fájl és a `ssl_key` értékéhez a __kulcs__ fájlt.

+ **Az Azure Kubernetes Service (AKS) üzembe helyezése**

  Amikor üzembe az aks-ben, hozzon létre egy új AKS-fürtöt, vagy csatlakoztasson egy már meglévőt. Új fürt létrehozásának használ [AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-) használja egy meglévő fürt csatlakoztatása közben [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-). Mindkét adja vissza egy konfigurációs objektumot, amely rendelkezik egy `enable_ssl` metódust.

  A `enable_ssl` módszert használja, vagy hozzá kell adni egy Microsoft által biztosított tanúsítványt.

  * A tanúsítvány használatakor __a Microsoft által biztosított__, kell használnia a `leaf_domain_label` paraméter. Ez a paraméter használatával hoz létre a szolgáltatás a Microsoft által biztosított tanúsítványt használ. A `leaf_domain_label` létrehozásához a szolgáltatás DNS-nevét használja. Érték például `myservice` hoz létre a tartomány nevére `myservice<6-random-characters>.<azureregion>.cloudapp.azure.com`, ahol `<azureregion>` az a régió, amely tartalmazza a szolgáltatás. Másik lehetőségként használhatja a `overwrite_existing_domain` paraméterrel írhatja felül a meglévő levél tartománycímkéjét.

    Üzembe helyezése (vagy újratelepítés) SSL engedélyezve van a szolgáltatást, állítsa be a `ssl_enabled` paramétert `True`, bárhol is alkalmazható. Állítsa be a `ssl_certificate` paraméter értékét a __tanúsítvány__ fájl és a `ssl_key` értékéhez a __kulcs__ fájlt.

    > [!IMPORTANT]
    > A Microsoft által biztosított tanúsítványt használ, amikor nem kell vásárolni a saját tanúsítvánnyal vagy tartománynév számára.

    A következő példa bemutatja, hogyan hozhat létre, amelyek lehetővé teszik a Microsoft által létrehozott SSL-tanúsítvány konfigurációkat:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name).enable_ssl(leaf_domain_label = "myservice")
    ```

  * Használata esetén __vásárolt tanúsítványt__, használja a `ssl_cert_pem_file`, `ssl_key_pem_file`, és `ssl_cname` paramétereket.  A következő példa bemutatja, hogyan hozhat létre egy SSL-tanúsítvány használatával adnia használó konfigurációk `.pem` fájlok:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name).enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  További információ a `enable_ssl`, lásd: [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) és [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

+ **Az Azure Container Instances (ACI) üzembe helyezése**

  Üzembe helyezése az aci Szolgáltatásban, során adja meg az SSL-kapcsolódó paraméterek értékeit a kódrészletben látható módon:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  További információkért lásd: [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>A DNS frissítése

Ezt követően frissítenie kell a DNS, a web Service mutasson.

+ **Az ACI**:

  Az a tartománynév regisztrálójánál az által biztosított eszközök segítségével frissítse a DNS-rekordot a tartománynév. A rekord a szolgáltatás IP-címre kell mutatnia.

  A regisztráló és az idő függvényében élettartam (TTL) van konfigurálva a tartománynév, akár néhány órát, mielőtt az ügyfelek fel tudják oldani a tartománynév több percet is igénybe vehet.

+ **Az aks-ben**:

  > [!WARNING]
  > Ha használta a `leaf_domain_label` szolgáltatás létrehozásához a Microsoft által biztosított tanúsítvánnyal, ne manuálisan frissítse a DNS-értéket, a fürt számára. Az érték automatikusan kell beállítani.

  Frissítse a DNS, a "Beállítások" lapon a "nyilvános IP-címe" az AKS-fürtöt a képen látható módon. A nyilvános IP-címet az erőforráscsoportban, amely tartalmazza az AKS-ügynök csomópontok és más hálózati erőforrások létrehozása az erőforrástípusok egyikét találja.

  ![Azure Machine Learning service: Webszolgáltatások SSL használatával biztonságossá tétele](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>További lépések
Az alábbiak végrehajtásának módját ismerheti meg:
+ [Machine learning-webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md)
+ [Biztonságos futtatására kísérletek vagy következtetési egy Azure virtuális hálózaton belül](how-to-enable-virtual-network.md)


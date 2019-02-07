---
title: (ELAVULT) Vázlat használata az Azure Container Service és az Azure Container Registrybe
description: Hozzon létre egy ACS Kubernetes-fürtöt és egy Azure Container Registryt az első alkalmazás létrehozására az Azure-ban a Draft segítségével.
services: container-service
author: squillace
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 36e765d439d616ec165a2b53d2044586e73cde76
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809214"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(ELAVULT) Vázlat használata az Azure Container Service és az Azure Container Registry létrehozása és a Kubernetes-alkalmazás üzembe helyezése

> [!TIP]
> Ez a cikk, amely a frissített verziót használja Azure Kubernetes Service-ben, lásd: [vázlat használata az Azure Kubernetes Service (AKS)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

A [Draft](https://aka.ms/draft) egy új nyílt forráskódú eszköz, amely megkönnyíti a tárolóval működő alkalmazások fejlesztését és azok üzembe helyezését Kubernetes-fürtökben anélkül, hogy részletesen ismerné a Dockert és a Kubernetest – vagy akár csak telepítenie kellene őket. A Draft és a hasonló eszközök révén Ön és csapata a Kubernetesben való alkalmazáskészítésre összpontosíthat, anélkül, hogy sok figyelmet kellene fordítania az infrastruktúrára.

A Draft bármely Docker-rendszerképjegyzékkel és Kubernetes-fürttel használható, beleértve a helyi használatot. Ez az oktatóanyag bemutatja, hogyan használható az ACS Kubernetes és az ACR élő, de biztonságos fejlesztői folyamatok létrehozására a Draft segítségével Kubernetes, és az Azure DNS használatával teszi közzé, hogy mások is lássák, egy tartományi fejlesztői a folyamat.


## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
Egyszerűen [hozzon létre egy új Azure Container Registryt](../../container-registry/container-registry-get-started-azure-cli.md) a következő lépésekkel:

1. Hozzon létre egy Azure-erőforráscsoportot az ACR regisztrációs adatbázis és a Kubernetes-fürt kezeléséhez az ACS-ben.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Hozzon létre egy ACR-lemezkép beállításjegyzék [az acr létrehozása](/cli/azure/acr#az-acr-create) és ellenőrizze, hogy a `--admin-enabled` beállítás `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Azure Container Service létrehozása a Kubernetes-szel

Most már készen áll az [az acs create](/cli/azure/acs#az-acs-create) használatára, hogy ACS-fürtöt hozzon létre a Kubernetes-szel az `--orchestrator-type` értékeként.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> A Kubernetes nem az alapértelmezett vezénylőtípus, így mindenképpen használnia kell a `--orchestrator-type kubernetes` kapcsolót.

A kimenet sikeres működés esetén az alábbihoz hasonló.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Most, hogy kész a fürt, importálhatja a hitelesítő adatokat az [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) paranccsal. Ekkor már rendelkezik helyi konfigurációs fájllal a fürthöz, ami a Helm és a Draft számára szükséges.

## <a name="install-and-configure-draft"></a>A draft telepítése és konfigurálása


1. Töltse le, a környezetének tervezet https://github.com/Azure/draft/releases és telepítheti az útvonalon, hogy a parancs segítségével.
2. Töltse le a helm, a környezetnek https://github.com/kubernetes/helm/releases és [telepítheti az útvonalon, úgy, hogy a parancs segítségével](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Konfigurálja a Draftot a beállításjegyzék használatára, és hozzon létre altartományt minden általa létrehozott Helm-diagramhoz. A Draft konfigurálásához a következők szükségesek:
  - Az Azure Container Registry neve (a példában `draftacsdemo`)
  - A beállításkulcs vagy jelszó a következőből: `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

  Hívás `draft init` és a konfigurációs folyamat bekéri a fenti értékeket; vegye figyelembe, hogy az URL-cím formátuma a beállításjegyzék URL-címe, a beállításjegyzék neve (ebben a példában `draftacsdemo`) plusz `.azurecr.io`. A felhasználónév önállóan a beállításjegyzék neve. A folyamat az első futtatáskor a következőhöz lesz hasonló.
 ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
```

Most már készen áll az alkalmazás üzembe helyezésére.


## <a name="build-and-deploy-an-application"></a>Alkalmazás fejlesztése és üzembe helyezése

A Draft-tárházban [hat egyszerű példaalkalmazást](https://github.com/Azure/draft/tree/master/examples) talál. Klónozza az adattárat, és használja a [Java-példában](https://github.com/Azure/draft/tree/master/examples/java). Módosítsa a példák/java-könyvtárat, és a típus `draft create` hozhat létre az alkalmazást. Ez az alábbi példához hasonlóan nézhet ki.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

A parancs kimenete egy Dockerfile és egy Helm-diagram. A létrehozáshoz és üzembe helyezéshez egyszerűen írja be a `draft up` parancsot. A kimenet hosszú, de az alábbi példához hasonlóan kell lennie.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Az alkalmazás biztonságos megtekintéséhez

A tároló most már fut az ACS-ben. A megtekintéséhez használja a `draft connect` parancsot, amely a fürt IP-címhez biztonságos kapcsolatot létesít az alkalmazás egy adott portot, hogy helyileg is megtekintheti. Ha ez sikeres, keresse meg az URL-cím első sora után az alkalmazás csatlakozni a **sikeres** kijelző.

> [!NOTE]
> Üzenetet kap arról, hogy nincs podok készen is kap, ha Várjon egy kicsit, majd próbálkozzon újra, vagy megtekintheti a felkészülésére a podok `kubectl get pods -w` , és próbálkozzon újra, ha így tesznek.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Az előző példában a következőt `curl -s http://localhost:46143` a válasz fogadására `Hello World, I'm Java!`. Amikor, CTRL + vagy a CMD + C (operációs rendszer a környezettől függően), a biztonságos alagút szakadt, és léptetés továbbra is.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>A megosztóalkalmazás által egy üzembe helyezési tartomány beállítása az Azure DNS használata

Már elvégezte a Draft hoz létre az előző lépésekben fejlesztői iteráció hurok. Azonban megoszthatja az alkalmazás által az interneten keresztül:
1. Az ACS-fürtben (ezzel biztosítva a nyilvános IP-címet, amelyen meg az alkalmazást) bejövő forgalmi telepítése
2. Az egyéni tartományt az Azure DNS-delegálás, és a tartomány leképezése az IP cím ACS rendel a bejövőforgalom-vezérlőt

### <a name="use-helm-to-install-the-ingress-controller"></a>Helm használatával telepítünk a bejövőforgalom-vezérlőt.
Használat **helm** keresse meg és telepítse a `stable/traefik`, bejövőforgalom-vezérlőt, a buildek számára a bejövő kérések engedélyezéséhez.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Ezután állítson be egy figyelőpontot az `ingress` (bejövőforgalom-) vezérlőn a külső IP-érték rögzítéséhez, amikor üzembe van helyezve. Az IP-cím lesz leképezve a következő szakaszban az üzembe helyezés tartományához.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

Ebben az esetben az üzembe helyezés tartományához használható külső IP-cím: `13.64.108.240`. A tartományt most már leképezheti erre az IP-címre.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Map the ingress IP to a custom subdomain

A Draft kiadást hoz létre minden egyes létrehozott Helm-diagramhoz – minden egyes alkalmazáshoz, amin dolgozik. Mindegyik kap egy létrehozott nevet, amelyet **draft** , egy _altartomány_ felügyelt legfelső szintű _üzembe helyezési tartomány_ , amelyek. (A jelen példában a `squillace.io`-t használjuk az üzembe helyezés tartományaként.) Az altartomány-viselkedés engedélyezéséhez létre kell hoznia egy A rekordot `'*.draft'` az üzembe helyezés tartományának DNS-bejegyzéseihez, hogy minden egyes létrehozott altartomány a Kubernetes-fürt bejövőforgalom-vezérlőjéhez legyen irányítva. 

Saját tartományszolgáltatójának saját módszere van a DNS-kiszolgálók hozzárendelésére; [a tartomány névkiszolgálóinak delegálására az Azure DNS-be](../../dns/dns-delegate-domain-azure-dns.md) tegye a következőket:

1. Hozzon létre egy erőforráscsoportot a zóna számára.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Hozzon létre DNS-zónát a tartományához.
Használja az [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) parancsot a névkiszolgálók beszerzéséhez a tartomány DNS-vezérlésének az Azure DNS-be való delegálásához.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Adja hozzá a tartományszolgáltatónak megadott DNS-kiszolgálókat az üzembe helyezési tartományhoz, ami lehetővé teszi, hogy az Azure DNS szolgáltatással átirányíthassa tartományát, ahogy kívánja. Tartomány eltérő teheti ezt meg; [az Azure DNS a tartomány névkiszolgálóinak delegálására](../../dns/dns-delegate-domain-azure-dns.md) néhány látható, ha tisztában van a részleteket. 
4. Miután a tartomány delegálva lett az Azure DNS-, hozzon létre az üzembe helyezési tartomány-hozzárendelés, A rekordhalmaz-bejegyzést a `ingress` IP az előző szakaszban szereplő 2. lépés.
  ```azurecli
  az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
  ```
A kimenet a következőhöz hasonló lesz:
  ```json
  {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
  }
  ```
5. Telepítse újra a **vázlat**

   1. Távolítsa el **draftd** írja be a fürtből `helm delete --purge draft`. 
   2. Telepítse újra a **draft** azonos használatával `draft-init` parancs, de a `--ingress-enabled` lehetőséget:
    ```bash
    draft init --ingress-enabled
    ```
   Fentiekkel először, kövesse a megjelenő utasításokat. Lehetősége van azonban egy további kérdésre válaszolni, a teljes tartománynevet a az Azure DNS-ben konfigurált elérési úton.

6. Adja meg a legfelső szintű tartományt (például draft.example.com) bejövő forgalom: draft.squillace.io
7. Meghívásakor `draft up` ezúttal fogja látni az alkalmazás (vagy `curl` ,) az űrlap URL-címen `<appname>.draft.<domain>.<top-level-domain>`. Ebben a példában esetén `http://handy-labradoodle.draft.squillace.io`. 
```bash
curl -s http://handy-labradoodle.draft.squillace.io
Hello World, I'm Java!
```


## <a name="next-steps"></a>További lépések

Most, hogy már van ACS Kubernetes-fürtje, megvizsgálhatja az [Azure Container Registry](../../container-registry/container-registry-intro.md) használatát ebben a forgatókönyvben további, különböző üzemelő példányok létrehozásához. Például létrehozhat egy draft._basedomain.toplevel_ tartományi DNS-rekordkészletet, amely mélyebb altartományból szabályozza a dolgokat adott ACS-környezetekhez.







---
title: "A Draft használata az Azure Container Service-szel és az Azure Container Registryvel | Microsoft Docs"
description: "Hozzon létre egy ACS Kubernetes-fürtöt és egy Azure Container Registryt az első alkalmazás létrehozására az Azure-ban a Draft segítségével."
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Kubernetes, Draft, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 08fd66ed68b651bd24bc1bc58ec9631106665381
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>A Draft használata az Azure Container Service-szel és az Azure Container Registryvel alkalmazások készítéséhez és a Kubernetesben való üzembe helyezéséhez

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

A [Draft](https://aka.ms/draft) egy új nyílt forráskódú eszköz, amely megkönnyíti a tárolóval működő alkalmazások fejlesztését és azok üzembe helyezését Kubernetes-fürtökben anélkül, hogy részletesen ismerné a Dockert és a Kubernetest – vagy akár csak telepítenie kellene őket. A Draft és a hasonló eszközök révén Ön és csapata a Kubernetesben való alkalmazáskészítésre összpontosíthat, anélkül, hogy sok figyelmet kellene fordítania az infrastruktúrára.

A Draft bármely Docker-rendszerképjegyzékkel és Kubernetes-fürttel használható, beleértve a helyi használatot. Ez az oktatóanyag bemutatja, ACS használata Kubernetes és ACR élő, de biztonságos fejlesztői folyamatokat létrehozni a Vázlat használata Kubernetes és az Azure DNS használatával teszi közzé, hogy mások is láthatják olyan tartományban fejlesztői folyamat.


## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
Egyszerűen [hozzon létre egy új Azure Container Registryt](../../container-registry/container-registry-get-started-azure-cli.md) a következő lépésekkel:

1. Hozzon létre egy Azure-erőforráscsoportot az ACR regisztrációs adatbázis és a Kubernetes-fürt kezeléséhez az ACS-ben.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Hozzon létre az ACR kép beállításjegyzék [az acr létrehozása](/cli/azure/acr#create) , és győződjön meg arról, hogy a `--admin-enabled` beállítás `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Azure Container Service létrehozása a Kubernetes-szel

Most már készen áll az [az acs create](/cli/azure/acs#create) használatára, hogy ACS-fürtöt hozzon létre a Kubernetes-szel az `--orchestrator-type` értékeként.
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

Most, hogy kész a fürt, importálhatja a hitelesítő adatokat az [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) paranccsal. Ekkor már rendelkezik helyi konfigurációs fájllal a fürthöz, ami a Helm és a Draft számára szükséges.

## <a name="install-and-configure-draft"></a>A draft telepítése és konfigurálása


1. A környezetnek a https://github.com/Azure/draft/releases vázlat letöltése és telepítése az elérési ÚTHOZ, hogy a parancs is használható.
2. Töltse le a környezetnek a https://github.com/kubernetes/helm/releases helm és [telepítse azt az elérési út, hogy a parancs is használható](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Konfigurálja a Draftot a beállításjegyzék használatára, és hozzon létre altartományt minden általa létrehozott Helm-diagramhoz. A Draft konfigurálásához a következők szükségesek:
  - Az Azure Container Registry neve (a példában `draftacsdemo`)
  - A beállításkulcs vagy jelszó a következőből: `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

  Hívás `draft init` , és a konfigurációs folyamat bekéri a fenti értékek; vegye figyelembe, hogy az URL-cím formátuma a beállításjegyzék URL-címet a neve (a példában `draftacsdemo`) plus `.azurecr.io`. A felhasználónév az a saját maga neve. A folyamat az első futtatáskor a következőhöz lesz hasonló.
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

A Draft-tárházban [hat egyszerű példaalkalmazást](https://github.com/Azure/draft/tree/master/examples) talál. A tárház klónozása, és most használja az [Java példa](https://github.com/Azure/draft/tree/master/examples/java). Váltson át a példák/java könyvtár, és írja be `draft create` hozható létre az alkalmazás. Ez az alábbi példához hasonlóan nézhet ki.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

A parancs kimenete egy Dockerfile és egy Helm-diagram. A létrehozáshoz és üzembe helyezéshez egyszerűen írja be a `draft up` parancsot. A kimeneti túl hosszú, de az alábbi példához hasonló legyen.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Az alkalmazás biztonságos megtekintéséhez

A tároló most már fut. Ha az ACS. A megtekintéséhez használja a `draft connect` parancsot, amely a fürt IP-címhez biztonságos kapcsolatot létesít egy adott portot az alkalmazáshoz, helyileg tekintheti. Ha sikeres, keresse meg az URL-cím, az első sor után az alkalmazás kapcsolódni a **sikeres** mutató.

> [!NOTE]
> Ha üzenetet kap arról, hogy nincs három munkaállomás-csoporttal kész van-e, várjon egy kicsit, és próbálkozzon újra, vagy a három munkaállomás-csoporttal felkészülésére a figyelheti `kubectl get pods -w` , és próbálkozzon újra, ha így tesznek.

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

Az előző példában a következőt `curl -s http://localhost:46143` kapott a választ `Hello World, I'm Java!`. Ha Ön CTRL + vagy CMD + C (az operációs rendszer a környezettől függően), van bontva a biztonságos alagutat, és léptetés továbbra is.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>A megosztóalkalmazás által telepítési tartománynév beállítása az Azure DNS-ben

Már elvégezte a fejlesztői iterációs hurok vázlat hozza létre az előző lépésben. Azonban megoszthatja az alkalmazás által az interneten keresztül:
1. Egy érkező telepíti az ACS fürt (adjon meg egy nyilvános IP-cím, amelynek meg szeretné jeleníteni az alkalmazás)
2. Az egyéni tartomány az Azure DNS-delegálás, és a tartomány leképezése az IP-cím ACS rendel a érkező vezérlő

### <a name="use-helm-to-install-the-ingress-controller"></a>A bejövő adatok tartományvezérlő telepítése helm használatával.
Használjon **helm** keresése és telepítése `stable/traefik`, az érkező-vezérlőhöz, a buildek a bejövő kérések engedélyezéséhez.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Ezután állítson be egy figyelőpontot az `ingress` (bejövőforgalom-) vezérlőn a külső IP-érték rögzítéséhez, amikor üzembe van helyezve. Az IP-cím az, amelyik [az üzembe helyezés tartományhoz lesz csatlakoztatva](#wire-up-deployment-domain) a következő szakaszban.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

Ebben az esetben az üzembe helyezés tartományához használható külső IP-cím: `13.64.108.240`. A tartományt most már leképezheti erre az IP-címre.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Az érkező IP-cím hozzárendelése egyéni altartomány

A Draft kiadást hoz létre minden egyes létrehozott Helm-diagramhoz – minden egyes alkalmazáshoz, amin dolgozik. Mindegyik lekérdezi egy létrehozott nevet, amellyel a **vázlat** , egy _altartomány_ fölött a legfelső szintű _telepítési tartomány_ , amelyek. (A jelen példában a `squillace.io`-t használjuk az üzembe helyezés tartományaként.) Az altartomány-viselkedés engedélyezéséhez létre kell hoznia egy A rekordot `'*.draft'` az üzembe helyezés tartományának DNS-bejegyzéseihez, hogy minden egyes létrehozott altartomány a Kubernetes-fürt bejövőforgalom-vezérlőjéhez legyen irányítva. 

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
Használja az [az network dns zone create](/cli/azure/network/dns/zone#create) parancsot a névkiszolgálók beszerzéséhez a tartomány DNS-vezérlésének az Azure DNS-be való delegálásához.
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
3. Adja hozzá a tartományszolgáltatónak megadott DNS-kiszolgálókat az üzembe helyezési tartományhoz, ami lehetővé teszi, hogy az Azure DNS szolgáltatással átirányíthassa tartományát, ahogy kívánja. Ehhez a módszer függ a tartomány adnia; [delegálása az Azure DNS-tartomány nameservers](../../dns/dns-delegate-domain-azure-dns.md) egyes, amelyet érdemes tudni részleteket tartalmazza. 
4. Ha a tartomány delegálva lett az Azure DNS-, a központi telepítés tartomány-leképezés csak akkor A rekordhalmaz bejegyzés létrehozása a `ingress` IP az előző szakaszban 2. lépés.
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
   2. Telepítse újra a **vázlat** azonos használatával `draft-init` parancs, de a `--ingress-enabled` lehetőséget:
    ```bash
    draft init --ingress-enabled
    ```
   Kövesse a megjelenő utasításokat, úgy, ahogy az első alkalommal fent. Azonban, hogy egy további kérdésre válaszolni, a teljes tartománynevet az Azure DNS-beli konfigurált elérési úton.

6. Adja meg a legfelső szintű tartomány (pl. draft.example.com) érkező: draft.squillace.io
7. Hívás esetén `draft up` ezúttal fogja látni az alkalmazás (vagy `curl` azt), az URL-cím `<appname>.draft.<domain>.<top-level-domain>`. E példában `http://handy-labradoodle.draft.squillace.io`. 
```bash
curl -s http://handy-labradoodle.draft.squillace.io
Hello World, I'm Java!
```


## <a name="next-steps"></a>Következő lépések

Most, hogy már van ACS Kubernetes-fürtje, megvizsgálhatja az [Azure Container Registry](../../container-registry/container-registry-intro.md) használatát ebben a forgatókönyvben további, különböző üzemelő példányok létrehozásához. Például létrehozhat egy draft._basedomain.toplevel_ tartományi DNS-rekordkészletet, amely mélyebb altartományból szabályozza a dolgokat adott ACS-környezetekhez.







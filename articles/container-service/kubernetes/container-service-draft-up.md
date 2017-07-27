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
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: b70d2340c0f1286fa355a78a4cd0cb1ce37cbc39
ms.contentlocale: hu-hu
ms.lasthandoff: 07/25/2017

---

# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>A Draft használata az Azure Container Service-szel és az Azure Container Registryvel alkalmazások készítéséhez és a Kubernetesben való üzembe helyezéséhez

A [Draft](https://aka.ms/draft) egy új nyílt forráskódú eszköz, amely megkönnyíti a tárolóval működő alkalmazások fejlesztését és azok üzembe helyezését Kubernetes-fürtökben anélkül, hogy részletesen ismerné a Dockert és a Kubernetest – vagy akár csak telepítenie kellene őket. A Draft és a hasonló eszközök révén Ön és csapata a Kubernetesben való alkalmazáskészítésre összpontosíthat, anélkül, hogy sok figyelmet kellene fordítania az infrastruktúrára.

A Draft bármely Docker-rendszerképjegyzékkel és Kubernetes-fürttel használható, beleértve a helyi használatot. Ez az oktatóanyag bemutatja, hogyan használható az ACS Kubernetes, az ACR és az Azure DNS élő CI/CD fejlesztői folyamatok létrehozására a Draft segítségével.


## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
Egyszerűen [hozzon létre egy új Azure Container Registryt](../../container-registry/container-registry-get-started-azure-cli.md) a következő lépésekkel:

1. Hozzon létre egy Azure-erőforráscsoportot az ACR regisztrációs adatbázis és a Kubernetes-fürt kezeléséhez az ACS-ben.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Hozzon létre egy ACR-rendszerképjegyzéket az [az acr create](/cli/azure/acr#create) használatával
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Azure Container Service létrehozása a Kubernetes-szel

Most már készen áll az [az acs create](/cli/azure/acs#create) használatára, hogy ACS-fürtöt hozzon létre a Kubernetes-szel az `--orchestrator-type` értékeként.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
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
A Draft eszközre vonatkozó telepítési utasításokat a [Draft-tárház](https://github.com/Azure/draft/blob/master/docs/install.md) tartalmazza. Viszonylag egyszerű, de egyéb konfigurációt is igényel, mivel a [Helmre](https://aka.ms/helm) támaszkodik a Helm-diagramok létrehozásához és a Kubernetes-fürtben való üzembe helyezéséhez.

1. [A Helm letöltése és telepítése](https://aka.ms/helm#install).
2. A Helm segítségével keresse meg és telepítse a `stable/traefik`-et, illetve a bejövőforgalom-vezérlőt a bejövő kérések engedélyezéséhez a buildek számára.
    ```bash
    $ helm search traefik
    NAME            VERSION DESCRIPTION
    stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    Ezután állítson be egy figyelőpontot az `ingress` (bejövőforgalom-) vezérlőn a külső IP-érték rögzítéséhez, amikor üzembe van helyezve. Az IP-cím az, amelyik [az üzembe helyezés tartományhoz lesz csatlakoztatva](#wire-up-deployment-domain) a következő szakaszban.

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    Ebben az esetben az üzembe helyezés tartományához használható külső IP-cím: `13.64.108.240`. A tartományt most már leképezheti erre az IP-címre.

## <a name="wire-up-deployment-domain"></a>Az üzembe helyezési tartomány beállítása

A Draft kiadást hoz létre minden egyes létrehozott Helm-diagramhoz – minden egyes alkalmazáshoz, amin dolgozik. Mindegyik kap egy létrehozott nevet, amelyet a draft _altartományként_ használ az Ön által felügyelt legfelső szintű _üzembe helyezési tartomány_ fölött. (A jelen példában a `squillace.io`-t használjuk az üzembe helyezés tartományaként.) Az altartomány-viselkedés engedélyezéséhez létre kell hoznia egy A rekordot `'*'` az üzembe helyezés tartományának DNS-bejegyzéseihez, hogy minden egyes létrehozott altartomány a Kubernetes-fürt bejövőforgalom-vezérlőjéhez legyen irányítva.

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
3. Adja hozzá a tartományszolgáltatónak megadott DNS-kiszolgálókat az üzembe helyezési tartományhoz, ami lehetővé teszi, hogy az Azure DNS szolgáltatással átirányíthassa tartományát, ahogy kívánja.
4. Az üzembe helyezési tartomány számára hozzon létre A rekordhalmaz-bejegyzést az előző szakaszban szereplő 2. lépésbeli `ingress` IP-címre való leképezéshez.
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
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
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. Konfigurálja a Draftot a beállításjegyzék használatára, és hozzon létre altartományt minden általa létrehozott Helm-diagramhoz. A Draft konfigurálásához a következők szükségesek:
  - Az Azure Container Registry neve (a példában `draft`)
  - A beállításkulcs vagy jelszó a következőből: `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.
  - az üzembe helyezés gyökértartománya, amelyet konfigurált a Kubernetes bejövő forgalmának külső IP-címének leképezésére (itt `squillace.io`)

  A `draft init` parancs hívását követően a konfigurációs folyamat bekéri a fenti értékeket. A folyamat az első futtatáskor a következőhöz lesz hasonló.
    ```
    draft init
    Creating pack ruby...
    Creating pack node...
    Creating pack gradle...
    Creating pack maven...
    Creating pack php...
    Creating pack python...
    Creating pack dotnetcore...
    Creating pack golang...
    $DRAFT_HOME has been configured at /Users/ralphsquillace/.draft.

    In order to install Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io, quay.io, myregistry.azurecr.io): draft.azurecr.io
    2. Enter your username: draft
    3. Enter your password:
    4. Enter your org where Draft will push images [draft]: draft
    5. Enter your top-level domain for ingress (e.g. draft.example.com): squillace.io
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
    ```

Most már készen áll az alkalmazás üzembe helyezésére.


## <a name="build-and-deploy-an-application"></a>Alkalmazás fejlesztése és üzembe helyezése

A Draft-tárházban [hat egyszerű példaalkalmazást](https://github.com/Azure/draft/tree/master/examples) talál. A tárház klónozását követően használja a [Python-példát](https://github.com/Azure/draft/tree/master/examples/python). Váltson az examples/Python könyvtárra, és írja be a `draft create` parancsot az alkalmazás létrehozásához. Ez az alábbi példához hasonlóan nézhet ki.
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

A parancs kimenete egy Dockerfile és egy Helm-diagram. A létrehozáshoz és üzembe helyezéshez egyszerűen írja be a `draft up` parancsot. A kimenet hosszú, de az alábbi példához hasonlóan kezdődik.
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

és ha sikeres, a következő példához hasonlóan végződik.
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

Függetlenül a diagram nevétől, mostantól a `curl http://gangly-bronco.squillace.io` webhely fogadni tudja a `Hello World!` választ.

## <a name="next-steps"></a>Következő lépések

Most, hogy már van ACS Kubernetes-fürtje, megvizsgálhatja az [Azure Container Registry](../../container-registry/container-registry-intro.md) használatát ebben a forgatókönyvben további, különböző üzemelő példányok létrehozásához. Például létrehozhat egy draft._basedomain.toplevel_ tartományi DNS-rekordkészletet, amely mélyebb altartományból szabályozza a dolgokat adott ACS-környezetekhez.








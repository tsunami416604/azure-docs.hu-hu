---
title: (ELAVULT) Piszkozat használata az Azure Container Service szolgáltatással és az Azure Container Registry beállításjegyzékkel
description: Hozzon létre egy ACS Kubernetes-fürtöt és egy Azure Container Registryt az első alkalmazás létrehozására az Azure-ban a Draft segítségével.
author: squillace
ms.service: container-service
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 8d688d2918c9100019d033e93e9a3dca9e492de2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271135"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(ELAVULT) A Piszkozat használata az Azure Container Service szolgáltatással és az Azure Container Registry alkalmazás sal a Kubernetes alkalmazásának létrehozásához és üzembe helyezéséhez

> [!TIP]
> Az Azure Kubernetes-szolgáltatást használó cikk frissített verziójáról a [Vázlat használata az Azure Kubernetes szolgáltatással (AKS)](../../aks/kubernetes-draft.md)című témakörben olvashat.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

A [Draft](https://aka.ms/draft) egy új nyílt forráskódú eszköz, amely megkönnyíti a tárolóval működő alkalmazások fejlesztését és azok üzembe helyezését Kubernetes-fürtökben anélkül, hogy részletesen ismerné a Dockert és a Kubernetest – vagy akár csak telepítenie kellene őket. A Draft és a hasonló eszközök révén Ön és csapata a Kubernetesben való alkalmazáskészítésre összpontosíthat, anélkül, hogy sok figyelmet kellene fordítania az infrastruktúrára.

A Draft bármely Docker-rendszerképjegyzékkel és Kubernetes-fürttel használható, beleértve a helyi használatot. Ez az oktatóanyag bemutatja, hogyan hozhat létre az ACS-t a Kubernetes és az ACR használatával egy élő, de biztonságos fejlesztői folyamat létrehozásához a Kubernetes-ben a Piszkozat használatával, és hogyan használhatja az Azure DNS-t a fejlesztői folyamat elérhetővé tétele érdekében, hogy mások egy tartományban láthassák.


## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
Egyszerűen [hozzon létre egy új Azure Container Registryt](../../container-registry/container-registry-get-started-azure-cli.md) a következő lépésekkel:

1. Hozzon létre egy Azure-erőforráscsoportot az ACR-beállításjegyzék és a Kubernetes-fürt kezeléséhez az ACS-ben.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Hozzon létre egy ACR lemezkép-jegyzéket `--admin-enabled` [az acr create](/cli/azure/acr#az-acr-create) használatával, és győződjön meg arról, hogy a beállítás `true`a .
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


1. Töltse le a https://github.com/Azure/draft/releases piszkozatot a környezetéhez, és telepítse a PATH-ba, hogy a parancs használható legyen.
2. Töltse le a https://github.com/kubernetes/helm/releases kormányt a környezetéhez, és [telepítse a PATH-ba, hogy a parancs használható legyen](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Konfigurálja a Draftot a beállításjegyzék használatára, és hozzon létre altartományt minden általa létrehozott Helm-diagramhoz. A Draft konfigurálásához a következők szükségesek:
   - Az Azure Container Registry neve (a példában `draftacsdemo`)
   - A beállításkulcs vagy jelszó a következőből: `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

   A `draft init` hívás és a konfigurációs folyamat a fenti értékeket kéri; vegye figyelembe, hogy a rendszerleíró adatbázis URL-formátuma `draftacsdemo`a `.azurecr.io`rendszerleíró adatbázis neve (ebben a példában) plusz . A felhasználónév önmagában a rendszerleíró adatbázis neve. A folyamat az első futtatáskor a következőhöz lesz hasonló.
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

A Draft-tárházban [hat egyszerű példaalkalmazást](https://github.com/Azure/draft/tree/master/examples) talál. Klónozza a repo, és használjuk a [Java példa](https://github.com/Azure/draft/tree/master/examples/example-java). Váltson a példák/java `draft create` könyvtárba, és írja be az alkalmazás felépítéséhez. Ez az alábbi példához hasonlóan nézhet ki.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

A parancs kimenete egy Dockerfile és egy Helm-diagram. A létrehozáshoz és üzembe helyezéshez egyszerűen írja be a `draft up` parancsot. A kimenet kiterjedt, de a következő példához hasonlónak kell lennie.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Az alkalmazás biztonságos megtekintése

A tároló most fut az ACS-ben. A megtekintéséhez használja `draft connect` a parancsot, amely biztonságos kapcsolatot hoz létre a fürt IP-címéhez egy adott porttal az alkalmazáshoz, hogy helyileg megtekinthesse. Ha sikeres, keresse meg az URL-t, amely az alkalmazáshoz való csatlakozáshoz csatlakozik a **SIKER** jelző után az első sorban.

> [!NOTE]
> Ha kap egy üzenetet arról, hogy nem podok voltak kész, várjon egy pillanatra, `kubectl get pods -w` és próbálja meg újra, vagy meg lehet nézni a podok kész lesz, majd próbálja meg újra, ha nem.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Az előző példában beírhatja `curl -s http://localhost:46143` a `Hello World, I'm Java!`válasz fogadását. Ha CTRL+ vagy CMD+C (az operációs rendszer környezetétől függően) bontja a biztonságos alagutat, és folytathatja a iterációt.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Az alkalmazás megosztása központi telepítési tartomány azure DNS-sel való konfigurálásával

Már végrehajtotta a draft által az előző lépésekben létrehozott fejlesztői iterációs hurkot. Az alkalmazást azonban megoszthatja az interneten keresztül:
1. Bejövő adatszolgáltatás telepítése az ACS-fürtben (az alkalmazás megjelenítéséhez szükséges nyilvános IP-cím megadásához)
2. Az egyéni tartomány delegálása az Azure DNS-re és a tartomány hozzárendelése a be- ésélményvezérlőhöz rendelt ACS IP-címre

### <a name="use-helm-to-install-the-ingress-controller"></a>A vezérlő használata a be- és abe- és visszatérésvezérlő telepítéséhez.
Helm **helm** segítségével keresse meg `stable/traefik`és telepítse a bejövő kapcsolat vezérlőt, hogy engedélyezze a bejövő kérelmeket a buildek.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Ezután állítson be egy figyelőpontot az `ingress` (bejövőforgalom-) vezérlőn a külső IP-érték rögzítéséhez, amikor üzembe van helyezve. Az IP-cím az, amelyik az üzembe helyezés tartományhoz lesz csatlakoztatva a következő szakaszban.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

Ebben az esetben az üzembe helyezés tartományához használható külső IP-cím: `13.64.108.240`. A tartományt most már leképezheti erre az IP-címre.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>A be- és előkerülési cím hozzárendelése egyéni altartományhoz

A Draft kiadást hoz létre minden egyes létrehozott Helm-diagramhoz – minden egyes alkalmazáshoz, amin dolgozik. Mindegyik kap egy generált nevet, amelyet a **vázlat** _altartományként_ használ a vezérlő _gyökértelepítési tartomány_ tetején. (Ebben a példában `squillace.io` központi telepítési tartományként használjuk.) Az altartomány viselkedésének engedélyezéséhez létre kell `'*.draft'` hoznia egy A rekordot a központi telepítési tartomány DNS-bejegyzéseihez, hogy minden létrehozott altartomány a Kubernetes-fürt bejövő forgalomirányítójához kerüljön. 

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
3. Adja hozzá a tartományszolgáltatónak megadott DNS-kiszolgálókat az üzembe helyezési tartományhoz, ami lehetővé teszi, hogy az Azure DNS szolgáltatással átirányíthassa tartományát, ahogy kívánja. Ennek módja tartományonként változik; [delegálja a tartománynévkiszolgálókat az Azure DNS-re,](../../dns/dns-delegate-domain-azure-dns.md) és tartalmaz néhány olyan részletet, amelyet tudnia kell. 
4. Miután a tartomány delegálta az Azure DNS-re, hozzon létre `ingress` egy Rekordkészlet-bejegyzést a központi telepítési tartomány leképezése az IP-hez az előző szakasz 2.
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
5. **Piszkozat** újratelepítése

   1. A **draftd** fürtből a beírással eltávolítható a kidolgozás. `helm delete --purge draft` 
   2. Telepítse újra a **piszkozatot** ugyanazzal `draft-init` a paranccsal, de a `--ingress-enabled` következő lehetőséggel:
      ```bash
      draft init --ingress-enabled
      ```
      Válaszoljon a fenti első alkalommal. Azonban van még egy kérdés, amire válaszolnia kell, az Azure DNS-sel konfigurált teljes tartományelérési utat használva.

6. Adja meg a belépő -ba-ba (pl. draft.example.com) felső szintű tartományát: draft.squillace.io
7. Amikor ezúttal telefonál, `draft up` láthatja az alkalmazást (vagy `curl` azt) az űrlap `<appname>.draft.<domain>.<top-level-domain>`URL-címén. Ebben a példában `http://handy-labradoodle.draft.squillace.io`. 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>További lépések

Most, hogy már van ACS Kubernetes-fürtje, megvizsgálhatja az [Azure Container Registry](../../container-registry/container-registry-intro.md) használatát ebben a forgatókönyvben további, különböző üzemelő példányok létrehozásához. Például létrehozhat egy draft._basedomain.toplevel_ tartományi DNS-rekordkészletet, amely mélyebb altartományból szabályozza a dolgokat adott ACS-környezetekhez.







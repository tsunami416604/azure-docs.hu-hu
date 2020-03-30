---
title: Titkok kezelése Azure dev-térben végzett munka során
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Ismerje meg, hogyan használhatja a Kubernetes titkos kulcsokat futtatáskor vagy létrehozáskor az Azure Dev Spaces alkalmazásainak fejlesztése során
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, tárolók
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438461"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Titkok kezelése Azure dev-térben végzett munka során

A szolgáltatások szükség lehet bizonyos jelszavakat, kapcsolati karakterláncok és egyéb titkos kulcsok, például adatbázisok vagy más biztonságos Azure-szolgáltatások. Ezeknek a titkos kulcsoknak az értékeit a konfigurációs fájlokban, akkor elérhetővé teheti a kódot, mint a környezeti változók.  Ezeket a konfigurációs fájlokat óvatosan kell kezelni, hogy ne veszélyeztesse a titkos kulcsok biztonságát.

## <a name="storing-and-using-runtime-secrets"></a>Futásidejű titkos kulcsok tárolása és használata

Az Azure Dev Spaces két ajánlott, egyszerűsített lehetőséget kínál a titkos kulcsok tárolására az Azure `values.dev.yaml` Dev Spaces ügyféleszközök `azds.yaml`által létrehozott Helm-diagramokban: a fájlban és a szövegközi közvetlenül a alkalmazásban. Nem ajánlott titkokat tárolni `values.yaml`a.

> [!NOTE]
> A következő megközelítések bemutatják, hogyan tárolhatja és használhatja az ügyfél-eszközök által létrehozott Helm-diagramok titkos titkait. Ha saját Helm-diagramot hoz létre, a Helm diagram segítségével közvetlenül kezelheti és tárolhatja a titkokat.

### <a name="using-valuesdevyaml"></a>A values.dev.yaml használata

Az Azure Dev Spaces-szel már elkészített `values.dev.yaml` projektben hozzon `azds.yaml` létre egy fájlt ugyanabban a mappában, amely meghatározza a titkos kulcsokat és értékeket. Példa:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Ellenőrizze `azds.yaml` a fájlhivatkozásokat `values.dev.yaml` választhatóként a `?`használatával. Példa:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Ha további titkos fájljai vannak, itt is hozzáadhatja őket.

Frissítse vagy ellenőrizze a szolgáltatás hivatkozik a titkos kulcsok at környezeti változók. Példa:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Futtassa a `azds up`frissített szolgáltatásokat a segítségével.

```console
azds up
```
 
A `kubectl` titkos kulcsok létrehozásának ellenőrzésére használható.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Nem ajánlott a titkos kulcsokat a forrásvezérlőben tárolni. Ha git használatával, `.gitignore` add a `values.dev.yaml` fájlhoz, hogy ne véglegesítse a titkos kulcsokat a forrás-ellenőrzés.

### <a name="using-azdsyaml"></a>Az azds.yaml használata

Az Azure Dev Spaces használatával már elkészített projektben adja hozzá a titkos kulcsokat és az `azds.yaml`értéket *$PLACEHOLDER* a *configurations.develop.install.set területen.* Példa:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> A titkos értékeket közvetlenül is `azds.yaml`megadhatja *anélkül, hogy $PLACEHOLDER* a szintaxist a rendszerben. Ez a megközelítés azonban `azds.yaml` nem ajánlott, mivel a forrásellenőrzés tárolja.
     
Hozzon `.env` létre egy fájlt `azds.yaml` ugyanabban a mappában, amelyen meg szeretné határozni a *$PLACEHOLDER* értékeket. Példa:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Nem ajánlott a titkos kulcsokat a forrásvezérlőben tárolni. Ha git használatával, `.gitignore` add a `.env` fájlhoz, hogy ne véglegesítse a titkos kulcsokat a forrás-ellenőrzés.

Frissítse vagy ellenőrizze a szolgáltatás hivatkozik a titkos kulcsok at környezeti változók. Példa:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Futtassa a `azds up`frissített szolgáltatásokat a segítségével.

```console
azds up
```
 
A `kubectl` titkos kulcsok létrehozásának ellenőrzésére használható.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Titkok használata összeállítási argumentumként

Az előző szakasz bemutatja, hogyan tárolhatja és használhatja a titkos kulcsokat a tároló futási idején. Bármilyen titkos kulcsot használhat a tárolókészítési időben, például egy `azds.yaml`privát NuGet jelszavát a használatával.

A `azds.yaml`alkalmazásban állítsa be a buildidő-titkos kulcsokat a `<variable name>: ${secret.<secret name>.<secret key>}` *configurations.develop.build.args-ban* a szintaxis használatával. Példa:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

A fenti példában *a mynugetsecret* egy meglévő titkos kulcs, és *a pattoken* egy meglévő kulcs.

>[!NOTE]
> A titkos nevek és `.` kulcsok tartalmazhatják a karaktert. Használja `\` a `.` menekülésre, amikor titkokat ad át, mint épít argumentumokat. Például, hogy adja át a titkos nevű *foo.bar* a *kulcs a token:* `MYTOKEN: ${secret.foo\.bar.token}`. Ezenkívül a titkos kulcsok kiértékelhetők előtaggal és utótag szöveggel. Például: `MYURL: eus-${secret.foo\.bar.token}-version1`. A szülő- és nagyszülő-terekben elérhető titkok buildargumentumként is átadhatók.

A Docker-fájlban *ARG* használja az ARG-direktívát a titkos kulcsot, majd használja ugyanazt a változót később a Dockerfile-ban. Példa:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Frissítse a fürtben futó szolgáltatásokat ezekkel a módosításokkal. A parancssorban futtassa a következő parancsot:

```
azds up
```

## <a name="next-steps"></a>További lépések

Ezekkel a módszerekkel most már biztonságosan csatlakozhat egy adatbázishoz, egy Azure-gyorsítótárhoz a Redis számára, vagy hozzáférhet a biztonságos Azure-szolgáltatásokhoz.
 

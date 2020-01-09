---
title: A titkok kezelése az Azure fejlesztői területtel való munka során
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Ismerje meg, hogyan használhatja a Kubernetes titkokat futtatáskor vagy létrehozáskor az Azure dev Spaces-alkalmazások fejlesztésekor
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438461"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>A titkok kezelése az Azure fejlesztői területtel való munka során

A szolgáltatásokhoz bizonyos jelszavak, a kapcsolatok karakterláncai és egyéb titkos kulcsok, például adatbázisok vagy egyéb biztonságos Azure-szolgáltatások szükségesek. Ha beállítja a titkos kódok értékeit a konfigurációs fájlokban, a programkódban környezeti változókként elérhetővé teheti őket.  Ezeket a konfigurációs fájlokat körültekintően kell kezelni, hogy ne veszélyeztesse a titkok biztonságát.

## <a name="storing-and-using-runtime-secrets"></a>Futásidejű titkok tárolása és használata

Az Azure dev Spaces szolgáltatás két ajánlott, egyszerűbb lehetőséget biztosít a titkok tárolására az Azure dev Spaces-ügyfél eszközei által létrehozott Helm-diagramokon: a `values.dev.yaml` fájlban, és közvetlenül a `azds.yaml`. A titkok tárolása nem ajánlott `values.yaml`ban.

> [!NOTE]
> Az alábbi módszerek azt mutatják be, hogyan tárolhatók és használhatók az ügyfél-eszközkészlet által létrehozott Helm-diagramok titkai. Ha létrehoz egy saját Helm-diagramot, a Helm diagramot közvetlenül is használhatja a titkok kezeléséhez és tárolásához.

### <a name="using-valuesdevyaml"></a>A Values. dev. YAML használata

Egy olyan projektben, amelyet már előkészített az Azure dev Spaces használatával, hozzon létre egy `values.dev.yaml` fájlt ugyanabban a mappában, mint `azds.yaml` a titkos kulcsok és értékek meghatározásához. Példa:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

`?`használatával ellenőrizze, hogy a `azds.yaml` fájl hivatkozásai `values.dev.yaml` választhatók-e. Példa:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Ha további titkos fájlokkal rendelkezik, ezeket is hozzáadhatja.

Frissítse vagy ellenőrizze, hogy a szolgáltatás környezeti változókként hivatkozik-e a titkokra. Példa:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
A frissített szolgáltatásokat `azds up`használatával futtathatja.

```console
azds up
```
 
A `kubectl` használatával ellenőrizheti, hogy a titkok létre lettek-e hozva.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Nem ajánlott a titkokat a forrás vezérlőelemben tárolni. Ha a git-t használja, vegyen fel `values.dev.yaml`t a `.gitignore` fájlba, és ne véglegesítse a titkos kulcsokat a verziókövetés során.

### <a name="using-azdsyaml"></a>A azds. YAML használata

Egy olyan projektben, amelyet már előkészített az Azure dev Spaces használatával, adja hozzá a titkos kulcsokat és az értékeket a konfigurációk területen található *$PLACEHOLDER* szintaxissal *. fejlessze. install. set* in `azds.yaml`. Példa:

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
> A titkos értékeket közvetlenül a `azds.yaml` *$PLACEHOLDER* szintaxisának használata nélkül is megadhatja. Ez a megközelítés azonban nem ajánlott, mivel `azds.yaml` a verziókövetés tárolja.
     
Hozzon létre egy `.env` fájlt ugyanabban a mappában, mint `azds.yaml` a *$PLACEHOLDER* értékek definiálásához. Példa:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Nem ajánlott a titkokat a forrás vezérlőelemben tárolni. Ha a git-t használja, vegyen fel `.env`t a `.gitignore` fájlba, és ne véglegesítse a titkos kulcsokat a verziókövetés során.

Frissítse vagy ellenőrizze, hogy a szolgáltatás környezeti változókként hivatkozik-e a titkokra. Példa:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
A frissített szolgáltatásokat `azds up`használatával futtathatja.

```console
azds up
```
 
A `kubectl` használatával ellenőrizheti, hogy a titkok létre lettek-e hozva.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Titkok használata Build argumentumként

Az előző szakaszban megmutatta, hogyan tárolhat és használhat titkokat a tároló futási idején. A tárolók felépítésének időpontjában bármilyen titkos kulcsot is használhat, például egy privát NuGet tartozó jelszót `azds.yaml`használatával.

A `azds.yaml`ban állítsa be a konfigurációkban a létrehozási idő titkait. a `<variable name>: ${secret.<secret name>.<secret key>}` szintaxissal alakítsa ki a Build. *Build. ARG* -t. Példa:

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

A fenti példában a *mynugetsecret* egy meglévő titkos kulcs, és a *pattoken* egy meglévő kulcs.

>[!NOTE]
> A titkos nevek és kulcsok a `.` karaktert is tartalmazhatják. A `\` használatával Escape-`.` a titkokat Build argumentumként való átadásakor. Ha például egy *foo. bar* nevű titkos kulcsot szeretne átadni a *token*kulcsaként: `MYTOKEN: ${secret.foo\.bar.token}`. Emellett a titkokat előtaggal és Postfix szöveggel is kiértékelheti. Például: `MYURL: eus-${secret.foo\.bar.token}-version1`. Emellett a szülő és a nagyszülő terekben elérhető titkokat Build argumentumként lehet átadni.

A Docker használja az *ARG* direktívát a titkos kód felhasználásához, majd használja ugyanezt a változót később a Docker. Példa:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Frissítse a fürtön futó szolgáltatásokat ezekkel a módosításokkal. A parancssorban futtassa a következő parancsot:

```
azds up
```

## <a name="next-steps"></a>Következő lépések

Ezekkel a módszerekkel mostantól biztonságosan csatlakozhat egy adatbázishoz, egy Azure-gyorsítótárhoz a Redis-hez, vagy hozzáférhet a biztonságos Azure-szolgáltatásokhoz.
 

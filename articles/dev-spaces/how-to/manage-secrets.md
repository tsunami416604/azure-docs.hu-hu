---
title: A titkok kezelése az Azure fejlesztői területtel való munka során
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
ms.openlocfilehash: b184f72dfbbfe093443ab8a9b79bafbece3a3d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790170"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>A titkok kezelése az Azure fejlesztői területtel való munka során

A szolgáltatásokhoz bizonyos jelszavak, a kapcsolatok karakterláncai és egyéb titkos kulcsok, például adatbázisok vagy egyéb biztonságos Azure-szolgáltatások szükségesek. Ha beállítja a titkos kódok értékeit a konfigurációs fájlokban, a programkódban környezeti változókként elérhetővé teheti őket.  Ezeket körültekintően kell kezelni, hogy ne veszélyeztesse a titkok biztonságát.

Az Azure dev Spaces szolgáltatás két ajánlott, egyszerűbb lehetőséget biztosít a titkok tárolására az Azure dev Spaces-ügyfél eszközei által létrehozott Helm-diagramokon: a `values.dev.yaml` fájlban, és közvetlenül a `azds.yaml`. A titkok tárolása nem ajánlott `values.yaml`ban. A jelen cikkben definiált ügyféleszközök által létrehozott Helm-diagramok két megközelítésén kívül, ha létrehoz egy saját Helm-diagramot, a Helm diagramot közvetlenül is használhatja a titkok kezeléséhez és tárolásához.

## <a name="method-1-valuesdevyaml"></a>1\. módszer: Values. dev. YAML
1. Nyissa meg a VS Code-ot a projekttel, amely engedélyezve van az Azure dev Spaces szolgáltatásban.
2. Adjon hozzá egy _Values. dev. YAML_ nevű fájlt ugyanabban a mappában, mint a meglévő _azds. YAML_ , és adja meg a titkos kulcsot és az értékeket, ahogy az alábbi példában látható:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. a _azds. YAML_ már a _Values. dev. YAML_ fájlra hivatkozik, ha létezik. Ha más fájlnevet szeretne, frissítse a install. Values szakaszt:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Módosítsa a programkódot úgy, hogy a következő példához hasonlóan környezeti változókként tekintse át ezeket a titkokat:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Frissítse a fürtön futó szolgáltatásokat ezekkel a módosításokkal. A parancssorban futtassa a következő parancsot:

    ```
    azds up
    ```
 
6. Választható A parancssorban győződjön meg arról, hogy a következő titkok lettek létrehozva:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Győződjön meg arról, hogy a _. gitignore_ fájlhoz adja hozzá a _Values. dev. YAML_ fájlt, hogy elkerülje a titkos kódok véglegesítését a verziókövetés során.
 
 
## <a name="method-2-azdsyaml"></a>2\. módszer: azds. YAML
1.  A _azds. YAML_szakaszban állítsa be a titkokat a YAML szakasz konfigurációk/fejlesztés/telepítés területén. Bár a titkos értékeket közvetlenül is megadhatja, nem ajánlott, mert a _azds. YAML_ be van jelölve a verziókövetésba. Ehelyett adja hozzá a helyőrzőket a "$PLACEHOLDER" szintaxis használatával.

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
     
2.  Hozzon létre egy _. env_ fájlt ugyanabban a mappában, mint a _azds. YAML_. Adja meg a titkokat a standard Key = Value jelöléssel. Ne véglegesítse a _. env_ fájlt a verziókövetés számára. (A git-alapú verziókövetés rendszerből való kilépéshez vegye fel azt a _. gitignore_ fájlba.) A következő példa egy _. env_ fájlt mutat be:

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Módosítsa a szolgáltatás forráskódját úgy, hogy az a kódban szereplő titkos kódokra hivatkozzon, ahogy az alábbi példában is látható:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Frissítse a fürtön futó szolgáltatásokat ezekkel a módosításokkal. A parancssorban futtassa a következő parancsot:

    ```
    azds up
    ```

4.  választható A kubectl titkainak megtekintése:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="passing-secrets-as-build-arguments"></a>Titkok átadása Build argumentumként

Az előző fejezetek azt mutatták be, hogyan lehet átadni a titkokat a tároló futási idején. Egy titkos kulcsot is átadhat a tároló felépítési idejére, például egy privát NuGet tartozó jelszót `azds.yaml`használatával.

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
 

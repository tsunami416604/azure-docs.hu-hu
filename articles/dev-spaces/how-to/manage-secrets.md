---
title: Titkos kulcsok kezelése, az egy Azure-fejlesztési terület használatakor
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Container Service, tárolók
ms.openlocfilehash: 900529d54a26729d9d0fb949d9217d5e2d618254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66515290"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Titkos kulcsok kezelése, az egy Azure-fejlesztési terület használatakor

A szolgáltatások szükség lehet bizonyos jelszavak, a kapcsolati karakterláncok és egyéb titkos adatait, például adatbázisok vagy más biztonságos Azure-szolgáltatásokkal. Konfigurációs fájlok titkos adatokat értékének beállításával, elérhetővé teheti őket a kód a környezeti változókként.  Ezek a titkos kódok biztonságának fenyegetése elkerülése érdekében óvatosan kell kezelni.

Az Azure fejlesztési tárolóhelyek lehetőségeket kínál a két ajánlott módszer, titkos kódok tárolása az Azure fejlesztési tárolóhelyek ügyfél eszközkészlet által létrehozott Helm-diagramok: a values.dev.yaml fájlt, és közvetlenül a azds.yaml beágyazott. Titkos kódok tárolása values.yaml nem ajánlott. A két megközelítés használatos Helm-en kívül hibakeresését az ügyfél által generált diagramok definiált ebben a cikkben létrehozásakor a saját Helm-diagramot, a Helm-diagramot használhatja közvetlenül a kezelésére és titkos kulcsok tárolására.

## <a name="method-1-valuesdevyaml"></a>1\. módszer: values.dev.yaml
1. Nyissa meg a VS Code a projekthez, amely az Azure fejlesztési tárolóhelyek engedélyezve van.
2. Adjon hozzá egy fájlt _values.dev.yaml_ ugyanabban a mappában, mint a meglévő _azds.yaml_ és határozza meg a titkos kulcsot és az értékeket, az alábbi példában látható módon:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds.yaml_ már hivatkozik a _values.dev.yaml_ fájlt, ha az már létezik. Ha inkább egy másik fájlnevet, frissítse a install.values szakaszban:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Módosítsa a szolgáltatás kód a titkos adatokat hivatkoznak környezeti változókként, az alábbi példában látható módon:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Ezeket a módosításokat a fürtben futó szolgáltatás frissítése. A parancssorban futtassa a parancsot:

    ```
    azds up
    ```
 
6. (Nem kötelező) A parancssorból ellenőrizze, hogy létrejöttek-e a titkos adatokat:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Győződjön meg arról, hogy hozzáadhat _values.dev.yaml_ , a _.gitignore_ fájl verziókövetési rendszerben titkos kódok véglegesítését elkerülése érdekében.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>2\. módszer: Közvetlenül a azds.yaml beágyazott
1.  A _azds.yaml_, állítsa be a titkos kulcsokat a yaml szakasz konfigurációk/fejlesztés/telepítés alatt. Bár erre elméletileg titkos kulcs értékeket közvetlenül, ez nem ajánlott, mert _azds.yaml_ forrásvezérlőben be van jelölve. Ehelyett adja hozzá a helyőrzőket a "$PLACEHOLDER" szintaxis használatával.

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
     
2.  Hozzon létre egy _.env_ ugyanabban a mappában található fájl _azds.yaml_. Adja meg a titkok standard kulcs = érték jelöléssel. Nem véglegesíteni a _.env_ verziókövetés-fájlt. (Hagyja ki a forráskezelőből git-alapú verziókövető rendszereket, adja hozzá a az _.gitignore_ fájl.) A következő példa bemutatja egy _.env_ fájlt:

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Módosítsa a szolgáltatás forráskód való hivatkozáshoz a titkos kód, a következő példához hasonlóan az adatokat:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Ezeket a módosításokat a fürtben futó szolgáltatás frissítése. A parancssorban futtassa a parancsot:

    ```
    azds up
    ```

4.  (nem kötelező) A kubectl nézet titkos kódok:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>További lépések

A következő módszerekkel, most már biztonságosan kapcsolódhat egy adatbázist, az Azure Cache redis, vagy biztonságos Azure-szolgáltatások eléréséhez.
 

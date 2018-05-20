---
title: Titkos kulcsok kezelése az Azure fejlesztői címtér az használatakor |} Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
ms.technology: azds-kubernetes
description: Gyors Kubernetes fejlesztése a tárolók és mikroszolgáltatások létrehozására az Azure-on
keywords: Docker, Kubernetes, Azure, AKS, az Azure Tárolószolgáltatás, tárolók
manager: douge
ms.openlocfilehash: b77d862f578ddc374dbb58117b4ea58eb973e5fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Titkos kulcsok kezelése az Azure fejlesztői címtér az használatakor

A szolgáltatások szükség lehet egyes jelszavak, kapcsolati karakterláncok, és más titkos adatokat, például az adatbázisok vagy más biztonságos Azure-szolgáltatásokkal. Ezeknek a kulcsoknak értékének beállításával konfigurációs fájlokat, akkor is használhatja őket a kódban környezeti változóként.  Ezek a titkos kulcsok biztonságának fenyegetése elkerülése érdekében óvatosan kell kezelni.

Azure fejlesztői szóközöket biztosít két ajánlott, hogy a titkos kulcsok tárolására szolgáló beállítások: a values.dev.yaml fájlban, és közvetlenül a azds.yaml beágyazott. A titkos kulcsok tárolása values.yaml nem javasolt.
 
## <a name="method-1-valuesdevyaml"></a>1. módszer: values.dev.yaml
1. Nyissa meg a Visual STUDIO Code a projektet a Azure fejlesztői szóközöket engedélyezett.
2. Adjon hozzá nevű fájlt _values.dev.yaml_ ugyanabban a mappában található, mint a meglévő _values.yaml_ , és adja meg a titkos kulcs és az értékeket, az alábbi példában látható módon:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. Frissítés _azds.yaml_ állapítható meg, hogy Azure fejlesztői tárolóhelyekkel szeretné használni az új _values.dev.yaml_ fájlt. Ehhez adja hozzá ezt a konfigurációt a configurations.develop.container szakaszban:

    ```yaml
           container:
             values:
             - "charts/webfrontend/values.dev.yaml"
    ```
 
4. Módosítsa a szolgáltatáskód hibáit hivatkozni ezeknek a kulcsoknak környezeti változóként, az alábbi példában látható módon:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Az ezekkel a módosításokkal a fürtben működő szolgáltatások frissítésével. A parancssorban futtassa a parancsot:

    ```
    azds up
    ```
 
6. (Választható) A parancssorból ellenőrizze, hogy ezeknek a kulcsoknak létrejöttek:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Győződjön meg arról, hogy hozzáadta _values.dev.yaml_ számára a _.gitignore_ fájl titkos kulcsainak verziókezelő véglegesítése elkerülése érdekében.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>2. módszer: Beágyazott közvetlenül a azds.yaml
1.  A _azds.yaml_, állítsa be a a yam szakasz konfigurációk/fejlesztése/telepítés titkos kulcsok. Bár adhatja meg a titkos kulcs értékeket közvetlenül nincs, akkor nem ajánlott, mert _azds.yaml_ forrás vezérlőbe be van jelölve. Ehelyett adja hozzá a helyőrzők a "$PLACEHOLDER" szintaxis használatával.

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT_DEV"
                host: "$REDIS_HOST_DEV"
                key: "$REDIS_KEY_DEV"
    ```
     
2.  Hozzon létre egy _.env_ azonos mappában lévő fájl _azds.yaml_. Adja meg a titkos kulccsal szabványos = érték jelöléssel. Nem véglegesíthető a _.env_ fájl a verziókövetési rendszerrel. (Nincs megadva a verziókövetésből git-alapú rendszerek, vegye fel azt a _.gitignore_ fájl.) Az alábbi példa mutatja egy _.env_ fájlt:

    ```
    REDIS_PORT_DEV=3333
    REDIS_HOST_DEV=myredishost
    REDIS_KEY_DEV=myrediskey
    ```
2.  Módosítsa a szolgáltatás forráskódot, ezeknek a kulcsoknak a kódban, az alábbi példában látható módon hivatkozni:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Az ezekkel a módosításokkal a fürtben működő szolgáltatások frissítésével. A parancssorban futtassa a parancsot:

    ```
    azds up
    ```

4.  (választható) A kubectl titkok megtekintése:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>További lépések

Az ezekkel a módszerekkel most már biztonságosan csatlakozhat egy adatbázist, a Redis gyorsítótár vagy hozzáférés biztonságos Azure-szolgáltatásokhoz.
 
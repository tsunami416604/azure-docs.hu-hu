---
title: A TLS 1,0-es és 1,1-es verziójának eltávolítása az Azure cache használatával a Redis-hez
description: Ismerje meg, hogyan távolíthatja el a TLS 1,0 és 1,1 alkalmazást az alkalmazásból az Azure cache Redis való kommunikációja során
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: efb9e8b8abdcb442e2c5c4d8bfd1b2e1e60865ce
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197852"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>A TLS 1,0-es és 1,1-es verziójának eltávolítása az Azure cache használatával a Redis-hez

A Transport Layer Security (TLS) 1,2-es vagy újabb verziójának kizárólagos használatára az egész iparágra kiterjedő leküldés. A 1,0-es és 1,1-as TLS-verziók ismertek olyan támadásokra, mint például a BEAST és az USZKÁR, valamint más gyakori biztonsági rések és a kitettségek (CVE) gyengeségei. Emellett nem támogatják a Payment Card Industry (PCI) megfelelőségi szabványai által ajánlott modern titkosítási módszereket és titkosítási csomagokat. Ez a [TLS biztonsági blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) részletesebben ismerteti ezeket a biztonsági réseket.

Ennek a tevékenységnek a részeként a következő módosításokat hajtjuk végre az Azure cache Redis:

* **1. fázis:** Az újonnan létrehozott gyorsítótár-példányok esetében az alapértelmezett minimális TLS-verziót 1,2-re (korábban TLS 1,0) konfiguráljuk.  Ezen a ponton nem frissülnek a meglévő gyorsítótár-példányok. Ha szükséges, [megváltoztathatja a TLS minimális verzióját](cache-configure.md#access-ports) 1,0-re vagy 1,1-ra a visszamenőleges kompatibilitás érdekében. Ezt a változást a Azure Portal vagy más felügyeleti API-k segítségével teheti meg.
* **2. fázis:** A TLS 1,0-es és 1,1-es verziójának támogatása nem áll le. A módosítás után az alkalmazás a TLS 1,2-es vagy újabb verzióját fogja használni a gyorsítótárral való kommunikációhoz.

Emellett a változás részeként eltávolítja a régebbi, nem biztonságos Cypher-csomagok támogatását.  A támogatott Cypher-csomagok a következőre lesznek korlátozva, ha a gyorsítótár a 1,2-es minimális TLS-verzióval van konfigurálva.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Ez a cikk általános útmutatást nyújt a korábbi TLS-verziók függőségeinek észleléséhez és az alkalmazásból való eltávolításához.

A módosítások érvénybe léptetésének dátuma:

| Felhő               | 1. fázis kezdési dátuma | 2. fázis kezdő dátuma      |
|---------------------|--------------------|-------------------------|
| Azure (globális)      |  2020. január 13.  | Május 11., 2020            |
| Azure Government    |  Március 13., 2020    | Május 11., 2020            |
| Azure Germany       |  Március 13., 2020    | Május 11., 2020            |
| Azure China         |  Március 13., 2020    | Május 11., 2020            |

## <a name="check-whether-your-application-is-already-compliant"></a>Annak ellenőrzését, hogy az alkalmazás már megfelelő-e

A legkönnyebben megtudhatja, hogy az alkalmazás a TLS 1,2-mel működik-e, ha a TLS- **verzió minimális** értékét a TLS 1,2 értékre állítja egy olyan tesztelési vagy előkészítési gyorsítótárban, amelyet használ. A **TLS minimális verziója** beállítás a Azure Portal gyorsítótár-példányának [speciális beállításaiban](cache-configure.md#advanced-settings) található. Ha az alkalmazás a változás után is továbbra is a várt módon működik, valószínűleg megfelelőnek kell lennie. Előfordulhat, hogy az alkalmazás által használt egyes Redis-kódtárakat külön kell konfigurálnia, hogy engedélyezze a TLS 1,2-et, hogy az adott biztonsági protokollon keresztül Redis az Azure cache-hez.

## <a name="configure-your-application-to-use-tls-12"></a>Az alkalmazás konfigurálása a TLS 1,2 használatára

A legtöbb alkalmazás Redis-kódtárakat használ a gyorsítótárral való kommunikáció kezelésére. Az alábbiakban útmutatást talál néhány, a különböző programozási nyelveken és keretrendszerekben található népszerű ügyféloldali függvénytár konfigurálásához a TLS 1,2 használatához.

### <a name="net-framework"></a>.NET-keretrendszer

A Redis .NET-ügyfelek alapértelmezés szerint a legkorábbi TLS-verziót használják a .NET-keretrendszer 4.5.2-es vagy korábbi verziójában, és a legújabb TLS-verziót használják a .NET-keretrendszer 4,6-es vagy újabb verziójára Ha a .NET-keretrendszer régebbi verzióját használja, a TLS 1,2-et manuálisan is engedélyezheti:

* **StackExchange. Redis:** Állítsa `ssl=true` be `sslprotocols=tls12` a és a karakterláncot a kapcsolatok karakterláncában.
* **ServiceStack. Redis:** Kövesse a [ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) utasításokat, és a ServiceStack. Redis v 5.6 minimálisan szükséges.

### <a name="net-core"></a>.NET Core

A Redis .NET Core-ügyfelek alapértelmezés szerint a legújabb TLS-verziót használják.

### <a name="java"></a>Java

A Redis Java-ügyfelek TLS 1,0-et használnak a Java 6-os vagy korábbi verzióján. A jedis, a saláta és a Redisson nem tud csatlakozni az Azure cache-hez a Redis, ha a TLS 1,0 le van tiltva a gyorsítótárban. A Java-keretrendszer frissítése új TLS-verziók használatára.

A Java 7 esetében a Redis-ügyfelek alapértelmezés szerint nem használják a TLS 1,2-et, de konfigurálható rá. A jedis segítségével megadhatja a mögöttes TLS-beállításokat a következő kódrészlettel:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

A saláta és a Redisson-ügyfelek még nem támogatják a TLS-verzió megadását, ezért a gyorsítótár csak TLS 1,2-kapcsolatokat fogad el. Az ügyfelekre vonatkozó javítások felülvizsgálata folyamatban van, ezért ezeket a csomagokat egy frissített verzióra kell ellenőrizni.

A Java 8 esetében a TLS 1,2 alapértelmezés szerint használatos, és a legtöbb esetben nem szükséges frissíteni az ügyfél konfigurációját. A biztonság érdekében tesztelje az alkalmazást.

### <a name="nodejs"></a>Node.js

A Node Redis és a IORedis alapértelmezés szerint a TLS 1,2-et használja.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* PHP 7-es verziónál korábbi verziók: a Predis csak a TLS 1,0-et támogatja. Ezek a verziók nem működnek a TLS 1,2; a TLS 1,2 használatára kell frissítenie.
 
* PHP 7,0 – PHP 7.2.1: a Predis alapértelmezés szerint csak a TLS 1,0 vagy 1,1 protokollt használja. A TLS 1,2 használatát a következő megkerülő megoldással végezheti el. A TLS 1,2 megadása az ügyfél példányának létrehozásakor:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7,3 és újabb verziók: a Predis a legújabb TLS-verziót használja.

#### <a name="phpredis"></a>PhpRedis

A PhpRedis nem támogatja a TLS használatát bármilyen PHP-verzióban.

### <a name="python"></a>Python

A Redis-másolási szolgáltatás alapértelmezés szerint a TLS 1,2 protokollt használja.

### <a name="go"></a>GO

A Redigo alapértelmezés szerint a TLS 1,2-et használja.

## <a name="additional-information"></a>További információ

- [Az Azure cache konfigurálása a Redis-hez](cache-configure.md)

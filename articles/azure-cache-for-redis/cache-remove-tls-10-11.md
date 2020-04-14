---
title: A TLS 1.0 és 1.1 eltávolítása az Azure Cache for Redis használatával
description: Ismerje meg, hogyan távolíthatja el a TLS 1.0-s és 1.1-es állapotát az alkalmazásból, amikor az Azure Cache for Redis-szel kommunikál
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 809fbe85a9783777d5dbef86357bd5a386bd6f81
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261239"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>A TLS 1.0 és 1.1 eltávolítása az Azure Cache for Redis használatával

A Transport Layer Security (TLS) 1.2-es vagy újabb verziójának kizárólagos használata felé halad unk. A TLS 1.0-s és 1.1-es verziói róluk ismert, hogy ki vannak téve az olyan támadásoknak, mint a BEAST és az USZS, és más gyakori sebezhetőségi és kitettségi (CVE) hiányosságokkal rendelkeznek. Nem támogatják a Fizetésikártya-ipar (PCI) megfelelőségi szabványai által ajánlott modern titkosítási módszereket és titkosítási csomagokat sem. Ez [a TLS biztonsági blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) részletesebben ismerteti a biztonsági rések némelyikét.

Ennek részeként a következő módosításokat hajtjuk végre a Redis-hez rendelt Azure Cache-ben:

* **1. fázis:** Az újonnan létrehozott gyorsítótárpéldányok esetében az alapértelmezett minimális TLS-verzió 1.2-es lesz. (Ez korábban TLS 1.0 volt.) A meglévő gyorsítótár-példányok ezen a ponton nem frissülnek. Szükség esetén a visszamenőleges kompatibilitás érdekében a [minimális TLS-verziót](cache-configure.md#access-ports) 1.0-ra vagy 1.1-re módosíthatja. Ez a módosítás az Azure Portalon vagy más felügyeleti API-kon keresztül hajtható el.
* **2. fázis:** Nem támogatjuk tovább a TLS 1.0-s és 1.1-es verzióját. A módosítás után az alkalmazásnak a TLS 1.2-es vagy újabb használatát kell használnia a gyorsítótárral való kommunikációhoz.

Ezenkívül a módosítás részeként eltávolítjuk a régebbi, nem biztonságos cypher lakosztályok támogatását.  Támogatott cypher csomagjaink a következőkre lesznek korlátozva, ha a gyorsítótár 1.2-es minimális TLS-verziójával van konfigurálva.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Ez a cikk általános útmutatást nyújt a korábbi TLS-verziókfüggőségek észleléséhez és az alkalmazásból való eltávolításához.

A módosítások hatálybalépésének dátumai a következők:

| Felhő               | 1. fázis Kezdési dátum | 2. fázis Kezdési dátum      |
|---------------------|--------------------|-------------------------|
| Azure (globális)      |  2020. január 13.  | 2020. május 11. |
| Azure Government    |  2020. március 13.    | 2020. május 11.            |
| Azure Germany       |  2020. március 13.    | 2020. május 11.            |
| Azure China         |  2020. március 13.    | 2020. május 11.            |

## <a name="check-whether-your-application-is-already-compliant"></a>Annak ellenőrzése, hogy az alkalmazás már megfelel-e a követelményeknek

A legegyszerűbb módja annak, hogy megtudja, hogy az alkalmazás működni fog-e a TLS 1.2-vel, ha a **Minimális TLS verzióértéket** TLS 1.2-re állítja egy teszt- vagy átmeneti gyorsítótárban, amelyet használ. A **Minimális TLS-verzióbeállítás** a gyorsítótár-példány [speciális beállításaiban](cache-configure.md#advanced-settings) található az Azure Portalon. Ha az alkalmazás továbbra is a várt módon működik a módosítás után, akkor valószínűleg megfelelő. Előfordulhat, hogy konfigurálnia kell néhány Redis ügyfélkönyvtárat, amelyet az alkalmazás kifejezetten a TLS 1.2 engedélyezéséhez használ, hogy az adott biztonsági protokollon keresztül csatlakozhassanak az Azure Cache for Redis-hez.

## <a name="configure-your-application-to-use-tls-12"></a>Az alkalmazás konfigurálása a TLS 1.2 használatára

A legtöbb alkalmazás Redis ügyfélkódtárakat használ a gyorsítótárakkal való kommunikáció kezelésére. Az alábbiakban a népszerű ügyfélkódtárak különböző programozási nyelveken és keretrendszerekben a TLS 1.2 használatára vonatkozó beállítására vonatkozó utasításokat olvashatja.

### <a name="net-framework"></a>.NET-keretrendszer

A Redis .NET ügyfelek alapértelmezés szerint a legkorábbi TLS-verziót használják a .NET Framework 4.5.2-es vagy korábbi verzióján, és a .NET Framework 4.6-os vagy újabb verzióján a legújabb TLS-verziót használják. Ha a .NET Framework régebbi verzióját használja, manuálisan engedélyezheti a TLS 1.2-t:

* **StackExchange.Redis:** Állítsa `ssl=true` `sslprotocols=tls12` be, és a kapcsolat ihúr.
* **ServiceStack.Redis:** Kövesse a [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) utasításokat, és legalább a ServiceStack.Redis 5.6-os szükséges.

### <a name="net-core"></a>.NET Core

A Redis .NET Core ügyfelek alapértelmezés szerint a legújabb TLS-verziót használják.

### <a name="java"></a>Java

A Redis Java-ügyfelek a TLS 1.0-s verzióját használják a Java 6-os vagy korábbi verzióján. Jedik, saláta és Redisson nem tud csatlakozni az Azure Cache for Redis, ha a TLS 1.0 le van tiltva a gyorsítótárban. Frissítse a Java-keretrendszert az új TLS-verziók használatához.

Java 7 esetén a Redis-ügyfelek alapértelmezés szerint nem használják a TLS 1.2-t, de konfigurálhatók hozzá. A Jedik lehetővé teszik az alapul szolgáló TLS-beállítások megadását a következő kódrészlettel:

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

A saláta és a Redisson ügyfelek még nem támogatják a TLS-verzió megadását, így megszakadnak, ha a gyorsítótár csak TLS 1.2-es kapcsolatokat fogad el. Ezen ügyfelek javításait ellenőrizzük, ezért ellenőrizze ezeket a csomagokat egy frissített verzióhoz ezzel a támogatással.

A Java 8-ban a TLS 1.2 alapértelmezés szerint használatos, és a legtöbb esetben nem szükséges az ügyfélkonfiguráció frissítése. A biztonság kedvéért tesztelje az alkalmazást.

### <a name="nodejs"></a>Node.js

A Redis csomópont és az IORedis alapértelmezés szerint a TLS 1.2-t használja.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis között
 
* A PHP 7-nél korábbi verziók: A Predis csak a TLS 1.0-t támogatja. Ezek a verziók nem működnek a TLS 1.2- vel; frissítenie kell a TLS 1.2 használatához.
 
* PHP 7.0-tól PHP 7.2.1-ig: A Predis alapértelmezés szerint csak a TLS 1.0-t vagy az 1.1-et használja. A TLS 1.2-es használata a következő kerülő megoldás. Az ügyfélpéldány létrehozásakor adja meg a TLS 1.2 értéket:

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

* PHP 7.3 és újabb verziók: A Predis a legújabb TLS verziót használja.

#### <a name="phpredis"></a>PhpRedis között

A PhpRedis nem támogatja a TLS-t egyetlen PHP verzióban sem.

### <a name="python"></a>Python

A Redis-py alapértelmezés szerint a TLS 1.2-t használja.

### <a name="go"></a>GO

A Redigo alapértelmezés szerint a TLS 1.2-t használja.

## <a name="additional-information"></a>További információ

- [Az Azure Cache beállítása a Redis-hez](cache-configure.md)

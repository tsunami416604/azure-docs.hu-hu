---
title: A TLS 1,0 és 1,1 használatának eltávolítása az Azure cache for Redis | Microsoft Docs
description: Ismerje meg, hogyan távolíthatja el a TLS 1,0 és 1,1 alkalmazást az alkalmazásból az Azure cache Redis való kommunikációja során
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901897"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>A TLS 1,0 és 1,1 használatának eltávolítása az Azure cache Redis

Az egész iparágra kiterjedő leküldés kizárólag a TLS 1,2 vagy újabb verzióra. A 1,0-es és 1,1-as TLS-verziók ismertek olyan támadásokra, mint például a BEAST és az USZKÁR, és más gyakori biztonsági rések és a kitettségek (CVE) gyengeségei vannak. Emellett nem támogatják a PCI-megfelelőségi szabványok által ajánlott modern titkosítási módszereket és titkosítási csomagokat. Ez a [TLS biztonsági blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) további részletekben ismerteti ezeket a biztonsági réseket.

Habár ezek egyike sem jelent azonnali problémát, érdemes lehet a TLS 1,0 és a 1,1 használatát a lehető leghamarabb áthelyezni. A Redis-hez készült Azure cache az 2020-es március 31-én kezdődően leállítja a TLS-verziók támogatását. Az alkalmazásnak legalább TLS 1,2-et kell használnia ahhoz, hogy ezt a dátumot követően kommunikáljon a gyorsítótárral.

Ez a cikk általános útmutatást nyújt a függőségek alkalmazásból való észleléséhez és eltávolításához.

## <a name="check-if-your-application-is-already-compliant"></a>Ellenőrizze, hogy az alkalmazás már megfelelő-e

A legegyszerűbb módszer annak megállapítására, hogy az alkalmazás a TLS 1,2-mel működik-e, ha a minimális TLS-verziót szeretné beállítani tesztelési vagy előkészítési gyorsítótárban, amelyet a TLS 1,2-re használ. A TLS minimális verziószámát a Azure Portal gyorsítótár-példányának [speciális beállításainál](cache-configure.md#advanced-settings) találja. Ha az alkalmazás a változás után is továbbra is a várt módon működik, valószínűleg megfelelőnek kell lennie. Előfordulhat, hogy az alkalmazás egyes Redis-kódtárait kifejezetten úgy kell konfigurálni, hogy engedélyezzék a TLS 1,2 használatát ahhoz, hogy az adott biztonsági protokollon keresztül Redis az Azure cache-hez.

## <a name="configure-your-application-to-use-tls-12"></a>Az alkalmazás konfigurálása a TLS 1,2 használatára

A legtöbb alkalmazás Redis használ a gyorsítótárral való kommunikáció kezelésére. Az alábbiakban megtudhatja, hogyan konfigurálhat néhány népszerű ügyféloldali kódtárat különböző programozási nyelveken és keretrendszereken a TLS 1,2 használatához.

### <a name="net-framework"></a>.NET-keretrendszer

A Redis .NET-ügyfelek alapértelmezés szerint a legalacsonyabb TLS-verziót használják a .NET-keretrendszer 4.5.2-es vagy újabb verziójában, valamint a legmagasabb TLS-verziót 4,6-es vagy újabb verzióban Ha a .NET-keretrendszer régebbi verzióját használja, a TLS 1,2-et manuálisan is engedélyezheti:

* StackExchange. Redis: a (z) `ssl=true` és `sslprotocls=tls12` beállítása a kapcsolatok karakterláncában.
* ServiceStack. Redis: kövesse [ezeket az utasításokat](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

A Redis .NET Core-ügyfelek alapértelmezés szerint a legmagasabb TLS-verziót használják.

### <a name="java"></a>Java

A Redis Java-ügyfelek TLS 1,0-et használnak a Java 6-os vagy újabb verziójában. A jedis, a saláta és a Radisson nem tud csatlakozni az Azure cache-hez a Redis, ha a TLS 1,0 le van tiltva a gyorsítótárban. Jelenleg nincs ismert Áthidaló megoldás.

A Java 7-es vagy újabb verziójában a Redis-ügyfelek alapértelmezés szerint nem használják a TLS 1,2-et, de lehet rá konfigurálva. A saláta és a Radisson jelenleg nem támogatja ezt a jogot. Ha a gyorsítótár csak TLS 1,2-kapcsolatokat fogad el, a rendszer megtöri. A jedis segítségével megadhatja a mögöttes TLS-beállításokat a következő kódrészlettel:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

A Node Redis és a IORedis alapértelmezés szerint a TLS 1,2-et használja.

### <a name="php"></a>PHP

A PHP 7 Predis nem fog működni, mivel az utóbbi csak a TLS 1,0-et támogatja. A Predis PHP-7.2.1 vagy az alatta alapértelmezés szerint a TLS 1,0 vagy a 1,1 protokollt használja. A TLS 1,2-et a-ügyfél példányának létrehozásakor adhatja meg:

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

A PHP 7,3-es vagy újabb verzióiban a Predis a legújabb TLS-verziót használja.

A PhpRedis nem támogatja a TLS használatát bármilyen PHP-verzióban.

### <a name="python"></a>Python

A Redis-másolási szolgáltatás alapértelmezés szerint a TLS 1,2 protokollt használja.

### <a name="go"></a>GO

A Redigo alapértelmezés szerint a TLS 1,2-et használja.

## <a name="additional-information"></a>További információk

- [Az Azure cache konfigurálása a Redis-hez](cache-configure.md)

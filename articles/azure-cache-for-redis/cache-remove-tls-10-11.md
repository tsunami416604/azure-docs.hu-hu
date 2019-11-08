---
title: A TLS 1,0 és 1,1 eltávolítása az Azure cache használatával a Redis-hez | Microsoft Docs
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
ms.openlocfilehash: 638ce4b07201453d0bb00d3610cb695b72b6d698
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806755"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>A TLS 1,0-es és 1,1-es verziójának eltávolítása az Azure cache használatával a Redis-hez

A Transport Layer Security (TLS) 1,2-es vagy újabb verziójának kizárólagos használatára az egész iparágra kiterjedő leküldés. A 1,0-es és 1,1-as TLS-verziók ismertek olyan támadásokra, mint például a BEAST és az USZKÁR, valamint más gyakori biztonsági rések és a kitettségek (CVE) gyengeségei. Emellett nem támogatják a Payment Card Industry (PCI) megfelelőségi szabványai által ajánlott modern titkosítási módszereket és titkosítási csomagokat. Ez a [TLS biztonsági blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) részletesebben ismerteti ezeket a biztonsági réseket.

Habár a fenti megfontolások egyike sem jelent azonnali problémát, javasoljuk, hogy hamarosan állítsa le a TLS 1,0 és a 1,1 használatát. A Redis-hez készült Azure cache a 2020-es március 31-én leállítja a TLS-verziók támogatását. Ezt követően az alkalmazás a TLS 1,2-es vagy újabb verzióját fogja használni a gyorsítótárral való kommunikációhoz.

Ez a cikk általános útmutatást nyújt a korábbi TLS-verziók függőségeinek észleléséhez és az alkalmazásból való eltávolításához.

## <a name="check-whether-your-application-is-already-compliant"></a>Annak ellenőrzését, hogy az alkalmazás már megfelelő-e

A legkönnyebben megtudhatja, hogy az alkalmazás a TLS 1,2-mel működik-e, ha a TLS- **verzió minimális** értékét a TLS 1,2 értékre állítja egy olyan tesztelési vagy előkészítési gyorsítótárban, amelyet használ. A **TLS minimális verziója** beállítás a Azure Portal gyorsítótár-példányának [speciális beállításaiban](cache-configure.md#advanced-settings) található. Ha az alkalmazás a változás után is továbbra is a várt módon működik, valószínűleg megfelelőnek kell lennie. Előfordulhat, hogy az alkalmazás által használt egyes Redis-kódtárakat külön kell konfigurálnia, hogy engedélyezze a TLS 1,2-et, hogy az adott biztonsági protokollon keresztül Redis az Azure cache-hez.

## <a name="configure-your-application-to-use-tls-12"></a>Az alkalmazás konfigurálása a TLS 1,2 használatára

A legtöbb alkalmazás Redis-kódtárakat használ a gyorsítótárral való kommunikáció kezelésére. Az alábbiakban útmutatást talál néhány, a különböző programozási nyelveken és keretrendszerekben található népszerű ügyféloldali függvénytár konfigurálásához a TLS 1,2 használatához.

### <a name="net-framework"></a>.NET-keretrendszer

A Redis .NET-ügyfelek alapértelmezés szerint a legkorábbi TLS-verziót használják a .NET-keretrendszer 4.5.2-es vagy korábbi verziójában, és a legújabb TLS-verziót használják a .NET-keretrendszer 4,6-es vagy újabb verziójára Ha a .NET-keretrendszer régebbi verzióját használja, a TLS 1,2-et manuálisan is engedélyezheti:

* **StackExchange. Redis:** `ssl=true` és `sslprotocls=tls12` beállítása a kapcsolatok karakterláncában.
* **ServiceStack. Redis:** Kövesse a [ServiceStack. Redis utasításait](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

A Redis .NET Core-ügyfelek alapértelmezés szerint a legújabb TLS-verziót használják.

### <a name="java"></a>Java

A Redis Java-ügyfelek TLS 1,0-et használnak a Java 6-os vagy korábbi verzióján. A jedis, a saláta és a Radisson nem tud csatlakozni az Azure cache-hez a Redis, ha a TLS 1,0 le van tiltva a gyorsítótárban. Jelenleg nincs ismert Áthidaló megoldás.

A Java 7-es vagy újabb verzióiban a Redis-ügyfelek alapértelmezés szerint nem használják a TLS 1,2-et, de konfigurálható rá. A saláta és a Radisson jelenleg nem támogatja ezt a konfigurációt. Akkor törnek, ha a gyorsítótár csak TLS 1,2-kapcsolatokat fogad el. A jedis segítségével megadhatja a mögöttes TLS-beállításokat a következő kódrészlettel:

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

A PHP 7 Predis nem fog működni, mert a PHP 7 csak a TLS 1,0-et támogatja. A Predis a PHP 7.2.1 vagy korábbi verzióiban alapértelmezés szerint a TLS 1,0 vagy a 1,1 protokollt használja. Az ügyfél-példány létrehozásakor megadhatja a TLS 1,2-et:

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

## <a name="additional-information"></a>További információ

- [Az Azure cache konfigurálása a Redis-hez](cache-configure.md)

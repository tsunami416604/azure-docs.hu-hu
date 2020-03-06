---
title: Linuxos OpenSSL konfigurálása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan konfigurálhatja az OpenSSL-t a Linux rendszerhez.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331144"
---
# <a name="configure-openssl-for-linux"></a>Linuxos OpenSSL konfigurálása

Ha bármilyen Speech SDK-verziót használ a 1.9.0 előtt, az [OpenSSL](https://www.openssl.org) dinamikusan van konfigurálva a gazda-rendszer verzióra. A Speech SDK újabb verzióiban az OpenSSL ( [1.1.1](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)-es verzió) statikusan kapcsolódik a Speech SDK alapvető könyvtárához.

A kapcsolat biztosításához ellenőrizze, hogy az OpenSSL-tanúsítványok telepítve vannak-e a rendszerbe. Parancs futtatása:
```bash
openssl version -d
```

Az Ubuntu/Debian-alapú rendszerek kimenetének a következőnek kell lennie:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Győződjön meg arról, hogy a OPENSSLDIR alatt van-e `certs` alkönyvtár. A fenti példában `/usr/lib/ssl/certs`lenne.

* Ha `/usr/lib/ssl/certs` van, és számos különböző tanúsítványfájl tartalmaz (`.crt` vagy `.pem` kiterjesztéssel), nincs szükség további műveletekre.

* Ha a OPENSSLDIR valami más, mint `/usr/lib/ssl` és/vagy a több különálló fájl helyett egyetlen tanúsítványfájl-fájl található, be kell állítania egy megfelelő SSL környezeti változót, hogy jelezze, hol találhatók a tanúsítványok.

## <a name="examples"></a>Példák

- A OPENSSLDIR `/opt/ssl`. `certs` alkönyvtár számos `.crt`-vagy `.pem`-fájllal rendelkezik.
Állítsa be a környezeti változót úgy, hogy `/opt/ssl/certs` a Speech SDK-t használó program futtatása előtt `SSL_CERT_DIR`. Például:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- A OPENSSLDIR `/etc/pki/tls` (például RHEL/CentOS-alapú rendszereken). Van `certs` alkönyvtár egy tanúsítványfájl-fájllal, például `ca-bundle.crt`.
A Speech SDK-t használó program futtatása előtt állítsa be `SSL_CERT_FILE` környezeti változót úgy, hogy erre a fájlra mutasson. Például:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók a Speech SDK-ról](speech-sdk.md)

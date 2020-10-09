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
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683164"
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

Győződjön meg arról, hogy a OPENSSLDIR alatt van-e `certs` alkönyvtár. A fenti példában lenne `/usr/lib/ssl/certs` .

* Ha van, `/usr/lib/ssl/certs` és számos egyéni tanúsítványfájl tartalmaz ( `.crt` vagy `.pem` kiterjesztéssel), nincs szükség további műveletekre.

* Ha a OPENSSLDIR valami más, mint a `/usr/lib/ssl` és/vagy a több különálló fájl helyett egyetlen tanúsítványfájl-fájl, meg kell adnia egy megfelelő SSL környezeti változót, hogy jelezze, hol találhatók a tanúsítványok.

## <a name="examples"></a>Példák

- A OPENSSLDIR `/opt/ssl` . Van `certs` több vagy több fájllal rendelkező alkönyvtár `.crt` `.pem` .
Állítsa be a környezeti változót `SSL_CERT_DIR` úgy, hogy a `/opt/ssl/certs` Speech SDK-t használó program futtatása előtt mutasson. Például:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- A OPENSSLDIR `/etc/pki/tls` (például RHEL/CentOS-alapú rendszereken). Van `certs` alkönyvtár egy tanúsítványfájl-fájllal, például: `ca-bundle.crt` .
`SSL_CERT_FILE`A SPEECH SDK-t használó program futtatása előtt állítsa be a környezeti változót úgy, hogy az adott fájlban mutasson. Például:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Azt is érdemes megjegyezni, hogy egyes Linux-disztribúciók nem rendelkeznek meghatározott TMP vagy TMPDIR környezeti változóval. Ez azt eredményezi, hogy a Speech SDK minden alkalommal letölti a visszavont tanúsítványok listáját (CRL) ahelyett, hogy a CRL-t gyorsítótárazza a lemezre a lejáratig. A kezdeti kapcsolat teljesítményének növeléséhez [létrehozhat egy TMPDIR nevű környezeti változót, és beállíthatja a kiválasztott ideiglenes könyvtár elérési útjára.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Speech SDK ismertetése](speech-sdk.md)

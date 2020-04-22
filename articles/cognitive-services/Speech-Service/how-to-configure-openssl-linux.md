---
title: Linuxos OpenSSL konfigurálása
titleSuffix: Azure Cognitive Services
description: További információ az OpenSSL Linux ra konfigurálásáról.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683164"
---
# <a name="configure-openssl-for-linux"></a>Linuxos OpenSSL konfigurálása

Ha a Speech SDK bármely verzióját használja az 1.9.0 előtt, az [OpenSSL](https://www.openssl.org) dinamikusan van konfigurálva az állomás-rendszer verzióra. A beszédfelismerési SDK későbbi verzióiban az OpenSSL [(1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)verzió) statikusan kapcsolódik a beszédbeszéd SDK központi könyvtárához.

A kapcsolat biztosításához ellenőrizze, hogy telepítve van-e az OpenSSL-tanúsítványok a rendszerben. Parancs futtatása:
```bash
openssl version -d
```

Az Ubuntu/Debian alapú rendszerek kimenetének a következőnek kell lennie:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Ellenőrizze, hogy `certs` van-e alkönyvtár az OPENSSLDIR könyvtár alatt. A fenti példában ez `/usr/lib/ssl/certs`lenne .

* Ha van, `/usr/lib/ssl/certs` és sok egyedi tanúsítványfájlt tartalmaz (a mivel `.crt` vagy `.pem` a kiterjesztéssel), nincs szükség további műveletekre.

* Ha az OPENSSLDIR `/usr/lib/ssl` valami más, mint és/vagy több különálló fájl helyett egyetlen tanúsítványkötegfájl van, akkor be kell állítania egy megfelelő SSL környezeti változót, amely jelzi, hogy hol találhatók a tanúsítványok.

## <a name="examples"></a>Példák

- Az OPENSSLDIR a `/opt/ssl`. Van `certs` alkönyvtár sok `.crt` `.pem` vagy fájlokat.
Állítsa be `SSL_CERT_DIR` a `/opt/ssl/certs` környezeti változót úgy, hogy pontozzon a beszédfelismerési SDK-t használó program futtatása előtt. Például:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- Az OPENSSLDIR (mint az RHEL/CentOS alapú rendszereken) van. `/etc/pki/tls` Van `certs` alkönyvtár egy tanúsítványkötegfájllal, `ca-bundle.crt`például .
Állítsa be, hogy a környezeti változó `SSL_CERT_FILE` a fájlra mutasson, mielőtt olyan programot futtatna, amely a Beszéd SDK-t használja. Például:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Azt is érdemes megjegyezni, hogy egyes Linux-disztribúciók nem rendelkeznek tmp vagy TMPDIR környezeti változóval. Ez azt eredményezi, hogy a beszédfelismerési SDK minden alkalommal letölti a visszavont tanúsítványok listáját ,, ahelyett, hogy a visszavont tanúsítványok listáját lemezre gyorsítótárazná újra felhasználásként, amíg le nem járnak. A kezdeti kapcsolatteljesítmény ének javítása érdekében [létrehozhat egy TMPDIR nevű környezeti változót, és beállíthatja azt a kiválasztott ideiglenes könyvtár elérési útvonalára.](https://help.ubuntu.com/community/EnvironmentVariables). .

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Speech SDK ismertetése](speech-sdk.md)

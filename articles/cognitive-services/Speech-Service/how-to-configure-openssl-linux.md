---
title: Az OpenSSL konfigurálása Linuxra
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
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156488"
---
# <a name="configure-openssl-for-linux"></a>Az OpenSSL konfigurálása Linuxra

Ha bármilyen Speech SDK-verziót használ a 1.9.0 előtt, az [OpenSSL](https://www.openssl.org) dinamikusan van konfigurálva a gazda-rendszer verzióra. A Speech SDK újabb verzióiban az OpenSSL ( [1.1.1](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)-es verzió) statikusan kapcsolódik a Speech SDK alapvető könyvtárához.

## <a name="troubleshoot-connectivity"></a>Kapcsolatok hibaelhárítása

Ha a Speech SDK 1.9.0 kiadásának használata esetén kapcsolódási hibák merülnek fel, győződjön meg arról, hogy a `ssl/certs` könyvtár létezik a `/usr/lib` könyvtárban, amely a Linux fájlrendszerben található. Ha a `ssl/certs` könyvtár *nem létezik*, ellenőrizze, hogy az OpenSSL telepítve van-e a rendszeren a következő parancs használatával:

```bash
which openssl
```

Ezután keresse meg az OpenSSL `certs` könyvtárat, és másolja a könyvtár tartalmát a `/usr/lib/ssl/certs` könyvtárba. Ezután próbálja meg újra, hogy megoldódott-e a kapcsolódási problémák.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók a Speech SDK-ról](speech-sdk.md)
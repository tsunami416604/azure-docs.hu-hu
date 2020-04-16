---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 5ff25155529b9a763bcf5af6cfb15593e9da9853
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422185"
---
Kezelése tömörített hang hajtja végre [GStreamer](https://gstreamer.freedesktop.org). Licencelési okokból a GStreamer bináris fájljai nincsenek lefordítva és nem kapcsolódnak a beszédfelismerési SDK-hoz. A fejlesztőknek több függőséget és beépülő modult kell telepíteniük.

# <a name="ubuntu-1604-1804-or-debian-9"></a>[Ubuntu, 16.04, 18.04 vagy Debian 9](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rehl--centos"></a>[REHL / CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> A RHEL / CentOS, kövesse az utasításokat, [hogyan kell beállítani OpenSSL Linux .](../../../../how-to-configure-openssl-linux.md)

---
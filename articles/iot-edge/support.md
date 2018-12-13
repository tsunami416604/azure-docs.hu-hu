---
title: Támogatott operációs rendszerek, tároló motorok – Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg, melyik operációs rendszerek futtathatók az Azure IoT Edge-démon és a futtatókörnyezeti és az éles eszközök támogatott tároló-motorok
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3c75f50e40f83a4dc6765ae422fbb2458f40d770
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100181"
---
# <a name="azure-iot-edge-supported-systems"></a>Az Azure IoT Edge által támogatott rendszerek

Nincsenek többféle módon, hogy az Azure IoT Edge-termék támogatása.

**Hibák Reporting** – a legtöbb fejlesztési, amely az Azure IoT Edge-termék hiányzóra történik, az IoT Edge nyílt forráskódú projekt. A hibák jelenteni lehet a [problémák lap](https://github.com/azure/iotedge/issues) a projekt. Javítások gyorsan eljutnak a projektből a termékfrissítéseket.

**A Microsoft ügyfél-támogatási csapatának** – felhasználók, akik rendelkeznek egy [támogatási csomag](https://azure.microsoft.com/support/plans/) hozzon létre egy támogatási jegyet közvetlenül a Microsoft ügyfél-támogatási csapata is léphet az [az Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Kérelmek funkció** – az Azure IoT Edge-termék nyomon követi a szolgáltatással kapcsolatos kéréseit a termék keresztül [User Voice lap](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Operációs rendszerek
Az Azure IoT Edge, amelyek futtathatók tárolók; a legtöbb operációs rendszeren fut. azonban ezek mindegyikét nem egyaránt támogatottak. Operációs rendszerek számíthatnak támogatás szintjét képviselő szinten vannak csoportosítva.

### <a name="tier-1"></a>1. szint
1. rétegbeli rendszerek tekinthető, hivatalosan támogatott. Ez azt jelenti, hogy a Microsoft:
* Ezek az operációs rendszer automatizált teszteket rendelkezik
* biztosít a számukra a telepítési csomagok

Általánosan elérhető
| Operációs rendszer | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| A stretch Raspbian | Nem | Igen|
| Ubuntu Server 16.04 | Igen | Nem |
| Ubuntu Server 18.04 | Igen | Nem |

Nyilvános előzetes verzió
| Operációs rendszer | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core (2018 áprilisi frissítés) | Igen | Nem |
| A Windows 10 IoT Enterprise (2018 áprilisi frissítés) | Igen | Nem |
| A Windows 10-es kiszolgáló 1803-as verzióban | Igen | Nem |

### <a name="tier-2"></a>2. szint
2. rétegbeli rendszerek szerint kompatibilis az Azure IoT Edge segítségével olyan, és viszonylag könnyen használható. Ez azt jelenti, hogy:
* A Microsoft alkalmi tesztelése a platformon végzett, vagy tudja, hogy sikeresen rendszeren történő futtatása az Azure IoT Edge egy partner
* Más platformok telepítőcsomagok ezeken a platformokon is működhet.

| Operációs rendszer | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Igen | Igen |
| Debian 8 | Igen | Igen |
| Debian 9 | Igen | Igen |
| RHEL 7.5 | Igen | Igen |
| Ubuntu 18.04 | Igen | Igen |
| Ubuntu 16.04 | Igen | Igen |
| A szél folyó 8 | Igen | Nem |
| Yocto | Igen | Nem |

## <a name="container-engines"></a>Tároló-motorok
Az Azure IoT Edge kell egy tároló motor modulok, függetlenül attól, amelyen futó operációs rendszer elindításához. A Microsoft biztosít egy tároló motor moby-motor, ez a követelmény teljesítéséhez. A Moby nyílt forráskódú projekt alapul. A docker CE és a Docker EE más népszerű motorokkal. Ezeket is alapján a Moby megnyílik forráskódú projektje, és kompatibilis az Azure IoT Edge segítségével. A Microsoft biztosít a elérhető legjobb lehetőség támogatásával rendszerekben a tároló motorok; használatával a Microsoft azonban nem rendelkezik az LHS.Gold.Multi.B4 őket hibák javításait teszi. Ebből kifolyólag a Microsoft azt javasolja, az éles rendszereket moby-motor használatával.


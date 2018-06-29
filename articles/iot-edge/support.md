---
title: Eszközplatform-támogatás az Azure IoT peremhálózati |} Microsoft Docs
description: Azure IoT peremhálózati által támogatott platformok
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6bf918428312c511505304bd23b68cd19e46471
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036189"
---
# <a name="azure-iot-edge-support"></a>Az Azure IoT Edge-támogatás
Nincsenek többféle módon, hogy az Azure IoT peremhálózati termék támogatása.

**Hibát** – a legtöbb, amely a Azure IoT peremhálózati termék fejlesztését történik, az IoT-Edge nyílt forráskódú projektben. A hibák jelenthetők a [problémák lap](https://github.com/azure/iot-edge/issues) a projekt. Javítások gyorsan ne a projektből a termékfrissítéseket.

**A Microsoft ügyfél-támogatási csapatának** –, akik rendelkeznek a [támogatási csomag](https://azure.microsoft.com/support/plans/) is végezhetnek, amelyet a Microsoft ügyfél-támogatási csapatának közvetlenül a támogatási jegy létrehozásával a [Azure-portálon]( https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Szolgáltatás-kérelmek** – az Azure IoT peremhálózati termék frissítéseiről keresztül a termék nyomon követi az [felhasználói véleményekkel foglalkozó weblapunkra](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Operációs rendszerek
Az Azure IoT peremhálózati futtatható tárolók; a legtöbb operációs rendszeren fut azonban mindegyik nem egyaránt támogatottak. Operációs rendszerek, amelyek megfelelnek a felhasználók várható támogatás szintjének rétegek vannak csoportosítva.

### <a name="tier-1"></a>1. szint
1. rétegbeli rendszerek-re, hivatalosan támogatott. Ez azt jelenti, hogy a Microsoft:
* Ezeket az operációs rendszer van az automatikus tesztek
* a telepítőcsomagok biztosít

Általánosan elérhető
* Ubuntu 18.04
* Ubuntu 16.04
* A stretch Raspbian

Nyilvános előzetes verzió
* Windows 10-es kiszolgáló 1803
* Windows 10 IoT vállalati (április 2018 frissítéssel)
* Windows 10 IoT mag (április 2018 frissítéssel)

### <a name="tier-2"></a>2. szint
2. szintű rendszerek-re az Azure IoT él, és viszonylag könnyen használható. Ez azt jelenti, hogy:
* Microsoft végzett tesztelése a platformokon alkalmi vagy egy partner sikeresen a platformon futó Azure IoT peremhálózati tudja
* A más platformok telepítőcsomagok működnek ezek a rendszerek

Ubuntu Server 18.04

Ubuntu Server 16.04

Szél folyó 8

Yocto

Debian

Mac

## <a name="container-engines"></a>Tároló motorok
Az Azure IoT peremhálózati kell egy tároló motor modulok, függetlenül attól, amelyen futó operációs rendszer elindításához. A Microsoft biztosít egy tároló motor moby-motor, ez a követelmény teljesítésére. A Moby nyílt forráskódú projekt alapul. Docker CE és a Docker EE más népszerű tároló motorok. Azok a Moby megnyílik-forrás projektre is alapul, és Azure IoT peremhálózati kompatibilis. A Microsoft ajánlott beavatkozást támogatást biztosít az rendszerekről tároló motorok; a Microsoft azonban nem rendelkezik küldje el a probléma javítását a bennük foglalt lehetőséget. Ezért a Microsoft azt javasolja, moby-motor használata az éles rendszerek esetén.


<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 
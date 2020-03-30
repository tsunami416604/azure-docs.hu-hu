---
title: Támogatott operációs rendszerek, konténermotorok – Azure IoT Edge
description: Ismerje meg, hogy mely operációs rendszerek futtathatják az Azure IoT Edge démont és a futásidejűt, valamint a támogatott tárolómotorokat az éles eszközökhöz
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536938"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge által támogatott rendszerek

Ez a cikk részletesen ismerteti, hogy mely rendszereket és összetevőket támogatja az IoT Edge, akár hivatalosan, akár előzetes verzióban.

Ha problémákat tapasztal az Azure IoT Edge szolgáltatás használata közben, számos módja van a támogatás keresésére. Próbálja ki az alábbi csatornák egyikét a támogatáshoz:

**Hibák jelentése** – Az Azure IoT Edge-termékbe bekövetkező fejlesztés nagy része az IoT Edge nyílt forráskódú projektben történik. A hibákat a projekt [problémaoldalán](https://github.com/azure/iotedge/issues) lehet jelenteni. A javítások gyorsan elhaladnak a projekttől a termékfrissítésekig.

**Microsoft ügyfélszolgálati csapat** – A [támogatási csomaggal](https://azure.microsoft.com/support/plans/) rendelkező felhasználók bevonhatják a Microsoft ügyfélszolgálati csapatát, ha közvetlenül az [Azure Portalról](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)hoznak létre támogatási jegyet.

**Szolgáltatáskérések** – Az Azure IoT Edge termék a termék Felhasználói hangoldalán keresztül követi nyomon a [funkciókérelmeket.](https://feedback.azure.com/forums/907045-azure-iot-edge)

## <a name="container-engines"></a>Konténermotorok

Az Azure IoT Edge-modulok tárolókként vannak megvalósítva, ezért az IoT Edge-nek egy tárolómotorra van szüksége az indításukhoz. A Microsoft egy tárolómotort, a Moby-engine-t biztosít, hogy megfeleljen ennek a követelménynek. Ez a tárolómotor a Moby nyílt forráskódú projekten alapul. A Docker CE és a Docker EE más népszerű tárolómotorok. A Moby nyílt forráskódú projekten is alapulnak, és kompatibilisek az Azure IoT Edge-el. A Microsoft a lehető legjobb támogatást nyújtja az ilyen tárolómotorokat használó rendszerekhez; a Microsoft azonban nem tudja szállítani a bennük lévő problémák at. Ezért a Microsoft azt javasolja, hogy a moby-motort éles rendszereken használják.

<br>
<center>

![A Moby motor konténerfutási időként](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operációs rendszerek

Az Azure IoT Edge a legtöbb olyan operációs rendszeren fut, amely tárolókat futtat; azonban nem mindegyik rendszer egyformán támogatott. Az operációs rendszerek olyan rétegekbe vannak csoportosítva, amelyek a felhasználók által elvárt támogatási szintet képviselik.

* Tier 1 rendszerek támogatottak. Tier 1 rendszerek esetén a Microsoft:
  * rendelkezik ezzel az operációs rendszerrel az automatizált tesztekben
  * telepítőcsomagokat biztosít számukra
* A Tier 2 rendszerek kompatibilisek az Azure IoT Edge-el, és viszonylag könnyen használhatók. tier 2 rendszerek esetében:
  * A Microsoft informális tesztelést végzett a platformokon, vagy tud egy olyan partnerről, amely sikeresen futtatta az Azure IoT Edge-et a platformon
  * A más platformokra vonatkozó telepítőcsomagok ezeken a platformokon is működhetnek

A gazdaoperációs rendszer család mindig meg kell egyeznie a modul tárolójában használt vendég operációs rendszer családjával. Más szóval, csak Linux-tárolókat használhat Linux és Windows tárolókon windowsos rendszeren. A Windows használata esetén csak a folyamat elkülönített tárolók támogatottak, nem Hyper-V elkülönített tárolók.  

<br>
<center>

![A gazdaoperációs rendszer megfelel a vendég operációs rendszernek](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Első réteg

Az alábbi táblázatban felsorolt rendszereket a Microsoft támogatja, akár általánosan elérhető, akár nyilvános előzetes verzióban, és minden új kiadással tesztelik őket. 

| Operációs rendszer | AMD64 között | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Szerver 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes verzió  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Szerver 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes verzió |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

A fent felsorolt Windows operációs rendszerek a Windows-tárolókat futtató eszközökre vonatkozó követelmények, amely az egyetlen támogatott konfiguráció éles környezetben. Az Azure IoT Edge telepítőcsomagok a Windows lehetővé teszik a Linux-tárolók a Windows; ez a konfiguráció azonban csak fejlesztési és tesztelési célokra szolgál. További információ: [Az IoT Edge használata A Windows linuxos tárolók futtatásához.](how-to-install-iot-edge-windows-with-linux.md)

### <a name="tier-2"></a>Második réteg

Az alábbi táblázatban felsorolt rendszerek kompatibilisnek tekinthetők az Azure IoT Edge-el, de a Microsoft nem teszteli aktívan vagy nem karbantartja őket.

| Operációs rendszer | AMD64 között | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Beágyazott Linux Flex OPERÁCIÓS RENDSZER](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Beágyazott Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Beágyazott Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Beágyazott Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Beágyazott Linux Omni operációs rendszer](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Beágyazott Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Beágyazott Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + KAR64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Szél folyó 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> A Debian 10 rendszerek, köztük a Raspian Buster, az OpenSSL olyan verzióját használják, amelyet az IoT Edge nem támogat. Az IoT Edge telepítése előtt a következő paranccsal telepítsen egy korábbi verziót:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Kiadások

IoT Edge kiadási eszközök és kiadási megjegyzések érhetők el az [azure-iotedge kiadások](https://github.com/Azure/azure-iotedge/releases) oldalon. Ez a szakasz az egyes verziók összetevőinek könnyebb megjelenítését segítő kibocsátási megjegyzésekből származó információkat tükrözi.

Az IoT Edge-összetevők egyenként telepíthetők vagy frissíthetők, és visszafelé kompatibilisek a régebbi verziók összetevőivel. Az alábbi táblázat az egyes kiadásokban található összetevőket sorolja fel:

| Kiadás   | Biztonsági démon  | Élű elosztó<br>Peremhálózati ügynök | Libiothsm | Moby  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

Az IoT Edge a Microsoft.Azure.Device.Client SDK-t használja. További információ: az [Azure IoT C# SDK GitHub-tártár](https://github.com/Azure/azure-iot-sdk-csharp) vagy az [Azure SDK a .NET referenciatartalom.](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) Az alábbi lista az ügyfél SDK-jának azon verzióját mutatja be, amelyen az egyes kiadások tesztelése történik:

* **IoT Edge 1.0.9**: Ügyfél SDK 1.21.1
* **IoT Edge 1.0.8**: Ügyfél SDK 1.20.3
* **IoT Edge 1.0.7**: Ügyfél SDK 1.20.1
* **IoT Edge 1.0.6**: Ügyfél SDK 1.17.1
* **IoT Edge 1.0.5**: Ügyfél SDK 1.17.1

## <a name="virtual-machines"></a>Virtuális gépek

Az Azure IoT Edge virtuális gépeken futtatható. A virtuális gép IoT Edge-eszközként való használata gyakori, ha az ügyfelek szeretnék bővíteni a meglévő infrastruktúrát peremhálózati intelligenciával. A gazdagép virtuálisgép-operációs rendszer családjának meg kell egyeznie a modul tárolójában használt vendég operációs rendszer családjával. Ez a követelmény ugyanaz, mint amikor az Azure IoT Edge közvetlenül fut egy eszközön. Az Azure IoT Edge az alapul szolgáló virtualizációs technológia független, és olyan platformokon működő virtuális gépeken működik, mint a Hyper-V és a vSphere.

<br>
<center>

![Azure IoT Edge egy virtuális gépben](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimális rendszerkövetelmények

Az Azure IoT Edge nagyszerűen fut a Raspberry Pi3-tól a kiszolgálószintű hardverekhez képest kicsi eszközökön. A forgatókönyvhöz megfelelő hardver kiválasztása a futtatni kívánt számítási feladatoktól függ. A végső eszközdöntés meghozatala bonyolult lehet; azonban könnyedén elkezdheti a megoldás prototípusát a hagyományos laptopokon vagy asztali számítógépeken.

A prototípus-készítés során szerzett tapasztalat segít a végső eszközválasztás irányításában. A következő kérdéseket kell figyelembe vennie:

* Hány modul van a munkaterhelésben?
* Hány réteget osztanak meg a modulok tárolói?
* Milyen nyelven vannak megírva a modulok?
* Mennyi adatot fognak feldolgozni a modulok?
* A moduloknak szüksége van speciális hardverre a munkaterhelések felgyorsításához?
* Melyek a megoldás kívánt teljesítményjellemzői?
* Mennyi a hardverköltségkerete?

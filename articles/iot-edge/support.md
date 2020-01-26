---
title: Támogatott operációs rendszerek, Container Engine-Azure IoT Edge
description: Megtudhatja, hogy mely operációs rendszerek futtathatják a Azure IoT Edge démont és a futtatókörnyezetet, valamint a támogatott tárolókat az üzemi eszközökhöz
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3f1f4efc13e2d11e7111264564a227a484d611d5
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760096"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge támogatott rendszerek

Ez a cikk részletesen ismerteti, hogy mely rendszerek és összetevők támogatottak a IoT Edge, akár hivatalosan, akár előzetes verzióban.

Ha a Azure IoT Edge szolgáltatás használata során problémákba ütközik, többféleképpen is kérhet támogatást. Próbálja ki a következő csatornák egyikét a támogatáshoz:

**Hibajelentések** – a Azure IoT Edge termékbe bekerülő fejlesztés többsége a IoT Edge nyílt forráskódú projektben történik. A hibákat a projekt [problémák lapján](https://github.com/azure/iotedge/issues) lehet jelenteni. Gyorsan megtörténik a javítások a projektből a termékek frissítéseibe való beüzemelése.

A **Microsoft ügyfélszolgálati csapata** – a [támogatási csomaggal](https://azure.microsoft.com/support/plans/) rendelkező felhasználók a támogatási jegyet közvetlenül a [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)hozhatják létre a Microsoft ügyfélszolgálati csapatának.

**Szolgáltatásra vonatkozó kérelmek** – a Azure IoT Edge termék a termék [felhasználói hangoldalán](https://feedback.azure.com/forums/907045-azure-iot-edge)keresztül követi nyomon a szolgáltatásra vonatkozó kéréseket.

## <a name="container-engines"></a>Tároló-motorok

Azure IoT Edge-modulok tárolóként vannak megvalósítva, ezért IoT Edge a tároló motorjának kell elindítania azokat. A Microsoft tároló-motort biztosít a Moby-Engine számára a követelmény teljesítéséhez. Ez a tároló motor a Moby nyílt forráskódú projekten alapul. A Docker CE és a Docker EE más népszerű tároló-motor. Emellett a nyílt forráskódú Moby-projekten alapulnak, és kompatibilisek Azure IoT Edgeokkal. A Microsoft a tároló-motorokat használó rendszerek esetében nyújt legjobb támogatást. a Microsoft azonban nem tudja kiszolgálni a javításokat a bennük felmerülő problémák miatt. Ezért a Microsoft a Moby Engine használatát javasolja az éles rendszereken.

<br>
<center>

![a Moby Engine-t tároló futtatókörnyezetként](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operációs rendszerek

Azure IoT Edge a tárolók futtatására képes legtöbb operációs rendszeren fut; azonban nem mindegyik rendszer egyformán támogatott. Az operációs rendszerek olyan rétegekbe vannak csoportosítva, amelyek a támogatási felhasználók által elvárt szintet jelölik.

* Az 1. szintű rendszerek támogatottak. Az 1. szintű rendszerek esetében a Microsoft:
  * az operációs rendszer automatikus tesztek
  * telepítési csomagokat biztosít számukra
* A 2. szintű rendszerek kompatibilisek Azure IoT Edgeekkel, és viszonylag könnyen használhatók. 2\. szintű rendszerek esetén:
  * A Microsoft informális tesztelést végzett a platformokon, vagy olyan partnert ismer, amely sikeresen futtatott Azure IoT Edge a platformon
  * A más platformokhoz tartozó telepítési csomagok működhetnek ezeken a platformokon

A gazda operációs rendszer családjának mindig egyeznie kell a modul tárolójában használt vendég operációs rendszer családjának. Ez azt jelenti, hogy a Linux-tárolókat csak Linux-és Windows-tárolókban használhatja Windows rendszeren. A Windows használata esetén csak az elkülönített tárolók feldolgozása támogatott, a Hyper-V elkülönített tárolók nem.  

<br>
<center>

![gazdagép operációs rendszere megfelel a vendég operációs rendszer](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>1\. szint

A Microsoft a következő táblázatban felsorolt rendszereket támogatja: általánosan elérhető vagy nyilvános előzetes verzióban, és minden új kiadással tesztelve van. 

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes verzió  |
| [Ubuntu Server 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes verzió |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), Build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), Build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), 17763-es Build | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), Build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

A fent felsorolt Windows operációs rendszerek a Windows-tárolókat futtató eszközökre vonatkozó követelmények a Windows rendszeren, amely az egyetlen támogatott konfiguráció az éles környezetben. A Windows Azure IoT Edge telepítési csomagjai lehetővé teszik a Linux-tárolók használatát Windows rendszeren; Ez a konfiguráció azonban csak fejlesztési és tesztelési célokra használható. További információ: [IoT Edge használata Windows rendszeren Linux-tárolók futtatásához](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>2\. szint

Az alábbi táblázatban felsorolt rendszerek kompatibilisek Azure IoT Edge, de a Microsoft nem teszteli vagy nem tartja karban azokat.

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7,5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [A mentor Embedded Linux Flex operációs rendszer](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni operációs rendszer](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7,5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [8. Wind River](https://docs.windriver.com/category/os-wind_river_linux) | ![A Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> a Debian 10 rendszer, köztük a Raspian Buster, az OpenSSL olyan verzióját használja, IoT Edge nem támogatja. A következő parancs használatával telepítsen egy korábbi verziót a IoT Edge telepítése előtt:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtual Machines

A Azure IoT Edge virtuális gépeken is futtathatók. A virtuális gépek IoT Edge eszközként való használata gyakori, ha az ügyfelek a meglévő infrastruktúrát szeretnék kibővíteni a peremhálózati intelligenciával. A gazda VM operációs rendszer családjának meg kell egyeznie a modul tárolójában használt vendég operációs rendszer családjának. Ez a követelmény ugyanaz, mint amikor a Azure IoT Edge közvetlenül egy eszközön fut. Azure IoT Edge a mögöttes virtualizációs technológia, és olyan platformokon működik, mint például a Hyper-V és a vSphere.

<br>
<center>

![Azure IoT Edge egy virtuális gépen](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimális rendszerkövetelmények

A Azure IoT Edge nagyszerűen fut az eszközökön, mint a Pi3 a kiszolgálói minőségű hardverek számára. Ha kiválasztja a megfelelő hardvert a forgatókönyvhöz, a futtatni kívánt munkaterheléstől függ. Az eszköz végső döntése bonyolult lehet; a hagyományos laptopokon és asztali számítógépeken azonban könnyedén elindíthatja a prototípust.

A prototípus-készítés során a rendszer segít a végső eszköz kiválasztásában. Vegye figyelembe a következőket:

* Hány modul van a munkaterhelésben?
* Hány réteget osztanak meg a modulok tárolói?
* Milyen nyelven íródott modulok?
* Mennyi adattal fog dolgozni a modulok feldolgozása?
* Szükség van-e a modulok speciális hardverre a számítási feladatok felgyorsításához?
* Mik a megoldás kívánt teljesítménybeli jellemzői?
* Mi a hardveres költségkeret?

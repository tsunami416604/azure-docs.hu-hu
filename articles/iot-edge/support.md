---
title: Támogatott operációs rendszerek, tároló motorok – Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg, melyik operációs rendszerek futtathatók az Azure IoT Edge-démon és a futtatókörnyezeti és az éles eszközök támogatott tároló-motorok
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5bc133e81f9917aafb406a6bfb27922cdba48ef5
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190005"
---
# <a name="azure-iot-edge-supported-systems"></a>Az Azure IoT Edge által támogatott rendszerek

Nincsenek többféle módon, hogy az Azure IoT Edge-termék támogatása.

**Hibák Reporting** – a legtöbb fejlesztési, amely az Azure IoT Edge-termék hiányzóra történik, az IoT Edge nyílt forráskódú projekt. A hibák jelenteni lehet a [problémák lap](https://github.com/azure/iotedge/issues) a projekt. Javítások gyorsan eljutnak a projektből a termékfrissítéseket.

**A Microsoft ügyfél-támogatási csapatának** – felhasználók, akik rendelkeznek egy [támogatási csomag](https://azure.microsoft.com/support/plans/) hozzon létre egy támogatási jegyet közvetlenül a Microsoft ügyfél-támogatási csapata is léphet az [az Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Kérelmek funkció** – az Azure IoT Edge-termék nyomon követi a szolgáltatással kapcsolatos kéréseit a termék keresztül [User Voice lap](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Tároló-motorok
Az Azure IoT Edge szüksége van egy tároló-motor modulok elindításához, mivel azok tárolókként vannak megvalósítva. A Microsoft biztosít egy tároló motor moby-motor, ez a követelmény teljesítéséhez. A Moby nyílt forráskódú projekt alapul. A docker CE és a Docker EE más népszerű motorokkal. Ezek is alapján a Moby nyílt forráskódú projekt, és kompatibilis az Azure IoT Edge segítségével. A Microsoft biztosít a elérhető legjobb lehetőség támogatásával rendszerekben a tároló motorok; használatával a Microsoft azonban nem rendelkezik az LHS.Gold.Multi.B4 őket hibák javításait teszi. Ebből kifolyólag a Microsoft azt javasolja, az éles rendszereket moby-motor használatával.

<br>
<center>

![Container modul, Moby](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operációs rendszerek
Az Azure IoT Edge, amelyek futtathatók tárolók; a legtöbb operációs rendszeren fut. azonban ezek a rendszerek nem egyaránt támogatottak. Operációs rendszerek számíthatnak támogatás szintjét képviselő szinten vannak csoportosítva.
* 1. rétegbeli rendszerek tekinthető, hivatalosan támogatott. 1. rétegbeli rendszerek esetében a Microsoft:
    * Ez az operációs rendszer automatizált teszteket rendelkezik
    * biztosít a számukra a telepítési csomagok
* 2. rétegbeli rendszerek szerint kompatibilis az Azure IoT Edge segítségével olyan, és viszonylag könnyen használható. 2. rétegbeli rendszerek esetén:
    * A Microsoft alkalmi tesztelése a platformon végzett, vagy tudja, hogy sikeresen rendszeren történő futtatása az Azure IoT Edge egy partner
    * Más platformok telepítőcsomagok ezeken a platformokon is működhet.
    
A operációsrendszer-család a gazda operációs rendszer mindig meg kell egyeznie a vendég operációs rendszer egy modul tárolóban használt összessége. Más szóval csak használhatja a linuxon futó Linux-tárolók és a Windows-tárolók Windows. Windows, elkülönített tárolókban támogatottak, egyetlen folyamat használata esetén nem Hyper-V elkülönített tárolókat.  

<br>
<center>

![Gazda operációs rendszer megfelel a vendég operációs rendszer](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>1. szint
Általánosan elérhető

| Operációs rendszer | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| A stretch Raspbian | Nem | Igen|
| Ubuntu Server 16.04 | Igen | Nem |
| Ubuntu Server 18.04 | Igen | Nem |

Nyilvános előzetes verzió

| Operációs rendszer | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core, build 17763 | Igen | Nem |
| A Windows 10 IoT Enterprise, a build 17763 | Igen | Nem |
| Windows Server 2019 | Igen | Nem |

A Windows operációs rendszerek fent felsorolt Windows-tárolók a Windows rendszerű eszközök követelmények vonatkoznak. Ez a konfiguráció az egyetlen támogatott konfigurációkra vonatkozó üzemi. Windows Azure IoT Edge-telepítőcsomagok Windows; a Linux-tárolók használatának engedélyezése azonban ez a konfiguráció kizárólag fejlesztési és tesztelési szól. A Windows Linux-tárolók használata nem támogatott konfiguráció éles üzemi környezetek részei. Az alkalmazásfejlesztési forgatókönyvet bármilyen Windows 10-es build 14393 vagy újabb verzió és a Windows Server 2016-os vagy újabb verzió használható.

### <a name="tier-2"></a>2. szint

| Operációs rendszer | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Igen | Igen |
| Debian 8 | Igen | Igen |
| Debian 9 | Igen | Igen |
| RHEL 7.5 | Igen | Igen |
| Ubuntu 18.04 | Igen | Igen |
| Ubuntu 16.04 | Igen | Igen |
| A szél folyó 8 | Igen | Nem |
| Yocto | Igen | Nem |


## <a name="virtual-machines"></a>Virtuális gépek
Az Azure IoT Edge virtuális gépeken is futtatható. Virtuális gép, egy IoT Edge használatával eszköz esetén gyakori, mivel megvédi a peremhálózati intelligencia a meglévő infrastruktúra ügyfelei szeretnék. Az a gazdagép virtuális gép operációsrendszer-család egyeznie kell a vendég operációs rendszer egy modul tárolóban használt összessége. Ez a követelmény nem ugyanaz, mint az Azure IoT Edge futtatásakor közvetlenül az eszközön. Az Azure IoT Edge független a mögöttes virtualizációs technológia, és működteti, platformokon, például a Hyper-V- és vSphere-beli virtuális gépeken működik.

<br>
<center>

![Az Azure IoT Edge egy virtuális gépen](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimális rendszerkövetelményei
Az Azure IoT Edge kiválóan működjön az eszközök egy Raspberry Pi3 legyen a kiszolgáló szintű hardverre. A forgatókönyvnek megfelelő hardver kiválasztása attól függ, hogy a futtatni kívánt számítási feladatokat. Végső eszköz megugró lehet bonyolult; a hagyományos hordozható vagy asztali azonban prototípus-megoldás könnyedén elindíthatja.

Felhasználói élményt, amíg prototípus-készítés végső eszköz választását útmutató segítségével. Érdemes lehet kérdések a következők: 

* Hány modult az alkalmazások és szolgáltatások vannak?
* Hány rétegek tegye meg a modulok tárolókat?
* Milyen nyelven írták el a modulok? 
* Mekkora mennyiségű adatot fog a modulok feldolgozni?
* A modulok szükség van a speciális hardvereket felgyorsítása a számítási feladatok esetében?
* Mik azok a kívánt teljesítményt nyújt, hogy a megoldás?
* Mi az a hardver költségvetést?

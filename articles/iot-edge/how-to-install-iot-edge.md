---
title: A Azure IoT Edge telepítése | Microsoft Docs
description: Azure IoT Edge telepítési utasítások Windows vagy Linux rendszerű eszközökön
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: 7ab62b04f8bea76c7efb587665f87ccaf123da24
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109000"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>A Azure IoT Edge futtatókörnyezet telepítése vagy eltávolítása

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet az eszközökön kisméretű, málna PI-ként vagy ipari kiszolgálóként is telepíthető. Miután konfigurált egy eszközt az IoT Edge-futtatókörnyezettel, üzembe helyezhet rajta üzleti logikát a felhőből. További információ: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

Egy IoT Edge eszköz beállításának két lépése van. Első lépésként telepítse a futtatókörnyezetet és annak függőségeit, amelyekre ez a cikk vonatkozik. A második lépés az eszköz identitáshoz csatlakoztatása a felhőben, valamint a IoT Hub hitelesítés beállítása. Ezek a lépések a következő cikkekben találhatók.

Ez a cikk a Azure IoT Edge futtatókörnyezet Linux vagy Windows rendszerű eszközökön való telepítésének lépéseit sorolja fel. Windows-eszközök esetén a Linux-tárolók és a Windows-tárolók használata további választás. A Windows-tárolók jelenleg éles környezetekben ajánlottak. A Windowson futó Linux-tárolók fejlesztési és tesztelési forgatókönyvekhez hasznosak, különösen akkor, ha Windows rendszerű számítógépen fejlesztenek Linux rendszerű eszközökön való üzembe helyezéshez.

## <a name="prerequisites"></a>Előfeltételek

Az éles környezetekben jelenleg támogatott operációs rendszerekkel kapcsolatos legfrissebb információk: [Azure IoT Edge támogatott rendszerek](support.md#operating-systems)

# <a name="linux"></a>[Linux](#tab/linux)

X64-, ARM32-vagy ARM64-alapú Linux-eszközre van telepítve. A Microsoft az Ubuntu Server 16,04, az Ubuntu Server 18,04 és a Raspbian stretch operációs rendszerek telepítési csomagjait biztosítja.

>[!NOTE]
>A ARM64-eszközök támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

Készítse elő az eszközt a Microsoft telepítési csomagjainak eléréséhez.

1. Telepítse az eszköz operációs rendszerének megfelelő adattár-konfigurációt.

   * **Ubuntu Server 16,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspbian stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Másolja a generált listát a sources. list. d könyvtárba.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Telepítse a Microsoft GPG nyilvános kulcsot.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Windows-verzió

A Windows-tárolókkal IoT Edge a Windows rendszerhez 1809/Build 17762, amely a [Windows hosszú távú támogatásának](/windows/release-information/)legújabb verziója. Fejlesztési és tesztelési forgatókönyvek esetén a tárolók funkciót támogató bármely SKU (Pro, Enterprise, Server stb.) működni fog. Azonban mindenképpen tekintse át a támogatott rendszerek listáját az éles környezetbe állítás előtt.

A Linux-tárolókkal rendelkező IoT Edge a Windows bármely olyan verzióját futtathatja, amely megfelel a [Docker Desktop követelményeinek](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

### <a name="container-engine-requirements"></a>A tároló motorjának követelményei

A Azure IoT Edge egy [OCI-kompatibilis](https://www.opencontainers.org/) tároló motorra támaszkodik. Győződjön meg arról, hogy az eszköz támogatja a tárolókat.

Ha virtuális gépen telepíti a IoT Edget, engedélyezze a beágyazott virtualizációt, és foglaljon le legalább 2 GB memóriát. A Hyper-V esetében a 2. generációs virtuális gépekhez alapértelmezés szerint engedélyezve van a beágyazott virtualizálás. A VMware esetében van egy váltógomb, amely engedélyezi a szolgáltatást a virtuális gépen.

Ha a IoT Edge IoT Core-eszközre telepíti, a [távoli PowerShell-munkamenetben](/windows/iot-core/connect-your-device/powershell) a következő paranccsal ellenőrizze, hogy a Windows-tárolók támogatottak-e az eszközön:

```powershell
Get-Service vmcompute
```

---

Azure IoT Edge szoftvercsomagok az egyes csomagokban ( `usr/share/doc/{package-name}` vagy a címtárban) található licencfeltételek hatálya alá esnek `LICENSE` . A licencfeltételek elolvasása a csomag használata előtt. A csomagok telepítése és használata a jelen feltételek elfogadását jelenti. Ha nem fogadja el a licencfeltételeket, ne használja ezt a csomagot.

## <a name="install-a-container-engine"></a>Tároló-motor telepítése

Azure IoT Edge egy OCI-kompatibilis tároló-futtatókörnyezetre támaszkodik. Éles környezetekben javasolt a Moby-alapú motor használata. A Moby motor az egyetlen tároló motor, amelyet hivatalosan támogat a Azure IoT Edge. A Docker CE/EE tároló lemezképei kompatibilisek a Moby Runtime szolgáltatással.

# <a name="linux"></a>[Linux](#tab/linux)

Frissítési csomagok listája az eszközön.

   ```bash
   sudo apt-get update
   ```

Telepítse a Moby Engine-t.

   ```bash
   sudo apt-get install moby-engine
   ```

Ha a Moby Container Engine telepítésekor hibákat kap, ellenőrizze a Linux-kernelt a Moby Compatibility szolgáltatáshoz. Néhány beágyazott eszköz gyártója olyan eszközöket szállít, amelyek egyéni Linux-kerneleket tartalmaznak a tároló motorjának kompatibilitásához szükséges funkciók nélkül. Futtassa a következő parancsot, amely a Moby által biztosított [ellenőrzés-konfiguráció parancsfájlt](https://github.com/moby/moby/blob/master/contrib/check-config.sh) használja a rendszermag konfigurációjának ellenőrzéséhez:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

A parancsfájl kimenetében győződjön meg arról, hogy a és a összes eleme `Generally Necessary` `Network Drivers` engedélyezve van. Ha hiányoznak a funkciók, engedélyezze őket a kernel a forrásból való újraépítésével, majd a megfelelő kernel. config fájlban való felvételhez kapcsolódó modulok kiválasztásával. Hasonlóképpen, ha olyan kernel-konfigurációs generátort használ `defconfig` , mint a vagy a `menuconfig` , megkeresheti és engedélyezheti a megfelelő szolgáltatásokat, és ennek megfelelően újjáépítheti a kernelt. Miután telepítette az újonnan módosított kernelt, futtassa újra a bejelentkezést ellenőrző parancsfájlt annak ellenőrzéséhez, hogy az összes szükséges funkció engedélyezve lett-e.

# <a name="windows"></a>[Windows](#tab/windows)

Éles forgatókönyvek esetén használja a telepítési parancsfájlban található Moby-alapú motort. A motor telepítéséhez nincs szükség további lépésekre.

A Linux-tárolókkal rendelkező IoT Edge a saját tároló-futtatókörnyezetet kell megadnia. Telepítse a [Docker Desktopot](https://docs.docker.com/docker-for-windows/install/) az eszközére, és konfigurálja úgy, hogy a folytatás előtt [Linux-tárolókat használjon](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) .

---

## <a name="install-the-iot-edge-security-daemon"></a>A IoT Edge biztonsági démon telepítése

A IoT Edge biztonsági démon a IoT Edge eszközön biztosít és tart fenn biztonsági szabványokat. A démon minden rendszerindításkor elindul, és elindítja az eszközt a IoT Edge futtatókörnyezet további részének elindításával.

A jelen szakaszban ismertetett lépések a legújabb verziónak az internetkapcsolattal rendelkező eszközre történő telepítésének tipikus folyamatát tükrözik. Ha telepítenie kell egy adott verziót, például egy előzetes verziót, vagy offline üzemmódban kell telepítenie, kövesse a következő szakaszban található [Offline vagy adott verzió telepítési](#offline-or-specific-version-installation) lépéseit.

# <a name="linux"></a>[Linux](#tab/linux)

Frissítési csomagok listája az eszközön.

   ```bash
   sudo apt-get update
   ```

Ellenőrizze, hogy a IoT Edge mely verziói érhetők el.

   ```bash
   apt list -a iotedge
   ```

Ha a biztonsági démon legújabb verzióját szeretné telepíteni, használja a következő parancsot, amely az **libiothsm-STD** csomag legfrissebb verzióját is telepíti:

   ```bash
   sudo apt-get install iotedge
   ```

Ha a biztonsági démon egy adott verzióját szeretné telepíteni, adja meg a verziót az apt List kimenetből. Ugyanezt a verziót is meg kell adni a **libiothsm-STD** csomaghoz, amely egyébként a legújabb verzióját telepíti. A következő parancs például a 1.0.8 kiadás legújabb verzióját telepíti:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Ha a telepíteni kívánt verzió nem szerepel a listáján, kövesse a következő szakaszban található [Offline vagy adott verzió telepítésének](#offline-or-specific-version-installation) lépéseit. Ez a szakasz bemutatja, hogyan célozhatja meg a IoT Edge biztonsági démon vagy a Release Candidate verziójának korábbi verzióját.

# <a name="windows"></a>[Windows](#tab/windows)

>[!TIP]
>A IoT Core-eszközök esetében javasoljuk, hogy a telepítési parancsokat távoli PowerShell-munkamenet használatával futtassa. További információ: a [PowerShell használata a Windows IoT](/windows/iot-core/connect-your-device/powershell).

1. Futtassa a PowerShellt rendszergazdaként.

   Használjon a PowerShell AMD64-munkamenetét, ne a PowerShellt (x86). Ha nem tudja biztosan, hogy melyik munkamenet-típust használja, futtassa a következő parancsot:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Futtassa az [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) parancsot, amely a következő feladatokat hajtja végre:

   * Ellenőrzi, hogy a Windows rendszerű számítógép támogatott verziójú-e.
   * A tárolók funkció bekapcsolása.
   * Letölti a Moby Engine-t és a IoT Edge futtatókörnyezetet.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   A `Deploy-IoTEdge` parancs alapértelmezés szerint Windows-tárolókat használ. Ha Linux-tárolókat szeretne használni, adja hozzá a következő `ContainerOs` paramétert:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. Ezen a ponton a IoT Core-eszközök automatikusan újraindulnak. Előfordulhat, hogy a Windows 10 vagy Windows Server rendszerű eszközök újraindítást kérik. Ha igen, indítsa újra az eszközt.

Ha IoT Edge telepít egy eszközre, további paramétereket is használhat a folyamat módosításához, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom
* A telepítőt egy helyi könyvtárba irányítsa offline telepítéshez.

További információ ezekről a további paraméterekről: [PowerShell-parancsfájlok IoT Edge Windows rendszeren](reference-windows-scripts.md).

---

Most, hogy telepítette a tároló motort és a IoT Edge futtatókörnyezetet az eszközre, készen áll a következő lépésre, amely az eszköz regisztrálását IoT Hub és az eszköz Felhőbeli identitás-és hitelesítési információkkal való beállítására szolgál.

Válassza ki a következő cikket a használni kívánt hitelesítési típus alapján:

* A [szimmetrikus kulcsos hitelesítés](how-to-manual-provision-symmetric-key.md) gyorsabb a kezdéshez.
* Az [X. 509 tanúsítvány-hitelesítés](how-to-manual-provision-x509.md) biztonságosabb az éles környezetekben.

## <a name="offline-or-specific-version-installation"></a>Offline vagy adott verzió telepítése

Az ebben a szakaszban ismertetett lépések a normál telepítési lépések által nem tárgyalt forgatókönyvekre vonatkoznak. Ilyen lehet például a következők:

* IoT Edge telepítése kapcsolat nélküli üzemmódban
* Release Candidate verziójának telepítése
* Windows rendszeren telepítsen a legújabbtól eltérő verziót

# <a name="linux"></a>[Linux](#tab/linux)

Az ebben a szakaszban ismertetett lépéseket követve telepítheti a Azure IoT Edge futtatókörnyezet adott verzióját, amely nem érhető el a rendszeren `apt-get install` . A Microsoft-csomagok listája csak korlátozott számú közelmúltbeli verziót és azok alverzióit tartalmazza, ezért ezek a lépések mindazok számára szükségesek, akik egy régebbi verziót vagy egy kiadásra jelölt verziót kívánnak telepíteni.

A curl-parancsok használatával közvetlenül a IoT Edge GitHub-tárházból is megcélozhatja az összetevők fájljait.

<!-- TODO: Offline installation? -->

1. Navigáljon a [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)kiadásokhoz, és keresse meg a célként használni kívánt kiadási verziót.

2. Bontsa ki az **eszközök** szakaszt az adott verzióhoz.

3. Minden kiadásnak új fájlokkal kell rendelkeznie a IoT Edge biztonsági démonhoz és a hsmlib. Ezen összetevők frissítéséhez használja az alábbi parancsokat.

   1. Keresse meg a IoT Edge eszköz architektúrájának megfelelő **libiothsm-STD-** fájlt. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe.

   2. A következő parancsban található másolt hivatkozásra kattintva telepítheti a hsmlib adott verzióját:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. A IoT Edge eszköz architektúrájának megfelelő **iotedge** -fájl megkeresése. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe.

   4. A következő parancsban található másolt hivatkozásra kattintva telepítse a IoT Edge biztonsági démon adott verzióját.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

A telepítés során a rendszer letölti a három fájlt:

* Egy PowerShell-parancsfájl, amely tartalmazza a telepítési utasításokat
* Microsoft Azure IoT Edge CAB, amely tartalmazza a IoT Edge biztonsági démont (iotedged), a Moby Container Engine-t és a Moby CLI-t
* Visual C++ Újraterjeszthető csomag (VC Runtime) telepítője

Ha az eszköz a telepítés során offline állapotba kerül, vagy ha a IoT Edge egy adott verzióját szeretné telepíteni, akkor a fájlokat előre letöltheti az eszközre. A telepítés ideje alatt a letöltött fájlokat tartalmazó könyvtárban mutasson a telepítési parancsfájlra. A telepítő először ellenőrzi a könyvtárat, és csak azokat az összetevőket tölti le, amelyek nem találhatók. Ha az összes fájl offline állapotban érhető el, az internetkapcsolat nélkül is telepíthető.

1. A legújabb IoT Edge telepítési fájlokról a korábbi verziók mellett lásd: [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases).

2. Keresse meg a telepíteni kívánt verziót, majd töltse le a következő fájlokat a kibocsátási megjegyzések a IoT-eszközön lévő **eszközök** részéből:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab a 1.0.9 vagy újabb kiadásokból, vagy Microsoft-Azure-IoTEdge.cab a 1.0.8 és régebbi kiadásokból.

   A Microsoft-Azure-IotEdge-arm32.cab a 1.0.9-től kezdődően is elérhető tesztelési célokra. A IoT Edge jelenleg nem támogatott Windows ARM32-eszközökön.

   Fontos, hogy a PowerShell-parancsfájlt ugyanazzal a kiadással használja, mint a. cab-fájllal, amelyet a funkció az egyes kiadásokban lévő funkciók támogatásához módosít.

3. Ha a letöltött. cab-fájlhoz architektúra utótag tartozik, nevezze át a fájlt csak **Microsoft-Azure-IoTEdge.cab**.

4. Igény szerint a Visual C++ újraterjeszthető csomagjának telepítőjét is letöltheti. A PowerShell-parancsfájl például a következő verziót használja: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Mentse a telepítőt ugyanabban a mappában a IoT-eszközön, mint a IoT Edge fájlokat.

5. Ha offline összetevőkkel szeretné telepíteni a programot, a [dot forrás](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) a PowerShell-parancsfájl helyi példányát adja meg. 

6. Futtassa az [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) parancsot a (z `-OfflineInstallationPath` ) paraméterrel. Adja meg a fájl könyvtárának abszolút elérési útját. Például:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   A központi telepítési parancs a megadott helyi fájl könyvtárában található összetevőket fogja használni. Ha a. cab-fájl vagy a Visual C++-telepítő hiányzik, a rendszer megkísérli a letöltést.

---

Most, hogy telepítette a tároló motort és a IoT Edge futtatókörnyezetet az eszközre, készen áll a következő lépésre, amely [egy IoT Edge eszköz hitelesítése IoT hub](how-to-manual-provision-symmetric-key.md).

## <a name="uninstall-iot-edge"></a>IoT Edge eltávolítása

Ha el szeretné távolítani a IoT Edge telepítését az eszközről, használja a következő parancsokat.

# <a name="linux"></a>[Linux](#tab/linux)

Távolítsa el az IoT Edge-futtatókörnyezetet.

```bash
sudo apt-get remove --purge iotedge
```

A IoT Edge futtatókörnyezet eltávolításakor a létrehozott tárolók leállnak, de továbbra is léteznek az eszközön. Megtekintheti az összes tárolót, hogy azok megmaradjanak-e.

```bash
sudo docker ps -a
```

Törölje a tárolókat az eszközről, beleértve a két futtató tárolót is.

```bash
sudo docker rm -f <container name>
```

Végül távolítsa el a Container Runtime eszközt az eszközről.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

# <a name="windows"></a>[Windows](#tab/windows)

Ha el szeretné távolítani a IoT Edge telepítését a Windows-eszközről, használja az [uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) parancsot egy felügyeleti PowerShell-ablakból. Ez a parancs eltávolítja a IoT Edge futtatókörnyezetet, valamint a meglévő konfigurációt és a Moby Engine-adatait.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

A `Uninstall-IoTEdge` parancs nem működik a Windows IoT Core-on. IoT Edge eltávolításához újra kell telepítenie a Windows IoT Core-lemezképet.

Az eltávolítási lehetőségekkel kapcsolatos további információkért használja az parancsot `Get-Help Uninstall-IoTEdge -full` .

---

## <a name="next-steps"></a>További lépések

A IoT Edge futtatókörnyezet telepítése után konfigurálja az eszközt IoT Hubhoz való kapcsolódásra. A következő cikkek végigvezetik az új eszköz felhőben való regisztrálásán, majd az eszköz identitás-és hitelesítési adataival.

Válassza ki a következő cikket a használni kívánt hitelesítési típus alapján:

* **Szimmetrikus kulcs**: a IoT hub és az IoT Edge eszközön van egy biztonságos kulcs másolata. Amikor az eszköz csatlakozik a IoT Hubhoz, a kulcsok megegyeznek. Ez a hitelesítési módszer gyorsabb a kezdéshez, de nem biztonságos.

  [Azure IoT Edge eszköz beállítása szimmetrikus kulcsos hitelesítéssel](how-to-manual-provision-symmetric-key.md)

* **Önaláírt x. 509**: a IoT Edge eszköz x. 509 azonosító tanúsítványokkal rendelkezik, és a IoT hub a tanúsítványok ujjlenyomatát kapja. Amikor az eszköz csatlakozik a IoT Hubhoz, a rendszer összehasonlítja a tanúsítványt az ujjlenyomatával. Ez a hitelesítési módszer biztonságosabb, és éles környezetekben ajánlott.

  [Azure IoT Edge eszköz beállítása X. 509 tanúsítványalapú hitelesítéssel](how-to-manual-provision-x509.md)
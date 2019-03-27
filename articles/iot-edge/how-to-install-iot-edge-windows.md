---
title: Telepítse az Azure IoT Edge Windows |} A Microsoft Docs
description: Az Azure IoT Edge a Windows 10-es, a Windows Server és a Windows IoT Core telepítési utasításokat
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: d669842c60fb69820e6d94ad0a9359f6460101fe
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481875"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Az Azure IoT Edge-futtatókörnyezet telepíthető Windows

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható. 

Az IoT Edge-futtatókörnyezet kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk felsorolja a lépéseket az Azure IoT Edge-futtatókörnyezet telepítéséhez a Windows x64 (Intel vagy AMD) a rendszer. Windows támogatási jelenleg előzetes verzióban érhető el.

> [!NOTE]
> Linux-tárolók használata a Windows rendszereken a javasolt vagy támogatott éles konfiguráció nem az Azure IoT Edge-hez. Azonban ez használható fejlesztési és tesztelési célra.

## <a name="prerequisites"></a>Előfeltételek

Ebben a szakaszban használja, tekintse át, hogy a Windows-eszköz IoT Edge is támogatják-e, és előkészítéséhez, telepítés előtt egy tároló-motor. 

### <a name="supported-windows-versions"></a>Támogatott Windows-verziók

Az Azure IoT Edge attól függően, hogy Windows-tárolók vagy Linux-tárolók futtatja a Windows, különböző verzióit támogatja. 

Az Azure IoT Edge legújabb verzióját a Windows-tárolókkal futtassa az alábbi Windows-verziók:
* Windows 10-es vagy IoT Core-2018. október (build 17763) frissítése
* A Windows Server 2019 (build 17763)

Az Azure IoT Edge a Linux-tárolók legújabb verzióját a Windows alábbi verzióit futtathatja: 
* A Windows 10 Évfordulós frissítés (build 14393) vagy újabb
* Windows Server 2016 vagy újabb

További információ arról, hogy mely jelenleg támogatott operációs rendszerekkel: [Azure IoT Edge-támogatás](support.md#operating-systems). 

Mit tartalmaz az IoT Edge a legújabb verzióra további információ: [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases).

### <a name="prepare-for-a-container-engine"></a>Egy tároló-motor előkészítése 

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis](https://www.opencontainers.org/) container-motor. A termelési forgatókönyvekhez használja a Moby motor szerepel a telepítési parancsfájl a Windows-eszközön a Windows-tárolók futtatásához. A fejlesztés és tesztelés futtatása Linux-tárolók Windows-eszközén, de telepítése és konfigurálása egy tároló-motor az IoT Edge telepítése előtt kell. Mindkét forgatókönyvre érvényes tekintse meg az eszköz előkészítése az előfeltételeket a következő szakaszokat. 

Ha szeretné telepíteni az IoT Edge egy virtuális gépen, beágyazott virtualizálás engedélyezése, és legalább 2 GB memóriát lefoglalni. Beágyazott virtualizálás engedélyezése hogyan eltér attól függően, a hipervizor használatát. A Hyper-V a 2. generációs virtuális gépek beágyazott virtualizálás alapértelmezés szerint engedélyezve. A VMWare van egy ki-/ bekapcsolása a virtuális gépen a funkció engedélyezéséhez. 

#### <a name="moby-engine-for-windows-containers"></a>Windows-tárolókhoz Moby motor

Éles forgatókönyvekben IoT Edge futó Windows-eszközök esetén Moby egy a csak hivatalosan támogatott tároló-motor. A telepítési parancsfájl automatikusan telepíti a Moby motor az eszköz IoT Edge telepítése előtt. Az eszköz előkészítése a tárolók funkció bekapcsolásával. 

1. A kezdő sávban keresse meg **kapcsolja be Windows-szolgáltatások be- és kikapcsolása** , és nyissa meg a Vezérlőpult programjában.
2. Keresse meg és jelölje ki **tárolók**.
3. Kattintson az **OK** gombra. 

#### <a name="docker-for-linux-containers"></a>A docker Linux-tárolók esetén

Ha Windows fejlesztéséhez és teszteléséhez a tárolók a Linux rendszerű eszközök használata esetén használhatja [Docker for Windows](https://www.docker.com/docker-windows) a tároló motorként. Docker beállítható úgy, hogy [Linux-tárolók használata](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). Telepítheti a Dockert, és állítsa be úgy az IoT Edge telepítése előtt kell. Linux-tárolók nem támogatottak a Windows-eszközökön éles környezetben. 

Ha az IoT Edge-eszközt egy Windows-számítógépen, ellenőrizze, hogy megfelel-e a [rendszerkövetelmények](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge egy új eszköz telepítése

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagok feltételei vonatkoznak rá a licencet (a címtárban licenccel) a csomagokban található. Kérjük, olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

Egy PowerShell-szkript letölti és telepíti az Azure IoT Edge biztonsági démon. A biztonsági démon ezután elindítja az első két futásidejű modulok, az IoT Edge ügynök, amely lehetővé teszi az egyéb modulok távoli telepítések. 

Ha egy eszköz először telepíti az IoT Edge-futtatókörnyezet, kell biztosítsa az eszköz IoT hubról identitást. Egy adott IoT Edge-eszköz kiépítése az IoT Hub által biztosított eszközök kapcsolatok karakterlánc segítségével manuálisan. Másik lehetőségként használhatja a Device Provisioning Service-eszközök automatikus kiépítésére, amely akkor hasznos, ha sok eszköz beállításához van. Üzembe helyezési válaszaitól függően válassza ki a megfelelő telepítési parancsfájlt. 

A következő szakaszok ismertetik a gyakori alkalmazási helyzetek és az IoT Edge telepítési parancsfájl paraméterek egy új eszközön. 

### <a name="option-1-install-and-manually-provision"></a>Option 1: Telepítés és manuális üzembe helyezése

Az első lehetőségnél adja meg az eszköz kiépítése az IoT Hub által előállított eszközök kapcsolati karakterláncot. 

Kövesse a [egy új Azure IoT Edge-eszköz regisztrálása](how-to-register-device-portal.md) regisztrálja az eszközt, és az eszköz kapcsolati karakterláncának beolvasása. 

Ez a példa bemutatja a manuális telepítés a Windows-tárolókkal:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

Telepítésekor, és manuálisan-eszköz kiépítése, további paraméterek segítségével módosíthatja a telepítési többek között:
* Forgalomnak egy proxyn keresztül go
* A telepítő átirányítása egy offline könyvtár
* Deklaráljon egy adott ügynöknél tárolórendszerképet, és adja meg a hitelesítő adatokat, ha olyan privát beállításjegyzékbe
* Hagyja ki a Moby parancssori felület telepítése

Ezen telepítési lehetőségekről további információkért folytassa a cikk elolvasása, vagy hagyja ki kapcsolatos [összes telepítési paramétereket](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Option 2: Telepítés, és automatikusan üzembe helyezése

A második lehetőség, a üzembe helyezi az eszközt az IoT Hub Device Provisioning Service használatával. Adja meg a **hatókör azonosítója** a Device Provisioning Service-példányból, és a **regisztrációs azonosító** az eszközről.

Kövesse a [létrehozásával és kiépítésével egy szimulált TPM-eszköz peremhálózati eszköz, a Windows](how-to-auto-provision-simulated-device-windows.md) a Device Provisioning Service beállítása és lekérése a **hatókör azonosítója**, TPM-eszköz szimulálása és lekérése a  **Regisztrációs azonosító**, majd hozzon létre egyéni regisztrációt. Miután az eszköz regisztrálva van az IoT hub, a telepítés folytatásához.  

   >[!TIP]
   >Tartsa meg az ablak, amely a TPM-szimulátor fut, nyissa meg a telepítés során, és a tesztelés. 

Az alábbi példa bemutatja egy automatikus telepítése a Windows-tárolókkal:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

Telepítésekor, és manuálisan-eszköz kiépítése, további paraméterek segítségével módosíthatja a telepítési többek között:
* Forgalomnak egy proxyn keresztül go
* A telepítő átirányítása egy offline könyvtár
* Deklaráljon egy adott ügynöknél tárolórendszerképet, és adja meg a hitelesítő adatokat, ha olyan privát beállításjegyzékbe
* Hagyja ki a Moby parancssori felület telepítése

Ezen telepítési lehetőségekről további információkért folytassa a cikk elolvasása, vagy hagyja ki kapcsolatos [összes telepítési paramétereket](#all-installation-parameters).

## <a name="update-an-existing-installation"></a>Meglévő telepítés frissítése

Ha már már telepítve van az IoT Edge-futtatókörnyezet előtt egy eszközön, és kiépítette azt az IoT Hub-identitással, majd egy egyszerűsített telepítési parancsfájlt használhatja. A jelző `-ExistingConfig` deklarálja, hogy az IoT Edge-konfigurációs fájl már létezik az eszközön. A konfigurációs fájlt az eszköz identitás, valamint a tanúsítványok és hálózati beállításokkal kapcsolatos információkat tartalmazza. Ez a telepítési lehetőség is használhatja, hogy az eszközt eredetileg kiosztott manuálisan vagy automatikusan. 

További információkért lásd: [az IoT Edge biztonsági démon és a futtatókörnyezet frissítése](how-to-update-iot-edge.md).

Ez a példa bemutatja, hogy a konfigurációs fájlt mutat, és használja a Windows-tárolók telepítés: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

A konfigurációs fájlt az IoT Edge telepítésekor a további paraméterek használatával módosítsa a telepítést, többek között:
* Forgalomnak egy proxyn keresztül go
* A telepítő átirányítása egy offline címtáron, vagy 
* Hagyja ki a Moby parancssori felület telepítése. 

Egy IoT Edge agent tárolórendszerképet a parancsfájl paramétereit, nyelvben nem deklarálható, mert az adatok már be van állítva a konfigurációs fájlban a korábbi telepítésből származó. Ha szeretné módosítani az ügynök tárolórendszerképet, tegye a config.yaml fájlban. 

Ezen telepítési lehetőségekről további információkért folytassa a cikk elolvasása, vagy hagyja ki kapcsolatos [összes telepítési paramétereket](#all-installation-parameters).

## <a name="offline-installation"></a>Kapcsolat nélküli telepítés

A telepítés során négy fájlok letöltése: 
* IoT Edge biztonsági démon (iotedgd) zip 
* Moby motor zip
* Moby CLI zip
* Visual C++ terjeszthető csomag (VC modul) msi

Töltse le a egyikét vagy mindegyikét időben ezeket a fájlokat az eszközre, majd a telepítési parancsfájl a fájlokat tartalmazó könyvtárra mutasson. A telepítő a könyvtár először ellenőrzi, és csak ezután letölti összetevőket, amelyek nem található. Ha offline elérhetők a négy fájlt, telepíthet nincs internetkapcsolat. Ez a funkció használatával bírálja felül egy vagy több összetevők online verzióját.  

A legfrissebb telepítési fájljait és a korábbi verziók, lásd: [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases)

Az offline összetevők telepítéséhez használja a `-OfflineInstallationPath` paramétert, és adja meg a fájl abszolút elérési útja. Például:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>Az összes telepítési paraméterek

Az előző szakaszokban bemutatott gyakori telepítési forgatókönyvek a példa bemutatja, hogyan módosíthatja a telepítési parancsfájl-paraméterek használata. Ez a témakör a referenciatábla az érvényes paraméterek, telepítse az IoT Edge segítségével. További információkért futtassa `get-help Install-SecurityDaemon -full` egy PowerShell-ablakban. 

IoT Edge telepítése egy meglévő konfiguráció esetén a telepítési parancs használható ezeket a gyakori paramétereket: 

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Manuális** | None | **Váltson a paraméter**. Minden telepítés vagy kézi, a DPS, JE nutné deklarovat vagy existingconfig.<br><br>Deklarálja, hogy Ön biztosítja az eszköz kapcsolati karakterlánc manuálisan regisztrálhatja az eszközt |
| **A DPS** | None | **Váltson a paraméter**. Minden telepítés vagy kézi, a DPS, JE nutné deklarovat vagy existingconfig.<br><br>Deklarálja, hogy Ön egy Device Provisioning szolgáltatás (DPS) hatókör-Azonosítót és az eszköz regisztrációs Azonosítót a DPS keresztül a rendelkezésre biztosítja.  |
| **ExistingConfig** | None | **Váltson a paraméter**. Minden telepítés vagy kézi, a DPS, JE nutné deklarovat vagy existingconfig.<br><br>Deklarálja, hogy egy config.yaml fájl már létezik az eszközön, és annak kiépítési adatait. |
| **DeviceConnectionString** | Az IoT Edge-eszköz regisztrálva az IoT Hub, szimpla idézőjelek között a kapcsolati karakterlánc | **Szükséges** manuális telepítésre. Ha nem ad meg egy kapcsolati karakterláncot a parancsprogram paramétereinek, a rendszer kéri, egy, a telepítés során. |
| **ScopeId** | Hatókör azonosítója, a társított az IoT Hub Device Provisioning Service egy példányát. | **Szükséges** DPS-telepítéshez. Ha nem ad meg egy hatókör azonosítója a parancsfájl paramétereit, a rendszer kéri, egy, a telepítés során. |
| **RegistrationId** | A regisztrációs azonosító, az eszköz által generált | **Szükséges** DPS-telepítéshez. Ha nem ad meg egy regisztrációs Azonosítót a parancsfájl paramétereit, a rendszer kéri, egy, a telepítés során. |
| **ContainerOs** | **Windows** vagy **Linux** | Nincs operációs rendszer van megadva tárolót, Linux-e az alapértelmezett érték. Windows-tárolókhoz egy tároló-motor fog szerepelni a telepítést. Linux-tárolók esetén a telepítés megkezdése előtt egy tároló-motor telepítenie kell. Linux-tárolók futó Windows-hasznos alkalmazásfejlesztési forgatókönyvet, de éles környezetben nem támogatott. |
| **Proxy** | Proxy URL-címe | Ez a paraméter tartalmazza, ha az eszköz csatlakozik az internethez proxykiszolgálón keresztül kell. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Paraméterek és értékek kivonattábla | A telepítés során számos webes kérések száma. Ebben a mezőben használja adott webes kérések paraméterek beállításához. Ezt a paramétert akkor hasznos, a proxykiszolgálókra vonatkozó hitelesítő adatok konfigurálása. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Könyvtár elérési útja | Ha ezt a paramétert tartalmaz, a telepítő ellenőrzi a directory a iotedged zip, a Moby motor zip, a Moby CLI zip és a telepítéshez szükséges VC futásidejű MSI-fájlok. Ha minden négy fájlok a könyvtárban, az IoT Edge közben telepíthet offline állapotban van. Ez a paraméter használatával bírálja felül egy adott összetevőre online verzióját. |
| **AgentImage** | IoT Edge-ügynök lemezkép URI-ja | Alapértelmezés szerint egy új IoT Edge-telepítés a legutóbbi működés közbeni címkét használja az IoT Edge-ügynök lemezképének. Használja ezt a paramétert, állítsa be a rendszerkép verziószámát az adott címkével, vagy adja meg a saját lemezképe. További információkért lásd: [megismerheti az IoT Edge-címkék](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Felhasználónév** | Tároló-beállításjegyzékhez tartozó felhasználónevével | Használja ezt a paramétert csak akkor, ha a - AgentImage paraméter értéke privát beállításjegyzékben lévő tárolóban. Adja meg a felhasználónevét és a beállításjegyzék elérését. |
| **Jelszó** | Biztonságos jelszó-karakterlánc | Használja ezt a paramétert csak akkor, ha a - AgentImage paraméter értéke privát beállításjegyzékben lévő tárolóban. Adja meg a jelszót a beállításjegyzék elérését. | 
| **SkipMobyCli** | None | Csak alkalmazható, ha Windows - ContainerOS van beállítva. Ne telepítse a Moby parancssori felület (docker.exe) $MobyInstallDirectory. |

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Az IoT Edge-szolgáltatás állapotát ellenőrizheti: 

```powershell
Get-Service iotedge
```

Vizsgálja meg a szolgáltatás naplók az elmúlt 5 percben használatával:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

És futó rendelkező modulok listája:

```powershell
iotedge list
```

Egy új telepítést követően a futó van megjelennie csak modul **edgeAgent**. Miután [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md), mások láthatja. 

## <a name="manage-module-containers"></a>A modul tárolók kezelése

Az IoT Edge szolgáltatás egy tároló-motor az eszközön futó igényel. Amikor telepít egy modul egy eszközön, az IoT Edge-futtatókörnyezet motort használja, tároló, kérje le a tároló rendszerképét a beállításjegyzék, a felhőben. Az IoT Edge szolgáltatás lehetővé teszi, hogy a modulok használhatnak, és kérheti le a naplókat, de néha előfordulhat, hogy használni kívánt a tároló motor használatával kommunikálhat a tárolón. 

A modul fogalmak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-modulok](iot-edge-modules.md). 

Ha Windows-tárolók futtatja a Windows IoT Edge-eszközön, az IoT Edge-telepítés a Moby tároló motor tartalmazza. Ha Linux-tárolókat a Windows fejlesztői gépen fejleszt, Docker asztali valószínűleg használ. A Moby motor az azonos szabványt, mint a Docker alapul, és úgy lett kialakítva, Docker asztali ugyanazon a gépen párhuzamosan futtatni. Emiatt ha szeretné a Moby motor által felügyelt cél tárolók akkor különösen céljaként, amelyek a motor Docker helyett. 

Például az összes Docker-rendszerképek listájában, használja a következő parancsot:

```powershell
docker images
```

Minden Moby rendszerképek listázásához, ugyanazt a parancsot egy mutatót a Moby motor módosítása: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

A motor URI szerepel a telepítési parancsfájl kimenete, vagy található tároló futásidejű beállítások szakaszában a config.yaml fájlt. 

![config.yaml moby_runtime URI-t](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Tárolók és az eszközön futó rendszerképeket segítségével parancsokkal kapcsolatos további információkért lásd: [Docker parancssori felületen](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Távolítsa el az IoT Edge

Ha el kívánja távolítani az IoT Edge-telepítés a Windows-eszközről, használja a következő parancsot egy rendszergazdai PowerShell-ablakban a. Ezzel a paranccsal eltávolítható az IoT Edge-futtatókörnyezet, valamint a meglévő konfiguráció és a Moby engine-adatok. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

Ha azt tervezi, IoT Edge újratelepítése az eszközön, hagyja ki a `-DeleteConfig` és `-DeleteMobyDataRoot` paramétereket, hogy telepítse újra a biztonsági démon később a meglévő konfigurációs adatokkal. 

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha az IoT Edge megfelelően telepítése problémák merülnek fel, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

Egy meglévő telepítéshez az IoT Edge a legújabb verzióra frissítéséhez lásd [az IoT Edge biztonsági démon és a futtatókörnyezet frissítése](how-to-update-iot-edge.md).

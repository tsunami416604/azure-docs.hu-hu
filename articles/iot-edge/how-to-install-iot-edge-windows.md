---
title: Telepítse az Azure IoT Edge Windows |} A Microsoft Docs
description: Az Azure IoT Edge a Windows 10-es, a Windows Server és a Windows IoT Core telepítési utasításokat
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: f67f24cab907c3fe9998704e0a0a85d5b29f60a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808860"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Az Azure IoT Edge-futtatókörnyezet telepíthető Windows

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható. 

Az IoT Edge-futtatókörnyezet kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk az Azure IoT Edge-futtatókörnyezet telepíthető a Windows x64 (Intel vagy AMD) lépéseit sorolja fel a rendszer Windows-tárolók használatával.

> [!NOTE]
> Egy ismert probléma a Windows operációs rendszer megakadályozza, hogy alvó állapotba, és az IoT Edge-modulok (folyamat logikailag Windows Nano Server-tárolók) futtatásakor hibernálást használatára való átállást. A probléma hatással van az eszköz akkumulátor-élettartamát.
>
> Áthidaló megoldásként használja a parancsot `Stop-Service iotedge` minden futó IoT Edge-modulok leállítja ezeket az állapotokat az energiagazdálkodási használata előtt. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

Linux-tárolók használata a Windows rendszereken a javasolt vagy támogatott éles konfiguráció nem az Azure IoT Edge-hez. Azonban ez használható fejlesztési és tesztelési célra. További tudnivalókért lásd: [használata IoT Edge a Linux-tárolók futtatásához Windows](how-to-install-iot-edge-windows-with-linux.md).

Mit tartalmaz az IoT Edge a legújabb verzió kapcsolatos információkért lásd: [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Előfeltételek

Ebben a szakaszban használja, tekintse át, hogy a Windows-eszköz IoT Edge is támogatják-e, és előkészítéséhez, telepítés előtt egy tároló-motor. 

### <a name="supported-windows-versions"></a>Támogatott Windows-verziók

Fejlesztési és tesztelési forgatókönyvek esetén az Azure IoT Edge a Windows-tárolók Windows 10-es vagy Windows Server 2019 (build 17763), amely támogatja a containers szolgáltatást bármely verziója is telepíthető. Információ arról, hogy mely a termelési forgatókönyvekhez jelenleg támogatott operációs rendszerekről: [Azure IoT Edge által támogatott rendszerek](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Egy tároló-motor előkészítése 

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis](https://www.opencontainers.org/) container-motor. A termelési forgatókönyvekhez használja a Moby motor szerepel a telepítési parancsfájl a Windows-eszközön a Windows-tárolók futtatásához. 

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge egy új eszköz telepítése

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagok feltételei vonatkoznak rá a licencet (a címtárban licenccel) a csomagokban található. Kérjük, olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

Egy PowerShell-szkript letölti és telepíti az Azure IoT Edge biztonsági démon. A biztonsági démon ezután elindítja az első két futásidejű modulok, az IoT Edge ügynök, amely lehetővé teszi az egyéb modulok távoli telepítések. 

Ha egy eszköz először telepíti az IoT Edge-futtatókörnyezet, kell biztosítsa az eszköz IoT hubról identitást. Egy adott IoT Edge-eszköz kiépítése az IoT Hub által biztosított eszközök kapcsolati karakterlánc segítségével manuálisan. Másik lehetőségként használhatja a Device Provisioning Service-eszközök automatikus kiépítésére, amely akkor hasznos, ha sok eszköz beállításához van. Üzembe helyezési válaszaitól függően válassza ki a megfelelő telepítési parancsfájlt. 

A következő szakaszok ismertetik a gyakori alkalmazási helyzetek és az IoT Edge telepítési parancsfájl paraméterek egy új eszközön. 

### <a name="option-1-install-and-manually-provision"></a>Option 1: Telepítés és manuális üzembe helyezése

Az első lehetőségnél adjon meg egy **eszköz kapcsolati karakterláncának** regisztrálhatja az eszközt az IoT Hub által generált. 

Ez a példa bemutatja a manuális telepítés a Windows-tárolókkal:

1. Ha még nem tette, egy új IoT Edge-eszköz regisztrálása és lekérése a **eszköz kapcsolati karakterláncának**. Másolja a kapcsolati karakterláncot, ez a rész későbbi használat céljából. Ezt a lépést a következő eszközök használatával is elvégezheti:

   * [Azure Portal](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Futtassa a Powershellt rendszergazdaként.

   >[!NOTE]
   >PowerShell AMD64 munkamenet használata IoT Edge segítségével, nem (x86) PowerShell telepítéséhez. Ha nem biztos abban, hogy melyik munkamenet típusa használ, futtassa a következő parancsot:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. A **üzembe helyezés – IoTEdge** parancs ellenőrzi, hogy a Windows-gépen valamelyik támogatott verzióra, bekapcsolja a tárolók funkciót, és ezután letölti a moby-futtatókörnyezet és az IoT Edge-futtatókörnyezet. A parancs alapértelmezés szerint, a Windows-tárolók használatával. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Ezen a ponton IoT Core-eszközök automatikus újraindítását. Egyéb Windows 10-es vagy Windows Server-eszközök késztethetik, újra kell indítani. Ha igen, most indítsa újra az eszközt. Ha az eszköz készen áll, PowerShell futtatása rendszergazdaként újra.

5. A **Initialize-IoTEdge** parancsot az IoT Edge-futtatókörnyezet konfigurálása a gépen. A parancs alapértelmezés szerint a Windows-tárolókkal manuális kiépítési. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Amikor a rendszer kéri, adja meg az eszköz kapcsolati karakterláncát, amely az 1. lépésben lekért. Az eszköz kapcsolati karakterláncának társítja a fizikai eszköz az IoT Hub-Eszközazonosító. 

   Az eszköz kapcsolati karakterláncának formátuma a következő vesz igénybe, és nem tartalmazhat idézőjelek közé: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Szereplő lépések segítségével [sikeres telepítésének ellenőrzése](#verify-successful-installation) ellenőrizheti az állapotot az IoT Edge az eszközön. 

Telepítésekor, és manuálisan-eszköz kiépítése, további paraméterek segítségével módosíthatja a telepítési többek között:
* Forgalomnak egy proxyn keresztül go
* A telepítő átirányítása egy offline könyvtár
* Deklaráljon egy adott ügynöknél tárolórendszerképet, és adja meg a hitelesítő adatokat, ha olyan privát beállításjegyzékbe

Ezen telepítési lehetőségekről további információkért folytassa a további tudnivalók [összes telepítési paramétereket](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Option 2: Telepítés, és automatikusan üzembe helyezése

A második lehetőség, a üzembe helyezi az eszközt az IoT Hub Device Provisioning Service használatával. Adja meg a **hatókör azonosítója** a Device Provisioning Service-példányból, és a **regisztrációs azonosító** az eszközről.

A következő példa bemutatja egy automatikus telepítése a Windows-tárolókkal:

1. Kövesse a [létrehozásával és kiépítésével egy szimulált TPM-eszköz IoT Edge-eszközön a Windows](how-to-auto-provision-simulated-device-windows.md) a Device Provisioning Service beállítása és lekérése a **hatókör azonosítója**, TPM-eszköz szimulálása és lekérése a **Regisztrációs azonosító**, majd hozzon létre egyéni regisztrációt. Miután az eszköz regisztrálva van az IoT hub, folytassa a telepítési lépéseket.  

   >[!TIP]
   >Tartsa meg az ablak, amely a TPM-szimulátor fut, nyissa meg a telepítés során, és a tesztelés. 

2. Futtassa a Powershellt rendszergazdaként.

   >[!NOTE]
   >PowerShell AMD64 munkamenet használata IoT Edge segítségével, nem (x86) PowerShell telepítéséhez. Ha nem biztos abban, hogy melyik munkamenet típusa használ, futtassa a következő parancsot:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. A **üzembe helyezés – IoTEdge** parancs ellenőrzi, hogy a Windows-gépen valamelyik támogatott verzióra, bekapcsolja a tárolók funkciót, és ezután letölti a moby-futtatókörnyezet és az IoT Edge-futtatókörnyezet. A parancs alapértelmezés szerint, a Windows-tárolók használatával. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Ezen a ponton IoT Core-eszközök automatikus újraindítását. Egyéb Windows 10-es vagy Windows Server-eszközök késztethetik, újra kell indítani. Ha igen, most indítsa újra az eszközt. Ha az eszköz készen áll, PowerShell futtatása rendszergazdaként újra.

6. A **Initialize-IoTEdge** parancsot az IoT Edge-futtatókörnyezet konfigurálása a gépen. A parancs alapértelmezés szerint a Windows-tárolókkal manuális kiépítési. Használja a `-Dps` használata a Device Provisioning Service helyett a manuális kiépítési jelző.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. Amikor a rendszer kéri, adja meg a Device Provisioning Service a hatókör-azonosító és az eszközről, mindkettő kell a lekérdezés az 1. lépésben a regisztrációs azonosító.

8. Szereplő lépések segítségével [sikeres telepítésének ellenőrzése](#verify-successful-installation) ellenőrizheti az állapotot az IoT Edge az eszközön. 

Telepítésekor, és manuálisan-eszköz kiépítése, további paraméterek segítségével módosíthatja a telepítési többek között:
* Forgalomnak egy proxyn keresztül go
* A telepítő átirányítása egy offline könyvtár
* Deklaráljon egy adott ügynöknél tárolórendszerképet, és adja meg a hitelesítő adatokat, ha olyan privát beállításjegyzékbe

Ezen telepítési lehetőségekről további információkért folytassa a cikk elolvasása, vagy hagyja ki kapcsolatos [összes telepítési paramétereket](#all-installation-parameters).

## <a name="offline-installation"></a>Kapcsolat nélküli telepítés

A telepítés során két fájlok letöltése: 
* Az IoT Edge biztonsági démon (iotedged), Moby container-motor és Moby CLI tartalmazza a Microsoft Azure IoT Edge kabinetfájl.
* Visual C++ terjeszthető csomag (VC modul) msi

Töltse le az egyik vagy mindkét ezeket a fájlokat a kívánt időben az eszközre, majd a telepítési parancsfájl a fájlokat tartalmazó könyvtárra mutasson. A telepítő a könyvtár először ellenőrzi, és csak ezután letölti összetevőket, amelyek nem található. Ha a kapcsolat nélküli módban érhetők el az összes fájlját, telepíthet nincs internetkapcsolat. Ez a funkció segítségével telepítse az összetevők adott verzióját.  

A legújabb IoT Edge telepítési fájljait és a korábbi verziók, lásd: [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases).

Az offline összetevők telepítéséhez használja a `-OfflineInstallationPath` paraméter az üzembe helyezés IoTEdge részeként parancsot, és adja meg a fájl abszolút elérési útja. Például:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

A frissítés-IoTEdge parancsot, a cikk későbbi részében jelent meg a kapcsolat nélküli telepítés path paraméterrel is használhatja. 

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ellenőrizze az IoT Edge-szolgáltatás állapotát. Futtatásával kell szerepelnie.  

```powershell
Get-Service iotedge
```

Vizsgálja meg a szolgáltatási naplók az elmúlt 5 percben. Csak végzett telepítése az IoT Edge-futtatókörnyezet, jelenhet meg, a futó között eltelt idő hiba **üzembe helyezés – IoTEdge** és **Initialize-IoTEdge**. Ezek a hibák várhatóan, mivel a szolgáltatás elindítása előtt konfigurált próbál. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futó modulok listája. Egy új telepítést követően a futó van megjelennie csak modul **edgeAgent**. Miután [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md), mások láthatja. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>A modul tárolók kezelése

Az IoT Edge szolgáltatás egy tároló-motor az eszközön futó igényel. Amikor telepít egy modul egy eszközön, az IoT Edge-futtatókörnyezet motort használja, tároló, kérje le a tároló rendszerképét a beállításjegyzék, a felhőben. Az IoT Edge szolgáltatás lehetővé teszi, hogy a modulok használhatnak, és kérheti le a naplókat, de néha előfordulhat, hogy használni kívánt a tároló motor használatával kommunikálhat a tárolón. 

A modul fogalmak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-modulok](iot-edge-modules.md). 

Ha Windows-tárolók futtatja a Windows IoT Edge-eszközön, az IoT Edge-telepítés a Moby tároló motor tartalmazza. A Moby motor az azonos szabványt, mint a Docker alapul, és úgy lett kialakítva, Docker asztali ugyanazon a gépen párhuzamosan futtatni. Emiatt ha szeretné a Moby motor által felügyelt cél tárolók akkor különösen céljaként, amelyek a motor Docker helyett. 

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

## <a name="update-an-existing-installation"></a>Meglévő telepítés frissítése

Ha már már telepítve van az IoT Edge-futtatókörnyezet előtt egy eszközön, és kiépítette azt az IoT Hub-identitással, majd frissítheti a modul nélkül adja meg újból az eszközadatokat. 

További információkért lásd: [az IoT Edge biztonsági démon és a futtatókörnyezet frissítése](how-to-update-iot-edge.md).

Ez a példa bemutatja, hogy a konfigurációs fájlt mutat, és használja a Windows-tárolók telepítés: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Amikor frissíti az IoT Edge, további paraméterek használatával módosíthatja a frissítés többek között:
* Nyissa meg egy proxykiszolgálón keresztül a forgalmat, vagy
* A telepítő átirányítása egy offline könyvtár 
* Szükség esetén a kérdés nélkül újraindítása

Az IoT Edge agent tárolórendszerkép szkriptparaméterek nyelvben nem deklarálható, mert ezt az információt már be van állítva a konfigurációs fájlban a korábbi telepítésből származó. Ha szeretné módosítani az ügynök tárolórendszerképet, tegye a config.yaml fájlban. 

További információ ezen beállítások frissítése, a paranccsal `Get-Help Update-IoTEdge -full` vagy a [összes telepítési paramétereket](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Távolítsa el az IoT Edge

Ha el kívánja távolítani az IoT Edge-telepítés a Windows-eszközről, használja a következő parancsot egy rendszergazdai PowerShell-ablakban a. Ezzel a paranccsal eltávolítható az IoT Edge-futtatókörnyezet, valamint a meglévő konfiguráció és a Moby engine-adatok. 

```powershell
Uninstall-IoTEdge
```

Az Uninstall-IoTEdge parancs nem működik Windows IoT Core-on. Távolítsa el az IoT Edge Windows IoT Core-eszközökről, telepítse újra a Windows IoT Core-rendszerképet kell. 

További információ az eltávolítási beállítások, a parancs használata `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Az összes telepítési paraméterek

Az előző szakaszokban bemutatott gyakori telepítési forgatókönyvek a példa bemutatja, hogyan módosíthatja a telepítési parancsfájl-paraméterek használata. Ez a szakasz tartalmazza a referencia táblák a gyakori paraméterek telepítése, frissítése vagy távolítsa el az IoT Edge segítségével. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Az üzembe helyezés – IoTEdge parancs letölti és telepíti az IoT Edge biztonsági démon és annak függőségeit. A központi telepítési parancs elfogadja ezeket a gyakori paramétereket, többek között. A teljes listát a parancs használata `Get-Help Deploy-IoTEdge -full`.  

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs operációs rendszer van megadva, a Windows rendszer az alapértelmezett érték.<br><br>Windows-tárolókhoz az IoT Edge a telepítés része moby tároló motort használja. Linux-tárolók esetén a telepítés megkezdése előtt egy tároló-motor telepítenie kell. |
| **Proxy** | Proxy URL | Ez a paraméter tartalmazza, ha az eszköz csatlakozik az internethez proxykiszolgálón keresztül kell. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Könyvtár elérési útja | Ha ezt a paramétert tartalmaz, a telepítő ellenőrzi az IoT Edge cab és a telepítéshez szükséges VC futásidejű MSI-fájlok a listán szereplő könyvtár. A címtárban nem található fájlok letöltődnek. Ha mindkét fájlok a könyvtárban, az IoT Edge internetkapcsolat nélkül is telepítheti. Ez a paraméter használatával egy adott verzió használatához. |
| **InvokeWebRequestParameters** | Paraméterek és értékek kivonattábla | A telepítés során számos webes kérések száma. Ebben a mezőben használja adott webes kérések paraméterek beállításához. Ezt a paramétert akkor hasznos, a proxykiszolgálókra vonatkozó hitelesítő adatok konfigurálása. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Egyik sem | Ez a jelző lehetővé teszi, hogy a telepítési parancsfájl a számítógép újraindítása nélkül kérő üzenet, szükség esetén. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Az Initialize-IoTEdge parancsot az eszköz kapcsolati karakterláncát, és a műveleti adatokat az IoT Edge konfigurálja. Ez a parancs által létrehozott információk nagy részét majd a iotedge\config.yaml fájlban tárolja. Az inicializálás parancsot fogad el ezeket a gyakori paramétereket, többek között. A teljes listát a parancs használata `Get-Help Initialize-IoTEdge -full`. 

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Manuális** | None | **Váltson a paraméter**. Ha nincs kiépítési típus van megadva, manuális alapértelmezett értéke.<br><br>Deklarálja, hogy Ön biztosítja az eszköz kapcsolati karakterlánc manuálisan regisztrálhatja az eszközt |
| **A DPS** | None | **Váltson a paraméter**. Ha nincs kiépítési típus van megadva, manuális alapértelmezett értéke.<br><br>Deklarálja, hogy Ön egy Device Provisioning szolgáltatás (DPS) hatókör-Azonosítót és az eszköz regisztrációs Azonosítót a DPS keresztül a rendelkezésre biztosítja.  |
| **DeviceConnectionString** | Az IoT Edge-eszköz regisztrálva az IoT Hub, szimpla idézőjelek között a kapcsolati karakterlánc | **Szükséges** manuális telepítésre. Ha nem ad meg egy kapcsolati karakterláncot a parancsprogram paramétereinek, a rendszer kéri, egy, a telepítés során. |
| **ScopeId** | Hatókör azonosítója, a társított az IoT Hub Device Provisioning Service egy példányát. | **Szükséges** DPS-telepítéshez. Ha nem ad meg egy hatókör azonosítója a parancsfájl paramétereit, a rendszer kéri, egy, a telepítés során. |
| **RegistrationId** | A regisztrációs azonosító, az eszköz által generált | **Szükséges** DPS-telepítéshez. Ha nem ad meg egy regisztrációs Azonosítót a parancsfájl paramétereit, a rendszer kéri, egy, a telepítés során. |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs operációs rendszer van megadva, a Windows rendszer az alapértelmezett érték.<br><br>Windows-tárolókhoz az IoT Edge a telepítés része moby tároló motort használja. Linux-tárolók esetén a telepítés megkezdése előtt egy tároló-motor telepítenie kell. |
| **InvokeWebRequestParameters** | Paraméterek és értékek kivonattábla | A telepítés során számos webes kérések száma. Ebben a mezőben használja adott webes kérések paraméterek beállításához. Ezt a paramétert akkor hasznos, a proxykiszolgálókra vonatkozó hitelesítő adatok konfigurálása. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **AgentImage** | IoT Edge-ügynök lemezkép URI-ja | Alapértelmezés szerint egy új IoT Edge-telepítés a legutóbbi működés közbeni címkét használja az IoT Edge-ügynök lemezképének. Használja ezt a paramétert, állítsa be a rendszerkép verziószámát az adott címkével, vagy adja meg a saját lemezképe. További információkért lásd: [megismerheti az IoT Edge-címkék](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Felhasználónév** | Tároló-beállításjegyzékhez tartozó felhasználónevével | Használja ezt a paramétert csak akkor, ha a - AgentImage paraméter értéke privát beállításjegyzékben lévő tárolóban. Adja meg a felhasználónevét és a beállításjegyzék elérését. |
| **Jelszó** | Biztonságos jelszó-karakterlánc | Használja ezt a paramétert csak akkor, ha a - AgentImage paraméter értéke privát beállításjegyzékben lévő tárolóban. Adja meg a jelszót a beállításjegyzék elérését. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs tárolót, az operációs rendszer van megadva, a Windows az alapértelmezett érték. Windows-tárolókhoz egy tároló-motor fog szerepelni a telepítést. Linux-tárolók esetén a telepítés megkezdése előtt egy tároló-motor telepítenie kell. |
| **Proxy** | Proxy URL | Ez a paraméter tartalmazza, ha az eszköz csatlakozik az internethez proxykiszolgálón keresztül kell. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Paraméterek és értékek kivonattábla | A telepítés során számos webes kérések száma. Ebben a mezőben használja adott webes kérések paraméterek beállításához. Ezt a paramétert akkor hasznos, a proxykiszolgálókra vonatkozó hitelesítő adatok konfigurálása. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Könyvtár elérési útja | Ha ezt a paramétert tartalmaz, a telepítő ellenőrzi az IoT Edge cab és a telepítéshez szükséges VC futásidejű MSI-fájlok a listán szereplő könyvtár. A címtárban nem található fájlok letöltődnek. Ha mindkét fájlok a könyvtárban, az IoT Edge internetkapcsolat nélkül is telepítheti. Ez a paraméter használatával egy adott verzió használatához. |
| **RestartIfNeeded** | Egyik sem | Ez a jelző lehetővé teszi, hogy a telepítési parancsfájl a számítógép újraindítása nélkül kérő üzenet, szükség esetén. |


### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Kényszerített** | Egyik sem | Ez a jelző arra kényszeríti az eltávolítás, abban az esetben távolítsa el az előző próbálkozás sikertelen volt. 
| **RestartIfNeeded** | Egyik sem | Ez a jelző lehetővé teszi, hogy az eltávolítási parancsprogramot, a számítógép újraindítása nélkül kérő üzenet, szükség esetén. |


## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha az IoT Edge megfelelően telepítése problémák merülnek fel, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

Egy meglévő telepítéshez az IoT Edge a legújabb verzióra frissítéséhez lásd [az IoT Edge biztonsági démon és a futtatókörnyezet frissítése](how-to-update-iot-edge.md).

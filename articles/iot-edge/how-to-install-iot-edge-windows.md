---
title: A Azure IoT Edge telepítése Windows rendszeren | Microsoft Docs
description: Azure IoT Edge telepítési utasítások a Windows 10, a Windows Server és a Windows IoT Core rendszerhez
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 513cf477e8c2899da17ee8e9bdfdb9ad2bedd159
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828093"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>A Azure IoT Edge futtatókörnyezet telepítése Windows rendszeren

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható. 

További információ a IoT Edge futtatókörnyezetről: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

Ez a cikk azokat a lépéseket ismerteti, amelyekkel Windows-tárolók használatával telepítheti a Azure IoT Edge futtatókörnyezetet Windows x64 (AMD/Intel) rendszerre.

> [!NOTE]
> Egy ismert Windows operációs rendszerbeli probléma megakadályozza az alvó állapotba és a hibernált állapotba való áttérést, ha IoT Edge modulokat (folyamat-elkülönített Windows Nano Server-tárolókat) futtatnak. Ez a probléma hatással van az eszköz akkumulátorának élettartamára.
>
> Megkerülő megoldásként használja a `Stop-Service iotedge` parancsot a futó IoT Edge modulok leállításához, mielőtt használni kezdené ezeket a energiaellátási állapotokat. 

A Linux-tárolók használata Windows rendszereken nem ajánlott vagy támogatott éles környezetben a Azure IoT Edge számára. Azonban ez használható fejlesztési és tesztelési célra. További információ: [IoT Edge használata Windows rendszeren Linux-tárolók futtatásához](how-to-install-iot-edge-windows-with-linux.md).

További információ a IoT Edge legújabb verziójában található információkról: [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Előfeltételek

Ebből a szakaszból megtekintheti, hogy a Windows-eszköz támogatja-e a IoT Edget, és hogy a telepítés előtt előkészítse-e a tároló motorját. 

### <a name="supported-windows-versions"></a>Támogatott Windows-verziók

Fejlesztési és tesztelési forgatókönyvek esetén a Windows-tárolók Azure IoT Edge a Windows 10 vagy a Windows Server 2019 (Build 17763) bármely olyan verziójára telepíthető, amely támogatja a containers szolgáltatást. Az éles környezetekben jelenleg támogatott operációs rendszerekről a [Azure IoT Edge támogatott rendszerek](support.md#operating-systems)című témakörben olvashat bővebben. 

A IoT Core-eszközöknek tartalmaznia kell a IoT Core-Windows-tárolók választható funkcióját a IoT Edge futtatókörnyezet támogatásához. A következő parancs [távoli PowerShell-munkamenetben](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) való használatával ellenőrizhető, hogy a Windows-tárolók támogatottak-e az eszközön: 

```powershell
Get-Service vmcompute
```

Ha a szolgáltatás jelen van, akkor sikeres választ kaphat a **Futtatás**alatt felsorolt szolgáltatás állapotáról. Ha a vmcompute szolgáltatás nem található, az eszköz nem felel meg a IoT Edge követelményeinek. Forduljon a hardver szolgáltatóhoz, és kérje a funkció támogatását. 

### <a name="prepare-for-a-container-engine"></a>A Container Engine előkészítése 

A Azure IoT Edge egy [OCI-kompatibilis](https://www.opencontainers.org/) tároló motorra támaszkodik. Éles környezetekben a telepítési parancsfájlban található Moby Engine használatával Windows-tárolókat futtathat a Windows-eszközön. 

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge telepítése új eszközre

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagok feltételei vonatkoznak rá a licencet (a címtárban licenccel) a csomagokban található. Kérjük, olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

A PowerShell-parancsfájlok letöltik és telepítik a Azure IoT Edge biztonsági démont. A biztonsági démon ezután elindítja az első két futásidejű modult, a IoT Edge ügynököt, amely lehetővé teszi más modulok távoli központi telepítését. 

>[!TIP]
>A IoT Core-eszközök esetében javasoljuk, hogy a telepítési parancsokat RemotePowerShell-munkamenet használatával futtassa. További információ: a [PowerShell használata a Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Amikor első alkalommal telepíti a IoT Edge futtatókörnyezetet az eszközön, az eszközt egy IoT hub identitásával kell kiépíteni. Egy IoT Edge eszköz manuálisan is kiépíthető a IoT Hub által biztosított eszköz-összekapcsolási karakterlánc használatával. Vagy a Device kiépítési szolgáltatás (DPS) használatával automatikusan kiépítheti az eszközöket, ami hasznos lehet, ha több eszköz is be van állítva. Üzembe helyezési válaszaitól függően válassza ki a megfelelő telepítési parancsfájlt. 

A következő szakaszok ismertetik az új eszközön a IoT Edge telepítési parancsfájl általános használati eseteit és paramétereit. 

### <a name="option-1-install-and-manually-provision"></a>1\. módszer: Telepítés és manuális üzembe helyezése

Ebben az első lehetőségben egy IoT Hub által generált **eszköz-kapcsolódási karakterláncot** biztosít az eszköz kiépítéséhez. 

Ez a példa egy manuális telepítést mutat be Windows-tárolókkal:

1. Ha még nem tette meg, regisztráljon egy új IoT Edge eszközt, és kérje le az **eszköz csatlakoztatási karakterláncát**. Másolja a kapcsolódási karakterláncot a szakasz későbbi részében való használatra. Ezt a lépést a következő eszközök használatával végezheti el:

   * [Azure Portal](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Futtassa a PowerShellt rendszergazdaként.

   >[!NOTE]
   >A PowerShell AMD64-munkamenetének használatával telepítse a IoT Edge, nem a PowerShellt (x86). Ha nem biztos abban, hogy melyik munkamenet-típust használja, futtassa a következő parancsot:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Az **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows rendszerű számítógép támogatott verziójú-e, bekapcsolja a tárolók szolgáltatást, majd letölti a Moby Runtime és a IoT Edge futtatókörnyezetet. A parancs alapértelmezés szerint Windows-tárolókat használ. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Ezen a ponton a IoT Core-eszközök automatikusan újraindulnak. Előfordulhat, hogy a Windows 10 vagy Windows Server rendszerű eszközök újraindítását kérik. Ha igen, indítsa újra az eszközt. Ha az eszköz elkészült, futtassa újra a PowerShellt rendszergazdaként.

5. Az **inicializálás-IoTEdge** parancs konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a Windows-tárolók manuális kiépítés. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Ha a rendszer kéri, adja meg az 1. lépésben lekért eszköz-kapcsolódási karakterláncot. Az eszköz-csatlakoztatási karakterlánc a fizikai eszközt a IoT Hub eszköz-azonosítójával társítja. 

   Az eszköz-kapcsolatok karakterlánca a következő formátumot veszi figyelembe, és nem tartalmazhat idézőjeleket:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Az eszközön lévő IoT Edge állapotának ellenőrzéséhez kövesse a [sikeres telepítés ellenőrzése](#verify-successful-installation) című témakörben leírtakat. 

Ha manuálisan telepít és helyez üzembe egy eszközt, további paraméterek használatával módosíthatja a telepítést, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom
* A telepítő átirányítása offline könyvtárba
* Egy adott ügynök-tároló rendszerképének deklarálása, és a hitelesítő adatok megadása, ha azok egy privát beállításjegyzékben találhatók

További információ ezekről a telepítési lehetőségekről: ugorjon előre az [összes telepítési paraméter](#all-installation-parameters)megismeréséhez.

### <a name="option-2-install-and-automatically-provision"></a>2\. lehetőség: Telepítés, és automatikusan üzembe helyezése

Ebben a második lehetőségben az eszközt a IoT Hub Device Provisioning Service használatával kell kiépíteni. Adja meg az eszköz kiépítési szolgáltatásának **hatókör-azonosítóját** , valamint az Ön által előnyben részesített [igazolási mechanizmusra](../iot-dps/concepts-security.md#attestation-mechanism)vonatkozó egyéb információkat:

* [Szimulált TPM Edge-eszköz létrehozása és kiépítése Windows rendszeren](how-to-auto-provision-simulated-device-windows.md)
* [IoT Edge-eszköz létrehozása és kiépítése a szimmetrikus kulcs igazolásával](how-to-auto-provision-symmetric-keys.md)

Amikor automatikusan telepít és kiépít egy eszközt, további paramétereket is használhat a telepítés módosításához, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom
* A telepítő átirányítása offline könyvtárba
* Egy adott ügynök-tároló rendszerképének deklarálása, és a hitelesítő adatok megadása, ha azok egy privát beállításjegyzékben találhatók

A telepítési lehetőségekkel kapcsolatos további információkért olvassa el a jelen cikk olvasását, vagy ugorjon az [összes telepítési paraméter](#all-installation-parameters)megismerése elemre.

## <a name="offline-installation"></a>Offline telepítés

A telepítés során két fájl töltődik le:

* Microsoft Azure IoT Edge CAB, amely tartalmazza a IoT Edge biztonsági démont (iotedged), a Moby Container Engine-t és a Moby CLI-t.
* Vizuális C++ Újraterjeszthető csomag (VC Runtime) MSI

A fájlok közül egyet vagy mindkettőt bármikor letöltheti az eszközre, majd a telepítési parancsfájlt a fájlokat tartalmazó könyvtárba irányíthatja. A telepítő először ellenőrzi a könyvtárat, és csak azokat az összetevőket tölti le, amelyek nem találhatók. Ha az összes fájl offline állapotban érhető el, az internetkapcsolat nélkül is telepíthető. Ezzel a funkcióval az összetevők egy adott verzióját is telepítheti.  

A legújabb IoT Edge telepítési fájlokról a korábbi verziók mellett lásd: [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases).

Az offline összetevőkkel való telepítéshez használja `-OfflineInstallationPath` a paramétert az Deploy-IoTEdge parancs részeként, és adja meg a fájl könyvtárának abszolút elérési útját. Például:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Használhatja az offline telepítési útvonal paramétert is az Update-IoTEdge paranccsal, amely a cikk későbbi részében is elérhető. 

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ellenőrizze az IoT Edge-szolgáltatás állapotát. A lista futtatásaként kell szerepelnie.  

```powershell
Get-Service iotedge
```

Vizsgálja meg a szolgáltatási naplók az elmúlt 5 percben. Ha befejezte a IoT Edge futtatókörnyezet telepítését, akkor előfordulhat, hogy az **üzembe helyezés-IoTEdge** és az **inicializálás-IoTEdge**futtatása között elérkezett hibák listája látható. A rendszer ezeket a hibákat várta, mivel a szolgáltatás a konfigurálás előtt megpróbálja elindítani a szolgáltatást. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futó modulok listája. Új telepítés után az egyetlen modulnak kell megjelennie a **edgeAgent**. A [IoT Edge-modulok első üzembe helyezése](how-to-deploy-modules-portal.md) után a **edgeHub**másik rendszermodulja is elindul az eszközön. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Modul-tárolók kezelése

A IoT Edge szolgáltatáshoz az eszközön futó tároló motor szükséges. Amikor modult telepít egy eszközre, a IoT Edge futtatókörnyezet a tároló motor használatával kéri le a tároló lemezképét a felhőben lévő beállításjegyzékből. A IoT Edge szolgáltatás lehetővé teszi a modulok interakcióját és a naplók beolvasását, de előfordulhat, hogy a tároló motort szeretné használni a tárolóval való interakcióhoz. 

További információ a modulokkal kapcsolatos fogalmakról: [Azure IoT Edge modulok megismerése](iot-edge-modules.md). 

Ha Windows-tárolókat futtat a Windows IoT Edge eszközön, akkor a IoT Edge telepítés tartalmazza a Moby Container Engine-t. A Moby Engine a Docker-vel azonos szabványokon alapult, és úgy tervezték, hogy párhuzamosan fusson ugyanazon a gépen, mint a Docker Desktop. Ezért ha a Moby Engine által felügyelt tárolókat kíván célozni, a Docker helyett kifejezetten a motort kell megcéloznia. 

Ha például az összes Docker-lemezképet szeretné listázni, használja a következő parancsot:

```powershell
docker images
```

Az összes Moby-lemezkép listázásához módosítsa ugyanazt a parancsot, és mutasson rá a Moby Engine-re: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

A motor URI-ja megjelenik a telepítési parancsfájl kimenetében, vagy megkeresheti a config. YAML fájl Container Runtime Settings szakaszában. 

![moby_runtime URI a config. YAML](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

További információ az eszközön futó tárolókkal és képekkel való kommunikációhoz használható parancsokról: Docker [parancssori felületek](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Meglévő telepítés frissítése

Ha már telepítette az IoT Edge futtatókörnyezetet az eszközön, és kiépíti azt egy IoT Hub identitásával, akkor az eszköz adatainak újbóli megadása nélkül is frissítheti a futtatókörnyezetet. 

További információ: [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md).

Ez a példa egy olyan telepítést mutat be, amely egy meglévő konfigurációs fájlra mutat, és Windows-tárolókat használ: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

IoT Edge frissítésekor további paramétereket is használhat a frissítés módosításához, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom, vagy
* A telepítő átirányítása offline könyvtárba 
* Újraindítás anélkül, hogy szükség lenne rá

Parancsfájl-paraméterekkel nem deklarálható IoT Edge ügynök-tároló rendszerképe, mert ez az információ már be van állítva az előző telepítés konfigurációs fájljába. Ha módosítani szeretné az ügynök tárolójának rendszerképét, tegye a következőt a config. YAML fájlban. 

A frissítési lehetőségekkel kapcsolatos további információkért használja az parancsot `Get-Help Update-IoTEdge -full` , vagy tekintse meg az [összes telepítési paramétert](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>IoT Edge eltávolítása

Ha el szeretné távolítani a IoT Edge telepítését a Windows-eszközről, használja a következő parancsot egy felügyeleti PowerShell-ablakból. Ez a parancs eltávolítja a IoT Edge futtatókörnyezetet, valamint a meglévő konfigurációt és a Moby Engine-adatait. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Az uninstall-IoTEdge parancs nem működik a Windows IoT Core-on. Ha el szeretné távolítani IoT Edget a Windows IoT Core-eszközökről, újra kell telepítenie a Windows IoT Core rendszerképet. 

Az eltávolítási lehetőségekkel kapcsolatos további információkért használja az parancsot `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Minden telepítési paraméter

Az előző részekben gyakori telepítési forgatókönyvek jelentek meg, példákkal, hogy miként lehet paramétereket használni a telepítési parancsfájl módosításához. Ez a szakasz a IoT Edge telepítésére, frissítésére és eltávolítására szolgáló közös paraméterek táblázatait tartalmazza. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Az Deploy-IoTEdge parancs letölti és telepíti a IoT Edge biztonsági démont és annak függőségeit. Az üzembe helyezési parancs elfogadja ezeket a közös paramétereket többek között. A teljes listához használja az parancsot `Get-Help Deploy-IoTEdge -full`.  

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs megadva tároló operációs rendszer, a Windows az alapértelmezett érték.<br><br>Windows-tárolók esetén a IoT Edge a telepítésben található Moby Container Engine-t használja. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tároló motort. |
| **Proxy** | Proxy URL-címe | Adja meg ezt a paramétert, ha az eszköznek egy proxykiszolgálón keresztül kell átesnie az Internet eléréséhez. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Könyvtár elérési útja | Ha ez a paraméter szerepel a rendszerben, a telepítő megkeresi a felsorolt könyvtárat a IoT Edge cab és a VC Runtime MSI-fájlokhoz, amelyek szükségesek a telepítéséhez. A címtárban nem található fájlok letöltődnek. Ha mindkét fájl szerepel a címtárban, a IoT Edge internetkapcsolat nélkül is telepítheti. Ezt a paramétert egy adott verzió használatára is használhatja. |
| **InvokeWebRequestParameters** | Paraméterek és értékek szórótábla | A telepítés során több webes kérelem készül. Ezt a mezőt használhatja a webes kérelmek paramétereinek megadásához. Ez a paraméter hasznos a proxykiszolgálók hitelesítő adatainak konfigurálásához. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | nincs | Ez a jelző lehetővé teszi, hogy az üzembe helyezési parancsfájl szükség esetén anélkül újraindítsa a gépet. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Az inicializálás-IoTEdge parancs a IoT Edge az eszköz kapcsolati karakterláncával és a működési adatokkal konfigurálja. A parancs által generált információk nagy részét ezután a iotedge\config.YAML fájlban tárolja a rendszer. Az inicializálási parancs elfogadja ezeket a közös paramétereket többek között. A teljes listához használja az parancsot `Get-Help Initialize-IoTEdge -full`. 

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Kézi** | Nincsenek | **Kapcsoló paraméter**. Ha nincs megadva a létesítési típus, a manuális beállítás az alapértelmezett érték.<br><br>Kijelenti, hogy az eszköz manuális kiépítéséhez meg kell adnia egy eszköz-kapcsolódási karakterláncot. |
| **DPS** | Nincsenek | **Kapcsoló paraméter**. Ha nincs megadva a létesítési típus, a manuális beállítás az alapértelmezett érték.<br><br>Kijelenti, hogy megadja az eszközök kiépítési szolgáltatásának (DPS) hatókör-AZONOSÍTÓját és az eszköz regisztrációs AZONOSÍTÓját a DPS-n keresztül történő üzembe helyezéshez.  |
| **DeviceConnectionString** | Egy IoT Hubban, egyetlen idézőjelben regisztrált IoT Edge eszközhöz tartozó kapcsolatok karakterlánca | Manuális telepítéshez **szükséges** . Ha nem ad meg egy kapcsolatok karakterláncot a parancsfájl paraméterei között, a rendszer a telepítés során kérni fogja az egyiket. |
| **Hatókör** | A IoT Hubhoz társított eszköz-kiépítési szolgáltatás példányának hatókör-azonosítója. | A DPS telepítéséhez **szükséges** . Ha nem ad meg hatókör-azonosítót a parancsfájl paraméterei között, a rendszer a telepítés során kérni fogja az egyiket. |
| **RegistrationId** | Az eszköz által generált regisztrációs azonosító | A DPS telepítéséhez **szükséges** a TPM vagy szimmetrikus kulcsú tanúsítvány használata esetén. |
| **SymmetricKey** | A IoT Edge eszköz identitásának kiépítéséhez használt szimmetrikus kulcs a DPS használatakor | A DPS-telepítéshez **szükséges** , ha szimmetrikus kulcsú igazolást használ. |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs megadva tároló operációs rendszer, a Windows az alapértelmezett érték.<br><br>Windows-tárolók esetén a IoT Edge a telepítésben található Moby Container Engine-t használja. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tároló motort. |
| **InvokeWebRequestParameters** | Paraméterek és értékek szórótábla | A telepítés során több webes kérelem készül. Ezt a mezőt használhatja a webes kérelmek paramétereinek megadásához. Ez a paraméter hasznos a proxykiszolgálók hitelesítő adatainak konfigurálásához. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **AgentImage** | IoT Edge ügynök rendszerképének URI-ja | Alapértelmezés szerint egy új IoT Edge telepítés a IoT Edge-ügynök lemezképének legújabb gördülő címkéjét használja. Ezzel a paraméterrel megadhat egy adott címkét a rendszerkép verziójához, vagy megadhatja a saját ügynökének rendszerképét. További információ: [IoT Edge címkék ismertetése](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Felhasználónév** | Tároló beállításjegyzékbeli felhasználóneve | Ezt a paramétert csak akkor használja, ha a-AgentImage paramétert egy privát beállításjegyzékben lévő tárolóra állítja be. Adjon meg egy felhasználónevet a beállításjegyzékhez való hozzáféréssel. |
| **Jelszó** | Biztonságos jelszó karakterlánca | Ezt a paramétert csak akkor használja, ha a-AgentImage paramétert egy privát beállításjegyzékben lévő tárolóra állítja be. Adja meg a beállításjegyzék eléréséhez szükséges jelszót. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs megadva tároló operációs rendszer, a Windows az alapértelmezett érték. Windows-tárolók esetén a rendszer a tároló motort fogja tartalmazni a telepítésben. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tároló motort. |
| **Proxy** | Proxy URL-címe | Adja meg ezt a paramétert, ha az eszköznek egy proxykiszolgálón keresztül kell átesnie az Internet eléréséhez. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Paraméterek és értékek szórótábla | A telepítés során több webes kérelem készül. Ezt a mezőt használhatja a webes kérelmek paramétereinek megadásához. Ez a paraméter hasznos a proxykiszolgálók hitelesítő adatainak konfigurálásához. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Könyvtár elérési útja | Ha ez a paraméter szerepel a rendszerben, a telepítő megkeresi a felsorolt könyvtárat a IoT Edge cab és a VC Runtime MSI-fájlokhoz, amelyek szükségesek a telepítéséhez. A címtárban nem található fájlok letöltődnek. Ha mindkét fájl szerepel a címtárban, a IoT Edge internetkapcsolat nélkül is telepítheti. Ezt a paramétert egy adott verzió használatára is használhatja. |
| **RestartIfNeeded** | nincs | Ez a jelző lehetővé teszi, hogy az üzembe helyezési parancsfájl szükség esetén anélkül újraindítsa a gépet. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Hatályba** | nincs | Ez a jelző arra az esetre kényszeríti az eltávolítást, ha az előző eltávolítási kísérlet sikertelen volt. 
| **RestartIfNeeded** | nincs | Ez a jelző lehetővé teszi az eltávolítási parancsfájl számára, hogy szükség esetén a gép újraindítását kérje. |

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha nem sikerül a IoT Edge megfelelően telepíteni, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést szeretne frissíteni a IoT Edge legújabb verziójára, tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

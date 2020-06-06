---
title: A Azure IoT Edge telepítése Windows rendszeren | Microsoft Docs
description: Azure IoT Edge telepítési utasítások a Windows 10, a Windows Server és a Windows IoT Core rendszerhez
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: ba3e8b9d7649d56d1639f7f608d85a2da04ff74a
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465558"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Az Azure IoT Edge-futtatókörnyezet telepítése Windows rendszeren

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet az eszközökön kisméretű, málna PI-ként vagy ipari kiszolgálóként is telepíthető. Ha egy eszköz konfigurálva van a IoT Edge futtatókörnyezettel, megkezdheti az üzleti logika telepítését a felhőből.

További információ a IoT Edge futtatókörnyezetről: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

Ez a cikk azokat a lépéseket ismerteti, amelyekkel Windows-tárolók használatával telepítheti a Azure IoT Edge futtatókörnyezetet Windows x64 (AMD/Intel) rendszerre.

> [!NOTE]
> Egy ismert Windows operációs rendszerbeli probléma megakadályozza az alvó állapotba és a hibernált állapotba való áttérést, ha IoT Edge modulokat (folyamat-elkülönített Windows Nano Server-tárolókat) futtatnak. Ez a probléma hatással van az eszköz akkumulátorának élettartamára.
>
> Megkerülő megoldásként használja a parancsot a `Stop-Service iotedge` futó IoT Edge modulok leállításához, mielőtt használni kezdené ezeket a energiaellátási állapotokat.

A Linux-tárolók használata Windows rendszereken nem ajánlott vagy támogatott éles környezetben a Azure IoT Edge számára. Azonban fejlesztési és tesztelési célokra is használható. További információ: [IoT Edge használata Windows rendszeren Linux-tárolók futtatásához](how-to-install-iot-edge-windows-with-linux.md).

További információ a IoT Edge legújabb verziójában található információkról: [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Előfeltételek

Ebből a szakaszból megtekintheti, hogy a Windows-eszköz támogatja-e a IoT Edget, és hogy a telepítés előtt előkészítse-e a tároló motorját.

### <a name="supported-windows-versions"></a>Támogatott Windows-verziók

A Windowshoz készült IoT Edge a Windows 1809/Build 17763-es verzióját igényli, amely a Windows rendszerhez készült legújabb [hosszú távú támogatási Build](https://docs.microsoft.com/windows/release-information/). A Windows SKU támogatásához tekintse meg a következő témakört: mi támogatott a termelési forgatókönyvek, valamint a fejlesztési és tesztelési forgatókönyvek esetében:

* **Termelés**: az éles környezetekben jelenleg támogatott operációs rendszerek legfrissebb információit a [Azure IoT Edge támogatott rendszerek](support.md#operating-systems)című részben tekintheti meg.
* **Fejlesztés és tesztelés**: fejlesztési és tesztelési forgatókönyvek esetén a Windows-tárolókkal való Azure IoT Edge a windows Build 17763 minden olyan SKU-ra telepíthető (Pro, Enterprise, Server stb.), amely támogatja a tárolók szolgáltatást.

A IoT Core-eszközöknek tartalmaznia kell a IoT Core Windows-tárolók választható funkcióját a IoT Edge futtatókörnyezet támogatásához. A következő parancs [távoli PowerShell-munkamenetben](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) való használatával ellenőrizhető, hogy a Windows-tárolók támogatottak-e az eszközön:

```powershell
Get-Service vmcompute
```

Ha a szolgáltatás jelen van, akkor sikeres választ kaphat a **Futtatás**alatt felsorolt szolgáltatás állapotáról. Ha a `vmcompute` szolgáltatás nem található, akkor az eszköz nem felel meg a IoT Edge követelményeinek. Forduljon a hardver szolgáltatóhoz, és kérje a funkció támogatását.

### <a name="prepare-for-a-container-engine"></a>A Container Engine előkészítése

A Azure IoT Edge egy [OCI-kompatibilis](https://www.opencontainers.org/) tároló motorra támaszkodik. Éles környezetekben a telepítési parancsfájlban található Moby Engine használatával Windows-tárolókat futtathat a Windows-eszközön.

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge telepítése új eszközre

>[!NOTE]
>Azure IoT Edge szoftvercsomagok a csomagokban (a LICENCek könyvtárában) található licencfeltételek hatálya alá esnek. A csomag használata előtt olvassa el a licencfeltételeket. A csomag telepítése és használata jelenti a jelen feltételek elfogadását. Ha nem fogadja el a licencfeltételeket, ne használja a csomagot.

A PowerShell-parancsfájlok letöltik és telepítik a Azure IoT Edge biztonsági démont. A biztonsági démon ezután elindítja az első két futásidejű modult, a IoT Edge ügynököt, amely lehetővé teszi más modulok távoli központi telepítését.

>[!TIP]
>A IoT Core-eszközök esetében javasoljuk, hogy a telepítési parancsokat RemotePowerShell-munkamenet használatával futtassa. További információ: a [PowerShell használata a Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Amikor első alkalommal telepíti a IoT Edge futtatókörnyezetet az eszközön, az eszközt egy IoT hub identitásával kell kiépíteni. Egy IoT Edge eszköz manuálisan is kiépíthető a IoT Hub által biztosított eszköz-összekapcsolási karakterlánc használatával. Vagy a Device kiépítési szolgáltatás (DPS) használatával automatikusan kiépítheti az eszközöket, ami hasznos lehet, ha több eszköz is be van állítva. A kiépítési döntéstől függően válassza ki a megfelelő telepítési parancsfájlt.

A következő szakaszok ismertetik az új eszközön a IoT Edge telepítési parancsfájl általános használati eseteit és paramétereit.

### <a name="option-1-install-and-manually-provision"></a>1. lehetőség: telepítés és manuális kiépítés

Ebben az első lehetőségben egy IoT Hub által generált **eszköz-kapcsolódási karakterláncot biztosít az eszköz kiépítéséhez** .

Ez a példa egy manuális telepítést mutat be Windows-tárolókkal:

1. Ha még nem tette meg, regisztráljon egy új IoT Edge eszközt, és kérje le az **eszköz csatlakoztatási karakterláncát**. Másolja a kapcsolódási karakterláncot a szakasz későbbi részében való használatra. Ezt a lépést a következő eszközök használatával végezheti el:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

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

### <a name="option-2-install-and-automatically-provision"></a>2. lehetőség: telepítés és automatikus kiépítés

Ebben a második lehetőségben az eszközt a IoT Hub Device Provisioning Service használatával kell kiépíteni. Adja meg az eszköz kiépítési szolgáltatásának **hatókör-azonosítóját** , valamint az Ön által előnyben részesített [igazolási mechanizmusra](../iot-dps/concepts-security.md#attestation-mechanism)vonatkozó egyéb információkat:

* [Szimulált IoT Edge eszköz létrehozása és kiépítése virtuális TPM-sel Windows rendszeren](how-to-auto-provision-simulated-device-windows.md)
* [Szimulált IoT Edge eszköz létrehozása és kiépítése X. 509 tanúsítványok használatával](how-to-auto-provision-x509-certs.md)
* [IoT Edge-eszköz létrehozása és kiépítése a szimmetrikus kulcs igazolásával](how-to-auto-provision-symmetric-keys.md)

Amikor automatikusan telepít és kiépít egy eszközt, további paramétereket is használhat a telepítés módosításához, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom
* A telepítő átirányítása offline könyvtárba
* Egy adott ügynök-tároló rendszerképének deklarálása, és a hitelesítő adatok megadása, ha azok egy privát beállításjegyzékben találhatók

A telepítési lehetőségekkel kapcsolatos további információkért olvassa el a jelen cikk olvasását, vagy ugorjon az [összes telepítési paraméter](#all-installation-parameters)megismerése elemre.

## <a name="offline-or-specific-version-installation"></a>Offline vagy adott verzió telepítése

A telepítés során a rendszer letölti a három fájlt:

* Egy PowerShell-parancsfájl, amely tartalmazza a telepítési utasításokat
* Microsoft Azure IoT Edge CAB, amely tartalmazza a IoT Edge biztonsági démont (iotedged), a Moby Container Engine-t és a Moby CLI-t
* Visual C++ Újraterjeszthető csomag (VC Runtime) telepítője

Ha az eszköz a telepítés során offline állapotba kerül, vagy ha a IoT Edge egy adott verzióját szeretné telepíteni, akkor a fájlokat előre letöltheti az eszközre. A telepítés ideje alatt a letöltött fájlokat tartalmazó könyvtárban mutasson a telepítési parancsfájlra. A telepítő először ellenőrzi a könyvtárat, és csak azokat az összetevőket tölti le, amelyek nem találhatók. Ha az összes fájl offline állapotban érhető el, az internetkapcsolat nélkül is telepíthető.

A IoT Edge frissítéséhez használhatja az offline telepítési útvonal paramétert is. További információ: [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md).

1. A legújabb IoT Edge telepítési fájlokról a korábbi verziók mellett lásd: [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases).

2. Keresse meg a telepíteni kívánt verziót, majd töltse le a következő fájlokat a kibocsátási megjegyzések a IoT-eszközön lévő **eszközök** részéből:

   * IoTEdgeSecurityDaemon. ps1
   * A Microsoft-Azure-IoTEdge-amd64. cab a 1.0.9 vagy újabb kiadásokból, vagy a Microsoft-Azure-IoTEdge. CAB fájlokból származik a 1.0.8 és régebbi kiadásokból.

   A Microsoft-Azure-IotEdge-arm32. cab a 1.0.9-től kezdődően is elérhető tesztelési célokra. A IoT Edge jelenleg nem támogatott Windows ARM32-eszközökön.

   Fontos, hogy a PowerShell-parancsfájlt ugyanazzal a kiadással használja, mint a. cab-fájllal, amelyet a funkció az egyes kiadásokban lévő funkciók támogatásához módosít.

3. Ha a letöltött. cab-fájlhoz architektúra utótag tartozik, nevezze át a fájlt csak **Microsoft-Azure-IoTEdge. cab**névre.

4. Igény szerint a Visual C++ újraterjeszthető csomagjának telepítőjét is letöltheti. A PowerShell-parancsfájl például a következő verziót használja: [vc_redist. x64. exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Mentse a telepítőt ugyanabban a mappában a IoT-eszközön, mint a IoT Edge fájlokat.

5. Ha offline összetevőkkel szeretné telepíteni a programot, a [dot forrás](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) a PowerShell-parancsfájl helyi példányát adja meg. Ezután használja a `-OfflineInstallationPath` paramétert a parancs részeként, `Deploy-IoTEdge` és adja meg a fájl könyvtárának abszolút elérési útját. Példa:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   A központi telepítési parancs a megadott helyi fájl könyvtárában található összetevőket fogja használni. Ha a. cab-fájl vagy a Visual C++-telepítő hiányzik, a rendszer megkísérli a letöltést.

6. A parancs futtatásával `Initialize-IoTEdge` kiépítheti az eszközt a IoT hub identitásával. Adja meg a manuális kiépítés esetén az eszközhöz tartozó kapcsolatok karakterláncát, vagy válasszon egyet az előző [automatikus kiépítés](#option-2-install-and-automatically-provision) szakaszban leírt módszerek közül.

   Ha az eszköz futása után újraindult `Deploy-IoTEdge` , a Futtatás előtt futtassa újra a PowerShell-parancsfájlt `Initialize-IoTEdge` .

Az offline telepítési lehetőségről további információt az [összes telepítési paraméter](#all-installation-parameters)megismeréséhez ugorjon.

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ellenőrizze az IoT Edge-szolgáltatás állapotát. A lista futtatásaként kell szerepelnie.  

```powershell
Get-Service iotedge
```

A szolgáltatási naplók vizsgálata az elmúlt 5 percben. Ha befejezte a IoT Edge futtatókörnyezet telepítését, akkor előfordulhat, hogy az **üzembe helyezés-IoTEdge** és az **inicializálás-IoTEdge**futtatása között elérkezett hibák listája látható. A rendszer ezeket a hibákat várta, mivel a szolgáltatás a konfigurálás előtt megpróbálja elindítani a szolgáltatást.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

A [hibaelhárítási eszköz](troubleshoot.md#run-the-check-command) futtatásával keresse meg a leggyakoribb konfigurációs és hálózati hibákat.

```powershell
iotedge check
```

Amíg nem telepíti az első modult az eszközön való IoT Edgere, a rendszer nem telepíti a **$edgeHub** rendszermodult az eszközre. Ennek eredményeképpen az automatikus ellenőrzés hibaüzenetet ad vissza a `Edge Hub can bind to ports on host` kapcsolat ellenőrzésekor. Ez a hiba figyelmen kívül hagyható, kivéve, ha egy modulnak az eszközre történő telepítése után következik be.

Végül a futó modulok listázása:

```powershell
iotedge list
```

Új telepítés után az egyetlen modulnak kell megjelennie a **edgeAgent**. A [IoT Edge-modulok első üzembe helyezése](how-to-deploy-modules-portal.md) után a **edgeHub**másik rendszermodulja is elindul az eszközön.

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

További információ az eszközön futó tárolókkal és képekkel való kommunikációhoz használható parancsokról: [Docker parancssori felületek](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>IoT Edge eltávolítása

Ha el szeretné távolítani a IoT Edge telepítését a Windows-eszközről, használja a következő parancsot egy felügyeleti PowerShell-ablakból. Ez a parancs eltávolítja a IoT Edge futtatókörnyezetet, valamint a meglévő konfigurációt és a Moby Engine-adatait.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Az uninstall-IoTEdge parancs nem működik a Windows IoT Core-on. Ha el szeretné távolítani IoT Edget a Windows IoT Core-eszközökről, újra kell telepítenie a Windows IoT Core rendszerképet.

Az eltávolítási lehetőségekkel kapcsolatos további információkért használja az parancsot `Get-Help Uninstall-IoTEdge -full` .

## <a name="verify-installation-script"></a>Telepítési parancsfájl ellenőrzése

A cikkben megadott telepítési parancsok a meghívó-webkérés parancsmag használatával kérhetik a telepítési parancsfájlt a alkalmazásból `aka.ms/iotedge-win` . Ez a hivatkozás a `IoTEdgeSecurityDaemon.ps1` legújabb [IoT Edge-kiadás](https://github.com/Azure/azure-iotedge/releases)parancsfájlára mutat. A parancsfájlt vagy a parancsfájl egy verzióját egy adott kiadásból is letöltheti a IoT Edge eszközön lévő telepítési parancsok futtatásához.

A megadott parancsfájl a biztonság növelésére van aláírva. Az aláírást úgy ellenőrizheti, ha letölti a szkriptet az eszközre, majd a következő PowerShell-parancsot futtatja:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

A kimeneti állapot **érvényes** , ha az aláírás ellenőrzése megtörtént.

## <a name="all-installation-parameters"></a>Minden telepítési paraméter

Az előző részekben gyakori telepítési forgatókönyvek jelentek meg, példákkal, hogy miként lehet paramétereket használni a telepítési parancsfájl módosításához. Ez a szakasz a IoT Edge telepítésére, frissítésére és eltávolítására szolgáló közös paraméterek táblázatait tartalmazza.

### <a name="deploy-iotedge"></a>Üzembe helyezés – IoTEdge

Az Deploy-IoTEdge parancs letölti és telepíti a IoT Edge biztonsági démont és annak függőségeit. Az üzembe helyezési parancs elfogadja ezeket a közös paramétereket többek között. A teljes listához használja az parancsot `Get-Help Deploy-IoTEdge -full` .  

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs megadva tároló operációs rendszer, a Windows az alapértelmezett érték.<br><br>Windows-tárolók esetén a IoT Edge a telepítésben található Moby Container Engine-t használja. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tároló motort. |
| **Proxy** | Proxy URL-címe | Adja meg ezt a paramétert, ha az eszköznek egy proxykiszolgálón keresztül kell átesnie az Internet eléréséhez. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Könyvtár elérési útja | Ha ez a paraméter szerepel a rendszerben, a telepítő megkeresi a felsorolt könyvtárat a IoT Edge cab és a VC Runtime MSI-fájlokhoz, amelyek szükségesek a telepítéséhez. A címtárban nem található fájlok letöltődnek. Ha mindkét fájl szerepel a címtárban, a IoT Edge internetkapcsolat nélkül is telepítheti. Ezt a paramétert egy adott verzió használatára is használhatja. |
| **InvokeWebRequestParameters** | Paraméterek és értékek szórótábla | A telepítés során több webes kérelem készül. Ezt a mezőt használhatja a webes kérelmek paramétereinek megadásához. Ez a paraméter hasznos a proxykiszolgálók hitelesítő adatainak konfigurálásához. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Nincs | Ez a jelző lehetővé teszi, hogy az üzembe helyezési parancsfájl szükség esetén anélkül újraindítsa a gépet. |

### <a name="initialize-iotedge"></a>Inicializálás – IoTEdge

Az inicializálás-IoTEdge parancs a IoT Edge az eszköz kapcsolati karakterláncával és a működési adatokkal konfigurálja. A parancs által generált információk nagy részét ezután a iotedge\config.YAML fájlban tárolja a rendszer. Az inicializálási parancs elfogadja ezeket a közös paramétereket többek között. A teljes listához használja az parancsot `Get-Help Initialize-IoTEdge -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Kézi** | Nincs | **Kapcsoló paraméter**. Ha nincs megadva a létesítési típus, a manuális beállítás az alapértelmezett érték.<br><br>Kijelenti, hogy az eszköz manuális kiépítéséhez meg kell adnia egy eszköz-kapcsolódási karakterláncot. |
| **DPS** | Nincs | **Kapcsoló paraméter**. Ha nincs megadva a létesítési típus, a manuális beállítás az alapértelmezett érték.<br><br>Kijelenti, hogy megadja az eszközök kiépítési szolgáltatásának (DPS) hatókör-AZONOSÍTÓját és az eszköz regisztrációs AZONOSÍTÓját a DPS-n keresztül történő üzembe helyezéshez.  |
| **DeviceConnectionString** | Egy IoT Hubban, egyetlen idézőjelben regisztrált IoT Edge eszközhöz tartozó kapcsolatok karakterlánca | Manuális kiépítés esetén **szükséges** . Ha nem ad meg egy kapcsolatok karakterláncot a parancsfájl paraméterei között, a rendszer kérni fogja az egyet. |
| **Hatókör** | A IoT Hubhoz társított eszköz-kiépítési szolgáltatás példányának hatókör-azonosítója. | A DPS kiépítés esetén **szükséges** . Ha nem ad meg hatókör-azonosítót a parancsfájl paraméterei között, a rendszer kérni fogja az egyiket. |
| **Regisztrációban** | Az eszköz által generált regisztrációs azonosító | A DPS kiépítés esetén **szükséges** a TPM vagy a szimmetrikus kulcsú tanúsítvány használata esetén. Nem **kötelező** , ha X. 509 tanúsítvány-igazolást használ. |
| **X509IdentityCertificate** | Az eszközön található X. 509 eszköz azonosító tanúsítványának URI-ja. | A DPS kiépítés esetén **szükséges** , ha X. 509 tanúsítvány-igazolást használ. |
| **X509IdentityPrivateKey** | Az eszközön az X. 509 eszköz Identity Certificate kulcs URI-ja. | A DPS kiépítés esetén **szükséges** , ha X. 509 tanúsítvány-igazolást használ. |
| **SymmetricKey** | A IoT Edge eszköz identitásának kiépítéséhez használt szimmetrikus kulcs a DPS használatakor | A DPS kiépítés esetében **szükséges** , ha szimmetrikus kulcsú igazolást használ. |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs megadva tároló operációs rendszer, a Windows az alapértelmezett érték.<br><br>Windows-tárolók esetén a IoT Edge a telepítésben található Moby Container Engine-t használja. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tároló motort. |
| **InvokeWebRequestParameters** | Paraméterek és értékek szórótábla | A telepítés során több webes kérelem készül. Ezt a mezőt használhatja a webes kérelmek paramétereinek megadásához. Ez a paraméter hasznos a proxykiszolgálók hitelesítő adatainak konfigurálásához. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **AgentImage** | IoT Edge ügynök rendszerképének URI-ja | Alapértelmezés szerint egy új IoT Edge telepítés a IoT Edge-ügynök lemezképének legújabb gördülő címkéjét használja. Ezzel a paraméterrel megadhat egy adott címkét a rendszerkép verziójához, vagy megadhatja a saját ügynökének rendszerképét. További információ: [IoT Edge címkék ismertetése](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Username** | Tároló beállításjegyzékbeli felhasználóneve | Ezt a paramétert csak akkor használja, ha a-AgentImage paramétert egy privát beállításjegyzékben lévő tárolóra állítja be. Adjon meg egy felhasználónevet a beállításjegyzékhez való hozzáféréssel. |
| **Jelszó** | Biztonságos jelszó karakterlánca | Ezt a paramétert csak akkor használja, ha a-AgentImage paramétert egy privát beállításjegyzékben lévő tárolóra állítja be. Adja meg a beállításjegyzék eléréséhez szükséges jelszót. |

### <a name="update-iotedge"></a>Frissítés – IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs megadva tároló operációs rendszer, a Windows az alapértelmezett érték. Windows-tárolók esetén a rendszer a tároló motort fogja tartalmazni a telepítésben. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tároló motort. |
| **Proxy** | Proxy URL-címe | Adja meg ezt a paramétert, ha az eszköznek egy proxykiszolgálón keresztül kell átesnie az Internet eléréséhez. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Paraméterek és értékek szórótábla | A telepítés során több webes kérelem készül. Ezt a mezőt használhatja a webes kérelmek paramétereinek megadásához. Ez a paraméter hasznos a proxykiszolgálók hitelesítő adatainak konfigurálásához. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Könyvtár elérési útja | Ha ez a paraméter szerepel a rendszerben, a telepítő megkeresi a felsorolt könyvtárat a IoT Edge cab és a VC Runtime MSI-fájlokhoz, amelyek szükségesek a telepítéséhez. A címtárban nem található fájlok letöltődnek. Ha mindkét fájl szerepel a címtárban, a IoT Edge internetkapcsolat nélkül is telepítheti. Ezt a paramétert egy adott verzió használatára is használhatja. |
| **RestartIfNeeded** | Nincs | Ez a jelző lehetővé teszi, hogy az üzembe helyezési parancsfájl szükség esetén anélkül újraindítsa a gépet. |

### <a name="uninstall-iotedge"></a>Eltávolítás – IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Hatályba** | Nincs | Ez a jelző arra az esetre kényszeríti az eltávolítást, ha az előző eltávolítási kísérlet sikertelen volt.
| **RestartIfNeeded** | Nincs | Ez a jelző lehetővé teszi az eltávolítási parancsfájl számára, hogy szükség esetén a gép újraindítását kérje. |

## <a name="next-steps"></a>Következő lépések

Most, hogy már telepített egy IoT Edge eszközt a futtatott futtatókörnyezettel, telepítheti [IoT Edge modulokat](how-to-deploy-modules-portal.md).

Ha nem sikerül a IoT Edge megfelelően telepíteni, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést szeretne frissíteni a IoT Edge legújabb verziójára, tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

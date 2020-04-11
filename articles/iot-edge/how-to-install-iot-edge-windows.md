---
title: Az Azure IoT Edge telepítése Windows rendszeren | Microsoft dokumentumok
description: Az Azure IoT Edge telepítési utasításai Windows 10, Windows Server és Windows IoT Core rendszeren
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: 61b382f1c286209a12d0be39a81e6817806d3251
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113460"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Az Azure IoT Edge-futtatókörnyezet telepítése Windows rendszeren

Az Azure IoT Edge-futásidejű, mi bekapcsolja az eszközt egy IoT Edge-eszköz. A futásidejű olyan kicsi eszközökre is telepíthető, mint a Raspberry Pi vagy az ipari kiszolgáló. Miután egy eszköz konfigurálva van az IoT Edge-futásidejű, megkezdheti az üzleti logika üzembe helyezését a felhőből.

Ha többet szeretne megtudni az IoT Edge futásidejű, [az Azure IoT Edge futásidejű és architektúrájának megismerése](iot-edge-runtime.md)című témakörben olvashat bővebben.

Ez a cikk felsorolja az Azure IoT Edge futásidejű windows x64 -es (AMD/Intel) rendszerwindows-tárolók használatával történő telepítésének lépéseit.

> [!NOTE]
> A Windows operációs rendszer ismert problémája megakadályozza az alvó és hibernált energiaállapotra való áttérést, amikor az IoT Edge-modulok (folyamatelszigetelt Windows Nano Server-tárolók) futnak. Ez a probléma hatással van az akkumulátor élettartamára az eszközön.
>
> Kerülő megoldásként a `Stop-Service iotedge` paranccsal leállíthatja az IoT Edge-modulok at, mielőtt ezeket a energiagazdálkodási állapotokat használná.

Linux-tárolók használata a Windows rendszereknem ajánlott vagy támogatott éles konfiguráció az Azure IoT Edge. Azonban fejlesztési és tesztelési célokra is használható. További információ: [Az IoT Edge használata A Windows rendszeren Linux-tárolók futtatásához.](how-to-install-iot-edge-windows-with-linux.md)

Az IoT Edge legújabb verziójában található információkról az [Azure IoT Edge-kiadások című témakörben](https://github.com/Azure/azure-iotedge/releases)talál.

## <a name="prerequisites"></a>Előfeltételek

Ebben a szakaszban áttekintheti, hogy a Windows-eszköz támogatja-e az IoT Edge-et, és előkészítheti azt egy tárolómotorra a telepítés előtt.

### <a name="supported-windows-versions"></a>Támogatott Windows-verziók

A Windows IoT Edge használatához a Windows 1809/build 17763-as verziója szükséges, amely a Windows legújabb [hosszú távú támogatási buildje.](https://docs.microsoft.com/windows/release-information/) A Windows Termékváltozat támogatása, olvassa el, mi támogatott attól függően, hogy készül az éles forgatókönyvek vagy fejlesztési és tesztelési forgatókönyvek:

* **Éles környezet:** A legfrissebb információkért, amelyek arról, hogy mely operációs rendszerek jelenleg támogatott éles forgatókönyvek, [lásd: Azure IoT Edge támogatott rendszerek.](support.md#operating-systems)
* **Fejlesztés és tesztelés:** Fejlesztési és tesztelési forgatókönyvek esetén az Azure IoT Edge Windows-tárolókkal telepíthető a Windows 10 vagy a Windows Server 2019 bármely olyan verziójára, amely támogatja a tárolók szolgáltatását.

Az IoT Core-eszközöknek tartalmazniuk kell az IoT Core Windows-tárolók opcionális szolgáltatását az IoT Edge futásidejű támogatásához. A [következő](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) paranccsal ellenőrizze, hogy a Windows-tárolók támogatottak-e az eszközön:

```powershell
Get-Service vmcompute
```

Ha a szolgáltatás jelen van, sikeres választ kell kapnia a **futó**szolgáltatás állapotával. Ha `vmcompute` a szolgáltatás nem található, majd az eszköz nem felel meg az IoT Edge követelményeinek. Érdeklődjön a hardverszolgáltatónál, hogy érdeklődjön a szolgáltatás támogatásáról.

### <a name="prepare-for-a-container-engine"></a>Felkészülés a konténermotorra

Az Azure IoT Edge egy [OCI-kompatibilis](https://www.opencontainers.org/) tárolómotorra támaszkodik. Éles környezetben a telepítési parancsfájlban található Moby motorral futtassa a Windows-tárolókat a Windows-eszközön.

## <a name="install-iot-edge-on-a-new-device"></a>Az IoT Edge telepítése új eszközre

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagokra a csomagokban (a LICENC címtárban) található licencfeltételek vonatkoznak. Kérjük, olvassa el a licencfeltételeket a csomag használata előtt. A csomag telepítése és használata azt jelenti, hogy ön elfogadja ezeket a feltételeket. Ha nem ért egyet a licencfeltételekkel, ne használja a csomagot.

Egy PowerShell-parancsfájl letölti és telepíti az Azure IoT Edge biztonsági démon. A biztonsági démon ezután elindítja az első két futásidejű modulok, az IoT Edge-ügynök, amely lehetővé teszi a távoli üzembe helyezések más modulok.

>[!TIP]
>Az IoT Core-eszközök esetében azt javasoljuk, hogy remotePowerShell-munkamenethasználatával futasd a telepítési parancsokat. További információt a [PowerShell használata Windows IoT-hez című](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)témakörben talál.

Amikor telepíti az IoT Edge futásidejű első alkalommal egy eszközön, ki kell építenie az eszközt egy IoT hub identitással. Egyetlen IoT Edge-eszköz manuálisan kiépíthető az IoT Hub által biztosított eszközkapcsolati karakterlánc használatával. Vagy használhatja az eszközkiépítési szolgáltatás (DPS) automatikusan eszközök kiépítése, ami akkor hasznos, ha sok eszközt kell beállítani. A ttól függően, hogy a kiépítési választás, válassza ki a megfelelő telepítési parancsfájlt.

A következő szakaszok ismertetik a gyakori használati esetek és paraméterek az IoT Edge telepítési parancsfájl egy új eszközön.

### <a name="option-1-install-and-manually-provision"></a>1. lehetőség: Telepítés és manuális üzembe építés

Ebben az első lehetőségben az IoT Hub által az eszköz kiépítése által létrehozott **eszközkapcsolati karakterláncot** biztosít.

Ez a példa a Windows-tárolók kézi telepítését mutatja be:

1. Ha még nem tette meg, regisztráljon egy új IoT Edge-eszközt, és olvassa be az **eszköz kapcsolati karakterláncát.** Másolja a szakasz későbbi részében használni hozandó kapcsolati karakterláncot. Ezt a lépést a következő eszközökkel hajthatja végre:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Futtassa a PowerShellt rendszergazdaként.

   >[!NOTE]
   >Az IoT Edge telepítéséhez használja a PowerShell AMD64-munkamenetét, ne a PowerShellt (x86). Ha nem biztos abban, hogy melyik munkamenettípust használja, futtassa a következő parancsot:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. A **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows-gép egy támogatott verzió, bekapcsolja a tárolók funkciót, majd letölti a moby futásidejű és az IoT Edge futásidejű. A parancs alapértelmezés szerint Windows-tárolókat használ.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Ezen a ponton az IoT Core-eszközök automatikusan újraindulhatnak. Más Windows 10- vagy Windows Server-eszközök újraindítást kérhetnek. Ha igen, indítsa újra az eszközt most. Ha az eszköz készen áll, futtassa újra a PowerShellt rendszergazdaként.

5. Az **Initialize-IoTEdge** parancs konfigurálja az IoT Edge futásidejét a számítógépen. A parancs alapértelmezés szerint manuálisan létesít és windowsos tárolókat.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Amikor a rendszer kéri, adja meg az 1. Az eszköz kapcsolati karakterlánc társítja a fizikai eszköz egy eszközazonosítót az IoT Hubban.

   Az eszköz kapcsolati karakterlánca a következő formátumot veszi fel, és nem tartalmazhat idézőjeleket:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. A Sikeres [telepítés ellenőrzése](#verify-successful-installation) című lépéseivel ellenőrizheti az IoT Edge állapotát az eszközön.

Ha manuálisan telepít és épít ki egy eszközt, további paramétereket is használhat a telepítés módosításához, beleértve a következőket:

* Közvetlen forgalom egy proxykiszolgálón keresztül
* A telepítő rámutatása kapcsolat nélküli könyvtárra
* Adott ügynöktároló-rendszerkép deklarációja, és hitelesítő adatok megadása, ha az egy privát beállításjegyzékben van

A telepítési lehetőségekkel kapcsolatos további információkért ugorjon előre az [összes telepítési paramétermegismeréséhez.](#all-installation-parameters)

### <a name="option-2-install-and-automatically-provision"></a>2. lehetőség: Telepítés és automatikus üzembe építés

Ebben a második lehetőségben az iot hub-eszközkiépítési szolgáltatás használatával építi ki az eszközt. Adja meg a **hatókör-azonosítót** egy eszközkiépítési szolgáltatáspéldányból, valamint az előnyben részesített [tanúsítványmechanizmusra](../iot-dps/concepts-security.md#attestation-mechanism)vonatkozó egyéb információkat:

* [Szimulált IoT Edge-eszköz létrehozása és kiépítése virtuális TPM-mel Windows rendszeren](how-to-auto-provision-simulated-device-windows.md)
* [Szimulált IoT Edge-eszköz létrehozása és kiépítése X.509-es tanúsítványokkal](how-to-auto-provision-x509-certs.md)
* [IoT Edge-eszköz létrehozása és kiépítése szimmetrikus kulcsigazolás sal](how-to-auto-provision-symmetric-keys.md)

Amikor automatikusan telepít és kiépít egy eszközt, további paramétereket is használhat a telepítés módosításához, beleértve a következőket:

* Közvetlen forgalom egy proxykiszolgálón keresztül
* A telepítő rámutatása kapcsolat nélküli könyvtárra
* Adott ügynöktároló-rendszerkép deklarációja, és hitelesítő adatok megadása, ha az egy privát beállításjegyzékben van

A telepítési lehetőségekről további információt a cikk olvasásával vagy az [Összes telepítési paraméterrel](#all-installation-parameters)kapcsolatos további tudnivalókért.

## <a name="offline-or-specific-version-installation"></a>Kapcsolat nélküli vagy adott verziótelepítés

A telepítés során három fájl töltődik le:

* Egy PowerShell-parancsfájl, amely a telepítési utasításokat tartalmazza
* Microsoft Azure IoT Edge fülke, amely az IoT Edge biztonsági démont (iotedged), a Moby tárolómotort és a Moby CLI-t tartalmazza
* Visual C++ újraterjeszthető csomag (VC runtime) telepítő

Ha az eszköz offline állapotú lesz a telepítés során, vagy ha az IoT Edge egy adott verzióját szeretné telepíteni, ezeket a fájlokat előre letöltheti az eszközre. Amikor eljön a telepítés ideje, irányítsa a telepítési parancsfájlt a letöltött fájlokat tartalmazó könyvtárra. A telepítő először ellenőrzi a könyvtárat, majd csak azokat az összetevőket tölti le, amelyek nem találhatók meg. Ha az összes fájl elérhető kapcsolat nélkül, telepítheti internetkapcsolat nélkül.

A kapcsolat nélküli telepítési elérési út paraméter t is használhatja az IoT Edge frissítéséhez. További információ: [Az IoT Edge biztonsági démonának és futásidejének frissítése](how-to-update-iot-edge.md)című témakörben talál.

1. A legújabb IoT Edge telepítőfájlokat a korábbi verziókkal együtt az [Azure IoT Edge-kiadások ban.](https://github.com/Azure/azure-iotedge/releases)

2. Keresse meg a telepíteni kívánt verziót, és töltse le a következő fájlokat a kibocsátási megjegyzések **Eszközök** szakaszából az IoT-eszközre:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab az 1.0.9-es vagy újabb kiadásokból, vagy a Microsoft-Azure-IoTEdge.cab az 1.0.8-as és újabb kiadásokból.

   A Microsoft-Azure-IotEdge-arm32.cab is elérhető 1.0.9-től kezdve csak tesztelési célokra. Az IoT Edge jelenleg nem támogatott Windows ARM32-eszközökön.

   Fontos, hogy a PowerShell-parancsfájlt ugyanabból a kiadásból használja, mint a .cab fájlt, mert a funkciók változnak, hogy támogassa a funkciókat az egyes kiadásokban.

3. Ha a letöltött .cab fájlon architektúra-utótag található, nevezze át a fájlt csak **Microsoft-Azure-IoTEdge.cab fájlra.**

4. Szükség esetén töltsön le egy telepítőt a Visual C++ újraterjeszthető programhoz. A PowerShell-parancsfájl például a következő verziót használja: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Mentse a telepítőt ugyanabban a mappában az IoT-eszközön, mint az IoT Edge-fájlokat.

5. Az offline összetevőkkel való telepítéshez a PowerShell-parancsfájl helyi példányát [forrásként](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) kell kezelni. Ezután használja `-OfflineInstallationPath` a paramétert `Deploy-IoTEdge` a parancs részeként, és adja meg a fájlkönyvtár abszolút elérési útját. Például:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   A központi telepítési parancs a megadott helyi fájlkönyvtárban található összetevőket fogja használni. Ha a .cab fájl vagy a Visual C++ telepítő hiányzik, megpróbálja letölteni őket.

6. Futtassa a parancsot az `Initialize-IoTEdge` eszköz kiépítéséhez egy identitással az IoT Hubban. Adjon meg egy eszközkapcsolati karakterláncot a manuális kiépítéshez, vagy válasszon az előző [automatikus üzembe létesítési](#option-2-install-and-automatically-provision) szakaszban ismertetett módszerek közül.

   Ha az eszköz futás `Deploy-IoTEdge`után újraindult, a futtatás `Initialize-IoTEdge`előtt ismét forrásforrásként adja meg a PowerShell-parancsfájlt.

Az offline telepítési lehetőséggel kapcsolatos további információkért ugorjon előre az [összes telepítési paramétermegismeréséhez.](#all-installation-parameters)

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ellenőrizze az IoT Edge-szolgáltatás állapotát. Meg kell sorolni a futás.  

```powershell
Get-Service iotedge
```

Vizsgálja meg az elmúlt 5 perc szolgáltatásnaplóit. Ha most fejezte be az IoT Edge futtatófa telepítését, a **Deploy-IoTEdge** és az **Initialize-IoTEdge**közötti hibák listája jelenhet meg. Ezek a hibák várhatóak, mivel a szolgáltatás a konfigurálás előtt próbál elindulni.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futtasson automatikus ellenőrzést a leggyakoribb konfigurációs és hálózati hibákról.

```powershell
iotedge check
```

Futó modulok listája. Egy új telepítés után az egyetlen modul, amelyet látnia kell, az **edgeAgent**. Miután először telepítette az [IoT Edge-modulokat,](how-to-deploy-modules-portal.md) a másik rendszermodul, az **edgeHub**is elindul az eszközön.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Modultárolók kezelése

Az IoT Edge szolgáltatás hoz egy tárolómotor fut az eszközön. Amikor üzembe helyez egy modult egy eszközre, az IoT Edge futásidejű a tárolómotor segítségével lekéri a tárolórendszerképet a felhőben lévő rendszerleíró adatbázisból. Az IoT Edge szolgáltatás lehetővé teszi, hogy a modulok és a naplók lekérése, de néha érdemes használni a tárolómotort, hogy a tároló is.

A modulfogalmakról további információt [az Azure IoT Edge-modulok megismerése című témakörben talál.](iot-edge-modules.md)

Ha Windows-tárolókat futtat a Windows IoT Edge-eszközön, akkor az IoT Edge telepítése tartalmazza a Moby tárolómotort. A Moby motor a Docker-rel azonos szabványokon alapult, és úgy tervezték, hogy párhuzamosan fusson ugyanazon a gépen, mint a Docker Desktop. Ebből az okból, ha azt szeretné, hogy a cél a Moby motor által kezelt tárolók, meg kell kifejezetten a cél, hogy a motor helyett Docker.

Az összes Docker-lemezkép listázásához például használja a következő parancsot:

```powershell
docker images
```

Az összes Moby-kép listázásához módosítsa ugyanazt a parancsot a Moby motorra mutató mutatóval:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

A motor URI-ja szerepel a telepítési parancsfájl kimenetében, vagy megtalálhatja a config.yaml fájl tárolófutási beállítások szakaszában.

![moby_runtime uri a config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Az eszközön futó tárolókkal és lemezképekkel való kommunikációhoz használható parancsokkal kapcsolatos további tudnivalókért olvassa el a [Docker parancssori felületeit.](https://docs.docker.com/engine/reference/commandline/docker/)

## <a name="uninstall-iot-edge"></a>IoT Edge eltávolítása

Ha el szeretné távolítani az IoT Edge-telepítést a Windows-eszközről, használja a következő parancsot egy felügyeleti PowerShell-ablakból. Ez a parancs eltávolítja az IoT Edge futásidejű, a meglévő konfiguráció és a Moby motor adatait.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Az Uninstall-IoTEdge parancs nem működik Windows IoT Core rendszeren. Az IoT Edge eltávolítása a Windows IoT Core eszközök, újra kell telepítenie a Windows IoT Core lemezkép.

Az eltávolítási beállításokkal kapcsolatos további `Get-Help Uninstall-IoTEdge -full`tudnivalókért használja a parancsot.

## <a name="verify-installation-script"></a>Telepítési parancsfájl ellenőrzése

A cikkben található telepítési parancsok az Invoke-WebRequest parancsmag segítségével `aka.ms/iotedge-win`kérik a telepítőparancsfájlt a alkalmazástól. Ez a hivatkozás`IoTEdgeSecurityDaemon.ps1` a legutóbbi [IoT Edge-kiadásból](https://github.com/Azure/azure-iotedge/releases)származó parancsfájlra mutat. Ezt a parancsfájlt vagy a parancsfájl egy adott kiadásból egy verzióját is letöltheti, hogy futtassa a telepítési parancsokat az IoT Edge-eszközön.

A megadott parancsfájl alá van írva a biztonság növelése érdekében. Az aláírás támlájára úgy ellenőrizheti, hogy letölti a parancsfájlt az eszközre, majd futtatja a következő PowerShell-parancsot:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

A kimeneti állapot **Érvényes,** ha az aláírás tellenőrzi.

## <a name="all-installation-parameters"></a>Minden telepítési paraméter

Az előző szakaszok gyakori telepítési forgatókönyveket vezettek be, példákkal a telepítési parancsfájl módosításához a paraméterek használatára. Ez a szakasz az IoT Edge telepítéséhez, frissítéséhez vagy eltávolításához használt általános paraméterek referenciatáblázatait tartalmazza.

### <a name="deploy-iotedge"></a>Üzembe helyezés-IoTEdge

A Deploy-IoTEdge parancs letölti és telepíti az IoT Edge biztonsági démont és annak függőségeit. A központi telepítési parancs elfogadja ezeket a gyakori paramétereket, többek között. A teljes listához használja `Get-Help Deploy-IoTEdge -full`a parancsot.  

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs között** | **Windows** vagy **Linux** | Ha nincs megadva tárolóoperációs rendszer, a Windows az alapértelmezett érték.<br><br>Windows-tárolók esetén az IoT Edge a telepítésben található moby tárolómotort használja. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tárolómotort. |
| **Proxy** | Proxy URL-címe | Adja meg ezt a paramétert, ha az eszköznek proxykiszolgálón keresztül kell eljutnia az interneteléréséhez. További információ: [Az IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md)című témakörben talál. |
| **OfflineInstallationPath** | Címtár elérési útja | Ha ez a paraméter is szerepel, a telepítő ellenőrzi az IoT Edge cab és a VC Runtime MSI fájlok telepítéshez szükséges könyvtárát. A program letölti a könyvtárban nem található fájlokat. Ha mindkét fájl a címtárban van, az IoT Edge-et internetkapcsolat nélkül is telepítheti. Ezt a paramétert egy adott verzió használatára is használhatja. |
| **InvokeWebRequestParameters** | Paraméterek és értékek kivonata | A telepítés során számos webes kérés történik. Ebben a mezőben paramétereket állíthat be ezekhez a webes kérelmekhez. Ez a paraméter a proxykiszolgálók hitelesítő adatainak konfigurálásához hasznos. További információ: [Az IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md)című témakörben talál. |
| **RestartIfNeeded (ÚjrakezdésHa szükséges)** | Nincs | Ez a jelző lehetővé teszi, hogy a központi telepítési parancsfájl szükség esetén figyelmeztetés nélkül indítsa újra a számítógépet. |

### <a name="initialize-iotedge"></a>Inicializálás-IoTEdge

Az Initialize-IoTEdge parancs konfigurálja az IoT Edge-et az eszköz kapcsolati karakterláncával és a működési részletekkel. A parancs által létrehozott adatok nagy része ezután az iotedge\config.yaml fájlban tárolódik. Az inicializálási parancs többek között elfogadja ezeket a gyakori paramétereket. A teljes listához használja `Get-Help Initialize-IoTEdge -full`a parancsot.

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Kézi** | None | **Kapcsoló paraméter**. Ha nincs megadva létesítési típus, a kézi érték az alapértelmezett érték.<br><br>Deklarálja, hogy eszközkapcsolati karakterláncot fog biztosítani az eszköz manuális kiépítéséhez |
| **Dps** | None | **Kapcsoló paraméter**. Ha nincs megadva létesítési típus, a kézi érték az alapértelmezett érték.<br><br>Deklarálja, hogy egy Eszközkiépítési szolgáltatás (DPS) hatókörazonosítóját és az eszköz regisztrációs azonosítóját fogja biztosítani a DPS-en keresztül történő kiépítéséhez.  |
| **DeviceConnectionString** | Az IoT Hubban regisztrált IoT Edge-eszköz kapcsolati karakterlánca egyszeres idézőjelben | Kézi kiépítéshez **szükséges.** Ha nem ad meg kapcsolati karakterláncot a parancsfájl paramétereiben, a rendszer kérni fogja az egyiket. |
| **Hatókörazonosító** | Az IoT Hubhoz társított eszközkiépítési szolgáltatás egy példányának hatókör-azonosítója. | A DPS-kiépítéshez **szükséges.** Ha nem adja meg a hatókör-azonosítót a parancsfájl paramétereiben, a rendszer kérni fogja az egyiket. |
| **Regisztrációs azonosító** | Az eszköz által létrehozott regisztrációs azonosító | A DPS-kiépítéshez **szükséges,** ha TPM-et vagy szimmetrikus kulcsigazolást használ. **Nem kötelező,** ha X.509 tanúsítványt használ. |
| **X509IdentityCertificate** | Az X.509 eszközidentitás-tanúsítvány URI-elérési útja az eszközön. | A DPS-kiépítéshez **szükséges,** ha X.509 tanúsítványt használ. |
| **X509IdentityPrivateKey** | Az X.509 eszközidentitás-tanúsítvány kulcsának URI-elérési útja az eszközön. | A DPS-kiépítéshez **szükséges,** ha X.509 tanúsítványt használ. |
| **Szimmetrikus kulcs** | Az IoT Edge-eszköz identitásának kiépítéséhez használt szimmetrikus kulcs a DPS használatakor | A DPS-kiépítéshez **szükséges,** ha szimmetrikus kulcsigazolást használ. |
| **ContainerOs között** | **Windows** vagy **Linux** | Ha nincs megadva tárolóoperációs rendszer, a Windows az alapértelmezett érték.<br><br>Windows-tárolók esetén az IoT Edge a telepítésben található moby tárolómotort használja. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tárolómotort. |
| **InvokeWebRequestParameters** | Paraméterek és értékek kivonata | A telepítés során számos webes kérés történik. Ebben a mezőben paramétereket állíthat be ezekhez a webes kérelmekhez. Ez a paraméter a proxykiszolgálók hitelesítő adatainak konfigurálásához hasznos. További információ: [Az IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md)című témakörben talál. |
| **AgentImage** | IoT Edge-ügynök képe URI | Alapértelmezés szerint egy új IoT Edge-telepítés az IoT Edge-ügynök lemezképének legújabb gördülő címkéjét használja. Ezzel a paraméterrel egy adott címkét állíthat be a lemezkép-verzióhoz, vagy saját ügynöklemezképet adhat meg. További információ: [IoT Edge-címkék ismertetése.](how-to-update-iot-edge.md#understand-iot-edge-tags) |
| **Felhasználónév** | Tároló beállításjegyzék-felhasználóneve | Csak akkor használja ezt a paramétert, ha az -AgentImage paramétert egy privát beállításjegyzékben lévő tárolóra állítja be. Adjon hozzáférést a rendszerleíró adatbázishoz. |
| **Jelszó** | Biztonságos jelszókarakterlánc | Csak akkor használja ezt a paramétert, ha az -AgentImage paramétert egy privát beállításjegyzékben lévő tárolóra állítja be. Adja meg a rendszerleíró adatbázis eléréséhez a jelszót. |

### <a name="update-iotedge"></a>Frissítés-IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs között** | **Windows** vagy **Linux** | Ha nincs megadva tárolóoperációs rendszer, a Windows az alapértelmezett érték. Windows-tárolók esetén a telepítés tárolómotort fog tartalmazni. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tárolómotort. |
| **Proxy** | Proxy URL-címe | Adja meg ezt a paramétert, ha az eszköznek proxykiszolgálón keresztül kell eljutnia az interneteléréséhez. További információ: [Az IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md)című témakörben talál. |
| **InvokeWebRequestParameters** | Paraméterek és értékek kivonata | A telepítés során számos webes kérés történik. Ebben a mezőben paramétereket állíthat be ezekhez a webes kérelmekhez. Ez a paraméter a proxykiszolgálók hitelesítő adatainak konfigurálásához hasznos. További információ: [Az IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md)című témakörben talál. |
| **OfflineInstallationPath** | Címtár elérési útja | Ha ez a paraméter is szerepel, a telepítő ellenőrzi az IoT Edge cab és a VC Runtime MSI fájlok telepítéshez szükséges könyvtárát. A program letölti a könyvtárban nem található fájlokat. Ha mindkét fájl a címtárban van, az IoT Edge-et internetkapcsolat nélkül is telepítheti. Ezt a paramétert egy adott verzió használatára is használhatja. |
| **RestartIfNeeded (ÚjrakezdésHa szükséges)** | Nincs | Ez a jelző lehetővé teszi, hogy a központi telepítési parancsfájl szükség esetén figyelmeztetés nélkül indítsa újra a számítógépet. |

### <a name="uninstall-iotedge"></a>Eltávolítás-IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Erő** | Nincs | Ez a jelző kényszeríti az eltávolítást arra az esetre, ha az előző eltávolítási kísérlet sikertelen lett volna.
| **RestartIfNeeded (ÚjrakezdésHa szükséges)** | Nincs | Ez a jelző lehetővé teszi, hogy az eltávolítási parancsfájl szükség esetén figyelmeztetés nélkül indítsa újra a számítógépet. |

## <a name="next-steps"></a>További lépések

Most, hogy egy IoT Edge-eszközt létesített a futásidejű telepítve, telepítheti az [IoT Edge-modulokat.](how-to-deploy-modules-portal.md)

Ha problémái vannak az IoT Edge megfelelő telepítésével, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést az IoT Edge legújabb verziójára szeretne frissíteni, olvassa [el az IoT Edge biztonsági démonának és futásidejű](how-to-update-iot-edge.md)verziójának frissítése című témakört.

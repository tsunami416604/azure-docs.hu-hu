<properties
    pageTitle="IoT Hub-eszközfelügyelet – Első lépések | Microsoft Azure"
    description="Azure IoT Hub-eszközfelügyelet C# környezethez – Első lépéseket ismertető oktatóanyag | Microsoft Azure Az Azure IoT Hubot és a C# környezetet a Microsoft Azure IoT SDK-kal együtt használhatja eszközfelügyelet megvalósításához."
    services="iot-hub"
    documentationCenter=".net"
    authors="ellenfosborne"
    manager="timlt"
    editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Ismerkedés az Azure IoT Hub for .NET eszközfelügyelettel C# használatával (előzetes kiadás)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Bevezetés
Az Azure IoT Hub-eszközfelügyelettel való ismerkedéshez létre kell hoznia egy Azure IoT Hubot, eszközöket kell kiosztania az IoT Hubban, és el kell indítania több szimulált eszközt. Az oktatóanyag végigvezeti ezeken a lépéseken.

> [AZURE.NOTE]  Akkor is létre kell hoznia egy új IoT Hubot az eszközfelügyeleti képességek engedélyezéséhez, ha már rendelkezik egy meglévő IoT Hubbal, mivel a meglévő IoT Hub még nem rendelkezik eszközfelügyeleti képességekkel. Miután az eszközfelügyelet általánosan elérhetővé válik, az összes meglévő IoT Hub frissül az eszközfelügyeleti képességekkel.

## Előfeltételek

A következőknek telepítve kell lenniük a lépések végrehajtásához:

- Microsoft Visual Studio 2015
- Git
- CMake (2.8-as vagy újabb verzió). A CMake a <https://cmake.org/download/> weboldalról telepíthető. Windows rendszerű számítógép esetében válassza a Windows Installer (.msi) lehetőséget. Feltétlenül jelölje be a jelölőnégyzetet, amellyel a CMake hozzáadható az aktuális felhasználói PATH változóhoz.
- Aktív Azure-előfizetés.

    Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].

## Eszközfelügyelet-kompatibilis IoT Hub létrehozása

Létre kell hoznia egy eszközfelügyelet-kompatibilis IoT Hubot, amelyhez a szimulált eszközök csatlakozhatnak. Az alábbi lépések bemutatják, hogyan hajthatja végre ezt a feladatot az Azure Portal segítségével.

1.  Jelentkezzen be az [Azure Portal].
2.  Az ugrósávon kattintson az **Új** elemre, majd az **Eszközök internetes hálózata**, végül az **Azure IoT Hub** elemre.

    ![][img-new-hub]

3.  Az **IoT Hub** panelen válassza ki az IoT Hub konfigurációját.

    ![][img-configure-hub]

  -   A **Név** mezőben adja meg az IoT Hub nevét. Ha a **Név** érvényes és elérhető, egy zöld pipa jelenik meg a **Név** mezőben.
  -   Válasszon ki egy **tarifacsomagot és méretet**. Az oktatóanyag teljesítéséhez nem kell egy konkrét csomagot kiválasztani.
  -   Az **Erőforráscsoport** területen hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. További információk: [Using Resource groups to manage your Azure resources] (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez).
  -   Jelölje be az **Eszközfelügyelet engedélyezése** jelölőnégyzetet.
  -   A **Hely** területen válasszon ki egy helyet, ahol az IoT Hubot üzemeltetni kívánja. Az IoT Hub-eszközfelügyelet nyilvános előzetes verziója csak az USA keleti régiójában, Észak-Európában és Kelet-Ázsiában érhető el. A jövőben az összes régióban elérhető lesz.

    > [AZURE.NOTE]  Ha nem jelöli be az **Eszközfelügyelet engedélyezése** jelölőnégyzetet, a minták nem fognak működni.

4.  Az IoT Hub konfigurációs beállításainak kiválasztása után kattintson a **Létrehozás** gombra. Az IoT Hub létrehozása eltarthat néhány percig. A folyamat állapotát a **Kezdőpulton** vagy az **Értesítés** panelen ellenőrizheti.

    ![][img-monitor]

5.  Amikor az IoT Hub sikeresen létrejött, nyissa meg az új IoT Hub paneljét, jegyezze fel az **Állomásnevet**, majd kattintson a **Kulcsok** ikonra.

    ![][img-keys]

6.  Kattintson az **iothubowner** házirendre, majd másolja ki és jegyezze fel az **iothubowner** panelen látható kapcsolati karakterláncot. Másolja egy olyan helyre, ahol később elérheti, mivel szükség lesz rá az oktatóanyag további részeinek teljesítéséhez.

    > [AZURE.NOTE] Éles forgatókönyvekben ne használja az **iothubowner** hitelesítő adatait.

    ![][img-connection]

Ezzel létrehozott egy eszközfelügyelet-kompatibilis IoT Hubot. A kapcsolati karakterláncra szükség lesz az oktatóanyag további részeinek teljesítéséhez.

## A minták összeállítása és eszközök kiosztása az IoT Hubban

Ebben a szakaszban egy olyan parancsfájlt fog futtatni, amely összeállítja a szimulált eszközöket és a mintákat, valamint kiosztja az új eszközidentitások egy készletét az IoT Hub eszközjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az eszközjegyzékbe.

Kövesse az alábbi lépéseket a minták összeállításához és az eszközök kiosztásához az IoT Hubban:

1.  Nyissa meg a **VS2015 fejlesztői parancssorát**.

2.  Klónozza a GitHub-adattárat. **Ügyeljen arra, hogy olyan könyvtárba klónozza, amelynek a nevében nem szerepelnek szóközök.**

      ```
      git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
      ```

3.  Abból a gyökérmappából, amelybe klónozta az **azure-iot-sdks** adattárat, navigáljon az **\\azure-iot-sdks\\csharp\\service\\samples** mappába, és futtassa a következőt, lecserélve a helyőrző értékét az előző szakaszban feljegyzett kapcsolati karakterláncra:

      ```
      setup.bat <IoT Hub Connection String>
      ```

A parancsfájl a következő műveleteket hajtja végre:

1.  A **cmake** parancs futtatásával létrehoz egy Visual Studio 2015-megoldást a szimulált eszközhöz. Ez a projektfájl az **azure-iot-sdks\\csharp\\service\\samples\\cmake\\iotdm\_client\\samples\\iotdm\_simple\_sample\\iotdm\_simple\_sample.vcxproj**. Vegye figyelembe, hogy a forrásfájlok az ***azure-iot-sdks\\c\\iotdm\_client\\samples\\iotdm\_simple\_sample** mappában találhatók.

2.  Összeállítja az eszköz **iotdm\_simple\_sample.vcxproj** projektfájlját.

3.  Összeállítja az eszköz felügyeleti mintáit: **azure-iot-sdks\\csharp\\service\\samples\\GetStartedWithIoTDM\\GetStartedWithIoTDM.sln**.

4.  Futtatja a **GenerateDevices.exe** parancsot az eszközidentitások kiosztásához az IoT Hubban. Az eszközök leírása a **sampledevices.json** fájlban található (az **azure-iot-sdks\\node\\service\\samples** mappában), és az eszközök kiosztása után a hitelesítő adatokat a **devicecreds.txt** fájl tárolja (amely az **azure-iot-sdks\\csharp\\service\\samples\\bin** könyvtárban található).

## A szimulált eszközök elindítása

Most, hogy az eszközök hozzá lettek adva az eszközjegyzékhez, elindíthatja a szimulált felügyelt eszközöket. Az Azure IoT Hubban kiosztott minden eszközidentitáshoz egy szimulált eszköz indul el.

Egy fejlesztői parancssor segítségével az **\\azure-iot-sdks\\csharp\\service\\samples\\bin** mappában futtassa a következőt:

  ```
  simulate.bat
  ```

Ez a parancsfájl az **iotdm\_simple\_sample.exe** egy példányát futtatja a **devicecreds.txt** fájlban felsorolt minden egyes eszközhöz. A szimulált eszköz addig fog futni, amíg be nem zárja a parancsablakot.

Az **iotdm\_simple\_sample** mintaalkalmazás a C nyelvhez készült Azure IoT Hub eszközfelügyeleti ügyfélkönyvtár használatával lett összeállítva, amely lehetővé teszi az Azure IoT Hub által felügyelhető IoT-eszközök létrehozását. Az eszközkészítők ezt a könyvtárat használhatják az eszköz tulajdonságainak jelentéséhez, illetve az eszköz feladataihoz szükséges végrehajtható műveletek megvalósításához. Ez a könyvtár a nyílt forráskódú Azure IoT Hub SDK-k részét képező mellékelt összetevő.

A **simulate.bat** futtatásakor a kimeneti ablakban egy adatfolyam jelenik meg. Ez a kimenet a bejövő és a kimenő forgalmat jeleníti meg, valamint tartalmazza a **printf** utasításokat az alkalmazásspecifikus visszahívási függvényekben. Így megtekintheti a bejövő és kimenő forgalmat, és hogy a mintaalkalmazás hogyan kezeli a dekódolt csomagokat. Amikor az eszköz csatlakozik az IoT Hubhoz, a szolgáltatás automatikusan elkezdi megfigyelni az eszköz erőforrásait. Az IoT Hub DM-ügyfélkönyvtára ezután meghívja az eszköz visszahívásait, hogy lekérje az eszköz legutóbbi értékeit.

Az alábbiakban az **iotdm\_simple\_sample** mintaalkalmazás kimenete látható. Felül egy sikeres **REGISTERED** üzenet látható, amely az IoT Hubhoz csatlakozó **Device11-7ce4a850** azonosítójú eszközt mutatja.

> [AZURE.NOTE]  A kevésbé részletes kimenet érdekében fordítsa le és futtassa a kiskereskedelmi konfigurációt.

![][img-output]

Futtassa tovább az összes szimulált eszközt a „Következő lépések” szakaszban szereplő oktatóanyagok teljesítése közben.

## Következő lépések

Az Azure IoT Hub eszközfelügyeleti szolgáltatásaival kapcsolatos további tudnivalókért megtekintheti a következő oktatóanyagokat:

- [How to use the device twin][lnk-tutorial-twin] (Ikereszközök használata)

- [How to find device twins using queries][lnk-tutorial-queries] (Ikereszközök keresése lekérdezésekkel)

- [How to use device jobs to update device firmware][lnk-tutorial-jobs] (Eszközfeladatok használata az eszköz belső vezérlőprogramjának frissítéséhez)

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure Portal]: https://portal.azure.com/
[Erőforráscsoportok használata az Azure-erőforrások kezeléséhez]: ../azure-portal/resource-group-portal.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md



<!--HONumber=jun16_HO2-->



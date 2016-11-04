---
title: IoT Hub-eszközfelügyelet – Első lépések | Microsoft Docs
description: Azure IoT Hub-eszközfelügyelet C# környezethez – Első lépéseket ismertető oktatóanyag | Microsoft Azure Az Azure IoT Hubot és a C# környezetet a Microsoft Azure IoT SDK-kal együtt használhatja eszközfelügyelet megvalósításához.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: juanpere

---
# Ismerkedés az Azure IoT Hub for .NET eszközfelügyelettel C# használatával (előzetes kiadás)
[!INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Bevezetés
Az Azure IoT Hub-eszközfelügyelettel való megismerkedéshez létre kell hoznia egy Azure IoT Hubot, eszközöket kell kiosztania az IoT Hubban, el kell indítania több szimulált eszközt, és meg kell tekintenie azokat a minta eszközfelügyeleti felhasználói felületen. Az oktatóanyag végigvezeti ezeken a lépéseken.

> [!NOTE]
> Akkor is létre kell hoznia egy új IoT Hubot az eszközfelügyeleti képességek engedélyezéséhez, ha már rendelkezik egy meglévő IoT Hubbal, mivel a meglévő IoT Hubok még nem rendelkeznek ezzel a képességekkel. Miután az eszközfelügyelet általánosan elérhetővé válik, az összes meglévő IoT Hub frissül az eszközfelügyeleti képességekkel.
> 
> 

## Előfeltételek
Ez az oktatóanyag azt feltételezi, hogy Windows rendszerű fejlesztési számítógépet használ.

A következőknek telepítve kell lenniük a lépések végrehajtásához:

* Microsoft Visual Studio 2015
* Git
* CMake (2.8-as vagy újabb verzió). A CMake a <https://cmake.org/download/> weboldalról telepíthető. Windows rendszerű számítógép esetében válassza a Windows Installer (.msi) lehetőséget. Feltétlenül jelölje be a jelölőnégyzetet, amellyel a CMake hozzáadható az aktuális felhasználói PATH változóhoz.
* Node.js 6.1.0 vagy újabb.  A platformjának megfelelő Node.js-t a <https://nodejs.org/> webhelyről telepítheti.
* Aktív Azure-előfizetés. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].

## Eszközfelügyelet-kompatibilis IoT Hub létrehozása
Létre kell hoznia egy eszközfelügyelet-kompatibilis IoT Hubot, amelyhez a szimulált eszközök csatlakozhatnak. Az alábbi lépések bemutatják, hogyan hajthatja végre ezt a feladatot az Azure Portal segítségével.

1. Jelentkezzen be az [Azure Portal].
2. Az ugrósávon kattintson az **Új** elemre, majd az **Eszközök internetes hálózata**, végül az **Azure IoT Hub** elemre.
   
   ![][img-new-hub]
3. Az **IoT Hub** panelen válassza ki az IoT Hub konfigurációját.
   
   ![][img-configure-hub]
   
   * A **Név** mezőben adja meg az IoT Hub nevét. Ha a **Név** érvényes és elérhető, egy zöld pipa jelenik meg a **Név** mezőben.
   * Válasszon ki egy **tarifacsomagot és méretet**. Az oktatóanyag teljesítéséhez nem kell egy konkrét csomagot kiválasztani.
   * Az **Erőforráscsoport** területen hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. További információk: [Using Resource groups to manage your Azure resources](Erőforráscsoportok használata az Azure-erőforrások kezeléséhez.md).
   * Jelölje be az **Eszközfelügyelet engedélyezése** jelölőnégyzetet.
   * A **Hely** területen válasszon ki egy helyet, ahol az IoT Hubot üzemeltetni kívánja. Az IoT Hub-eszközfelügyelet nyilvános előzetes verziója csak az USA keleti régiójában, Észak-Európában és Kelet-Ázsiában érhető el. A jövőben az összes régióban elérhető lesz.
   
   > [!NOTE]
   > Ha nem jelöli be az **Eszközfelügyelet engedélyezése** jelölőnégyzetet, a minták nem fognak működni.<br/>Az **Eszközfelügyelet engedélyezése** jelölőnégyzet bejelölésével az IoT Hub olyan előzetes verziója jön létre, amely kizárólag az USA keleti régiójában, Észak-Európában és Kelet-Ázsiában támogatott, és nem termelési forgatókönyvekhez készült. Az eszközök nem telepíthetők át eszközfelügyelet-kompatibilis hubokba, illetve azokból máshova.
   > 
   > 
4. Az IoT Hub konfigurációs beállításainak kiválasztása után kattintson a **Létrehozás** gombra. Az IoT Hub létrehozása eltarthat néhány percig. A folyamat állapotát a **Kezdőpulton** vagy az **Értesítés** panelen ellenőrizheti.
   
   ![][img-monitor]
5. Amikor az IoT Hub sikeresen létrejött, nyissa meg az új IoT Hub paneljét, jegyezze fel az **Állomásnevet**, majd kattintson a **Megosztott hozzáférési házirendek** elemre.
   
   ![][img-keys]
6. Kattintson az **iothubowner** házirendre, majd másolja ki és jegyezze fel az **iothubowner** panelen látható kapcsolati karakterláncot. Másolja egy olyan helyre, ahol később elérheti, mivel szükség lesz rá az oktatóanyag további részeinek teljesítéséhez.
   
   > [!NOTE]
   > Éles forgatókönyvekben ne használja az **iothubowner** hitelesítő adatait.
   > 
   > 
   
   ![][img-connection]

Ezzel létrehozott egy eszközfelügyelet-kompatibilis IoT Hubot. A kapcsolati karakterláncra szükség lesz az oktatóanyag további részeinek teljesítéséhez.

## A minták összeállítása és eszközök kiosztása az IoT Hubban
Ebben a szakaszban egy olyan parancsfájlt futtat, amely összeállítja a szimulált eszközöket és a mintákat, valamint kiosztja az új eszközidentitások egy készletét az IoT Hub eszközjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az eszközjegyzékbe.

Kövesse az alábbi lépéseket a minták összeállításához és az eszközök kiosztásához az IoT Hubban:

1. Nyissa meg a **VS2015 fejlesztői parancssorát**.
2. Klónozza a GitHub-adattárat. **Ügyeljen arra, hogy olyan könyvtárba klónozza, amelynek a nevében nem szerepelnek szóközök.**
   
     ```
     git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
     ```
3. Abból a gyökérmappából, amelybe klónozta az **azure-iot-sdks** adattárat, navigáljon az **\\azure-iot-sdks\\csharp\\service\\samples** mappába, és futtassa a következőt, lecserélve a helyőrző értékét az előző szakaszban feljegyzett kapcsolati karakterláncra:
   
     ```
     setup.bat <IoT Hub Connection String>
     ```

A parancsfájl a következő műveleteket hajtja végre:

1. A **cmake** parancs futtatásával létrehoz egy Visual Studio 2015-megoldást a szimulált eszközhöz. Ez a projektfájl az **azure-iot-sdks\\csharp\\service\\samples\\cmake\\iotdm\_client\\samples\\iotdm\_simple\_sample\\iotdm\_simple\_sample.vcxproj**. Vegye figyelembe, hogy a forrásfájlok az ***azure-iot-sdks\\c\\iotdm\_client\\samples\\iotdm\_simple\_sample** mappában találhatók.
2. Összeállítja az eszköz **iotdm\_simple\_sample.vcxproj** projektfájlját.
3. Összeállítja az eszköz felügyeleti mintáit: **azure-iot-sdks\\csharp\\service\\samples\\GetStartedWithIoTDM\\GetStartedWithIoTDM.sln**.
4. Futtatja a **GenerateDevices.exe** parancsot az eszközidentitások kiosztásához az IoT Hubban. Az eszközök leírása a **sampledevices.json** fájlban található (az **azure-iot-sdks\\node\\service\\samples** mappában), és az eszközök kiosztása után a hitelesítő adatokat a **devicecreds.txt** fájl tárolja (amely az **azure-iot-sdks\\csharp\\service\\samples\\bin** könyvtárban található).

## A szimulált eszközök elindítása
Most, hogy az eszközök hozzá lettek adva az eszközjegyzékhez, elindíthatja a szimulált felügyelt eszközöket. Az Azure IoT Hubban kiosztott minden eszközidentitáshoz egy szimulált eszköz indul el.

Egy fejlesztői parancssor segítségével az **\\azure-iot-sdks\\csharp\\service\\samples\\bin** mappában futtassa a következőt:

  ```
  simulate.bat
  ```

Ez a parancsfájl az **iotdm\_simple\_sample.exe** egy példányát futtatja a **devicecreds.txt** fájlban felsorolt minden egyes eszközhöz. A szimulált eszköz addig fog futni, amíg be nem zárja a parancsablakot.

Az **iotdm\_simple\_sample** mintaalkalmazás a C nyelvhez készült Azure IoT Hub eszközfelügyeleti ügyfélkönyvtár használatával lett összeállítva, amely lehetővé teszi az Azure IoT Hub által felügyelhető IoT-eszközök létrehozását. Az eszközkészítők ezt a könyvtárat használhatják az eszköz tulajdonságainak jelentéséhez, illetve az eszköz feladataihoz szükséges végrehajtható műveletek megvalósításához. Ez a könyvtár a nyílt forráskódú Azure IoT Hub SDK-k részét képező mellékelt összetevő.

A **simulate.bat** futtatásakor a kimeneti ablakban egy adatfolyam jelenik meg. Ez a kimenet a bejövő és a kimenő forgalmat jeleníti meg, valamint tartalmazza a **printf** utasításokat az alkalmazásspecifikus visszahívási függvényekben. Ez a kimenet lehetővé teszi, hogy megtekintse a bejövő és kimenő forgalmat és azt, hogy a mintaalkalmazás hogyan kezeli a dekódolt csomagokat. Amikor az eszköz csatlakozik az IoT Hubhoz, a szolgáltatás automatikusan elkezdi megfigyelni az eszköz erőforrásait. Az IoT Hub DM-ügyfélkönyvtára ezután meghívja az eszköz visszahívásait, hogy lekérje az eszköz legutóbbi értékeit.

Az alábbiakban az **iotdm\_simple\_sample** mintaalkalmazás kimenete látható. Felül egy sikeres **REGISTERED** üzenet látható, amely az IoT Hubhoz csatlakozó **Device11-7ce4a850** azonosítójú eszközt mutatja.

> [!NOTE]
> A kevésbé részletes kimenet érdekében fordítsa le és futtassa a kiskereskedelmi konfigurációt.
> 
> 

![][img-output]

Futtassa tovább az összes szimulált eszközt az alábbi szakaszokban teljesítése közben.

## A minta eszközfelügyeleti felhasználói felület futtatása
Miután létrehozott egy IoT Hubot, és számos szimulált eszközt futtat és felügyelethez regisztrált, üzembe helyezheti a minta eszközfelügyeleti felhasználói felületet. A minta eszközfelügyeleti felhasználói felület működés közben látható példával szolgál az eszközfelügyeleti API-k használatáról az interaktív felhasználói élmény megteremtése érdekében.  A minta eszközfelügyeleti felhasználói felületről további információkat (beleértve az [ismert problémákat](https://github.com/Azure/azure-iot-device-management#knownissues)) a következő GitHub-adattárban talál: [Azure IoT device management UI][lnk-dm-github] (Azure IoT eszközfelügyeleti felhasználói felület).

A minta eszközfelügyeleti felhasználói felület lekérdezéséhez, felépítéséhez és futtatásához kövesse az alábbi lépéseket:

1. Nyisson meg egy **parancssort**.
2. Ellenőrizze, hogy az előfeltételekről szóló szakaszban megadottak szerint telepítette-e a Node.js 6.1.0-s vagy újabb verzióját, a következő beírásával: `node --version`.
3. Az alábbi parancs futtatásával klónozza az Azure IoT eszközfelügyeleti felhasználói felület GitHub-adattárát:
   
    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
4. Az Azure IoT eszközfelügyeleti felhasználói felület adattára klónozott másolatának gyökérkönyvtárában futtassa a következő parancsot a függő csomagok lekéréséhez:
   
    ```
    npm install
    ```
5. Az npm install parancs befejeződését követően futtassa a következő parancsot a kód felépítéséhez:
   
    ```
    npm run build
    ```
6. Szövegszerkesztő használatával nyissa meg a user-config.json fájlt a klónozott mappa gyökérkönyvtárában. Cserélje le a „&lt;YOUR CONNECTION STRING HERE&gt;” szöveget az IoT Hub számára az előző szakaszban megadott kapcsolati karakterláncra, majd mentse a fájlt.
7. A parancssorban futtassa a következő parancsot az eszközfelügyeleti UX-alkalmazás elindításához:
   
    ```
    npm run start
    ```
8. Amikor a parancssorban megjelenik a „Services have started” (Szolgáltatások elindultak) szöveg, nyisson meg egy webböngészőt (jelenleg az Edge/IE 11+/Safari/Chrome böngészők támogatottak), és a szimulált eszközök megtekintéséhez navigáljon az eszközfelügyeleti alkalmazáshoz a következő URL-címen: <http://127.0.0.1:3003>.
   
    ![][img-dm-ui]

Folytassa a következő eszközfelügyeleti oktatóanyaggal, és közben hagyja változatlanul futni a szimulált eszközöket és az eszközfelügyeleti alkalmazást.

## Következő lépések
További bevezetés az IoT Hub használatába: [Átjáró SDK – első lépések][lnk-gateway-SDK].

Az Azure IoT Hub eszközfelügyeleti szolgáltatásaival kapcsolatos további tudnivalókért tekintse meg a következő oktatóanyagot: [Az Azure IoT Hub eszközfelügyeleti szolgáltatásának felfedezése a minta felhasználói felület használatával][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure Portal]: https://portal.azure.com/
[Erőforráscsoportok használata az Azure-erőforrások kezeléséhez]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md



<!--HONumber=sep16_HO1-->



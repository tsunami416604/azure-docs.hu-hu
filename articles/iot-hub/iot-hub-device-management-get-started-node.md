<properties
    pageTitle="IoT Hub-eszközfelügyelet – Első lépések | Microsoft Azure"
    description="Azure IoT Hub-eszközfelügyelet C# környezethez – Első lépéseket ismertető oktatóanyag | Microsoft Azure Az Azure IoT Hubot és a C# környezetet a Microsoft Azure IoT SDK-kal együtt használhatja eszközfelügyelet megvalósításához."
    services="iot-hub"
    documentationCenter=".net"
    authors="juanjperez"
    manager="timlt"
    editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="juanpere"/>

# Ismerkedés az Azure IoT Hub for .NET eszközfelügyelettel a node.js használatával (előzetes kiadás)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Bevezetés
Az Azure IoT Hub-eszközfelügyelettel való megismerkedéshez létre kell hoznia egy Azure IoT Hubot, eszközöket kell kiosztania az IoT Hubban, el kell indítania több szimulált eszközt, és meg kell tekintenie azokat a mintaként szolgáló eszközfelügyeleti felhasználói felületen. Az oktatóanyag végigvezeti ezeken a lépéseken.

> [AZURE.NOTE]  Akkor is létre kell hoznia egy új IoT Hubot az eszközfelügyeleti képességek engedélyezéséhez, ha már rendelkezik egy meglévő IoT Hubbal, mivel a meglévő IoT Hub még nem rendelkezik ezekkel a képességekkel. Miután az eszközfelügyelet általánosan elérhetővé válik, az összes meglévő IoT Hub frissül az eszközfelügyeleti képességekkel.

## Előfeltételek

Ez az oktatóanyag azt feltételezi, hogy Ubuntu Linux fejlesztési számítógépet használ.

A következő szoftvereknek telepítve kell lenniük a lépések végrehajtásához:

- Git

- gcc (4.9-es vagy újabb verzió). A `gcc --version` parancs használatával ellenőrizheti az adott környezetben telepített aktuális verziót. Információk a gcc verziójának frissítéséről Ubuntu 14.04 rendszeren: <http://askubuntu.com/questions/466651/how-do-i-use-the-latest-gcc-4-9-on-ubuntu-14-04>.

- [CMake](https://cmake.org/download/) (2.8-as vagy újabb verzió). A `cmake --version` parancs használatával ellenőrizheti az adott környezetben telepített aktuális verziót.

- Node.js 6.1.0 vagy újabb.  A platformjának megfelelő Node.js-t a <https://nodejs.org/> webhelyről telepítheti.

- Aktív Azure-előfizetés. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].

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
  -   Jelölje be az **Eszközfelügyelet engedélyezése** jelölőnégyzetet. Ha nem jelöli be az **Eszközfelügyelet engedélyezése** jelölőnégyzetet, a minták nem fognak működni. Az **Eszközfelügyelet engedélyezése** jelölőnégyzet bejelölésével az IoT Hub olyan előzetes verziója jön létre, amely kizárólag az USA keleti régiójában, Észak-Európában és Kelet-Ázsiában támogatott, és nem termelési forgatókönyvekhez készült. Az eszközök nem telepíthetők át eszközfelügyelet-kompatibilis hubokba, illetve azokból máshova.
  -   A **Hely** területen válasszon ki egy helyet, ahol az IoT Hubot üzemeltetni kívánja. Az IoT Hub-eszközfelügyelet nyilvános előzetes verziója csak az USA keleti régiójában, Észak-Európában és Kelet-Ázsiában érhető el. A jövőben az összes régióban elérhető lesz.

4.  Az IoT Hub konfigurációs beállításainak kiválasztása után kattintson a **Létrehozás** gombra. Az IoT Hub létrehozása eltarthat néhány percig. A folyamat állapotát a **Kezdőpulton** vagy az **Értesítés** panelen ellenőrizheti.

    ![][img-monitor]

5.  Amikor az IoT Hub sikeresen létrejött, nyissa meg az új IoT Hub paneljét, jegyezze fel az **Állomásnevet**, majd kattintson a **Megosztott hozzáférési házirendek** elemre.

    ![][img-keys]

6.  Kattintson az **iothubowner** házirendre, majd másolja ki és jegyezze fel az **iothubowner** panelen látható kapcsolati karakterláncot. Másolja egy olyan helyre, ahol később elérheti, mivel szükség lesz rá az oktatóanyag további részeinek teljesítéséhez.

    > [AZURE.NOTE] Éles forgatókönyvekben ne használja az **iothubowner** hitelesítő adatait.

    ![][img-connection]

Ezzel létrehozott egy eszközfelügyelet-kompatibilis IoT Hubot. A kapcsolati karakterláncra szükség lesz az oktatóanyag további részeinek teljesítéséhez.

## A minták összeállítása és eszközök kiosztása az IoT Hubban

Ebben a szakaszban egy olyan parancsfájlt futtat, amely összeállítja a szimulált eszközöket és a mintákat, valamint kiosztja az új eszközidentitások egy készletét az IoT Hub eszközjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az eszközjegyzékbe.

Kövesse az alábbi lépéseket a minták összeállításához és az eszközök kiosztásához az IoT Hubban:

1.  Nyisson meg egy rendszerhéjat.

2.  Klónozza a GitHub-adattárat. **Ügyeljen arra, hogy olyan könyvtárba klónozza, amelynek a nevében nem szerepelnek szóközök.**

      ```
      git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
      ```

3.  Abból a gyökérmappából, amelybe klónozta az **azure-iot-sdks** adattárat, navigáljon az **azure-iot-sdks/c/build_all/linux** könyvtárba, és hajtsa végre az alábbi parancsot az előfeltételként szolgáló csomagok és a függő kódtárak telepítéséhez:

      ```
      ./setup.sh
      ```


4.  Abból a gyökérmappából, amelybe klónozta az **azure-iot-sdks** adattárat, navigáljon az **azure-iot-sdks/node/service/samples** könyvtárba, és hajtsa végre az alábbi parancsot, lecserélve a helyőrző értékét az előző szakaszban feljegyzett kapcsolati karakterláncra:

      ```
      ./setup.sh <IoT Hub Connection String>
      ```

A parancsfájl a következő műveleteket hajtja végre:

1.  Futtatja a **cmake** parancsot a szimulált eszköz összeállításához szükséges fájlok létrehozásához. A futtatható fájl az **azure-iot-sdks/node/service/samples/cmake/iotdm\_client/samples/iotdm\_simple\_sample** mappában található. Vegye figyelembe, hogy a forrásfájlok az **azure-iot-sdks/c/iotdm\_client/samples/iotdm\_simple\_sample** mappában találhatók.

2.  Összeállítja az eszköz futtatható **iotdm\_simple\_sample** fájlját.

3.  Futtatja az `npm install` parancsot a szükséges csomagok telepítéséhez.

4.  Futtatja a `node generate_devices.js` parancsot az eszközidentitások kiosztásához az IoT Hubban. Az eszközök leírása a **sampledevices.json** fájlban található. Az eszközök kiosztása után a hitelesítő adatokat a **devicecreds.txt** fájl tárolja (amely az **azure-iot-sdks/node/service/samples** könyvtárban található).

## A szimulált eszközök elindítása

Most, hogy az eszközök hozzá lettek adva az eszközjegyzékhez, elindíthatja a szimulált felügyelt eszközöket. Az Azure IoT Hubban kiosztott minden eszközidentitáshoz egy-egy szimulált eszközt kell elindítani.

Rendszerhéj használatával navigáljon az **azure-iot-sdks/node/service/samples** könyvtárhoz, majd futtassa a következőt:

  ```
  ./simulate.sh
  ```

A parancsfájl kimenetében megjelennek azok a parancsok, amelyeket futtatnia kell a **devicecreds.txt** fájlban felsorolt egyes eszközökhöz tartozó **iotdm\_simple\_sample** elindításához. Futtassa egyenként a parancsokat egy különálló terminálablakból az egyes szimulált eszközökhöz. A szimulált eszköz addig fog futni, amíg be nem zárja a parancsablakot.

Az **iotdm\_simple\_sample** alkalmazás a C-hez készült Azure IoT Hub eszközfelügyeleti ügyfélkönyvtár használatával lett összeállítva, amely lehetővé teszi az Azure IoT Hub által felügyelhető IoT-eszközök létrehozását. Az eszközkészítők ezt a könyvtárat használhatják az eszköz tulajdonságainak jelentéséhez, illetve az eszköz feladataihoz szükséges végrehajtható műveletek megvalósításához. Ez a könyvtár a nyílt forráskódú Azure IoT Hub SDK-k részét képező mellékelt összetevő.

A **simulate.sh** futtatásakor a kimeneti ablakban egy adatfolyam jelenik meg. Ez a kimenet a bejövő és a kimenő forgalmat jeleníti meg, valamint tartalmazza a **printf** utasításokat az alkalmazásspecifikus visszahívási függvényekben. Ez a kimenet lehetővé teszi, hogy megtekintse a bejövő és kimenő forgalmat és azt, hogy a mintaalkalmazás hogyan kezeli a dekódolt csomagokat. Amikor az eszköz csatlakozik az IoT Hubhoz, a szolgáltatás automatikusan elkezdi megfigyelni az eszköz erőforrásait. Az IoT Hub DM-ügyfélkönyvtára ezután meghívja az eszköz visszahívásait, hogy lekérje az eszköz legutóbbi értékeit.

Az alábbiakban az **iotdm\_simple\_sample** mintaalkalmazás kimenete látható. Felül egy sikeres **REGISTERED** üzenet látható, amely az IoT Hubhoz csatlakozó **Device11-7ce4a850** azonosítójú eszközt mutatja.

> [AZURE.NOTE]  A kevésbé részletes kimenet érdekében fordítsa le és futtassa a kiskereskedelmi konfigurációt.

![][img-output]

Futtassa tovább az összes szimulált eszközt az alábbi szakaszokban teljesítése közben.

## A minta eszközfelügyeleti felhasználói felület futtatása

Miután létrehozott egy IoT Hubot, és számos szimulált eszközt futtat és felügyelethez regisztrált, üzembe helyezheti a minta eszközfelügyeleti felhasználói felületet. A minta eszközfelügyeleti felhasználói felület működés közben látható példával szolgál az eszközfelügyeleti API-k használatáról az interaktív felhasználói élmény megteremtése érdekében.  A minta eszközfelügyeleti felhasználói felületről további információkat (beleértve az [ismert problémákat](https://github.com/Azure/azure-iot-device-management#knownissues)) a következő GitHub-adattárban talál: [Azure IoT device management UI][lnk-dm-github] (Azure IoT eszközfelügyeleti felhasználói felület).

A minta eszközfelügyeleti felhasználói felület lekérdezéséhez, felépítéséhez és futtatásához kövesse az alábbi lépéseket:

1. Nyisson meg egy rendszerhéjat.

2. Ellenőrizze, hogy az előfeltételekről szóló szakaszban megadottak szerint telepítette-e a Node.js 6.1.0-s vagy újabb verzióját, a következő beírásával: `node --version`.

3. Az alábbi parancs rendszerhéjban történő futtatásával klónozza az Azure IoT eszközfelügyeleti felhasználói felület GitHub-adattárát:

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. Az Azure IoT eszközfelügyeleti felhasználói felület adattára klónozott másolatának gyökérkönyvtárában futtassa a következő parancsot a függő csomagok lekéréséhez:

    ```
    npm install
    ```

5. Az npm install parancs befejeződését követően a rendszerhéjban futtassa a következő parancsot a kód felépítéséhez:

    ```
    npm run build
    ```

6. Szövegszerkesztő használatával nyissa meg a user-config.json fájlt a klónozott mappa gyökérkönyvtárában. Cserélje le a „&lt;YOUR CONNECTION STRING HERE&gt;” szöveget az IoT Hub számára az előző szakaszban megadott kapcsolati karakterláncra, majd mentse a fájlt.

7. A rendszerhéjban futtassa a következő parancsot az eszközfelügyeleti UX-alkalmazás elindításához:

    ```
    npm run start
    ```

8. Amikor a parancssorban megjelenik a „Services have started” (Szolgáltatások elindultak) szöveg, nyisson meg egy webböngészőt, és a szimulált eszközök megtekintéséhez navigáljon az eszközfelügyeleti alkalmazáshoz, a következő URL-címen: <http://127.0.0.1:3003>.

    ![][img-dm-ui]

Folytassa a következő eszközfelügyeleti oktatóanyaggal, és közben hagyja változatlanul futni a szimulált eszközöket és az eszközfelügyeleti alkalmazást.


## Következő lépések

További bevezetés az IoT Hub használatába: [Átjáró SDK – első lépések][lnk-gateway-SDK].

Az Azure IoT Hub eszközfelügyeleti szolgáltatásaival kapcsolatos további tudnivalókért tekintse meg a következő oktatóanyagot: [Az Azure IoT Hub eszközfelügyeleti szolgáltatásának felfedezése a minta felhasználói felület használatával][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started-node/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started-node/image2.png
[img-monitor]: media/iot-hub-device-management-get-started-node/image3.png
[img-keys]: media/iot-hub-device-management-get-started-node/image4.png
[img-connection]: media/iot-hub-device-management-get-started-node/image5.png
[img-output]: media/iot-hub-device-management-get-started-node/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started-node/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure Portal]: https://portal.azure.com/
[Erőforráscsoportok használata az Azure-erőforrások kezeléséhez]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md


<!--HONumber=sep16_HO1-->



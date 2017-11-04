## <a name="install-the-prerequisites"></a>Az előfeltételek telepítése

1. Telepítés [Visual Studio 2015-öt vagy 2017](https://www.visualstudio.com). A szabad Community Edition is használhatja, ha a licencelési teljesülnek. Ne felejtse el feltüntetni a Visual C++ és NuGet Package Manager.

1. Telepítés [git](http://www.git-scm.com) , és győződjön meg arról, hogy a parancssorból futtatható git.exe.

1. Telepítés [CMake](https://cmake.org/download/) , és győződjön meg arról, hogy a parancssorból futtatható cmake.exe. CMake 3.7.2 verzió vagy újabb ajánlott. A **.msi** telepítő legegyszerűbb lehetőség a Windows. Legalább CMake hozzáadni az elérési ÚTJÁT az aktuális felhasználó, amikor a telepítő kéri.

1. Telepítés [Python 2.7](https://www.python.org/downloads/release/python-27). Ellenőrizze, hogy a Python hozzáadása a `PATH` környezeti változó. Ugrás a **Vezérlőpult** > **rendszer és biztonság** > **rendszer** > **Speciális rendszerbeállítások**  >  **Környezeti változók**. Adja hozzá `C:\Python27` az elérési útra. 

1. A parancssorba az Azure IoT peremhálózati GitHub-tárházban, a helyi számítógép klónozása a következő parancsot:

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>A minta létrehozása

Most már lefordíthatja az IoT él futásidejű és minták a helyi számítógépen:

1. Nyissa meg **Developer-parancssorból VS 2015** vagy **Developer-parancssorból VS 2017**, attól függően, a verzió.

1. Az **iot-edge** adattár helyi példányában lépjen a gyökérmappába.

1. A build parancsfájl futtatása a következőképpen:

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

Ez a parancsfájl létrehoz egy Visual Studio megoldás, és létrehozza a megoldás. A Visual Studio megoldás található a **build** mappa a helyi példánya a **iot-edge** tárház. Build a egység tesztek futtatása, és adja hozzá szeretné a `--run-unittests` paraméter. Ha szeretne build és a végpontok közötti tesztek, vegye fel a `--run-e2e-tests`.

> [!NOTE]
> Minden alkalommal, amikor a **build.cmd** parancsfájl törli és állítja helyre a **build** a gyökérmappájában lévő mappának a helyi példányának a **iot-edge** tárházba.
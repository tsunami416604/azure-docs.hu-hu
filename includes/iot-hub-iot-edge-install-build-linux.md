## <a name="install-the-prerequisites"></a>Az előfeltételek telepítése

A jelen oktatóanyagban szereplő lépések azt feltételezik, Ubuntu Linux futtatja.

Az előfeltételként szükséges csomagok telepítéséhez nyisson meg egy kezelőfelületet, és futtassa a következő parancsokat:

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake pkg-config libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

A rendszerhéj, az Azure IoT peremhálózati GitHub-tárházban, a helyi számítógép klónozása a következő parancsot:

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>A minta létrehozása

Most már lefordíthatja az IoT él futásidejű és minták a helyi számítógépen:

1. Nyisson meg egy rendszerhéjat.

1. Az **iot-edge** adattár helyi példányában lépjen a gyökérmappába.

1. A build parancsfájl futtatása a következőképpen:

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

Ez a szkript a **cmake** segédprogramot használja a **build** nevű mappa létrehozásához az **iot-edge** adattár helyi példányának gyökérmappájában, valamint a makefile előállításához. Ezt követően a szkript felépíti a megoldást, és kihagyja az egységteszteket és a teljes körű teszteket. Build a egység tesztek futtatása, és adja hozzá szeretné a `--run-unittests` paraméter. Ha szeretne build és a végpontok közötti tesztek, vegye fel a `--run-e2e-tests`.

> [!NOTE]
> A **build.sh** szkript minden futtatásakor törli, majd újra létrehozza a **build** mappát az **iot-edge** adattár helyi példányának gyökérmappájában.
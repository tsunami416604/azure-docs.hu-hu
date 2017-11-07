## <a name="build-iot-edge"></a>Az IoT-Edge összeállítása

Ez az oktatóanyag IoT peremhálózati az egyéni modulok kommunikálni a távoli figyelési előkonfigurált megoldást használja. Ezért kell felépítenie az IoT él modulok egyéni forrás kódból. Az alábbi szakaszok azt ismertetik, hogyan IoT peremhálózati telepítéséhez, és hozni az egyéni IoT peremhálózati modult.

### <a name="install-iot-edge"></a>Az IoT-Edge telepítése

Az Intel NUC előre lefordított IoT peremhálózati szoftver telepítése a következő lépésekből áll:

1. Adja meg a szükséges intelligens csomag tárházak találhatók az Intel NUC a következő parancsok futtatásával:

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    Adja meg `y` Ha a parancs felszólítja, hogy **tartalmazzák ezt a csatornát?**.

1. Az intelligens Csomagkezelő frissítése a következő parancs futtatásával:

    ```bash
    smart update
    ```

1. Telepítse az Azure IoT biztonsági csomagot a következő parancs futtatásával:

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. Ellenőrizze a telepítést a "Hello world" példával futtatásával. Ez a minta hello world üzenet log.txT fájlba írja öt másodpercenként. Az alábbi parancsokat futtassa a "Hello world" példával:

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    Figyelmen kívül hagyása **érvénytelen argumentum** üzenetek a minta leállításakor.

    A következő paranccsal tekintheti meg a naplófájl tartalmát:

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>Hibaelhárítás

Ha hibaüzenetet kap, a "nincs csomag biztosítja a fejlesztői linux util", próbálkozzon az Intel NUC újraindul.

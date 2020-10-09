---
title: 'Oktatóanyag: IoT Edge eszköz Machine Learning konfigurálása Azure IoT Edge'
description: Ebben az oktatóanyagban egy Linux rendszerű Azure-beli virtuális gépet állít be Azure IoT Edge eszközként, amely transzparens átjáróként működik.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 1e92da6f99ccb32580d7f0286b5bcbf77d694262
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855650"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Oktatóanyag: IoT Edge-eszköz konfigurálása

> [!NOTE]
> Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. Ha ezt a cikket közvetlenül megérkezett, javasoljuk, hogy kezdje a sorozat [első cikkével](tutorial-machine-learning-edge-01-intro.md) a legjobb eredmények érdekében.

Ebben a cikkben egy Linux rendszerű Azure-beli virtuális gépet konfigurálunk olyan IoT Edge eszközként, amely transzparens átjáróként működik. Egy transzparens átjáró konfigurációja lehetővé teszi az eszközök számára, hogy az átjárón keresztül csatlakozzanak az Azure IoT Hubhoz, és nem tudta, hogy az átjáró létezik. Ugyanakkor egy, az Azure-ban IoT Hub eszközzel kommunikáló felhasználó nem ismeri a köztes átjáró eszközét. Végső soron az Edge Analytics szolgáltatást hozzáadjuk a rendszerhez IoT Edge modulok az átlátszó átjáróhoz való hozzáadásával.

A cikkben ismertetett lépéseket általában egy felhőalapú fejlesztő hajtja végre.

## <a name="create-certificates"></a>Tanúsítványok létrehozása

Ahhoz, hogy egy eszköz átjáróként működjön, képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra. Azure IoT Edge lehetővé teszi, hogy egy nyilvános kulcsokra épülő infrastruktúrát (PKI) használjon az eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben lehetővé tesszük, hogy egy alsóbb rétegbeli IoT-eszköz egy transzparens átjáróként működő IoT Edge-eszközhöz kapcsolódjon. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie a IoT Edge eszköz identitását. További információ arról, hogy IoT Edge eszközök hogyan használják a tanúsítványokat: [Azure IoT Edge tanúsítvány-használati adatok](iot-edge-certs.md).

Ebben a szakaszban létrehozjuk az önaláírt tanúsítványokat egy Docker-rendszerkép használatával, amelyet aztán kiépítünk és futtatunk. Úgy döntöttünk, hogy a lépés elvégzéséhez Docker-rendszerképet használunk, mert jelentősen csökkenti a Windows fejlesztői gépen lévő tanúsítványok létrehozásához szükséges lépések számát. A Docker-rendszerképpel kapcsolatos automatizált információk megismeréséhez tekintse meg a [bemutató tanúsítványok létrehozása a IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md) című témakört.

1. Jelentkezzen be a fejlesztői virtuális gépre.

2. Hozzon létre egy új mappát az elérési úttal és névvel `c:\edgeCertificates` .

3. Ha még nem fut, a Windows Start menüjéből indítsa el a **Docker for Windows** programot.

4. Nyissa meg a Visual Studio Code-ot.

5. Válassza ki a **fájl**  >  **megnyitása mappát..** . és válassza a **C: \\ Source \\ IoTEdgeAndMlSample \\ CreateCertificates**elemet.

6. Az Explorer ablaktáblán kattintson a jobb gombbal a **Docker** elemre, és válassza a **rendszerkép létrehozása**lehetőséget.

7. A párbeszédpanelen fogadja el az alapértelmezett értéket a rendszerkép neve és a címke: **createcertificates: Latest**.

    ![Tanúsítványok létrehozása a Visual Studio Code-ban](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Várjon, amíg a Build befejeződik.

    > [!NOTE]
    > Előfordulhat, hogy megjelenik egy figyelmeztetés a hiányzó nyilvános kulcsról. Ezt a figyelmeztetést nyugodtan figyelmen kívül hagyhatja. Hasonlóképpen egy biztonsági figyelmeztetés is megjelenik, amely azt javasolja, hogy ellenőrizze/állítsa vissza a rendszerkép engedélyeit, ami nyugodtan figyelmen kívül hagyhatja ezt a rendszerképet.

9. A Visual Studio Code Terminal ablakban futtassa a createcertificates tárolót.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. A Docker kérni fogja a **c: \\ ** meghajtó elérését. Válassza a **megosztás**lehetőséget.

11. Ha a rendszer kéri, adja meg a hitelesítő adatait.

12. Ha a tároló futása befejeződött, keresse meg a következő fájlokat a **c: \\ edgeCertificates**:

    * c: \\ edgeCertificates \\ tanúsítványok \\ Azure-IOT-test-only. root. ca. CERT. PEM
    * c: \\ edgeCertificates \\ tanúsítványok \\ New-Edge-Device-Full-Chain. CERT. PEM
    * c: \\ edgeCertificates \\ tanúsítványok \\ New-Edge-Device. CERT. PEM
    * c: \\ edgeCertificates \\ tanúsítványok \\ New-Edge-Device. CERT. pfx
    * c: \\ edgeCertificates \\ Private \\ New-Edge-Device. key. PEM

## <a name="upload-certificates-to-azure-key-vault"></a>Tanúsítványok feltöltése a Azure Key Vaultba

Ha biztonságosan szeretné tárolni a tanúsítványokat, és több eszközről is elérhetővé kívánja tenni őket, feltöltjük a tanúsítványokat a Azure Key Vaultba. Ahogy az a fenti listából is látható, két típusú tanúsítványfájl létezik: PFX és PEM. A PFX-t Key Vault, Key Vaultre feltöltött tanúsítványként kezeljük. A PEM-fájlok egyszerű szövegként jelennek meg, és Key Vault titokként kezeljük őket. A [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks)futtatásával létrehozott Azure Machine learning munkaterülethez társított Key Vault fogjuk használni.

1. A [Azure Portal](https://portal.azure.com)navigáljon a Azure Machine learning munkaterülethez.

2. A Azure Machine Learning munkaterület áttekintés lapján keresse meg a **Key Vault**nevét.

    ![Key Vault-név másolása](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. A fejlesztői gépen töltse fel a tanúsítványokat Key Vaultba. Cserélje le az **\<subscriptionId\>** és **\<keyvaultname\>** az erőforrás adatait.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Ha a rendszer kéri, jelentkezzen be az Azure-ba.

5. A szkript néhány percig fut az új Key Vault bejegyzéseket felsoroló kimenettel.

    ![Key Vault parancsfájl kimenete](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>IoT Edge-eszköz létrehozása

Ha Azure IoT Edge eszközt szeretne csatlakoztatni egy IoT hubhoz, először létre kell hoznia egy identitást az eszközhöz a központban. A kapcsolódási karakterláncot a felhőben található eszköz-identitásból fogjuk használni, hogy a futtatókörnyezetet a IoT Edge eszközön konfigurálja. Ha egy konfigurált eszköz csatlakozik a központhoz, a modulok üzembe helyezésére és üzenetek küldésére van lehetőség. A fizikai IoT Edge eszköz konfigurációját a IoT hub megfelelő eszköz-identitásának módosításával is megváltoztathatja.

Ebben az oktatóanyagban létrehozjuk az új eszköz identitását a Visual Studio Code használatával. Ezeket a lépéseket a [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)vagy az [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)használatával is elvégezheti.

1. A fejlesztői gépen nyissa meg a Visual Studio Code-ot.

2. Bontsa ki az **Azure IoT hub** keretet a Visual Studio Code Explorer nézetből.

3. Kattintson a három pontra, majd válassza az **IoT Edge eszköz létrehozása**lehetőséget.

4. Adja meg az eszköz nevét. A kényelmes használat érdekében a **aaTurbofanEdgeDevice** nevet használjuk, így a felsorolt eszközök tetejére rendezi a rendszer.

5. Az új eszköz megjelenik az eszközök listájában.

    ![Új aaTurbofanEdgeDevice megtekintése a VS Code Explorerben](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Azure-beli virtuális gép üzembe helyezése

Az Azure Marketplace-en az Ubuntu-rendszerképeken a [Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) segítségével hozhatja létre IoT Edge eszközét ehhez az oktatóanyaghoz. Az Ubuntu-lemezkép Azure IoT Edge telepíti a legújabb Azure IoT Edge futtatókörnyezetet és annak függőségeit az indításkor. A virtuális gépet egy PowerShell-parancsfájl, `Create-EdgeVM.ps1` egy Resource Manager-sablon, `IoTEdgeVMTemplate.json` és egy rendszerhéj-parancsfájl használatával telepítjük `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Programozott telepítés engedélyezése

Ha a piactéren parancsfájl-alapú telepítésben szeretné használni a rendszerképet, engedélyeznie kell a programozott üzembe helyezést a rendszerképhez.

1. Jelentkezzen be az Azure Portalra.

1. Válassza az **Összes szolgáltatás** elemet.

1. A keresősáv mezőben adja meg és válassza ki a **piactér**lehetőséget.

1. A piactéren keresse meg és válassza ki az **Ubuntu-Azure IoT Edge**.

1. A programozott módon történő üzembe helyezéshez válassza az **első lépések** hivatkozást.

1. Kattintson az **Engedélyezés** gombra, majd a **Mentés**elemre.

    ![Programozott telepítés engedélyezése virtuális géphez](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Ekkor megjelenik egy sikeres értesítés.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Ezután futtassa a parancsfájlt a IoT Edge eszközhöz tartozó virtuális gép létrehozásához.

1. Nyisson meg egy PowerShell-ablakot, és navigáljon a **EdgeVM** könyvtárba.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Futtassa a szkriptet a virtuális gép létrehozásához.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Ha a rendszer kéri, adja meg az egyes paraméterek értékeit. Az előfizetés, az erőforráscsoport és a hely esetében javasoljuk, hogy a jelen oktatóanyagban szereplő összes erőforráshoz ugyanazt használja.

    * **Azure-előfizetés azonosítója**: a Azure Portal található
    * **Erőforráscsoport neve**: emlékezetes név az oktatóanyag erőforrásainak csoportosításához
    * **Hely**: az az Azure-hely, ahol a virtuális gép létre lesz hozva. Például: westus2 vagy northeurope. További információkért tekintse meg az összes [Azure-helyet](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: annak a rendszergazdai fióknak a neve, amelyet a virtuális gépre való bejelentkezéshez használni fog
    * **AdminPassword**: a virtuális gépen a AdminUsername beállított jelszó

4. Ahhoz, hogy a parancsfájl be tudja állítani a virtuális gépet, be kell jelentkeznie az Azure-ba az Ön által használt Azure-előfizetéshez társított hitelesítő adatokkal.

5. A parancsfájl megerősíti a virtuális gép létrehozásához szükséges információkat. Válassza az **y** vagy az **ENTER billentyűt** a folytatáshoz.

6. A szkript több percig fut, ahogy a következő lépéseket hajtja végre:

    * Hozzon létre egy erőforráscsoportot, ha az már nem létezik
    * A virtuális gép létrehozása
    * NSG-kivételek hozzáadása a virtuális géphez a 22-es (SSH), a 5671 (AMQP), a 5672 (AMPQ) és a 443 (TLS) portok esetében
    * Az [Azure CLI](/cli/azure/install-azure-cli-apt) telepítése

7. A parancsfájl az SSH kapcsolati karakterláncot adja meg a virtuális géphez való csatlakozáshoz. Másolja a következő lépéshez tartozó kapcsolatok karakterláncát.

    ![SSH-kapcsolatok karakterláncának másolása virtuális géphez](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Kapcsolódás a IoT Edge eszközhöz

A következő néhány szakaszban az általunk létrehozott Azure-beli virtuális gépet konfiguráljuk. Az első lépés a virtuális géphez való kapcsolódás.

1. Nyisson meg egy parancssort, és illessze be a szkript kimenetéről másolt SSH-kapcsolatok karakterláncát. Adja meg saját adatait a Felhasználónév, az utótag és a régió számára az előző szakaszban a PowerShell-szkriptnek megadott értékek alapján.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Amikor a rendszer felszólítja a gazdagép hitelességének ellenőrzésére, írja be az **Igen** értéket, és válassza az **ENTER billentyűt**.

3. Ha a rendszer kéri, adja meg a jelszavát.

4. Az Ubuntu megjeleníti az üdvözlő üzenetet, és a következőhöz hasonló promptot kell látnia: `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Key Vault tanúsítványok letöltése

A cikk korábbi szakaszaiban a tanúsítványokat a Key Vault, hogy elérhetővé tegyék azokat a IoT Edge eszközön és a levélben lévő eszközön. A levél eszköz olyan alsóbb rétegbeli eszköz, amely az IoT Edge eszközt használja átjáróként a IoT Hub való kommunikációhoz.

Az oktatóanyagban később fogunk foglalkozni a levél eszközzel. Ebben a szakaszban a tanúsítványokat a IoT Edge eszközre kell letölteni.

1. Jelentkezzen be az Azure-ba az Azure CLI-vel a Linux rendszerű virtuális gépen futó SSH-munkamenetből.

    ```azurecli
    az login
    ```

1. A rendszer kérni fogja, hogy nyisson meg egy böngészőt, <https://microsoft.com/devicelogin> és adjon meg egy egyedi kódot. Ezeket a lépéseket a helyi gépen hajthatja végre. A hitelesítés befejezése után zárd be a böngészőablakot.

1. Sikeres hitelesítés esetén a Linux rendszerű virtuális gép bejelentkezik, és kilistázza az Azure-előfizetéseit.

1. Állítsa be az Azure CLI-parancsokhoz használni kívánt Azure-előfizetést.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Hozzon létre egy könyvtárat a virtuális gépen a tanúsítványok számára.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Töltse le a Key vaultban tárolt tanúsítványokat: New-Edge-Device-Full-Chain. CERT. PEM, New-Edge-Device. key. PEM, és Azure-IOT-test-only. root. ca. CERT. PEM

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>A IoT Edge eszköz konfigurációjának frissítése

A IoT Edge futtatókörnyezet a fájlt használja a konfiguráció megőrzéséhez `/etc/iotedge/config.yaml` . A fájlban háromféle információt kell frissíteni:

* **Eszköz-összekapcsolási karakterlánc**: az eszköz identitásának IoT hub
* **Tanúsítványok:** az alsóbb rétegbeli eszközökkel létesített kapcsolatokhoz használandó tanúsítványok
* **Állomásnév:** a virtuális gép IoT Edge eszköz teljes TARTOMÁNYNEVE (FQDN).

A IoT Edge virtuális gép létrehozásához használt Ubuntu-rendszerkép *Azure IoT Edge* egy olyan rendszerhéj-parancsfájlt tartalmaz, amely frissíti a config. YAML fájlt a kapcsolódási karakterlánccal.

1. A Visual Studio Code-ban kattintson a jobb gombbal a IoT Edge eszközre, majd válassza az **eszköz csatlakoztatási karakterláncának másolása**lehetőséget.

    ![A Visual Studio Code-ból származó kapcsolatok karakterláncának másolása](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Az SSH-munkamenetben futtassa a parancsot, hogy frissítse a config. YAML fájlt az eszköz kapcsolati karakterláncával.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

A következő lépésben frissíteni fogjuk a tanúsítványokat és a gazdagépet a config. YAML fájl közvetlen szerkesztésével.

1. Nyissa meg a config. YAML fájlt.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. A config. YAML tanúsítványok szakaszának frissítésével távolítsa el a sortávolságot `#` , és állítsa be az elérési utat úgy, hogy a fájl a következő példához hasonlóan néz ki:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Győződjön meg arról, hogy a (z) **:** sor nem rendelkezik korábbi szóközökkel, és hogy a beágyazott tanúsítványok mindegyike két szóközzel van behúzva.

    A nano-on a jobb gombbal kattintva beillesztheti a vágólap tartalmát az aktuális kurzor pozícióba. A karakterlánc cseréjéhez a nyílbillentyűk segítségével navigáljon a lecserélni kívánt karakterláncra, törölje a karakterláncot, majd kattintson a jobb gombbal a pufferből való beillesztéshez.

3. A Azure Portal navigáljon a virtuális géphez. Másolja a DNS-nevet (a gép teljes tartománynevét) az **Áttekintés** szakaszból.

4. Illessze be a teljes tartománynevet a config. YML állomásnév szakaszába. Győződjön meg arról, hogy a név mind kisbetűs.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Mentse és zárjuk be a fájlt ( `Ctrl + X` , `Y` , `Enter` ).

6. Indítsa újra a iotedge démont.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Győződjön meg arról, hogy a IoT Edge démon állapota (a parancs után írja be a ": q" parancsot a kilépéshez).

    ```bash
    systemctl status iotedge
    ```

8. Ha hibákat lát (a "hiba" előtaggal ellátott színes szöveg \[ \] ), akkor a rendszer részletes információkat vizsgál a démon naplóiban.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="next-steps"></a>További lépések

Épp most fejezte be az Azure-beli virtuális gépek Azure IoT Edge transzparens átjáróként való konfigurálását. Elkezdtük a Azure Key Vaultba feltöltött tesztelési tanúsítványok generálását. Ezután egy parancsfájl-és Resource Manager-sablonnal telepítettük a virtuális gépet az "Ubuntu Server 16,04 LTS + Azure IoT Edge Runtime" rendszerképpel az Azure piactéren. Az SSH-n keresztül csatlakoztatott virtuális géppel az Azure-ba jelentkezett be, és a tanúsítványokat a Key Vaultból letöltöttük. A config. YAML fájl frissítésével számos frissítést készítettünk a IoT Edge futtatókörnyezet konfigurációjában.

IoT Edge modulok létrehozásához folytassa a következő cikkel.

> [!div class="nextstepaction"]
> [Egyéni IoT Edge-modulok létrehozása és üzembe helyezése](tutorial-machine-learning-edge-06-custom-modules.md)

---
title: 'Oktatóanyag: IoT Edge eszköz Machine Learning konfigurálása Azure IoT Edge'
description: Ebben az oktatóanyagban egy Linux rendszerű Azure-beli virtuális gépet állít be Azure IoT Edge eszközként, amely transzparens átjáróként működik.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a9f9c6ebd55752ea5a3400da8d42b6c6487277df
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514646"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Oktatóanyag: IoT Edge-eszköz konfigurálása

> [!NOTE]
> Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. Ha ezt a cikket közvetlenül megérkezett, javasoljuk, hogy kezdje a sorozat [első cikkével](tutorial-machine-learning-edge-01-intro.md) a legjobb eredmények érdekében.

Ebben a cikkben egy Linux rendszerű Azure-beli virtuális gépet konfigurálunk olyan Azure IoT Edge eszközként, amely transzparens átjáróként működik. Az átlátszó átjáró konfigurációja lehetővé teszi az eszközök számára, hogy az átjárón keresztül csatlakozzanak az Azure IoT Hubhoz anélkül, hogy az átjáró létezik. Ugyanakkor a IoT Hub eszközzel kommunikáló felhasználók nem ismerik a köztes átjáró eszközét. Végső soron az átlátszó átjáróval vesszük fel a peremhálózat-elemzéseket a rendszerbe azáltal, hogy IoT Edge modulokat adnak hozzá az átjáróhoz.

A cikkben ismertetett lépéseket általában egy felhőalapú fejlesztő hajtja végre.

## <a name="generate-certificates"></a>Tanúsítványok előállítása

Ahhoz, hogy egy eszköz átjáróként működjön, képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra. Azure IoT Edge lehetővé teszi, hogy egy nyilvános kulcsokra épülő infrastruktúrát (PKI) használjon az eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben lehetővé tesszük, hogy egy alsóbb rétegbeli eszköz olyan IoT Edge-eszközhöz kapcsolódjon, amely transzparens átjáróként működik. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie a IoT Edge eszköz identitását. További információ arról, hogy IoT Edge eszközök hogyan használják a tanúsítványokat: [Azure IoT Edge tanúsítvány-használati adatok](iot-edge-certs.md).

Ebben a szakaszban létrehozjuk az önaláírt tanúsítványokat egy Docker-rendszerkép használatával, amelyet aztán kiépítünk és futtatunk. Úgy döntöttünk, hogy egy Docker-rendszerképet használunk ennek a lépésnek a végrehajtásához, mert jelentősen csökkentette a tanúsítványok a Windows fejlesztői gépen való létrehozásához szükséges lépések számát. A Docker-rendszerképpel kapcsolatos automatizált információk megismeréséhez tekintse meg a [bemutató tanúsítványok létrehozása a IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md) című témakört.

1. Jelentkezzen be a fejlesztői virtuális gépre.

2. Nyisson meg egy parancssort, és futtassa a következő parancsot egy könyvtár létrehozásához a virtuális gépen.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. A Windows Start menüjéből indítsa el a **Docker for Windows** programot.

4. Nyissa meg a Visual Studio Code-ot.

5. Válassza a **fájl** > **mappa megnyitása..** . lehetőséget, és válassza a **C:\\forrás\\IoTEdgeAndMlSample\\CreateCertificates**elemet.

6. Kattintson a jobb gombbal a Docker, és válassza a **rendszerkép létrehozása**lehetőséget.

7. A párbeszédpanelen fogadja el az alapértelmezett értéket a rendszerkép neve és a címke: **createcertificates: Latest**.

8. Várjon, amíg a Build befejeződik.

    > [!NOTE]
    > Előfordulhat, hogy megjelenik egy figyelmeztetés a hiányzó nyilvános kulcsról. Ezt a figyelmeztetést nyugodtan figyelmen kívül hagyhatja. Hasonlóképpen egy biztonsági figyelmeztetés is megjelenik, amely azt javasolja, hogy ellenőrizze/állítsa vissza a rendszerkép engedélyeit, ami nyugodtan figyelmen kívül hagyhatja ezt a rendszerképet.

9. A Visual Studio Code Terminal ablakban futtassa a createcertificates tárolót.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. A Docker kérni fogja a (z) **c:\\** meghajtó elérését. Válassza a **megosztás**lehetőséget.

11. Ha a rendszer kéri, adja meg a hitelesítő adatait.

12. Ha a tároló futása befejeződött, keresse meg a következő fájlokat a **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\Azure-IOT-test-only. root. ca. CERT. PEM
    * c:\\edgeCertificates\\certs\\New-Edge-Device-Full-Chain. CERT. PEM
    * c:\\edgeCertificates\\certs\\New-Edge-Device. CERT. PEM
    * c:\\edgeCertificates\\certs\\New-Edge-Device. CERT. pfx
    * c:\\edgeCertificates\\Private\\New-Edge-Device. key. PEM

## <a name="upload-certificates-to-azure-key-vault"></a>Tanúsítványok feltöltése a Azure Key Vaultba

Ha biztonságosan szeretné tárolni a tanúsítványokat, és több eszközről is elérhetővé kívánja tenni őket, feltöltjük a tanúsítványokat a Azure Key Vaultba. Ahogy az a fenti listából is látható, két típusú tanúsítványfájl létezik: PFX és PEM. A PFX-t Key Vault, Key Vaultre feltöltött tanúsítványként kezeljük. A PEM-fájlok egyszerű szövegként jelennek meg, és Key Vault titokként kezeljük őket. A [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks)futtatásával létrehozott Azure Machine learning munkaterülethez társított Key Vault fogjuk használni.

1. A [Azure Portal](https://portal.azure.com)navigáljon a Azure Machine learning munkaterülethez.

2. A Azure Machine Learning munkaterület áttekintés lapján keresse meg a **Key Vault**nevét.

    ![Key Vault-név másolása](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. A fejlesztői gépen töltse fel a tanúsítványokat Key Vaultba. Cserélje le **\<subscriptionId\>** és **\<keyvaultname\>** az erőforrás adataira.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Ha a rendszer kéri, jelentkezzen be az Azure-ba.

5. A szkript néhány percig fut az új Key Vault bejegyzéseket felsoroló kimenettel.

    ![Key Vault parancsfájl kimenete](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>IoT Edge-eszköz létrehozása

Ha Azure IoT Edge eszközt szeretne csatlakoztatni egy IoT hubhoz, először létre kell hoznia egy identitást az eszközhöz a központban. A kapcsolódási karakterláncot a felhőben található eszköz-identitásból fogjuk használni, hogy a futtatókörnyezetet a IoT Edge eszközön konfigurálja. Ha az eszköz konfigurálva lett, és csatlakozik a központhoz, a modulok üzembe helyezésére és üzenetek küldésére van lehetőség. A fizikai IoT Edge eszköz konfigurációját a IoT hub megfelelő eszköz-identitásának konfigurációjának módosításával is megváltoztathatja.

Ebben az oktatóanyagban létrehozjuk az új eszköz identitását a Visual Studio Code használatával. Ezeket a lépéseket a [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)vagy az [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)használatával is elvégezheti.

1. A fejlesztői gépen nyissa meg a Visual Studio Code-ot.

2. Nyissa meg az **Azure IoT hub eszközök** keretét a Visual Studio Code Explorer nézetből.

3. Kattintson a három pontra, majd válassza az **IoT Edge eszköz létrehozása**lehetőséget.

4. Adja meg az eszköz nevét. A kényelmes használat érdekében a **aaTurbofanEdgeDevice** használjuk, így a korábban az eszköz-hám használatával létrehozott összes ügyféleszközök elküldheti a tesztelési adatmennyiséget.

5. Az új eszköz megjelenik az eszközök listájában.

    ![Új aaTurbofanEdgeDevice megtekintése a VS Code Explorerben](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Azure-beli virtuális gép üzembe helyezése

Az Azure Marketplace-en az Ubuntu-rendszerképeken a [Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) segítségével hozhatja létre IoT Edge eszközét ehhez az oktatóanyaghoz. Az Ubuntu-lemezkép Azure IoT Edge telepíti a legújabb Azure IoT Edge futtatókörnyezetet és annak függőségeit az indításkor. A virtuális gépet egy PowerShell-szkripttel telepítjük, `Create-EdgeVM.ps1`; Resource Manager-sablon, `IoTEdgeVMTemplate.json`; és egy rendszerhéj-parancsfájl, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Programozott telepítés engedélyezése

Ha a piactéren parancsfájl-alapú telepítésben szeretné használni a rendszerképet, engedélyeznie kell a programozott üzembe helyezést a rendszerképhez.

1. Jelentkezzen be az Azure portálra.

1. Válassza az **Összes szolgáltatás** elemet.

1. A keresősáv mezőben adja meg és válassza ki a **piactér**lehetőséget.

1. A keresősáv mezőben adja meg és válassza ki **a Azure IoT Edge Ubuntun**.

1. Bejelöli a **kívánt programozott üzembe helyezést? Első lépések** hivatkozása.

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
    * NSG-kivételek hozzáadása a virtuális géphez a 22-es (SSH), a 5671 (AMQP), a 5672 (AMPQ) és a 443 (SSL) portok esetében
    * Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)telepítése)

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

4. Az Ubuntu megjeleníti az üdvözlő üzenetet, és megjelenik egy üzenet, például `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Key Vault tanúsítványok letöltése

A cikk korábbi szakaszaiban a tanúsítványokat a Key Vault, hogy elérhetővé tegyék azokat a IoT Edge eszközön és a levélben lévő eszközön, amely egy olyan alsóbb rétegbeli eszköz, amely az IoT Edge eszközt használja átjáróként az IoT Hubsal való kommunikációhoz. Az oktatóanyagban később fogunk foglalkozni a levél eszközzel. Ebben a szakaszban a tanúsítványokat a IoT Edge eszközre kell letölteni.

1. Jelentkezzen be az Azure-ba az Azure CLI-vel a Linux rendszerű virtuális gépen futó SSH-munkamenetből.

    ```bash
    az login
    ```

1. A rendszer megkéri, hogy nyisson meg egy böngészőt <https://microsoft.com/devicelogin> és adjon meg egy egyedi kódot. Ezeket a lépéseket a helyi gépen hajthatja végre. A hitelesítés befejezése után zárd be a böngészőablakot.

1. Sikeres hitelesítés esetén a Linux rendszerű virtuális gép bejelentkezik, és kilistázza az Azure-előfizetéseit.

1. Állítsa be az Azure CLI-parancsokhoz használni kívánt Azure-előfizetést.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Hozzon létre egy könyvtárat a virtuális gépen a tanúsítványok számára.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Töltse le a Key vaultban tárolt tanúsítványokat: New-Edge-Device-Full-Chain. CERT. PEM, New-Edge-Device. key. PEM, és Azure-IOT-test-only. root. ca. CERT. PEM

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>A IoT Edge eszköz konfigurációjának frissítése

Az IoT Edge Runtime a fájl/etc/iotedge/config.YAML használja a konfiguráció megőrzéséhez. A fájlban háromféle információt kell frissíteni:

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

2. Frissítse a config. YAML tanúsítványok szakaszát a vezető `#` eltávolításával, és állítsa be az elérési utat úgy, hogy a fájl a következő példához hasonlóan néz ki:

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

5. Mentse és zárjuk be a fájlt (`Ctrl + X`, `Y`, `Enter`).

6. Indítsa újra a iotedge démont.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Győződjön meg arról, hogy a IoT Edge démon állapota (a parancs után írja be a ": q" parancsot a kilépéshez).

    ```bash
    systemctl status iotedge
    ```

8. Ha hibák jelennek meg (a "\[hiba\]" előtaggal ellátott színes szöveg), akkor a rendszer részletes információt tartalmaz a következő helyen: a Daemon-naplók.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Következő lépések

Épp most fejezte be az Azure-beli virtuális gépek Azure IoT Edge transzparens átjáróként való konfigurálását. Először a Azure Key Vaultba feltöltött tesztelési tanúsítványok létrehozásával kezdtük el. Ezután egy parancsfájl-és Resource Manager-sablonnal telepítettük a virtuális gépet az "Ubuntu Server 16,04 LTS + Azure IoT Edge Runtime" rendszerképpel az Azure piactéren. A szkript az Azure CLI telepítésének további lépéseit vette igénybe (az[Azure CLI telepítése az apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)használatával). Az SSH-n keresztül csatlakoztatott virtuális gépekkel, az Azure-ba való bejelentkezéssel, a Key Vault tanúsítványok letöltésével, valamint az IoT Edge Runtime konfigurációjának számos frissítését a config. YAML fájl frissítésével végezheti el. További információ a IoT Edge átjáróként való használatáról: [IoT Edge eszköz átjáróként](iot-edge-as-gateway.md)való használata. Az IoT Edge eszköz transzparens átjáróként való konfigurálásával kapcsolatos további információkért lásd: [IoT Edge-eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)működéshez.

IoT Edge modulok létrehozásához folytassa a következő cikkel.

> [!div class="nextstepaction"]
> [Egyéni IoT Edge-modulok létrehozása és üzembe helyezése](tutorial-machine-learning-edge-06-custom-modules.md)

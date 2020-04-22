---
title: 'Oktatóanyag: IoT Edge-eszköz konfigurálása – Machine Learning az Azure IoT Edge-en'
description: Ebben az oktatóanyagban egy Azure virtuális gép Linux linuxos azure IoT Edge-eszközként konfigurálható, amely átlátszó átjáróként működik.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 353ed321ce3b6161b28bf67d852a81f809880603
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733012"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Oktatóanyag: IoT Edge-eszköz konfigurálása

> [!NOTE]
> Ez a cikk az Azure Machine Learning IoT Edge-en való használatával kapcsolatos oktatóanyag sorozatának része. Ha megérkezett ezt a cikket közvetlenül, javasoljuk, hogy kezdődik az [első cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredményt.

Ebben a cikkben egy Linuxot futtató Azure virtuális gépet konfigurálunk egy IoT Edge-eszközként, amely átlátszó átjáróként működik. Az átlátható átjárókonfiguráció lehetővé teszi, hogy az eszközök az átjárón keresztül csatlakozzanak az Azure IoT Hubhoz anélkül, hogy tudnák, hogy az átjáró létezik. Ugyanakkor az Azure IoT Hub ban az eszközökkel interakcióba lépő felhasználó nem ismeri a köztes átjáróeszközt. Végső soron az IoT Edge-modulok hozzáadásával hozzáadjuk a rendszerhez az IoT Edge-modulokat az átlátszó átjáróhoz.

A cikkben ismertetett lépéseket általában egy felhőalapú fejlesztő hajtja végre.

## <a name="create-certificates"></a>Tanúsítványok létrehozása

Ahhoz, hogy egy eszköz átjáróként működjön, képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos csatlakozásra. Az Azure IoT Edge lehetővé teszi, hogy egy nyilvános kulcsú infrastruktúra (PKI) használatával biztonságos kapcsolatokat létesíteni az eszközök között. Ebben az esetben engedélyezzük egy alsóbb rétegbeli IoT-eszköz számára, hogy egy átlátszó átjáróként működő IoT Edge-eszközhöz csatlakozzon. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie az IoT Edge-eszköz identitását. Az IoT Edge-eszközök tanúsítványai használatáról az [Azure IoT Edge-tanúsítvány használatának részletei című témakörben talál további](iot-edge-certs.md)információt.

Ebben a szakaszban hozzuk létre az önaláírt tanúsítványok egy Docker-rendszerkép, amely et aztán létre és futtat. Úgy döntöttünk, hogy egy Docker-rendszerképet használunk ennek a lépésnek a végrehajtásához, mivel jelentősen csökkenti a tanúsítványok létrehozásához szükséges lépések számát a Windows fejlesztői gépen. Lásd: [Demótanúsítványok létrehozása az IoT Edge-eszközfunkciók teszteléséhez,](how-to-create-test-certificates.md) hogy megértse, mit automatizálunk a Docker-lemezképpel.

1. Jelentkezzen be a fejlesztői virtuális gépbe.

2. Hozzon létre egy új `c:\edgeCertificates`mappát az elérési úttal és a névvel.

3. Ha még nem futott, indítsa el **a Docker for Windows alkalmazást** a Windows Start menüjéből.

4. Nyissa meg a Visual Studio Code-ot.

5. Válassza **a Fájlmegnyitása** > **mappa...** és a **C:\\\\forrás\\IoTEdgeAndMlSample CreateCertificates**lehetőséget.

6. Az Intéző ablaktábláján kattintson a jobb gombbal a **dockerfile** elemre, és válassza **a Kép összeállítása parancsot.**

7. A párbeszédpanelen fogadja el a kép név és címke alapértelmezett értékét: **createcertificates: latest**.

    ![Tanúsítványok létrehozása a Visual Studio-kódban](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Várja meg, amíg befejeződik a build.

    > [!NOTE]
    > Előfordulhat, hogy egy hiányzó nyilvános kulcsra vonatkozó figyelmeztetés jelenik meg. Nyugodtan figyelmen kívül hagyhatja ezt a figyelmeztetést. Hasonlóképpen megjelenik egy biztonsági figyelmeztetés, amely azt javasolja, hogy ellenőrizze / alaphelyzetbe engedélyeket a kép, amely biztonságosan figyelmen kívül hagyja ezt a képet.

9. A Visual Studio Code terminálablakában futtassa a createcertificates tárolót.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. A Docker kérni fogja a **c:\\ ** meghajtó elérését. Válassza **a Megosztás lehetőséget.**

11. Adja meg a hitelesítő adatait, amikor a rendszer kéri.

12. Miután a tároló futása befejeződik, ellenőrizze a következő fájlokat **\\c: edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\\\edgeCertificates\\private-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Tanúsítványok feltöltése az Azure Key Vaultba

Tanúsítványok biztonságos tárolásához és több eszközről való elérhetővé tétele érdekében feltöltjük a tanúsítványokat az Azure Key Vaultba. Mint látható a fenti listából, kétféle tanúsítvány fájlunk van: PFX és PEM. A PFX-et key vault-tanúsítványokként kezeljük, amelyeket fel kell tölteni a Key Vaultba. A PEM fájlok egyszerű szöveg, és kezeljük őket Key Vault titkos kulcsokat. Az [Azure-jegyzetfüzetek](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks)futtatásával létrehozott Azure Machine Learning-munkaterülethez társított Key Vaultot fogjuk használni.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az Azure Machine Learning-munkaterületet.

2. Az Azure Machine Learning munkaterület áttekintő lapján keresse meg a **Key Vault**nevét.

    ![Kulcstartó nevének másolása](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. A fejlesztői gépen töltse fel a tanúsítványokat a Key Vaultba. Cserélje le ** \<\> az előfizetési azonosítót** és ** \<a keyvaultname-t\> ** az erőforrásadatokra.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Ha a rendszer kéri, jelentkezzen be az Azure-ba.

5. A parancsfájl néhány percig fog futni az új Key Vault-bejegyzéseket tartalmazó kimenettel.

    ![Key Vault parancsfájl kimenete](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>IoT Edge-eszköz létrehozása

Az Azure IoT Edge-eszköz egy IoT hubhoz való csatlakoztatásához először hozzon létre egy identitást az eszköz höz a hubban. A kapcsolati karakterláncot a felhőben lévő eszközidentitásból vesszük, és az IoT Edge-eszközön konfiguráljuk a futásidejűt. Miután egy konfigurált eszköz csatlakozik a hubhoz, képesek vagyunk telepíteni a modulokat és üzeneteket küldeni. A fizikai IoT Edge-eszköz konfigurációját is módosíthatjuk a megfelelő eszközidentitás ioT hubban való módosításával.

Ebben az oktatóanyagban a Visual Studio-kód használatával hozzuk létre az új eszközidentitást. Ezeket a lépéseket az [Azure Portalon](how-to-register-device.md#register-in-the-azure-portal)vagy az [Azure CLI-n](how-to-register-device.md#register-with-the-azure-cli)is végrehajthatja.

1. A fejlesztőgépen nyissa meg a Visual Studio Code alkalmazást.

2. Bontsa ki az **Azure IoT** Hub-keretet a Visual Studio Kódkezelő nézetből.

3. Kattintson a három pontra, és válassza **az IoT Edge-eszköz létrehozása**lehetőséget.

4. Adjon nevet az eszköznek. A kényelem érdekében az **aaaTurbofanEdgeDevice nevet használjuk,** így a felsorolt eszközök tetejére rendezi.

5. Az új eszköz megjelenik az eszközök listájában.

    ![Új aaTurbofanEdgeDevice megtekintése a VS Code explorer ben](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Az Azure virtuális gép telepítése

Az [Azure IoT Edge az Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) Marketplace-ről az Azure Marketplace-ről az IoT Edge-eszköz létrehozásához az oktatóanyaghoz. Az Azure IoT Edge ubuntulemezkép telepíti a legújabb Azure IoT Edge futásidejű és az indítási függőségek. A virtuális gép telepítése PowerShell-parancsfájl használatával; `Create-EdgeVM.ps1` erőforrás-kezelősablon, `IoTEdgeVMTemplate.json`; és egy shell `install packages.sh`script, .

### <a name="enable-programmatic-deployment"></a>Programozott telepítés engedélyezése

A marketplace-ről a rendszerkép egy parancsfájlalapú központi telepítés, engedélyeznie kell a programozott központi telepítés a rendszerkép.

1. Jelentkezzen be az Azure portálra.

1. Válassza az **Összes szolgáltatás** elemet.

1. A keresősávon adja meg a Marketplace elemet, és válassza a **Piactér**lehetőséget.

1. A Marketplace keresősávjában adja meg és válassza **az Azure IoT Edge parancsot az Ubuntun.**

1. A programozott telepítéshez válassza az **Első** lépések hivatkozást.

1. Válassza az **Engedélyezés** gombot, majd a **Mentés gombot.**

    ![Programozott központi telepítés engedélyezése a virtuális gépszámára](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Látni fogja a siker értesítést.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Ezután futtassa a parancsfájlt az IoT Edge-eszköz virtuális gépének létrehozásához.

1. Nyisson meg egy PowerShell-ablakot, és keresse meg az **EdgeVM** könyvtárat.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Futtassa a parancsfájlt a virtuális gép létrehozásához.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Amikor a rendszer kéri, adja meg az értékeket az egyes paraméterekhez. Előfizetés, erőforráscsoport és hely esetén azt javasoljuk, hogy ugyanazt használja, mint az összes erőforrás az oktatóanyag ban.

    * **Azure-előfizetés-azonosító:** megtalálható az Azure Portalon
    * **Erőforráscsoport neve:** emlékezetes név az oktatóanyag erőforrásainak csoportosításához
    * **Hely**: Az Azure-hely, ahol a virtuális gép jön létre. Például westus2 vagy észak-európai. További információkért tekintse meg az összes [Azure-helyet.](https://azure.microsoft.com/global-infrastructure/locations/)
    * **AdminUsername**: a virtuális gépre való bejelentkezéshez használt rendszergazdai fiók neve
    * **AdminPassword**: az AdminUsername beállításához a virtuális gépen beállított jelszó

4. Ahhoz, hogy a parancsfájl képes legyen beállítani a virtuális gép, be kell jelentkeznie az Azure-ba a használt Azure-előfizetéshez társított hitelesítő adatokkal.

5. A parancsfájl megerősíti a virtuális gép létrehozásához szükséges információkat. A folytatáshoz válassza az **y** vagy az **Enter** lehetőséget.

6. A parancsfájl néhány percig fut, mivel végrehajtja a következő lépéseket:

    * Az erőforráscsoport létrehozása, ha még nem létezik
    * A virtuális gép létrehozása
    * NSG-kivételek hozzáadása a virtuális géphez a 22-es (SSH), az 5671 -es (AMQP), az 5672-es (AMPQ) és a 443-as (TLS) portokhoz
    * Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)telepítése )

7. A parancsfájl kimenetele az SSH-kapcsolati karakterlánc a virtuális géphez való csatlakozáshoz. Másolja a kapcsolati karakterláncot a következő lépéshez.

    ![SSH kapcsolati karakterlánc másolása virtuális géphez](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Csatlakozás az IoT Edge-eszközhöz

A következő néhány szakasz konfigurálja az általunk létrehozott Azure virtuális gépet. Az első lépés a virtuális géphez való csatlakozás.

1. Nyisson meg egy parancssort, és illessze be a parancsfájl kimenetéről másolt SSH-kapcsolati karakterláncot. Adja meg a saját adatait a felhasználónévhez, utótaghoz és régióhoz az előző szakaszban a PowerShell-parancsfájlnak megadott értékeknek megfelelően.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Amikor a rendszer a gazdagép hitelességének ellenőrzésére kéri, írja be az **igen** parancsot, és válassza az **Enter lehetőséget.**

3. Amikor a rendszer kéri, adja meg a jelszavát.

4. Ubuntu megjelenik egy üdvözlő üzenetet, és `<username>@<machinename>:~$`akkor meg kell jelennie egy gyors, mint .

## <a name="download-key-vault-certificates"></a>A Key Vault-tanúsítványok letöltése

A cikk korábbi verzióiban tanúsítványokat töltöttünk fel a Key Vaultba, hogy elérhetővé tegyük őket az IoT Edge-eszközünkre és a levéleszközünkre. A levéleszköz egy alsóbb rétegbeli eszköz, amely az IoT Edge-eszközt használja átjáróként az IoT Hubbal való kommunikációhoz.

Mi foglalkozik a levél eszköz később a tutorial. Ebben a szakaszban töltse le a tanúsítványokat az IoT Edge-eszközre.

1. A Linux virtuális gépen lévő SSH-munkamenetből jelentkezzen be az Azure-ba az Azure CLI-vel.

    ```azurecli
    az login
    ```

1. A rendszer kérni fogja, hogy <https://microsoft.com/devicelogin> nyisson meg egy böngészőt, és adjon meg egy egyedi kódot. Ezeket a lépéseket a helyi számítógépen hajthatja végre. Zárja be a böngészőablakot, ha végzett a hitelesítésével.

1. Sikeres hitelesítés után a Linux virtuális gép bejelentkezik, és felsorolja az Azure-előfizetések.

1. Állítsa be az Azure CLI-parancsokhoz használni kívánt Azure-előfizetést.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Hozzon létre egy könyvtárat a virtuális gép a tanúsítványok.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Töltse le a key vaultban tárolt tanúsítványokat: új szél-device-full chain.cert.pem, new-edge-device.key.keym és azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Az IoT Edge-eszköz konfigurációjának frissítése

Az IoT Edge futásidejű `/etc/iotedge/config.yaml` használja a fájlt a konfiguráció megőrzéséhez. Három információt kell frissítenünk ebben a fájlban:

* **Eszközkapcsolati karakterlánc:** az eszköz identitásának kapcsolati karakterlánca az IoT Hubban
* **Tanúsítványok:** az alsóbb rétegbeli eszközökkel létesített kapcsolatokhoz használandó tanúsítványok
* **Állomásnév:** a VM IoT Edge-eszköz teljesen minősített tartományneve (FQDN).

Az *Azure IoT Edge ubuntui* lemezkép, amely az IoT Edge virtuális gép létrehozásához használt egy shell script, amely frissíti a config.yaml a kapcsolati karakterlánc.

1. A Visual Studio-kódban kattintson a jobb gombbal az IoT Edge-eszközre, majd válassza **az Eszközkapcsolati karakterlánc másolása parancsot.**

    ![Kapcsolati karakterlánc másolása a Visual Studio-kódból](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Az SSH-munkamenetben futtassa a parancsot a config.yaml fájl eszközkapcsolati karakterlánccal való frissítéséhez.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Ezután frissítjük a tanúsítványokat és a hostname-t a config.yaml közvetlen szerkesztésével.

1. Nyissa meg a config.yaml fájlt.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Frissítse a config.yaml tanúsítványszakaszát a `#` sortávolság eltávolításával és az elérési út beállításával, hogy a fájl a következő példához hasonlóan jelenjen meg:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Győződjön meg arról, hogy a **tanúsítványok:** sor nem rendelkezik előző szóközzel, és hogy a beágyazott tanúsítványok mindegyike két szóközzel van behúzva.

    A nano jobb gombbal történő kattintással a vágólap tartalmát az aktuális kurzorpozícióba illeszti be. A karakterlánc cseréjéhez a billentyűzetnyilokkal navigáljon a cserélni kívánt karakterlánchoz, törölje a karakterláncot, majd kattintson a jobb gombbal a pufferből való beillesztéshez.

3. Az Azure Portalon keresse meg a virtuális gépet. Másolja a DNS-nevet (a számítógép teljes tartományneve) az **Áttekintés** szakaszból.

4. Illessze be a teljes tartománynevet a config.yml fájl állomásnév szakaszába. Győződjön meg arról, hogy a név kisbetűs.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. A fájl mentése`Ctrl + X`és `Y` `Enter`bezárása ( , , , )

6. Indítsa újra az iotedge démont.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Ellenőrizze az IoT Edge démon állapotát (a parancs után írja be a ":q" parancsot a kilépéshez).

    ```bash
    systemctl status iotedge
    ```

8. Ha hibákat lát (színes szöveg\[\]előtaggal " HIBA ") az állapot Vizsgálja démon naplók részletes hibainformációkat.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>További lépések

Most fejeztük be egy Azure virtuális gép azure IoT Edge transzparens átjáróként való konfigurálását. Az Azure Key Vaultba feltöltött teszttanúsítványok létrehozásával kezdtük. Ezután egy parancsfájlt és egy Erőforrás-kezelő sablont használtunk a virtuális gép üzembe helyezéséhez az "Ubuntu Server 16.04 LTS + Azure IoT Edge futásidejű" lemezképpel az Azure Marketplace-ről. A virtuális gép működésbe lépve ssh-n keresztül csatlakoztunk, bejelentkeztünk az Azure-ba és letöltött tanúsítványokat a Key Vaultból. A config.yaml fájl frissítésével számos frissítést készítettünk az IoT Edge runtime konfigurációjához.

További információ: [Hogyan lehet egy IoT Edge-eszköz átjáróként használható,](iot-edge-as-gateway.md) és [konfigurálja az IoT Edge-eszköz, hogy egy átlátszó átjáróként működjön.](how-to-create-transparent-gateway.md)

Folytassa a következő cikket az IoT Edge-modulok létrehozásához.

> [!div class="nextstepaction"]
> [Egyéni IoT Edge-modulok létrehozása és üzembe helyezése](tutorial-machine-learning-edge-06-custom-modules.md)

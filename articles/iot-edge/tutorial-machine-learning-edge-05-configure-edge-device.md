---
title: Konfigurálja az IoT Edge-eszköz – Machine Learning az Azure IoT Edge |} A Microsoft Docs
description: Állítsa be egy Azure virtuális gép Linux rendszerű, Azure IoT Edge-eszköz, amely transzparens átjáróként üzemel.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a2096004a7b389f627c528a8dfb4768ac001f390
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155629"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Oktatóanyag: IoT Edge-eszköz konfigurálása

> [!NOTE]
> Ez a cikk egy sorozat része az IoT Edge-ben az Azure Machine Learning használatával kapcsolatos oktatóanyagot. Ha érkezett, ez a cikk közvetlenül, javasoljuk, hogy először a [először cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredmények.

Ez a cikk egy Azure virtuális gépet az Azure IoT Edge-eszköz, amely transzparens átjáróként lehet Linux operációs rendszert futtató konfiguráljuk. A transzparens átjáró konfigurációja lehetővé teszi, hogy az eszközök csatlakoztatása az Azure IoT hubba anélkül, hogy az átjárón keresztül, hogy az átjáró létezik. Egy időben az eszközöket az IoT Hub használata a felhasználók nem észleli a a köztes átjáróeszközt. Végső soron használjuk a transzparens átjáró peremhálózati elemzési hozzáadása a rendszer az átjáró, IoT Edge-modulok hozzáadásával.

Ez a cikk lépéseit egy felhőszolgáltatás-fejlesztői általában végzi.

## <a name="generate-certificates"></a>Tanúsítványok előállítása

Egy eszköz működéséhez, átjáróként, képesnek kell lennie, biztonságosan csatlakozhat az alsóbb rétegbeli eszközök. Az Azure IoT Edge lehetővé teszi, hogy a nyilvános kulcsokra épülő infrastruktúrájú (PKI) eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben azt engedélyezi egy alsóbb rétegbeli eszközök transzparens átjáróként működő IoT Edge-eszköz csatlakozni. Ésszerű biztonságának fenntartása érdekében az alsóbb rétegbeli eszközök kell erősítse meg az IoT Edge-eszköz identitását. IoT Edge-eszközök tanúsítványok használatával kapcsolatos további információkért lásd: [Azure IoT Edge használatának Tanúsítványadatok](iot-edge-certs.md).

Ebben a szakaszban létrehozunk, hogy Ezután létrehozhatja és futtathatja egy Docker-rendszerkép használata az önaláírt tanúsítványokat. Docker-rendszerkép használata jelentősen csökken a tanúsítványok a Windows fejlesztői gépen való létrehozásához szükséges lépéseket a lépés végrehajtásához választottuk. Lásd: [Windows-tanúsítványok létrehozása](how-to-create-transparent-gateway.md#generate-certificates-with-windows) a részletes tájékoztatás a tanúsítványok előállításához egy tároló használata nélkül. [Linux-tanúsítványok létrehozása](how-to-create-transparent-gateway.md#generate-certificates-with-linux) az utasításokat, hogy a Docker-rendszerkép az automatikus rendelkezik.

1. Jelentkezzen be a fejlesztési virtuális gépre.

2. Nyisson meg egy parancssort, és futtassa a következő parancsot egy könyvtár létrehozására a virtuális gépen.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Indítsa el **Docker for Windows** a Windows Start menüjében.

4. Nyissa meg a Visual Studio Code-ot.

5. Válassza ki **fájl** > **mappa megnyitása...**  válassza **C:\\forrás\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Kattintson a jobb gombbal a docker-fájlban, és válassza a **létrehozása lemezkép**.

7. A párbeszédpanelen fogadja el az alapértelmezett érték a rendszerkép nevét és címkéjét: **createcertificates:latest**.

8. Várjon, amíg befejeződik a build.

    > [!NOTE]
    > A hiányzó nyilvános kulccsal kapcsolatos figyelmeztetés jelenhet meg. Már biztonságosan figyelmen kívül hagyható. Hasonlóképpen látni fogja a biztonsági figyelmeztetést, amely javasolja, hogy ellenőrzés/reset a rendszerképhez, amelyet biztonsággal figyelmen kívül hagyhatja ezt a képet az engedélyek.

9. A Visual Studio Code terminálablakban futtassa a createcertificates tárolót.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker fogja kérni a hozzáférést a **c:\\**  meghajtót. Válassza ki **megoszthatja azt**.

11. Adja meg a hitelesítő adatait, amikor a rendszer kéri.

12. Egyszer a tároló végeztével fut, a következő fájlok keresése **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * C:\\edgeCertificates\\privát\\új edge device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Tanúsítványok feltöltése az Azure Key Vaultba

A tanúsítványokat tárolja biztonságos helyen, és elérhetővé teheti őket a több eszközre, az Azure Key Vault fel fogja a tanúsítványokat. Amint láthatja, hogy a fenti listából, kétféle típusú tanúsítvány van: PFX és PEM. A PFX, Key Vault Certificates a Key Vaultba feltölteni kívánt kezeljük. A PEM-fájlok egyszerű szöveges és kezeljük őket Key Vault titkos kódként. A Key Vault az Azure Machine Learning-szolgáltatás futtatásával létrehozott munkaterület társítva ezzel a [Azure notebookok](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg az Azure Machine Learning szolgáltatás munkaterületet.

2. Az Azure Machine Learning szolgáltatás munkaterület áttekintése lapon keresse meg a **Key Vault**.

    ![Másolja a kulcstároló nevét](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. A fejlesztői gépen a tanúsítványok feltöltése a Key Vaulthoz. Cserélje le **\<subscriptionId\>** és **\<keyvaultname\>** erőforrás adataival.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Ha a rendszer kéri, jelentkezzen be az Azure-bA.

5. A parancsprogram futtatása néhány percet, amely felsorolja az új Key Vault-bejegyzések kimenettel.

    ![A Key Vault parancsprogram kimenete](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>IoT Edge-eszköz létrehozása

Az Azure IoT Edge-eszköz csatlakozik az IoT hub, először létrehozzuk az identitás, az eszköz az agyban. Azt a kapcsolati karakterláncra az eszközidentitást is a felhőben, és ezzel a futtatókörnyezet konfigurálása az IoT Edge-eszközön. Ha az eszközük lett konfigurálva, és csatlakozik a hubhoz, tudjuk, hogyan helyezhet üzembe modulokat, és üzeneteket küldeni. A fizikai IoT Edge-eszköz konfigurációjának módosításával, hogy a megfelelő eszközidentitást az IoT hub konfigurációját is módosítható.

Ebben az oktatóanyagban a Visual Studio Code használatával új eszközidentitást hozunk létre. Ezeket a lépéseket használatával is elvégezhetik az [az Azure portal](how-to-register-device-portal.md), vagy [Azure CLI-vel](how-to-register-device-cli.md).

1. A fejlesztői gépén nyissa meg a Visual Studio Code-ot.

2. Nyissa meg a **Azure IoT Hub-eszközök** keretet, a Visual Studio Code Explorerben nézetből.

3. Kattintson a három pontra, majd válassza a **IoT Edge-eszköz létrehozása**.

4. Nevezze el az eszközt. Az egyszerűség kedvéért használjuk **aaTurbofanEdgeDevice** így segítségével kihasználhatja a Tesztadatok küldhet eszköz korábban létrehozott összes ügyfél előre rendezi.

5. Az új eszközt az eszközlistáról megjelennek.

    ![Új aaTurbofanEdgeDevice megtekintése a VS Code Explorerben](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Az Azure virtuális gép üzembe helyezése

Használjuk a [Ubuntu rendszeren az Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) kép az IoT Edge-eszköz létrehozásához ebben az oktatóanyagban az Azure piactérről. Az Azure IoT Edge Ubuntu-képre az indítási telepíti a legújabb Azure IoT Edge-futtatókörnyezet és annak függőségeit. A virtuális gép, egy PowerShell-parancsprogram segítségével üzembe helyezzük `Create-EdgeVM.ps1`; egy Resource Manager-sablon `IoTEdgeVMTemplate.json`; és a egy héjparancsfájlt `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Programozott üzembe helyezés engedélyezése

A rendszerkép használata egy parancsfájlokkal történő üzembe helyezéséhez a piactérről, szükségünk ahhoz, hogy a kép programozott telepítés.

1. Jelentkezzen be az Azure portálra.

1. Válassza az **Összes szolgáltatás** elemet.

1. A keresősávba írja be, és válassza ki **Marketplace**.

1. A keresősávba írja be, és válassza ki **Ubuntu rendszeren az Azure IoT Edge**.

1. Válassza ki a **történő programozott telepítését? Első lépések** hivatkozás.

1. Válassza ki a **engedélyezése** gombra, majd **mentése**.

    ![Virtuális gép programozott üzembe helyezés engedélyezése](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. A sikeres címtármódosítást jelző értesítés jelenik meg.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Ezután futtassa a szkriptet a virtuális gép számára az IoT Edge-eszköz létrehozása.

1. Nyisson meg egy PowerShell-ablakot, és keresse meg a **EdgeVM** könyvtár.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Futtassa a szkriptet a virtuális gép létrehozásához.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Amikor a rendszer kéri, adja meg az egyes paraméterek értékeit. Előfizetést erőforráscsoportot és helyet, azt javasoljuk, használja ugyanazt, hogy ez az oktatóanyag során az összes erőforrás.

    * **Az Azure előfizetés-azonosító**: az Azure Portalon található
    * **Erőforráscsoport-nevet**: könnyen megjegyezhető nevet az erőforrások csoportosítása ehhez az oktatóanyaghoz
    * **Hely**: Azure-beli hely, ahol a virtuális gép létrejön. Ha például westus2 vagy northeurope. További információkért lásd: az összes [Azure-helyen](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: a nevét, a rendszergazdai fiók használatával jelentkezzen be a virtuális gép
    * **AdminPassword**: a jelszó beállítása a AdminUsername a virtuális gépen

4. A parancsfájl a virtuális Gépet is beállíthatók jelentkezzen be az Azure-bA a hitelesítő adatokat használ az Azure-előfizetéséhez társított kell.

5. A parancsfájl megerősíti, hogy az adatokat a virtuális gép létrehozásához. Válassza ki **y** vagy **Enter** folytatásához.

6. A szkript futtatása több percig, mert a következő lépések végrehajtása:

    * Ha már nem létezik, hozza létre az erőforráscsoportot
    * A virtuális gép létrehozása
    * NSG-kivételek bővíteni a virtuális gép port 22-es (SSH), 5671-es (AMQP), 5672 (AMPQ), és a 443-as (SSL)
    * Telepítse a [az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. A parancsfájl kimenetének a csatlakozni a virtuális Géphez tartozó SSH kapcsolati karakterláncot. Másolja a kapcsolati karakterláncot a következő lépéshez.

    ![Virtuális gép SSH-kapcsolati karakterlánc másolása](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Csatlakozás az IoT Edge-eszköz

A következő néhány szakaszban állítsa be a létrehozott Azure virtuális gép. Az első lépés a virtuális gép kapcsolódni fog.

1. Nyisson meg egy parancssort, és illessze be a kimásolt a parancsfájl kimenetében SSH kapcsolati karakterláncot. Adja meg a felhasználónév, utótag és régió szerint a megadott értékeket a saját adatait a PowerShell-parancsfájlt az előző szakaszban.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Amikor a rendszer kéri, a gazdagép hitelességének ellenőrzése, írja be a **Igen** válassza **Enter**.

3. Amikor a rendszer kéri, adja meg a jelszót.

4. Ubuntu egy üdvözlő üzenetben majd, és megjelenik egy kérdés jellegű `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Letölti a tanúsítványokat a Key Vault

Ez a cikk a korábbi Key Vault az elérhetővé tételükhöz az IoT Edge-eszköz és az alsóbb rétegbeli használó eszközökön az IoT Edge-eszközt átjáróként az IoT hubbal való kommunikációhoz van levél eszköz tanúsítványok feltölti azt. A levél eszköz azt fogja foglalkozik, az oktatóanyag későbbi részében. Ebben a szakaszban töltse le a tanúsítványokat az IoT Edge-eszköz.

1. Az SSH-munkamenetből a Linux rendszerű virtuális gépen jelentkezzen be az Azure-bA az Azure CLI használatával.

    ```bash
    az login
    ```

1. Egy böngészőben nyissa meg a kéri <https://microsoft.com/devicelogin> , és adja meg egy egyedi kódot. Végrehajthatja ezeket a lépéseket a helyi gépen. Ha elkészült, zárja be a böngészőablakot hitelesítő.

1. Sikeres hitelesítést, amikor a Linux rendszerű virtuális gép jelentkezzen be, és listázása az Azure-előfizetést.

1. Az Azure parancssori felület parancsai használni kívánt Azure-előfizetés ASet.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Hozzon létre egy könyvtárat, a tanúsítványok a virtuális gépen.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Töltse le a tanúsítványokat a key vaultban tárolt: új – edge-eszközök – teljes-chain.cert.pem, új edge device.key.pem és az azure-iot-teszt – only.root.ca.cert.pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Az IoT Edge-eszköz konfigurációjának frissítése

Az IoT Edge-futtatókörnyezet használ a fájl /etc/iotedge/config.yaml megőrizni annak konfigurációját. Ez a fájl adatainak művelethez három adatra frissíteni kell:

* **Eszköz kapcsolati karakterláncának**: az eszközidentitást az IoT Hub kapcsolati sztring
* **Tanúsítványok:** a tanúsítványok, a kapcsolatok alsóbb rétegbeli eszközök használata
* **Állomásnév:** a virtuális gép IoT Edge-eszköz teljes tartománynevét (FQDN).

A *Ubuntu rendszeren az Azure IoT Edge* egy héjparancsfájlt, amely frissíti a config.yaml a kapcsolati karakterláncot tartalmaz, amely azt az IoT Edge virtuális gép létrehozásához használt lemezkép.

1. A Visual Studio Code-ban kattintson a jobb gombbal az IoT Edge-eszközön, majd válassza ki **másolás eszköz kapcsolati karakterláncának**.

    ![Másolja a kapcsolati karakterláncot a Visual Studio Code-ból](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Az SSH-munkamenetben futtassa a parancsot a config.yaml fájl frissítése az eszköz kapcsolati karakterlánccal.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Ezután frissítjük a tanúsítványok és az állomásnév a config.yaml közvetlen szerkesztésével.

1. Nyissa meg a config.yaml fájlt.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Frissítse a config.yaml tanúsítványok szakaszában a vezető eltávolításával `#` és a következő példához hasonlóan jelenik meg a fájlt a beállításnak az elérési út:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Győződjön meg arról, hogy a "tanúsítványok:" nem rendelkezik megelőző szóközöket és, hogy a tanúsítványok mindegyike előzi meg két szóközt.

    Kattintson a jobb gombbal a nano fog illessze be a jelenlegi kurzor pozíciójával a vágólap tartalmát. Cserélje le a karakterláncot, a billentyűzet nyilak használatával keresse meg a karakterláncot szeretne cserélni, törölje a karakterláncot, majd kattintson a jobb gombbal a buffer szolgáltatásból származó beilleszteni.

3. Az Azure Portalon lépjen a virtuális gép. A DNS-név (a gép teljes Tartományneve) másolja a **áttekintése** szakaszban.

4. Illessze be a teljes Tartománynevet a config.yml állomásnév szakaszában. Győződjön meg arról, hogy az összes betűjét.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Mentse és zárja be a fájlt (`Ctrl + X`, `Y`, `Enter`).

6. A iotedge démon újraindításához.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Az IoT Edge-démon állapotának ellenőrzése (a parancs után írja be a ": q" való kilépéshez).

    ```bash
    systemctl status iotedge
    ```

8. Ha hibába ütközik (színes előtaggal szöveg "\[hiba\]") a naplókban állapot vizsgálja meg démon a hibával kapcsolatos részletes információk.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>További lépések

Csak befejeződött az Azure IoT Edge-transzparens átjáró konfigurálása egy Azure virtuális Gépen. Megkezdtük hogy azt az Azure Key Vaultba feltöltött teszttanúsítványokat létrehozásával. Ezután használtuk parancsfájlt és a Resource Manager-sablon üzembe helyezéséhez a virtuális Gépet az "Ubuntu Server 16.04 LTS + az Azure IoT Edge-futtatókörnyezet" a rendszerképet az Azure marketplace-ről. A szkript az Azure CLI telepítése, a további lépés tartott ([Azure CLI telepítése az Apt használatával](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). A virtuális gép mentése és futó azt SSH-n keresztül csatlakozik, az Azure-bA regisztrált, letölti a tanúsítványokat a Key Vaultból és végrehajtott frissítéseket az IoT Edge-futtatókörnyezet konfigurációját a config.yaml fájl frissítésével. Átjáróként az IoT Edge használatával kapcsolatos további információkért lásd: [hogyan az IoT Edge-eszközt átjáróként használható](iot-edge-as-gateway.md). Az IoT Edge-eszköz konfigurálása transzparens átjáróként további információkért lásd: [a transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md).

Folytassa a következő cikk IoT Edge-modulok készítése.

> [!div class="nextstepaction"]
> [Hozhat létre és telepíthet egyéni IoT Edge-modulok](tutorial-machine-learning-edge-06-custom-modules.md)

---
title: Eszközök kiépítése többcsatornás használatra az Azure IoT Hub-eszközkiépítési szolgáltatásban
description: Eszközök kiépítése többpéldányos használatra az eszközkiépítési szolgáltatás (DPS) példányával
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e0dec0a67ed33186797ccec8066aaad89ceb8dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434738"
---
# <a name="how-to-provision-for-multitenancy"></a>A több-bérlős képzés biztosítása 

A kiépítési szolgáltatás által meghatározott felosztási házirendek számos felosztási forgatókönyvet támogatnak. Két gyakori forgatókönyv a következő:

* **Földrajzi hely / GeoLatency:** Ahogy egy eszköz mozog a helyek között, a hálózati késés javul azáltal, hogy az eszköz kiépítésű az IoT hub legközelebb az egyes helyeken. Ebben a forgatókönyvben az IoT-központok egy csoportja, amely több régióra kiterjedő, ki vannak jelölve a regisztrációk. A **legalacsonyabb késés-hozzárendelési** házirend van kiválasztva ezekhez a regisztrációkhoz. Ez a házirend hatására az eszközkiépítési szolgáltatás kiértékeli az eszköz késését, és meghatározza a szekrényben lévő IoT hub az IoT-központok csoportjából. 

* **Több-bérlős:** Az IoT-megoldásban használt eszközök előfordulhat, hogy hozzá kell rendelni egy adott IoT hub vagy Az IoT hubok csoportja. A megoldás megkövetelheti az összes eszköz egy adott bérlő kommunikálni egy adott csoportja IoT hubok. Bizonyos esetekben a bérlő iT-központok tulajdonában lehet, és az eszközök az IoT-központokhoz kell hozzárendelni.

Gyakori, hogy összekapcsolják ezt a két forgatókönyvet. Például egy több-bérlős IoT-megoldás gyakran hozzá rendeli a bérlői eszközöket az IoT-központok egy csoportja használatával, amelyek régiók között vannak elosztva. Ezek a bérlői eszközök hozzárendelhetők az IoT hubhoz abban a csoportban, amely a földrajzi hely alapján a legalacsonyabb késést tartalmazza.

Ez a cikk egy szimulált eszközmintát használ az [Azure IoT C SDK-ból,](https://github.com/Azure/azure-iot-sdk-c) hogy bemutassa, hogyan építhet nek ki eszközöket egy több-bérlős forgatókönyvben a régiók között. A cikkben a következő lépéseket hajthatja végre:

* Az Azure CLI használatával két regionális IoT-központot hozhat létre **(USA nyugati régiója** és **USA keleti régiója)**
* Több-bérlős regisztráció létrehozása
* Az Azure CLI használatával két regionális Linux-virtuális gépet hozhat létre, amelyek eszközként működnek ugyanabban a régióban **(USA nyugati régióiban** és **usa keleti régióiban**)
* Az Azure IoT C SDK fejlesztői környezetének beállítása mindkét Linux os virtuális gépen
* Szimulálja az eszközöket, hogy lássa, hogy a legközelebbi régióban ugyanahhoz a bérlőhöz vannak kiépítve.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portal rövid útmutatójával.](./quick-setup-auto-provision.md)


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Hozzon létre két regionális IoT-központot

Ebben a szakaszban az Azure Cloud Shell használatával hozhat létre két új regionális IoT-központot az **USA nyugati** és az USA **keleti régióiban** egy bérlő számára.


1. Az Azure Cloud Shell használatával hozzon létre egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

    A következő példa létrehoz egy *contoso-us-resource-group* nevű erőforráscsoportot az *eastus* régióban. Javasoljuk, hogy ezt a csoportot használja a cikkben létrehozott összes erőforráshoz. Ez megkönnyíti a tisztítást, miután befejezte.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Az Azure Cloud Shell használatával hozzon létre egy IoT-központot az **eastus** régióban az [az io hub create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal. Az IoT-központ hozzá lesz adva a *contoso-us-resource-group hoz.*

    A következő példa létrehoz egy *Contoso-east-hub* nevű IoT hubot az *eastus* helyen. A **contoso-east-hub**helyett saját egyedi hubnevet kell használnia.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    A parancs végrehajtásához néhány perc is igénybe vehet.

3. Az Azure Cloud Shell használatával hozzon létre egy IoT-központot a **Westus** régióban az [az iohub create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal. Ez az IoT-központ is hozzáadódik a contoso-us-resource-group.This IoT hub will also be added to the *contoso-us-resource-group.*

    A következő példa létrehoz egy *Contoso-west-hub* nevű IoT hubot a *Westus* helyen. A **contoso-west-hub**helyett saját egyedi hubnevet kell használnia.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs végrehajtásához néhány perc is igénybe vehet.



## <a name="create-the-multitenant-enrollment"></a>A több-bérlős regisztráció létrehozása

Ebben a szakaszban egy új regisztrációs csoportot hoz létre a bérlői eszközökszámára.  

Az egyszerűség kedvéért ez a cikk [szimmetrikus kulcsigazolást](concepts-symmetric-key-attestation.md) használ a regisztrációval. A biztonságosabb megoldás érdekében fontolja meg [az X.509 tanúsítvány tanúsítvány](concepts-security.md#x509-certificates) használatát megbízhatósági lánccal.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg az eszközkiépítési szolgáltatás példányát.

2. Válassza az **Igénylések kezelése** lapot, majd kattintson a lap tetején található **Beléptetési csoport hozzáadása** gombra. 

3. A **Beiktatási csoport hozzáadása mezőbe**írja be a következő adatokat, majd kattintson a **Mentés** gombra.

    **Csoportnév**: Adja meg **a contoso-us-devices**értéket.

    **Tanúsítvány típusa**: Válassza a **Szimmetrikus kulcs lehetőséget.**

    **Kulcsok automatikus létrehozása**: Ezt a jelölőnégyzetet már be kell pipani.

    **Válassza ki, hogyan szeretné hozzárendelni az eszközöket az elosztókhoz**: Válassza **a Legalacsonyabb késleltetés**lehetőséget .

    ![Több-bérlős regisztrációs csoport hozzáadása a szimmetrikus kulcstanúsítványhoz](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. A **Beiktatási csoport hozzáadása**elemre kattintson az Új **IoT-központ csatolása** elemre mindkét regionális központ összekapcsolására.

    **Előfizetés:** Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyhez létrehozta a regionális IoT-központokat.

    **IoT hub:** Válassza ki a létrehozott regionális csomópontok egyikét.

    **Hozzáférési házirend**: Válassza az **iothubowner lehetőséget.**

    ![A regionális IoT-központok összekapcsolása a létesítési szolgáltatással](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Miután mindkét regionális IoT-központ össze van kapcsolva, ki kell választania őket a regisztrációs csoporthoz, és kattintson a **Mentés** gombra a regisztráció regionális IoT hub-csoportjának létrehozásához.

    ![A regisztráció regionális központi csoportjának létrehozása](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. A regisztráció mentése után nyissa meg újra, és jegyezze fel az **elsődleges kulcsot**. A kulcsok létrehozásához először mentenie kell a regisztrációt. Ezzel a kulccsal lesz nek egyedi eszközkulcsok létrehozása mindkét szimulált eszköz később.


## <a name="create-regional-linux-vms"></a>Regionális Linux-virtuális gépek létrehozása

Ebben a szakaszban két regionális Linux virtuális gépet (VM) hoz létre. Ezek a virtuális gépek akarat fuss egy eszköz szimulációs mintát az egyes régiókban, hogy bemutassa az eszköz kiépítése a bérlői eszközök mindkét régióból.

A karbantartás megkönnyítése érdekében ezek a virtuális gépek ugyanabba az erőforráscsoportba kerülnek, amely a *contoso-us-resource-group ioT-hubokat*tartalmazza. A virtuális gépek azonban külön régiókban **(USA nyugati régióban** és **USA keleti régióban)** fognak futni.

1. Az Azure Cloud Shell, hajtsa végre a következő parancsot, hogy hozzon létre egy **USA-keleti** régióbeli virtuális gép, miután a következő paraméter módosításokat a parancsban:

    **--name**: Adjon meg egy egyedi nevet a **kelet-amerikai** regionális eszköz virtuális gép. 

    **--admin-username**: Használja a saját rendszergazdai felhasználónevét.

    **--admin-jelszó:** Használja a saját rendszergazdai jelszavát.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    A parancs feldolgozása eltarthat néhány percig. Miután a parancs befejeződött, jegyezze fel a **publicIpAddress** értékét az USA keleti régiójában virtuális gép.

1. Az Azure Cloud Shell, hajtsa végre a parancsot, hogy hozzon létre egy **USA-régió régió** virtuális gép, miután a következő paraméter módosításokat a parancsban:

    **--name**: Adjon meg egy egyedi nevet a **nyugat-amerikai** regionális eszköz virtuális gép. 

    **--admin-username**: Használja a saját rendszergazdai felhasználónevét.

    **--admin-jelszó:** Használja a saját rendszergazdai jelszavát.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    A parancs feldolgozása eltarthat néhány percig. Miután a parancs befejeződött, jegyezze fel a **publicIpAddress** értékét a nyugat-amerikai régió virtuális gép.

1. Nyisson meg két parancssori héjat. Csatlakozzon az egyes rendszerhéjban lévő regionális virtuális gépek egyikéhez az SSH használatával. 

    Adja át a rendszergazdai felhasználónevet, és a nyilvános IP-címet, amelyet a virtuális gép paraméterként az SSH paraméterként. Amikor a rendszer kéri, adja meg a rendszergazdai jelszót.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Az Azure IoT C SDK fejlesztői környezetének előkészítése

Ebben a szakaszban az Azure IoT C SDK minden virtuális gép klónozása lesz. Az SDK tartalmaz egy mintát, amely szimulálja a bérlő eszköz kiépítése az egyes régiókból.

1. Minden virtuális gépre telepítse a **CMake,** **g++**, **gcc**és [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) parancsokat a következő parancsokkal:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Keresse meg az SDK [legújabb kiadásának](https://github.com/Azure/azure-iot-sdk-c/releases/latest) címkenevét.

1. Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) klónozása mindkét virtuális gépen.  Használja az előző lépésben található címkét `-b` a paraméter értékeként:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Mindkét virtuális gép esetében hozzon létre egy új **cmake** mappát a tárházban, és módosítsa azt a mappát.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Mindkét virtuális gép esetén futtassa a következő parancsot, amely a fejlesztői ügyfélplatformra jellemző SDK-verziót készíti. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Egyedi eszközkulcsok származtatása

Ha szimmetrikus kulcsigazolást használ a csoportos regisztrációkkal, nem használja közvetlenül a regisztrációs csoport kulcsait. Ehelyett minden eszközhöz egyedi származtatott kulcsot hoz létre, amelyet a [szimmetrikus kulcsokkal rendelkező Csoportos regisztrációk](concepts-symmetric-key-attestation.md#group-enrollments)ban említ.

Az eszközkulcs létrehozásához használja a csoportfőkulcsot az eszköz egyedi regisztrációs azonosítójának [HMAC-SHA256-os](https://wikipedia.org/wiki/HMAC) számának kiszámításához és az eredmény Base64 formátumba történő konvertálásához.

Ne adja meg a csoportfőkulcsot az eszközkódban.

A Bash rendszerhéj-példával hozzon létre egy származtatott eszközkulcsot minden eszközhöz **az openssl**használatával.

- Cserélje le a **KEY** értékét a regisztrációhoz korábban feljegyzett **elsődleges kulcsra.**

- Cserélje le a **REG_ID** értékét a saját egyedi regisztrációs azonosítójára az egyes eszközökhöz. Mindkét azonosító definiálásához használjon kisalfanumerikus és kötőjel ('-') karaktereket.

Példa *eszközkulcs-generálásra a contoso-simdevice-east számára:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Példa eszközkulcs-generálásra *a contoso-simdevice-west számára:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


A bérlői eszközök mindegyike a származtatott eszközkulcsát és egyedi regisztrációs azonosítóját fogja használni a regisztrációs csoporttal szimmetrikus kulcsigazolás végrehajtásához a bérlői IoT-központoknak való kiépítés során.




## <a name="simulate-the-devices-from-each-region"></a>Az egyes régiókeszközeinek szimulálása


Ebben a szakaszban egy kiépítési minta az Azure IoT C SDK mindkét regionális virtuális gépek. 

A mintakód egy eszköz indítási sorozatát szimulálja, amely elküldi a létesítési kérelmet az eszközkiépítési szolgáltatás példányának. A rendszerindítási sorrend hatására az eszköz fellesz ismerve, és hozzá van rendelve az IoT hubhoz, amely a késés alapján a legközelebb van.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Nyissa **meg az\_~/azure-iot-sdk-c/provisioning\_client/samples/prov\_\_dev\_\_client\_sample/prov dev client sample.c-t** mindkét virtuális gép szerkesztéséhez.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön `hsm_type` meg arról, hogy `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` a változó az alábbi módon van beállítva, hogy megfeleljen a regisztrációs csoport tanúsítványmetódusának. 

    Mentse a módosításokat a fájlokmindkét virtuális gépen.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Mindkét virtuális gépen keresse `prov_dev_set_symmetric_key_info()` meg a **prov\_\_dev-ügyfél\_sample.c,** amely kommentálta a hívást.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Ne fűzzön megjegyzést a függvényhívásokról, és cserélje le a helyőrző értékeket (beleértve a szögletes zárójeleket is) az egyes eszközök egyedi regisztrációs azonosítóival és származtatott eszközgombjaival. Az alábbi kulcsok csak például célokat szolgálnak. Használja a korábban létrehozott kulcsokat.

    USA keleti része:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    USA nyugati:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Mentse a fájlokat.

1. Mindkét virtuális gépen keresse meg az alábbi mintamappát, és készítse el a mintát.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Miután a build sikeres, futtassa **a prov\_dev-ügyfél\_\_sample.exe futtatása** mindkét virtuális gépen a bérlői eszköz szimulálása minden régióból. Figyelje meg, hogy minden eszköz a szimulált eszköz régióihoz legközelebb eső bérlőIoT hubhoz van lefoglalva.

    Futtassa a szimulációt:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Példa kimenetre az USA keleti részének virtuális gépéről:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Példa kimenetre az USA nyugati részének virtuális gépéről:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát a cikkben létrehozott erőforrásokkal, hagyja őket. Ha nem tervezi az erőforrás további használatát, a cikk által létrehozott összes erőforrás törléséhez kövesse az alábbi lépéseket a szükségtelen költségek elkerülése érdekében.

Az itt leírt lépések feltételezik, hogy a jelen cikkben szereplő összes erőforrást a **contoso-us-resource-group**nevű erőforráscsoport utasításai nak megfelelően hozta létre.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport név szerint törlése:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. A **Név szerint szűrés...** mezőbe írja be az erőforrásokat tartalmazó erőforráscsoport nevét, **contoso-us-resource-group**. 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

- További újraépítés, lásd: [IoT Hub-eszköz újrakiépítése fogalmak](concepts-device-reprovision.md) 
- További kiépítés: A [korábban automatikusan kiépített eszközök kiirtása](how-to-unprovision-devices.md) 












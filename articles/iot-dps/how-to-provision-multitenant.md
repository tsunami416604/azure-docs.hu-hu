---
title: Eszközök kiépítése több-bérlős módhoz az Azure IoT Hub Device Provisioning Service az |} A Microsoft Docs
description: Eszközök kiépítése több-bérlős módhoz az a device provisioning service-példány
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 43989ff9dac7cdad76117aaa6e3c862453b24813
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865110"
---
# <a name="how-to-provision-for-multitenancy"></a>Hogyan helyezhet üzembe több-bérlős módhoz 

A foglalási szabályzatok a kiépítési szolgáltatás által definiált elosztási forgatókönyveket különféle támogatja. Két gyakori forgatókönyvek a következők:

* **Földrajzi hely meghatározásának / GeoLatency**: Eszköz helye között helyezi át, mert hálózati késés létesíteni az eszköz kiépítése az IoT hub és az egyes helyekre legközelebbi lett fejlesztve. Ebben a forgatókönyvben az IoT-központok span-régiók között, egy csoportot ki van jelölve regisztrációk. A **legkisebb késés** ezek regisztrációk foglalási szabályzat van kiválasztva. Ez a szabályzat eszköz késés kiértékeléséhez, és határozza meg a szekrényben tartották ki a csoport az IoT hub az IoT hub Device Provisioning Service okoz. 

* **Több-bérlős**: IoT-megoldás belül használt eszközök lesznek hozzárendelve az egy adott IoT hub és IoT-központok csoportja szükségessé. A megoldás egy adott csoportjában, IoT-központok kommunikálni egy adott bérlő összes eszköz lehet szükség. Bizonyos esetekben a bérlő saját IoT-központok és hozzá kell rendelni a saját IoT-központok eszközök megkövetelése.

Szokás úgy, hogy ezen két forgatókönyvet. Például egy több-bérlős IoT-megoldás gyakran hozzárendelése bérlői eszközök egy csoportját apró IoT-központok régióban. Bérlő ezeket az eszközöket az IoT hub benne, amely a legkisebb késéssel földrajzi helye alapján is hozzárendelhető.

Ez a cikk egy szimulált eszköz mintát használja a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) egy több-bérlős forgatókönyvben eszközök kiépítése több régió bemutatásához. Ebben a cikkben végre fogja hajtani a következő lépéseket:

* Az Azure CLI használatával hozzon létre két regionális IoT hubok (**USA nyugati RÉGIÓJA** és **USA keleti Régiójában**)
* Hozzon létre egy több-bérlős regisztráció
* Az Azure CLI használatával két regionális Linux rendszerű virtuális gép létrehozása az azonos régióban lévő eszközök segítségével (**USA nyugati RÉGIÓJA** és **USA keleti Régiójában**)
* Az Azure IoT C SDK mindkét Linuxos virtuális gépeken a fejlesztési környezet beállítása
* Az eszközökre, hogy a legközelebb eső régióban ugyanazt bérlőhöz kiépítésüket szimulálásához.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Befejezése után a [IoT Hub Device Provisioning Service beállítása az Azure Portallal](./quick-setup-auto-provision.md) rövid.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Hozzon létre két regionális IoT-központok

Ebben a szakaszban két új regionális IoT hubra létrehozása az Azure Cloud Shell fogja használni a **USA nyugati RÉGIÓJA** és **USA keleti Régiójában** régióban egy bérlő.


1. Az Azure Cloud Shell használatával hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az-group-create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

    A következő példában létrehozunk egy erőforráscsoportot, nevű *contoso-us-resource-group* a a *eastus* régióban. Javasoljuk, hogy a jelen cikkben létrehozott összes erőforrást használjon ehhez a csoporthoz. Ez megkönnyíti karbantartás befejezése után.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Az IoT hub létrehozása az Azure Cloud Shell használatával a **eastus** -régióhoz a [az iot hub létrehozása](/cli/azure/iot/hub#az-iot-hub-create) parancsot. Az IoT hub hozzáadódik a *contoso-us-resource-group*.

    A következő példában létrehozunk egy nevű IoT hubot *contoso – kelet-hub* a a *eastus* helyét. Helyett a saját egyedi hub nevet kell használnia **contoso – kelet-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    A parancs eltarthat néhány percig.

3. Az IoT hub létrehozása az Azure Cloud Shell használatával a **westus** -régióhoz a [az iot hub létrehozása](/cli/azure/iot/hub#az-iot-hub-create) parancsot. Az IoT-központ is bekerül a *contoso-us-resource-group*.

    A következő példában létrehozunk egy nevű IoT hubot *contoso-Nyugat-hub* a a *westus* helyét. Helyett a saját egyedi hub nevet kell használnia **contoso-Nyugat-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs eltarthat néhány percig.



## <a name="create-the-multitenant-enrollment"></a>A több-bérlős regisztráció létrehozása

Ebben a szakaszban létrehozhat egy új regisztrációs csoport a bérlő eszközökhöz.  

Ez a cikk az egyszerűség kedvéért használja [szimmetrikus kulcsát a kulcsigazoláshoz](concepts-symmetric-key-attestation.md) a regisztrációval. A biztonságosabb megoldás, fontolja meg [tanúsítvány X.509-igazoláshoz](concepts-security.md#x509-certificates) és a egy megbízhatósági láncot.

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com), és nyissa meg a Device Provisioning Service-példány.

2. Válassza ki a **beléptetések kezelése** fülre, majd a **regisztrációs csoport hozzáadása** gombra a lap tetején. 

3. A **regisztrációs csoport hozzáadásához**, adja meg a következőket, majd kattintson a **mentése** gombra.

    **Csoport neve**: Adja meg **contoso-us-eszközök**.

    **Tanúsítvány típusa**: Válassza ki **szimmetrikus kulcs**.

    **Kulcsok automatikus létrehozása**: A jelölőnégyzet már ellenőrizni kell.

    **Válassza ki, hogyan szeretné hozzárendelni az eszközöket hubs**: Válassza ki **legkisebb késés**.

    ![Több-bérlős regisztrációs csoportot szimmetrikus kulcsát a kulcsigazoláshoz hozzáadása](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. A **regisztrációs csoport hozzáadásához**, kattintson a **egy új IoT hub csatolása** mutató hivatkozást is a regionális központok.

    **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki az előfizetést, ahol létrehozta a regionális IoT-központok.

    **Az IoT hub**: Válassza ki a létrehozott regionális központok egyikét.

    **Hozzáférési szabályzat**: Válasszon **iothubowner**.

    ![A regionális IoT hubok az eszközkiépítési szolgáltatás csatolása](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Után mindkét regionális IoT-központok vannak kapcsolva, ki kell jelölnie azokat a regisztrációs csoport és kattintson a **mentése** a regisztrációt a regionális IoT hub csoport létrehozásához.

    ![A beléptetéshez regionális eseményközpont-csoport létrehozása](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. A regisztrációt a mentés után nyissa meg újra, és jegyezze fel a **elsődleges kulcs**. A regisztrációs kulcsai jön létre, először mentenie kell. Ezt a kulcsot később mindkét szimulált eszközök az eszköz egyedi kulcsok használható.


## <a name="create-regional-linux-vms"></a>Regionális Linux rendszerű virtuális gépek létrehozása

Ebben a szakaszban két regionális Linux rendszerű virtuális gépek (VM) hoz létre. Ezek a virtuális gépek bemutatása a bérlő eszközök mindkét régióból eszközkiépítési minden régióból fog futni egy eszköz szimulálása minta.

Győződjön meg a karbantartás egyszerűbb, ezek a virtuális gépek, a rendszer hozzáadja a ugyanazt az erőforráscsoportot, amely tartalmazza a létrehozott, IoT-központok *contoso-us-resource-group*. Azonban a virtuális gépek külön régióban fog futni (**USA nyugati RÉGIÓJA** és **USA keleti Régiójában**).

1. Az Azure Cloud Shellt, hajtsa végre a következő parancsot hozhat létre egy **USA keleti Régiójában** módosítása után a következő paraméter a parancsot a virtuális gép régió:

    **--name**: Adjon meg egy egyedi nevet a **USA keleti Régiójában** regionális eszköz virtuális Gépet. 

    **– felügyeleti-username**: A saját rendszergazdai felhasználónév használata.

    **– a rendszergazdai jelszó-**: Használja a saját rendszergazdai jelszót.

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

    Ez a parancs végrehajtásához néhány percet vesz igénybe. A parancs befejezése után jegyezze fel a **publicIpAddress** az USA keleti régiójában VM értékét.

1. Az Azure Cloud Shellt, hajtsa végre a parancsot hozhat létre egy **USA nyugati RÉGIÓJA** módosítása után a következő paraméter a parancsot a virtuális gép régió:

    **--name**: Adjon meg egy egyedi nevet a **USA nyugati RÉGIÓJA** regionális eszköz virtuális Gépet. 

    **– felügyeleti-username**: A saját rendszergazdai felhasználónév használata.

    **– a rendszergazdai jelszó-**: Használja a saját rendszergazdai jelszót.

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

    Ez a parancs végrehajtásához néhány percet vesz igénybe. A parancs befejezése után jegyezze fel a **publicIpAddress** értéket a virtuális gép USA nyugati régiója számára.

1. Nyissa meg a két parancssori ismertetése. Csatlakozzon a regionális virtuális gépek minden SSH-val rendszerhéj egyikét. 

    A rendszergazda felhasználónevét és a nyilvános IP-címet a virtuális gép ssh paraméterekként feljegyzett adja át. Adja meg a rendszergazdai jelszót, amikor a rendszer kéri.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Az Azure IoT C SDK-t fejlesztési környezet előkészítése

Ebben a szakaszban az egyes virtuális Gépeken az Azure IoT C SDK fogja klónozni. Az SDK tartalmaz egy mintát, amely minden régióból egy bérlő eszközregisztrációs szimulálni fogja.


1. Minden virtuális gép telepítése **Cmake**, **g ++**, **gcc**, és [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) a következő parancsokkal:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. Klónozás a [az Azure IoT C SDK-val](https://github.com/Azure/azure-iot-sdk-c) mindkét virtuális gépeken.

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Az adattár mérete jelenleg körülbelül 220 MB. Ez a művelet várhatóan több percig is eltarthat.

1. Mindkét virtuális gépekhez, hozzon létre egy új **cmake** belüli a tárházat és a módosítás ahhoz a mappához.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Mindkét virtuális gép futtassa az alábbi parancsot, amely létrehozza egy adott a fejlesztési fejlesztésiügyfél-platformhoz SDK verziója. 

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


## <a name="derive-unique-device-keys"></a>Származtasson eszköz egyedi kulcsok

A csoportos beléptetések szimmetrikus kulcsát a kulcsigazoláshoz használatakor a regisztrációs csoport kulcsok közvetlenül nem használja. Ehelyett, hozzon létre egy egyedi származtatott kulcs az egyes eszközök és az említett [csoportos beléptetések szimmetrikus kulcsok](concepts-symmetric-key-attestation.md#group-enrollments).

Az eszköz kulcs létrehozásához használja a csoport főkulcs számítási egy [HMAC-SHA256 algoritmust](https://wikipedia.org/wiki/HMAC) az eszköz és az eredmény Base64 formátumra alakítható az egyedi regisztrációs ID.

A csoport főkulcs nem tartalmazzák az eszköz-kódjában.

A Bash rendszerhéj példa használatával hozza létre a származtatott eszközkulcs minden egyes eszköz használatára vonatkozó **openssl**.

- Cserélje le az értéket a **kulcs** az a **elsődleges kulcs** regisztrációjához korábban feljegyzett.

- Cserélje le az értéket a **REG_ID** a saját egyedi regisztrációs azonosítóval, minden egyes eszközhöz. Alfanumerikus kisbetűt és kötőjelet ("-") karaktert a mindkét azonosítók definiálása.

Példa eszköz kulcs létrehozásának a *contoso-simdevice-keleti*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Példa eszköz kulcs létrehozásának a *contoso-simdevice-nyugati*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


A bérlő eszközök egyes segítségével saját származtatott eszköz kulcsát, és egyedi regisztrációs azonosító hajthatja végre szimmetrikus kulcsát a kulcsigazoláshoz a regisztrációs csoport a bérlőhöz IoT hubs üzembe helyezés során.




## <a name="simulate-the-devices-from-each-region"></a>Az eszközök nem minden régióban szimulálása


Ebben a szakaszban az Azure IoT C SDK-eszközkiépítési minta frissül mindkét regionális virtuális gépek esetében. 

A mintakód a kiépítési kérést küld a Device Provisioning Service-példány eszköz rendszerindítási sorrend szimulálja. A rendszerindítási sorrend miatt az eszköz elfogadandó és az IoT hub legközelebb eső alapján késés rendelve.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Nyissa meg **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_fejlesztési\_ügyfél\_minta/prov\_fejlesztési\_ügyfél\_sample.c**mindkét virtuális szerkesztésre.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg arról, hogy a `hsm_type` változó értéke `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` felel meg a regisztrációs csoport igazolási módszert az alább látható módon. 

    A fájlokat a mindkét virtuális gép módosításainak mentése.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Mindkét virtuális gép, keresse meg a hívást `prov_dev_set_symmetric_key_info()` a **prov\_fejlesztési\_ügyfél\_sample.c** amely megjegyzésként szerepel.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Állítsa vissza a függvényhívások, és cserélje le a helyőrző értékeket (beleértve a csúcsos zárójeleket) az egyedi regisztrációs azonosítók és származtatott eszköz kulcsok minden egyes eszközhöz. Az alább látható kulcsai például kizárólag célra. Használja a korábban létrehozott kulcsokat.

    USA keleti RÉGIÓJA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    USA nyugati RÉGIÓJA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Mentse a fájlokat.

1. Mindkét virtuális gépeken keresse meg az alább látható minta mappát, és hozza létre a mintát.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. A sikeres fordítás után futtassa **prov\_fejlesztési\_ügyfél\_sample.exe** minden régióból egy bérlő eszköz szimulálása mindkét virtuális gépeken. Figyelje meg, hogy minden eszközhöz hozzá van rendelve a bérlőhöz legközelebb eső régióban a szimulált eszközt az IoT hub.

    A szimuláció futtatása:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    A virtuális gépről East US kimeneti példa:

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

    A virtuális gépről West US kimeneti példa:
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

Ha azt tervezi, hogy folytatja a jelen cikkben létrehozott erőforrásokat, hagyhatja őket. Ha nem tervezi, hogy továbbra is használhassa az erőforrás, használja az alábbi lépéseket a felesleges költségek elkerülése érdekében ebben a cikkben létrehozott összes erőforrást törli.

Itt a lépések feltételezik, hogy létrehozott összes erőforrást Ez a cikk utasításai nevű ugyanabban az erőforráscsoportban lévő **contoso-us-resource-group**.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport törlése a név alapján:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. Az a **Szűrés név alapján...**  szövegmezőbe írja be az erőforrás nevének csoportba az erőforrásokat tartalmazó **contoso-us-resource-group**. 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

- További Reprovisioning kapcsolatban lásd: [IoT Hub Device reprovisoning fogalmak](concepts-device-reprovision.md) 
- Megszüntetés további tudnivalókért lásd: [hogyan eszközöket, amelyek korábban automatikus – kiépített megszüntetése](how-to-unprovision-devices.md) 












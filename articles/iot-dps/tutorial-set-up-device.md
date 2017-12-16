---
title: "Eszköz beállítása az Azure IoT Hub eszköz kiépítése szolgáltatás |} Microsoft Docs"
description: "Az eszköz gyártási folyamat során az IoT Hub eszköz kiépítése szolgáltatáson keresztül kiépítését eszköz beállítása"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 835a54f147b9ea543df21e7dfeb226ac42aceda3
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Eszköz telepítéséhez használja az Azure IoT Hub eszköz kiépítése szolgáltatás beállítása

Az előző oktatóanyag megtanulta, hogyan állíthat be az Azure IoT Hub eszköz kiépítése szolgáltatás automatikus kiépítéséhez az eszközök az IoT hub. Ez az oktatóanyag a gyártási folyamat során az eszköz beállításának útmutatást nyújt, hogy az eszköz kiépítése szolgáltatáshoz az eszköz alapján konfigurálhatja a [hardveres biztonsági modul (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security), és az eszköz az eszköz kiépítése szolgáltatáshoz kapcsolódni, az első alkalommal való indításakor. Ez az oktatóanyag ismerteti a folyamatok:

> [!div class="checklist"]
> * Válassza ki a hardveres biztonsági modul
> * A kijelölt hardveres biztonsági MODULT eszköz kiépítése ügyfél SDK építése
> * Bontsa ki a biztonsági összetevők
> * Az eszközön az eszköz kiépítése szolgáltatáshoz konfigurációjának beállítása

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt hozzon létre az eszköz kiépítése szolgáltatáshoz-példányt és az IoT-központ szerepel az oktatóanyag utasításai [állítsa be az eszközök kiépítését felhő](./tutorial-set-up-cloud.md).


## <a name="select-a-hardware-security-module"></a>Válassza ki a hardveres biztonsági modul

A [eszköz kiépítése szolgáltatáshoz ügyfél SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) támogatja a hardveres biztonsági modulok (vagy HSM-EK) két típusú: 

- [Platformmegbízhatósági modul (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module).
    - BE egy bevett a legtöbb Windows-alapú eszközplatformokat, valamint néhány Linux/Ubuntu-alapú eszközökön. Egy eszköz gyártóját, ha ezek az eszközökön futó operációs rendszer egyik sem, és keres egy bevett a HSM-EK dönthet erről a HSM. A TPM modulok csak regisztrálhat egyes eszközökre külön-külön az eszköz kiépítése szolgáltatáshoz. Fejlesztési célokra szolgál a TPM szimulátor is használhatja a Windows vagy Linux fejlesztési számítógépén.

- [X.509](https://cryptography.io/en/latest/x509/) alapú hardveres biztonsági modulok. 
    - X.509-alapú hardveres biztonsági modulok viszonylag újabb processzorok, a munkahelyi jelenleg halad a Microsofton belül zavargások vagy DARABOLHATÓ modulok, amelyek X.509-tanúsítványokat az vannak. Az X.509 modulok csoportos regisztráláshoz a portálon. Egyes nem - Windows operációs rendszer például embedOS is támogatja. Fejlesztési célra az eszköz kiépítése szolgáltatáshoz ügyfél SDK egy X.509 eszköz szimulátor támogatja. 

Egy eszköz gyártó szerint kell kiválasztania a hardveres biztonsági modulok/modulok, amely az előző típusok egyikét alapulnak. Más típusú hardveres biztonsági modulok jelenleg nem támogatottak az eszköz kiépítése szolgáltatáshoz ügyfél SDK.   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>A kijelölt hardveres biztonsági MODULT eszköz kiépítése ügyfél SDK építése

A eszköz kiépítése ügyfél SDK segítségével valósítja meg a kijelölt biztonsági mechanizmus szoftverben. A következő lépések bemutatják, hogyan használható az SDK a kiválasztott HSM lapka használata:

1. Ha követte a [gyors üzembe helyezés szimulált eszköz létrehozásához](./quick-create-simulated-device.md), akkor a telepítő készen áll az SDK létrehozására. Ha nem, kövesse az első négy lépést című részre [készítse elő a fejlesztési környezetet](./quick-create-simulated-device.md#setupdevbox). Ezeket a lépéseket a GitHub-tárház klónozása az eszköz kiépítése szolgáltatáshoz ügyfél SDK-ban, valamint telepítse az `cmake` eszköz felépítéséhez. 

1. Az SDK összeállítása típusának megfelelő HSM jelölt ki az eszközt, a parancssor használatával a következő parancsok egyikét:
    - TPM-eszközök:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - A TPM szimulátor:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Az X.509 eszközök és szimulátor:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

1. Az SDK alapértelmezett támogatást nyújt a Windows vagy Ubuntu megvalósításait TPM és X.509 hardveres biztonsági modulokat futtató eszközökön. Ezek a támogatott hardveres biztonsági modulok, a folytatáshoz című részre [bontsa ki a biztonsági összetevők](#extractsecurity) alatt. 
 
## <a name="support-custom-tpm-and-x509-devices"></a>Támogatja az egyéni TPM és X.509-eszközök

Az eszköz kiépítése rendszer ügyfél SDK-val nem alapértelmezett támogatást nyújt a TPM és X.509 eszközök, amelyeken fut a Windows vagy Ubuntu. Az ilyen eszközök kell az adott HSM lapka használata, az egyéni kód írását, ahogy az az alábbi lépéseket:

### <a name="develop-your-custom-repository"></a>Az egyéni tárház fejlesztése

1. A könyvtár eléréséhez a HSM-ből fejlesztéséhez. Ebben a projektben van szüksége a statikus könyvtárat, az eszköz kiépítése SDK-ban való felhasználására létrehozásához.
1. Szalagtár a következő fejléc fájlban meghatározott funkciók kell megvalósítania: egy. Egyéni TPM valósítja meg a definiált függvények [egyéni HSM dokumentum](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).
    b. Az egyéni X.509 valósítja meg a definiált függvények [egyéni HSM dokumentum](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

### <a name="integrate-with-the-device-provisioning-service-client"></a>Az eszköz-üzembehelyezési szolgáltatásügyfél integrálása

Miután szalagtár sikeresen létrehozta saját, helyezze át a IOT hubbal C-SDK, és szalagtár elleni hivatkozásra:

1. Adja meg az egyéni HSM GitHub-tárházban, a könyvtár elérési útja és neve a következő cmake parancsot az:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
1. A visual studióban nyissa meg az SDK-t, és állítsa be úgy. 

    - A létrehozási folyamat összeállítja az SDK-könyvtár.
    - Az SDK megkísérli szemben a cmake parancs definiált egyéni hardveres biztonsági modul hivatkozásra.

1. Futtassa a `\azure-iot-sdk-c\provisioning_client\samples\prov_dev_client_ll_sample\prov_dev_client_ll_sample.c` minta ellenőrizheti, ha a HSM-ből megfelelően történik.

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>Bontsa ki a biztonsági összetevők

A következő lépésre az eszközön a hardveres biztonsági MODULT a biztonsági összetevők kibontásához.

1. A TPM-eszköz esetén kell tudni, a **ellenőrzőkulccsal** a TPM lapka gyártótól származó társítva. Egy egyedi származtathatók **regisztrációs Azonosítót** kivonatolás az ellenőrzőkulcsot a TPM eszközhöz. 
2. X.509-eszköz szüksége lesz a eszköz(ök) - a végfelhasználói tanúsítványokban egyedi eszközbeléptetésnél során az eszközök csoport regisztrációkat főtanúsítványok kiállított tanúsítványokhoz juthat.

Ezen biztonsági összetevők kell regisztrálnia kell az eszközöket az kiépítése szolgáltatáshoz. A létesítési szolgáltatás majd vár bármely induljanak el, és csatlakozzon az újabb bármikor időben ezeket az eszközöket. Lásd: [kezelése az eszközök regisztrációját](how-to-manage-enrollments.md) kapcsolatos információk ezen biztonsági összetevők használatával történő létrehozásáról a regisztrációt. 

Amikor az eszköz az első alkalommal elindul, az ügyfél SDK kommunikál a lapka biztonsági összetevők kibontani az eszközt, és az eszközök kiépítését szolgáltatásban történő regisztráció ellenőrzi. 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Az eszközön az eszköz kiépítése szolgáltatáshoz konfigurációjának beállítása

Az eszköz gyártási folyamat utolsó lépése, hogy az alkalmazás, amely az eszköz kiépítése szolgáltatáshoz ügyfél SDK segítségével az eszközt regisztrálni kell a szolgáltatással. Ez az SDK az alábbi API felületeket biztosít az alkalmazásait:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Ne felejtse el a változókat inicializálni `uri` és `id_scope` említetteknek megfelelően a [szimulálás első rendszerindítási sorrend a a gyors üzembe helyezési eszköz szakasza](./quick-create-simulated-device.md#firstbootsequence), használatuk előtt. Az eszközök kiépítését regisztráció API `Prov_Device_LL_Create` csatlakozik a globális eszköz kiépítése szolgáltatáshoz. A *azonosító hatókör* az szolgáltatás által létrehozott, és biztosítja, hogy az egyediségi. Már nem módosítható, és a regisztráció azonosítók egyedi azonosításához használt. A `iothub_uri` lehetővé teszi, hogy az IoT Hub-regisztráció API `IoTHubClient_LL_CreateFromDeviceAuth` csatlakozhatnak a megfelelő IoT-központot. 


Ezen API-k segítségével csatlakozzon és az eszköz kiépítése szolgáltatáshoz regisztrálja a rendszerindításkor, az IoT hub kapcsolatos információkat és ezt az eszközt. A fájl `provisioning_client/samples/prov_client_ll_sample/prov_client_ll_sample.c` ezen API-k használatát ismerteti. Általában létrehozásához szükséges következő keretet biztosít az ügyfelek regisztrációjának:

```C
static const char* global_uri = "global.azure-devices-provisioning.net";
static const char* id_scope = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    prov_dev_security_init(hsm_type); // initialize your HSM 

    prov_transport = Prov_Device_HTTP_Protocol;
    
    PROV_CLIENT_LL_HANDLE handle = Prov_Device_LL_Create(global_uri, id_scope, prov_transport); // Create your provisioning client

    if (Prov_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
        // The register_callback is called when registration is complete or fails
            Prov_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    Prov_Client_LL_Destroy(handle); // Clean up the Provisioning client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

Az eszköz kiépítése szolgáltatáshoz regisztrációs ügyfélalkalmazást először egy teszt szolgáltatás a telepítőprogram használatával a szimulált eszköz használatával is szűkítéséhez. Miután az alkalmazás a tesztelési környezetben működik, összeállítani, az adott eszköz, és másolja a végrehajtható fájlt az eszköz lemezképhez. Az eszköz nem indulnak el, de kell [regisztrálja az eszközt, az eszköz kiépítése szolgáltatáshoz](./tutorial-provision-device-to-hub.md#enrolldevice) az eszköz indítása előtt. Tekintse meg a következő ismerje meg, ez a folyamat az alábbi lépéseket. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ezen a ponton előfordulhat, hogy állította be az eszközök kiépítését és az IoT-központ szolgáltatások a portálon. Ha az eszköz-üzembehelyezési telepítő abandon, és/vagy késleltetés ezen szolgáltatások bármelyikét használja, azt javasoljuk felesleges költségek megcélzásával elkerülheti ezeket leállítása.

1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Az **Összes erőforrás** panel felső részén kattintson a **Törlés** elemre.  
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Az **Összes erőforrás** panel felső részén kattintson a **Törlés** elemre.  


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Válassza ki a hardveres biztonsági modul
> * A kijelölt hardveres biztonsági MODULT eszköz kiépítése ügyfél SDK építése
> * Bontsa ki a biztonsági összetevők
> * Az eszközön az eszköz kiépítése szolgáltatáshoz konfigurációjának beállítása

A következő oktatóanyag áttekintésével megismerheti, hogyan az eszközt, hogy az IoT hub kiépítéséhez az Azure IoT Hub kiépítése szolgáltatáshoz automatikus üzembe helyezéséhez ügyfélszoftvereken továbblépés.

> [!div class="nextstepaction"]
> [Az eszközt, hogy az IoT hub kiépítése](tutorial-provision-device-to-hub.md)


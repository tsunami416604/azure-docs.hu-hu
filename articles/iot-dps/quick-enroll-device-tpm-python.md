---
title: TPM-eszköz regisztrálása az Azure Device Provisioning Service-be a Python használatával | Microsoft Docs
description: Azure rövid útmutató – TPM-eszköz regisztrálása az Azure IoT Hub Device Provisioning Service-be a Python regisztrációs szolgáltatási SDK-val
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-masebo
ms.date: 01/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d191a4b0034c50973d66ae6f0dd1bc55f0eaa31f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>TPM-eszköz regisztrálása az IoT Hub Device Provisioning Service-be a Python regisztrációs szolgáltatási SDK-val
[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Ezek a lépések bemutatják, hogyan hozhat létre egyéni regisztrációt TPM-eszközhöz programozott módon az Azure IoT Hub Device Provisioning Service-ben a [Python regisztrációs szolgáltatási SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client), valamint egy Python-mintaalkalmazás használatával. Bár a Python szolgáltatásoldali SDK Windows és Linux rendszerű gépeken is működik, ez a cikk egy Windows rendszerű fejlesztési számítógépet használ a regisztrációs folyamat bemutatására.

A folytatás előtt [végezze el az IoT Hub Device Provisioning Service beállítási lépéseit az Azure Portalon](./quick-setup-auto-provision.md).


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Töltse le és telepítse a [Python 2.x-es vagy 3.x-es verzióját](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változókhoz. 

1. Válasszon egyet az alábbi lehetőségek közül:

    - Hozza létre és fordítsa le az **Azure IoT Python SDK-t**. A Python-csomagok létrehozásához kövesse [ezeket az utasításokat](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md). Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagot](http://www.microsoft.com/download/confirmation.aspx?id=48145) is telepítse a Python natív DLL-jeinek használatához.

    - [Telepítse vagy frissítse a *pip*-et, a Python csomagkezelő rendszerét](https://pip.pypa.io/en/stable/installing/), és a telepítse a csomagot az alábbi parancs segítségével:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Szüksége lesz az eszköz ellenőrzőkulcsára. Ha a [szimulált eszköz létrehozásával és kiépítésével](quick-create-simulated-device.md) kapcsolatos rövid útmutató lépéseit követte a szimulált TPM-eszköz létrehozásához, használja az adott eszközhöz létrehozott kulcsot. Egyéb esetekben az SDK-hoz mellékelt következő ellenőrzőkulcsot is használhatja:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>A Python-mintakód módosítása

Ez a szakasz bemutatja, hogyan adhatja hozzá a TPM-eszköz kiépítési adatait a mintakódhoz. 

1. Egy szövegszerkesztővel hozzon létre egy új **TpmEnrollment.py** fájlt.

1. Adja meg a következő `import` utasításokat és változókat a **TpmEnrollment.py** fájl elején. Ezután cserélje le a `dpsConnectionString` elemet a kapcsolati karakterláncra, amely az **Azure Portal** **Device Provisioning Service** panelén, a **Megosztott elérési szabályzatok** alatt található. Cserélje le az `endorsementKey` elemet [A környezet előkészítése](quick-enroll-device-tpm-python.md#prepareenvironment) szakaszban feljegyzett értékre. Végezetül hozzon létre egy egyedi `registrationid` azonosítót, de ne feledje, hogy az kizárólag kisbetűs alfanumerikus karaktereket és kötőjeleket tartalmazhat.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Adja hozzá az alábbi függvényt és függvényhívást a csoportos regisztráció létrehozásának megvalósításához:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Mentse és zárja be a **TpmEnrollment.py** fájlt.
 

## <a name="run-the-sample-tpm-enrollment"></a>A mintául szolgáló TPM-regisztráció futtatása

1. Nyisson meg egy parancssort, és futtassa a szkriptet.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Tekintse át a sikeres regisztráció kimenetét.

1. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Kattintson a **Regisztrációk kezelése** elemre. Figyelje meg, hogy a TPM-eszköz megjelenik az **Egyedi regisztrációk** lapon a korábban létrehozott `registrationid` néven. 

    ![Sikeres TPM-regisztráció ellenőrzése a portálon](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy részletesebben is áttekinti a Java-szolgáltatásmintát, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be a Python-minta kimeneti ablakát a gépen.
1. Ha létrehozott egy szimulált TPM-eszközt, zárja be a TPM-szimulátor ablakát.
1. Lépjen az eszközkiépítési szolgáltatásra az Azure Portalon, kattintson a **Regisztrációk kezelése** lehetőségre, majd válassza az **Egyéni regisztrációk** lapot. Válassza ki a rövid útmutató segítségével létrehozott regisztrációs bejegyzés *Regisztrációs azonosítóját*, majd kattintson a panel tetején lévő **Törlés** gombra.  


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egyéni regisztrációs bejegyzést hozott létre programozott módon egy TPM-eszközhöz, és igény szerint létrehozott egy szimulált TPM-eszközt a gépén, majd kiépítette azt az IoT Hubon az Azure IoT Hub Device Provisioning Service-szel. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával.

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)

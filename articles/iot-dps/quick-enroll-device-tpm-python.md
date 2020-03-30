---
title: TPM-eszköz regisztrálása az Azure-eszközkiépítési szolgáltatásba python használatával
description: Rövid útmutató – TPM-eszköz regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatás (DPS) python-létesítési szolgáltatás SDK használatával. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: c5fe0a577ead9d8c6408d4268d21465a7b762b6d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77920620"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Rövid útmutató: TPM-eszköz igénylése az IoT Hub-eszközkiépítési szolgáltatásba a Python-létesítési szolgáltatás SDK használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Ebben a rövid útmutatóban programozott módon egyéni regisztrációt hozhat létre egy TPM-eszközhöz az Azure IoT Hub-eszközkiépítési szolgáltatásban, a Python-létesítési szolgáltatás SDK használatával egy minta Python-alkalmazás segítségével.

## <a name="prerequisites"></a>Előfeltételek

- Az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon.](./quick-setup-auto-provision.md)
- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Python 2.x vagy 3.x](https://www.python.org/downloads/). Ez a rövid útmutató az alábbi [Python-létesítési szolgáltatás SDK-t](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) telepíti.
- [Pip](https://pip.pypa.io/en/stable/installing/), ha nem tartalmazza a Python terjesztését.
- Ellenőrző kulcs. Kövesse a [szimulált eszköz létrehozása és üzembe létesítése](quick-create-simulated-device.md) című, vagy az SDK-hoz mellékelt ellenőrzőkulcsot, az alábbiakban ismertetett lépéseket.

> [!IMPORTANT]
> Ez a cikk csak az elavult V1 Python SDK vonatkozik. Az IoT Hub-eszközkiépítési szolgáltatás eszköz- és szolgáltatásügyfelei még nem érhetők el a V2-ben. A csapat jelenleg keményen dolgozik, hogy v2-funkció paritás.

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Töltse le és telepítse a [Python 2.x-es vagy 3.x-es verzióját](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változókhoz. 

1. A [Python-létesítési szolgáltatás SDK,](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client)válasszon az alábbi lehetőségek közül:

    - Hozza létre és fordítsa le az **Azure IoT Python SDK-t**. A Python-csomagok létrehozásához kövesse [ezeket az utasításokat](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md). Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagot](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) is telepítse a Python natív DLL-jeinek használatához.

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

1. Adja meg a következő `import` utasításokat és változókat a **TpmEnrollment.py** fájl elején. Ezután cserélje le a `dpsConnectionString` elemet a kapcsolati sztringre, amely az **Azure Portal****Device Provisioning Service** panelén, a **Megosztott elérési szabályzatok** alatt található. Cserélje le az `endorsementKey` elemet [A környezet előkészítése](quick-enroll-device-tpm-python.md#prepareenvironment) szakaszban feljegyzett értékre. Végezetül hozzon létre egy egyedi `registrationid` azonosítót, de ne feledje, hogy az kizárólag kisbetűs alfanumerikus karaktereket és kötőjeleket tartalmazhat.  
   
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

1. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Válassza a **Regisztrációk kezelése** lehetőséget. Figyelje meg, hogy a TPM-eszköz megjelenik az **Egyedi regisztrációk** lapon a korábban létrehozott `registrationid` néven. 

    ![Sikeres TPM-regisztráció ellenőrzése a portálon](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy vizsgálja meg a Java szolgáltatás minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be a Python-minta kimeneti ablakát a gépen.
1. Ha létrehozott egy szimulált TPM-eszközt, zárja be a TPM-szimulátor ablakát.
1. Keresse meg az Eszközkiépítési szolgáltatást az Azure Portalon, válassza a **Regisztrációk kezelése**lehetőséget, majd *Registration ID* jelölje be az **Egyéni beléptetések** lapot. **Delete**


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban programozott módon létrehozott egy egyéni regisztrációs bejegyzést egy TPM-eszközhöz, és adott esetben létrehozott egy TPM-szimulált eszközt a gépen, és kiépítette azt az IoT hubra az Azure IoT Hub-eszközlétesítési szolgáltatás használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával.

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)

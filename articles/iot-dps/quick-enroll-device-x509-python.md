---
title: X.509-eszközök regisztrációja az Azure Device Provisioning Service-be a Python használatával | Microsoft Docs
description: Azure rövid útmutató – X.509-eszközök regisztrálása az Azure IoT Hub Device Provisioning Service-be a Python regisztrációs szolgáltatási SDK-val
author: bryanla
ms.author: bryanla
ms.date: 01/25/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: c677bece27d011c5618845d950dd87e5b0e6bd06
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629337"
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>X.509-eszközök regisztrációja az IoT Hub Device Provisioning Service-be a Python regisztrációs szolgáltatási SDK-val
[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ezek a lépések egy Python-mintaalkalmazáson keresztül bemutatják, hogyan regisztrálhatja szimulált X.509-eszközök egy csoportját programozott módon az Azure IoT Hub Device Provisioning Service-be a [Python regisztrációs szolgáltatási SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) használatával. Bár a Java szolgáltatásoldali SDK Windows és Linux rendszerű gépeken is működik, ez a cikk egy Windows rendszerű fejlesztési számítógépet használ a regisztrációs folyamat bemutatására.

A folytatás előtt [végezze el az IoT Hub Device Provisioning Service beállítási lépéseit az Azure Portalon](./quick-setup-auto-provision.md).

> [!NOTE]
> A gyors üzembe helyezés csak a **Regisztrációs csoportot** támogatja. A _Python regisztrációs szolgáltatási SDK_-val végzett **egyéni regisztráció** kidolgozása folyamatban van.
> 

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Töltse le és telepítse a [Python 2.x-es vagy 3.x-es verzióját](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változókhoz. 

1. Válasszon egyet az alábbi lehetőségek közül:

    - Hozza létre és fordítsa le az **Azure IoT Python SDK-t**. A Python-csomagok létrehozásához kövesse [ezeket az utasításokat](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md). Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagot](http://www.microsoft.com/download/confirmation.aspx?id=48145) is telepítse a Python natív DLL-jeinek használatához.

    - [Telepítse vagy frissítse a *pip*-et, a Python csomagkezelő rendszerét](https://pip.pypa.io/en/stable/installing/), és a telepítse a csomagot az alábbi parancs segítségével:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Egy .pem fájlra lesz szüksége, amely egy köztes vagy fő hitelesítésszolgáltatói X.509-tanúsítványt tartalmaz, amely fel lett töltve és hitelesítve lett az eszközkiépítési szolgáltatás által. Az **Azure IoT c SDK** tartalmazza azokat az eszközöket, amelyek segítségével létrehozhat egy X.509-tanúsítványláncot, feltölthet egy fő- vagy köztes tanúsítványt a láncból, valamint végrehajthat egy tulajdonlástanúsítási eljárást a szolgáltatással a tanúsítvány hitelesítéséhez. Az eszközkészlet használatához klónozza az [Azure IoT c SDK-t](https://github.com/Azure/azure-iot-sdk-c), és kövesse az [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) fájlban foglalt lépéseket a gépen.


## <a name="modify-the-python-sample-code"></a>A Python-mintakód módosítása

Ez a szakasz bemutatja, hogyan adhatja hozzá az X.509-eszköz kiépítési adatait a mintakódhoz. 

1. Egy szövegszerkesztővel hozzon létre egy új **EnrollmentGroup.py** fájlt.

1. Adja hozzá a következő `import` utasításokat és változókat az **EnrollmentGroup.py** fájl elejéhez. Ezután cserélje le a `dpsConnectionString` elemet a kapcsolati sztringre, amely az **Azure Portal****Device Provisioning Service** panelén, a **Megosztott elérési szabályzatok** alatt található. A tanúsítvány helyőrzőjét cserélje le [A környezet előkészítése](quick-enroll-device-x509-python.md#prepareenvironment) szakaszban korábban létrehozott tanúsítványra. Végezetül hozzon létre egy egyedi `registrationid` azonosítót, de ne feledje, hogy az kizárólag kisbetűs alfanumerikus karaktereket és kötőjeleket tartalmazhat.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""

    GROUP_ID = "{registrationid}"
    ```

1. Adja hozzá az alábbi függvényt és függvényhívást a csoportos regisztráció létrehozásának megvalósításához:
   
    ```python
    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

1. Mentse és zárja be az **EnrollmentGroup.py** fájlt.
 

## <a name="run-the-sample-group-enrollment"></a>A mintául szolgáló csoportos regisztráció futtatása

1. Nyisson meg egy parancssort, és futtassa a szkriptet.

    ```cmd/sh
    python EnrollmentGroup.py
    ```

1. Tekintse át a sikeres regisztráció kimenetét.

1. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Kattintson a **Regisztrációk kezelése** elemre. Az X.509-eszközök csoportjának a **Regisztrációs csoportok** lapon kell megjelennie a korábban létrehozott `registrationid` név alatt. 

    ![Sikeres X.509-regisztráció ellenőrzése a portálon](./media/quick-enroll-device-x509-python/1.png)  


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy részletesebben is áttekinti a Java-szolgáltatásmintát, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be a Java-minta kimeneti ablakát a gépen.
1. Zárja be az _X509-tanúsítványkészítő_ ablakát a gépen.
1. Lépjen az eszközkiépítési szolgáltatásra az Azure Portalon, kattintson a **Regisztrációk kezelése** elemre, majd válassza a **Regisztrációs csoportok** lapot. Válassza ki a rövid útmutató segítségével beléptetett X.509-eszközök *CSOPORTNEVÉT*, és kattintson a panel tetején lévő **Törlés** gombra.  


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban X.509-eszközök egy szimulált csoportját regisztrálta az eszközkiépítési szolgáltatásba. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)

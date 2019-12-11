---
title: TPM-eszköz regisztrálása az Azure Device kiépítési szolgáltatásba Node. js használatával
description: Gyors útmutató – TPM-eszköz regisztrálása az Azure IoT Hub Device Provisioning Serviceba (DPS) a Node. js szolgáltatás SDK-val. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 82e78801225659a1d79829a5d26741a885931919
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974750"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Gyors útmutató: TPM-eszköz regisztrálása IoT Hub Device Provisioning Service a Node. js szolgáltatás SDK-val

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


Ezek a lépések bemutatják, hogyan hozhat létre egyéni regisztrációt TPM-eszközhöz programozott módon az Azure IoT Hub Device Provisioning Service-be a [Node.js szolgáltatási SDK](https://github.com/Azure/azure-iot-sdk-node), valamint egy Node.js-mintaalkalmazás használatával. Emellett egy szimulált TPM-eszközt is regisztrálhat a kiépítési szolgáltatásba ezzel az egyéni regisztrációs bejegyzéssel. Bár ezek a lépések Windows és Linux rendszerű gépeken is használhatóak, ehhez a cikkhez egy Windows rendszerű fejlesztői gépet fogunk használni.

## <a name="prerequisites"></a>Előfeltételek

- A folytatás előtt végezze el az [IoT Hub Device Provisioning Service az Azure Portallal való beállítását ismertető](./quick-setup-auto-provision.md) szakasz lépéseit. 
-  Győződjön meg arról, hogy a [Node.js 4.0-s vagy újabb verziója](https://nodejs.org) telepítve van a gépén.
- Ha a rövid útmutató végén szeretne regisztrálni egy szimulált eszközt, kövesse a [szimulált eszköz létrehozásával és](quick-create-simulated-device.md) kiépítésével kapcsolatos lépéseket egészen addig a lépésig, ahol az eszközhöz tartozó záradékot kap. Jegyezze fel a jóváhagyó kulcsot, amelyet később a rövid útmutatóban fog használni. **Ne kövesse az egyéni regisztráció Azure Portallal való létrehozásának lépéseit.**
 
## <a name="create-the-individual-enrollment-sample"></a>Az egyéni regisztrációs minta létrehozása 

 
1. A munkamappában egy parancsablakból futtassa a következőt:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Egy szövegszerkesztővel hozzon létre egy **create_individual_enrollment.js** fájlt a munkamappában. Adja hozzá a következő kódot a fájlhoz, és mentse:

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

## <a name="run-the-individual-enrollment-sample"></a>Az egyéni regisztrációs minta futtatása
  
1. A minta futtatásához szüksége lesz a kiépítési szolgáltatás kapcsolati sztringjére. 
    1. Jelentkezzen be a Azure Portalba, majd a bal oldali menüben kattintson a **minden erőforrás** gombra, és nyissa meg az eszköz kiépítési szolgáltatását. 
    2. Válassza a **megosztott hozzáférési házirendek**lehetőséget, majd válassza ki a tulajdonságok megnyitásához használni kívánt hozzáférési szabályzatot. A **Hozzáférési szabályzat** ablakban másolja és jegyezze fel az elsődleges kulcs kapcsolati sztringjét. 

       ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Emellett az eszköz ellenőrzőkulcsára is szüksége lesz. Ha a [szimulált eszköz létrehozásával és kiépítésével](quick-create-simulated-device.md) kapcsolatos rövid útmutató lépéseit követte a szimulált TPM-eszköz létrehozásához, használja az adott eszközhöz létrehozott kulcsot. Egyéb esetben egyéni regisztrációs minta létrehozásához használhatja a következő, az SDK-hoz mellékelt ellenőrzőkulcsot:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. A TPM-eszköz egyéni regisztrációjának létrehozásához futtassa a következő parancsot (ne hagyja ki a parancs argumentumai körüli idézőjeleket sem):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. Sikeres létrehozás esetén a parancsablak az új egyéni regisztráció tulajdonságait jeleníti meg.

    ![A regisztráció tulajdonságai a parancskimenetben](./media/quick-enroll-device-tpm-node/output.png) 

4. Ellenőrizze, hogy létrejött-e az egyéni regisztráció. Az Azure Portal Device Provisioning Service összefoglalási panelén válassza a **Regisztrációk kezelése** lehetőséget. Válassza az **Egyéni regisztrációk** fület, és válassza ki az új beléptetési bejegyzést (*első*) a záradék kulcsának és a bejegyzés egyéb tulajdonságainak ellenőrzéséhez.

    ![A regisztráció tulajdonságai a portálon](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Most, hogy létrehozta egy TPM-eszköz egyéni regisztrációját, ha szeretné elvégezni egy szimulált eszköz regisztrációját, folytathatja a [szimulált eszköz létrehozásával és kiépítésével](quick-create-simulated-device.md) kapcsolatos rövid útmutató fennmaradó lépéseivel. Ügyeljen arra, hogy a rövid útmutatóban szereplő Azure Portal segítségével egyéni regisztrációt hozzon létre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha meg szeretné vizsgálni a Node. js szolgáltatási mintákat, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be a Node.js-minta kimeneti ablakát a gépen.
1. Ha létrehozott egy szimulált TPM-eszközt, zárja be a TPM-szimulátor ablakát.
2. Navigáljon a Azure Portal eszköz kiépítési szolgáltatásához, válassza a regisztrációk **kezelése**lehetőséget, majd válassza az **Egyéni regisztrációk** fület. jelölje be az ebben a rövid útmutatóban létrehozott beléptetési bejegyzéshez tartozó *regisztrációs azonosító* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
 
## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban programozott módon létrehozott egy egyéni beléptetési bejegyzést egy TPM-eszközhöz, és opcionálisan létrehozott egy TPM-mel szimulált eszközt a gépen, és kiépítheti azt a IoT hubhoz az Azure IoT Hub Device Provisioning Service használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
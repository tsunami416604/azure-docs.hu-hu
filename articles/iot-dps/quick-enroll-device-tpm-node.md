---
title: Enroll TPM device to Azure Device Provisioning Service using Node.js
description: Quickstart - Enroll TPM device to Azure IoT Hub Device Provisioning Service using Node.js service SDK. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 890ad28d99bfc53fa8a3fb40caf0469b31aeee61
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422985"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Quickstart: Enroll TPM device to IoT Hub Device Provisioning Service using Node.js service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


Ezek a lépések bemutatják, hogyan hozhat létre egyéni regisztrációt TPM-eszközhöz programozott módon az Azure IoT Hub Device Provisioning Service-be a [Node.js szolgáltatási SDK](https://github.com/Azure/azure-iot-sdk-node), valamint egy Node.js-mintaalkalmazás használatával. Emellett egy szimulált TPM-eszközt is regisztrálhat a kiépítési szolgáltatásba ezzel az egyéni regisztrációs bejegyzéssel. Bár ezek a lépések Windows és Linux rendszerű gépeken is használhatóak, ehhez a cikkhez egy Windows rendszerű fejlesztői gépet fogunk használni.

## <a name="prerequisites"></a>Előfeltételek

- A folytatás előtt végezze el az [IoT Hub Device Provisioning Service az Azure Portallal való beállítását ismertető](./quick-setup-auto-provision.md) szakasz lépéseit. 
-  Győződjön meg arról, hogy a [Node.js 4.0-s vagy újabb verziója](https://nodejs.org) telepítve van a gépén.
- If you want to enroll a simulated device at the end of this quickstart, follow the steps in [Create and provision a simulated device](quick-create-simulated-device.md) up until the step where you get an endorsement key for the device. Note down the endorsement key, you will use it later in this quickstart. **Ne kövesse az egyéni regisztráció Azure Portallal való létrehozásának lépéseit.**
 
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
    1. Sign in to the Azure portal, select the **All resources** button on the left-hand menu and open your Device Provisioning service. 
    2. Select **Shared access policies**, then select the access policy you want to use to open its properties. A **Hozzáférési szabályzat** ablakban másolja és jegyezze fel az elsődleges kulcs kapcsolati sztringjét. 

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

4. Ellenőrizze, hogy létrejött-e az egyéni regisztráció. Az Azure Portal Device Provisioning Service összefoglalási panelén válassza a **Regisztrációk kezelése** lehetőséget. Select the **Individual Enrollments** tab and select the new enrollment entry (*first*) to verify the endorsement key and other properties for the entry.

    ![A regisztráció tulajdonságai a portálon](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Most, hogy létrehozta egy TPM-eszköz egyéni regisztrációját, ha szeretné elvégezni egy szimulált eszköz regisztrációját, folytathatja a [szimulált eszköz létrehozásával és kiépítésével](quick-create-simulated-device.md) kapcsolatos rövid útmutató fennmaradó lépéseivel. Be sure to skip the steps to create an individual enrollment using the Azure portal in that quickstart.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
If you plan to explore the Node.js service samples, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Zárja be a Node.js-minta kimeneti ablakát a gépen.
1. Ha létrehozott egy szimulált TPM-eszközt, zárja be a TPM-szimulátor ablakát.
2. Navigate to your Device Provisioning service in the Azure portal, select **Manage enrollments**, and then select the **Individual Enrollments** tab. Select the check box next to the *Registration ID* for the enrollment entry you created using this quickstart, and press the **Delete** button at the top of the pane. 
 
## <a name="next-steps"></a>Következő lépések
In this quickstart, you’ve programmatically created an individual enrollment entry for a TPM device, and, optionally, created a TPM simulated device on your machine and provisioned it to your IoT hub using the Azure IoT Hub Device Provisioning Service. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
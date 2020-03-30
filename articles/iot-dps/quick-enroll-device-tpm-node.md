---
title: TPM-eszköz regisztrálása az Azure-eszközkiépítési szolgáltatásba a Node.js használatával
description: Rövid útmutató – TPM-eszköz regisztrálása az Azure IoT Hub-eszközlétesítési szolgáltatás (DPS) a Node.js szolgáltatás SDK használatával. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: e21aaa20edf6d3a2f690bf9f77e8c9973a7b1c52
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77604930"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Rövid útmutató: TPM-eszköz igénylése az IoT Hub-eszközkiépítési szolgáltatásba a Node.js szolgáltatás SDK használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Ebben a rövid útmutatóban programozott módon egyéni regisztrációt hozhat létre egy TPM-eszközhöz az Azure IoT Hub-eszközkiépítési szolgáltatásban a Node.js Service SDK és egy minta Node.js alkalmazás használatával. Emellett egy szimulált TPM-eszközt is regisztrálhat a kiépítési szolgáltatásba ezzel az egyéni regisztrációs bejegyzéssel.

## <a name="prerequisites"></a>Előfeltételek

- Az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon.](./quick-setup-auto-provision.md)
- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Node.js v4.0+](https://nodejs.org). Ez a rövid útmutató az alábbi [Node.js service SDK-t](https://github.com/Azure/azure-iot-sdk-node) telepíti.
- Ellenőrző kulcs (nem kötelező). Kövesse a szimulált eszköz létrehozása és kiépítése című, [aknaleadott](quick-create-simulated-device.md) lépéseit. Ne hozzon létre egyéni regisztrációt az Azure Portal használatával.

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
    1. Jelentkezzen be az Azure Portalon, válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg az Eszközkiépítési szolgáltatást. 
    2. Válassza **a Megosztott hozzáférési házirendek**lehetőséget, majd válassza ki a tulajdonságok megnyitásához használni kívánt hozzáférési szabályzatot. A **Hozzáférési szabályzat** ablakban másolja és jegyezze fel az elsődleges kulcs kapcsolati sztringjét. 

       ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Emellett az eszköz ellenőrzőkulcsára is szüksége lesz. Ha a [szimulált eszköz létrehozásával és kiépítésével](quick-create-simulated-device.md) kapcsolatos rövid útmutató lépéseit követte a szimulált TPM-eszköz létrehozásához, használja az adott eszközhöz létrehozott kulcsot. Ellenkező esetben a [node.js szolgáltatás SDK-hoz](https://github.com/Azure/azure-iot-sdk-node)mellékelt következő jóváhagyási kulcsot használhatja:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. A TPM-eszköz egyéni regisztrációjának létrehozásához futtassa a következő parancsot (ne hagyja ki a parancs argumentumai körüli idézőjeleket sem):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. Sikeres létrehozás esetén a parancsablak az új egyéni regisztráció tulajdonságait jeleníti meg.

    ![A regisztráció tulajdonságai a parancskimenetben](./media/quick-enroll-device-tpm-node/output.png) 

4. Ellenőrizze, hogy létrejött-e az egyéni regisztráció. Az Azure Portal Device Provisioning Service összefoglalási panelén válassza a **Regisztrációk kezelése** lehetőséget. Válassza az **Egyéni beléptetések** lapot, és válassza ki az új beléptetési bejegyzést (*először*) az ellenőrzőkulcs és a bejegyzés egyéb tulajdonságainak ellenőrzéséhez.

    ![A regisztráció tulajdonságai a portálon](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Most, hogy létrehozta egy TPM-eszköz egyéni regisztrációját, ha szeretné elvégezni egy szimulált eszköz regisztrációját, folytathatja a [szimulált eszköz létrehozásával és kiépítésével](quick-create-simulated-device.md) kapcsolatos rövid útmutató fennmaradó lépéseivel. Ügyeljen arra, hogy hagyja ki a lépéseket, hogy hozzon létre egy egyéni regisztráció az Azure Portalon az adott rövid útmutató.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy vizsgálja meg a Node.js szolgáltatás minták, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be a Node.js-minta kimeneti ablakát a gépen.
1. Ha létrehozott egy szimulált TPM-eszközt, zárja be a TPM-szimulátor ablakát.
2. Keresse meg az Eszközkiépítési szolgáltatást az Azure Portalon, válassza a **Regisztrációk kezelése**lehetőséget, majd *Registration ID* jelölje be az **Egyéni beléptetések** lapot. **Delete** 
 
## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban programozott módon létrehozott egy egyéni regisztrációs bejegyzést egy TPM-eszközhöz, és adott esetben létrehozott egy TPM-szimulált eszközt a gépen, és kiépítette azt az IoT hubra az Azure IoT Hub-eszközlétesítési szolgáltatás használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
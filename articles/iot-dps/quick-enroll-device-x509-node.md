---
title: X.509-eszközök regisztrációja az Azure Device Provisioning Service-be a Node.js használatával | Microsoft Docs
description: Azure rövid útmutató – X.509-eszközök regisztrációja az Azure IoT Hub Device Provisioning Service-be a Node.js szolgáltatásoldali SDK-val
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 12/21/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0d255cd3a076b892f0732766e0084f78a7859aa4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>X.509-eszközök regisztrációja az IoT Hub Device Provisioning Service-be a Node.js szolgáltatásoldali SDK-val

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]


Ezek a lépések bemutatják, hogyan hozhat létre programozott módon regisztrációs csoportot egy köztes vagy fő hitelesítésszolgáltatói X.509-tanúsítványhoz a [Node.js szolgáltatásoldali SDK](https://github.com/Azure/azure-iot-sdk-node) és egy Node.js-minta használatával. Bár a lépések Windows és Linux rendszerű gépeken egyaránt alkalmazhatók, ez a cikk egy Windows rendszerű fejlesztési számítógépet használ.
 

## <a name="prerequisites"></a>Előfeltételek

- Mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. 

 
- Győződjön meg arról, hogy a [Node.js 4.0-s vagy újabb verziója](https://nodejs.org) telepítve van a gépén.


- Egy .pem fájlra lesz szüksége, amely egy köztes vagy fő hitelesítésszolgáltatói X.509-tanúsítványt tartalmaz, amely fel lett töltve és hitelesítve lett az eszközkiépítési szolgáltatás által. Az **Azure IoT c SDK** tartalmazza azokat az eszközöket, amelyek segítségével létrehozhat egy X.509-tanúsítványláncot, feltölthet egy fő- vagy köztes tanúsítványt a láncból, valamint végrehajthat egy tulajdonlástanúsítási eljárást a szolgáltatással a tanúsítvány hitelesítéséhez. Az eszközkészlet használatához klónozza az [Azure IoT c SDK-t](https://github.com/Azure/azure-iot-sdk-c), és kövesse a [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) fájlban foglalt lépéseket a gépen.

## <a name="create-the-enrollment-group-sample"></a>A regisztrációs csoport mintájának létrehozása 

 
1. A munkamappában egy parancsablakból futtassa a következőt:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Egy szövegszerkesztővel hozzon létre egy **create_enrollment_group.js** fájlt a munkamappában. Adja hozzá a következő kódot a fájlhoz, és mentse:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ````

## <a name="run-the-enrollment-group-sample"></a>A regisztrációs mintacsoport futtatása
 
1. A minta futtatásához szüksége lesz a kiépítési szolgáltatás kapcsolati karakterláncára. 
    1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson az **Összes erőforrás** gombra, és nyissa meg az eszközkiépítési szolgáltatást. 
    2. Kattintson a **Megosztott elérési szabályzatok** elemre, majd a használni kívánt hozzáférési szabályzatra a tulajdonságainak megnyitásához. A **Hozzáférési szabályzat** ablakban másolja és jegyezze fel az elsődleges kulcs kapcsolati karakterláncát. 

    ![A kiépítési szolgáltatás kapcsolati karakterláncának lekérése a portálról](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Amint azt az [Előfeltételek](#prerequisites) részben már említettük, egy .pem fájlra is szüksége lesz, amely egy köztes vagy fő hitelesítésszolgáltatói X.509-tanúsítványt tartalmaz, amely már fel lett töltve és hitelesítve lett az eszközkiépítési szolgáltatás által. Annak ellenőrzéséhez, hogy a tanúsítvány már fel lett töltve és hitelesítve lett, az Azure Portalon a Device Provisioning Service összefoglaló panelén kattintson a **Tanúsítványok** gombra. Keresse meg a csoportos regisztrációhoz használni kívánt tanúsítványt, és győződjön meg arról, hogy az állapotának értéke *Hitelesítve*.

    ![Hitelesített tanúsítvány a portálon](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Egy regisztrációs csoport a tanúsítványhoz való létrehozásához futtassa az alábbi parancsot (a parancs argumentumai körül lévő idézőjeleket is írja be):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Sikeres létrehozás esetén a parancsablakban az új regisztrációs csoport tulajdonságai jelennek meg.

    ![A regisztráció tulajdonságai a parancskimenetben](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Ellenőrizze, hogy a regisztrációs csoport létrejött-e. Az Azure Portal Device Provisioning Service összefoglalási panelén válassza a **Regisztrációk kezelése** lehetőséget. Válassza a **Regisztrációs csoportok** lapot, és ellenőrizze, hogy az új regisztrációs bejegyzés (*első*) megjelenik-e.

    ![A regisztráció tulajdonságai a portálon](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy részletesebben is áttekinti a Node.js-szolgáltatásmintákat, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes Azure-erőforrást.
 
1. Zárja be a Node.js-minta kimeneti ablakát a gépen.
2. Lépjen az eszközkiépítési szolgáltatásra az Azure Portalon, kattintson a **Regisztrációk kezelése** elemre, majd válassza a **Regisztrációs csoportok** lapot. Válassza ki a rövid útmutató segítségével létrehozott regisztrációs bejegyzés *Regisztrációs azonosítóját*, majd kattintson a panel tetején lévő **Törlés** gombra.  
3. Az Azure Portalon az eszközkiépítési szolgáltatásban kattintson a **Tanúsítványok** lehetőségre, majd a rövid útmutató céljaira feltöltött tanúsítványra, és végül a **Törlés** gombra a **Tanúsítvány részletei** ablak tetején.  
 
## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egy csoportos regisztrációt hozott létre egy köztes vagy fő hitelesítésszolgáltatói X.509-tanúsítvány számára az Azure IoT Hub Device Provisioning Service használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
---
title: Azure eszköz kiépítése szolgáltatáshoz SDK-k használatával eszközbeléptetésnél kezelése |} Microsoft Docs
description: Az IoT Hub eszköz kiépítése szolgáltatás a szolgáltatás SDK-k használatával eszközbeléptetésnél kezelése
author: yzhong94
ms.author: yizhon
ms.date: 04/04/18
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: 9a68d928c70e1e233f6de7df13441a1f688f456a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629847"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Eszköz regisztrációját az Azure eszköz kiépítése szolgáltatáshoz SDK-k kezelése
A *eszközregisztráció* létrehoz egy rekordot egyetlen eszközt vagy eszközöket, amelyek bármikor előfordulhat, hogy regisztrálja az eszköz kiépítése szolgáltatáshoz csoportja. A beléptetési rekord tartalmazza a kezdeti kívánt beállításait, hogy a regisztráció, beleértve a kívánt IoT-központ részeként őket. Ez a cikk bemutatja, hogyan kezelheti az eszközök regisztrációját a létesítési szolgáltatás programozott módon, az Azure IoT kiépítése szolgáltatáshoz SDK-k.  Az SDK-k az adott adattár, Azure IoT SDK a githubon érhetők el.

## <a name="prerequisites"></a>Előfeltételek
* Az eszköz kiépítése szolgáltatáspéldány szerzi be a kapcsolati karakterláncot.
* Az eszköz biztonsági összetevők az beszerzése a [állapotigazolási mechanizmus](concepts-security.md#attestation-mechanism) használja:
    * [**Platformmegbízhatósági modul (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Az egyes beléptetési: regisztrációs Azonosítót és a TPM ellenőrzőkulccsal fizikai eszközről, vagy a TPM szimulátor.
        * Beléptetési csoport nem vonatkozik a TPM-igazolás.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Az egyes beléptetési: A [levéltanúsítvány](/azure/iot-dps/concepts-security#leaf-certificate) fizikai eszközről, vagy az SDK-ból [DARABOLHATÓ](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulátor.
        * Beléptetési csoport: A [/legfelső szintű tanúsítvány](/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány](/azure/iot-dps/concepts-security#intermediate-certificate), fizikai eszközön eszköz tanúsítvány létrehozásához használt.  Azt is tudna generálni az SDK DARABOLHATÓ emulátorától.
* Lehet, hogy pontos API-hívások különböző nyelvi különbségek miatt. Tekintse át a minták a Githubon előírt részletek:
   * [Java-kiépítés szolgáltatás ügyfél-minták](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [NODE.js szolgáltatás ügyfél kiépítési minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET-kiépítés szolgáltatás ügyfél-minták](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Hozzon létre egy eszközök beléptetése
Az üzembe helyezési szolgáltatással az eszközök regisztrálása két módja van:

* Egy **beléptetési csoport** , amelyek egy közös igazolás mechanizmus X.509-tanúsítványokat, írja alá az egy eszközcsoportra bejegyzés a [legfelső szintű tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány ](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Azt javasoljuk, egy beléptetési csoport az eszközök, amelyek egy kezdeti szükségeskonfiguráció számos vagy eszközök valamennyi amelyet ugyanannak a bérlőnek. Vegye figyelembe, hogy csak regisztrálhatja az eszközöket, amelyek használják az X.509 tanúsítvány mechanizmust *beléptetési csoportok*. 

    Létrehozhat egy beléptetési csoport az SDK-k a munkafolyamat a következő:

    1. Beléptetési csoport az igazolás mechanizmus X.509 legfelső szintű tanúsítványt használ.  Hívja Service SDK API ```X509Attestation.createFromRootCertificate``` főtanúsítvánnyal beléptetési tanúsítvány létrehozásához.  X.509 legfelső szintű tanúsítvány vagy a PEM-fájl vagy karakterláncként megadva.
    1. Hozzon létre egy új ```EnrollmentGroup``` változó segítségével a ```attestation``` létrehozott, valamint egy egyedi ```enrollmentGroupId```.  Másik lehetőségként beállíthatja a paraméterek, például a ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Hívja Service SDK API ```createOrUpdateEnrollmentGroup``` a háttér alkalmazásában ```EnrollmentGroup``` beléptetési csoport létrehozásához.

* Egy **egyes beléptetési** regisztrálni lehet, hogy egyetlen eszköz bejegyzés. Egyes regisztrációkat használhatja X.509-tanúsítványokat vagy (a fizikai vagy virtuális TPM) SAS-tokenje igazolás mechanizmusokat. Azt javasoljuk, egyes regisztrációkat külön kezdeti konfigurációt igénylő eszközök, vagy eszközök, amelyek is csak TPM vagy virtuális TPM SAS-tokenje igazoló mechanizmusként. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    Az egyes tagság hozhatja létre az SDK-k a munkafolyamat a következő:
    
    1. Válassza ki a ```attestation``` mechanizmus, amely a TPM vagy X.509.
        1. **TPM**: szolgáltatás SDK API meghívása az ellenőrzőkulccsal fizikai eszközről, vagy a TPM-szimulátor használatával a bemeneti adatként, ```TpmAttestation``` beléptetési tanúsítvány létrehozásához. 
        2. **X.509**: az ügyféltanúsítvány használatával a bemeneti adatként, hívása szolgáltatás SDK API-JÁNAK ```X509Attestation.createFromClientCertificate``` beléptetési tanúsítvány létrehozásához.
    2. Hozzon létre egy új ```IndividualEnrollment``` változó használatával a ```attestation``` létrehozott, valamint egy egyedi ```registrationId``` bemenetként, ez az eszköz vagy a TPM szimulátor generált.  Másik lehetőségként beállíthatja a paraméterek, például a ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Hívja Service SDK API ```createOrUpdateIndividualEnrollment``` a háttér alkalmazásában ```IndividualEnrollment``` az egyes tagság létrehozásához.

Miután sikeresen létrehozott egy beléptetési, az az eszköz kiépítése szolgáltatáshoz egy beléptetési eredményt adja vissza. Ez a munkafolyamat mutatják be a minták [azt korábban említettük](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Regisztrációs bejegyzés frissítése

Miután létrehozta a regisztrációs bejegyzés, érdemes lehet frissíti a regisztrációját.  A lehetséges forgatókönyvek például a kívánt tulajdonság, a tanúsítvány metódus frissítése vagy eszközök hozzáférésének visszavonása.  Nincsenek egyedi beléptetéshez és a csoport beléptetési különböző API-k, de nem kell megkülönböztetni igazolási módszert.

Ez a munkafolyamat következő regisztrációs bejegyzést is frissítheti:
* **Egyéni regisztráció**:
    1. Beszerezni a legújabb regisztráció a létesítési szolgáltatás első szolgáltatás SDK API-val ```getIndividualEnrollment```.
    2. A paraméter a legújabb beléptetési szükség szerint módosítható. 
    3. A legújabb regisztrációját használó hívja Service SDK API ```createOrUpdateIndividualEnrollment``` a regisztrációs bejegyzés frissítése.
* **Csoport beléptetési**:
    1. Beszerezni a legújabb regisztráció a létesítési szolgáltatás első szolgáltatás SDK API-val ```getEnrollmentGroup```.
    2. A paraméter a legújabb beléptetési szükség szerint módosítható.
    3. A legújabb regisztrációját használó hívja Service SDK API ```createOrUpdateEnrollmentGroup``` a regisztrációs bejegyzés frissítése.

Ez a munkafolyamat mutatják be a minták [azt korábban említettük](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Távolítsa el a regisztrációs bejegyzés

* **Az egyes beléptetési** törölheti az hívja Service SDK API-t ```deleteIndividualEnrollment``` használatával ```registrationId```.
* **Csoport beléptetési** törölheti az hívja Service SDK API-t ```deleteEnrollmentGroup``` használatával ```enrollmentGroupId```.

Ez a munkafolyamat mutatják be a minták [azt korábban említettük](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Az egyes regisztrációkat tömeges művelet

Tömeges művelet létrehozására, frissítésére, és távolítsa el a munkafolyamat következő több egyedi regisztrációkat végezheti el:

1. Hozzon létre egy változót több tartalmazó ```IndividualEnrollment```.  Ez a változó végrehajtásának eltér minden nyelvhez.  Tekintse át a tömeges művelet mintát a Githubon részletei.
2. Hívja Service SDK API ```runBulkOperation``` rendelkező egy ```BulkOperationMode``` a kért műveletet, és egyes regisztrációkat a változót. Négy módok támogatottak: létrehozásához, frissítéséhez updateIfMatchEtag, és törléséhez.

Miután sikeresen elvégezte egy műveletet, az eszköz kiépítése szolgáltatáshoz meghaladná a tömeges művelet eredményeként.

Ez a munkafolyamat mutatják be a minták [azt korábban említettük](#prerequisites).

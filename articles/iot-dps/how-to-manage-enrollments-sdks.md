---
title: "Az Azure eszköz kiépítése szolgáltatáshoz SDK-k eszközbeléptetésnél kezelése |} Microsoft Docs"
description: "Az IoT Hub eszköz kiépítése szolgáltatás a szolgáltatás SDK-k a eszközbeléptetésnél kezelése"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 12/01/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: arjmands
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a3d763009c7a7f45ddce96732977a79567f7ef44
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Eszköz regisztrációját az Azure eszköz kiépítése szolgáltatáshoz SDK-k kezelése
A *eszközregisztráció* létrehoz egy rekordot egyetlen eszközt vagy eszközöket, amelyek bármikor előfordulhat, hogy regisztrálja az eszköz kiépítése szolgáltatáshoz csoportja. A beléptetési rekord tartalmazza a kezdeti kívánt beállításait, hogy a regisztráció, beleértve a kívánt IoT-központ részeként őket. Ez a cikk bemutatja, hogyan kezelheti az eszközök regisztrációját a létesítési szolgáltatás programozott módon, az Azure IoT kiépítése szolgáltatáshoz SDK-k.  Az SDK-k az adott adattár, Azure IoT SDK a githubon érhetők el.

## <a name="samples"></a>Példák
Ez a cikk ellenőrzi, hogy a magas szintű fogalmak eszközbeléptetésnél a létesítési szolgáltatás programozott módon, az Azure IoT kiépítése szolgáltatáshoz SDK-k kezelése.  Lehet, hogy pontos API-hívások különböző nyelvi különbségek miatt.  Tekintse át a minták a Githubon részletes nyújtunk:
* [Java-kiépítés szolgáltatás ügyfél-minták](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
* [NODE.js szolgáltatás ügyfél kiépítési minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)

## <a name="prerequisites"></a>Előfeltételek
* Egy eszköz kiépítése szolgáltatáspéldány származó kapcsolati karakterlánc
* Eszköz biztonsági összetevők:
    * [**TPM**](https://docs.microsoft.com/azure/iot-dps/concepts-security):
        * Az egyes beléptetési: regisztrációs Azonosítót és a TPM ellenőrzőkulccsal fizikai eszközről, vagy a TPM szimulátor.
        * Beléptetési csoport nem vonatkozik a TPM-igazolás.
    * [**X.509**](https://docs.microsoft.com/azure/iot-dps/concepts-security):
        * Az egyes beléptetési: A [levéltanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#leaf-certificate) fizikai eszköz vagy az emulátor feldarabolására HASZNÁLNAK.
        * Beléptetési csoportnak: A [legfelső szintű tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), fizikai eszközön eszköz tanúsítvány létrehozásához használt.  Azt is létrehozhatók, DARABOLHATÓ emulátorától.

## <a name="create-a-device-enrollment"></a>Hozzon létre egy eszközök beléptetése

Az üzembe helyezési szolgáltatással az eszközök regisztrálása két módja van:

* Egy **beléptetési csoport** , amelyek egy közös igazolás mechanizmus X.509-tanúsítványokat, írja alá az egy eszközcsoportra bejegyzés a [legfelső szintű tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány ](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Azt javasoljuk, egy beléptetési csoport az eszközök, amelyek kívánt kezdeti konfigurációja számos vagy eszközök valamennyi amelyet ugyanannak a bérlőnek. Vegye figyelembe, hogy csak regisztrálhatja az eszközöket, amelyek használják az X.509 tanúsítvány mechanizmust *beléptetési csoportok*. 

    Létrehozhat egy beléptetési csoport az SDK-k a munkafolyamat a következő:

    1. Beléptetési csoport az igazolás mechanizmus X.509 legfelső szintű tanúsítványt használ.  Hívja Service SDK API ```X509Attestation.createFromRootCertificate``` főtanúsítvánnyal beléptetési tanúsítvány létrehozásához.  X.509 legfelső szintű tanúsítvány vagy a PEM-fájl vagy karakterláncként megadva.
    1. Hozzon létre egy új ```EnrollmentGroup``` változó segítségével a ```attestation``` létrehozott, valamint egy egyedi ```enrollmentGroupId```.  Másik lehetőségként beállíthatja a paraméterek, például a ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Hívja Service SDK API ```createOrUpdateEnrollmentGroup``` a háttér alkalmazásában ```EnrollmentGroup``` beléptetési csoport létrehozásához.

* Egy **egyes beléptetési** regisztrálni lehet, hogy egyetlen eszköz bejegyzés. Egyes regisztrációkat használhatja X.509-tanúsítványokat vagy a SAS-tokenje (a valós vagy virtuális TPM) igazolás mechanizmusokat. Azt javasoljuk, egyes regisztrációkat az eszközök, amelyek külön kezdeti konfigurációt igényelnek, vagy az eszközök, amelyek is csak TPM vagy virtuális TPM SAS-tokenje igazoló mechanizmusként. Előfordulhat, hogy az egyes regisztrációkat a kívánt IoT hub eszköz-azonosító van megadva.

    Az egyes tagság hozhatja létre az SDK-k a munkafolyamat a következő:
    
    1. Válassza ki a ```attestation``` mechanizmus, amely a TPM vagy X.509.
        1. **TPM**: szolgáltatás SDK API meghívása az ellenőrzőkulccsal fizikai eszközről, vagy a TPM-szimulátor használatával a bemeneti adatként, ```TpmAttestation``` beléptetési tanúsítvány létrehozásához. 
        2. **X.509**: az ügyféltanúsítvány használatával a bemeneti adatként, hívása szolgáltatás SDK API-JÁNAK ```X509Attestation.createFromClientCertificate``` beléptetési tanúsítvány létrehozásához.
    2. Hozzon létre egy új ```IndividualEnrollment``` változó használatával a ```attestation``` létrehozott, valamint egy egyedi ```registrationId``` bemenetként, ez az eszköz vagy a TPM szimulátor generált.  Másik lehetőségként beállíthatja a paraméterek, például a ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Hívja Service SDK API ```createOrUpdateIndividualEnrollment``` a háttér alkalmazásában ```IndividualEnrollment``` az egyes tagság létrehozásához.

Miután sikeresen létrehozott egy beléptetési, az eszköz kiépítése szolgáltatáshoz egy beléptetési eredmény alakítanák vissza.

Ez a munkafolyamat mutatják be a [minták](#samples).

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

Ez a munkafolyamat mutatják be a [minták](#samples).

## <a name="remove-an-enrollment-entry"></a>Távolítsa el a regisztrációs bejegyzés

* **Az egyes beléptetési** törölheti az hívja Service SDK API-t ```deleteIndividualEnrollment``` használatával ```registrationId```.
* **Csoport beléptetési** törölheti az hívja Service SDK API-t ```deleteEnrollmentGroup``` használatával ```enrollmentGroupId```.

Ez a munkafolyamat mutatják be a [minták](#samples).

## <a name="bulk-operation-on-individual-enrollments"></a>Az egyes regisztrációkat tömeges művelet

Tömeges művelet létrehozására, frissítésére, és távolítsa el a munkafolyamat következő több egyedi regisztrációkat végezheti el:

1. Hozzon létre egy változót több tartalmazó ```IndividualEnrollment```.  Ez a változó végrehajtásának eltér minden nyelvhez.  Tekintse át a tömeges művelet mintát a Githubon részletei.
2. Hívja Service SDK API ```runBulkOperation``` rendelkező egy ```BulkOperationMode``` a kért műveletet, és egyes regisztrációkat a változót. Négy módok támogatottak: létrehozásához, frissítéséhez updateIfMatchEtag, és törléséhez.

Miután sikeresen elvégezte egy műveletet, az eszköz kiépítése szolgáltatáshoz meghaladná a tömeges művelet eredményeként.

Ez a munkafolyamat mutatják be a [minták](#samples).

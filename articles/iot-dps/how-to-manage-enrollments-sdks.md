---
title: Azure Device Provisioning szolgáltatás SDK-k használatával eszközök beléptetésének kezelése |} A Microsoft Docs
description: Az IoT Hub Device Provisioning Service szolgáltatás SDK-k használatával az eszközök beléptetésének kezelése
author: yzhong94
ms.author: yizhon
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: c73a40e46d86632732454ae16ea4f83e3ffa0281
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674752"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Hogyan eszközök beléptetésének kezelése az Azure Device Provisioning szolgáltatás SDK-k
A *eszközregisztráció* létrehoz egy rekordot egy adott eszköz vagy egy csoportot az eszközről, bármikor előfordulhat, hogy regisztrálja a Device Provisioning Service szolgáltatással. A regisztrációs rekord az regisztrálása, beleértve a kívánt IoT-központ részeként (ök) höz kezdeti kívánt beállításait tartalmazza. Ez a cikk bemutatja, hogyan kezelheti az eszközök regisztrációját a kiépítési szolgáltatás programozott módon, az Azure IoT Provisioning Service SDK-k segítségével.  Az SDK a Githubon, az Azure IoT SDK-k ugyanabban az adattárban érhetők el.

## <a name="prerequisites"></a>Előfeltételek
* A kapcsolati karakterlánc lekérését a Device Provisioning Service-példány.
* Szerezze be az eszköz biztonsági összetevőinek a [igazolási mechanizmus](concepts-security.md#attestation-mechanism) használt:
    * [**Platformmegbízhatósági modul (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Egyéni regisztráció: Regisztrációs azonosító és a TPM Ellenőrzőkulcsát egy fizikai eszközt vagy TPM-szimulátort.
        * Regisztrációs csoportot nem vonatkozik a TPM-igazolás.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Egyéni regisztráció: A [levéltanúsítványt](/azure/iot-dps/concepts-security) fizikai eszközt vagy az SDK [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulátort.
        * Regisztrációs csoportot: A [/legfelső szintű tanúsítvány](/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítványt](/azure/iot-dps/concepts-security#intermediate-certificate)szolgál egy fizikai eszköz az eszköz tanúsítvány előállításához.  Is létrehozható, az SDK DICE emulátort.
* Pontos API-hívások nyelvi különbségek miatt eltérő lehet. Tekintse át a mintákat a Githubon előírt részletei:
   * [Java Provisioning Service ügyfél-minták](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [NODE.js Provisioning Service ügyfél-minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET provisioning Service ügyfél-minták](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Hozzon létre egy eszközregisztrációs
Regisztrálhatja az eszközöket a kiépítési szolgáltatás két módja van:

* Egy **regisztrációs csoportot** , amelyek egy közös igazolási mechanizmus által aláírt X.509-tanúsítványok az eszközök egy csoportját egy bejegyzés az [főtanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítványt ](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Ajánlott regisztrációs csoportot használni kívánt kezdeti konfigurációval rendelkező eszközök nagy számú, vagy eszközök célzó ugyanazt bérlőhöz. Vegye figyelembe, hogy csak regisztrálhat eszközöket, amelyek az X.509-igazolási mechanizmus, *regisztrációs csoportok*. 

    A következő munkafolyamat SDK-k egy regisztrációs csoportot hozhat létre:

    1. Regisztrációs csoportnak az igazolási mechanizmust használ az X.509-tanúsítvány legfelső szintű.  Szolgáltatás SDK API-t ```X509Attestation.createFromRootCertificate``` hozhat létre a regisztrációs tanúsítvány legfelső szintű tanúsítvánnyal.  X.509 legfelső szintű tanúsítvány vagy egy PEM-fájlt vagy egy karakterláncként megadva.
    1. Hozzon létre egy új ```EnrollmentGroup``` változó használatával a ```attestation``` létrehozott és a egy egyedi ```enrollmentGroupId```.  Igény szerint beállíthatja a paraméterek ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Szolgáltatás SDK API-t ```createOrUpdateEnrollmentGroup``` a háttérrendszer alkalmazásában ```EnrollmentGroup``` egy regisztrációs csoport létrehozásához.

* Egy **egyéni regisztráció** regisztrálható egyetlen eszközhöz tartozó bejegyzés. Egyéni regisztrációk felhasználhatja X.509-tanúsítványokat vagy SAS-tokeneket (az egy fizikai vagy virtuális TPM-ben) igazolási mechanizmusként. Azt javasoljuk, hogy egyéni regisztrációk használatát, eszközök, az egyedi kezdeti konfigurációt igénylő vagy az eszközök, amelyek a TPM-eszköz vagy virtuális TPM-n keresztül SAS-tokeneket csak használhatja az igazolási mechanizmusként. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    A következő munkafolyamat SDK-kal hozhat létre egyéni regisztrációt:
    
    1. Válassza ki a ```attestation``` mechanizmust, amely a TPM vagy X.509 lehet.
        1. **A TPM**: Szolgáltatás SDK API meghívása az ellenőrzőkulcs fizikai eszközt vagy TPM-szimulátort használja bemeneti, ```TpmAttestation``` regisztrációs tanúsítvány létrehozásához. 
        2. **X.509**: Szolgáltatás SDK API meghívása az ügyféltanúsítványt használja a bemeneteként, ```X509Attestation.createFromClientCertificate``` regisztrációs tanúsítvány létrehozásához.
    2. Hozzon létre egy új ```IndividualEnrollment``` változó használatával a ```attestation``` létrehozott és a egy egyedi ```registrationId``` bemenetként, az eszközön, vagy a TPM-szimulátor generált.  Igény szerint beállíthatja a paraméterek ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Szolgáltatás SDK API-t ```createOrUpdateIndividualEnrollment``` a háttérrendszer alkalmazásában ```IndividualEnrollment``` hozhat létre egyéni regisztrációt.

Miután sikeresen létrehozott egy beléptetési, a Device Provisioning Service-regisztrációs eredményt adja vissza. Ez a munkafolyamat mutatják be a minták [azt korábban említettük](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Regisztrációs bejegyzés frissítése

Regisztrációs bejegyzés létrehozása után érdemes frissíteni a regisztrációt.  A lehetséges forgatókönyvek között megtalálható, a kívánt tulajdonság frissítése, a igazolási módszert frissítése, vagy visszavonja az eszköz elérését.  Számos különböző API-k egyéni regisztráció és a csoportos regisztrációhoz, de nem tesz különbséget az igazolási mechanizmust.

Ez a munkafolyamat a következő regisztrációs bejegyzés is frissítheti:
* **Egyéni regisztráció**:
    1. Le a legújabb regisztrációt a kiépítési szolgáltatás első szolgáltatás SDK API-val ```getIndividualEnrollment```.
    2. A paraméter a legújabb beléptetési szükség szerint módosítsa. 
    3. Szolgáltatás SDK API használatával a legfrissebb regisztrációs hívás ```createOrUpdateIndividualEnrollment``` a regisztrációs bejegyzés frissítése.
* **Csoportos regisztráció**:
    1. Le a legújabb regisztrációt a kiépítési szolgáltatás első szolgáltatás SDK API-val ```getEnrollmentGroup```.
    2. A paraméter a legújabb beléptetési szükség szerint módosítsa.
    3. Szolgáltatás SDK API használatával a legfrissebb regisztrációs hívás ```createOrUpdateEnrollmentGroup``` a regisztrációs bejegyzés frissítése.

Ez a munkafolyamat mutatják be a minták [azt korábban említettük](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Regisztrációs bejegyzés eltávolítása

* **Egyéni regisztráció** szolgáltatás SDK API-JÁNAK meghívásával törölheti ```deleteIndividualEnrollment``` használatával ```registrationId```.
* **Csoportos regisztráció** szolgáltatás SDK API-JÁNAK meghívásával törölheti ```deleteEnrollmentGroup``` használatával ```enrollmentGroupId```.

Ez a munkafolyamat mutatják be a minták [azt korábban említettük](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Az egyéni regisztrációk tömeges művelet

Műveletcsoport létrehozása, frissítése vagy távolítsa el a következő munkafolyamat több egyéni regisztrációk végezheti el:

1. Hozzon létre egy változót, amely tartalmazza a több ```IndividualEnrollment```.  Ez a változó megvalósítását eltér minden nyelvhez.  Tekintse át a tömeges művelet minta a Githubon részleteit.
2. Szolgáltatás SDK API-t ```runBulkOperation``` együtt egy ```BulkOperationMode``` a kívánt műveletet és az egyéni regisztrációk változót. Négy módok használata támogatott:, updateIfMatchEtag, módosítása és törlése.

Miután sikeresen elvégezte a műveletet, a Device Provisioning Service termékazonosítóhoz egy kötegelt művelet eredménye.

Ez a munkafolyamat mutatják be a minták [azt korábban említettük](#prerequisites).

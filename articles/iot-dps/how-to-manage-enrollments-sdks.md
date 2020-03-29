---
title: Eszközregisztrációk kezelése az Azure DPS SDK-k használatával
description: Eszközregisztrációk kezelése az IoT Hub-eszközkiépítési szolgáltatásban (DPS) a Service SDK-k használatával
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975074"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Eszközregisztrációk kezelése az Azure-eszközkiépítési szolgáltatás SDK-kkal
Az *eszközregisztráció* egyetlen eszköz vagy eszközcsoport rekordját hozza létre, amely egy bizonyos ponton regisztrálhat az eszközkiépítési szolgáltatással. A regisztrációs rekord tartalmazza az eszköz(ek) kezdeti kívánt konfigurációját a regisztráció részeként, beleértve a kívánt IoT hubot is. Ez a cikk bemutatja, hogyan kezelheti az eszközregisztrációk a létesítési szolgáltatás programozott használatával az Azure IoT-létesítési szolgáltatás SDK-k.  Az SDK-k a GitHubon érhetők el ugyanabban a tárházban, mint az Azure IoT SDK-k.

## <a name="prerequisites"></a>Előfeltételek
* Szerezze be a kapcsolati karakterláncot az Eszközkiépítési szolgáltatás példányából.
* Szerezze be az eszköz biztonsági összetevőit az [alkalmazott tanúsítványmechanizmushoz:](concepts-security.md#attestation-mechanism)
    * [**Platformmegbízhatósági modul (TPM):**](/azure/iot-dps/concepts-security#trusted-platform-module)
        * Egyéni regisztráció: Regisztrációs azonosító és TPM-ellenőrzőkulcs fizikai eszközről vagy TPM-szimulátorból.
        * A regisztrációs csoport nem vonatkozik a TPM-tanúsítványra.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Egyéni regisztráció: A [Leaf tanúsítvány](/azure/iot-dps/concepts-security) fizikai eszközről vagy az SDK [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulátor.
        * Igénylési csoport: A [hitelesítésszolgáltatói/főtanúsítvány](/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány,](/azure/iot-dps/concepts-security#intermediate-certificate)amely eszköztanúsítvány fizikai eszközön történő előállításához használatos.  Az SDK DICE emulátorból is létrehozható.
* A pontos API-hívások a nyelvi különbségek miatt eltérőek lehetnek. A részletekért tekintse át a GitHubon megadott mintákat:
   * [Java provisioning service ügyfélminták](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node.js kiépítési szolgáltatásügyfél-minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET-kiépítési szolgáltatásügyfél-minták](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Eszközregisztráció létrehozása
Az eszközöket kétféleképpen regisztrálhatja a kiépítési szolgáltatással:

* A **beléptetési csoport** olyan eszközök csoportjának bejegyzése, amelyek az X.509 tanúsítványok közös tanúsítvány-tanúsítási mechanizmusával rendelkeznek, és amelyeket a [főtanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)ír alá. Azt javasoljuk, hogy egy regisztrációs csoport nagy számú eszköz, amely a kívánt kezdeti konfigurációt, vagy az eszközök minden megy ugyanahhoz a bérlőhöz. Ne feledje, hogy az X.509 tanúsítványmechanizmust regisztrációs *csoportként*használó eszközöket csak regisztrálhatja. 

    A munkafolyamatot követően létrehozhat egy regisztrációs csoportot az SDK-kkal:

    1. Az igénylési csoport esetében az igazolási mechanizmus X.509 főtanúsítványt használ.  Hívja meg a ```X509Attestation.createFromRootCertificate``` Service SDK API-t főtanúsítvánnyal a regisztrációhoz való tanúsítvány létrehozásához.  Az X.509 főtanúsítvány PEM-fájlban vagy karakterláncként érhető el.
    1. Hozzon ```EnrollmentGroup``` létre egy ```attestation``` új változót a létrehozott és egy egyedi ```enrollmentGroupId```használatával.  Tetszés szerint olyan ```Device ID```paramétereket is beállíthat, mint a , ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Hívja meg a ```createOrUpdateEnrollmentGroup``` Szolgáltatás SDK ```EnrollmentGroup``` API-t a háttéralkalmazásban egy regisztrációs csoport létrehozásához.

* **Az egyéni regisztráció** egyetlen eszköz bejegyzése, amely regisztrálhat. Az egyéni beléptetések x.509-es tanúsítványokat vagy SAS-jogkivonatokat használhatnak (fizikai vagy virtuális TPM-ből) igazolási mechanizmusként. Azt javasoljuk, hogy egyedi regisztrációkat használjon az egyedi kezdeti konfigurációt igénylő eszközökhöz, vagy olyan eszközökhöz, amelyek csak TPM-en vagy virtuális TPM-en keresztül használhatják a SAS-jogkivonatokat igazolási mechanizmusként. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    A munkafolyamatot követően egyéni regisztrációt hozhat létre az SDK-kkal:
    
    1. Válassza ```attestation``` ki a mechanizmust, amely lehet TPM vagy X.509.
        1. **TPM**: Az ellenőrzőkulcs fizikai eszközről vagy a TPM-szimulátor bemeneti, ```TpmAttestation``` hívhatja Service SDK API-t, hogy tanúsítványt hozzon létre a regisztrációhoz. 
        2. **X.509:** Az ügyféltanúsítvány bevitele ként meghívhat Service ```X509Attestation.createFromClientCertificate``` SDK API-t a regisztrációhoz használt tanúsítvány létrehozásához.
    2. Hozzon ```IndividualEnrollment``` létre egy ```attestation``` új változót ```registrationId``` a létrehozott és egy egyedi bemeneti, amely az eszközön, vagy generált a TPM Simulator.  Tetszés szerint olyan ```Device ID```paramétereket is beállíthat, mint a , ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Hívja meg a ```createOrUpdateIndividualEnrollment``` Service SDK ```IndividualEnrollment``` API-t a háttéralkalmazásban egy egyéni regisztráció létrehozásához.

Miután sikeresen létrehozott egy regisztrációt, az eszközkiépítési szolgáltatás egy regisztrációs eredményt ad vissza. Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

## <a name="update-an-enrollment-entry"></a>Regisztrációs bejegyzés frissítése

Miután létrehozott egy regisztrációs bejegyzést, érdemes lehet frissíteni a regisztrációt.  A lehetséges forgatókönyvek közé tartozik a kívánt tulajdonság frissítése, az igazolási módszer frissítése vagy az eszközhozzáférés visszavonása.  Az egyéni regisztrációhoz és a csoportregisztrációhoz különböző API-k tartoznak, de a tanúsítványmechanizmus nem különbözik.

A regisztrációs bejegyzéseket a következő munkafolyamat ot követően frissítheti:
* **Egyéni regisztráció**:
    1. Először a Service SDK API-val ```getIndividualEnrollment```szerezheti be a legújabb regisztrációt a létesítési szolgáltatásból.
    2. Szükség szerint módosítsa a legutóbbi regisztráció paraméterét. 
    3. A legutóbbi regisztráció használatával hívja meg ```createOrUpdateIndividualEnrollment``` a Service SDK API-t a regisztrációs bejegyzés frissítéséhez.
* **Csoportos regisztráció**:
    1. Először a Service SDK API-val ```getEnrollmentGroup```szerezheti be a legújabb regisztrációt a létesítési szolgáltatásból.
    2. Szükség szerint módosítsa a legutóbbi regisztráció paraméterét.
    3. A legutóbbi regisztráció használatával hívja meg ```createOrUpdateEnrollmentGroup``` a Service SDK API-t a regisztrációs bejegyzés frissítéséhez.

Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

## <a name="remove-an-enrollment-entry"></a>Regisztrációs bejegyzés eltávolítása

* **Az egyéni regisztráció** a Service SDK API ```deleteIndividualEnrollment``` hívásával törölhető a használatával. ```registrationId```
* **A csoportregisztráció** a Service SDK ```deleteEnrollmentGroup``` ```enrollmentGroupId```API hívásával törölhető a használatával.

Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

## <a name="bulk-operation-on-individual-enrollments"></a>Tömeges művelet az egyes regisztrációkon

Tömeges műveletet hajthat végre több egyéni regisztráció létrehozására, frissítésére vagy eltávolítására a munkafolyamatot követően:

1. Hozzon létre egy ```IndividualEnrollment```több .  Ennek a változónak a megvalósítása minden nyelven eltérő.  Tekintse át a tömeges művelet minta a GitHubon a részletekért.
2. Hívja meg a ```runBulkOperation``` Service ```BulkOperationMode``` SDK API-t a kívánt művelettel és a változóval az egyes regisztrációkhoz. Négy mód támogatott: létrehozás, frissítés, updateIfMatchEtag és törlés.

Miután sikeresen végrehajtott egy műveletet, az eszközkiépítési szolgáltatás tömeges művelet eredményt ad vissza.

Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

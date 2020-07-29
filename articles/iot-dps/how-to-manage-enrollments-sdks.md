---
title: Eszközök regisztrálásának kezelése az Azure DPS SDK-k használatával
description: Az eszközök regisztrálásának kezelése a IoT Hub Device Provisioning Serviceban (DPS) a Service SDK-k használatával
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74975074"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Az eszközök regisztrálásának kezelése az Azure Device kiépítési szolgáltatás SDK-k használatával
Az *eszközök* regisztrálása egyetlen eszköz vagy eszközök egy csoportját hozza létre, amelyek egy bizonyos ponton regisztrálhatnak az eszköz kiépítési szolgáltatásával. A beléptetési rekord tartalmazza az eszköz (ek) kezdeti kívánt konfigurációját a regisztráció részeként, beleértve a kívánt IoT hubot is. Ez a cikk bemutatja, hogyan kezelheti az eszközök regisztrációját a kiépítési szolgáltatás számára az Azure IoT-létesítési szolgáltatás SDK-k használatával programozott módon.  Az SDK-k a GitHubon érhetők el ugyanazon a tárházon, mint az Azure IoT SDK-k.

## <a name="prerequisites"></a>Előfeltételek
* Szerezze be a kapcsolatok karakterláncát az eszköz kiépítési szolgáltatásának példányáról.
* Szerezze be az eszköz biztonsági összetevőit a használt [igazolási mechanizmushoz](concepts-security.md#attestation-mechanism) :
    * [**Platformmegbízhatósági modul (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Egyéni regisztráció: regisztrációs azonosító és TPM-kiterjesztési kulcs fizikai eszközről vagy TPM-szimulátorból.
        * A regisztrációs csoport nem vonatkozik a TPM-igazolásra.
    * [**X. 509**](/azure/iot-dps/concepts-security):
        * Egyéni regisztráció: a fizikai eszközről vagy az SDK [Dice](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulátorból származó [levél tanúsítványa](/azure/iot-dps/concepts-security) .
        * Beléptetési Csoport: a [hitelesítésszolgáltató/főtanúsítvány](/azure/iot-dps/concepts-security#root-certificate) vagy a [közbenső tanúsítvány](/azure/iot-dps/concepts-security#intermediate-certificate), amely az eszköz tanúsítványának fizikai eszközön történő előállítására szolgál.  Az SDK-DICE emulátorból is létrehozható.
* A pontos API-hívások a nyelvi különbségek miatt eltérőek lehetnek. A részletekért tekintse át a GitHubon megadott mintákat:
   * [Java-kiépítési szolgáltatás ügyféloldali mintái](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node.js kiépítési szolgáltatás ügyféloldali mintái](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET-kiépítési szolgáltatás ügyféloldali mintái](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Eszközök regisztrálásának létrehozása
Az eszközöket kétféleképpen lehet regisztrálni a kiépítési szolgáltatással:

* A **beléptetési csoport** olyan eszközök csoportja, amelyek osztoznak az X. 509 tanúsítványok közös igazolási mechanizmusán, amelyet a [Főtanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [közbenső tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)írt alá. Azt javasoljuk, hogy nagy számú, a kívánt kezdeti konfigurációt megosztó eszközhöz használjon beléptetési csoportot, vagy ha az összes eszköz ugyanahhoz a bérlőhöz fog csatlakozni. Vegye figyelembe, hogy csak az X. 509 igazolási mechanizmust használó eszközök regisztrálása *beléptetési csoportként*. 

    A munkafolyamatot követő SDK-k segítségével létrehozhat egy regisztrációs csoportot:

    1. Beléptetési csoport esetén az igazolási mechanizmus X. 509 főtanúsítványt használ.  Hívja meg a Service SDK API ```X509Attestation.createFromRootCertificate``` -t a főtanúsítvánnyal a regisztrációhoz szükséges igazolás létrehozásához.  Az X. 509 főtanúsítvány egy PEM-fájlban vagy karakterláncként van megadva.
    1. Hozzon létre egy új ```EnrollmentGroup``` változót a ```attestation``` létrehozott és egy egyedi használatával ```enrollmentGroupId``` .  Opcionálisan a következő paramétereket is megadhatja:, ```Device ID``` ```IoTHubHostName``` ```ProvisioningStatus``` .
    2. Hívja a Service SDK API-t a ```createOrUpdateEnrollmentGroup``` háttérbeli alkalmazásban ```EnrollmentGroup``` egy regisztrációs csoport létrehozásához.

* Az **Egyéni regisztráció** egy olyan bejegyzés, amely regisztrálható egyetlen eszközön. Az egyéni regisztrációk X. 509 tanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális TPM-ből) is használhatnak igazolási mechanizmusként. Azt javasoljuk, hogy egyéni regisztrációkat használjon olyan eszközökhöz, amelyek egyedi kezdeti konfigurációt igényelnek, vagy olyan eszközök esetében, amelyek csak a TPM vagy a virtuális TPM modulon keresztül használhatják az igazolási mechanizmusként szolgáló SAS-jogkivonatokat. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    Létrehozhat egy egyéni regisztrációt a munkafolyamatot követő SDK-k használatával:
    
    1. Válassza ki a ```attestation``` mechanizmust, amely TPM vagy X. 509 lehet.
        1. **TPM**: a jóváhagyó kulcs használata fizikai eszközről vagy TPM-szimulátorból bemenetként a Service SDK API meghívásával ```TpmAttestation``` igazolást hozhat létre a regisztráláshoz. 
        2. **X. 509**: az ügyféltanúsítvány használata bemenetként a Service SDK API meghívásával ```X509Attestation.createFromClientCertificate``` tanúsítványokat hozhat létre a regisztráláshoz.
    2. Hozzon létre egy új ```IndividualEnrollment``` változót a ```attestation``` létrehozott és egy egyedi ```registrationId``` bemenettel, amely az eszközön található, vagy a TPM-szimulátorból lett létrehozva.  Opcionálisan a következő paramétereket is megadhatja:, ```Device ID``` ```IoTHubHostName``` ```ProvisioningStatus``` .
    3. Az egyéni regisztráció létrehozásához hívja a Service SDK API ```createOrUpdateIndividualEnrollment``` -t a háttérbeli alkalmazásban ```IndividualEnrollment``` .

A regisztráció sikeres létrehozása után az eszköz kiépítési szolgáltatása beléptetési eredményt ad vissza. Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

## <a name="update-an-enrollment-entry"></a>Beléptetési bejegyzés frissítése

A regisztrációs bejegyzés létrehozása után érdemes lehet frissíteni a beléptetést.  Lehetséges forgatókönyvek például a kívánt tulajdonság frissítése, az igazolási módszer frissítése vagy az eszközök hozzáférésének visszavonása.  Különböző API-k vannak az egyéni regisztrációhoz és a csoportos regisztrációhoz, de az igazolási mechanizmus nem tesz különbséget.

A következő munkafolyamat után frissítheti a beléptetési bejegyzést:
* **Egyéni regisztráció**:
    1. Először a Service SDK API-val szerezze be a kiépítési szolgáltatás legújabb regisztrációját ```getIndividualEnrollment``` .
    2. Szükség szerint módosítsa a legújabb regisztráció paraméterét. 
    3. A legfrissebb regisztrációval hívja meg a Service SDK API ```createOrUpdateIndividualEnrollment``` -t a beléptetési bejegyzés frissítéséhez.
* **Csoportos regisztráció**:
    1. Először a Service SDK API-val szerezze be a kiépítési szolgáltatás legújabb regisztrációját ```getEnrollmentGroup``` .
    2. Szükség szerint módosítsa a legújabb regisztráció paraméterét.
    3. A legfrissebb regisztrációval hívja meg a Service SDK API ```createOrUpdateEnrollmentGroup``` -t a beléptetési bejegyzés frissítéséhez.

Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

## <a name="remove-an-enrollment-entry"></a>Beléptetési bejegyzés eltávolítása

* Az **Egyéni regisztráció** törléséhez hívja a Service SDK API-t ```deleteIndividualEnrollment``` a használatával ```registrationId``` .
* A **csoportos regisztráció** törléséhez hívja a Service SDK API-t ```deleteEnrollmentGroup``` a használatával ```enrollmentGroupId``` .

Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

## <a name="bulk-operation-on-individual-enrollments"></a>Tömeges művelet az egyes regisztrációk esetében

A munkafolyamatot követő több egyéni regisztráció létrehozásához, frissítéséhez vagy eltávolításához tömeges műveletet is végrehajthat:

1. Hozzon létre egy változót, amely többt tartalmaz ```IndividualEnrollment``` .  A változó megvalósítása minden nyelv esetében eltér.  A részletekért tekintse át a GitHubon a tömeges művelet mintáját.
2. Hívja meg a Service SDK API ```runBulkOperation``` -t a ```BulkOperationMode``` kívánt művelethez és a változóhoz az egyes regisztrációk esetében. Négy mód támogatott: létrehozás, frissítés, updateIfMatchEtag és törlés.

A művelet sikeres elvégzése után az eszköz kiépítési szolgáltatása tömeges művelet eredményét fogja visszaadni.

Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

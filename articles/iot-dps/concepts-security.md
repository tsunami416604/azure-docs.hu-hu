---
title: Azure IoT Hub-eszközkiépítési szolgáltatás – Biztonsági fogalmak
description: Az eszközkiépítési szolgáltatással (DPS) és az IoT Hubmal rendelkező eszközökre vonatkozó biztonsági kiépítési fogalmak ismertetése
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271563"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT Hub-eszközkiépítési szolgáltatás biztonsági fogalmai 

Az IoT Hub-eszközkiépítési szolgáltatás egy segítő szolgáltatás az IoT Hub, amely segítségével konfigurálja a nulla érintésű eszköz kiépítése egy adott IoT hub. Az eszközkiépítési szolgáltatással [biztonságos](concepts-auto-provisioning.md) és méretezhető módon több millió eszközt biztosíthat automatikusan. Ez a cikk áttekintést nyújt az eszköz kiépítéssel kapcsolatos *biztonsági* fogalmakról. Ez a cikk minden olyan személy számára fontos, aki részt vesz egy eszköz üzembe helyezésre való felszerzésében.

## <a name="attestation-mechanism"></a>Igazolási mechanizmus

Az igazolási mechanizmus az eszköz identitásának megerősítésére használt módszer. Az igazolási mechanizmus is fontos a regisztrációs lista, amely megmondja a létesítési szolgáltatás, amely egy adott eszköz höz használt tanúsítvány milyen módszerrel.

> [!NOTE]
> Az IoT Hub "hitelesítési sémát" használ egy hasonló koncepcióhoz az adott szolgáltatásban.

Az Eszközkiépítési szolgáltatás a következő igazolási formákat támogatja:
* **X.509 tanúsítványok** a szabványos X.509 tanúsítványhitelesítési folyamat alapján.
* **Platformmegbízhatósági modul (TPM)** egy nonce kihívás alapján, a TPM-szabvány használatával a kulcsok egy aláírt megosztott hozzáférésű aláírási (SAS) jogkivonat bemutatásához. Ez a tanúsítványforma nem igényel fizikai TPM-et az eszközön, de a szolgáltatás elvárja, hogy tanúsítsa az ellenőrző kulcs használatát a [TPM specifikációszerint.](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)
* **A** megosztott hozzáférésű aláírás (SAS) [biztonsági jogkivonatain](../iot-hub/iot-hub-devguide-security.md#security-tokens)alapuló szimmetrikus kulcs , amely kivonatolt aláírást és beágyazott lejárati rendszert tartalmaz. További információ: [Symmetric key attestation](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A hardveres biztonsági modul (HSM) az eszköztitkok biztonságos, hardveralapú tárolására szolgál, és a titkos tárolás legbiztonságosabb formája. Mind az X.509-es tanúsítványok, mind a SAS-jogkivonatok tárolhatók a HSM-ben. A HSM-ek a kiépítés által támogatott mindkét tanúsítványmechanizmussal használhatók.

> [!TIP]
> Javasoljuk, hogy használjon HSM-et olyan eszközökkel, amelyek biztonságosan tárolják a titkos kulcsokat az eszközökön.

Az eszköztitkok szoftverben (memóriában) is tárolhatók, de kevésbé biztonságos tárolási forma, mint a HSM.

## <a name="trusted-platform-module"></a>Platformmegbízhatósági modul (TPM)

A TPM hivatkozhat a platform hitelesítéséhez használt kulcsok biztonságos tárolására vonatkozó szabványra, vagy a szabványt megvalósító modulokkal való interakcióhoz használt I/O interfészre. A TPM-ek különálló hardverként, integrált hardverként, belső vezérlőprogram-alapúként vagy szoftveralapúként létezhetnek. További információ a [TPM-ekről és a TPM-igazolásokról.](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation) Az eszközkiépítési szolgáltatás csak a TPM 2.0-s támogatását támogatja.

A TPM-tanúsítvány egy nonce kihíváson alapul, amely az ellenőrző és tárolási gyökérkulcsokat használja egy aláírt sas-jogkivonat bemutatásához.

### <a name="endorsement-key"></a>Ellenőrző kulcs

Az ellenőrző kulcs a TPM-ben található aszimmetrikus kulcs, amelyet a gyártási idő alatt hoztak létre vagy injektáltak, és minden TPM esetében egyedi. Az ellenőrzőkulcs nem módosítható és nem távolítható el. Az ellenőrzőkulcs titkos része soha nem szabadul fel a TPM-en kívül, míg az ellenőrzőkulcs nyilvános része az eredeti TPM felismerésére szolgál. További információ az [ellenőrző kulcsról](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Tároló gyökérkulcsa

A tárológyökér-kulcs a TPM-ben van tárolva, és az alkalmazások által létrehozott TPM-kulcsok védelmére szolgál, így ezek a kulcsok nem használhatók a TPM nélkül. A tároló gyökérkulcsa akkor jön létre, amikor átveszi a TPM tulajdonjogát; ha törli a TPM-et, hogy egy új felhasználó átvehesse a tulajdonjogot, új tárológyökér-kulcs jön létre. További információ a [tároló gyökérkulcsáról.](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx)

## <a name="x509-certificates"></a>X.509 bizonyítványok

Az X.509 tanúsítványok igazolási mechanizmusként való használata kiváló módja az éles környezet méretezésének és az eszközkiépítés egyszerűsítésének. Az X.509 tanúsítványok általában egy megbízhatósági tanúsítványláncba vannak rendezve, amelyben a lánc minden tanúsítványát a következő magasabb tanúsítvány titkos kulcsa írja alá, és így tovább, önaláírt főtanúsítványban végződve. Ez a megállapodás létrehoz egy delegált bizalmi láncot a megbízható legfelső szintű hitelesítésszolgáltató (CA) által létrehozott főtanúsítványból az egyes köztes hitelesítésszolgáltatókon keresztül az eszközre telepített végfelhasználói "levél" tanúsítványig. További információ: [Device Authentication using X.509 CA Certificates](/azure/iot-hub/iot-hub-x509ca-overview). 

A tanúsítványlánc gyakran az eszközökhöz társított logikai vagy fizikai hierarchiát jelöli. Például a gyártó:
- önaláírt legfelső szintű hitelesítésszolgáltatói tanúsítvány kiállítása
- használja a főtanúsítványt, hogy egyedi köztes hitelesítésszolgáltatói tanúsítványt hozzon létre minden gyárhoz
- az egyes gyári tanúsítványok felhasználásával egyedi köztes hitelesítésszolgáltatói tanúsítványt állít elő az üzem minden egyes gyártósorához
- és végül használja a gyártósori tanúsítványt, hogy egyedi eszköz (end-entity) tanúsítványt hozzon létre a sorban gyártott minden egyes eszközhöz. 

További információ: [Az IoT-iparág X.509 hitelesítésszolgáltatói tanúsítványainak fogalmi ismerete.](/azure/iot-hub/iot-hub-x509ca-concept) 

### <a name="root-certificate"></a>Főtanúsítvány

A főtanúsítvány egy hitelesítésszolgáltatót (CA) jelölő, önaláírt X.509-es tanúsítvány. Ez a tanúsítványlánc végállomása vagy megbízhatósági horgonya. A főtanúsítványokat egy szervezet önkiadta, vagy megvásárolhatja egy főtanúsítvány-szolgáltatótól. További információ: [X.509 hitelesítésszolgáltatói tanúsítványok beszerezhetése](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). A főtanúsítvány ra legfelső szintű hitelesítésszolgáltatói tanúsítványként is lehet hivatkozni.

### <a name="intermediate-certificate"></a>Köztes tanúsítvány

A köztes tanúsítvány egy X.509 tanúsítvány, amelyet a főtanúsítvány (vagy egy másik köztes tanúsítvány, amelynek láncában a főtanúsítvány) írta alá. A lánc utolsó köztes tanúsítványa a levéltanúsítvány aláírására szolgál. A köztes tanúsítványra köztes hitelesítésszolgáltatói tanúsítványnak is lehet tekinteni.

### <a name="end-entity-leaf-certificate"></a>Végfelhasználói "levél" tanúsítvány

A levéltanúsítvány vagy a végfelhasználói tanúsítvány azonosítja a tanúsítvány tulajdonosát. A tanúsítványláncban a főtanúsítvány, valamint nulla vagy több köztes tanúsítvány található. A levéltanúsítvány nem használható más tanúsítványok aláírására. Egyedileg azonosítja az eszközt a létesítési szolgáltatás, és néha az eszköz tanúsítvány. A hitelesítés során az eszköz a tanúsítványhoz társított személyes kulcsot használja a szolgáltatás birtokában lévő kihívás igazolásának megválaszolására.

[Az egyéni beléptetési](./concepts-service.md#individual-enrollment) bejegyzéshez használt levéltanúsítványoknak követelményként kell megkötniük a **Tulajdonos nevét** az Egyéni beléptetési bejegyzés regisztrációs azonosítójára. A [regisztrációs csoport](./concepts-service.md#enrollment-group) bejegyzésével használt levéltanúsítványok **tulajdonosnevét** a kívánt eszközazonosítóra kell állítani, amely megjelenik a regisztrációs csoport hitelesített eszközének **regisztrációs nyilvántartásában.**

További információ: [Az X.509 hitelesítésszolgáltatói tanúsítvánnyal aláírt eszközök hitelesítése.](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates)

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Az eszköz hozzáférésének szabályozása a létesítési szolgáltatáshoz X.509 tanúsítványokkal

A létesítési szolgáltatás kétféle regisztrációs bejegyzést tesz elérhetővé, amelyek segítségével szabályozhatja az X.509-es tanúsítványmechanizmust használó eszközök hozzáférését:  

- [Az egyes beléptetési](./concepts-service.md#individual-enrollment) bejegyzések egy adott eszközhöz társított eszköztanúsítvánnyal vannak konfigurálva. Ezek a bejegyzések szabályozzák az adott eszközök regisztrációját.
- [Az igénylési csoport](./concepts-service.md#enrollment-group) bejegyzései egy adott köztes vagy legfelső szintű hitelesítésszolgáltatói tanúsítványhoz vannak társítva. Ezek a bejegyzések szabályozzák az összes olyan eszköz regisztrációját, amely a tanúsítványláncban rendelkezik ezzel a köztes vagy főtanúsítvánnyal. 

Amikor egy eszköz csatlakozik a létesítési szolgáltatáshoz, a szolgáltatás rangsorolja a konkrét regisztrációs bejegyzéseket a kevésbé specifikus regisztrációs bejegyzésekkel szemben. Ez azt, hogy ha az eszköz egyéni regisztrációlétezik, a létesítési szolgáltatás alkalmazza ezt a bejegyzést. Ha nincs egyéni regisztráció az eszközhöz, és az eszköz tanúsítványláncában található első köztes tanúsítvány igénylési csoportja létezik, a szolgáltatás ezt a bejegyzést alkalmazza, és így tovább, a gyökérlánchoz. A szolgáltatás az általa talált első alkalmazandó bejegyzést alkalmazza, úgy, hogy:

- Ha a talált első regisztrációs bejegyzés engedélyezve van, a szolgáltatás elhatárolásra képessé tesz az eszközt.
- Ha az első talált regisztrációs bejegyzés le van tiltva, a szolgáltatás nem építi ki az eszközt.  
- Ha az eszköz tanúsítványláncában lévő tanúsítványok egyikéhez sem található beléptetési bejegyzés, a szolgáltatás nem építi ki az eszközt. 

Ez a mechanizmus és a tanúsítványláncok hierarchikus struktúrája hatékony rugalmasságot biztosít az egyes eszközök és eszközcsoportok hozzáférésének szabályozásában. Képzeljen el például öt eszközt a következő tanúsítványláncokkal: 

- *1. eszköz:*-> A->-eszköz tanúsítványa
- *2. eszköz:*-> -> device 2 tanúsítvány
- *3. eszköz*: főtanúsítvány -> A-> 3-as eszköz tanúsítványa
- *4. eszköz:*-> B->->-eszköz tanúsítványa
- *5. eszköz:* root tanúsítvány -> B-> 5-ös eszköz tanúsítványa

Kezdetben létrehozhat egy engedélyezett csoport regisztrációs bejegyzést a főtanúsítványhoz, hogy mind az öt eszközhöz hozzáférést biztosítson. Ha a B tanúsítvány később veszélybe kerül, létrehozhat egy letiltott regisztrációs csoportbejegyzést a B tanúsítványhoz, hogy megakadályozza *a 4-es* és *az 5-ös eszköz* regisztrációját. Ha még később *a 3-as eszköz* biztonsága sérül, létrehozhat egy letiltott egyéni beléptetési bejegyzést a tanúsítványához. Ez visszavonja a hozzáférést a *Device 3,* de továbbra is lehetővé teszi *az Eszköz 1* és device *2* regisztrálása.
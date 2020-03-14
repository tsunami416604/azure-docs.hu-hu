---
title: Azure IoT Hub Device Provisioning Service – biztonsági fogalmak
description: Az eszközök kiépítési szolgáltatásával (DPS) és IoT Hubekkel kapcsolatos biztonsági kiépítési fogalmakat ismerteti.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271563"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT Hub Device Provisioning Service biztonsági fogalmak 

A IoT Hub Device Provisioning Service egy olyan IoT Hub segítő szolgáltatás, amellyel a nulla érintéses eszköz kiépíthető a megadott IoT hubhoz. A Device kiépítési szolgáltatással akár több millió eszközt is biztonságosan és méretezhető módon lehet [automatikusan kiépíteni](concepts-auto-provisioning.md) . Ez a cikk áttekintést nyújt az eszközök kiépítés során felmerülő *biztonsági* fogalmakról. Ez a cikk az eszközök üzembe helyezésére való felkészüléshez szükséges összes Personával kapcsolatos.

## <a name="attestation-mechanism"></a>Igazolási mechanizmus

Az igazolási mechanizmus az eszköz identitásának megerősítésére szolgál. Az igazolási mechanizmus a beléptetési listához is kapcsolódik, amely közli a kiépítési szolgáltatással, hogy az adott eszközzel milyen igazolást kell használni.

> [!NOTE]
> IoT Hub a "hitelesítési séma" kifejezést használja az adott szolgáltatáshoz hasonló fogalomhoz.

A Device kiépítési szolgáltatás a következő igazolási formákat támogatja:
* **X. 509 tanúsítványok** a szabványos x. 509 tanúsítvány-hitelesítési folyamat alapján.
* **Platformmegbízhatósági modul (TPM)** egy egyszeres kihívás alapján, a kulcsokhoz tartozó TPM standard használatával egy aláírt közös hozzáférésű aláírási (SAS-) tokent mutat be. Ez az igazolás nem igényel fizikai TPM-t az eszközön, de a szolgáltatás a [TPM-specifikáció](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)alapján tanúsítja, hogy a hátirat kulcsát használja.
* A **szimmetrikus kulcs** a közös hozzáférésű aláírás (SAS) [biztonsági jogkivonatok](../iot-hub/iot-hub-devguide-security.md#security-tokens)alapján, amelyek egy kivonatoló aláírást és egy beágyazott lejáratot foglalnak magukban. További információ: [szimmetrikus kulcs igazolása](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A hardveres biztonsági modul, vagy a HSM biztonságos, hardveres tárolást biztosít az eszköz titkai számára, és a titkos tároló legbiztonságosabb formája. Az X. 509 tanúsítványok és az SAS-tokenek a HSM-ben is tárolhatók. A HSM a kiépítés által támogatott igazolási mechanizmusokkal is használható.

> [!TIP]
> Javasoljuk, hogy használjon egy HSM-et az eszközökkel, hogy biztonságosan tárolja az eszközein a titkokat.

Az eszköz titkai a szoftverben (memóriában) is tárolhatók, de ez a tárterület kevésbé biztonságos, mint a HSM.

## <a name="trusted-platform-module"></a>platformmegbízhatósági modul

A TPM a platform hitelesítéséhez használt kulcsok biztonságos tárolására szolgál, vagy a szabványt megvalósító modulokkal való interakcióhoz használt I/O-felületre hivatkozhat. A TPM különálló hardverként, integrált hardverként, belső vezérlőprogram-vagy szoftver-alapúként is létezhetnek. További információ a [TPM és a TPM-igazolásról](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Az eszközök kiépítési szolgáltatása csak a TPM 2,0-es verziója használatát támogatja.

A TPM-igazolás egy egyszer használatos kihíváson alapul, amely a jóváhagyás és a tároló legfelső szintű kulcsait használja egy aláírt közös hozzáférésű aláírási (SAS) jogkivonat megjelenítéséhez.

### <a name="endorsement-key"></a>Érvényesítési kulcs

A jóváhagyó kulcs egy aszimmetrikus kulcs, amely a TPM-ben található, amelyet belsőleg generáltak vagy injektáltak a gyártási időszakban, és minden TPM esetében egyediek. A jóváhagyó kulcs nem módosítható és nem távolítható el. A jóváhagyó kulcs privát része soha nem jelenik meg a TPM-en kívül, míg a jóváhagyó kulcs nyilvános része egy valódi TPM felismerésére szolgál. További információ a [jóváhagyó kulcsról](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Tároló legfelső szintű kulcsa

A tároló legfelső szintű kulcsát a TPM tárolja, és az alkalmazások által létrehozott TPM-kulcsok elleni védelemre szolgál, így ezek a kulcsok nem használhatók a TPM nélkül. A rendszer a tároló legfelső szintű kulcsát hozza létre a TPM tulajdonjogának elvégzése során. Ha törli a TPM-t, hogy az új felhasználó tulajdonjogot szabadítson fel, új tárolási legfelső szintű kulcs jön létre. További információ a [tároló legfelső szintű kulcsáról](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X. 509 tanúsítványok

Az X. 509 tanúsítványok az igazolási mechanizmusként való használata kiváló módszer a termelés méretezésére és az eszközök üzembe helyezésének egyszerűsítésére. Az X. 509 tanúsítványokat jellemzően olyan megbízhatósági láncba rendezi a rendszer, amelyben a láncban lévő összes tanúsítványt aláírja a következő magasabb tanúsítvány titkos kulcsa, és így tovább, egy önaláírt főtanúsítványban leáll. Ez a megállapodás egy megbízható legfelső szintű hitelesítésszolgáltató (CA) által létrehozott főtanúsítványból delegált megbízhatósági láncot hoz létre az egyes közbenső HITELESÍTÉSSZOLGÁLTATÓKon keresztül az eszközre telepített végfelhasználói "levél" tanúsítványhoz. További információért lásd: [az eszközök hitelesítése X. 509 hitelesítésszolgáltatói tanúsítványokkal](/azure/iot-hub/iot-hub-x509ca-overview). 

A tanúsítványlánc gyakran az eszközökhöz társított logikai vagy fizikai hierarchiát jelképezi. A gyártó például a következőket teheti:
- önaláírt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány kiállítása
- a főtanúsítvány használata egyedi közbenső HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány előállításához minden egyes gyárhoz
- az egyes gyári tanúsítványok használata egy egyedi közbenső HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány előállításához a üzemben lévő minden egyes gépsor esetében
- Végül pedig használja az éles vonal tanúsítványát, hogy létrehozzon egy egyedi eszköz-(végfelhasználói) tanúsítványt a sorban gyártott összes eszközhöz. 

További információ: [az X. 509 hitelesítésszolgáltatói tanúsítványok fogalmi megértése a IoT-iparágban](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Főtanúsítvány

A főtanúsítvány egy önaláírt X. 509 tanúsítvány, amely egy hitelesítésszolgáltatót (CA) jelöl. Ez a tanúsítványlánc végállomása vagy megbízhatósági kapcsolata. A főtanúsítványokat a szervezet önállóan vagy egy főtanúsítvány-szolgáltatótól vásárolhatja meg. További információért lásd: [X. 509 hitelesítésszolgáltatói tanúsítványok beolvasása](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). A főtanúsítványt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak is nevezzük.

### <a name="intermediate-certificate"></a>Köztes tanúsítvány

A köztes tanúsítvány egy X. 509 tanúsítvány, amelyet a főtanúsítvány (vagy egy másik, a lánc főtanúsítványával rendelkező közbenső tanúsítvány) írt alá. A lánc utolsó köztes tanúsítványa a levél tanúsítványának aláírására szolgál. A köztes tanúsítványok köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak is lehet hivatkozni.

### <a name="end-entity-leaf-certificate"></a>Végfelhasználói "levél" tanúsítvány

A levél tanúsítványa vagy a végfelhasználói tanúsítvány azonosítja a tanúsítvány tulajdonosát. A tanúsítvány főtanúsítványa a tanúsítványlánc, valamint nulla vagy több köztes tanúsítvány. A levél tanúsítványa nem használható más tanúsítványok aláírására. Egyedileg azonosítja az eszközt a kiépítési szolgáltatás számára, és más néven az eszköz tanúsítványa. A hitelesítés során az eszköz a tanúsítványhoz tartozó titkos kulcsot használja arra, hogy válaszoljon a szolgáltatásból származó birtoklási kihívásra.

Az [Egyéni beléptetési](./concepts-service.md#individual-enrollment) bejegyzésekhez használt levél-tanúsítványokhoz követelmény, hogy a **tulajdonos nevét** az egyéni beléptetési bejegyzés regisztrációs azonosítójára kell beállítani. A [beléptetési csoport](./concepts-service.md#enrollment-group) bejegyzéseihez használt levél-tanúsítványoknak a **tulajdonos nevét** a kívánt eszköz-azonosítóra kell beállítani, amely a beléptetési csoportban lévő hitelesített eszköz **regisztrációs rekordjaiban** jelenik meg.

További információért lásd: [X. 509 hitelesítésszolgáltatói tanúsítványokkal aláírt eszközök hitelesítése](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Eszköz hozzáférésének szabályozása a kiépítési szolgáltatáshoz X. 509 tanúsítvánnyal

A kiépítési szolgáltatás két típusú beléptetési bejegyzést tesz elérhetővé, amelyek segítségével szabályozhatja az X. 509 igazolási mechanizmust használó eszközök hozzáférését:  

- Az [egyes beléptetési](./concepts-service.md#individual-enrollment) bejegyzések egy adott eszközhöz társított eszköz tanúsítványával vannak konfigurálva. Ezek a bejegyzések vezérlik az adott eszközök regisztrációit.
- A [beléptetési csoport](./concepts-service.md#enrollment-group) bejegyzései egy adott közbenső vagy legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal vannak társítva. Ezek a bejegyzések vezérlik az összes olyan eszköz regisztrációját, amelynél az adott közbenső vagy főtanúsítvány szerepel a tanúsítvány láncában. 

Amikor egy eszköz csatlakozik a kiépítési szolgáltatáshoz, a szolgáltatás a kevésbé konkrét beléptetési bejegyzéseken keresztül rangsorolja a beléptetési bejegyzéseket. Azaz ha az eszközhöz egyéni regisztráció van, a kiépítési szolgáltatás alkalmazza ezt a bejegyzést. Ha nincs egyéni regisztráció az eszközhöz, és egy regisztrációs csoport az eszköz tanúsítványlánc-láncában az első közbenső tanúsítványhoz, a szolgáltatás alkalmazza a bejegyzést, és így tovább, a láncot a gyökérbe. A szolgáltatás a megtalált első megfelelő bejegyzést alkalmazza, például:

- Ha az első beléptetési bejegyzés engedélyezve van, a szolgáltatás kiépíti az eszközt.
- Ha az első beléptetési bejegyzés le van tiltva, a szolgáltatás nem építi ki az eszközt.  
- Ha nem található beléptetési bejegyzés az eszköz tanúsítványlánc egyik tanúsítványához sem, a szolgáltatás nem építi ki az eszközt. 

Ez a mechanizmus és a tanúsítványlánc hierarchikus szerkezete nagy rugalmasságot biztosít az egyes eszközökhöz, illetve az eszközök csoportjaihoz való hozzáférés szabályozásához. Tegyük fel például, hogy öt eszköz a következő tanúsítványláncot: 

- *1. eszköz*: főtanúsítvány – > A tanúsítvány – > eszköz 1 tanúsítványa
- *2. eszköz*: főtanúsítvány – > A tanúsítvány – > eszköz 2 tanúsítványa
- *3. eszköz*: főtanúsítvány – > tanúsítvány A-> 3. eszköz tanúsítványa
- *4. eszköz*: főtanúsítvány – > B tanúsítvány > 4. eszköz tanúsítványa
- *5. eszköz*: főtanúsítvány – > B tanúsítvány > 5. eszköz tanúsítványa

Kezdetben létrehozhat egyetlen engedélyezett csoportos beléptetési bejegyzést a főtanúsítvány számára, hogy az összes öt eszköz számára engedélyezze a hozzáférést. Ha a B tanúsítvány később sérül, létrehozhat egy letiltott regisztrációs csoport bejegyzést a B tanúsítványhoz, amely megakadályozza a *4* . és az *5. eszköz* regisztrációját. Ha továbbra is a *3. eszköz* biztonsága sérül, létrehozhat egy letiltott egyéni beléptetési bejegyzést a tanúsítványához. Ez visszavonja a *3. eszköz*hozzáférését, de az *1* . és a *2* . eszköz regisztrációját is lehetővé teszi.
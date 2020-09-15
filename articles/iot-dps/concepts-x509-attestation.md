---
title: Azure IoT Hub Device Provisioning Service-X. 509 tanúsítvány igazolása
description: Az X. 509 tanúsítvány-igazolás és a Device kiépítési szolgáltatás (DPS) és a IoT Hub használatával kapcsolatos fogalmakat ismerteti.
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 911f819343f675ebe0a2604d912e6e26aa646eb5
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533060"
---
# <a name="x509-certificate-attestation"></a>X.509 tanúsítványigazolás

Ez a cikk áttekintést nyújt az eszközök X. 509 tanúsítvány igazolásával történő kiépítés során felmerülő fogalmakról. Ez a cikk az eszközök üzembe helyezésére való felkészüléshez szükséges összes Personával kapcsolatos.

Az X. 509 tanúsítványokat a hardveres biztonsági modul HSM-ben lehet tárolni.

> [!TIP]
> Javasoljuk, hogy használjon egy HSM-et az eszközökkel, hogy biztonságosan tárolja a titkokat, például az X. 509 tanúsítványt az eszközökön az éles környezetben.


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
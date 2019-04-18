---
title: Az Azure IoT Hub Device Provisioning Service - TPM-igazolás
description: Ez a cikk a TPM-igazolás IoT Device Provisioning Service használatával folyamat fogalmi áttekintése.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 07c5dbce0b98d1c197164f4fc77682f78ede57f0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048877"
---
# <a name="tpm-attestation"></a>TPM-igazolás

IoT Hub Device Provisioning Service, amellyel egy adott IoT hub használatával beavatkozás nélküli eszközök konfigurálása IoT hub segítő szolgáltatása. A Device Provisioning Service szolgáltatással biztonságosan is millió eszköz kiépítését.

Ez a cikk ismerteti az identitás igazolási folyamat használata esetén egy [TPM](./concepts-device.md). TPM-et a platformmegbízhatósági modul rövidítése, és a egy hardveres biztonsági modul (HSM) típusú. Ez a cikk feltételezi, hogy egy különálló, belső vezérlőprogram használ, vagy TPM integrált. Szoftver emulált TPM kiválóan alkalmas prototípus-készítés, illetve tesztelési, de azok nem nyújtanak biztonsági diszkrét, belső vezérlőprogram, azonos szintű, vagy integrált TPM tegye. Éles környezetben TPM szoftver használatát nem javasoljuk. TPM típusaival kapcsolatban további információkért lásd: [röviden bemutatja A TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Ez a cikk csak akkor jelentősége HMAC-val kulcs támogatása, valamint azok ellenőrzőkulcs kulcsok TPM 2.0-val használó eszközök esetében. Akkor sem eszközök X.509-tanúsítványokat használnak a hitelesítéshez. A TPM-egész iparágra kiterjedő, ISO szabvány a Trusted Computing Group a, és tudjon meg többet a TPM a [teljes TPM 2.0 specifikációja](https://trustedcomputinggroup.org/tpm-library-specification/) vagy a [ISO/IEC 11889 specifikációja](https://www.iso.org/standard/66510.html). Ez a cikk azt is feltételezi, ismeri a nyilvános és titkos kulcspárokat és azok hogyan használhatók a titkosításhoz.

A Device Provisioning Service-eszközök az SDK-k kezelni a mindent, ami az Ön számára ebben a cikkben leírt. Hiba esetén nem kell, hogy semmi mást megvalósításához, ha az eszközök az SDK-kat használ. Ez a cikk segít megérteni a koncepciót tekintve, eseményeit a TPM biztonsági lapkával amikor az eszköz építi ki, és miért így biztonságos.

## <a name="overview"></a>Áttekintés

TPM a biztonságos a megbízható legfelső szintű úgynevezett az ellenőrzőkulcs (EK) használja. Az Ellenőrzőkulcs egyedi, a TPM-be, és megváltoztatásának lényegében módosítja az eszköz be egy új.

Nincs más típusú, hogy TPM rendelkezik,-e a tároló-gyökérkulcs (tároló-gyökérkulcs azonosító) nevű kulcsot. Egy tároló-gyökérkulcs azonosító a TPM-tulajdonosi generálhatja, miután vesz igénybe a platformmegbízhatósági modul. A TPM-eszköz saját tulajdonba módja a TPM-specifikus üzenettel, "valaki beállít egy jelszót a HSM-en." TPM-eszköz új tulajdonosnak vásárolható meg, ha az új tulajdonos tulajdonukba vehetik a TPM-eszköz hozzon létre egy új tároló-gyökérkulcs azonosító. Az új tároló gyökérkulcs azonosító generációs biztosítja, hogy az előző tulajdonosétól. a TPM-eszköz nem használható. Mivel a tároló Gyökérkulcsa egyedi, a TPM-eszköz tulajdonosa, a tároló-gyökérkulcs azonosító magából a TPM a tulajdonos számára az adatok lezárása használható. A tároló Gyökérkulcsa egy tesztkörnyezet, a tulajdonos, hogy a kulcsokat biztosít, és hozzáférést revocability ide, ha az eszköz vagy TPM vásárolható meg. Olyan, egy új házat megéri: saját tulajdonba módosítása a ajtók szüntetni, és a korábbi tulajdonos tároló gyökérkulcs (azonosító) által az összes bútordarab megsemmisítése, de nem módosíthatja a ház (EK) címét.

Ha egy eszköz be van állítva, és készen áll a használatra, fog rendelkezni az Ellenőrzőkulcs és a egy tároló-gyökérkulcs azonosító használható.

![A TPM saját tulajdonba](./media/concepts-tpm-attestation/tpm-ownership.png)

A TPM-eszköz saját tulajdonba a Megjegyzés: A TPM saját tulajdonba attól függ, hogy sok-sok dolog, beleértve a TPM gyártójával, használja a TPM-eszközök készlete és az eszköz operációs rendszere. Kövesse az utasításokat saját tulajdonba a rendszerben.

A Device Provisioning Service azonosíthatja és-eszközök regisztrálása az Ellenőrzőkulcs (EK_pub) nyilvános részét használja. Az eszköz gyártója olvashatja a EK_pub előállítás vagy végleges tesztelése során, és töltse fel a kiépítési szolgáltatás a EK_pub úgy, hogy az eszköz felismer, amikor csatlakozik a kiépítése. A Device Provisioning Service nem ellenőrzi a tároló gyökérkulcs azonosító vagy a tulajdonos, így "törlésének" a TPM-eszköz törli az ügyféladatokat, de az Ellenőrzőkulcs (és más gyártói adatokat) megmaradjanak, és az eszköz továbbra is felismer a Device Provisioning Service üzembe helyezése szolgáltatáshoz való csatlakozáskor.

## <a name="detailed-attestation-process"></a>Részletes igazolási folyamat

Amikor a TPM-eszköz először csatlakozik a Device Provisioning Service, a szolgáltatás először ellenőrzi a megadott EK_pub ellen a EK_pub tárolja a regisztrációs listában. Ha nem egyeznek a EK_pubs, az eszköz nem engedélyezett üzembe helyezni. A EK_pubs felelnek meg, ha a szolgáltatás majd megköveteli az eszköz az Ellenőrzőkulcs nonce problémásnak bizonyulhatnak, ami biztonságos kihívást használt személyazonosság-n keresztül titkos része tulajdonjogának igazolásához. A Device Provisioning Service létrehoz egy egyszeri, és ezután titkosítja a tároló Gyökérkulcsa, majd a EK_pub, amelyek által biztosított az eszközt a kezdeti regisztrációs hívás során. A TPM mindig a privát része az Ellenőrzőkulcs biztonságban tartja. Ez megakadályozza, hogy a hamisítás, és biztosítja a SAS-tokeneket biztonságosan felhasznált engedéllyel rendelkező eszközökre.

Vizsgáljunk meg részletesen az igazolási folyamat.

### <a name="device-requests-an-iot-hub-assignment"></a>Eszköz kér egy IoT Hub-hozzárendelés

Az eszköz először csatlakozik a Device Provisioning Service, és kéri, hogy üzembe helyezése. Ennek során az eszköz megadja a szolgáltatás regisztrációs Azonosítóval, egy azonosító hatóköre és a EK_pub és SRK_pub a TPM-ből. A szolgáltatás a titkosított egyszeri továbbítja a az eszközt, és az egyszeri visszafejteni, és újra csatlakozhat, és üzembe helyezése SAS-tokent használ, amely az eszköz kéri.

![Eszköz kérések kiépítése](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>NONCE challenge

Az eszköz vesz igénybe a egyszeri, és használja az Ellenőrzőkulcs és a tároló gyökérkulcs azonosító titkos része az egyszeri visszafejteni a TPM-eszközre; a rendelés nonce titkosítási ad megbízhatósági az Ellenőrzőkulcs, amely nem módosítható, a tároló Gyökérkulcsa, amely módosítható, ha új tulajdonost a platformmegbízhatósági modul vesz igénybe.

![Az egyszeri visszafejtése](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Ellenőrizze az egyszeri és hitelesítő adatok fogadása

Az eszköz ezután egy SAS-jogkivonat használatával a visszafejtett egyszeri bejelentkezés és helyreállítani a kapcsolatot a Device Provisioning Service az aláírt SAS-jogkivonat használatával. Az egyszeri feladat befejeződött, az a szolgáltatás lehetővé teszi, hogy az eszköz kiépítéséhez.

![Eszköz helyreállítja a csatolást kapcsolat a Device Provisioning Service Ellenőrzőkulcs tulajdonjogának ellenőrzése](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>További lépések

Most, hogy az eszköz csatlakozik az IoT hubhoz, és biztos abban a tudatban, hogy az eszközök kulcsok biztonságos tárolása biztonságos. Most, hogy megismerte, hogyan a Device Provisioning Service biztonságos ellenőrzi egy eszköz identitásának TPM-eszköz használatával, tekintse meg a következő cikkekben további információ:

* [További információ az Automatikus kiépítés kapcsolatos fogalmak](./concepts-auto-provisioning.md)
* [Ismerkedés az automatikus regisztráció](./quick-setup-auto-provision.md) az SDK-k segítségével gondoskodik a folyamatot.

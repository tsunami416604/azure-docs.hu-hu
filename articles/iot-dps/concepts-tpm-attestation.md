---
title: Az Azure IoT Hub eszköz-üzembehelyezési szolgáltatás – a TPM-igazolás
description: Ez a cikk általános áttekintést nyújt az a TPM igazolás folyamata az IoT-eszközök kiépítés szolgáltatásának használatát.
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 90f41e56f8e95584959576d3e5ad837f4774048a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629082"
---
# <a name="tpm-attestation"></a>TPM-igazolás

IoT Hub eszköz kiépítése szolgáltatáshoz olyan IoT hub, amely egy megadott IoT-központ nulla érintéssel eszközkiépítési konfigurálására használt segítő szolgáltatás. Az eszköz kiépítése szolgáltatáshoz megadhat eszközök millióira biztonságos módon.

Ez a cikk ismerteti a identitását igazoló folyamat használata esetén egy [TPM](./concepts-device.md). TPM a platformmegbízhatósági modul rövidítése, és a hardveres biztonsági modul (HSM) típusa. Ez a cikk azt feltételezi, hogy a különálló, belső vezérlőprogram használja, vagy integrált TPM. Szoftver emulált TPM jól alkalmazható a prototípusának vagy tesztelni, de azok nem adja meg a biztonsági, különálló, belső vezérlőprogram azonos szinten, vagy integrált TPM tegye. Szoftverrel TPM éles környezetben nem javasoljuk. [További](http://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf) TPM típusú.

Ez a cikk csak fontos eszközökön TPM 2.0 és HMAC kulcs támogatása és az ellenőrzőkulcs kulcsok használatával. Nincs eszközök X.509 tanúsítvány alapú hitelesítést használ. TPM az iparágban használt, a Trusted Computing Group az ISO-szabvány, és további a TPM a [teljes TPM 2.0 spec](https://trustedcomputinggroup.org/tpm-library-specification/) vagy a [ISO/IEC 11889 spec](https://www.iso.org/standard/66510.html). Ez a cikk is azt feltételezi, hogy ismeri a nyilvános és titkos kulcspár, és azok használata a titkosításhoz.

Az eszköz kiépítése szolgáltatáshoz eszköz az SDK-k kezelni a mindent meg ebben a cikkben ismertetett. Nincs szükség az Ön megvalósítani semmi mást, ha az SDK-k használnak az eszközök számára. Ez a cikk segít megérteni fogalmilag, mi jelenít meg a TPM biztonsági lapka használata során az eszköz rendelkezéseket és miért, biztonságos.

## <a name="overview"></a>Áttekintés

TPM használja a kulcsa (EK) úgynevezett biztonságos megbízhatóság. A Ellenőrzőkulcs egyedi, a TPM-be, és ez lényegében módosítása az eszközön be egy új.

Nincs más típusú kulcsot, hogy a TPM rendelkezik, néven a legfelső szintű kulcsot (tároló gyökérkulcs azonosító). Egy tároló-gyökérkulcs azonosító a TPM-tulajdonosi generálhatja, miután a platformmegbízhatósági modul vesz igénybe. A TPM saját tulajdonba módja a TPM-specifikus megkapta a "valaki állítja be a jelszót a a hardveres biztonsági MODULT." A TPM eszköz új tulajdonosnak értékesítik, ha az új tulajdonos tulajdonba a TPM egy új tároló-gyökérkulcs azonosító létrehozásához. Az új tároló gyökérkulcs azonosító generációs biztosítja, hogy az előző tulajdonoshoz nem használható a TPM-be. Mivel a tároló Gyökérkulcsa a TPM tulajdonosának egyedi, a tároló Gyökérkulcsa segítségével zárja le adatokat a TPM-eszközre magát, hogy a tulajdonos. A tároló Gyökérkulcsa biztosít a tulajdonos a kulcsok tárolására védőfalhoz, és hozzáférést revocability biztosít, ha az eszköz vagy a TPM megvásárolható. Egy új házat áthelyezése hasonlít: átvétel módosítása a ajtók a zárolását, és a korábbi tulajdonos tároló gyökérkulcs (azonosító) által hátrahagyott összes berendezés megsemmisítése, de a ház (EK) a cím nem módosítható.

Ha egy eszköz be van állítva, és készen áll a használatra, lesz egy Ellenőrzőkulcs és egy tároló-gyökérkulcs azonosító használható is.

![Saját tulajdonba vétel TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

A TPM saját tulajdonba a OneNote-ra: számos elemet, beleértve a TPM gyártóját, a TPM eszközök használt, és az eszköz operációs rendszere saját tulajdonba vétel TPM függ. Kövesse az utasításokat saját tulajdonba a rendszerben.

Az eszköz kiépítése szolgáltatáshoz használ a nyilvános részét a Ellenőrzőkulcs (EK_pub) regisztrálhatják eszközeiket. Az eszköz gyártója olvashatja a EK_pub előállítás vagy végső tesztelése során, és töltse fel a létesítési szolgáltatás a EK_pub, hogy az eszköz felismerje a rendszer a kiépítés való kapcsolódáskor. Az eszköz kiépítése szolgáltatáshoz nem ellenőrzi a tároló gyökérkulcs azonosító vagy a tulajdonos, így "törlésének" a TPM törli az ügyféladatokat, de a Ellenőrzőkulcs (és más gyártó adatok) megmaradjanak, és az eszköz továbbra is felismer az eszköz kiépítése szolgáltatáshoz kiépítés való kapcsolódáskor.

## <a name="detailed-attestation-process"></a>Részletes állapotigazolási folyamat

Amikor TPM Modullal rendelkező eszköz, először az eszköz kiépítése szolgáltatáshoz csatlakozik, a szolgáltatás először ellenőrzi a megadott EK_pub szemben a EK_pub, a beléptetési listában. Ha a EK_pubs nem egyezik, az eszköz nem engedélyezett kiépítését. A EK_pubs felelnek meg, ha a szolgáltatás majd megköveteli az eszköz tulajdonjogát, a titkos része, amely identitásának igazolásához használt biztonságos kihívást nonce kihívást keresztül Ellenőrzőkulcs igazolásához. Az eszköz kiépítése szolgáltatáshoz egy nonce hoz létre, és ezután titkosítja a tároló Gyökérkulcsa, majd a EK_pub, amelyek mindegyikét által biztosított az eszköz kezdeti regisztrációs hívás során. A TPM mindig tartja a Ellenőrzőkulcs titkos része biztonságos. Ezzel megakadályozza, hogy a hamisítás, és biztosítja a SAS-tokenje biztonságosan szolgálnak hitelesített eszközöket.

A tanúkiszolgáló folyamatot részletesen bemutatjuk.

### <a name="device-requests-an-iot-hub-assignment"></a>Eszköz kér az IoT-központ hozzárendelés

Az eszköz először csatlakozik az eszköz kiépítése szolgáltatáshoz, és kiépítését kéri. Ennek során az eszköz biztosítja azt a szolgáltatást a regisztrációs Azonosítót, egy azonosító hatókört, és a EK_pub és SRK_pub a TPM modul. A szolgáltatás átadja a titkosított nonce vissza az eszközt, és az eszköz használatával fejti vissza a nonce, és, amelyek segítségével bejelentkezhetnek a SAS-jogkivonat ismételt csatlakozással, és üzembe helyezése kéri.

![Kiépítés eszköz kérelmek](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>NONCE kérdés

Az eszköz a nonce vesz igénybe, és használja a Ellenőrzőkulcs és a tároló gyökérkulcs azonosító titkos része a nonce visszafejtése a TPM-eszközre; nonce titkosítási delegáltak bizalmi kapcsolat a Ellenőrzőkulcs, amely nem módosítható, a tároló Gyökérkulcsa, amelyen módosítható, ha az új tulajdonos veszi a platformmegbízhatósági modul sorrendjét.

![A nonce visszafejtése](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>A nonce érvényesítése és a hitelesítő adatok fogadására

Az eszköz ezután a visszafejtett nonce használatával SAS-jogkivonat aláírása és az eszköz kiépítése szolgáltatáshoz az aláírt SAS tokent kapcsolat visszaállítása. A Nonce kérdéssel befejeződött a szolgáltatás lehetővé teszi, hogy az eszköz kiépítése.

![Eszköz helyreállítja a csatolást kapcsolatot terjesztési pontok Ellenőrzőkulcs tulajdonjog ellenőrzése](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>További lépések

Az eszköz csatlakozik, IoT-központot, és a rest-e meg, hogy az eszközök kulcsokat biztonságosan tárolja a Tudásbázis biztonságos. Most, hogy tudja, hogyan az eszköz kiépítése szolgáltatáshoz biztonságosan ellenőrzi a TPM használatával egy eszköz identitása, tekintse meg az alábbi cikkekből tudhat meg többet:

* [Automatikus üzembe helyezése a fogalmak megismerése](./concepts-auto-provisioning.md)
* [Az Automatikus kiépítés első lépéseiben](./quick-setup-auto-provision.md) gondoskodunk a folyamat az SDK-k használatával.

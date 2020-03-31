---
title: Azure IoT Hub-eszközkiépítési szolgáltatás – TPM-tanúsítvány
description: Ez a cikk az IoT-eszközlétesítési szolgáltatás (DPS) használatával a TPM-tanúsítványfolyamat fogalmi áttekintését ismerteti.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 624171ffc10a06ac3089b6dceb1683c63c88dbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975278"
---
# <a name="tpm-attestation"></a>TPM-igazolás

Az IoT Hub-eszközkiépítési szolgáltatás egy segítő szolgáltatás az IoT Hub, amely segítségével konfigurálja a nulla érintésű eszköz kiépítése egy adott IoT hub. Az eszközkiépítési szolgáltatással több millió eszközt biztosíthat biztonságos módon.

Ez a cikk a [TPM](./concepts-device.md)használata kori identitás-igazolási folyamatot ismerteti. A TPM a platformmegbízhatósági modul rövidítése, és egyfajta hardveres biztonsági modul (HSM). Ez a cikk feltételezi, hogy különálló, belső vezérlőprogramot vagy integrált TPM-et használ. A szoftveremulátortutánzó TPM-ek kiválóan alkalmasak prototípus-készítésre vagy tesztelésre, de nem nyújtanak ugyanolyan szintű biztonságot, mint a különálló, belső vezérlőprogram vagy integrált TPM-ek. Nem javasoljuk a szoftveres TPM-ek használatát éles környezetben. A TPM-típusokról további információt [a Rövid bevezetés a TPM-hez](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf)című témakörben talál.

Ez a cikk csak a HMAC-kulcstámogatással és ellenőrzőkulcsokkal rendelkező TPM 2.0-s eszközt használó eszközök esetében fontos. Nem az X.509-es tanúsítványokat használó eszközökre van. A TPM a Trusted Computing Group iparági szabványa, és a TPM-ről a [teljes TPM 2.0 specifikáción](https://trustedcomputinggroup.org/tpm-library-specification/) vagy az [ISO/IEC 11889 specifikáción](https://www.iso.org/standard/66510.html)olvashat bővebben. Ez a cikk azt is feltételezi, hogy ismeri a nyilvános és személyes kulcspárokat, valamint a titkosításhoz való használatukmódját.

Az eszközkiépítési szolgáltatás SDK-k kezeli konkálása mindent, ami ebben a cikkben az Ön számára. Nincs szükség további eszközök bevezetésére, ha az SDK-kat használja az eszközökön. Ez a cikk segít megérteni fogalmilag, mi történik a TPM biztonsági chip, ha az eszköz rendelkezéseket, és miért olyan biztonságos.

## <a name="overview"></a>Áttekintés

A TPM-ek a megbízhatóság biztonságos gyökereként az ellenőrzőkulcsot (EK) használják. Az EK egyedülálló a TPM-ben, és a módosítás lényegében újat változtat az eszközből.

Van egy másik típusú kulcs, amelyet a Tpm-ek rendelkeznek, az úgynevezett tárológyökér-kulcs (SRK). Az SRK-t a TPM tulajdonosa generálhatja, miután a TPM tulajdonjogát veszi át. A TPM tulajdonjogának átvétele a TPM-specifikus módja annak, hogy azt mondja: "valaki jelszót állít be a HSM-en.". Ha egy TPM-eszközt új tulajdonosnak adnak el, az új tulajdonos átveheti a TPM tulajdonjogát egy új SRK létrehozásához. Az új SRK generáció biztosítja, hogy az előző tulajdonos ne használhassa a TPM-et. Mivel az SRK a TPM tulajdonosa számára egyedi, az SRK használható adatok zárolására az adott tulajdonos számára a TPM-be. Az SRK egy sandboxot biztosít a tulajdonos számára a kulcsok tárolásához, és hozzáférést biztosít az eszköz vagy a TPM értékesítése esetén. Ez olyan, mint beköltözik egy új házat: tulajdonjogát megváltoztatja a zárak az ajtókon, és elpusztítja az összes bútor maradt az előző tulajdonosok (SRK), de nem lehet megváltoztatni a címét, a ház (EK).

Miután egy eszköz be van állítva, és készen áll a használatra, akkor lesz mind egy EK és egy SRK használható.

![A TPM tulajdonjogának átvétele](./media/concepts-tpm-attestation/tpm-ownership.png)

Egy megjegyzés a TPM tulajdonjogának átvételéről: A TPM tulajdonjogának átvétele sok mindentől függ, beleértve a TPM gyártóját, a használt TPM-eszközök készletét és az eszköz operációs rendszerét. A tulajdonláshoz kövesse a rendszerre vonatkozó utasításokat.

Az eszközkiépítési szolgáltatás az EK (EK_pub) nyilvános részét használja az eszközök azonosítására és regisztrálására. Az eszköz szállítója a gyártás vagy a végső tesztelés során olvashatja a EK_pub, és feltöltheti a EK_pub a létesítési szolgáltatásba, hogy az eszköz felismerje, amikor csatlakozik a kiépítéshez. Az eszközkiépítési szolgáltatás nem ellenőrzi az SRK-t vagy a tulajdonost, így a TPM "törli" az ügyféladatokat, de az EK (és más szállítói adatok) megmarad, és az eszköz továbbra is felismeri az eszközkiépítési szolgáltatás, amikor csatlakozik a kiépítéshez.

## <a name="detailed-attestation-process"></a>Részletes igazolási folyamat

Amikor egy TPM-mel rendelkező eszköz először csatlakozik az eszközkiépítési szolgáltatáshoz, a szolgáltatás először ellenőrzi a megadott EK_pub a regisztrációs listában tárolt EK_pub. Ha a EK_pubs nem egyeznek, az eszköz nem rendelkezhet. Ha a EK_pubs nem egyeznek, a szolgáltatás, akkor megköveteli az eszközt, hogy igazolja tulajdonjogát a magán része az EK keresztül nonce kihívás, amely egy biztonságos kihívás, amely et használnak, hogy igazolja személyazonosságát. Az eszközkiépítési szolgáltatás létrehoz egy nonce, majd titkosítja azt az SRK, majd a EK_pub, amelyek mindegyike biztosítja az eszköz a kezdeti regisztrációs hívás során. A TPM mindig biztonságban tartja az EK privát részét. Ez megakadályozza a hamisítást, és biztosítja, hogy a SAS-jogkivonatok biztonságosan legyenek kiépítve az engedélyezett eszközökre.

Nézzük végig a tanúsítvány folyamat részletesen.

### <a name="device-requests-an-iot-hub-assignment"></a>Az eszköz IoT Hub-hozzárendelést kér

Először az eszköz csatlakozik az eszköz kiépítési szolgáltatáshoz, és kéri a kiépítést. Ennek során az eszköz biztosítja a szolgáltatást a regisztrációs azonosító, egy azonosító hatókör, valamint a EK_pub és SRK_pub a TPM. A szolgáltatás továbbítja a titkosított nonce vissza az eszközre, és kéri az eszközt, hogy visszafejti a nonce, és használja, hogy aláírja a SAS-jogkivonatot, hogy újra csatlakozzon, és befejezni a kiépítést.

![Eszközkérelmek kiépítése](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce kihívás

A készülék veszi a nonce, és használja a magán részeit az EK és SRK dekódolni a nonce a TPM; a nonce titkosítási delegáltok bizalmi sorrendje az EK-tól, amely nem módosítható, az SRK-nak, amely változhat, ha egy új tulajdonos átveszi a TPM tulajdonjogát.

![A nonce visszafejtése](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>A nonce ellenőrzése és hitelesítő adatok fogadása

Az eszköz ezután aláírhatja a SAS-jogkivonatot a visszafejtett nonce használatával, és újra létrehozhat egy kapcsolatot az eszközkiépítési szolgáltatással az aláírt SAS-jogkivonat használatával. A Nonce kihívás befejeződött, a szolgáltatás lehetővé teszi az eszköz kiépítése.

![Az eszköz újra létrehozza a kapcsolatot az eszközkiépítési szolgáltatással az EK-tulajdonjog érvényesítéséhez](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>További lépések

Most az eszköz csatlakozik az IoT Hubhoz, és biztonságban nyugszik abban a tudatban, hogy az eszközök kulcsai biztonságosan vannak tárolva. Most, hogy már tudja, hogy az eszközkiépítési szolgáltatás hogyan ellenőrzi biztonságosan az eszköz identitását a TPM használatával, további információért tekintse meg az alábbi cikkeket:

* [Ismerje meg az automatikus kiépítés összes koncepcióját](./concepts-auto-provisioning.md)
* [Az SDK-k](./quick-setup-auto-provision.md) használatával első lépések a folyamat ügyelete.

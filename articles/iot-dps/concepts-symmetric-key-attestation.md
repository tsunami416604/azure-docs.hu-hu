---
title: Azure IoT Hub-eszközkiépítési szolgáltatás – szimmetrikus kulcsigazolás
description: Ez a cikk az IoT-eszközkiépítési szolgáltatás (DPS) használatával használt szimmetrikus kulcsigazolás fogalmi áttekintését ismerteti.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 0e3d343c0a68dd527e4e8e8d23e5b3843a216a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271511"
---
# <a name="symmetric-key-attestation"></a>Szimmetrikus kulcsú igazolás

Ez a cikk ismerteti az identitás-tanúsítvány folyamat, ha szimmetrikus kulcsokat használ az eszközkiépítési szolgáltatás használata. 

A szimmetrikus kulcsigazolás egy egyszerű módszer az eszköz kiépítési szolgáltatáspéldánysal való hitelesítéséhez. Ez az igazolási módszer egy "Hello world" élményt jelent azoknak a fejlesztőknek, akik újak az eszközkiépítésben, vagy nem rendelkeznek szigorú biztonsági követelményekkel. A [TPM](concepts-tpm-attestation.md) vagy [X.509 tanúsítvánnyal](concepts-security.md#x509-certificates) használt eszközigazolás biztonságosabb, és szigorúbb biztonsági követelmények hez kell használni.

A szimmetrikus kulcsregisztrációk nagyszerű lehetőséget biztosítanak a korlátozott biztonsági funkciókkal rendelkező örökölt eszközök számára, hogy az Azure IoT-n keresztül a felhőbe induljanak. A régebbi eszközökkel való szimmetrikus kulcsigazolásról a [Szimmetrikus kulcsok használata az örökölt eszközökkel című](how-to-legacy-device-symm-key.md)témakörben talál további információt.


## <a name="symmetric-key-creation"></a>Szimmetrikus kulcs létrehozása

Alapértelmezés szerint az Eszközkiépítési szolgáltatás új szimmetrikus kulcsokat hoz létre, amelyek alapértelmezett hossza 32 bájt, amikor új beléptetéseket ment az **Automatikus létrehozási kulcsok** beállítás engedélyezve van.

![Szimmetrikus billentyűk automatikus létrehozása](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

A beállítás letiltásával saját szimmetrikus kulcsokat is biztosíthat a regisztrációkhoz. Saját szimmetrikus kulcsok megadásakor a kulcsok kulcsának 16 és 64 bájt közötti hosszúságúnak kell lennie. A szimmetrikus kulcsokat érvényes Base64 formátumban is meg kell adni.



## <a name="detailed-attestation-process"></a>Részletes igazolási folyamat

Szimmetrikus kulcs igazolást az eszközkiépítési szolgáltatás végzi az azonos [biztonsági jogkivonatok](../iot-hub/iot-hub-devguide-security.md#security-token-structure) által támogatott IoT hubok eszközök azonosítására. Ezek a biztonsági jogkivonatok [a megosztott hozzáférésű aláírás (SAS) jogkivonatok.](../service-bus-messaging/service-bus-sas.md) 

A SAS-jogkivonatok kivonatolt *aláírással* rendelkeznek, amely a szimmetrikus kulccsal jön létre. Az aláírást az eszközkiépítési szolgáltatás újra létrehozza annak ellenőrzésére, hogy az igazolás során bemutatott biztonsági token hiteles-e vagy sem.

A SAS-tokenek a következő űrlappal rendelkeznek:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Az egyes tokenek összetevői:

| Érték | Leírás |
| --- | --- |
| {aláírás} |HMAC-SHA256 aláírási karakterlánc. Az egyes regisztrációk esetében ez az aláírás a szimmetrikus kulccsal (elsődleges vagy másodlagos) a kivonat végrehajtásával készül létre. A beléptetési csoportok esetében a beléptetési csoport kulcsából származó kulcs használható a kivonat végrehajtásához. A kivonat az űrlap üzenetén történik: `URL-encoded-resourceURI + "\n" + expiry`. **Fontos:** A hmac-SHA256 számítás végrehajtásához való használat előtt a kulcsot base64-ből kell dekódolni. Az aláírás eredményének URL-kódolásúnak is kell lennie. |
| {resourceURI} |A regisztrációs végpont URI-ja, amely ezzel a jogkivonattal érhető el, kezdve az eszközkiépítési szolgáltatás példány hatókör-azonosítójával. Például: `{Scope ID}/registrations/{Registration ID}` |
| {lejárat} |UTF8 karakterláncok másodpercek száma a 00:00:00 UTC 1970. |
| {URL-kódolt-resourceURI} |A kisbetűs erőforrás URI-jának kisbetűs URL-kódolása |
| {policyName} |Annak a megosztott hozzáférési szabályzatnak a neve, amelyre ez a jogkivonat hivatkozik. A szimmetrikus kulcsigazolással történő kiépítéskor használt házirendnév a **regisztráció.** |

Amikor egy eszköz tanúsítja az egyéni regisztrációt, az eszköz az egyes beléptetési bejegyzésben definiált szimmetrikus kulcsot használja a SAS-jogkivonat-aláírás létrehozásához.

A SAS-jogkivonatot létrehozó kódpéldákért lásd: [Biztonsági jogkivonatok.](../iot-hub/iot-hub-devguide-security.md#security-token-structure)

Az Azure IoT C SDK támogatja a szimmetrikus kulcsigazolásbiztonsági tokenek létrehozását. Például az Azure IoT C SDK egyéni regisztrációval tanúsítandó használata, [lásd: Egy szimulált eszköz kiépítése szimmetrikus kulcsokkal.](quick-create-simulated-device-symm-key.md)


## <a name="group-enrollments"></a>Csoportos regisztrációk

A csoportregisztrációk szimmetrikus kulcsait az eszközök nem használják közvetlenül a kiépítés során. Ehelyett olyan eszközök, amelyek egy regisztrációs csoport kiépítéshez tartoznak egy származtatott eszközkulcs használatával. 

Először egy egyedi regisztrációs azonosító van definiálva minden egyes, regisztrációs csoporttal rendelkező eszközhöz. A regisztrációs azonosító érvényes karakterei a kisalfanumerikus és a kötőjel ('-'). Ennek a regisztrációs azonosítónak egyedinek kell lennie, amely azonosítja az eszközt. Előfordulhat például, hogy egy örökölt eszköz nem támogat számos biztonsági funkciót. Előfordulhat, hogy az örökölt eszköz csak MAC-címmel vagy sorozatszámmal rendelkezik az eszköz egyedi azonosításához. Ebben az esetben a regisztrációs azonosító a következőhöz hasonló MAC-címből és sorszámból állhat:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Ez a pontos példa a [hogyan lehet kiépíteni](how-to-legacy-device-symm-key.md) az örökölt eszközök szimmetrikus kulcsok használatával cikket.

Miután egy regisztrációs azonosítót definiáltak az eszközhöz, a regisztrációs csoport szimmetrikus kulcsát használja a regisztrációs azonosító [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) kivonatának kiszámításához egy származtatott eszközkulcs létrehozásához. A regisztrációs azonosító kivonatolása a következő C# kóddal hajtható végre:

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Az eredményül kapott eszközkulcs ezután egy tanúsítványhoz használandó SAS-jogkivonat létrehozásához használatos. A regisztrációs csoport minden egyes eszköze egyedi származtatott kulcsból létrehozott biztonsági jogkivonat használatával szükséges. A regisztrációs csoport szimmetrikus kulcsa nem használható közvetlenül az igazoláshoz.

#### <a name="installation-of-the-derived-device-key"></a>A származtatott eszközkulcs telepítése

Ideális esetben az eszköz kulcsok származtatják és telepítik a gyárban. Ez a módszer garantálja, hogy a csoportkulcs soha nem része az eszközre telepített szoftvereknek. Ha az eszközhöz MAC-cím vagy sorozatszám van rendelve, a kulcs származtatható és beadható az eszközbe, azonban a gyártó úgy dönt, hogy tárolja azt.

Vegye figyelembe az alábbi ábrát, amely a gyárban létrehozott eszközkulcsok táblázatát mutatja az egyes eszközregisztrációs azonosítók csoportregisztrációs kulccsal (**K**) való kivonatolásával. 

![Gyárból rendelt eszközkulcsok](./media/concepts-symmetric-key-attestation/key-diversification.png)

Az egyes eszközök identitását a gyárban telepített regisztrációs azonosító és származtatott eszközkulcs jelöli. Az eszközkulcs soha nem másolódik más helyre, és a csoportkulcs soha nem kerül tárolásra az eszközön.

Ha az eszközkulcsok nincsenek telepítve a gyárban, az eszközidentitás biztonságos tárolásához egy [hardveres biztonsági modult](concepts-security.md#hardware-security-module) kell használni a HSM-hez.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri a szimmetrikus kulcs tanúsítvány, nézd meg a következő cikkeket, hogy többet:

* [Rövid útmutató: Szimmetrikus kulcs kiosztása szimulált eszköz számára](quick-create-simulated-device-symm-key.md)
* [Ismerje meg az automatikus kiépítés fogalmait](./concepts-auto-provisioning.md)
* [Az automatikus kiépítés használatának első lépései](./quick-setup-auto-provision.md) 

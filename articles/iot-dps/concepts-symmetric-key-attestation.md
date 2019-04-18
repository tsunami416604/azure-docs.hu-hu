---
title: Az Azure IoT Hub Device Provisioning Service - szimmetrikus kulcsigazolás
description: Ez a cikk az IoT Device Provisioning Service használatával szimmetrikus kulcsigazolás fogalmi áttekintése.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 2f6e1e1a27e32e567cf0eaa8ff7a99046ed81bbe
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050944"
---
# <a name="symmetric-key-attestation"></a>Szimmetrikus kulcsú igazolás

Ez a cikk ismerteti az identitás igazolási folyamat, amikor a szimmetrikus kulcsok használata a Device Provisioning Service. 

A szimmetrikus kulcsát a kulcsigazoláshoz egy egyszerű módja, ha a Device Provisioning Service-példánya egy eszköz hitelesítéséhez. Ez a metódus igazolási fejlesztőknek szól, akik az új eszközök üzembe helyezését, vagy nincsenek szigorú biztonsági követelmények egy "Hello world" élmény jelöli. Az eszköz állapotigazolási használ egy [TPM](concepts-tpm-attestation.md) vagy egy [X.509-tanúsítvány](concepts-security.md#x509-certificates) további, biztonságos, és szigorúbb biztonsági követelményeket kell használni.

Szimmetrikus kulcs regisztrációk is rendelkezhetnek nagyszerű örökölt eszközök korlátozott biztonsági funkciókkal, elindíthat a felhőbe az Azure IoT-n keresztül. Az örökölt eszközök szimmetrikus kulcsigazolás további információkért lásd: [szimmetrikus kulcsok használata az örökölt eszközök](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Szimmetrikus kulcs létrehozása

Alapértelmezés szerint a Device Provisioning Service hoz létre új szimmetrikus kulcsokat egy 32 bájt alapértelmezés szerint mennyi az mentésekor a rendszer új regisztrációk a **kulcsok automatikus létrehozása** beállítás engedélyezve van.

![Automatikus létrehozás szimmetrikus kulcsokat](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Ez a beállítás letiltásával regisztrációját a saját szimmetrikus kulcsokat is megadhatja. A saját szimmetrikus kulcsok megadása esetén a kulcsok a kulcs hossza 16 és 64 bájt között kell rendelkeznie. Szimmetrikus kulcsokat is, érvényes Base64 formátumban kell megadni.



## <a name="detailed-attestation-process"></a>Részletes igazolási folyamat

A Device Provisioning Service szolgáltatással szimmetrikus kulcsigazolás használatával ugyanez történik [biztonsági jogkivonatokat](../iot-hub/iot-hub-devguide-security.md#security-token-structure) azonosíthatja azokat az eszközöket az IoT hub által támogatott. Ezek a biztonsági jogkivonatok olyan [közös hozzáférésű Jogosultságkód (SAS) tokenek](../service-bus-messaging/service-bus-sas.md). 

SAS-tokeneket rendelkezik egy kivonatolt *aláírás* létrehozott szimmetrikus kulcs használatával. Az aláírás ellenőrzéséhez, hogy egy biztonsági jogkivonatot igazolás során bemutatott hiteles-e vagy sem a Device Provisioning Service által újból létrejön.

SAS-tokeneket rendelkezik a következő formátumot követi:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Az alábbiakban minden tokenhez összetevőinek:

| Érték | Leírás |
| --- | --- |
| {aláírás} |Egy HMAC-SHA256 aláírás karakterlánc. Az egyéni regisztrációk az aláírás elő a szimmetrikus kulcs (elsődleges vagy másodlagos) segítségével hajtsa végre a kivonatot. Regisztrációs csoportok esetén a regisztrációs csoport kulcs osztályból származtatott kulcs segítségével hajtsa végre a kivonatot. A kivonat történik, egy üzenet a következő formában: `URL-encoded-resourceURI + "\n" + expiry`. **Fontos**: A kulcs a HMAC-SHA256 végzéséhez használat előtt kell dekódolható a Base64 kódolású. Ezenkívül az aláírás eredmény URL-kódolású kell lennie. |
| {resourceURI} |A regisztrációs végpont az ezzel a tokennel elérhető URI kezdve a Device Provisioning Service-példány hatókör azonosítója. Például: `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |UTF8 karakterláncok az alapidőpont 00:00:00 (UTC), a 1970. január 1. óta eltelt másodpercek száma. |
| {URL-encoded-resourceURI} |Lower használatieset-URL-Címének kódolása a kisbetűs erőforrás URI azonosítója |
| {policyName} |Ez a token által hivatkozott megosztott elérési házirend neve. A szabályzat neve a szimmetrikus kulcs állapotigazolás üzembe helyezésekor használt **regisztrációs**. |

Ha egy eszköz van tanúsító az egyéni regisztrációt, az eszköz a kivonatolt aláírás az SAS-token létrehozásához használja az egyéni regisztrációs bejegyzés meghatározott szimmetrikus kulcs.

Hozzon létre egy SAS-token hitelesítésikód-példák, lásd: [biztonsági jogkivonatokat](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Az Azure IoT C SDK létrehozása-szimmetrikus kulcsát a kulcsigazoláshoz biztonsági jogkivonatokat támogatja. Az Azure IoT C SDK használatával az egyéni regisztrációt auditokkal egy példa: [szimulált eszköz kiépítése a szimmetrikus kulcsok](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Csoportos beléptetések

A szimmetrikus kulcsokat a csoportos beléptetések nem használja közvetlenül eszközök üzembe helyezésekor. Ehelyett eszközökhöz tartozó regisztrációs csoport üzembe helyezése egy eszköz származtatott kulcs használatával. 

Először egy egyedi regisztrációs azonosító minden eszközhöz egy regisztrációs csoport a tanúsító van meghatározva. A regisztrációs Azonosítót érvényes karakterek: alfanumerikus kisbetűt és kötőjelet ("-"). A regisztrációs Azonosítót lehet valami egyedivé, amely azonosítja az eszközt. Örökölt eszköz például előfordulhat, hogy támogatja a számos biztonsági funkció. Az örökölt eszköz csak akkor lehet szükség, egy MAC-cím vagy a sorozatszámot érhető el, hogy az eszköz egyedi azonosításához. Ebben az esetben a regisztrációs azonosító összeállítható a MAC-cím és a következőhöz hasonló sorozatszám:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Ez a példa pontos használatban van a [szimmetrikus kulcsok használata örökölt eszközök kiépítése](how-to-legacy-device-symm-key.md) cikk.

Miután egy regisztrációs azonosító definiálva van az eszközön, a regisztrációs csoport a szimmetrikus kulcs kiszámításához használt egy [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) kivonatát a regisztrációs azonosító egy származtatott eszközkulcs előállításához. A regisztrációs Azonosítót kivonatolás az alábbi C# kóddal hajthatja végre:

```C#
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

```C#
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Az eredményül kapott eszköz kulcsát majd szolgál egy SAS-token használható tanúsítvány létrehozásához. Minden eszköz egy regisztrációs csoportnak auditokkal származtatott egyedi kulcs alapján generált biztonsági jogkivonat használata szükséges. A regisztrációs csoport szimmetrikus kulcs nem használható közvetlenül igazolás.

#### <a name="installation-of-the-derived-device-key"></a>A származtatott eszközkulcs telepítése

Ideális esetben az eszköz kulcsok származtatott, és a gyári telepítve. Ez a módszer biztosítja, hogy a csoport kulcs soha nem szerepel az eszközre telepített minden szoftver. Amikor az eszköz egy MAC-cím vagy a sorozatszámot van hozzárendelve, a kulcs származtatott, és kártevő program férkőzik be az eszköz azonban a gyártó úgy dönt, hogy tárolja.

Vegye figyelembe a következő bemutató diagram, amelyek eszközkulcsok gyárból minden egyes eszköz regisztrációs Azonosítót a csoport a regisztrációs kulccsal kivonatolásával jön létre egy táblát (**K**). 

![A gyári hozzárendelt eszközkulcsok](./media/concepts-symmetric-key-attestation/key-diversification.png)

Minden egyes eszköz identitását a regisztrációs azonosító és a gyári telepített származtatott eszközkulcs képviseli. Az eszköz kulcs soha nem másolja egy másik helyre, és a csoport kulcsot soha nem tárolja az eszközön.

Ha az eszköz kulcsok nincsenek telepítve az előállító egy [hardveres biztonsági modul HSM](concepts-security.md#hardware-security-module) segítségével biztonságosan tárolja az eszközidentitást.

## <a name="next-steps"></a>További lépések

Most, hogy a szimmetrikus kulcs állapotigazolási megismerése, tekintse meg a következő cikkekben további információ:

* [Rövid útmutató: Szimulált eszköz kiépítése a szimmetrikus kulcsok](quick-create-simulated-device-symm-key.md)
* [További információ az Automatikus kiépítés fogalmak](./concepts-auto-provisioning.md)
* [Automatikus kiépítés használatának első lépései](./quick-setup-auto-provision.md) 

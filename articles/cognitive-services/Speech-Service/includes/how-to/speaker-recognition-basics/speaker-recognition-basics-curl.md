---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3a32946e10e8a81394b54fc44e4c92e8625e7ad6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015476"
---
Ebben a rövid útmutatóban megtudhatja, milyen alapszintű tervezési mintákat Speaker Recognition a Speech SDK használatával, beleértve a következőket:

* Szövegtől függő és szöveg-független ellenőrzés
* Beszélő azonosítása a hangminták azonosításához a hangminta
* Hangprofilok törlése

A beszédfelismeréssel kapcsolatos fogalmak [áttekintését az áttekintő](../../../speaker-recognition-overview.md) cikkben tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> A Speaker Recognition jelenleg *csak* a régióban létrehozott Azure Speech-erőforrások esetében támogatott `westus` .

## <a name="text-dependent-verification"></a>Szövegtől függő ellenőrzés

Speaker Verification az a szabály, amely megerősíti, hogy egy beszélő ismert vagy **regisztrált** hangra illeszkedik. Az első **lépés a** hangprofil beléptetése, hogy a szolgáltatásnak van-e összehasonlítani a jövőbeli hangmintákkal. Ebben a példában egy **szövegtől függő** stratégia használatával regisztrálja a profilt, amelyhez a regisztrációhoz és ellenőrzéshez egy adott jelszó szükséges. A támogatott hozzáférési kódok listáját a [dokumentációs](/rest/api/speakerrecognition/) dokumentációban tekintheti meg.

Első lépésként [hozzon létre egy hangprofilt](/rest/api/speakerrecognition/verification/textdependent/createprofile). A beszédfelismerési szolgáltatás előfizetési kulcsát és régióját be kell szúrnia a jelen cikkben szereplő összes curl-parancsba.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Vegye figyelembe, hogy háromféle hangprofil létezik:

- Szövegtől függő ellenőrzés
- Szövegtől független ellenőrzés
- Szöveg – független azonosítás

Ebben az esetben egy szövegtől függő ellenőrző hangprofilt hoz létre. A következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Ezután [regisztrálja a hangprofilt](/rest/api/speakerrecognition/verification/textdependent/createenrollment). A `--data-binary` paraméter értékeként olyan hangfájlt kell megadnia a számítógépen, amely a támogatott hozzáférési kódok valamelyikét tartalmazza, például a "saját hang a Passporton Rögzíthet egy ilyen hangfájlt egy alkalmazással, például a [Windows Hangrögzítővel](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab), vagy [szöveges – beszéd](../../../index-text-to-speech.yml)használatával is létrehozhatja.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

A következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Ez a válasz azt jelzi, hogy két további hangmintát kell regisztrálnia.

Miután regisztrálta az összesen három hangmintát, a következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Most már készen áll [egy hangminta ellenőrzésére a hangprofilban](/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Ez a hangminta ugyanazt a jelszót tartalmazza, mint a hangprofil regisztrálásához használt minták.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

A következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

Az `Accept` azt jelenti, hogy a jelszó egyeztetve lett, és az ellenőrzés sikerült. A válasz szintén tartalmaz egy hasonlósági pontszámot a 0,0-1.0-s verziótól kezdve.

A befejezéshez [törölje a hangprofilt](/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

Nincs válasz.

## <a name="text-independent-verification"></a>Szövegtől független ellenőrzés

A **szövegtől függő** hitelesítéssel szemben a **szövegtől független** ellenőrzés:

* Nincs szükség egy bizonyos jelszó megírására, amit mondanak
* A nem igényel három hangmintát, *de a* teljes hang 20 másodpercet igényel

Először [hozzon létre egy szövegtől független ellenőrzési profilt](/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

A következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

Ezután [regisztrálja a hangprofilt](/rest/api/speakerrecognition/verification/textindependent/createenrollment). A három hangminta elküldése helyett újra kell küldenie a hangmintákat, amelyek összesen 20 másodperces hangot tartalmaznak.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Miután elküldte a megfelelő hangmintákat, a következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Most már készen áll [egy hangminta ellenőrzésére a hangprofilban](/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Ez a hangminta nem feltétlenül tartalmaz jelszót. Bármilyen beszédet tartalmazhat, feltéve, hogy összesen legalább négy másodperces hangot tartalmaz.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

A következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

Ez `Accept` azt jelenti, hogy az ellenőrzés sikeres volt. A válasz szintén tartalmaz egy hasonlósági pontszámot a 0,0-1.0-s verziótól kezdve.

A befejezéshez [törölje a hangprofilt](/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

Nincs válasz.

## <a name="speaker-identification"></a>Beszélőazonosítás

A Speaker Identification segítségével megállapítható, hogy **ki** beszél a regisztrált hangok adott csoportjából. A folyamat hasonló a **szövegtől független ellenőrzéshez**, és a fő különbség, hogy egyszerre több hangprofillal is képes ellenőrizni, nem pedig egyetlen profilon.

Első lépésként [hozzon létre egy szöveg-független azonosító profilt](/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

A következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Ezután [regisztrálja a hangprofilt](/rest/api/speakerrecognition/identification/textindependent/createenrollment). Újra be kell állítania a hangmintákat, amelyek összesen 20 másodperces hangot tartalmaznak. Ezeknek a mintáknak nem kell jelszót tartalmazniuk.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Miután elküldte a megfelelő hangmintákat, a következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Most már készen áll [egy hangminta azonosítására a hangprofil használatával](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). Az azonosítás parancs fogadja a lehetséges Hangprofilok azonosítóinak vesszővel tagolt listáját. Ebben az esetben csak a korábban létrehozott hangprofil AZONOSÍTÓját kell átadnia. Ha azonban azt szeretné, több hangprofil-azonosító is átadható, ahol minden hangprofil regisztrálva van hangmintákkal egy másik hangon.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

A következő választ kell kapnia.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

A válasz tartalmazza a hangprofil AZONOSÍTÓját, amely a leginkább megfelel az Ön által elküldött hangmintának. Emellett a jelölt Hangprofilok listáját is tartalmazza, a hasonlósági sorrendben rangsorolva.

A befejezéshez [törölje a hangprofilt](/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

Nincs válasz.
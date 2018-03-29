---
title: IoT Hub eszköz létesítési szolgáltatás – Automatikus kiépítés fogalmak
description: Ez a cikk általános áttekintést nyújt az eszközök automatikus-kiépítését, IoT eszköz kiépítése szolgáltatáshoz, az IoT-központ és az ügyfél-SDK-szakaszainak.
services: iot-dps
keywords: ''
author: BryanLa
ms.author: bryanla
ms.date: 03/27/2018
ms.topic: conceptual
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: cd458b1f6d26fbd5f5821a04cd01be5c3a4e4514
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="auto-provisioning-concepts"></a>Automatikus kiépítés fogalmak

Lásd: a [áttekintése](about-iot-dps.md), az eszköz kiépítése szolgáltatáshoz egy segítő szolgáltatás, amely lehetővé teszi, hogy az IoT-központ eszközök just-in-time kiépítése emberi beavatkozás nélkül. Sikeres kiépítése után az eszközök közvetlenül a kijelölt IoT hubbal csatlakoznak. Ezt a folyamatot nevezzük automatikus kiépítés, és biztosítja az eszközök állást egy out-of-az-box regisztrálása és a kezdeti konfigurálása.

## <a name="overview"></a>Áttekintés

Az Azure IoT automatikus átadásának is kell megadni, három fázisra osztható:

1. **Szolgáltatáskonfiguráció** -egyszeri konfigurálása az Azure IoT-központ és az IoT Hub eszköz kiépítése szolgáltatáshoz példányok a létrehozó őket, és azok közötti kapcsolat létrehozásához.

   > [!NOTE]
   > Az IoT-megoldásból méretétől függetlenül akkor is, ha azt tervezi, hogy az eszközök több millió azt egy **egyszeri**.

2. **Az eszközregisztráció** -a folyamat, amellyel az eszközöket, amelyek megkísérli regisztrálni a jövőben tudomást az eszköz kiépítése szolgáltatáspéldány. Regisztrációs eszköz azonosító adatok konfigurálása a létesítési szolgáltatás, vagy az "egyedi tagság" egyetlen eszközhöz, vagy a "csoport"eszközök regisztrációja a több úgy érhető el. Identitás alapul a [állapotigazolási mechanizmus](concepts-security.md#attestation-mechanism) az eszköz célja, hogy használja, amely lehetővé teszi, hogy a létesítési szolgáltatás igazolják az eszköz eredetiséget való regisztráció során:

   - **TPM**: konfigurált "különálló regisztrációja", az eszközidentitást alapul a TPM-regisztrációs Azonosítót és a nyilvános kulcsa. Fényében, hogy a TPM egy [specification]((https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)), a szolgáltatás csak a meghatározás (hardveres vagy szoftveres) TPM kivitelezéstől függetlenül minden igazolják vár. Lásd: [eszközök kiépítését: identitás igazolás a platformmegbízhatósági Modullal](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) részleteinek a TPM-alapú tanúsítvány. 

   - **X509**: konfigurált "különálló regisztrációja" vagy "csoport regisztrációja", az eszközidentitást alapuló X.509 digitális tanúsítvány, amely fel van töltve a regisztráció .pem vagy .cer-fájlként.

   > [!IMPORTANT]  
   > Bár nem előfeltétele az eszköz kiépítése Services, javasoljuk, hogy eszköz használata hardveres biztonsági modul (HSM) bizalmas eszköz azonosító adatok, például X.509 tanúsítványok és kulcsok tárolására.

3. **Eszköz regisztrálása és konfigurálása** - regisztrációs szoftvereket, beépített eszköz kiépítése szolgáltatáshoz ügyfél SDK megfelelő az eszköz- és állapotigazolási mechanizmus használatával kezdeményezett rendszerindítás során. A szoftver kapcsolatot hoz létre a létesítési szolgáltatás hitelesítéshez, az eszköz és a későbbi regisztrációs az IoT-központ a. Sikeres regisztráláskor az eszközt és annak az IoT-központ az eszköz egyedi azonosítója és a kapcsolat adatait, lehetővé téve lekéréses kezdeti konfigurációját és a telemetriai adatok megkezdéséhez valósul meg. Éles környezetben a ebben a fázisban akkor fordulhat elő, hét és hónap után az előző két szakaszra.

## <a name="roles-and-operations"></a>Szerepkörök és műveletek

A fázisok az előző szakaszban ismertetett is kiterjedhetnek hét és hónap, miatt éles valóságok felfedezését gyártási időt, például szállítási, vámügyi folyamat stb. Emellett akkor is kiterjedhet a tevékenységek között a különböző vevő entitásokat megadott több szerepkört. Ez a szakasz mélyebb áttekinti a különböző szerepkörök és az egyes fázisok kapcsolatos műveletek, majd Szekvenciadiagram folyamatot szemlélteti. 

Az eszköz gyártójához, a jellemző engedélyezése a igazolási módszert is automatikus kiépítés követelményeinek helyezi. Gyártási műveletek akkor is előfordulhat automatikus kiépítés fázisok, különösen azokban az esetekben, ahol új eszközök automatikus kiépítés már létrejötte után gyűjtött időzítése független.

Quickstarts sorozata balra, a gyakorlati tapasztalatok automatikus kiépítés magyarázatának elősegítésére a tartalomjegyzék szerepelnek. Ahhoz, hogy megkönnyítése/folyamat egyszerűsítése érdekében a tanulási szoftver szimulálására szolgál egy fizikai eszköz beléptetési és nyilvántartási. Néhány Quickstarts megkövetelik a több szerepkört is, beleértve a nem létező szerepkörök, a Quickstarts szimulált jellemzői miatt műveleteket műveletek végrehajtását.

| Szerepkör | Művelet | Leírás | 
|------| --------- | ------------| 
| Gyártó | Identitás- és a regisztrációs URL-cím kódolása | Az igazolás mechanizmus, amely alapján, a gyártó felelős kódolást az eszközinformáció identitás, eszköz kiépítése szolgáltatáshoz regisztrációs URL-CÍMÉT.<br><br>**– Gyorsútmutatók**: mivel az eszköz szimulálják, gyártó Role szerepkör nincs. A fejlesztői szerepkör a részletekért tekintse meg ezt az információt, a regisztrációs mintaalkalmazás kódolási módjától. |  
| | Adja meg az eszközidentitás | A megbízó eszköz azonosító adatok, mint a gyártó felelős a kommunikációt a következő operátor (vagy a kijelölt ügynökök), vagy közvetlenül regisztrál az API-k segítségével az eszköz kiépítése szolgáltatáshoz.<br><br>**– Gyorsútmutatók**: mivel az eszköz szimulálják, gyártó Role szerepkör nincs. Az operátor szerepkör a részletekért tekintse meg az eszközidentitást, amely az eszköz kiépítése szolgáltatáspéldány a szimulált eszköz beléptetése módjától. | 
| Operátor | Konfigurálja az Automatikus kiépítés | Ez a művelet megfelel-e az Automatikus kiépítés első fázisa.<br><br>**– Gyorsútmutatók**: elvégzi operátori szerepkör konfigurálása az eszköz kiépítése szolgáltatáshoz, és az Azure-előfizetés IoT-központ példánya. | Telepítés automatikusan-kiépítés |
|  | Az eszközazonosító regisztrálása | Ez a művelet megfelel-e az Automatikus kiépítés második fázisa.<br><br>**– Gyorsútmutatók**: operátori szerepkör elvégezhető, a szimulált eszköz regisztrálásának eszköz kiépítése szolgáltatáshoz példányában. Az eszközidentitást (TPM vagy X.509) a gyorsindító alatt szimulált igazolás metódus határozza meg. A fejlesztői szerepkör igazolás a részletekért tekintse meg. | 
| Eszköz létesítési szolgáltatás,<br>IoT Hub | \<összes művelet\> | Mindkét egy éles megvalósítást fizikai eszközöket, és Quickstarts szimulált eszközökkel ezek a szerepkörök teljesülnek az IoT-szolgáltatásaival, az Azure-előfizetéshez konfigurált keresztül. A szerepkörök/műveletek működnek ugyanúgy, mert az IoT-szolgáltatásaival a fizikai és a szimulált eszköz kiépítése közömbös. | 
| Fejlesztői | Build/telepítés regisztrációs szoftver | Ez a művelet a harmadik szakasz automatikus kiépítés megfelel. A fejlesztői felelős kialakításához, és a regisztrációs szoftverek telepítését az eszközre, a megfelelő SDK használatával.<br><br>**– Gyorsútmutatók**: A regisztrációs mintaalkalmazás készít egy valódi eszközt, a platform/nyelvének megfelelő, a munkaállomáson (helyett egy fizikai eszközön való üzembe helyezés akkor) futtató szimulálja. A regisztrációs alkalmazás ugyanazokat a műveleteket, mint egy fizikai az eszközökre alkalmazott hajt végre. Az igazolás metódus (TPM vagy X.509 tanúsítvány), és a regisztrációs URL-cím és az eszköz kiépítése szolgáltatáspéldány "azonosító hatókörén" adja meg. Az eszközidentitást az SDK igazolás logikai futásidőben, a megadott metódus alapján határozza meg: <ul><li>**TPM-igazolási** -a fejlesztő munkaállomás fut egy [TPM szimulátor alkalmazás](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Ha fut, egy különálló alkalmazás használatával olvasható ki a TPM "Ellenőrzőkulccsal" és "Regisztrációs azonosítója" használatra az eszközidentitást regisztrálása a. Az SDK-igazolási logikai is használja a szimulátor alatt a regisztrációt egy aláírt SAS-jogkivonat hitelesítési és a regisztráció ellenőrzése.</li><li>**X509 állapotigazolási** -eszközt használ [hozzon létre tanúsítványt](how-to-use-sdk-tools.md#x509-certificate-generator). Miután létrehozott, hozzon létre a tanúsítványfájlt, a regisztráció szükséges. Az SDK-igazolási logikai is használja a tanúsítvány alatt a regisztrációt a hitelesítés és a regisztráció ellenőrzése.</li></ul> | 
| Eszköz | Rendszerindítási és regisztrálása | A harmadik szakasz automatikus kiépítés, megfelel-e ez a művelet által a fejlesztői eszközök regisztrációs szoftver által teljesülnek. Tekintse meg a részleteket a fejlesztői szerepkör. Első elinduljon: <ol><li>Az eszköz kiépítése szolgáltatáspéldány, a globális URL-cím és a fejlesztés során megadott "azonosító hatókör" szolgáltatás kapcsolódik az alkalmazás.</li><li>A csatlakozás után az eszköz hitelesítése a igazolási módszert és a regisztráció során megadott identitás szemben.</li><li>Ha hitelesítése megtörtént, az eszköz regisztrálva van a megadott kiépítési szolgáltatáspéldány IoT Hub-példány.</li><li>Sikeres regisztráció esetén egy eszköz egyedi azonosító és az IoT-központ végpontjának visszatér a bejegyzési kérelem IoT-központ való kommunikációhoz.</li><li> Ott, az eszköz az első is nyilvántartani [eszköz iker](~/articles/iot-hub/iot-hub-devguide-device-twins.md) konfigurációs állapotát, és a "telemetrikus" adatok reporting megkezdéséhez.</li></ol>**– Gyorsútmutatók**: az eszköz szimulálják, mert a regisztrációs szoftver a fejlesztő munkaállomás fut-e.| 

Az alábbi ábra a szerepkörök és alkalmazás-előkészítő műveletek során az eszköz automatikus átadásának foglalja össze:
<br><br>
![Automatikus kiépítés feladatütemezési eszköz](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png) 

> [!NOTE]
> Ha szükséges, a gyártó is végrehajthat eszköz kiépítése szolgáltatáshoz API-val "kapcsolatos igénylési eszközidentitás" művelet (ahelyett, hogy a következő operátor keresztül). Az alkalmazás-előkészítés és további részletes tárgyalását lásd: a [nulla touch eszközregisztráció, az Azure IoT videó](https://myignite.microsoft.com/sessions/55087) (jelölő 41:00 kezdődően)

## <a name="next-steps"></a>További lépések

Akkor lehet hasznos lehet könyvjelzőt tenni az ebben a cikkben hivatkozási pontként munka a módja a megfelelő automatikus kiépítés Quickstarts keresztül. 

Kezdje a "Beállítása automatikus kiépítés" gyors üzembe helyezés, amely a legjobban megfelel a felügyeleti eszköz igény szerint, amely végigvezeti a "Service-konfiguráció" fázis befejezése:

- [Állítsa be automatikus átadásának Azure parancssori felület használatával](quick-setup-auto-provision-cli.md)
- [Állítsa be automatikus átadásának az Azure portál használatával](quick-setup-auto-provision.md)
- [Állítsa be automatikus átadásának Resource Manager-sablon használatával](quick-setup-auto-provision-rm.md)

Majd folytassa az "Auto-provision a szimulált eszköz" gyors üzembe helyezés, amely megfelel az eszköz állapotigazolási mechanizmus és az eszköz kiépítése szolgáltatáshoz SDK/nyelvi beállításai. A gyors üzembe helyezés, az Ön végezze el az "Eszközök regisztrációja" és "eszköz regisztrálása és konfigurálása" fázisok: 

|  | Szimulált eszköz állapotigazolási mechanizmus | Gyors üzembe helyezési SDK/nyelv |  |
|--|--|--|--|
|  | Platformmegbízhatósági modul (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509-tanúsítvány | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |





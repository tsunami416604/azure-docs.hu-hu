---
title: Az IoT Hub Device Provisioning Service - automatikus kiépítés alapfogalmait
description: Ez a cikk az eszköz automatikus kiépítés, az IoT Device Provisioning Service, az IoT Hub és ügyféloldali SDK-k fázisai fogalmi áttekintése.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 0df4eb664accd828c47d834fb0014d0d60f57458
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051733"
---
# <a name="auto-provisioning-concepts"></a>Automatikus kiépítés alapfogalmait

Leírtak szerint a [áttekintése](about-iot-dps.md), a Device Provisioning Service, segédszolgáltatás, amely lehetővé teszi az IoT hub-eszközök just-in-time kiépítését emberi beavatkozás nélkül. A sikeres kiépítés után az eszközök közvetlenül a kijelölt az IoT Hub használatának csatlakoznak. Ezt a folyamatot nevezzük automatikus kiépítés, és a egy-az-beépített regisztráció és a kezdeti konfigurációs élmény a eszközöket biztosít.

## <a name="overview"></a>Áttekintés

Az Azure IoT automatikus kiépítés elválasztható három fázisra osztható:

1. **Szolgáltatás konfigurációjának** – az Azure IoT Hub és az IoT Hub Device Provisioning Service-példányokhoz hasonlóan létrehozó őket, és ezek között kapcsolat létrehozása egy egyszeri konfigurációját.

   > [!NOTE]
   > Az IoT-megoldás bármekkora, még akkor is, ha azt tervezi, hogy több millió eszköz támogatja ez az egy **egyszeri**.

2. **Eszközregisztráció** – a folyamat az, hogy az eszközök, amelyeket a rendszer próbál meg regisztrálni a jövőben tisztában a Device Provisioning Service-példány. [Regisztráció](concepts-service.md#enrollment) konfigurálásával érhető el eszköz azonosító adatok a kiépítési szolgáltatásba, vagy egy adott eszköz "egyéni regisztrációt", vagy a "csoport"-eszközök regisztrációja a több mint. Identitás alapján a [igazolási mechanizmus](concepts-security.md#attestation-mechanism) az eszköz célja, hogy használja, amely lehetővé teszi, hogy igazolja eredetiséget az eszköz regisztrációja során a kiépítési szolgáltatás:

   - **A TPM**: "egyéni regisztrációt" konfigurálva, az eszközidentitást alapján a TPM-eszköz regisztrációs azonosító és a nyilvános kulcsa. Tekintve, hogy a TPM- [specifikáció](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/), a szolgáltatás csak a specifikációi szerint történik, függetlenül a TPM-implementáció (hardveres vagy szoftveres) auditokkal vár. Lásd: [eszközök üzembe helyezését: A TPM-hez identitásigazolási](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) TPM-alapú igazolás részleteiért. 

   - **X509**: "egyéni regisztráció" vagy "Csoportos regisztráció" konfigurálva, az eszközidentitást alapján egy X.509 digitális tanúsítvány, amely fel van töltve a regisztráció .pem vagy .cer-fájlként.

   > [!IMPORTANT]  
   > Bár nem szükséges előfeltétel a Device Provisioning Service használatával, Határozottan javasoljuk, hogy az eszköz használatának egy hardveres biztonsági modul (HSM) bizalmas eszköz azonosító adatok, például az X.509 tanúsítványok és kulcsok tárolására.

3. **Eszköz regisztrálása és konfigurálása** - regisztrációs szoftver, amely a Device Provisioning Service ügyféloldali SDK-val az eszköz- és igazolási mechanizmus megfelelő beépített által kezdeményezett rendszerindítás esetén. A szoftver az eszköz és a későbbi regisztrációs a hitelesítéshez a kiépítési szolgáltatás kapcsolatot létesít az IoT hubban. Sikeres regisztráció esetén az eszköz és annak az IoT Hub eszköz egyedi azonosítója, és a kapcsolat adatait, lehetővé téve, hogy kérje le a kezdeti konfigurációját, és a telemetria megkezdéséhez van megadva. Éles környezetben a ebben a fázisban akkor fordulhat elő, hét vagy hónapon belül az előző két fázisban történik.

## <a name="roles-and-operations"></a>Szerepkörök és műveletek

Az előző szakaszban tárgyalt fázisai is kiterjedhet hetek vagy hónapok, miatt éles valóságok felfedezését gyártási idő, például a szállítási vámügyi folyamat, és így tovább. Emellett akkor is kiterjedhet a tevékenységek között megadott részt vevő különféle entitásokat több szerepkört. Ebben a szakaszban a különféle szerepkörök és az egyes fázisok kapcsolódó műveletek alaposabban vesz igénybe, majd azt a folyamatot Szekvenciadiagram mutatja be. 

Az eszköz gyártója, jellemző engedélyezése az igazolási mechanizmust is automatikus kiépítés követelmények helyezi. Gyártási műveletek is fordulhat elő, az Automatikus kiépítés fázisokra, különösen azokban az esetekben, ahol az új eszközök gyűjtött után az Automatikus kiépítés már létrejött időzítése független.

Rövid útmutatók sorozatát szerepelnek a tartalomjegyzékből a bal oldalán, az Automatikus kiépítés révén gyakorlati tapasztalatokat magyarázatának elősegítésére. Annak érdekében, hogy a tanulási folyamat megkönnyítése és egyszerűbb, szoftverek beléptetéséhez és regisztrálásához a fizikai eszköz szimulálása használhatók. Néhány rövid útmutatók szükséges teljesítése több szerepkört is, beleértve a nem létező szerepkörök, a rövid útmutató szimulált jellege miatt műveleteket műveletek.

| Szerepkör | Művelet | Leírás |
|------| --------- | ------------|
| Gyártó | Identitás és a regisztrációs URL-Címének kódolása | A használt igazolási eljárás alapján, a gyártó felelős az identitás eszközinformáció kódolást, a Device Provisioning Service-regisztrációs URL-címe.<br><br>**Rövid útmutatók**: az eszköz szimulált van, mivel gyártó Role szerepkör nincs. Tekintse meg a fejlesztői szerepkör részletes hogyan juthat ezt az információt, a kódolási egy alkalmazást. |
| | Adja meg az eszközidentitás | Az identitás eszközinformáció létrehozója, mint a gyártó felelős kommunikál az operátor (vagy egy kijelölt ügynököt), vagy közvetlenül a Device Provisioning Service API-kon keresztül regisztrálja azt.<br><br>**Rövid útmutatók**: az eszköz szimulált van, mivel gyártó Role szerepkör nincs. Tekintse meg az operátori szerepkör részletes hogyan juthat az eszközidentitás, amelyet regisztrálni egy szimulált eszközt a Device Provisioning Service-példányában. |
| Művelet | Automatikus kiépítés beállítása | Ez a művelet az első fázisa az Automatikus kiépítés felel meg.<br><br>**Rövid útmutatók**: Az operátor szerepkör végezhet, a Device Provisioning Service és az IoT Hub-példányok konfigurálása az Azure-előfizetésében. |
|  | Eszközidentitás regisztrálása | Ez a művelet az Automatikus kiépítés második szakaszának felel meg.<br><br>**Rövid útmutatók**: Az operátor szerepkörrel végezhet, a szimulált eszköz regisztrálását a Device Provisioning Service-példányában. Az eszközidentitást az állapotigazolási módszer a rövid útmutatóban (TPM vagy X.509) alatt szimulált határozza meg. Tekintse meg a fejlesztői szerepkör állapotigazolási részleteiről. |
| Eszközök kiépítési szolgáltatáshoz<br>IoT Hub | \<minden művelet\> | A fizikai eszközöket is a termelési implementációt, és a szimulált eszközök gyors útmutatók ezek a szerepkörök teljesülnek keresztül az IoT-szolgáltatások konfigurálása az Azure-előfizetésében. A szerepköröket, illetve az operations pontosan ugyanúgy működnek, mivel közömbös a fizikai vagy szimulált eszközök kiépítése az IoT-szolgáltatások. |
| Fejlesztői | Buildelési és üzembe helyezés eszközregisztrációs szoftver | Ez a művelet az Automatikus kiépítés a harmadik szakasz felel meg. A fejlesztő feladata létrehozásához és az eszközregisztrációs szoftver telepítéséhez az eszköznek a megfelelő SDK-val.<br><br>**Rövid útmutatók**: Az alkalmazást hoz létre egy igazi eszközön, a platform/nyelvével választási lehetősége, amely a munkaállomáson (való üzembe helyezése egy fizikai eszköz) nem fut, szimulálja. A regisztrációs alkalmazást egy fizikai eszközön üzembe helyezett ugyanazokat a műveleteket hajt végre. Az igazolás metódus (TPM vagy X.509 tanúsítvány), továbbá a regisztrációs URL-címe és a Device Provisioning Service-példány "azonosító hatóköre" adja meg. Az eszközidentitást az SDK igazolási logika futásidőben, a megadott metódus alapján határozzák meg: <ul><li>**TPM-igazolást** – a fejlesztői munkaállomáson fut egy [TPM-szimulátor alkalmazás](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Ha fut, bontsa ki a TPM-et "Ellenőrzőkulcsot" és "Regisztrációs azonosító" használatra az eszközidentitás regisztrálását a külön kérelmet szolgál. Az SDK-állapotigazolási logika is használja a szimulátor regisztrációhoz, hitelesítési és regisztrációs ellenőrzési aláírt SAS-jogkivonatát.</li><li>**X509 igazolási** -eszközt használ [hozzon létre tanúsítványt](how-to-use-sdk-tools.md#x509-certificate-generator). Miután jönnek létre, a tanúsítványfájlt, a beléptetési használandó hozzon létre. Az SDK-állapotigazolási logika is használja a tanúsítvány regisztrációhoz, hitelesítési és regisztrációs ellenőrzése.</li></ul> |
| Eszköz | Rendszerindítási és regisztrálása | Automatikus kiépítés, a harmadik szakasz megfelel-e ez a művelet által az eszközregisztrációs szoftver fejlesztője által készített teljesülnek. Tekintse meg a fejlesztői szerepkör részleteiről. Esetén az első rendszerindításkor: <ol><li>Az alkalmazás kapcsolódik a Device Provisioning Service-példánnyal, a globális URL-cím és a szolgáltatás "Azonosító hatóköre" fejlesztés során megadott száma.</li><li>A csatlakozás után az eszköz hitelesítése a metódus igazolási és a regisztráció során megadott identitás szemben.</li><li>A hitelesítést követően az eszköz regisztrációja az IoT Hub-példány a kiépítési szolgáltatás-példánya által megadott.</li><li>Sikeres regisztráció esetén egy egyedi azonosító és az IoT Hub eszközvégpont visszatér a regisztrációs alkalmazást az IoT hubbal való kommunikációhoz.</li><li> Itt az eszköz kezdeti is lekéri [ikereszköz](~/articles/iot-hub/iot-hub-devguide-device-twins.md) konfigurációs állapotát, és kezdje meg a reporting telemetriai adatokat.</li></ol>**Rövid útmutatók**: az eszköz szimulált van, mivel az eszközregisztrációs szoftver a fejlesztői munkaállomáson fut-e.|

A következő ábra összefoglalja a szerepkörök és az alkalmazás-előkészítő műveletek eszköz automatikus kiépítés során:
<br><br>
[![Automatikus kiépítés feladatütemezési eszköz](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Ha szeretné, a gyártó is elvégezheti a Device Provisioning Service API-val "kapcsolatos igénylési eszközidentitás" műveletet (helyett az operátor keresztül). Az alkalmazás-előkészítés és további részletes tárgyalását lásd: a [nulla touch eszközregisztráció, az Azure IoT-videó](https://youtu.be/cSbDRNg72cU?t=2460) (jelölő 41:00-gyel kezdődik)

## <a name="roles-and-azure-accounts"></a>Szerepkörök és az Azure-fiókok

Hogyan egyes szerepkörök egy Azure-fiókra van leképezve a forgatókönyv-függő, és több forgatókönyv, amely tartozhat. Az alábbi gyakori minták webhelyünkre adjon meg egy általános ismertetése, hogyan szerepkörök általában leképezve egy Azure-fiókkal kapcsolatban.

#### <a name="chip-manufacturer-provides-security-services"></a>Lapka gyártójától biztonsági szolgáltatásokat biztosít.

Ebben a forgatókönyvben a gyártó adatbiztonságot első szintű ügyfelek számára. Ebben a forgatókönyvben ezen első szintű ügyfelek előfordulhat, hogy elsődleges, nem rendelkeznek részletes biztonság kezeléséhez. 

A gyártó biztonsági hardveres biztonsági modulokban (HSM) vezet. A biztonsági kulcsok, a tanúsítványok és az egyéb lekérését a lehetséges ügyfelek, akik már rendelkeznek a DPS példány és a regisztrációs csoportok beállítása a gyártó is tartalmazhatnak. A gyártó generálhat ügyfeleinek biztonsági információit.

Ebben a forgatókönyvben lehet két Azure-fiókok jár:

- **Fiók #1**: A kezelő és a fejlesztői szerepköröket bizonyos mértékű valószínűleg osztva. A entitás a HSM modulok vásárolhatnak a gyártó. Ezek a modulok a számla 1 társított DPS példány vannak mutatott. DPS regisztrációk, az a entitás az eszköz regisztrációs beállítások a DPS Szolgáltatásban újrakonfigurálásával bérbe több második szintű ügyfél-eszközök. Az entitás is IoT-központok lefoglalt végfelhasználói háttérrendszerekhez való eléréséhez a eszköztelemetria stb. Ez utóbbi esetben egy másik fiókot nem lehet szükség.

- **Fiók #2**: Végfelhasználók, második szintű előfordulhat, hogy a saját IoT-központokat. A számla 1 csak pontok hozzárendelve fogadó fél bérelt rendelkező eszközök helyes-e a fiók. Ez a konfiguráció szükséges csatolása a DPS és az IoT hubok között az Azure-fiókokat, amelyek az Azure Resource Manager-sablonokkal teheti meg.

#### <a name="all-in-one-oem"></a>Teljes körű OEM

A gyártó "Teljes körű OEM" lehet, ahol csak egyetlen gyártó fiók van szükség. A gyártó a biztonság és teljes körű kiépítés kezeli.

A gyártó eszközök megvásárló ügyfeleinknek egy felhőalapú alkalmazás rendelkezhetnek. Ez az alkalmazás és az IoT Hub, a gyártó által lefoglalt lenne illesztő.

Eladóautomaták vagy automatizált kávé gépek jelentik a példák az ehhez a forgatókönyvhöz.




## <a name="next-steps"></a>További lépések

Akkor lehet, hogy hasznos a könyvjelzők közé ebben a cikkben, hivatkozási pontként végzett munka végig a megfelelő automatikus kiépítés rövid útmutatók. 

Első lépésként egy "Automatikus kiépítés beállítása" rövid útmutató, amely a legjobban megfelel az eszköz a "Service-konfiguráció" szakasz végigvezeti felügyeleti szabályozó:

- [Állítsa be az Automatikus kiépítés az Azure CLI használatával](quick-setup-auto-provision-cli.md)
- [Állítsa be az Automatikus kiépítés az Azure portal használatával](quick-setup-auto-provision.md)
- [Automatikus kiépítés használata a Resource Manager-sablon beállítása](quick-setup-auto-provision-rm.md)

Majd folytassa az "Automatikus üzembe egy szimulált eszköz" a rövid útmutató, amely a megfelel az eszköz igazolási mechanizmus és a Device Provisioning Service-SDK/nyelvi beállításokat. Ebben a rövid útmutatóban vezeti végig az "Eszközök regisztrációja" és "eszköz regisztrálása és konfigurálása" fázisok: 

|  | Szimulált eszköz igazolási mechanizmus | A rövid útmutató SDK-t és nyelven |  |
|--|--|--|--|
|  | Platformmegbízhatósági modul (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509-tanúsítvány | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |





---
title: IoT Hub-eszközkiépítési szolgáltatás – Automatikus kiépítési fogalmak
description: Ez a cikk az eszközök automatikus kiépítésének fázisait ismerteti, az IoT-eszközlétesítési szolgáltatás (DPS), az IoT Hub és az ügyfél SDK-k használatával.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c94fa6b851dfc9923628a738a15f7c245204f73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975329"
---
# <a name="auto-provisioning-concepts"></a>Automatikus kiépítési fogalmak

Az áttekintés ben [leírtak szerint](about-iot-dps.md)az eszközkiépítési szolgáltatás egy segítő szolgáltatás, amely lehetővé teszi az eszközök just-in-time kiépítése egy IoT hub, emberi beavatkozás nélkül. Sikeres üzembe építés után az eszközök közvetlenül csatlakoznak a kijelölt IoT Hubhoz. Ezt a folyamatot automatikus kiépítésnek nevezzük, és beépített regisztrációt és kezdeti konfigurációs élményt biztosít az eszközökszámára.

## <a name="overview"></a>Áttekintés

Az Azure IoT automatikus kiépítése három szakaszra bontható:

1. **Szolgáltatáskonfiguráció** – az Azure IoT Hub és az IoT Hub-eszközkiépítési szolgáltatás példányainak egyszeri konfigurálása, létrehozásuk és közötti kapcsolat létrehozása.

   > [!NOTE]
   > Az IoT-megoldás méretétől függetlenül, még akkor is, ha több millió eszközt kíván támogatni, ez egy **egyszeri konfiguráció.**

2. **Eszközregisztráció** – az a folyamat, amelynek során az eszközkiépítési szolgáltatás példánya tisztában van azeszközök, amelyek a jövőben megpróbálnak regisztrálni. [A regisztráció](concepts-service.md#enrollment) a kiépítési szolgáltatásban az eszköz identitásadatainak konfigurálásával történik, mint egy "egyéni regisztráció" egyetlen eszközre, vagy egy "csoportos regisztráció" több eszközre. Az azonosító az eszköz által használt [tanúsítványmechanizmuson](concepts-security.md#attestation-mechanism) alapul, amely lehetővé teszi, hogy a létesítési szolgáltatás tanúsítsa az eszköz hitelességét a regisztráció során:

   - **TPM**: "egyéni regisztrációként" konfigurálva az eszköz identitása a TPM regisztrációs azonosítóján és a nyilvános jóváhagyási kulcson alapul. Mivel a TPM [specifikáció](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/), a szolgáltatás csak arra számít, hogy tanúsítja a specifikáció, függetlenül a Ttól, hogy a TPM végrehajtása (hardver vagy szoftver). A TPM-alapú tanúsítványokkal kapcsolatos részletekért [lásd: Eszközkiépítés: Identitásigazolás TPM-mel.](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) 

   - **X509**: "egyéni beléptetésként" vagy "csoportos regisztrációként" konfigurálva az eszköz identitása egy X.509 digitális tanúsítványon alapul, amelyet .pem vagy .cer fájlként töltenek fel a beiratkozásba.

   > [!IMPORTANT]  
   > Bár nem előfeltétele az eszközkiépítési szolgáltatások használatának, javasoljuk, hogy az eszköz használjon hardveres biztonsági modul (HSM) bizalmas eszközidentitás-adatok, például kulcsok és X.509 tanúsítványok tárolására.

3. **Eszközregisztráció és -konfiguráció** – a regisztrációs szoftver által történő indításkor kezdeményezett, amely az eszköznek és a tanúsítványmechanizmusnak megfelelő Eszközkiépítési szolgáltatás ügyfél SDK-jával készült. A szoftver kapcsolatot hoz létre a létesítési szolgáltatással az eszköz hitelesítéséhez, majd ezt követően regisztrál az IoT Hubban. Sikeres regisztráció után az eszköz az IoT Hub egyedi eszközazonosítóés kapcsolati adataival együtt kap, lehetővé téve, hogy lekéri a kezdeti konfigurációt, és megkezdje a telemetriai folyamatot. Éles környezetben ez a fázis hetekkel vagy hónapokkal az előző két fázis után fordulhat elő.

## <a name="roles-and-operations"></a>Szerepkörök és műveletek

Az előző szakaszban tárgyalt fázisok heteket vagy hónapokat ölelhetnek fel, mivel a gyártási realitások, mint például a gyártási idő, a szállítás, a vámeljárás stb. Emellett a különböző érintett entitások miatt több szerepkörön keresztül is átnyúlhatnak a tevékenységekre. Ez a szakasz az egyes fázisokhoz kapcsolódó különböző szerepköröket és műveleteket mélyebben vizsgálja, majd egy szekvenciadiagramban mutatja be a folyamatot. 

Az automatikus üzembe helyezés az eszköz gyártójára is vonatkozik, amely az igazolási mechanizmus engedélyezésére vonatkozik. A gyártási műveletek az automatikus kiépítési fázisok időzítésétől függetlenül is előfordulhatnak, különösen azokban az esetekben, amikor az automatikus kiépítés t követően új eszközöket szerznek be.

A bal oldali tartalomjegyzékben számos rövid útmutató található, amelyek segítenek az automatikus kiépítés gyakorlati felületen történő magyarázatában. A tanulási folyamat megkönnyítése/egyszerűsítése érdekében a szoftvert egy fizikai eszköz szimulálására használják a regisztrációhoz és a regisztrációhoz. Egyes rövid útmutatók megkövetelik, hogy több szerepkör műveleteit, beleértve a nem létező szerepkörök, a quickstarts szimulált jellege miatt.

| Szerepkör | Művelet | Leírás |
|------| --------- | ------------|
| Gyártó | Identitás és regisztrációs URL kódolása | Az alkalmazott tanúsítványmechanizmus alapján a gyártó felelős az eszköz identitásadatainak kódolásáért és az eszközkiépítési szolgáltatás regisztrációs URL-címének kódolásáért.<br><br>**Rövid útmutatók:** mivel az eszköz szimulált, nincs gyártói szerepkör. Tekintse meg a fejlesztői szerepkört a rról, hogyan kapja meg ezeket az információkat, amelyek et használnak a minta regisztrációs alkalmazás kódolásához. |
| | Eszközidentitás biztosítása | Az eszköz azonosító adatainak létrehozójaként a gyártó felelős azért, hogy azt az üzemeltetőnek (vagy egy kijelölt ügynöknek) közölje, vagy közvetlenül beírassa azt az Eszközkiépítési szolgáltatásba API-kon keresztül.<br><br>**Rövid útmutatók:** mivel az eszköz szimulált, nincs gyártói szerepkör. Tekintse meg az Operátor szerepkör t, hogy miként kapja meg az eszközidentitást, amely egy szimulált eszköz regisztrálására szolgál az Eszközkiépítési szolgáltatás példányában. |
| Művelet | Automatikus kiépítés konfigurálása | Ez a művelet megfelel az automatikus kiépítés első fázisának.<br><br>**Rövid útmutatók:** Az Operátori szerepkört hajtja végre, konfigurálva az Eszközkiépítési szolgáltatás és az IoT Hub-példányokat az Azure-előfizetésben. |
|  | Eszközidentitás igénylése | Ez a művelet megfelel az automatikus kiépítés második fázisának.<br><br>**Rövid útmutatók:** Ön az Operátor szerepkört hajtja végre, és regisztrálja a szimulált eszközt az eszközkiépítési szolgáltatáspéldányban. Az eszköz identitását a rövid útmutatóban szimulált tanúsítványmetódus határozza meg (TPM vagy X.509). Tekintse meg a fejlesztői szerepkört az igazolásrészleteiről. |
| eszközkiépítési szolgáltatás,<br>IoT Hub | \<minden művelet\> | A fizikai eszközökkel és a szimulált eszközökkel végzett rövid útmutatókkal végzett éles implementációk esetében ezek a szerepkörök az Azure-előfizetésben konfigurált IoT-szolgáltatásokon keresztül teljesednek. A szerepkörök/műveletek pontosan ugyanúgy működnek, mivel az IoT-szolgáltatások közömbösek a fizikai és a szimulált eszközök kiépítése iránt. |
| Fejlesztői | Regisztrációs szoftver létrehozása/telepítése | Ez a művelet megfelel az automatikus kiépítés harmadik fázisának. A fejlesztő felelős a regisztrációs szoftver létrehozásáért és telepítéséért az eszközön a megfelelő SDK használatával.<br><br>**Rövid útmutatók:** A minta regisztrációs alkalmazás hoz létre szimulálja a valódi eszköz, a platform/nyelv választás, amely fut a munkaállomáson (ahelyett, hogy üzembe helyezve azt a fizikai eszköz). A regisztrációs alkalmazás ugyanazokat a műveleteket hajtja végre, mint a fizikai eszközre telepített. Megadhatja az igazolási módszert (TPM vagy X.509 tanúsítvány), valamint az eszközkiépítési szolgáltatás példányának regisztrációs URL-címét és "azonosító hatókörét". Az eszköz identitását az SDK-tanúsítvány logikája határozza meg futásidőben, a megadott módszer alapján: <ul><li>**TPM-tanúsítvány** – a fejlesztési munkaállomás [tpm-szimulátoralkalmazást](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator)futtat. A futtatást követően egy külön alkalmazás segítségével bontja ki a TPM "ellenőrző kulcs" és a "regisztrációs azonosító" az eszköz identitásának regisztrálása. Az SDK-tanúsítvány logika is használja a szimulátor a regisztráció során, egy aláírt SAS-jogkivonat hitelesítési és beléptetési ellenőrzés bemutatása.</li><li>**X509 tanúsítvány** – egy eszköz segítségével [hoz létre tanúsítványt](how-to-use-sdk-tools.md#x509-certificate-generator). A létrehozást követően létrehozza a regisztrációhoz szükséges tanúsítványfájlt. Az SDK-tanúsítvány logika is használja a tanúsítványt a regisztráció során, hogy jelen van a hitelesítés és a beléptetés ellenőrzése.</li></ul> |
| Eszköz | Indítás és regisztráció | Ez a művelet megfelel az automatikus kiépítés harmadik fázisának, amelyet a Fejlesztő által készített eszközregisztrációs szoftver teljesít. A részletekért tekintse meg a Fejlesztői szerepkört. Első rendszerindításkor: <ol><li>Az alkalmazás a fejlesztés során megadott globális URL-cím és szolgáltatás "ID Scope" szolgáltatás szerint csatlakozik az eszközkiépítési szolgáltatás példányához.</li><li>A csatlakozás után az eszköz hitelesítésre kerül a regisztráció során megadott tanúsítványmetódus és identitás szerint.</li><li>A hitelesítés után az eszköz regisztrálva van a létesítési szolgáltatáspéldány által megadott IoT Hub-példányban.</li><li>Sikeres regisztráció után egy egyedi eszközazonosító és az IoT Hub-végpont visszakerül a regisztrációs alkalmazás az IoT Hub kommunikáció.</li><li> Innen az eszköz lehívhatja a kezdeti [eszköz ikerállapotát](~/articles/iot-hub/iot-hub-devguide-device-twins.md) a konfigurációhoz, és megkezdheti a telemetriai adatok jelentésének folyamatát.</li></ol>**Rövid útmutatók:** mivel az eszköz szimulált, a regisztrációs szoftver fut a fejlesztési munkaállomáson.|

Az alábbi ábra összefoglalja az eszköz automatikus kiépítése során a műveletek szerepköreit és szekvenálását:
<br><br>
[![Automatikus kiépítési sorrend egy eszközhöz](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Szükség esetén a gyártó az "Eszközidentitás igénylése" műveletet is elvégezheti az eszközkiépítési szolgáltatás API-k használatával (az Operátor helyett). A szekvenálás részletes ismertetése és még sok más, tekintse meg a [Zéró érintéses eszköz regisztrációját az Azure IoT-videóval](https://youtu.be/cSbDRNg72cU?t=2460) (a 41:00-as jelölőtől kezdődően)

## <a name="roles-and-azure-accounts"></a>Szerepkörök és Azure-fiókok

Hogyan minden egyes szerepkör le van képezve egy Azure-fiók forgatókönyv-függő, és van jó néhány forgatókönyv, amely lehet szó. Az alábbi közös minták segítségével általános ismereteket nyújt arról, hogy a szerepkörök általában egy Azure-fiókhoz vannak rendelve.

#### <a name="chip-manufacturer-provides-security-services"></a>A chipgyártó biztonsági szolgáltatásokat nyújt

Ebben az esetben a gyártó kezeli az első szintű ügyfelek biztonságát. Ez a forgatókönyv az első szintű ügyfelek előnyben részesíthetik, mivel nem kell részletes biztonságot kezelniük. 

A gyártó bevezeti a biztonságot a hardveres biztonsági modulokba (HSM). Ez a biztonság magában foglalhatja azt, hogy a gyártó kulcsokat, tanúsítványokat stb. A gyártó ezt a biztonsági információt ügyfelei számára is létrehozhatja.

Ebben a forgatókönyvben két Azure-fiók is érintett lehet:

- **Fiók #1**: Valószínűleg bizonyos mértékig megoszlanak az operátori és fejlesztői szerepkörök között. Ez a fél megvásárolhatja a HSM chipeket a gyártótól. Ezek a chipek a Fiók#1 társított DPS-példányokra mutatnak. A DPS-regisztrációkkal ez a fél több, kétszintű ügyfélnek is bérelhet eszközöket az eszközregisztrációs beállítások DPS-ben történő újrakonfigurálásával. Ez a fél is rendelkezhet IoT hubok kiosztott végfelhasználói háttérrendszerek interfész elérése érdekében eszköz telemetriai adatok, stb. Ez utóbbi esetben előfordulhat, hogy nincs szükség második fiókra.

- **Fiók #2:** Végfelhasználók, kettes szintű ügyfelek saját IoT-központokkal rendelkezhetnek. A fiókkal társított fél #1 csak a fiók megfelelő hubjára pontzatot. Ez a konfiguráció megköveteli a DPS és az IoT-központok összekapcsolását az Azure Resource Manager-sablonok között, amely az Azure Resource Manager-sablonokkal elvégezhető.

#### <a name="all-in-one-oem"></a>All-in-one OEM

A gyártó lehet egy "all-in-one oem", ahol csak egy gyártófiókra lenne szükség. A gyártó kezeli a biztonsági és kiépítési végpontig.

A gyártó felhőalapú alkalmazást biztosíthat az eszközöket vásárló ügyfeleknek. Ez az alkalmazás a gyártó által lefoglalt IoT Hub-tal érintkezik.

Az automaták vagy az automatizált kávéautomaták példákat jelentenek erre a forgatókönyvre.




## <a name="next-steps"></a>További lépések

Hasznos lehet, ha ezt a cikket hivatkozási pontként könyvjelzővel jelöli meg, miközben végighalad a megfelelő automatikus kiépítési rövid útmutatókon. 

Kezdje azzal, hogy végrehajtja az "Automatikus kiépítés beállítása" rövid útmutatót, amely a legjobban megfelel a felügyeleti eszköz preferenciájának, amely végigvezeti a "Szolgáltatás konfigurációja" fázison:

- [Automatikus kiépítés beállítása az Azure CLI használatával](quick-setup-auto-provision-cli.md)
- [Automatikus kiépítés beállítása az Azure Portalon](quick-setup-auto-provision.md)
- [Automatikus kiépítés beállítása Erőforrás-kezelő sablonnal](quick-setup-auto-provision-rm.md)

Ezután folytassa az "Automatikus üzembe helyezés egy szimulált eszköz" rövid útmutató, amely megfelel az eszköz igazolási mechanizmus és az eszköz kiépítése szolgáltatás SDK/nyelvi preferencia. Ebben a rövid útmutatóban végigvezeti az "Eszközregisztráció" és az "Eszközregisztráció és -konfiguráció" fázisokat: 

|  | Szimulált eszközigazolási mechanizmus | Quickstart SDK/Nyelv |  |
|--|--|--|--|
|  | Platformmegbízhatósági modul (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C #](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509 tanúsítvány | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C #](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |





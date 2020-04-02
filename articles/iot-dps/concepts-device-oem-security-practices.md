---
title: Biztonsági eljárások a gyártók számára - Azure IoT-eszközkiépítési szolgáltatás
description: Áttekintést nyújt a számítógépgyártók és az eszközgyártók általános biztonsági gyakorlatai, amelyek előkészítik az eszközöket az Azure IoT-eszközkiépítési szolgáltatásban (DPS) való regisztrálásra.
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 3854f353e4ea0b78c0162681e0b89d37419105d8
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529523"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Az Azure IoT-eszközgyártók biztonsági gyakorlatai
Ahogy egyre több gyártó bocsát ki IoT-eszközöket, hasznos lehet a gyakori gyakorlatokkal kapcsolatos útmutatás azonosítása. Ez a cikk összefoglalja az ajánlott biztonsági eljárásokat, amelyeket figyelembe kell venni, amikor az Azure IoT-eszközkiépítési szolgáltatás (DPS) használatával használható eszközöket gyárt.  

> [!div class="checklist"]
> * Eszközhitelesítési beállítások megadása
> * Tanúsítványok telepítése IoT-eszközökre
> * Platformmegbízhatósági modul (TPM) integrálása a gyártási folyamatba

## <a name="selecting-device-authentication-options"></a>Eszközhitelesítési beállítások megadása
Bármely IoT-eszközbiztonsági intézkedés végső célja egy biztonságos IoT-megoldás létrehozása. De az olyan problémák, mint a hardverkorlátozások, a költségek és a biztonsági szakértelem szintje, mind hatással vannak arra, hogy mely beállításokat választja. Továbbá a biztonsági megközelítés hatással van arra, hogy az IoT-eszközök hogyan csatlakoznak a felhőhöz. Bár [az IoT-biztonság számos elemét](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) kell figyelembe venni, egy kulcsfontosságú elem, amellyel minden ügyfél találkozik, milyen hitelesítési típushasználata. 

Három széles körben használt hitelesítési típus az X.509-es tanúsítványok, a platformmegbízhatósági modulok (TPM) és a szimmetrikus kulcsok. Bár léteznek más hitelesítési típusok, a legtöbb ügyfél, aki megoldásokat hoz létre az Azure IoT-n, a három típus egyikét használja. A cikk többi része az egyes hitelesítési típusok használatának előnyeit és hátrányait méri fel.

### <a name="x509-certificate"></a>X.509 tanúsítvány
Az X.509 tanúsítványok a hitelesítéshez használható digitális identitások. Az X.509 tanúsítványszabvány t az [IETF RFC 5280](https://tools.ietf.org/html/rfc5280)dokumentálja. Az Azure IoT-ben kétféleképpen lehet hitelesíteni a tanúsítványokat:
- Ujjlenyomat. Egy ujjlenyomat-algoritmus fut egy tanúsítványon hexadecimális karakterlánc létrehozásához. A létrehozott karakterlánc a tanúsítvány egyedi azonosítója. 
- Hitelesítésszolgáltató-hitelesítés teljes láncalapján. A tanúsítványlánc a végfelhasználói (EE) tanúsítvány hitelesítéséhez szükséges összes tanúsítvány hierarchikus listája. Az EE-tanúsítványok hitelesítéséhez a lánc ban lévő minden tanúsítványt hitelesíteni kell, beleértve a megbízható legfelső szintű hitelesítésszolgáltatót is. 

Profik az X.509-hez:
- Az X.509 az Azure IoT által támogatott legbiztonságosabb hitelesítési típus.
- Az X.509 magas szintű ellenőrzést tesz lehetővé a tanúsítványkezelés szempontjából.
- Számos gyártó áll rendelkezésre X.509 alapú hitelesítési megoldások biztosításához.

Hátrányok az X.509-hez:
- Sok vevőnek külső szállítókra kell támaszkodnia a tanúsítványaihoz.
- A tanúsítványkezelés költséges lehet, és hozzáadódik a megoldás teljes költségéhez.
- A tanúsítványok életciklusának kezelése nehéz lehet, ha a logisztika nem jól átgondolt. 

### <a name="trusted-platform-module-tpm"></a>Platformmegbízhatósági modul (TPM)
A TPM, más néven [ISO/IEC 11889](https://www.iso.org/standard/66510.html)szabvány a kriptográfiai kulcsok biztonságos létrehozásához és tárolásához. A TPM egy virtuális vagy fizikai I/O-eszközre is utal, amely a szabványt megvalósító modulokkal kommunikál. A TPM-eszközök létezhetnek különálló hardverként, integrált hardverként, belső vezérlőprogram-alapú modulként vagy szoftveralapú modulként. 

A TPI-k és a szimmetrikus billentyűk között két fő különbség van: 
- A TPM-chipek X.509 tanúsítványokat is tárolhatnak.
- A DPS-ben lévő TPM-tanúsítvány a TPM-ellenőrzőkulcsot (EK) használja, amely az aszimmetrikus hitelesítés egyik formája. Az aszimmetrikus hitelesítés esetén a titkosításhoz nyilvános kulcsot, a visszafejtéshez pedig egy külön személyes kulcsot használnak. Ezzel szemben a szimmetrikus kulcsok szimmetrikus hitelesítést használnak, ahol a titkos kulcsot használják a titkosításhoz és a visszafejtéshez. 

A TPM előnyei:
- A TPM-ek számos Windows-eszközön szabványos hardverként szerepelnek, és az operációs rendszer beépített támogatása is megtalálható. 
- A TPM-tanúsítvány könnyebben biztonságos, mint a megosztott hozzáférésű aláírás (SAS) tokenalapú szimmetrikus kulcsigazolás.
- Könnyedén lejár, és megújítja, vagy roll, eszköz hitelesítő adatait. A DPS automatikusan elvezeti az IoT Hub hitelesítő adatait, ha egy TPM-eszköz újrakiépítésére van szükség.

Hátrányok a TPM: 
- A TPM-ek összetettek és nehezen használhatók. 
- Alkalmazásfejlesztés TPM-ekkel nehéz, kivéve, ha van egy fizikai TPM vagy minőségi emulátor.
- Előfordulhat, hogy újra kell terveznie az eszköz tábláját, hogy a hardverben szerepeljen a TPM. 
- Ha az EK-t egy TPM-en görgeti, az tönkreteszi a TPM identitását, és létrehoz egy újat. Bár a fizikai chip ugyanaz marad, új identitással rendelkezik az IoT-megoldásában.

### <a name="symmetric-key"></a>Szimmetrikus kulcs
A szimmetrikus kulcsok esetén ugyanaz a kulcs az üzenetek titkosítására és visszafejtésére szolgál. Ennek eredményeképpen ugyanaz takarás ismert mind az eszköz, mind a hitelesítést végző szolgáltatás számára. Az Azure IoT támogatja a SAS-tokenalapú szimmetrikus kulcskapcsolatokat. A szimmetrikus kulcsos hitelesítés jelentős tulajdonosi felelősséget igényel a kulcsok biztonságossá tétele és az X.509-es hitelesítés sel azonos szintű biztonság elérése érdekében. Ha szimmetrikus kulcsokat használ, az ajánlott eljárás a kulcsok hardveres biztonsági modul (HSM) használatával történő védelme.

A szimmetrikus kulcs előnyei:
- A szimmetrikus kulcsok használata a legegyszerűbb, legalacsonyabb költségű módja a hitelesítés megkezdésének.
- A szimmetrikus billentyűk használata leegyszerűsíti a folyamatot, mivel nincs semmi extra generálni. 

Hátrányai szimmetrikus kulcs: 
- A szimmetrikus kulcsok jelentős erőfeszítéseket tesznek a kulcsok biztonságossá tétele érdekében. Ugyanaz a kulcs van megosztva az eszköz és a felhő között, ami azt jelenti, hogy a kulcsot két helyen kell védeni. Ezzel szemben a TPM és az X.509 tanúsítványokkal való kihívás a nyilvános kulcs birtoklásának bizonyul anélkül, hogy felfedné a személyes kulcsot.
- A szimmetrikus billentyűk megkönnyítik a rossz biztonsági eljárások követését. A szimmetrikus billentyűk gyakori tendenciája a titkosítatlan kulcsok titkoskódja az eszközökön. Bár ez a gyakorlat kényelmes, sebezhetővé teszi a kulcsokat. Bizonyos kockázatok csökkentése a szimmetrikus kulcs biztonságos tárolásával az eszközön. Ha azonban a prioritás végső soron a biztonság, nem pedig a kényelem, használja az X.509-es tanúsítványokat vagy a TPM protokollt a hitelesítéshez. 

### <a name="shared-symmetric-key"></a>Megosztott szimmetrikus kulcs
A szimmetrikus kulcshitelesítés egy változata, az úgynevezett megosztott szimmetrikus kulcs. Ez a megközelítés magában foglalja ugyanazt a szimmetrikus kulcsot minden eszközön. A javaslat az, hogy ne használjon megosztott szimmetrikus kulcsokat az eszközökön. 

Pro a megosztott szimmetrikus kulcshoz:
- Egyszerűen megvalósítható és olcsó, hogy készítsen nagy méretekben. 

Hátránya a megosztott szimmetrikus kulcs: 
- Nagyon sebezhető a támadással szemben. A könnyű végrehajtás előnyeit messze ellensúlyozza a kockázat. 
- Bárki megszemélyesítheti az eszközeit, ha megszerzik a megosztott kulcsot.
- Ha olyan megosztott szimmetrikus kulcsra támaszkodik, amely veszélybe kerül, valószínűleg elveszíti az eszközök feletti irányítást. 

### <a name="making-the-right-choice-for-your-devices"></a>A megfelelő választás az eszközök számára
Hitelesítési módszer kiválasztásához vegye figyelembe az egyes módszerek előnyeit és költségeit az egyedi gyártási folyamathoz.  Az eszköz hitelesítése, általában van egy inverz kapcsolat, hogy mennyire biztonságos egy adott megközelítés, és mennyire kényelmes.  

## <a name="installing-certificates-on-iot-devices"></a>Tanúsítványok telepítése IoT-eszközökre
Ha X.509-es tanúsítványokat használ az IoT-eszközök hitelesítéséhez, ez a szakasz útmutatást nyújt a tanúsítványok gyártási folyamatba való integrálásához. Több döntést kell meghoznod.  Ezek közé tartoznak a közös tanúsítványváltozókkal, a tanúsítványok létrehozásának időpontjával és telepítésével kapcsolatos döntések. 

Ha már hozzászokott a jelszavak használatához, megkérdezheti, hogy miért nem használhatja ugyanazt a tanúsítványt az összes eszközén, ugyanúgy, ahogy az összes eszközén is használhatja. Először is, ugyanazt a jelszót mindenhol veszélyes. A gyakorlat kivannak téve a vállalatok jelentős DDoS támadások, köztük az egyik, hogy levette a DNS az USA keleti partján néhány évvel ezelőtt. Soha ne használja ugyanazt a jelszót mindenhol, még a személyes fiókok. Másodszor, a tanúsítvány nem jelszó, hanem egyedi identitás. A jelszó olyan, mint egy titkos kód, amit bárki használhat, hogy kinyisson egy ajtót egy biztonságos épületben.  Ez olyasmi, amit tudsz, és bárkinek odaadhatod a jelszót, hogy bejusson.  A tanúsítvány olyan, mint a jogosítvány a fényképeddel és egyéb részletekkel, amelyeket megmutathatsz egy őrnek, hogy bejusson egy biztonságos épületbe. Ahhoz kötődik, aki vagy.  Feltéve, hogy az őr pontosan illeszkedik az emberek a vezetői engedélyek, csak akkor használhatja a jogosítványt (identitás) szerezni bejárat. 

### <a name="variables-involved-in-certificate-decisions"></a>A tanúsítványokkal kapcsolatos döntésekben részt vevő változók
Vegye figyelembe a következő változókat, és azt, hogy ezek hogyan befolyásolják a teljes gyártási folyamatot. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>A megbízhatósági tanúsítvány legfelső szintű szolgáltatásának helye
A nyilvános kulcsokkal kapcsolatos infrastruktúra (PKI) kezelése költséges és összetett lehet.  Különösen akkor, ha a vállalatnak nincs tapasztalata a PKI kezelésében. A következő lehetőségek közül választhat:
- Külső külső pki-t használjon. Köztes aláíró tanúsítványokat külső tanúsítványszállítótól vásárolhat. Vagy használhat saját hitelesítésszolgáltatót is. 
- Használjon saját felügyelt PKI-t. Fenntarthatja saját PKI-rendszerét, és létrehozhatja saját tanúsítványait.
- Használja az [Azure Sphere](https://azure.microsoft.com/services/azure-sphere/) biztonsági szolgáltatás. Ez a beállítás csak az Azure Sphere-eszközökre vonatkozik. 

#### <a name="where-certificates-are-stored"></a>A tanúsítványok tárolásának helye
Van néhány tényező, amely befolyásolja a döntést, ahol a tanúsítványok tárolása. Ezek közé tartozik az eszköz típusa, a várható haszonkulcs (függetlenül attól, hogy megengedheti-e a biztonságos tárolást), az eszköz képességei és a meglévő biztonsági technológia az eszközön, amelyet esetleg használhat. Vegye figyelembe a következő lehetőségeket:
- Egy hardveres biztonsági modulban (HSM). A HSM használata erősen ajánlott. Ellenőrizze, hogy az eszköz vezérlőtábláján van-e már HSM telepítve. Ha tudja, hogy nem rendelkezik HSM-mel, a hardver gyártójával együttműködve azonosítsa az igényeinek megfelelő HSM-et.
- Biztonságos helyen a lemezen, például egy megbízható végrehajtási környezetben (TEE).
- A helyi fájlrendszerben vagy egy tanúsítványtárolóban. Például a Windows tanúsítványtároló. 

#### <a name="connectivity-at-the-factory"></a>Csatlakozás a gyárban
A gyári kapcsolat határozza meg, hogy mikor és mikor kapja meg az eszközökre telepítandó tanúsítványokat. A csatlakozási lehetőségek a következők:
- Kapcsolat. A kapcsolat optimális, így leegyszerűsíti a tanúsítványok helyi létrehozásának folyamatát. 
- Nincs kapcsolat. Ebben az esetben egy hitelesítésszolgáltató aláírt tanúsítványával helyileg és offline is létrehozhat eszköztanúsítványokat. 
- Nincs kapcsolat. Ebben az esetben beszerezheti az előre létrehozott tanúsítványokat. Vagy használhat offline pki-t a tanúsítványok helyi létrehozásához.

#### <a name="audit-requirement"></a>Ellenőrzési követelmény
Attól függően, hogy milyen típusú eszközök et gyárt, előfordulhat, hogy a szabályozási követelmény, hogy hozzon létre egy naplózási nyomvonalat, hogyan eszköz identitások vannak telepítve az eszközökre. A naplózás jelentős termelési költséget jelent. Tehát a legtöbb esetben csak akkor tegye meg, ha szükséges. Ha nem biztos abban, hogy szükség van-e naplózásra, forduljon a vállalat jogi osztályához. A naplózási lehetőségek a következők: 
- Nem egy érzékeny iparág. Nincs szükség naplózásra.
- Érzékeny iparág. A tanúsítványokat biztonságos helyiségben kell elhelyezni a megfelelőségi tanúsítási követelményeknek megfelelően. Ha a tanúsítványok telepítéséhez biztonságos helyiségre van szüksége, valószínűleg már tisztában van azzal, hogy a tanúsítványok hogyan települnek az eszközökre. És valószínűleg már van egy ellenőrzési rendszer a helyén. 

#### <a name="length-of-certificate-validity"></a>A tanúsítvány érvényességének hossza
A licencekhöz hasonlóan a tanúsítványok lejárati dátuma is be van állítva a létrehozásukkor. A tanúsítvány érvényességének időtartamára az alábbiakat tetszésszerint:
- Megújítás nem szükséges.  Ez a megközelítés hosszú megújítási időszakot használ, így soha nem kell megújítania a tanúsítványt az eszköz élettartama alatt. Bár egy ilyen megközelítés kényelmes, ez is kockázatos.  Csökkentheti a kockázatot a biztonságos tárolás, például a HSM használatával az eszközökön. Az ajánlott eljárás azonban a hosszú élettartamú tanúsítványok használatának elkerülése.
- Megújítás szükséges.  Az eszköz élettartama alatt meg kell újítania a tanúsítványt. A tanúsítvány érvényességének hossza a környezettől függ, és a megújításhoz stratégiát kell kidolgoznia.  A stratégiának tartalmaznia kell, hogy hol kap tanúsítványokat, és milyen típusú, az eszközök nek kell használniuk a megújítási folyamat során. 

### <a name="when-to-generate-certificates"></a>Mikor kell tanúsítványokat létrehozni?
A gyár internetkapcsolati képességei hatással lesznek a tanúsítványok létrehozásának folyamatára. Több lehetősége is van a tanúsítványok létrehozásához: 

- Előre betöltött tanúsítványok.  Egyes HSM-szállítók prémium szolgáltatást kínálnak, amelyben a HSM-szállító tanúsítványokat telepít a vevő számára. Először az ügyfelek hozzáférést biztosítanak a HSM-szállítónak egy aláíró tanúsítványhoz. Ezután a HSM-szállító telepíti az aláíró tanúsítvány által aláírt tanúsítványokat minden olyan HSM-re, amelyet az ügyfél megvásárol. Az ügyfélnek mindössze annyit kell tennie, hogy telepíti a HSM-et az eszközre. Bár ez a szolgáltatás növeli a költségeket, segít a gyártási folyamat egyszerűsítésében.  És ez megoldja a kérdést, hogy mikor kell telepíteni a tanúsítványokat.
- Eszköz által létrehozott tanúsítványok.  Ha az eszközök belső tanúsítványokat hoznak létre, akkor ki kell bontania a nyilvános X.509 tanúsítványt az eszközről a DPS-ben való igényléshez. 
- Csatlakoztatott gyár.  Ha gyára rendelkezik kapcsolattal, bármikor létrehozhat eszköztanúsítványokat.
- Offline gyár saját PKI-vel. Ha a gyár nem rendelkezik kapcsolattal, és offline támogatással használja a saját PKI-t, szükség esetén létrehozhatja a tanúsítványokat.
- Offline gyár külső pki-vel. Ha a gyár nem rendelkezik kapcsolattal, és külső pki-t használ, előre létre kell hoznia a tanúsítványokat. És szükség lesz a tanúsítványok létrehozására egy olyan helyről, amely kapcsolattal rendelkezik. 

### <a name="when-to-install-certificates"></a>Mikor kell telepíteni a tanúsítványokat?
Miután létrehozta az IoT-eszközök tanúsítványait, telepítheti őket az eszközökre. 

Ha előre betöltött tanúsítványokat használ HSM-mel, a folyamat leegyszerűsödik. Miután a HSM telepítve van az eszközön, az eszköz kódja hozzáférhet. Ezután hívja meg a HSM API-kat a HSM-ben tárolt tanúsítvány eléréséhez. Ez a megközelítés a legkényelmesebb a gyártási folyamat. 

Ha nem használ előre betöltött tanúsítványt, telepítenie kell a tanúsítványt az éles folyamat részeként. A legegyszerűbb megközelítés az, hogy telepítse a tanúsítványt a HSM egy időben, hogy villog a kezdeti firmware képet. A folyamatnak hozzá kell adnia egy lépést a lemezkép minden eszközre való telepítéséhez. Ezt a lépést követően futtathatja a végső minőség-ellenőrzéseket és minden egyéb lépést, mielőtt becsomagolja és elszállítja az eszközt. 

Vannak olyan szoftvereszközök, amelyek lehetővé teszik a telepítési folyamat futtatását és a végső minőségellenőrzést egyetlen lépésben. Ezeket az eszközöket módosíthatja a tanúsítvány létrehozásához, vagy egy előre létrehozott tanúsítványtárolóból származó tanúsítvány lekérése érdekében. Ezután a szoftver telepítheti a tanúsítványt, ahol telepítenie kell. Az ilyen típusú szoftveres eszközök lehetővé teszik a termelésminőség-gyártás nagy mértékű futtatását. 

Miután telepítette a tanúsítványokat az eszközökre, a következő lépés az eszközök [DPS-en](about-iot-dps.md)való regisztrálásának megismerése. 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>A TPM integrálása a gyártási folyamatba
Ha egy TPM-et használ az IoT-eszközök hitelesítéséhez, ez a szakasz útmutatást nyújt. Az útmutató a széles körben használt TPM 2.0-s eszközök, amelyek kivonat-alapú üzenet hitelesítési kód (HMAC) kulcs támogatása. A TPM-chipek TPM-specifikációja a Trusted Computing Group által fenntartott ISO szabvány. A TPM-ről bővebben a [TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) és az [ISO/IEC 11889](https://www.iso.org/standard/66510.html)specifikációiban lehet látni. 

### <a name="taking-ownership-of-the-tpm"></a>A TPM tulajdonjogának átvétele
A TPM-chippel rendelkező eszközök gyártásának kritikus lépése a TPM tulajdonjogának átvétele. Erre a lépésre azért van szükség, hogy kulcsot biztosíthasson az eszköz tulajdonosának. Az első lépés az ellenőrző kulcs (EK) kinyerése az eszközről. A következő lépés az, hogy ténylegesen igényt tulajdonjogát. Ennek módja attól függ, hogy melyik TPM-et és az operációs rendszert használja. Szükség esetén lépjen kapcsolatba a TPM gyártójával vagy az eszköz operációs rendszerének fejlesztőjével, hogy megtudja, hogyan igényelheti a tulajdonjogot. 

A gyártási folyamat során kivonhatja az EK-t, és különböző időpontokban követelheti a tulajdonjogot, ami rugalmasságot biztosít. Számos gyártó használja ki ezt a rugalmasságot egy hardveres biztonsági modul (HSM) hozzáadásával, hogy fokozza eszközei biztonságát. Ez a szakasz útmutatást nyújt az EK kinyeréséhez, a TPM tulajdonjogának igényléséhez, valamint a lépések gyártási ütemtervbe való integrálásának megfontolásait. 

> [!IMPORTANT]
> Az alábbi útmutatás feltételezi, hogy különálló, belső vezérlőprogramot vagy integrált TPM-et használ. Azokon a helyeken, ahol alkalmazható, az útmutató megjegyzéseket ad hozzá a nem különálló vagy szoftveres TPM használatával kapcsolatos megjegyzésekhez. Ha szoftveres TPM-et használ, előfordulhat, hogy ez az útmutató nem tartalmazza a további lépéseket. A szoftveres TPI-k számos olyan implementációval rendelkeznek, amelyek túlmutatnak a jelen cikk hatókörén.  Általánosságban elmondható, hogy lehetséges a szoftverEs TPM integrálása a következő általános gyártási ütemtervbe. Bár az emulált TPM szoftver alkalmas prototípus-készítésre és tesztelésre, nem tud ugyanolyan szintű biztonságot nyújtani, mint egy különálló, belső vezérlőprogram vagy integrált TPM. Általános gyakorlatként ne használjon szoftveres TPM-et éles környezetben.

### <a name="general-manufacturing-timeline"></a>Általános gyártási ütemterv
A következő ütemterv bemutatja, hogyan megy keresztül a TPM egy termelési folyamaton, és hogyan kerül egy eszközbe. Minden gyártási folyamat egyedi, és ez az idővonal a leggyakoribb mintákat mutatja. Az idővonal útmutatást nyújt arra vonatkozóan, hogy mikor kell bizonyos műveleteket végrehajtani a billentyűkkel. 

#### <a name="step-1-tpm-is-manufactured"></a>1. lépés: A TPM gyártása
- Ha egy gyártótól vásárol TPM-eket az eszközökben való használatra, nézze meg, hogy kinyerik-e a nyilvános ellenőrzőkulcsokat (EK_pubs) az Ön számára. Ez akkor hasznos, ha a gyártó megadja a EK_pubs listáját a szállított eszközökkel. 
    > [!NOTE]
    > A TPM-gyártó írási hozzáférést adhat a regisztrációs listához a létesítési szolgáltatás megosztott hozzáférési szabályzatainak használatával.  Ez a megközelítés lehetővé teszi számukra, hogy a TPM-eket felírják a regisztrációs listára.  De ez a gyártási folyamat korai szakaszában van, és a TPM gyártójába való bizalmat igényel. Tegye meg a saját felelősségére. 

- Ha tpm-eket gyárt az eszközgyártóknak történő értékesítéshez, fontolja meg, hogy az ügyfeleknek listát ad a EK_pubs fizikai TPM-ekkel együtt.  Ha az ügyfelek nek EK_pubs biztosít, az egy lépéssel bővíti a folyamatot. 
- Ha a TPM-eket saját eszközeivel való használatra gyártja, azonosítsa, hogy a folyamat melyik pontja a legkényelmesebb a EK_pub kinyerésére. Az EK_pub az ütemterv bármely fennmaradó pontján kinyerheti. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>2. lépés: A TPM eszközre van telepítve
Ezen a ponton a termelési folyamat, tudnia kell, hogy melyik DPS-példány az eszköz fogja használni. Ennek eredményeképpen hozzáadhat eszközöket az automatikus kiépítés igénylési listájához. Az automatikus eszközkiépítésről a [DPS dokumentációjában](about-iot-dps.md)olvashat bővebben.
- Ha még nem vonta ki a EK_pub, most van itt az ideje, hogy erre. 
- A TPM telepítési folyamatától függően ez a lépés is jó alkalom arra, hogy átvegye a TPM tulajdonjogát. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>3. lépés: Az eszköz firmware-t és szoftvert telepített
A folyamat ezen pontján telepítse a DPS-ügyfelet az azonosító hatókörével és a globális URL-címmel együtt a kiépítéshez.
- Most van az utolsó esély, hogy kiszedje ma a EK_pub. Ha egy harmadik fél telepíti a szoftvert az eszközre, akkor érdemes először kivonni a EK_pub.
- A gyártási folyamat ezen pontja ideális a TPM tulajdonjogának átvételére.  
    > [!NOTE]
    > Ha szoftveres TPM-et használ, most telepítheti azt.  Egyszerre húzza ki a EK_pub.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>4. lépés: Az eszközt becsomagolják és elküldik a raktárba
Egy eszköz 6-12 hónapig ülhet egy raktárban, mielőtt üzembe helyezték. 

#### <a name="step-5-device-is-installed-into-the-location"></a>5. lépés: Az eszköz telepítve van a helyre
Miután az eszköz megérkezik a végső helyére, a DPS-szel történő automatikus kiépítésen megy keresztül.

További információ: [Autoprovisioning concepts](concepts-auto-provisioning.md) and [TPM attestation](concepts-tpm-attestation.md). 

## <a name="resources"></a>További források

A cikkben javasolt biztonsági eljárások mellett az Azure IoT erőforrásokat is biztosít a biztonságos hardverkiválasztásához és a biztonságos IoT-telepítések létrehozásához: 
- Az Azure IoT [biztonsági javaslatai](../iot-fundamentals/security-recommendations.md) a telepítési folyamat irányításához. 
- Az [Azure Security Center](https://azure.microsoft.com/services/security-center/) olyan szolgáltatást kínál, amely biztonságos IoT-telepítések létrehozásához nyújt segítséget. 
- A hardverkörnyezet kiértékeléséhez az [IoT-biztonság kiértékelése](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf)című tanulmányban talál segítséget. 
- A biztonságos hardver kiválasztásával kapcsolatban a [megfelelő biztonságos hardver az IoT-telepítéshez](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf)című témakörben talál segítséget. 
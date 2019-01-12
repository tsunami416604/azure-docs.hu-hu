---
title: Ismerje meg, hogyan védi a kezelő az eszközök, szoftverek – Azure IoT Edge |} A Microsoft Docs
description: Az IoT Edge-eszköz biztonsági forgalmazóval és biztonsági szolgáltatások biztonságának kezelése.
services: iot-edge
keywords: biztonság, a biztonságos elem, az enklávé, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 07/30/2018
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bc441e2bbd36c8d078eb67aff48e58684a026289
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232175"
---
# <a name="azure-iot-edge-security-manager"></a>Az Azure IoT Edge-biztonságkezelő

Az Azure IoT Edge biztonsági manager egy jól körülhatárolható biztonsági alapvető védelme érdekében az IoT Edge-eszköz és az összes összetevői által a biztonságos szilícium hardver paltformfüggetlen. A biztonság megerősítését kommunikációnak, és az eredeti eszköz gyártó (OEM) technológia integrációs pontot biztosít.

![Az Azure IoT Edge-biztonságkezelő](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge-biztonságkezelő célja, hogy az IoT Edge-eszköz és minden szoftver rejlő művelet integritásának védelemre.  Ezt úgy valósítja transitioning alapul szolgáló hardver gyökér megbízhatósági hardver bizalmi kapcsolatot (ha elérhető) biztonságos IoT Edge-futtatókörnyezet elindíthat és továbbra is a műveleteit sértetlenségének figyelésére.  Az IoT Edge-biztonságkezelő az biztonságos szilícium hardver és szoftver működő (ahol elérhető) segítségével a lehető legmagasabb szintű biztonsági intézkedésekkel nyújthat.  

Az IoT Edge-biztonságkezelő feladatai közé tartozik, de nem korlátozódik:

* Biztonságos, és az Azure IoT Edge-eszköz rendszerindítása mért.
* Identitás-eszközkiépítési és megbízható átvitelét ha vannak ilyenek.
* Üzemeltethet, és felhőszolgáltatásokhoz, mint például a Device Provisioning Service-összetevői eszköz védelmét.
* Biztonságosan kiépítése az IoT Edge-modulok egyedi identitásokkal.
* A forgalomirányító eszköz hardveres legfelső szintű megbízhatóság közjegyző szolgáltatásokon keresztül.
* IoT Edge-műveletek futásidőben sértetlenségének figyelésére.

IoT Edge-biztonságkezelő három összetevőt tartalmaz:

* IoT Edge biztonsági démon.
* Hardveres biztonsági modul platform absztrakciós réteg (HSM-PAL).
* Nem kötelező, de erősen ajánlott hardver szilícium legfelső szintű megbízhatóság vagy HSM.

## <a name="the-iot-edge-security-daemon"></a>Az IoT Edge biztonsági démon

IoT Edge biztonsági démon a felelős a logikai műveleteket az IoT Edge biztonsági manager szoftvert. Az IoT Edge-eszköz megbízható számítási alapja egy jelentős része legyen. 

### <a name="design-principles"></a>Tervezési alapelvek

Az IoT Edge biztonsági démon a következő két alapelvek: működési integritás maximalizálása és a minimalizálása érdekében a növekedést és a vásárlói lemorzsolódás.

#### <a name="maximize-operational-integrity"></a>Maximalizálja a működési integritás

IoT Edge biztonsági démon belül bármely megadott legfelső szintű megbízhatóság hardver defense képességét lehetséges legmagasabb integritását, működik. Megfelelő integrációnak köszönhetően a legfelső szintű megbízhatóság hardver méri, és figyeli a biztonság démon statikusan és futásidőben az illetéktelen beavatkozásoknak.

Fizikai hozzáférés, mindig egy fenyegetést, IoT-eszközökön. Hardver gyökér megbízhatósági integritását, az IoT Edge biztonsági démon megvédésekor fontos szerepet játszik.  Hardver gyökér megbízhatósági érkeznek a következő két változatban érhetők el:

* biztonságos elemek, például a titkosítási kulcsok és titkok bizalmas adatok védelme.
* biztonságos enklávék, titkos kulcsok védelme, például a kulcsokat és teljesítményigényes számítási feladatokhoz, mint például a mérési és számlázási.

Két különböző végrehajtási környezetekben hardver legfelső szintű megbízhatóság igénybevételéhez áll rendelkezésre:

* A standard vagy a látványos végrehajtási környezetben (REE) által használt, a bizalmas adatok védelme érdekében a biztonságos elemek használatát.
* A megbízható végrehajtási környezetben (TEE) által használt, biztonságos enklávé technológia segítségével bizalmas információk védelme érdekében, illetve szoftvervégrehajtás védelem használatát.

Biztonságos enklávék gyökérként használja, hardver a megbízható eszközökhöz bizalmas logikai belül az IoT Edge biztonsági démon várhatóan az enklávé belül találhatók.  A biztonsági démon bizalmas adatokkal nem rendelkező részeit a TEE kívül lehetnek.  Minden esetben várható eredeti tervezési gyártóknak (eszközgyártók általi) és a számítógépgyártó (OEM) mérjük és védi a rendszerindító és a futtatókörnyezet az IoT Edge biztonsági démon integritását a HSM-ből megbízhatósági kiterjeszteni.

#### <a name="minimize-bloat-and-churn"></a>Minimalizálása érdekében a növekedést és a vásárlói lemorzsolódás

Az IoT Edge biztonsági démon egy másik core alapelve, hogy adatváltozás.  A legmagasabb szintű megbízhatóság az IoT Edge biztonsági démon szorosan összekapcsolhatja a eszköz hardveres legfelső szintű megbízhatóság és natív kódú működhetnek.  Szokás az ilyen típusú realizations frissíteni a démon szoftver révén a hardver gyökér megbízhatósági a biztonságos frissítési útvonal (szemben az operációs rendszer a megadott frissítési mechanizmusok), amely adott hardver- és telepítési forgatókönyvtől függően kihívást jelenthet.  IoT-eszközt a erős javaslat biztonsági megújítási pedig azt jelöli, hogy túl sok frissítési követelményeket vagy nagyméretű frissítések is észleltünk adattartalmakat bővítheti a fenyegetés felület számos okból.  Ilyenek például a rendszer kihagyja a rendelkezésre állás maximalizálása érdekében a frissítések vagy a gyökér megbízhatósági hardver túl nagy frissítés is észleltünk adattartalmakat feldolgozása által korlátozott.  Mint ilyen, a terv az IoT Edge biztonsági démon az, hogy az erőforrás-igényű tömör, és ezért a megbízható számítástechnika alapvető kis és frissítési követelmények minimalizálása érdekében.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge biztonsági démon architektúrája

![Az Azure IoT Edge biztonsági démon](media/edge-security-manager/iot-edge-security-daemon.png)

Az IoT Edge biztonsági démon tervezésnek kihasználásához adatvédelmi technológia, a biztonság megerősítését a rendelkezésre álló hardver legfelső szintű lesz.  Lehetővé teszi egy Standard/gazdag végrehajtási környezetben (REE) és a egy megbízható végrehajtási környezetben (TEE) közötti vett split művelet során hardver technológiái nyújtanak megbízható végrehajtási környezeteket. Szerepkör-specifikus felületeket engedélyezze a kölcsönhatások, IoT Edge-eszköz és a műveletek biztosításához integritását, az IoT Edge főbb összetevőit.

#### <a name="cloud-interface"></a>Felhőalapú kapcsolat

A felhőbeli felület lehetővé teszi, hogy az IoT Edge biztonsági démon a felhőszolgáltatásokhoz kapcsolódik, például az eszköz biztonsági hasonló biztonsági megújítási felhőalapú üdvözli.  Például az IoT Edge biztonsági démon jelenleg ez az interfész elérésére használja az Azure IoT Hub [Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/) eszközt identitás-életciklus kezelésének.  

#### <a name="management-api"></a>Felügyeleti API

IoT Edge biztonsági démon kínál a felügyeleti API-t, amely hívja meg az IoT Edge-ügynök eltávolításakor létrehozása/indítása/leállítása/egy edge-modul. Az IoT Edge biztonsági démon "regisztráció" az összes aktív modulok tárolja. Tyto registrace egy modul identitás leképezése a modul néhány tulajdonságát. Ezekhez a tulajdonságokhoz néhány példa a a tároló vagy a docker-tároló tartalmának kivonatát futó folyamatot a folyamat azonosítója (pid) lehet.

Ezek a tulajdonságok a számítási feladatot (részletesen lásd alább) igazolja, hogy a hívó jogosult-e művelet végrehajtásához az API segítségével.

A felügyeleti API-t egy olyan privileged, csak az IoT Edge-ügynöktől hívható API.  Mivel az IoT Edge biztonsági démon csatlakoztatja, és az IoT Edge agent elindul, létrehozható egy implicit regisztrációs az IoT Edge-ügynök után azt, hogy az IoT Edge-ügynök nem lett illetéktelenül rendelkezik igazolt. A számítási feladatok API-t használó igazolási eljárást korlátozza a hozzáférést a felügyeleti API-t csak az IoT Edge-ügynök szolgál.

#### <a name="container-api"></a>Tároló API

IoT Edge biztonsági démon a tároló felület kommunikál a modul által okozott például Moby és a Docker használt rendszer kínál.

#### <a name="workload-api"></a>API-számítási feladatok

A számítási feladatok API egy IoT Edge biztonsági démon API elérhető összes modult, beleértve az IoT Edge-ügynök. Az azonosításhoz biztosít, bármelyik HSM rootolva aláírt jogkivonat vagy X509 tanúsítványt, és megfelelő megbízhatósági csomagot modulra. A megbízhatósági csomag tartalmazza a CA-tanúsítványok az összes többi kiszolgálóján, hogy bízzon meg a modulok.

IoT Edge biztonsági démon az igazolási folyamat meg szeretné védeni az API-t használja. Egy modult meghívja az API-t, az IoT Edge biztonsági démon megkísérli megtalálni egy regisztrációs identitását. Sikeres művelet esetén a modul mérésére használja a regisztráció tulajdonságai. Ha a mérték folyamat eredménye megegyezik a regisztráció, egy új HSM aláírt jogkivonat vagy X509 feltörték-e tanúsítvány jön létre. A modul visszatér a megfelelő CA-tanúsítványok (megbízhatósági bundle).  A modul csatlakoztatása az IoT hubhoz, más modulok tesznek, vagy a kiszolgáló indítása ezt a tanúsítványt használja. Ha az aláírt jogkivonat vagy a tanúsítvány lejár, feladata a modul egy új tanúsítvány kérése. 

### <a name="integration-and-maintenance"></a>Integráció és karbantartás

A Microsoft fenntartja a fő kód alapjának a [IoT Edge biztonsági démon a Githubon](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Telepítés és a frissítések

Telepítés és a frissítések, az IoT Edge biztonsági démon az operációs rendszer Csomagkezelő rendszeren keresztül történik. Hardver gyökér megbízhatósági az IoT Edge-eszközök további korlátozások a démon sértetlenségét, mivel kezeli az életciklusa – a biztonságos rendszerindítás és a frissítések felügyeleti rendszerek kell biztosítania.  Az eszközök számára, Fedezze fel a megfelelő az eszköz képességeinek megfelelően ezek rések leggyakoribb okai hagyják.

#### <a name="versioning"></a>Verziókezelés

Az IoT Edge-futtatókörnyezet nyomon követi, és az IoT Edge biztonsági démon verzióját jelenti. A verziószám az elvártnak megfelelően a *runtime.platform.version* attribútum az IoT Edge-ügynök modul jelentett tulajdonság.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Hardveres biztonsági modul platform hardverabsztrakciós réteg (HSM-PAL)

A HSM-PAL kivonatolja a megbízhatósági hardver a fejlesztői vagy a felhasználó az IoT Edge a saját hagyhatják el az összes gyökerében.  Van egy alkalmazásprogramozási felületet (API) és trans tartomány kommunikációs eljárásokat, például egy standard végrehajtási környezet és a egy biztonságos enklávé közötti kommunikáció kombinációját.  A HSM-PAL tényleges végrehajtása attól függ, hogy egy adott biztonságos hardver használja. A létezésük gyakorlatilag bármilyen biztonságos szilícium hardver használatát teszi lehetővé.

## <a name="secure-silicon-root-of-trust-hardware"></a>Biztonságos szilícium gyökér megbízhatósági hardver

Biztonságos szilícium forráshorgony megbízhatósági belül az IoT Edge-eszköz hardveres szükség.  Biztonságos szilícium tartalmazza a platformmegbízhatósági modul (TPM), beágyazott biztonságos elem (eSE), ARM TrustZone, Intel SGX és egyéni biztonságos szilícium technológiák számos különböző származnak.  Biztonságos szilícium főtanúsítvány az eszközök megbízhatósági használata erősen ajánlott a fenyegetések fizikailag IoT-eszközök kisegítő társított megadott.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge biztonsági manager integrálása és karbantartás

Az IoT Edge-biztonságkezelő célja, hogy azonosítsa és elkülönítheti az összetevőket, amelyek a biztonságának és integritásának a az Azure IoT Edge platform egyéni vezethet be védelemre. Harmadik felek, például az eszközkészítők, győződjön meg az eszköz hardveradatokkal egyéni biztonsági szolgáltatásait használja.  Tekintse meg a további lépések szakaszt az hivatkozásokat, amelyek bemutatják, hogyan lehet erősíti az Azure IoT-biztonságkezelő az a platformmegbízhatósági modul (TPM) a Linux és Windows platformokon. Ezekben a példákban használja a szoftvert vagy virtuális TPM, de diszkrét TPM-eszközök segítségével közvetlenül vonatkozik.  

## <a name="next-steps"></a>További lépések

Olvassa el a blogban a [intelligens peremhálózat biztosítása](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Létrehozása és kiépítése az [IoT Edge-eszköz a Linux rendszerű virtuális gép virtuális TPM-mel](how-to-auto-provision-simulated-device-linux.md).

Létrehozása és kiépítése az [IoT Edge-eszközön a Windows szimulált TPM-mel](how-to-auto-provision-simulated-device-windows.md).

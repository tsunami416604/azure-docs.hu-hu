---
title: Az Azure IoT Edge biztonsági manager |} A Microsoft Docs
description: Az IoT Edge-eszköz biztonsági forgalmazóval és biztonsági szolgáltatások biztonságának kezelése.
services: iot-edge
keywords: biztonság, a biztonságos elem, az enklávé, TEE, IoT Edge
author: eustacea
manager: timlt
ms.author: eustacea
ms.date: 07/30/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f0e548cdd1c59dc894899ddbac127dd76db7db26
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321183"
---
# <a name="azure-iot-edge-security-manager"></a>Az Azure IoT Edge-biztonságkezelő

Az Azure IoT Edge biztonsági manager egy jól körülhatárolható biztonsági alapvető védelme érdekében az IoT Edge-eszköz és az összes összetevői által a biztonságos szilícium hardver paltformfüggetlen. A biztonság megerősítését kommunikációnak, és az eredeti eszköz gyártó (OEM) technológia integrációs pontot biztosít.

![Az Azure IoT Edge-biztonságkezelő](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge-biztonságkezelő célja, hogy az IoT Edge-eszköz és minden szoftver rejlő művelet integritásának védelemre.  Ezt úgy valósítja transitioning alapul szolgáló hardver gyökér megbízhatósági hardver bizalmi kapcsolatot (ha elérhető) biztonságos IoT Edge-futtatókörnyezet elindíthat és továbbra is a műveleteit sértetlenségének figyelésére.  Az IoT Edge-biztonságkezelő lényegében magában foglalja a szoftvert biztonságos szilícium hardver együtt működik, ha elérhetők, és engedélyezve van a segítségével a lehető legmagasabb szintű biztonsági intézkedésekkel nyújthat.  

Az IoT Edge-biztonságkezelő feladatai (de nem kizárólag) a következők:

* Biztonságos, és az Azure IoT Edge-eszköz rendszerindítása mért.
* Identitás-eszközkiépítési és megbízható átvitelét ha vannak ilyenek.
* Üzemeltethet, és felhőszolgáltatásokhoz, mint például a Device Provisioning Service-összetevői eszköz védelmét.
* Biztonságosan kiépítése az IoT Edge-modulok a modul egyedi azonosítóval.
* A forgalomirányító eszköz hardveres legfelső szintű megbízhatóság közjegyző szolgáltatásokon keresztül.
* IoT Edge-műveletek futásidőben sértetlenségének figyelésére.

IoT Edge-biztonságkezelő három fő összetevőből áll:

* IoT Edge biztonsági démon.
* Hardveres biztonsági modul platform absztrakciós réteg (HSM-PAL).
* Nem kötelező, de erősen ajánlott hardver szilícium legfelső szintű megbízhatóság vagy HSM.

## <a name="the-iot-edge-security-daemon"></a>Az IoT Edge biztonsági démon

IoT Edge biztonsági démon a felelős a logikai műveleteket az IoT Edge biztonsági manager szoftvert.  A megbízható számítási alapja az IoT Edge-eszköz jelentős részét alkotják. 

### <a name="design-principles"></a>Tervezési alapelvek

Megfelelő és gyakorlatilag végrehajtani az Ön feladatkörei, IoT Edge biztonsági démon a célja, hogy megfelelnek a következő alapvető:

#### <a name="maximize-operational-integrity"></a>Maximalizálja a működési integritás

IoT Edge biztonsági démon a legmagasabb lehetséges belül bármely megadott legfelső szintű megbízhatóság hardver defense képességét integritási működéséhez tervezték. Megfelelő integrációnak köszönhetően a legfelső szintű megbízhatóság hardver méri, és figyeli a biztonság démon statikusan és futásidőben az illetéktelen beavatkozásoknak.

Fizikai hozzáférés, mindig IoT-eszközök fenyegetést általában.  Hardver gyökér megbízhatósági ezért fontos szerepet integritását, az IoT Edge biztonsági démon megvédésekor.  Hardver gyökér megbízhatósági érkeznek a következő két változatban érhetők el:

* biztonságos elemek, például a titkosítási kulcsok és titkok bizalmas adatok védelme.
* biztonságos enklávék, titkos kulcsok védelme, például a kulcsokat és teljesítményigényes számítási feladatokhoz, mint például a mérési és számlázási.

Létezik-e hardver gyökér megbízhatósági hasznosító két modell két különböző végrehajtási környezetekben ad lehetőséget:

* A standard vagy a látványos végrehajtási környezetben (REE) által használt, a bizalmas adatok védelme érdekében a biztonságos elemek használatát.
* A megbízható végrehajtási környezetben (TEE) által használt, biztonságos enklávé technológia segítségével bizalmas információk védelme érdekében, illetve szoftvervégrehajtás védelem használatát.

Biztonságos enklávék gyökérként használja, hardver a megbízható eszközökhöz bizalmas logikai belül az IoT Edge biztonsági démon várhatóan az enklávé belül találhatók.  A biztonsági démon bizalmas adatokkal nem rendelkező részeit a TEE kívül lehetnek.  Minden esetben várható eredeti tervezési gyártóknak (eszközgyártók általi) és a számítógépgyártó (OEM) mérjük és védi a rendszerindító és a futtatókörnyezet, az Edge biztonsági démon integritását a HSM-ből megbízhatósági kiterjeszteni.

#### <a name="minimize-bloat-and-churn"></a>Minimalizálása érdekében a növekedést és a vásárlói lemorzsolódás

Az Edge biztonsági démon egy másik core alapelve, hogy adatváltozás.  A legmagasabb szintű megbízhatóság IoT Edge biztonsági démon is szorosan összekapcsolhatja a eszköz hardveres legfelső szintű megbízhatóság, ha elérhetők, és működnek, nativní kód.  Szokás az ilyen típusú realizations frissíteni a démon szoftver révén a hardver gyökér megbízhatósági a biztonságos frissítési útvonal (szemben az operációs rendszer a megadott frissítési mechanizmusok), amely adott hardver- és telepítési forgatókönyvtől függően kihívást jelenthet.  IoT-eszközt a erős javaslat biztonsági megújítási pedig azt jelöli, hogy túl sok frissítési követelményeket vagy nagyméretű frissítések is észleltünk adattartalmakat bővítheti a fenyegetés felület számos okból.  Ilyenek például a rendszer kihagyja a rendelkezésre állás maximalizálása érdekében a frissítések vagy a gyökér megbízhatósági hardver túl nagy frissítés is észleltünk adattartalmakat feldolgozása által korlátozott.  Mint ilyen, a terv az IoT Edge biztonsági démon az, hogy az erőforrás-igényű tömör, és ezért a megbízható számítástechnika alapvető kis és frissítési követelmények minimalizálása érdekében.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge biztonsági démon architektúrája

![Az Azure IoT Edge biztonsági démon](media/edge-security-manager/iot-edge-security-daemon.png)

Az IoT Edge biztonsági démon tervezésnek kihasználásához adatvédelmi technológia, a biztonság megerősítését a rendelkezésre álló hardver legfelső szintű lesz.  Több tehát a gondos tervezésnek lesz, hogy vett split művelet (TEE) megbízható végrehajtási környezetet kínáló hardver technológiák előnyeit a egy Standard/gazdag végrehajtási környezetben (REE) és a egy megbízható végrehajtási környezetben (TEE) között.  Az IoT Edge biztonsági démon architektúrájának középpontjában engedélyezéséhez a peremhálózati eszköz és a műveletek biztosításához integritását, az IoT Edge fő összetevőinek kölcsönhatások szerepkör-specifikus felületeket.

#### <a name="cloud-interface"></a>Felhőalapú kapcsolat

A felhőbeli felület lehetővé teszi, hogy az IoT Edge biztonsági démon a felhőszolgáltatásokhoz kapcsolódik, például az eszköz biztonsági hasonló biztonsági megújítási felhőalapú üdvözli.  Például az IoT Edge biztonsági démon jelenleg ez az interfész elérésére használja az Azure IoT Hub [Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/) eszközt identitás-életciklus kezelésének.  

#### <a name="management-api"></a>Felügyeleti API

IoT Edge biztonsági démon kínál a felügyeleti API-t, amely hívja meg az Edge Agent eltávolításakor létrehozása/indítása/leállítása/egy edge-modul. Az IoT Edge biztonsági démon "regisztráció" az összes aktív modulok tárolja. Tyto registrace egy modul identitás leképezése a modul néhány tulajdonságát. Ezekhez a tulajdonságokhoz néhány példa a a tároló vagy a docker-tároló tartalmának kivonatát futó folyamatot a folyamat azonosítója (pid) lehet.

Ezek a tulajdonságok a számítási feladatot (részletesen lásd alább) igazolja, hogy a hívó jogosult-e művelet végrehajtásához az API segítségével.

A felügyeleti API-t egy olyan privileged, csak az IoT Edge-ügynöktől hívható API.  Mivel az IoT Edge biztonsági démon csatlakoztatja, és az IoT Edge agent elindul, létrehozható egy implicit regisztrációs az IoT Edge-ügynök után azt, hogy az IoT Edge-ügynök nem lett illetéktelenül rendelkezik igazolt. A számítási feladatok API-t használó igazolási eljárást korlátozza a hozzáférést a felügyeleti API-t csak az IoT Edge-ügynök szolgál.

#### <a name="container-api"></a>Tároló API

IoT Edge biztonsági démon a tároló felület kommunikál a modul által okozott például Moby és a Docker használt rendszer kínál.

#### <a name="workload-api"></a>API-számítási feladatok

A számítási feladatok API egy IoT Edge biztonsági démon API elérhető összes modult, beleértve az IoT Edge-ügynök. Az azonosításhoz, hardveres biztonsági modul formájában biztosít aláírt jogkivonat vagy X509 feltört eszköz tanúsítványt, és megfelelő megbízhatósági csomagot modulra. A megbízhatósági csomag tartalmazza a CA-tanúsítványok az összes többi kiszolgálót, hogy bízzon meg a modulok.

IoT Edge biztonsági démon az igazolási folyamat meg szeretné védeni az API-t használja. Egy modult meghívja az API-t, az IoT Edge biztonsági démon megkísérli megtalálni egy regisztrációs identitását. Sikeres művelet esetén a modul mérésére használja a regisztráció tulajdonságai. Ha az eredmény a mérési folyamat megegyezik a regisztráció, egy új HSM rootolva aláírt jogkivonat vagy X509 tanúsítvány jön létre, és a megfelelő CA-tanúsítványok (megbízhatósági csomagot) a rendszer visszalépteti a modul.  A modul csatlakoztatása az IoT hubhoz, más modulok tesznek, vagy a kiszolgáló indítása ezt a tanúsítványt használja. Ha az aláírt jogkivonat vagy a tanúsítvány lejár, feladata a modul egy új tanúsítvány kérése.  További szempontok be vannak építve a Tervező, hogy az identitás megújítási folyamatának is zökkenőmentes, amennyire csak lehetséges.

### <a name="integration-and-maintenance"></a>Integráció és karbantartás

A Microsoft fenntartja a fő kód alapjának a [IoT Edge biztonsági démon a Githubon](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Telepítés és a frissítések

Folyamatban van egy natív folyamat, telepítés és a frissítések, az IoT Edge biztonsági démon kezelik az operációs rendszer Csomagkezelő rendszeren keresztül.  Valószínű azonban, hogy IoT hardver legfelső szinttel, a megbízható eszközök él, és adja meg a további megerősítés a démon sértetlenségét kezelésével, annak életciklusát keresztül az eszközök biztonságos rendszerindítási és frissítések felügyeleti rendszerek.  Az eszközök számára, Fedezze fel a megfelelő az eszköz képességeinek megfelelően ezek rések leggyakoribb okai hagyják.

#### <a name="versioning"></a>Verziókezelés

Az IoT Edge-futtatókörnyezet nyomon követi, és az IoT Edge biztonsági démon verzióját jelenti. A verziószám az elvártnak megfelelően a *runtime.platform.version* attribútum az Edge Agent modul jelentett tulajdonság.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Hardveres biztonsági modul platform hardverabsztrakciós réteg (HSM-PAL)

A HSM-PAL kivonatolja a megbízhatósági hardver a fejlesztői vagy a felhasználó az IoT Edge a saját hagyhatják el az összes gyökerében.  Van egy alkalmazásprogramozási felületet (API) és trans tartomány kommunikációs eljárásokat, például egy standard végrehajtási környezet és a egy biztonságos enklávé közötti kommunikáció kombinációját.  A HSM-PAL tényleges végrehajtása attól függ, hogy egy adott biztonságos hardver használja.  A létezésük IoT ökoszisztémájában gyakorlatilag bármilyen biztonságos szilícium hardver használatát teszi lehetővé.

## <a name="secure-silicon-root-of-trust-hardware"></a>Biztonságos szilícium gyökér megbízhatósági hardver

Biztonságos szilícium forráshorgony megbízhatósági belül az IoT Edge-eszköz hardveres szükség.  Biztonságos szilícium tartalmazza a platformmegbízhatósági modul (TPM), beágyazott biztonságos elem (eSE), ARM TrustZone, Intel SGX és egyéni biztonságos szilícium technológiák számos különböző származnak.  Biztonságos szilícium főtanúsítvány az eszközök megbízhatósági használata erősen ajánlott a fenyegetések fizikailag IoT-eszközök kisegítő társított megadott.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge biztonsági manager integrálása és karbantartás

Az IoT Edge biztonsági manager egyik fő célja az identitáshoz és elkülönítheti az összetevőket a biztonságának és integritásának a egyéni vezethet be az Azure IoT Edge platform védelme biztosítja.  Ilyen valószínű, harmadik felek, például az eszközkészítők győződjön meg arról, hogy az eszköz hardveradatokkal egyéni biztonsági szolgáltatásait használja.  Tekintse meg a további lépések szakaszt alább a példához az Azure IoT-biztonságkezelő az a platformmegbízhatósági modul (TPM) a Linux és Windows platformokon felvértezni való hivatkozásokat.  Ezekben a példákban használja a szoftvert vagy virtuális TPM, de diszkrét TPM-eszközök segítségével közvetlenül vonatkozik.  

## <a name="next-steps"></a>További lépések

Olvassa el a blogban a [intelligens peremhálózat biztosítása](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Hozzon létre, és az Edge-eszköz kiépítése egy [a Linux rendszerű virtuális gép virtuális TPM](how-to-auto-provision-simulated-device-linux.md).

Létrehozása és kiépítése az egy [szimulált TPM-eszköz peremhálózati eszköz, a Windows](how-to-auto-provision-simulated-device-windows.md).

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/

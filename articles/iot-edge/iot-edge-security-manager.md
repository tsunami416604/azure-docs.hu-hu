---
title: Ismerje meg, hogyan védi a kezelő az eszközök, szoftverek – Azure IoT Edge |} A Microsoft Docs
description: Az IoT Edge-eszköz biztonsági forgalmazóval és biztonsági szolgáltatások biztonságának kezelése.
services: iot-edge
keywords: biztonság, a biztonságos elem, az enklávé, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f137070cb8a62f2c11f9e2688b5c7db47c1b866f
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208217"
---
# <a name="azure-iot-edge-security-manager"></a>Az Azure IoT Edge-biztonságkezelő

Az Azure IoT Edge biztonsági manager egy jól körülhatárolható biztonsági alapvető védelme érdekében az IoT Edge-eszköz és az összes összetevői által a biztonságos szilícium hardver paltformfüggetlen. Ez a biztonság megerősítésének középpontja, és technológiai integrációs pontot biztosít az eredeti berendezésgyártó (OEM) számára.

![Az Azure IoT Edge-biztonságkezelő](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge-biztonságkezelő célja, hogy az IoT Edge-eszköz és minden szoftver rejlő művelet integritásának védelemre. A Security Manager megbízik az alapul szolgáló hardver gyökerétől (ha elérhető) a IoT Edge futtatókörnyezet rendszerindításához és a folyamatban lévő műveletek figyeléséhez.  A IoT Edge Security Manager olyan szoftver, amely biztonságos szilícium-hardverrel (ahol elérhető) segíti a lehető legmagasabb szintű biztonsági garanciák nyújtását.  

A IoT Edge Security Manager feladatai közé tartoznak a következők:

* Biztonságos, és az Azure IoT Edge-eszköz rendszerindítása mért.
* Identitás-eszközkiépítési és megbízható átvitelét ha vannak ilyenek.
* Üzemeltethet, és felhőszolgáltatásokhoz, mint például a Device Provisioning Service-összetevői eszköz védelmét.
* IoT Edge-modulok biztonságos kiépítése egyedi identitásokkal.
* A forgalomirányító eszköz hardveres legfelső szintű megbízhatóság közjegyző szolgáltatásokon keresztül.
* IoT Edge-műveletek futásidőben sértetlenségének figyelésére.

IoT Edge-biztonságkezelő három összetevőt tartalmaz:

* IoT Edge biztonsági démon.
* Hardveres biztonsági modul platform absztrakciós réteg (HSM-PAL).
* Nem kötelező, de erősen ajánlott hardver szilícium legfelső szintű megbízhatóság vagy HSM.

## <a name="the-iot-edge-security-daemon"></a>Az IoT Edge biztonsági démon

A IoT Edge biztonsági démon felelős a IoT Edge Security Manager logikai műveleteiért. Ez a IoT Edge eszköz megbízható számítástechnikai alapjainak jelentős részét képezi. 

### <a name="design-principles"></a>Tervezési alapelvek

A IoT Edge biztonsági démon két alapvető alapelvet követ: maximalizálja a működési integritást, és csökkentse a duzzadás és a forgalom minimalizálását.

#### <a name="maximize-operational-integrity"></a>Maximalizálja a működési integritás

A IoT Edge biztonsági démon a lehető legnagyobb integritással működik a megbízhatósági kapcsolatok bármelyik gyökerének védelmi képességein belül. Megfelelő integrációnak köszönhetően a legfelső szintű megbízhatóság hardver méri, és figyeli a biztonság démon statikusan és futásidőben az illetéktelen beavatkozásoknak.

A fizikai hozzáférés mindig fenyegetést jelent az eszközök IoT. A megbízható hardver gyökerének fontos szerepet játszik a IoT Edge biztonsági démon integritásának védelmében.  A megbízható hardver gyökerének két fajtája van:

* biztonságos elemek, például a titkosítási kulcsok és titkok bizalmas adatok védelme.
* biztonságos enklávék, titkos kulcsok védelme, például a kulcsokat és teljesítményigényes számítási feladatokhoz, mint például a mérési és számlázási.

A megbízhatósági kapcsolat gyökerének kétféle végrehajtási környezete létezik:

* A bizalmas adatok védelme érdekében a biztonságos elemek használatára támaszkodó standard vagy sokoldalú végrehajtási környezet (REE).
* Az a megbízható végrehajtási környezet (TEE), amely a biztonságos enklávé technológiájának használatára támaszkodik a bizalmas információk védelme érdekében, és védelmet nyújt a szoftverek végrehajtásához.

Az olyan eszközök esetében, amelyekben a biztonságos enklávék a megbízhatóság gyökerének minősülnek, IoT Edge biztonsági démonon belüli bizalmas logikának az enklávéban kell lennie.  A biztonsági démon nem bizalmas részei a PÓLÓn kívül is lehetnek.  Az eredeti tervezési gyártók (ODM-EK) és az eredeti berendezésgyártók (OEM-ek) minden esetben kiterjesztik a megbízhatóságot a HSM-ből a rendszerindítási és futtatókörnyezeti IoT Edge biztonsági démon integritásának méréséhez és védelméhez.

#### <a name="minimize-bloat-and-churn"></a>Minimalizálása érdekében a növekedést és a vásárlói lemorzsolódás

A IoT Edge biztonsági démon egy másik alapelve, hogy csökkentse a forgalom minimalizálását.  A legmagasabb szintű megbízhatóság érdekében a IoT Edge biztonsági démon szorosan összekapcsolhatja az eszköz hardveres megbízhatósági gyökerét, és natív kóddal működhet.  Az ilyen típusú felismerések esetében gyakori, hogy a démoni szoftvereket a megbízhatósági kapcsolatok biztonságos frissítésének elérési útjai (az operációs rendszer által megadott frissítési mechanizmusok helyett) segítségével frissítsék, ami bizonyos helyzetekben kihívást jelenthet.  Míg a IoT-eszközök biztonsági megújítása ajánlott, a túlzott frissítési követelmények és a nagyméretű frissítési adattartalom számos módon kiterjeszthető a veszélyforrások felületére.  Ilyenek például a rendszer kihagyja a rendelkezésre állás maximalizálása érdekében a frissítések vagy a gyökér megbízhatósági hardver túl nagy frissítés is észleltünk adattartalmakat feldolgozása által korlátozott.  Ennek megfelelően a IoT Edge biztonsági démon kialakítása tömören tartja a lábnyomot és a megbízható számítástechnikai alapot, és a frissítési követelmények minimalizálására.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge biztonsági démon architektúrája

![Az Azure IoT Edge biztonsági démon](media/edge-security-manager/iot-edge-security-daemon.png)

A IoT Edge biztonsági démon kihasználja a megbízhatósági technológia összes rendelkezésre álló hardveres gyökerét a biztonság megerősítéséhez.  Emellett lehetővé teszi a megosztott működést a standard/sokoldalú végrehajtási környezet (REE) és egy megbízható végrehajtási környezet (TEE) között, amikor a hardveres technológiák megbízható végrehajtási környezeteket kínálnak. A szerepkör-specifikus felületek lehetővé teszik a IoT Edge fő összetevőinek a IoT Edge eszköz és a műveletei integritásának biztosítását.

#### <a name="cloud-interface"></a>Felhőalapú kapcsolat

A Cloud Interface lehetővé teszi, hogy a IoT Edge biztonsági démon hozzáférjen a Cloud Serviceshez, például a Felhőbeli bókohoz, hogy az eszköz biztonsága, például a biztonsági megújítás.  Például a IoT Edge biztonsági démon jelenleg ezt a felületet használja az Azure IoT Hub [Device kiépítési szolgáltatáshoz](https://docs.microsoft.com/azure/iot-dps/) való hozzáféréshez az eszköz identitásának életciklus-kezeléséhez.  

#### <a name="management-api"></a>Felügyeleti API

IoT Edge Security Daemon egy felügyeleti API-t kínál, amelyet az IoT Edge ügynök hív meg egy IoT Edge modul létrehozásakor/elindításakor/leállításakor vagy eltávolításakor. A biztonsági démon az összes aktív modul "regisztrációját" tárolja. Tyto registrace egy modul identitás leképezése a modul néhány tulajdonságát. Ezekhez a tulajdonságokhoz néhány példa a a tároló vagy a docker-tároló tartalmának kivonatát futó folyamatot a folyamat azonosítója (pid) lehet.

Ezeket a tulajdonságokat a munkaterhelés API (lásd alább) használja annak ellenőrzéséhez, hogy a hívó jogosult-e a művelet végrehajtására.

A felügyeleti API egy kiemelt API, amely csak a IoT Edge ügynöktől hívható meg.  Mivel az IoT Edge biztonsági démon csatlakoztatja, és az IoT Edge agent elindul, létrehozható egy implicit regisztrációs az IoT Edge-ügynök után azt, hogy az IoT Edge-ügynök nem lett illetéktelenül rendelkezik igazolt. Ugyanez az igazolási folyamat, amelyet a számítási feladatok API használ, korlátozza a felügyeleti API elérését is, hogy csak a IoT Edge ügynökhöz.

#### <a name="container-api"></a>Tároló API

A Container API a modul felügyeletéhez használt tárolórendszer (például a Moby vagy a Docker) használatával működik együtt.

#### <a name="workload-api"></a>API-számítási feladatok

A számítási feladatok API minden modul számára elérhető. Az identitást a HSM-feltört aláírt tokenként vagy egy X509-tanúsítványként, valamint a megfelelő megbízhatósági csomagban adja meg egy modulnak. A megbízhatósági csomag HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat tartalmaz az összes többi kiszolgálónak, amelyeknek a moduloknak megbízhatónak kell lenniük.

A IoT Edge Security Daemon egy igazolási folyamattal fogja védeni ezt az API-t. Amikor egy modul meghívja ezt az API-t, a biztonsági démon megpróbálja megkeresni az identitás regisztrációját. Sikeres művelet esetén a modul mérésére használja a regisztráció tulajdonságai. Ha a mérési folyamat eredménye megegyezik a regisztrációval, a rendszer új identitást hoz létre. A rendszer visszaadja a megfelelő HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat (megbízhatósági csomagot) a modulnak.  A modul csatlakoztatása az IoT hubhoz, más modulok tesznek, vagy a kiszolgáló indítása ezt a tanúsítványt használja. Ha az aláírt jogkivonat vagy tanúsítvány hamarosan lejár, a modul feladata az új tanúsítvány igénylése. 

### <a name="integration-and-maintenance"></a>Integráció és karbantartás

A Microsoft fenntartja a fő kód alapjának a [IoT Edge biztonsági démon a Githubon](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Telepítés és a frissítések

A IoT Edge biztonsági démon telepítése és frissítései az operációs rendszer csomagkezelő rendszerán keresztül kezelhetők. IoT Edge az eszközöket, amelyekhez a megbízhatósági kapcsolat szükséges, meg kell adnia a démon integritását azáltal, hogy az életciklusát a biztonságos rendszerindítási és frissítési felügyeleti rendszerek segítségével kezeli. Az eszközöknek a megfelelő eszköz képességei alapján kell megvizsgálniuk ezeket az eszközöket.

#### <a name="versioning"></a>Verziókezelés

Az IoT Edge-futtatókörnyezet nyomon követi, és az IoT Edge biztonsági démon verzióját jelenti. A verzió jelentése a IoT Edge Agent modul jelentett tulajdonságának *futtatókörnyezet. platform. Version* attribútuma.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Hardveres biztonsági modul platform hardverabsztrakciós réteg (HSM-PAL)

A HSM-PAL kivonatolja a megbízhatósági hardver a fejlesztői vagy a felhasználó az IoT Edge a saját hagyhatják el az összes gyökerében.  Az API-k és a tartományok közötti kommunikációs eljárások kombinációját foglalja magában, például a szabványos végrehajtási környezet és a biztonságos enklávé közötti kommunikációt.  A HSM-PAL tényleges végrehajtása attól függ, hogy egy adott biztonságos hardver használja. Létezése lehetővé teszi a gyakorlatilag bármilyen biztonságos szilícium-hardver használatát.

## <a name="secure-silicon-root-of-trust-hardware"></a>Biztonságos szilícium gyökér megbízhatósági hardver

Biztonságos szilícium forráshorgony megbízhatósági belül az IoT Edge-eszköz hardveres szükség.  Biztonságos szilícium tartalmazza a platformmegbízhatósági modul (TPM), beágyazott biztonságos elem (eSE), ARM TrustZone, Intel SGX és egyéni biztonságos szilícium technológiák számos különböző származnak.  Az eszközök biztonságos szilícium-megbízhatósági gyökerének használata ajánlott a IoT-eszközök fizikai hozzáférhetőségével kapcsolatos fenyegetések miatt.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge biztonsági manager integrálása és karbantartás

A IoT Edge Security Manager célja a Azure IoT Edge platform biztonságának és integritásának védelmét biztosító összetevők azonosítása és elkülönítése az egyéni megerősítés érdekében. A harmadik feleknek, például az eszközök gyártóinak az eszköz hardverével elérhető egyéni biztonsági funkciókat kell használniuk.  Az Azure IoT Security Manager a Linux és a Windows rendszerű platformon platformmegbízhatósági modul (TPM) használatával történő megerősítését bemutató hivatkozások a következő lépések szakaszban olvashatók. Ezek a példák szoftvereket vagy virtuális TPM használnak, de közvetlenül a különálló TPM-eszközök használatára vonatkoznak.  

## <a name="next-steps"></a>További lépések

Olvassa el a blogban a [intelligens peremhálózat biztosítása](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Virtuális TPM-sel rendelkező IoT Edge-eszköz létrehozása és kiépítése [Linux rendszerű virtuális gépen](how-to-auto-provision-simulated-device-linux.md).

[IoT Edge eszköz](how-to-auto-provision-simulated-device-windows.md)létrehozása és kiépítése szimulált TPM-sel Windows rendszeren.

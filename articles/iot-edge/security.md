---
title: Biztonsági keretrendszer - Azure IoT Edge | Microsoft dokumentumok
description: Ismerje meg az Azure IoT Edge fejlesztéséhez használt biztonsági, hitelesítési és engedélyezési szabványokat, amelyeket a megoldás tervezésekor figyelembe kell venni
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3212493963805de3c8845ec494d87fc92d72998a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760113"
---
# <a name="security-standards-for-azure-iot-edge"></a>Az Azure IoT Edge biztonsági szabványai

Az Azure IoT Edge az adatok és elemzések intelligens peremhálózatba való áthelyezése során rejlő kockázatokat kezeli. Az IoT Edge biztonsági szabványai egyensúlyt teremtenek a különböző üzembe helyezési forgatókönyvek rugalmassága és az összes Azure-szolgáltatás által elvárt védelem között.

Az IoT Edge különböző hardvergyártmányokon és modelleken fut, számos operációs rendszert támogat, és különböző telepítési forgatókönyvekre vonatkozik. Ahelyett, hogy konkrét megoldásokat kínálna konkrét forgatókönyvekhez, az IoT Edge egy bővíthető biztonsági keretrendszer, amely jól megalapozott elveken alapul, amelyek et méretezésre terveztek. A telepítési forgatókönyv kockázata számos tényezőtől függ, többek között a következőktől:

* Megoldás tulajdonjoga
* Telepítési földrajzi hely
* Adatérzékenység
* Adatvédelem
* Alkalmazás függőleges
* Szabályozási követelmények

Ez a cikk áttekintést nyújt az IoT Edge biztonsági keretrendszer. További információ: [Az intelligens perem hálózat ának védelme.](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)

## <a name="standards"></a>Szabványok

A szabványok elősegítik a könnyű ellenőrzést és a könnyű végrehajtást, mindkettő a biztonság fémjelzi. A biztonsági megoldásnak alkalmasnak kell lennie a kiértékelés alatt álló vizsgálatra a bizalom kiépítése érdekében, és nem akadályozhatja a telepítést. Az Azure IoT Edge-et biztonságossá tenni a keretrendszer kialakítása, amely jól bevált és az iparágban bevált biztonsági protokollokon alapul az ismerősség és az újrahasználat érdekében.

## <a name="authentication"></a>Hitelesítés

IoT-megoldás üzembe helyezésekor tudnia kell, hogy csak a megbízható szereplők, eszközök és modulok férhetnek hozzá a megoldáshoz. Tanúsítványalapú hitelesítés az Azure IoT Edge platform elsődleges hitelesítési mechanizmusa. Ez a mechanizmus az Internet Engineering Task Force (IETF) által a nyilvános kulcsú infrastruktúrára (PKiX) vonatkozó szabványok ból származik.

Az Azure IoT Edge-eszközzel interakcióba lépő összes eszköznek, modulnak és szereplőnek egyedi tanúsítványidentitással kell rendelkeznie. Ez az útmutató arról van szó, hogy a kapcsolati tevékenységek fizikai vagy hálózati kapcsolaton keresztül történnek. Nem minden forgatókönyv vagy összetevő alkalmas arra, hogy tanúsítványalapú hitelesítést, így a bővíthetősége a biztonsági keretrendszer biztonságos alternatívákat kínál.

További információ: [Azure IoT Edge tanúsítvány használata.](iot-edge-certs.md)

## <a name="authorization"></a>Engedélyezés

A minimális jogosultság elve azt mondja, hogy a felhasználók és a rendszer összetevői csak a szerepkörük höz szükséges minimális erőforrásokhoz és adatokhoz férhetnek hozzá. Eszközök, modulok és szereplők csak az erőforrásokhoz és adatokhoz férhetnek hozzá az engedély hatókörén belül, és csak akkor, ha azok építészetileg megengedett. Egyes engedélyek megfelelő jogosultságokkal konfigurálhatók, míg mások architekturálisan érvényesíthetők. Előfordulhat például, hogy egyes modulok jogosultak az Azure IoT Hubhoz való csatlakozásra. Azonban nincs ok, amiért egy modul egy IoT Edge-eszköz egy modul egy másik IoT Edge-eszköz két.

Más engedélyezési rendszerek közé tartozik a tanúsítvány aláíró jogok és a szerepkör-alapú hozzáférés-vezérlés (RBAC).

## <a name="attestation"></a>Igazolás

A tanúsítvány biztosítja a szoftverbitek integritását, ami fontos a rosszindulatú programok észleléséhez és megelőzéséhez. Az Azure IoT Edge biztonsági keretrendszer három fő kategóriába sorolja a tanúsítványt:

* Statikus tanúsítvány
* Futásidejű tanúsítvány
* Szoftverigazolás

### <a name="static-attestation"></a>Statikus tanúsítvány

A statikus tanúsítvány ellenőrzi az eszközön lévő összes szoftver integritását a bekapcsolás során, beleértve az operációs rendszert, az összes futási időt és a konfigurációs adatokat. Mivel a statikus tanúsítvány a bekapcsolás során történik, gyakran biztonságos rendszerindításnak nevezik. Az IoT Edge-eszközök biztonsági keretrendszere kiterjed a gyártókra, és olyan biztonságos hardverképességeket tartalmaz, amelyek biztosítják a statikus tanúsítványkezelési folyamatokat. Ezek a folyamatok közé tartozik a biztonságos rendszerindítás és a biztonságos firmware-frissítés. A szilíciumgyártókkal szoros együttműködésben végzett munka kiküszöböli a felesleges belső vezérlőprogram-rétegeket, így minimálisra csökkenti a fenyegetésfelületet.

### <a name="runtime-attestation"></a>Futásidejű tanúsítvány

Miután a rendszer befejezte a biztonságos rendszerindítási folyamatot, a jól megtervezett rendszereknek észlelniük kell a rosszindulatú programok befecskendezésére tett kísérleteket, és megfelelő ellenintézkedéseket kell tenniük. A rosszindulatú támadások a rendszer portjait és felületeit célozhatják meg. Ha a rosszindulatú szereplők fizikai hozzáféréssel rendelkeznek egy eszközhöz, akkor manipulálhatják magát az eszközt, vagy oldalcsatornás támadásokat használhatnak a hozzáféréshez. Az ilyen elégedetlenség, függetlenül attól, hogy rosszindulatú programok vagy jogosulatlan konfigurációs változások, nem észlelhető statikus tanúsítvány, mert a rendszerindítási folyamat után injektált. Az eszköz hardvere által kínált vagy végrehajtott ellenintézkedések segítenek az ilyen fenyegetések elhárításában. Az IoT Edge biztonsági keretrendszere kifejezetten olyan bővítményeket kér, amelyek leküzdik a futásidejű fenyegetéseket.  

### <a name="software-attestation"></a>Szoftverigazolás

Minden egészséges rendszernek, beleértve az intelligens peremhálózati rendszereket is, javításra és frissítésre van szüksége. A frissítési folyamatok biztonsága fontos, különben potenciális fenyegetési vektorok lehetnek. Az IoT Edge biztonsági keretrendszere mért és aláírt csomagokon keresztül kéri a frissítéseket a csomagok integritásának biztosítása és hitelesítése érdekében. Ez a szabvány minden operációs rendszerre és alkalmazásszoftver-bitre vonatkozik.

## <a name="hardware-root-of-trust"></a>A megbízhatóság hardvergyöke

Számos intelligens peremhálózati eszköz, különösen a potenciális rosszindulatú szereplők által fizikailag elérhető eszközök esetében a hardverbiztonság az utolsó védelem. A szabotázsálló hardver elengedhetetlen az ilyen telepítésekhez. Az Azure IoT Edge arra ösztönzi a biztonságos szilíciumhardver-gyártókat, hogy különböző szintű hardveres megbízhatósági gyökerét kínálják a különböző kockázati profilok és telepítési forgatókönyvek befogadására. A hardvermegbízhatóság származhat olyan közös biztonsági protokollszabványokból, mint a platformmegbízhatósági modul (ISO/IEC 11889) és a Trusted Computing Group eszközazonosító-összetételi motorja (DICE). A biztonságos enklávé technológiák, mint a TrustZones és a Software Guard Extensions (SGX) szintén hardvermegbízhatóságot biztosítanak.

## <a name="certification"></a>Tanúsítvány

Annak érdekében, hogy az ügyfelek megalapozott döntéseket hozhassanak az Azure IoT Edge-eszközök üzembe helyezéséhez történő beszerzésekor, az IoT Edge-keretrendszer minősítési követelményeket is tartalmaz. Ezeka követelmények alapvető tanúsítványok kapcsolatos biztonsági jogcímek és tanúsítványok vonatkozó érvényesítése a biztonsági megvalósítás. Például egy biztonsági jogcím tanúsítás azt jelenti, hogy az IoT Edge-eszköz olyan biztonságos hardvert használ, amelyről ismert, hogy ellenáll a rendszerindítási támadásoknak. Az érvényesítési tanúsítvány azt jelenti, hogy a biztonságos hardver megfelelően lett megvalósítva, hogy ezt az értéket az eszközön ajánlja fel. Az egyszerűség elvének tiszteletben tartása mellett a keretrendszer igyekszik minimálisszinten tartani a tanúsítás terhét.

## <a name="extensibility"></a>Bővíthetőség

Mivel az IoT-technológia különböző típusú üzleti átalakításokat hajt végre, a biztonságnak párhuzamosan kell fejlődnie a felmerülő forgatókönyvek kezelése érdekében. Az Azure IoT Edge biztonsági keretrendszer egy szilárd alappal kezdődik, amelyre bővíthetőségben különböző dimenziókba épít, hogy tartalmazza a következőket:

* A külső biztonsági szolgáltatások, például az Azure IoT Hub eszközkiépítési szolgáltatás.
* Harmadik féltől származó szolgáltatások, mint például a különböző alkalmazási vertikális (például ipari vagy egészségügyi) felügyelt biztonsági szolgáltatások vagy technológiai fókusz (például a hálóhálózatok biztonsági figyelése vagy a szilíciumhardver-igazolási szolgáltatások) a gazdag hálózaton keresztül Partnerek.
* Örökölt rendszerek, amelyek alternatív biztonsági stratégiákkal való utólagos felszerelést tartalmaznak, például a tanúsítványoktól eltérő biztonságos technológiát használnak a hitelesítéshez és az identitáskezeléshez.
* Biztonságos hardver a kialakulóban lévő biztonságos hardvertechnológiák és a szilíciumpartner-hozzájárulások bevezetéséhez.

Végül az intelligens peremhálózat biztosításához egy nyitott közösség együttműködésen alapuló hozzájárulása szükséges, amelyet az IoT biztosításához fűződő közös érdek vezérel. Ezek a hozzájárulások biztonságos technológiák vagy szolgáltatások formájában valósulhatnak meg. Az Azure IoT Edge biztonsági keretrendszer szilárd alapot biztosít a biztonság, amely bővíthető a maximális lefedettséget, hogy ugyanazt a megbízhatóságot és integritást az intelligens peremhálózat, mint az Azure felhő.  

## <a name="next-steps"></a>További lépések

További információ arról, hogy az Azure IoT Edge hogyan [biztosítja az intelligens peremhálózatot.](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)

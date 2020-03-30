---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793131"
---
A dolgok internete (IoT) infrastruktúrájának biztonságossá tétele szigorú, mélyreható biztonsági stratégiával rendelkezik. Ez a stratégia megköveteli, hogy a felhőben lévő adatok védelme, az adatok integritásának védelme a nyilvános interneten keresztül történő átvitel során, és biztonságosan üzembe eszközök. Minden réteg nagyobb biztonságot nyújt a teljes infrastruktúrában.

## <a name="secure-an-iot-infrastructure"></a>IoT-infrastruktúra védelme

Ez a részletes biztonsági stratégia az IoT-eszközök és infrastruktúra gyártásában, fejlesztésében és üzembe helyezésében részt vevő különböző szereplők aktív részvételével fejleszthető és hajtható végre. A következőkben egy magas szintű leírása ezeknek a játékosoknak.

* **IoT hardvergyártó/-integrátor**: Ezek a lejátszók általában az üzembe helyezett IoT-hardverek, a különböző gyártóktól származó integrátorok vagy beszállítók gyártói, akik hardvert biztosítanak más beszállítók által gyártott vagy integrált IoT-telepítéshez.

* **IoT-megoldás fejlesztője:** Az IoT-megoldás fejlesztését általában egy megoldásfejlesztő végzi. Ez a fejlesztő része lehet egy házon belüli csapatnak vagy egy rendszerintegrátornak (SI), amely erre a tevékenységre specializálódott. Az IoT-megoldás fejlesztője az IoT-megoldás különböző összetevőit a semmiből fejlesztheti ki, integrálhat különböző off-the-shelf vagy nyílt forráskódú összetevőket, vagy kisebb adaptációval rendelkező megoldásgyorsítókat alkalmazhat.

* **IoT-megoldás üzembe helyező:** Miután egy IoT-megoldás fejlesztése, azt kell telepíteni a területen. Ez a folyamat magában foglalja a hardver ek telepítését, az eszközök összekapcsolását és a megoldások telepítését a hardvereszközökön vagy a felhőben.

* **IoT-megoldás operátor:** Az IoT-megoldás üzembe helyezése után hosszú távú műveleteket, figyelést, frissítéseket és karbantartást igényel. Ezeket a feladatokat egy házon belüli csapat végezheti el, amely informatikai szakemberekből, hardverüzemeltetési és karbantartási csapatokból, valamint az általános IoT-infrastruktúra megfelelő viselkedését figyelő tartományszakértőkből áll.

Az alábbi szakaszok az egyes játékosok hoz a legjobb gyakorlatokat a biztonságos IoT-infrastruktúra fejlesztéséhez, üzembe helyezéséhez és üzemeltetéséhez.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT hardver gyártó/integrátor

Az alábbiakban az IoT-hardvergyártók és a hardverintegrátorok ajánlott eljárásai találhatók.

* **A hardver hatóköre a minimális követelményekhez**: A hardverkialakításnak tartalmaznia kell a hardver működéséhez szükséges minimális szolgáltatásokat, és semmi mást. Ilyen például az USB-portok felvétele, ha az eszköz működéséhez szükséges. Ezek a további funkciók megnyitják az eszközt a nem kívánt támadási vektorok számára, amelyeket el kell kerülni.

* **Hardverhamisíthatatlanság:** Építsen be olyan mechanizmusokat, amelyek észlelik a fizikai illetéktelen beavatkozást, például felnyitják vagy eltávolítják az eszköz egy részét. Ezek a szabotázsjelek a felhőbe feltöltött adatfolyam részét képezik, amely figyelmeztetheti az operátorokat ezekre az eseményekre.

* **Biztonságos hardver köré épülhet:** Ha az ELÁBÉ lehetővé teszi, hozzon létre olyan biztonsági funkciókat, mint a biztonságos és titkosított tárolás, vagy a platformmegbízhatósági modulon (TPM) alapuló rendszerindítási funkció. Ezek a funkciók biztonságosabbá teszik az eszközöket, és segítenek megvédeni a teljes IoT-infrastruktúrát.

* **A frissítések biztonságossá tétele**: A firmware-frissítések az eszköz élettartama alatt elkerülhetetlenek. A frissítésekhez biztonságos elérési utakat biztosító eszközök kiépítése és a belső vezérlőprogram-verziók kriptográfiai biztosítása lehetővé teszi az eszköz biztonságát a frissítések alatt és után.

## <a name="iot-solution-developer"></a>IoT-megoldás fejlesztője

Az alábbiakban az IoT-megoldások fejlesztőinek ajánlott eljárásokat teket tetszetős eljárásai:

* **Kövesse a biztonságos szoftverfejlesztési módszertant**: A biztonságos szoftverek fejlesztése a biztonságról való megalapozott gondolkodást igényel, a projekt kezdetétől egészen a megvalósításig, tesztelésig és üzembe helyezéséig. A platformok, nyelvek és eszközök választását ez a módszertan befolyásolja. A Microsoft biztonsági fejlesztési életciklusa lépésről lépésre bemutatja a biztonságos szoftverek készítését.

* **Válassza ki a nyílt forráskódú szoftverek óvatosan**: Nyílt forráskódú szoftver lehetőséget nyújt a gyors megoldások at. Nyílt forráskódú szoftverek kiválasztásakor vegye figyelembe a közösség tevékenységi szintjét az egyes nyílt forráskódú összetevők esetében. Az aktív közösség biztosítja a szoftverek támogatását, valamint a problémák felderítését és megoldását. Másik lehetőségként előfordulhat, hogy egy homályos és inaktív nyílt forráskódú szoftverprojekt nem támogatott, és a problémák valószínűleg nem kerülnek felderítésre.

* **Integrálás óvatosan:** Számos szoftverbiztonsági hiba létezik a könyvtárak és API-k határán. Előfordulhat, hogy az aktuális központi telepítéshez nem szükséges funkciók továbbra is elérhetők egy API-rétegen keresztül. Az általános biztonság érdekében ellenőrizze az integrált összetevők összes felületét a biztonsági hibák szempontjából.

## <a name="iot-solution-deployer"></a>IoT-megoldás-üzembe helyező

Az alábbiakban az IoT-megoldás üzembe helyezőinek ajánlott eljárásait tetszetős eljárásokkal kell elkövetni:

* **Hardver biztonságos telepítése:** Az IoT-telepítések szükség lehet hardver telepítése nem biztonságos helyeken, például nyilvános helyeken vagy felügyelet nélküli területi beállítások. Ilyen esetekben győződjön meg arról, hogy a hardver telepítése a lehető legnagyobb mértékben hamisíthatatlan. Ha usb vagy más port oka van a hardveren, győződjön meg arról, hogy azok biztonságosan vannak lefedve. Sok támadási vektor használhatja ezeket belépési pontként.

* **A hitelesítési kulcsok biztonságának megőrzése**: A telepítés során minden eszközhöz eszközazonosítók és a felhőszolgáltatás által létrehozott kapcsolódó hitelesítési kulcsok szükségesek. Tartsa ezeket a kulcsokat fizikailag biztonságos még a telepítés után is. Bármely feltört kulcs rosszindulatú eszköz segítségével meglévő eszköznek álcázhatja magát.

## <a name="iot-solution-operator"></a>IoT-megoldás operátor

Az alábbiakban az IoT-megoldás-operátorok ajánlott eljárásait taszák meg:

* **A rendszer naprakészen tartása**: Győződjön meg arról, hogy az eszközoperációs rendszerek és az összes eszközillesztő a legújabb verzióra frissül. Ha bekapcsolja az automatikus frissítéseket a Windows 10-ben (IoT vagy más termékkezelő készletek), a Microsoft naprakészen tartja azt, és biztonságos operációs rendszert biztosít az IoT-eszközök számára. Más operációs rendszerek (például a Linux) naprakészen tartása segít biztosítani, hogy azok is védve legyenek a rosszindulatú támadásokkal szemben.

* **A kártékony tevékenységek elleni védelem**: Ha az operációs rendszer lehetővé teszi, telepítse a legújabb víruskereső és kártevőirtó képességeket minden eszköz operációs rendszerére. Ez a gyakorlat segíthet a legtöbb külső fenyegetés csökkentésében. A legtöbb modern operációs rendszert megvédheti a fenyegetésektől a megfelelő lépések megtétele által.

* **Gyakran naplózva:** Az IoT-infrastruktúra naplózása a biztonsággal kapcsolatos problémák esetén kulcsfontosságú a biztonsági eseményekre adott válaszként. A legtöbb operációs rendszer beépített eseménynaplózást biztosít, amelyet gyakran felül kell vizsgálni, hogy megbizonyosodjon arról, hogy nem történt biztonsági rés. Naplózási információk külön telemetriai adatfolyamként küldhetők a felhőszolgáltatásba, ahol elemezhetők.

* **Az IoT-infrastruktúra fizikai védelme**: Az IoT-infrastruktúra elleni legsúlyosabb biztonsági támadások az eszközökfizikai elérésével indulnak el. Az egyik fontos biztonsági gyakorlat az USB-portok rosszindulatú használata és más fizikai hozzáférés elleni védelem. Az esetlegesen előforduló szabálysértések feltárásának egyik kulcsa a fizikai hozzáférés naplózása, például az USB-port használata. A Windows 10 (IoT és más sk-ek) ismét lehetővé teszi ezen események részletes naplózását.

* **Felhőbeli hitelesítő adatok védelme:** Az IoT-telepítés konfigurálására és működtetésére használt felhőalapú hitelesítési hitelesítő adatok valószínűleg a legegyszerűbb módja az IoT-rendszer elérésének és feltörésének. A hitelesítő adatok védelme a jelszó gyakori módosításával, és ne használja ezeket a hitelesítő adatokat nyilvános gépeken.

A különböző IoT-eszközök képességei eltérőek lehetnek. Előfordulhat, hogy egyes eszközök általános asztali operációs rendszereket futtató számítógépek, és egyes eszközök nagyon könnyű operációs rendszereket futtatnak. A korábban ismertetett biztonsági gyakorlati tanácsok különböző mértékben alkalmazhatók lehetnek ezekre az eszközökre. Ha rendelkezésre áll, az ezen eszközök gyártóitól származó további biztonsági és üzembe helyezési gyakorlati tanácsokat kell követni.

Előfordulhat, hogy néhány örökölt és korlátozott eszköz nem kifejezetten az IoT üzembe helyezéséhez lett tervezve. Előfordulhat, hogy ezek az eszközök nem képesek az adatok titkosítására, az internethez való csatlakozásra vagy a speciális naplózásra. Ezekben az esetekben egy modern és biztonságos helyszíni átjáró összesítheti az örökölt eszközökről származó adatokat, és biztosíthatja az eszközök interneten keresztültörténő csatlakoztatásához szükséges biztonságot. A helyszíni átjárók biztonságos hitelesítést, titkosított munkamenetek egyeztetését, parancsok fogadását a felhőből és sok más biztonsági funkciót biztosíthatnak.
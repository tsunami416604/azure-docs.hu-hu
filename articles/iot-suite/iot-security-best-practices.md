---
title: "Az IoT ajánlott biztonsági eljárások |} Microsoft Docs"
description: "Ajánlott biztonsági eljárások az IoT-infrastruktúra védelmének biztosítása"
services: 
suite: iot-suite
documentationcenter: 
author: YuriDio
manager: timlt
editor: 
ms.assetid: 24e7bda2-5f7b-44e3-b8af-761abd3276ff
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: yurid
ms.openlocfilehash: dcab91856bcebb8f3bfab8cc69b63fad487f8ace
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="internet-of-things-security-best-practices"></a>Az eszközök internetes hálózatát ajánlott biztonsági eljárások
Egy az eszközök internetes hálózatát (IoT) biztonságos infrastruktúra megköveteli a szigorú jellegű biztonsági stratégiájának. Ezt a stratégiát kell biztosítani az adatokat a felhőben, az átvitel alatt adatintegritás a nyilvános interneten keresztül védheti meg és biztonságosan ellátja az eszközöket. Minden egyes réteg hoz létre a nagyobb biztonság a teljes infrastruktúrában.

## <a name="secure-an-iot-infrastructure"></a>Az IoT-infrastruktúra védelme
E jellegű biztonsági stratégia fejlesztett, és a gyártási, fejlesztési és az IoT-eszközök és infrastruktúra központi telepítése a szerepet játszó különféle résztvevői aktív részvétele végrehajtásra. Az alábbiakban az ezeket a magas szintű leírását.  

* **Az IoT hardver gyártója/integráló**: ezek rendszerint megtörténhetne hardver összeállításakor különböző gyártók vagy hardver biztosítva az IoT központi telepítésének gyártott vagy más szállítók által integrált szállítók kiegészítők IoT hardver gyártói.
* **Az IoT-megoldás fejlesztői**: egy IoT-megoldás fejlesztése általában egy megoldás fejlesztő végzi. A fejlesztői is része egy belső fejlesztésű csoport vagy ezzel a tevékenységgel szakosodott rendszert integráló (SI). Az IoT-megoldás fejlesztői fejlesztése teljesen új IoT-megoldás különböző összetevői, integrálni vásárolt vagy nyílt forráskódú összetevők vagy elfogadják a kisebb kiigazítása előkonfigurált megoldásokat.
* **Az IoT-megoldás deployer**: után az IoT-megoldás fejlesztése, a mezőben telepíteni kell. Ebbe beletartozik a hardverek, eszközök összekapcsolása és központi telepítését a hardvereszközök megoldások vagy a felhőben központi telepítését.
* **Az IoT-megoldás operátor**: után IoT-megoldás van telepítve, a hosszú távú működésre, ellenőrzésre, frissítések és karbantartás van szükség. Ez egy belső fejlesztésű csoport, amely magában foglalja az információk informatikai szakemberek számára, a hardver műveletek és a karbantartás csoportok és a tartomány szakembereket alkalmaznak, akik a helyes működése általános IoT-infrastruktúra figyelése teheti.

A következő szakaszok az egyes ezek játékosok fejlesztése, telepítéséhez és működtetéséhez biztonságos IoT-infrastruktúra segítségével ajánlott eljárások nyújtása.

## <a name="iot-hardware-manufacturerintegrator"></a>Az IoT hardver gyártója/integráló
Az alábbiakban a gyakorlati tanácsok az IoT-hardvergyártók és hardver kiegészítők.

* **Hatókör hardvereszközeit úgy, hogy a minimális követelmények**: A hardver tervezési tartalmaznia kell a művelethez a hardver, és semmi további szükséges minimális funkciók. Egy példa egy USB-porttal tartalmazza, csak ha az eszköz működéséhez szükséges. Ezek a kiegészítő funkciók az eszköz nem kívánt támadási vektor ismert, hogy el kell kerülni a megnyitásához.
* **Ellenőrizze a hardver igazolása védelmet**: mechanizmusok észleléséhez fizikai illetéktelen módosítás, például az eszköz borítóján megnyitását, vagy távolítsa el az eszközt egy részét a Build. Ezek a jelek védelmet az adatfolyamot a felhőbe, amely ezeket az eseményeket az operátorok sikerült riasztást feltöltött részét képezhetik.
* **Biztonságos hardveren körül Build**: Ha ELÁBÉ lehetővé teszi, készíthetnek biztonsági funkciókat, például a biztonságos és titkosított tárolási, vagy rendszerindítási a platformmegbízhatósági modul (TPM) alapú funkciót. Ezek a funkciók tehetik az eszközöket, több biztonságos, és az általános IoT-infrastruktúra védelméhez.
* **Frissítések biztonságosabbá teheti**: az eszköz élettartama során belső vezérlőprogram frissítési előbb vagy utóbb elkerülhetetlenné. Eszközök biztonságos elérési utak a frissítések és a titkosítási biztosítani kell a belső vezérlőprogram verzióinak létrehozása lehetővé teszi az eszköz biztonságos alatt és után frissítéseket.

## <a name="iot-solution-developer"></a>Az IoT-megoldás fejlesztői
Az IoT-megoldás fejlesztők számára az ajánlott eljárások a következők:

* **Hajtsa végre a biztonságos software development módszert**: biztonságos szoftver fejlesztésének biztonsági, egészen a projektet a végrehajtási, a tesztelés és a telepítése a kezdetektől ground felfelé továbbléphetnek igényel. A választási lehetőségek platformok, a nyelv és az eszközök összes befolyásolja az ezt a módszert. A Microsoft biztonsági fejlesztési életciklus során biztonságos szoftver létrehozásának részletes megközelítését ismerteti.
* **Válassza ki a nyílt forráskódú szoftvereket körültekintően**: gyorsan a megoldások fejlesztése lehetőséget kínál a nyílt forráskódú szoftvereket. Kidolgozásakor nyílt forráskódú szoftvereket, fontolja meg az egyes nyílt forráskódú összetevők közösségi tevékenység szintjét. Egy aktív közösségi biztosítja, hogy a szoftver támogatja-e, és hogy problémák felderítése és címzett. Azt is megteheti lehetséges, hogy nem támogatja egy homályos és inaktív nyílt forráskódú szoftvereket, és problémák valószínűleg nem lesz felderítve.
* **Integrálható, így gondossággal járjanak**: sok szoftver biztonsági hiányosságokat lehet létrehozni a határ könyvtárakat és API-k. Funkció, amely nem feltétlenül szükséges, a jelenlegi üzemelő példány előfordulhat, hogy továbbra is elérhetők az API réteg keresztül. Ahhoz, hogy a teljes biztonsági, ellenőrizze, hogy a biztonsági hiányosságokat integrálva összetevők az összes illesztő.      

## <a name="iot-solution-deployer"></a>Az IoT-megoldás deployer
Ajánlott eljárások az IoT-megoldás telepítőket a következők:

* **Hardver biztonságos telepítése**: IoT központi telepítések szükség lehet a hardvert, hogy nem biztonságos helyen, például a nyilvános szóközt vagy felügyeletlen területi telepíthető. Ilyen helyzetekben, győződjön meg arról, hogy hardver telepítési hamisíthatatlan legnagyobb mértékben. Ha USB vagy más portok elérhetők a hardverre, győződjön meg arról, biztonságosan jelez. Számos támadási ezek belépési pontként használható.
* **Hitelesítési kulcsok biztonsága**: a telepítés során minden egyes eszköz eszközazonosítókat igényel, és hozzárendelt hitelesítési kulcsokat, a felhőalapú szolgáltatás által létrehozott. Ezek a kulcsok fizikailag biztonságos tartsa a telepítés után is. Feltört kulcs segítségével egy rosszindulatú eszköz helyettesítő meglévő eszközként.

## <a name="iot-solution-operator"></a>Az IoT-megoldás operátor
Az ajánlott eljárásokat az IoT-megoldás operátorok a következők:

* **A rendszer naprakészen tartása**: Győződjön meg arról, hogy a eszköz-operációsrendszerek és az összes eszközillesztőt frissítése a legújabb verzióra. Ha bekapcsolja az automatikus frissítések a Windows 10 (IoT vagy más termékváltozatok), a Microsoft azt naprakészen tartása, és egy biztonságos operációs rendszer biztosít a IoT-eszközök. Más operációs rendszerek (például a Linux) tartása naprakészen segít biztosítani, hogy is védve legyenek rosszindulatú támadások ellen.
* **Rosszindulatú tevékenységhez elleni**: lehetővé teszi az operációs rendszer, ha minden eszköz operációs rendszere telepítse a legújabb a víruskereső és kártevőirtó funkciókat. Ez segítheti a legtöbb veszélyek mérséklése. A legtöbb modern operációs rendszer ellen megvédheti megfelelő lépéseket.
* **Naplózási gyakran**: naplózás IoT infrastruktúra biztonsági problémák esetén kulcs-biztonsági incidensekre válaszol. A legtöbb operációs rendszer beépített eseménynaplózás kell vizsgálni a gyakran ellenőrizze, hogy nincs biztonsági szabálysértés történt meg. Naplózási információ elküldhető egy külön telemetriai adatfolyamként a felhőszolgáltatásba elemzése ahol.
* **Az IoT-infrastruktúra fizikailag védelme**: A legrosszabb biztonsági támadások ellen IoT infrastruktúra fizikai hozzáférés eszközökhöz indul el. Egy fontos biztonsági gyakorlat az USB-porttal rosszindulatú visszaélések és más fizikai hozzáférés elleni védelem érdekében. Egyik kulcsról a fellépő uncovering megszegése bejelentkezik a fizikai hozzáférés, például az USB-port használatát. Ebben az esetben a Windows 10 (IoT és egyéb SKU) lehetővé teszi, hogy ezek az események részletes naplózás.
* **Felhő hitelesítő adatok védelme**: Felhő hitelesítő adatok konfigurálása és az IoT központi telepítése operációs rendszer valószínűleg a hozzáférést és legegyszerűbben hibát okoz az IoT. A hitelesítő adatok védelméről gyakran megváltoztatta a jelszavát, és ezek a hitelesítő adatok használata a nyilvános gépeken tartózkodnak.

Különböző IoT-eszközök képességei eltérőek lehetnek. Egyes eszközök is közös asztali operációs rendszert futtató számítógépeken, és előfordulhat, hogy a bizonyos eszközök nagyon passzív operációs rendszert futtató. Az ajánlott biztonsági eljárásokkal leírt korábban előfordulhat, hogy alkalmazhatók a különböző mértékben ezekre az eszközökre. Ha a megadott, további biztonságot és ajánlott eljárások ezen eszközök gyártóktól származó kell követni.

Néhány örökölt és korlátozott eszköz előfordulhat, hogy nem készített kifejezetten az IoT-telepítés. Ezek az eszközök előfordulhat, hogy nem rendelkezik az adatok titkosítása, csatlakozzon az internethez, vagy adjon meg, speciális naplózási képesség. Ezekben az esetekben modern és biztonságos mező átjáró összesíteni az adatokat az örökölt eszközök, és adja meg a biztonsági, ezek az eszközök az interneten keresztül kapcsolódó szükséges. Mező átjárók biztosíthat biztonságos hitelesítési, a titkosított munkamenetek egyeztetés, a felhőhöz, és számos egyéb biztonsági funkciókról a parancsok fogadását.

## <a name="see-also"></a>Lásd még:
Az IoT-megoldásból biztosításával kapcsolatos további tudnivalókért lásd:

* [Az IoT-biztonsági architektúrája][lnk-security-architecture]
* [Az IoT üzembe][lnk-security-deployment]

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [Előre konfigurált prediktív karbantartási megoldás áttekintése][lnk-predictive-overview]
* [Gyakori kérdések az Azure IoT Suite][lnk-faq]

Az IoT-központ biztonsági olvashat [IoT-központ való hozzáférés szabályozása] [ lnk-devguide-security] az IoT Hub fejlesztői útmutató.

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md

[lnk-security-architecture]: iot-security-architecture.md
[lnk-security-deployment]: iot-suite-security-deployment.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md

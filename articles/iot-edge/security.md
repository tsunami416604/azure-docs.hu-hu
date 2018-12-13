---
title: Biztonsági keretrendszer – Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg a biztonsági, hitelesítési és engedélyezési szabványok gyűjteménye, amelyek az Azure IoT Edge fejlesztéséhez használt, és érdemes figyelembe venni, mivel a megoldás megtervezése
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1042f53147122a7370b464f6bfc892dcee70fe70
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100140"
---
# <a name="security-standards-for-azure-iot-edge"></a>Az Azure IoT Edge biztonsági szabványait

Intelligens peremhálózat biztosítása szükség, felruházza a bizalom a teljes körű IoT-megoldás működésére. Az Azure IoT Edge lett tervezve, amelyet a különböző kockázati-profilok központi telepítési forgatókönyv, bővíthető, és ugyanazt az összes Azure-szolgáltatásokból származó várt védelmet kínál.

Az Azure IoT Edge más hardvereken fut, támogatja a Linux és a Windows, és különböző telepítési forgatókönyvek alkalmazható.  Felmért kockázatokat attól függ, hogy számos szempontok, beleértve a megoldás tulajdonjogát, térségétől, adatok érzékenysége, adatvédelmi, alkalmazás vertikális, és szabályozási követelményeikkel.  Ahelyett, hogy bizonyos forgatókönyvek esetén konkrét megoldást kínál, logikus tervezhet egy bővíthető biztonsági keretrendszert, a méretezési csoport tervezett jól grounded elvek alapján. 
 
Ez a cikk a biztonsági keretrendszer áttekintést nyújt. További információkért lásd: [intelligens peremhálózat biztosítása](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Szabványok

Szabványok alapos könnyű és az egyszerű megvalósítása, amelyek a biztonsági hallmark előléptetése.  Megvalósíthatná biztonsági megoldást kell jellegűvé alatt hozhat létre megbízható értékelési vizsgálat, és nem lehet üzemelő példány egy küszöbértéket.  A kialakítás biztonságossá tétele az Azure IoT Edge-keretrendszer jelentette tesztelt és iparágban már bizonyított biztonsági protokollok kihasználhatja a megszokott és újbóli felhasználáshoz. 

## <a name="authentication"></a>Hitelesítés

Milyen szándékú felhasználók, eszközök és összetevők teljes körű IoT-megoldások révén a értékközvetítést résztvevő ismerete nélkül egy bizonytalan létfontosságú a megbízhatósági kapcsolat létrehozásához.  Az ilyen ismeretek való engedélyezésével már a betegfelvétel alapját biztonságos accountability kínál.  Az Azure IoT Edge teljesítsük a Tudásbázis-hitelesítésen keresztül.  A hitelesítés az Azure IoT Edge platformon az elsődleges mechanizmusa Tanúsítványalapú hitelesítés.  Ez a mechanizmus szabványokat fogalmaz meg a nyilvános kulcsú infrastruktúra (PKiX) az Internet Engineering Task Force (IETF) által meghatározott használatából.     

Az Azure IoT Edge biztonsági keretrendszer egyedi tanúsítványt minden eszközök, modulok (tárolók, amely magába foglalja az eszközön belül logikai) és az Azure IoT Edge-eszköz használata, fizikailag vagy a hálózati kapcsolat legyen az actors identitásainak szükségessé teszi.  Nem minden forgatókönyvben vagy összetevő előfordulhat, hogy a verziókövetésre, amelynek a biztonsági keretrendszer bővíthetőségét kínál biztonságos kialakítható vendéglátóipar a tanúsítványalapú hitelesítést. 

## <a name="authorization"></a>Engedélyezés

Szolgáltató és hozzáférés delegálása lehetővé teszi elengedhetetlen alapvető biztonsági irányelveket – a minimális jogosultság elvének elérésére.  Eszközök, modulok és actors is elérhet csak erőforrások és adatok engedély hatálya alá, és csak akkor, ha tekintve megengedett.  Ez azt jelenti, hogy néhány engedélyt konfigurálható elegendő jogosultsággal és másokkal tekintve érvényben.  Például egy modul rendszerjogosultságú konfigurálásával engedélyezhető használatával kezdeményeznek kapcsolatot az Azure IoT hubhoz, amíg nem indokolt miért modul egy Azure IoT Edge-eszköz hozzá kell férnie az ikereszköz egy másik Azure IoT Edge-eszköz modul.  Ezért az utóbbi kellene lennie tekintve kizár elmozdulást. 

Egyéb engedélyezési rendszerek például a tanúsítvány-aláírási jogok és a szerepköralapú hozzáférés-vezérlés (RBAC).  A biztonsági keretrendszer bővíthetőségét lehetővé teszi, hogy más érett engedélyezési rendszerek bevezetését. 

## <a name="attestation"></a>Igazolás

Igazolási garantálja a tárműveletek sértetlenségét szoftver bits.  Fontos észlelésére és megelőzésére kártevő szoftverek.  Az Azure IoT Edge biztonsági keretrendszer igazolási alatt három fő kategóriába sorolja be:

* Statikus igazolás
* Futtatókörnyezet-igazolás
* Szoftver-igazolás

### <a name="static-attestation"></a>Statikus igazolás

Statikus igazolási az összes szoftver az operációs rendszerek, az összes futásidejének tekintetében, beleértve az integritás ellenőrzése és bekapcsolási funkciójának eszköz konfigurációs adatokat.  Ezt gyakran nevezik a biztonságos rendszerindítás.  Az Azure IoT Edge-eszközök biztonsági keretrendszer szilícium-szállítók is kiterjed, és biztonságos ingrained hardvertulajdonságaival ahhoz, hogy biztosítsa statikus igazolási folyamat magában foglalja. Ezeket a folyamatokat tartalmazzák a biztonságos rendszerindítás és a biztonságos belső vezérlőprogram frissítése a folyamatokat.  Szilícium-szállítók szoros együttműködésben dolgozik kiküszöböli a felesleges belső vezérlőprogram rétegek ezáltal minimálisra csökkentik a fenyegetés felületet. 

### <a name="runtime-attestation"></a>Futtatókörnyezet-igazolás

A rendszer egy ellenőrzött rendszerindítási folyamat befejeződött, és működik, és fut, a jól megtervezett biztonságos rendszerek észleléséhez lenne megkísérli beszúrása a rendszerek portokat és interfészeket keresztül kártevők és a megfelelő ellenintézkedések.  Intelligens peremhálózat-eszközökhöz a fizikai felügyeleti lánc rosszindulatú szereplők eszköz felületek, például illetéktelen eltérő módon malcontent beszúrható legyen, és kiszolgálóoldali-csatorna támadások.   Ilyen malcontent, amely rosszindulatú vagy jogosulatlan konfigurációs módosításokat formájában, általában nem lennének megtalálhatóak a biztonságos rendszerindítási folyamat, mert a rendszerindítási folyamat után történjen, amikor.  Ellenintézkedések érhető el, vagy kényszeríti ki az eszköz hardvere nagyban hozzájárul a warding ilyen fenyegetések ki.  Az Azure IoT Edge biztonsági keretében explicit módon meghívja a futtatókörnyezet fenyegetések combatting-bővítmények.     

### <a name="software-attestation"></a>Szoftver-igazolás

Intelligens peremhálózat rendszerek például az összes kifogástalan rendszer alkalmas javításokat és frissítéseket kell lennie.  Biztonsági fontos a frissítési folyamat más módon potenciális fenyegetési vektoroknak lehetnek.  A security keretrendszere frissítéseit az Azure IoT Edge-hívást mérni, és írja alá a biztonságának biztosítása és a hitelesítéshez a csomagok a forrás-csomagok.  Ez az összes operációs rendszer és alkalmazás szoftver bits alkalmazható. 

## <a name="hardware-root-of-trust"></a>Hardver legfelső szintű megbízhatóság

A hardver által kínált biztonsági számos intelligens peremhálózati eszközökre, különösen a telepített helyen, ahol esetleges rosszindulatú actors fizikailag hozzáfér az eszköz telepítése az utolsó védelmi védelemre.  Emiatt az illetéktelen módosítás ellen védett hardver rögzítő megbízhatósági elengedhetetlen leginkább az ilyen környezetekhez.  Az Azure IoT Edge biztonsági keretében különböző változataira jellemző hardver gyökér megbízhatósági különböző kockázati, és a központi telepítési forgatókönyv kínálta biztonságos szilícium hardvergyártótól együttműködési jár. Ezek közé tartozik a biztonságos szilícium efféle közös biztonsági protokoll szabványok, például a platformmegbízhatósági modul (ISO/IEC 11889) és Trusted Computing Group az eszköz azonosítója Composition Engine (DICE) használatát.  Ide tartoznak a biztonságos enklávé technológiákkal, mint például a TrustZones és szoftverek Guard Extensions (SGX) is. 

## <a name="certification"></a>Tanúsítvány

Ügyfelek pedig megalapozottabb döntéseket, ha a központi telepítés az Azure IoT Edge-eszközök beszerzése érdekében az Azure IoT Edge-keretrendszert tartalmaz hardvertanúsítvány követelményeit.  A követelményeket ismertet a kérelmekre vonatkozó biztonsági jogcímeinek tanúsítványok és a tanúsítványok érvényesítése biztonsági végrehajtására vonatkozó.  Például a biztonsági jogcím minősítési lenne tájékoztatja, hogy az IoT Edge-eszköz ellenáll a rendszerindító támadások ismert, biztonságos hardvert használja a. Érvényesítési minősítés lenne tájékoztatja, hogy a biztonságos hardver megfelelően lett megvalósítva kínáló ezt az értéket az eszköz.  Az egyszerűség elvének betartásával keretében kínál a stratégiai hitelesítő terhe minimális védelmében.   

## <a name="extensibility"></a>Bővíthetőség

Bővítési egy első osztályú citizen az Azure IoT Edge biztonsági keretében.  A különböző típusú üzleti átalakítást vezetési IoT technológiát azt jelöli, hogy biztonsági zökkenőmentesen kell alakítani az új forgatókönyvek címre lockstep okból.  Az Azure IoT Edge biztonsági keretrendszert, amelyen, számos tekintetben az bővíthetőség tartalmazza a különböző dimenziókban az alapos rálátással kezdődik: 

* Belső biztonsági szolgáltatásokba, mint a Device Provisioning Service az Azure IoT Hub.
* Harmadik féltől származó szolgáltatásokkal, például a felügyelt biztonsági szolgáltatások (például az ipari vagy egészségügyi) különböző alkalmazás referenciaegyenesen vagy technológiai fókusz (ilyen például a biztonságfigyelés háló hálózatokat vagy a szilícium hardver igazolási szolgáltatásokról), gazdag hálózaton keresztül partnerek.
* Tartalmazza a helyettesítő biztonsági stratégiák, történő régebbi rendszerekkel, mint tanúsítványok nem biztonságos technológia használatával hitelesítést és identitáskezelést biztosít a.
* Hardver biztonságos hardver újonnan megjelenő technológiák és szilícium zökkenőmentes bevezetését a partner hozzájárulások biztonságos.

Ezek csak néhány példa a dimenziók, a bővítést és az Azure IoT Edge biztonsági keretrendszer tervezték biztonságos az alapjaitól kezdve a bővíthetőség támogatásához. 

A végén az intelligens peremhálózat biztosítása a legmagasabb sikeres eredménye a közös érdekeltséget IoT biztonságossá tétele tárolásuk egy nyílt közösségi hozzájárulásokat által biztosított együttműködési környezettel.  Ezek a hozzájárulások lehet biztonságos technológiákat és szolgáltatásokat formájában.  Az Azure IoT Edge biztonsági keretrendszer, bővíthető, az a maximális lefedettség kínálta megbízhatósági és az intelligens peremhálózat, mint az Azure-felhőben lévő integritási azonos szintű biztonság szilárd alapokat nyújt.  

## <a name="next-steps"></a>További lépések

További információk a hogyan Azure IoT Edge van [intelligens peremhálózat biztosítása](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

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
ms.openlocfilehash: b174d7f9b4b8438687512a90dc7a65b5649f758a
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229888"
---
# <a name="security-standards-for-azure-iot-edge"></a>Az Azure IoT Edge biztonsági szabványait

Az adatok és analitika áthelyezése az intelligens peremhálózat hoz létre kockázati forgatókönyvek, amelyek célja, hogy az Azure IoT Edge-címre. Az IoT Edge biztonsági szabványait úgy van kialakítva, hogy rugalmasságot biztosít a különböző kockázati, és a központi telepítési forgatókönyv mialatt továbbra is ugyanazt az összes Azure-szolgáltatásokból származó várt védelmet. 

Az Azure IoT Edge különböző hardver megkönnyíti és modellek fut, több operációs rendszert támogat, és különböző telepítési forgatókönyvek vonatkozik. A telepítési forgatókönyvben a kockázatok felmérése attól függ, hogy számos szempontok, beleértve a megoldás tulajdonjogát, térségétől, adatok érzékenysége, adatvédelmi, alkalmazás vertikális, és szabályozási követelményeikkel. Ahelyett, hogy bizonyos forgatókönyvek esetén konkrét megoldásokat kínál, az IoT Edge egy egy bővíthető biztonsági keretrendszert, a méretezési csoport tervezett jól grounded elvek alapján. 
 
Ez a cikk az IoT Edge biztonsági keretrendszer áttekintést nyújt. További információkért lásd: [intelligens peremhálózat biztosítása](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Szabványok

Szabványok támogatása alapos könnyű és az implementáció, mindkettő biztonsági hallmarks egyszerű. Egy biztonsági megoldás kell jellegűvé alatt hozhat létre megbízható értékelési vizsgálat, és nem lehet üzemelő példány egy küszöbértéket. A kialakítás biztonságossá tétele az Azure IoT Edge-keretrendszer alapul tesztelt, és az iparágban már bizonyított biztonsági protokollok ismerete és újbóli felhasználáshoz. 

## <a name="authentication"></a>Hitelesítés

Amikor egy IoT-megoldás üzembe helyezésekor kell, hogy csak megbízható actors, eszközök és összetevőit, hogy a megoldás a hozzáférést. Az ilyen ismeretek való engedélyezésével már a betegfelvétel alapját biztonságos accountability kínál.  Az Azure IoT Edge teljesítsük a Tudásbázis-hitelesítésen keresztül.  A hitelesítés az Azure IoT Edge platformon az elsődleges mechanizmusa Tanúsítványalapú hitelesítés.  Ez a mechanizmus szabványokat fogalmaz meg a nyilvános kulcsú infrastruktúra (PKiX) az Internet Engineering Task Force (IETF) által meghatározott használatából.     

Eszközök, modulok és használata az Azure IoT Edge-eszköz, akár fizikailag, akár egy hálózati kapcsolaton keresztül actors identitások egyedi tanúsítványt kell rendelkeznie. Nem minden forgatókönyvben vagy összetevő előfordulhat, hogy a verziókövetésre tanúsítvány alapú hitelesítést. Ezeket a forgatókönyveket, a biztonsági keretében bővíthetőségét biztonságos alternatívákat kínál. 

## <a name="authorization"></a>Engedélyezés

A legalacsonyabb jogosultsági szint elve szerint a felhasználók és a rendszer összetevőinek hozzáférés csak az erőforrások és adatok szerepkörökhöz szükséges minimális készletét kell rendelkeznie. Eszközök, modulok és actors hozzá kell férnie az csak az erőforrások és adatok engedély hatálya alá, és csak akkor tekintve megengedett. Néhány engedélyt, megfelelő jogosultságokkal rendelkező konfigurálható és mások tekintve kényszerítve.  Például egy modul engedélyezhető rendszerjogosultságú konfigurálással használatával kezdeményeznek kapcsolatot az Azure IoT hubra. Azonban nem indokolt miért modul egy Azure IoT Edge-eszköz hozzá kell férnie az ikereszköz egy másik Azure IoT Edge-eszköz modul.

Egyéb engedélyezési rendszerek tanúsítvány-aláírási jogokat, szerepköralapú hozzáférés-vezérlés (RBAC) és egyéb érett engedélyezési sémát tartalmazza. 

## <a name="attestation"></a>Igazolás

Igazolási garantálja a tárműveletek sértetlenségét szoftver bits.  Fontos észlelésére és megelőzésére kártevő szoftverek.  Az Azure IoT Edge biztonsági keretrendszer igazolási alatt három fő kategóriába sorolja be:

* Statikus igazolás
* Futtatókörnyezet-igazolás
* Szoftver-igazolás

### <a name="static-attestation"></a>Statikus igazolás

Statikus tanúsítvány igazolja, hogy az eszközön, beleértve az operációs rendszer, az összes modulok és bekapcsolási funkciójának eszköz konfigurációs adatokat az összes szoftver. Ezt gyakran nevezik a biztonságos rendszerindítás. A security keretrendszere az Azure IoT Edge-eszközök gyártók is kiterjed, és magában foglalja a statikus igazolási folyamatok kizárására biztonságos hardvertulajdonságaival. Ezeket a folyamatokat tartalmazzák a biztonságos rendszerindítás és a biztonságos belső vezérlőprogram frissítése.  Szilícium-szállítók szoros együttműködésben dolgozik eltávolítja a felesleges belső vezérlőprogram rétegek, így minimálisra csökkenti a fenyegetés felületet. 

### <a name="runtime-attestation"></a>Futtatókörnyezet-igazolás

A rendszer egy biztonságos rendszerindítási folyamat befejeződött, és a már működő, jól megtervezett rendszerek beszúrása a kártevők és a megfelelő ellenintézkedések kísérletek szeretné észlelni. Kártevő-támadásokat megcélozhatnak, a rendszer portokat és interfészeket eléréséhez a rendszer. Vagy, ha rosszindulatú fizikailag hozzáfér egy eszközt, előfordulhat, hogy illetéktelenül maga az eszköz vagy csatorna oldalán támadások használatával hozzáférést. Az ilyen malcontent, amely rosszindulatú vagy jogosulatlan konfigurációs módosításokat formájában, szúrhatja be a rendszerindítási folyamat után így statikus igazolási nem ismeri fel. Ellenintézkedések érhető el, vagy az eszköz hardveres segítségére ahhoz, hogy az ilyen fenyegetések kivédése kényszeríti.  Az Azure IoT Edge biztonsági keretében explicit módon meghívja a futtatókörnyezet fenyegetések elleni olyan bővítmények esetében.  

### <a name="software-attestation"></a>Szoftver-igazolás

Intelligens peremhálózat rendszerek például az összes kifogástalan rendszer el kell fogadnia, javításokat és frissítéseket.  Biztonsági fontos a frissítési folyamat ellenkező esetben ezek lehetnek a potenciális fenyegetési vektoroknak.  A security keretrendszere frissítéseit az Azure IoT Edge-hívást mérni, és írja alá a biztonságának biztosítása és a hitelesítéshez a csomagok a forrás-csomagok.  Ez a szabvány összes operációs rendszer és alkalmazás szoftver bits vonatkozik. 

## <a name="hardware-root-of-trust"></a>Hardver legfelső szintű megbízhatóság

Számos intelligens peremhálózat eszközökhöz, különösen a helyen, ahol a lehetséges kártevő szándékú felhasználók az eszközre, fizikai hozzáféréssel rendelkező eszközök eszköz hardveres biztonsági az utolsó védelmi védelemre. Módosítás ellen védett hardver elengedhetetlen az ilyen környezetekhez. Az Azure IoT Edge arra ösztönzi a biztonságos szilícium hardverszállítók kínáló hardver gyökér megbízhatósági különböző kockázati, és a központi telepítési forgatókönyv különböző változataira jellemző. Hardver megbízhatósági közös biztonsági protokoll szabványok, például a platformmegbízhatósági modul (ISO/IEC 11889) és Trusted Computing Group az eszköz azonosítója Composition Engine (DICE) származhatnak. Biztonságos enklávé technológiák, például TrustZones és szoftverek Guard Extensions (SGX) hardver megbízhatósági nyújt. 

## <a name="certification"></a>Tanúsítvány

Ügyfelek pedig megalapozottabb döntéseket, ha a központi telepítés az Azure IoT Edge-eszközök beszerzése érdekében az Azure IoT Edge-keretrendszert tartalmaz hardvertanúsítvány követelményeit.  A követelményeket ismertet a kérelmekre vonatkozó biztonsági jogcímeinek tanúsítványok és a tanúsítványok érvényesítése biztonsági végrehajtására vonatkozó.  Például a biztonsági jogcím minősítési lenne tájékoztatja, hogy az IoT Edge-eszköz ellenáll a rendszerindító támadások ismert, biztonságos hardvert használja a. Érvényesítési minősítés lenne tájékoztatja, hogy a biztonságos hardver megfelelően lett megvalósítva kínáló ezt az értéket az eszköz.  Egyszerűség elvének betartásával keretében megkísérli hitelesítő terhe minimális tartani.   

## <a name="extensibility"></a>Bővíthetőség

Bővítési egy első osztályú citizen az Azure IoT Edge biztonsági keretében.  A különböző típusú üzleti átalakítást vezetési IoT technológiát valószínűleg, hogy a biztonsági forgatókönyvek feltörekvő címre párhuzamosan kell alakítani.  Az Azure IoT Edge biztonsági keretrendszert, amelyen, számos tekintetben az bővíthetőség tartalmazza a különböző dimenziókban az alapos rálátással kezdődik: 

* Belső biztonsági szolgáltatásokba, mint a Device Provisioning Service az Azure IoT Hub.
* Harmadik féltől származó szolgáltatásokkal, például a felügyelt biztonsági szolgáltatások (például az ipari vagy egészségügyi) különböző alkalmazás referenciaegyenesen vagy technológiai fókusz (ilyen például a biztonságfigyelés háló hálózatokat vagy a szilícium hardver igazolási szolgáltatásokról), gazdag hálózaton keresztül partnerek.
* Tartalmazza a helyettesítő biztonsági stratégiák, történő régebbi rendszerekkel, mint tanúsítványok nem biztonságos technológia használatával hitelesítést és identitáskezelést biztosít a.
* Hardver biztonságos hardver újonnan megjelenő technológiák és szilícium elfogadását a partner hozzájárulások biztonságos.

A végén az intelligens peremhálózat biztosítása a legmagasabb sikeres eredménye a közös érdekeltséget IoT biztonságossá tétele tárolásuk egy nyílt közösségi hozzájárulásokat által biztosított együttműködési környezettel.  Ezek a hozzájárulások lehet biztonságos technológiákat és szolgáltatásokat formájában.  Az Azure IoT Edge biztonsági keretrendszer, bővíthető, az a maximális lefedettség kínálta megbízhatósági és az intelligens peremhálózat, mint az Azure-felhőben lévő integritási azonos szintű biztonság szilárd alapokat nyújt.  

## <a name="next-steps"></a>További lépések

További információk a hogyan Azure IoT Edge van [intelligens peremhálózat biztosítása](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

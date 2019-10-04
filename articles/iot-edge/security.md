---
title: Biztonsági keretrendszer – Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg a biztonsági, hitelesítési és engedélyezési szabványok gyűjteménye, amelyek az Azure IoT Edge fejlesztéséhez használt, és érdemes figyelembe venni, mivel a megoldás megtervezése
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 37d5288389c7b602eb0d13a736e289010d7e0f80
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208199"
---
# <a name="security-standards-for-azure-iot-edge"></a>Az Azure IoT Edge biztonsági szabványait

A Azure IoT Edge az adatai és az elemzések intelligens peremhálózatba való áthelyezésekor rejlő kockázatokat kezeli. A IoT Edge biztonsági szabványok rugalmasságot biztosítanak a különböző üzembe helyezési forgatókönyvek számára az összes Azure-szolgáltatástól várt védelemmel. 

IoT Edge a hardver különböző modelljein és típusain fut, több operációs rendszert támogat, és különböző üzembe helyezési forgatókönyvekre vonatkozik. Az üzembe helyezési forgatókönyvek kockázata a megoldás tulajdonjogát, a központi telepítési földrajzot, az adatok érzékenységét, az adatvédelmet, az alkalmazás vertikális és szabályozási követelményeit is magában foglalja. Ahelyett, hogy konkrét megoldásokat kínál konkrét forgatókönyvekhez, IoT Edge egy bővíthető biztonsági keretrendszer, amely a méretezéshez tervezett, jól megalapozott alapelveken alapul. 
 
Ez a cikk áttekintést nyújt a IoT Edge biztonsági keretrendszerről. További információkért lásd: [intelligens peremhálózat biztosítása](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Szabványok

A szabványok megkönnyítik az alapos vizsgálatot és a könnyű megvalósítást, mindkettő a biztonság szempontjából fémjelzi. A biztonsági megoldásnak a kiértékelés alatt meg kell adniuk a megbízhatóságot, és nem szabad az üzembe helyezés akadálya. A Azure IoT Edge biztonságossá tételének kialakítása az idő és az iparág által bizonyítottan jól bevált biztonsági protokollokon alapul. 

## <a name="authentication"></a>Authentication

IoT-megoldás üzembe helyezésekor tudnia kell, hogy csak a megbízható szereplők, eszközök és modulok férhetnek hozzá a megoldáshoz. A tanúsítványalapú hitelesítés a Azure IoT Edge platform hitelesítésének elsődleges mechanizmusa. Ez a mechanizmus a nyilvános kulcsokra épülő infrastruktúrát (PKiX) az Internet Engineering Task Force (IETF) által szabályozott szabványok összessége alapján származtatja.     

A Azure IoT Edge eszközzel (akár fizikailag, akár hálózati kapcsolatban) kommunikáló összes eszköznek, modulnak és szereplőnek egyedi tanúsítvány-identitással kell rendelkeznie. Nem minden forgatókönyv vagy összetevő használhat tanúsítvány alapú hitelesítést, így a biztonsági keretrendszer bővíthetősége biztonságos alternatívákat biztosít. 

További információ: [Azure IoT Edge tanúsítvány használata](iot-edge-certs.md).

## <a name="authorization"></a>Authorization

A legalacsonyabb jogosultság elve azt jelenti, hogy a rendszer felhasználóinak és összetevőinek csak a szerepkörök végrehajtásához szükséges erőforrások és adatok minimális készletéhez kell hozzáféréssel rendelkezniük. Az eszközök, modulok és színészek csak a jogosultsági hatókörén belüli erőforrásokhoz és információkhoz férnek hozzá, és csak akkor, ha az architektúra engedélyezve van. Bizonyos engedélyek megfelelő jogosultságokkal konfigurálhatók, mások pedig építészeti szempontból kényszerítve vannak.  Előfordulhat például, hogy egyes modulok engedélyt kapnak az Azure IoT Hubhoz való kapcsolódásra. Azonban nincs ok arra, hogy az egyik IoT Edge eszközhöz tartozó modul egy másik IoT Edge eszközben is hozzáférhessen egy modulhoz.

Más engedélyezési sémák közé tartozik a tanúsítvány-aláírási jogosultságok és a szerepköralapú hozzáférés-vezérlés (RBAC). 

## <a name="attestation"></a>Igazolás

Az igazolás biztosítja a szoftveres BITS integritását, ami fontos a kártevők észleléséhez és megelőzéséhez.  Az Azure IoT Edge biztonsági keretrendszer igazolási alatt három fő kategóriába sorolja be:

* Statikus igazolás
* Futtatókörnyezet-igazolás
* Szoftver-igazolás

### <a name="static-attestation"></a>Statikus igazolás

A statikus igazolás ellenőrzi az eszközön lévő összes szoftver integritását az áramellátás során, beleértve az operációs rendszert, az összes futtatókörnyezetet és a konfigurációs információt. Mivel a kikapcsolás során statikus igazolás történik, gyakran biztonságos rendszerindítás néven is ismert. A IoT Edge eszközök biztonsági keretrendszere kiterjed a gyártókra, és olyan biztonságos hardver-képességeket tartalmaz, amelyek biztosítják a statikus igazolási folyamatokat. Ezek a folyamatok a biztonságos rendszerindítást és a biztonságos belső vezérlőprogram frissítését tartalmazzák.  A szilícium-szállítókkal folytatott szoros együttműködés során a felesleges belső vezérlőprogram-rétegek elkerülhetők, így a fenyegetések felszínét csökkentik. 

### <a name="runtime-attestation"></a>Futtatókörnyezet-igazolás

Miután egy rendszer elvégezte a biztonságos rendszerindítást, a jól megtervezett rendszereknek fel kell ismerniük a kártevők beadására tett kísérleteket és a megfelelő ellenintézkedéseket. A kártevők elleni támadások a rendszerek portjait és felületeit célozzák meg. Ha a rosszindulatú szereplők fizikailag férnek hozzá egy eszközhöz, az eszköz maga is meghamisíthatja az eszközt, vagy használhatja a csatornákon keresztüli támadásokat, hogy hozzáférjenek. Az ilyen malcontent, függetlenül attól, hogy a kártevők és a jogosulatlan módosítások nem észlelhetők-e statikus igazolással, mert a rendszer a rendszerindítási folyamat után fecskendez. Az eszköz hardveres súgója által kínált vagy kényszerített ellenintézkedések az ilyen fenyegetések kivédéséhez.  A IoT Edge biztonsági keretrendszere explicit módon olyan bővítményeket hív meg, amelyek a futásidejű fenyegetések elleni küzdelemre irányulnak.  

### <a name="software-attestation"></a>Szoftver-igazolás

Az összes kifogástalan állapotú rendszernek, beleértve az intelligens peremhálózati rendszereket, javításokat és frissítéseket kell használnia.  A biztonsági frissítések fontosak a frissítési folyamatokhoz, ellenkező esetben lehetséges veszélyforrások lehetnek.  A IoT Edge biztonsági keretrendszere a mért és aláírt csomagok alapján kéri a frissítéseket a csomagok forrásának integritásának és hitelesítésének biztosítására.  Ez a szabvány az összes operációs rendszerre és alkalmazás szoftveres BITS-re vonatkozik. 

## <a name="hardware-root-of-trust"></a>Hardver legfelső szintű megbízhatóság

Számos intelligens peremhálózati eszközhöz, különösen azokhoz az eszközökhöz, amelyekhez a potenciális kártékony szereplők fizikailag hozzáférhetnek, a hardveres biztonság a védelem utolsó védelmi lehetősége. Az ilyen üzembe helyezések esetében elengedhetetlen a jogosulatlanul védett hardverek használata. Azure IoT Edge arra ösztönzi a biztonságos szilícium-gyártókat, hogy a különböző kockázati profilok és üzembe helyezési forgatókönyvek esetében különböző típusú hardveres megbízhatósági kapcsolatot nyújtsanak A hardveres megbízhatóság a közös biztonsági protokollok (például a platformmegbízhatósági modul (ISO/IEC 11889) és a Trusted Computing Group eszköz-azonosító összeállítási motorja (DICE) esetében lehet. A biztonságos enklávé-technológiák, például a TrustZones és a Software Guard Extensions (SGX ENKLÁVÉHOZ) is biztosítanak hardveres megbízhatóságot. 

## <a name="certification"></a>Tanúsítvány

Annak érdekében, hogy az ügyfelek tájékozott döntéseket hozhassanak Azure IoT Edge eszközeinek beszerzése során az üzembe helyezéshez, a IoT Edge keretrendszer tartalmaz minősítési követelményeket.  A követelményeket ismertet a kérelmekre vonatkozó biztonsági jogcímeinek tanúsítványok és a tanúsítványok érvényesítése biztonsági végrehajtására vonatkozó.  A biztonsági jogcímek tanúsítványa például azt jelenti, hogy a IoT Edge eszköz biztonságos hardvert használ, amely képes a rendszerindítási támadásokra. Az érvényesítési tanúsítvány azt jelenti, hogy a biztonságos hardver megfelelően lett megvalósítva, hogy felkínálja ezt az értéket az eszközön.  Az egyszerűség elve miatt a keretrendszer minimálisan igyekszik megtartani a minősítés terheit.   

## <a name="extensibility"></a>Bővíthetőség

A IoT technológia különböző típusú üzleti átalakításokat vezet be, a biztonságnak párhuzamosan kell fejlődni az új forgatókönyvek kezeléséhez.  Az Azure IoT Edge biztonsági keretrendszert, amelyen, számos tekintetben az bővíthetőség tartalmazza a különböző dimenziókban az alapos rálátással kezdődik: 

* Belső biztonsági szolgáltatásokba, mint a Device Provisioning Service az Azure IoT Hub.
* Harmadik féltől származó szolgáltatások, mint például a felügyelt biztonsági szolgáltatások a különböző alkalmazás-vertikális eszközökhöz (például az ipari vagy az egészségügyi szolgáltatáshoz) vagy a technológiai fókuszhoz (például a Mesh hálózatokban vagy a szilícium-tanúsítványok igazolási szolgáltatásaiban) egy gazdag hálózatán keresztül partnerek.
* Tartalmazza a helyettesítő biztonsági stratégiák, történő régebbi rendszerekkel, mint tanúsítványok nem biztonságos technológia használatával hitelesítést és identitáskezelést biztosít a.
* Biztonságos hardver a feltörekvő biztonságos hardveres technológiák és a szilícium-partnerek hozzájárulásainak bevezetéséhez.

Az intelligens peremhálózat biztonságossá tétele érdekében a IoT védelmének közös érdeke a nyílt Közösségtől származó együttműködésen alapuló hozzájárulásokat igényel.  Ezek a hozzájárulások lehet biztonságos technológiákat és szolgáltatásokat formájában.  Az Azure IoT Edge biztonsági keretrendszer, bővíthető, az a maximális lefedettség kínálta megbízhatósági és az intelligens peremhálózat, mint az Azure-felhőben lévő integritási azonos szintű biztonság szilárd alapokat nyújt.  

## <a name="next-steps"></a>További lépések

További információk a hogyan Azure IoT Edge van [intelligens peremhálózat biztosítása](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

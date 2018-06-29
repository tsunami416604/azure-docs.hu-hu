---
title: Az Azure IoT Edge biztonsági |} Microsoft Docs
description: Biztonsági, hitelesítési és engedélyezési IoT peremhálózati eszköz
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f198efe9ff5e4862a3bbe872ab50e5848c9dbb5c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030580"
---
# <a name="securing-azure-iot-edge"></a>Az Azure IoT peremhálózati biztonságossá tétele

Biztonságossá tétele az intelligens él szükség, felruházza a művelet egy teljes körű IoT-megoldás-be vetett bizalmat. Azure IoT peremhálózati készült biztonsági bővíthető különböző kockázati profilok, a központi telepítési forgatókönyve esetén, és az összes Azure-szolgáltatásokat a várt azonos védelmet nyújt.

Az Azure IoT peremhálózati fut másik hardverre, támogatja a Linux és a Windows, és különböző telepítési forgatókönyvek alkalmazandó.  Becsült kockázattal sok szempontok, beleértve a megoldás tulajdonjogát, központi telepítési geográfiai, adatok érzékenysége, adatvédelmi, alkalmazás függőleges és szabályozási követelmények függ.  Ahelyett, hogy az adott forgatókönyveket konkrét megoldást kínál, így megtervezheti egy bővíthető biztonsági keretrendszer méretezési tervezett jól grounded alapelvek alapján. 
 
Ez a cikk áttekintést biztonsági keretében. További információkért lásd: [biztonságossá tétele az intelligens él][lnk-edge-blog].

## <a name="standards"></a>Szabványok

Szabványok egyszerű vizsgálati és a könnyű végrehajtására, amelyek biztonsági hallmark lépteti elő.  A megfelelően felépített biztonsági megoldás alkalmasnak kell vizsgálatra értékelje megbízhatósági kapcsolat létrehozásához, és nem lehet a telepítéshez a küszöbértéket.  A Tervező keretrendszer Azure IoT peremhálózati biztonságossá jelentette time-tested, és kihasználhatják a tesztkörnyezet és újbóli protokollok bizonyítása iparági biztonsági. 

## <a name="authentication"></a>Hitelesítés

Milyen szereplője, eszközök és összetevők teljes körű IoT-megoldás értékesnek kézbesítését részt ismerete nélkül egy kétséges kiemelkedő megbízhatósági fejlesztése során.  Ilyen Tudásbázis történő beléptetésre engedélyezése való biztonságos accountability kínál.  Az Azure IoT peremhálózati eléri a Tudásbázis-hitelesítésen keresztül.  Az elsődleges módszer hitelesítéshez az Azure IoT peremhálózati platformhoz Tanúsítványalapú hitelesítés használata.  A mechanizmus előírásait nyilvános kulcsokra épülő infrastruktúrát (PKiX) az Internet Engineering Task Force (IETF) által meghatározott osztályból származik.     

Az Azure IoT peremhálózati biztonsági keretrendszere meghívja a tanúsítvány egyedi identitások az eszközök, modulok (tárolókat foglalják magukban az eszközön belüli logika) és szereplője használják az Azure IoT peremhálózati eszköz a fizikailag vagy hálózati kapcsolaton keresztül kell azt.  Nem minden forgatókönyv vagy összetevő nyújthatnak maga a tanúsítvány alapú hitelesítést, amelynek a biztonsági keretrendszer bővítési kínál az Elhelyezés biztonságos rendszere. 

## <a name="authorization"></a>Engedélyezés

Szolgáltató és a vezérlés hozzáférés delegálására képes elengedhetetlen alapvető biztonsági irányelveket – a legalacsonyabb jogosultsági szint elvét elérésére.  Eszközök, modulok és szereplője előfordulhat, hogy hozzáférést csak erőforrásokhoz és adatokhoz, az engedély hatókörén belül, és csak akkor, ha architektúráját engedélyezett.  Ez azt jelenti, hogy néhány engedélyekre elegendő jogosultsága és mások architektúráját kényszerített konfigurálhatók.  Például egy modul használatával kezdeményeznek kapcsolatot az Azure IoT Hub engedélyezhető kiemelt konfigurálással, amíg nincs miért egy Azure IoT peremhálózati eszköz a modul hozzá kell férnie egy másik Azure IoT peremhálózati eszköz modul iker OK.  Ezért az utóbbi kellene lennie architektúráját kizár elmozdulást. 

Más hitelesítési sémák a következők: tanúsítvány-aláírási jogosultságokat, valamint a szerepköralapú hozzáférés-vezérlést (RBAC).  Biztonsági keretrendszer kiterjeszthetőség lehetővé teszi a más érett hitelesítési sémák elfogadását. 

## <a name="attestation"></a>Igazolás

Állapotigazolási szoftver bits sértetlenségének biztosítja.  Fontos felderítésére és megelőzésére kártevő szoftver.  Az Azure IoT peremhálózati biztonsági keretrendszere osztályoz igazolás a három fő kategóriák:

* Statikus igazolás
* Futásidejű igazolás
* Szoftver-igazolás

### <a name="static-attestation"></a>Statikus igazolás

Statikus igazolás az összes szoftver az operációs rendszerek, minden futtatókörnyezetek, beleértve integritásának ellenőrzését, és bekapcsolási eszköz konfigurációs adatokat.  Ez gyakran nevezik biztonságos rendszerindítást.  Az Azure IoT peremeszközök biztonsági keretét szilícium szállítókkal is kiterjed, és magában foglalja a biztonságos ingrained hardverképességeinek ahhoz, hogy biztosítsa a statikus igazolás folyamatok. Ezeket a folyamatokat tartalmazzák a biztonságos rendszerindítás és biztonságos belső vezérlőprogramjának azonnali frissítése a folyamatok.  Működik-e a szilícium szállítók szorosan együttműködve megszünteti a felesleges belső vezérlőprogram rétegek ezáltal minimalizálja a fenyegetés felületet. 

### <a name="runtime-attestation"></a>Futásidejű igazolás

Után a rendszer egy érvényesített rendszerindítási folyamat befejeződött, és működik-e és fut, jól kidolgozott biztonságos rendszerek észleléséhez volna megkísérli szúrjon kártevő szoftver, a rendszer portokat és interfészeket keresztül, és tegye meg a megfelelő ellenintézkedések.  Az intelligens peremeszközök fizikai fogva a rosszindulatú szereplője szúrjon malcontent eszköz felületek, például az illetéktelen módosítást eltérő módon lehet, és ügyféloldali csatorna támadások.   Ilyen malcontent, amely rosszindulatú vagy jogosulatlan konfigurációs módosításokat formájában, általában észlelése nem történne a biztonságos rendszerindítási folyamat, mert ezek után történjen, amikor a rendszerindítási folyamat számára.  Ellenintézkedések kínált vagy kényszeríti ki az eszköz hardver jelentősen hozzájárul a warding ilyen veszélyek ki.  A biztonsági keretrendszere az Azure IoT peremhálózati explicit módon hívásokat futásidejű fenyegetések combatting-bővítmények.     

### <a name="software-attestation"></a>Szoftver-igazolás

Intelligens peremhálózati rendszerek például az összes megfelelő rendszer alkalmas frissítések és javítások kell lennie.  Biztonsági fontos a frissítési folyamat más módon potenciális fenyegetési vektoroknak enged utat is lehetnek.  Azure IoT peremhálózati hívások frissítésekhez biztonsági keretében mért, és írja alá a csomagok integritását biztosítja, és a csomagok forrását.  Ez az összes operációs rendszerek és alkalmazás szoftver bits alkalmazandó. 

## <a name="hardware-root-of-trust"></a>Hardver legfelső szintű megbízhatóság

Az intelligens peremeszközök, különösen a helyen, ahol lehetséges rosszindulatú szereplője fizikai hozzáférésük az eszközre telepített sok telepítéséhez a hardver által kínált biztonsági az utolsó védelmi védelemre.  Emiatt az illetéktelen módosítások ellen védett hardver rögzítő megbízhatósági elengedhetetlen leginkább az ilyen környezetekben.  Az Azure IoT szegély biztonsági keretrendszere biztonságos szilícium hardvergyártótól megvásárolható különböző változatban is elkészíti a különböző kockázati profilok és telepítési forgatókönyvek hardver bizalomforrásához kínálta együttműködés terjed ki. Ezek közé tartozik a használata biztonságos szilícium betartásáért közös biztonsági protokoll szabványok, például a platformmegbízhatósági modul (ISO/IEC 11889) és Trusted Computing Group's eszköz azonosítója összeállítás motor (feldarabolására HASZNÁLNAK).  Ezek közé biztonságos enklávét technológiák, például a TrustZones és szoftver őr Extensions (SGX) is tartozik. 

## <a name="certification"></a>Tanúsítvány

Ügyfelek kérdésekre vonatkozó döntések meghozatalában, amikor Azure IoT peremeszközök a telepítés az beszerzése érdekében az Azure IoT peremhálózati keretrendszer magában foglalja a hardvertanúsítvány követelményeit.  Ezeknek a követelményeknek való eligazodást: vonatkozó biztonsági jogcímeinek minősítései közül és érvényesítése biztonsági végrehajtására vonatkozó minősítései közül.  Például egy biztonsági jogcím hitelesítő volna tájékoztatja, hogy az IoT-peremhálózati eszköz használja-e a biztonságos rendszerindítás támadások ellenáll ismert hardver. Egy érvényesítési hitelesítő volna tájékoztatja, hogy a biztonságos hardveren lett a megfelelően megvalósított kínálta ezt az értéket az eszközt.  Egyszerűség elvének betartásával keretében kínál a látnia, minimális tartja certification okozta terheket.   

## <a name="extensibility"></a>Bővíthetőség

Bővíthetőségi Azure IoT peremhálózati biztonsági keretében első osztályú állampolgára.  A különböző üzleti átalakítások vezetői IoT technológia valószínűleg, hogy biztonsági zökkenőmentesen kell alakítani lockstep forgatókönyvek feltörekvő címre.  Az Azure IoT peremhálózati biztonsági keretrendszere kezdődik, amelyen azt a különböző dimenziókban felvenni bővítési buildek alapos rálátással: 

* Első fél biztonsági szolgáltatások, például az eszköz kiépítése szolgáltatáshoz Azure IoT hub.
* Harmadik féltől származó szolgáltatással, például kezelt biztonsági szolgáltatások különböző alkalmazás referenciaegyenesen (például a ipari vagy egészségügyi) vagy technológia fókusz (ilyen például a figyelés biztonsági háló hálózatok vagy szilícium hardver igazolási szolgáltatásokról) a gazdag hálózaton keresztül partnerek.
* A másik biztonsági stratégiák alkalmazásával felvenni a régebbi rendszerekre, például technológiával biztonságos eltérő tanúsítványok hitelesítés és identitás-kezelésre.
* Hardver biztonságos hardveren feltörekvő technológiákkal és szilícium zökkenőmentes elfogadását partner hozzájárulások biztonságos.

Példák néhány dimenziók, a bővítést és Azure IoT peremhálózati biztonsági keretrendszere az célja, hogy a bővíthetőség támogatásához biztonságos alapoktól kell. 

A végén a legmagasabb sikeres, az intelligens él védelméhez annak az eredménye egy nyissa meg a közös érdekelt IoT által vezérelt közösségi hozzájárulásokat együttműködést.  Ezek a hozzájárulások lehet biztonságos technológiák vagy szolgáltatások formájában.  Az Azure IoT peremhálózati biztonsági keretrendszere biztonsági, megbízhatósági és az intelligens él, az Azure-felhőbe integritás ugyanolyan szintű kínálta maximális kitöltésének bővíthető ben szilárd alapokat nyújt.  

## <a name="next-steps"></a>További lépések

További információk az Azure IoT peremhálózati Mitől [biztonságossá tétele az intelligens él][lnk-edge-blog].

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 
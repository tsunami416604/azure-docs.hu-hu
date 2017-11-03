---
title: "Az IoT-biztonsági architektúrája |} Microsoft Docs"
description: "IoT-biztonsági architektúrája irányelvek és szempontok"
services: 
suite: iot-suite
documentationcenter: 
author: YuriDio
manager: timlt
editor: 
ms.assetid: 18ed3eb0-9406-44e1-8a3a-93dc6726c7ac
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: yurid
ms.openlocfilehash: 2482dade7d17d05b2fc90fbf22b0466227a5983b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="internet-of-things-security-architecture"></a>Az eszközök internetes hálózatát biztonsági architektúrája
A rendszer tervezésekor fontos megérteni, hogy a rendszer potenciális fenyegetések, majd a megfelelő védelmekkel ennek megfelelően adja meg, a rendszer tervezett és tervezett. Különösen fontos tervezési kezdetétől a termék a biztonságot szem előtt tartva, mert ismertetése, hogy egy támadó lehet a rendszerbe segítségével győződjön meg arról, hogy megfelelő megoldást legyenek érvényben az elejétől. 

## <a name="security-starts-with-a-threat-model"></a>Biztonsági kezdődik-e a fenyegetések modellezése
A Microsoft fenyegetés modellek hosszú által használt termékek, és rendelkezik a modellezési folyamatának nyilvánosan elérhető a vállalati fenyegetést. A vállalat élmény mutatja be, hogy rendelkezik-e a modellezési fenyegetések Mik a legtöbb azonnali megértése túl nem várt előnyök vonatkozó. Például is létrehoz egy erőfeszítések egy megnyitott vitafórum másokkal a fejlesztői csapat, ami új ötleteket, valamint a termék kívül.

Modellezni célja megértése, hogyan egy támadó a rendszerbe, és gondoskodjon arról, hogy megfelelő megoldást hely lehet. Fenyegetés modellezési kényszeríti a tervezési csapat kell figyelembe venni azok mérséklési, mivel a rendszer úgy van kialakítva, hanem után a rendszer van telepítve. A tény különösen fontos, mert alkalmazásával az eszközök a mezőben a számtalan biztonsági védelmekkel lehessen hozni, nagyon eséllyel fordulnak elő hiba, és bezárja az ügyfelek veszélyben.

Sok a fejlesztési csapat munkaköre egy kiválóan alkalmas a rendszer a funkcionális követelményeinek ügyfelek előnyeit kihasználó rögzítése. Azonban, hogy valaki visszaél előfordulhat, hogy a rendszer nem egyértelmű módon azonosító további kihívást. Modellezni segít megérteni, hogy egy támadó előfordulhat, hogy mit a fejlesztési csapat és okát. Modellezni egy strukturált folyamat, amely létrehoz egy vitafórum a biztonsággal kapcsolatos tervezési döntések a rendszerben, valamint a Tervező menet végzett a hatás biztonsági változik. Ugyan a fenyegetések modellezése egyszerűen egy dokumentumot, az ebben a dokumentációban is ideális, a Tudásbázis, a megszerzett megőrzést folytonosságának megtanulta, és új súgó gyorsan csapat bevezetésében jelöli. Végezetül modellezni egy eredményeit is figyelembe kell venni más vonatkozásai, például hogy milyen biztonsági kötelezettségeit, az ügyfelek számára kívánja engedélyezni. E kötelezettségvállalások modellezni együtt értesíti, és a meghajtó vizsgálata a az eszközök internetes hálózatát (IoT) megoldás.

### <a name="when-to-threat-model"></a>Mikor érdemes a fenyegetés modell
[Modellezni](http://www.microsoft.com/security/sdl/adopt/threatmodeling.aspx) biztosít a legnagyobb értéket, ha a tervezési szakaszban részét képezi. Tervezésekor, hogy a legnagyobb rugalmasságot módosításokat elkerülése érdekében a fenyegetések. A kívánt eredmény fenyegetések kiküszöbölése azért. Sokkal egyszerűbb, mint azok mérséklési hozzáadása, kipróbálása és biztosítása naprakészek maradjanak és továbbá ilyen eltávolítási nem mindig lehetséges legyen. Fenyegetések kiküszöbölhetők a termék több érett válik, és több munkát és a fenyegetések modellezése a korai fejlesztésében mint sokkal nehezebb kompromisszumot pedig végső soron szükséges nehezebben válik.

### <a name="what-to-threat-model"></a>Mi a fenyegetések modellezése
Meg kell szál modell a teljes megoldás és is összpontosítson, a következő területeken:

* A biztonsági és adatvédelmi szolgáltatások
* A funkciókat, amelyek hibák a megfelelő biztonsági
* A funkciókat, amelyeket a megbízhatósági kapcsolat határán touch 

### <a name="who-threat-models"></a>Ki a fenyegetés modellek
Modellezni az a folyamat más.  Jó ötlet a fenyegetés modell dokumentumot mint bármely más összetevő megoldás kezeli, és ellenőrizze azt is. Sok a fejlesztési csapat munkaköre egy kiválóan alkalmas a rendszer a funkcionális követelményeinek ügyfelek előnyeit kihasználó rögzítése. Azonban, hogy valaki visszaél előfordulhat, hogy a rendszer nem egyértelmű módon azonosító további kihívást. Modellezni segít megérteni, hogy egy támadó előfordulhat, hogy mit a fejlesztési csapat és okát.

### <a name="how-to-threat-model"></a>Fenyegetések modellezése hogyan
A fenyegetés modellezési folyamatának áll négy lépést; a lépések a következők:

* Az alkalmazás minta
* Fenyegetések számbavétele
* Veszélyek mérséklése.
* A megoldást ellenőrzése

#### <a name="the-process-steps"></a>A folyamat lépései
Három szabályok megoldás szem előtt tartani a fenyegetések modellezése felépítése közben:

1. Hozzon létre egy referencia-architektúrában kívül diagramja. 
2. Indítsa el a szélesség-első. Áttekintheti, és megismerje a rendszer egészének előtt mély-ról.  Ezzel biztosíthatja, hogy Ön részletesen a megfelelő helyeken.
3. A folyamat meghajtó, ne engedje a folyamat, a meghajtó. Ha problémát talál a modellezési fázisban, és megismerheti, hogy nyissa meg azt a!  Nem látja, hogy slavishly kövesse az alábbi lépéseket.  

#### <a name="threats"></a>Fenyegetések
A fenyegetések modellezése négy fő elemei a következők:

* Folyamatok (web services, a Win32-szolgáltatások, * nix démonok stb. Vegye figyelembe, hogy bizonyos összetett entitások (például a mező átjárók és az érzékelők) azért, mivel a folyamat, amikor ezek a területek a műszaki Lehatolás nincs lehetőség.
* Adatokat tárolja (bárhol tárolja, például a konfigurációs fájlban vagy adatbázis)
* (Ha az adatok áthelyezése az alkalmazás egyéb elemek között) adatfolyama
* Külső entitások (semmit, amely együttműködik a rendszerrel, de nem ellenőrzés alatt áll, az alkalmazás, például használó felhasználók belefoglalásához és hírcsatornák műholdas)

A architekturális diagramja szereplő összes elem tartoznak a különböző fenyegetések; a STRIDE válasszanak használjuk. Olvasási [fenyegetés újra modellezési, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) további információkat a STRIDE elemeket.

Az alkalmazás diagram különböző elemeinek bizonyos STRIDE fenyegetések vonatkoznak:

* Folyamatok STRIDE vonatkoznak.
* Data flow TID vonatkoznak.
* Adattároló TID és vonatkoznak néha R, ha az adattároló naplófájlokat.
* Külső entitások SRD vonatkoznak.

## <a name="security-in-iot"></a>Az IoT biztonsági
Speciális csatlakoztatott eszközök lehetséges interakció területére és interakció kombinációját, amelyek figyelembe kell venni egy keretrendszert biztosít ezen eszközök digitális hozzáférésének biztonságossá tétele jelentős számú rendelkezik. "Digitális hozzáférés" kifejezés itt megkülönböztetni a közvetlen interakció keresztül végzett műveleteket biztonsági amennyiben a fizikai hozzáférés-vezérlés használatával. Például üzembe helyezése az eszköz ellátott zárolást ajtón. Fizikai hozzáférés nem lehet nem engedélyezett a szoftver- és hardverkonfigurációiról, amíg intézkedéseket is bevezető rendszer zavaró tényező való fizikai hozzáférés megakadályozása. 

A kommunikáció minták tájékozódáshoz azt követően áttekintjük "eszköz control" és "eszköz adatok" a figyelmet a azonos szinten. "Eszköz control" semmilyen információt, amely bármelyik fél által biztosított egy eszközön, azzal a céllal, módosítása vagy felé állapotában vagy a környezet állapotát annak viselkedését befolyásoló meghatározhassa. "Eszköz adatok" meghatározhassa semmilyen információt, amely az eszköz állapotát és a megfigyelt állapotáról a környezet más felek bocsát ki.

Ahhoz, hogy az ajánlott biztonsági eljárások optimalizálása érdekében javasoljuk, hogy egy tipikus IoT-architektúra kell osztani több összetevő/zóna a gyakorlatban modellezési fenyegetés részeként. A zónák teljesen ebben a szakaszban leírt, többek között:

* Eszköz,
* A mező átjáró
* A felhő átjárók, és
* Szolgáltatások.

Zónák széleskörű módon a szegmenseket, a megoldás; az egyes zónák gyakran van a saját adatok és a hitelesítési és engedélyezési követelményeinek. Zónák is használható elkülönítési kárt és alacsony megbízhatósági zónák magasabb megbízhatósági zónák hatásának korlátozása.

Minden egyes zónában van eltérő megbízhatósági tartományba, az alábbi ábrán a pontozott piros sorban észlelt. Ezt az adatok/információk átmenet egy forrás egy másikra. Ez a változás során az/adatait lehet Spoofing, Tampering, Letagadhatóság, Információfelfedés, szolgáltatásmegtagadásos és jogosultság jogosultságszint-emelés (STRIDE).

![Az IoT biztonsági zónák](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Az összetevők belül minden határ kitaláltak is kell alávetni STRIDE engedélyezése a teljes 360 fenyegetések modellezése nézet a megoldás. Az alábbi szakaszok ismertetik az egyes összetevők és adott biztonsági problémáit és megoldások, amelyek kell bevezetni.

A következő szakaszok ismertetik szabványos összetevők ezekben a zónákban található.

### <a name="the-device-zone"></a>Az eszköz zóna
Az eszköz környezet egy körül az eszköz azonnali fizikai hely, ahol fizikai hozzáférés, illetve a "helyi hálózat" az eszköz társ-társ digitális elérése esetén valósítható meg. A "helyi hálózat" feltételezett, hogy önálló, és a – szigetelt, de számára – a nyilvános internethez potenciálisan össze, és bármely rövid hatótávolságú vezeték nélküli rádió technológia, amely lehetővé teszi az eszközök társ-társ kommunikációs magában foglalja a hálózat lehet. Létezik *nem* közé tartozik a hálózati virtualizálási technológia létrehozása a helyi hálózaton érhet, és nem is szerepelnek a két eszközök kommunikálhassanak a nyilvános hálózati helyet, ha azok egy társ-társ kommunikációs kapcsolat megadását igénylő nyilvános operátor hálózatok.

### <a name="the-field-gateway-zone"></a>A mező átjáró zóna
A mező az átjáró az eszköz/berendezés vagy néhány általános célú kiszolgáló számítógépes szoftverek kommunikációs engedélyező, és esetleg eszköz verziókezelő rendszer-és eszköz adatfeldolgozási hub szolgáló. A mező átjáró tartozik az a mező átjáró és minden hozzá kapcsolt eszközöket. Foglalja, mező átjárók külső dedikált adatfeldolgozási berendezések működésre, általában kötött helyét, fizikai behatolás potenciálisan vonatkoznak, és csak korlátozott működési redundanciát. Minden, a tegyük fel például, győződjön meg arról, hogy egy mező átjáró általában egy dolog egy touch és szabotálására tudatában funkciójához van. 

Egy mező átjáró eltér puszta forgalom útválasztó, hogy ez volt-e a hozzáférés-kezelés aktív szerepet és információáramlás, ami azt jelenti, az alkalmazás címzett entitás és a hálózati kapcsolat vagy terminál-munkamenet. Egy NAT-eszköz vagy tűzfal, ezzel szemben nem felel meg a mező átjáróként mivel azok nem explicit kapcsolat vagy a munkamenet terminálok, de a ahelyett, hogy egy útvonalat (vagy blokk) kapcsolatok vagy a munkamenetek keresztül őket. A mező átjáró két különböző területére rendelkezik. Egy előtt álló az eszközöket, amelyek kapcsolódnak, és a zóna belső jelöli, és a másik oldal összes külső felek és a zónát a peremhálózaton.   

### <a name="the-cloud-gateway-zone"></a>A felhő átjáró zóna
Átjáró a rendszer, amely lehetővé teszi a távoli kommunikációs eszközök vagy több különböző helyére a mező átjárók és nyilvános hálózati helyet, általában egy felhőalapú vezérlő és az adatok elemző rendszer, az ilyen rendszerek összevonási felé között. Bizonyos esetekben egy felhőátjáróhoz azonnal megkönnyíthetik hozzáférés speciális eszközökre például táblagépek és telefonok részére. Az itt tárgyalt környezetében a "felhő" célja, hogy egy dedikált adatfeldolgozási rendszer, amely nincs kötve ugyanazon a helyen, a csatlakoztatott eszközök vagy mező átjárók hivatkozik. Is felhő zónában, működési mértékek célzott fizikai hozzáférés megakadályozása, és nem feltétlenül érhetők el a "nyilvános" felhőinfrastruktúráját.  

Egy felhőátjáróhoz potenciálisan társítja a hálózati virtualizálási átmeneti területre az átjáró és az összes csatlakoztatott eszközök vagy bármely más hálózati forgalmaktól mező átjárók háromszintű. A felhő átjáró sem egy eszköz, sem a feldolgozási vagy a tárolási létesítmény eszköz adatok; azok a felhő-átjáróval való kapcsolat. A felhő átjáró felhő magához az átjáróhoz valamint mező átjárók és az eszköz közvetlenül vagy közvetve csatlakoztatva tartozik. A zónát a peremhálózaton egy eltérő felületet biztosít, ahol az összes külső felek keresztül kommunikálnak.

### <a name="the-services-zone"></a>A szolgáltatások zóna
A "szolgáltatás" definiálva van ebben a környezetben, a szoftverfrissítési összetevő vagy modul, amely az eszközök mező vagy felhő átjárón keresztül az adatok gyűjtése és elemzése, valamint a parancs és a vezérlés vele.  Szolgáltatások a közvetítő folyamatok. Azok az identitásukat, átjárók és az egyéb alrendszerek alatt jár el, tárolására és adatok elemzése, önállóan kiadni parancsok adatok insights vagy az ütemezések alapuló eszközöket és információt teszi közzé és szabályozása képességek engedéllyel rendelkező végfelhasználók számára.

### <a name="information-devices-vs-special-purpose-devices"></a>Eszközök információk, és speciális eszközök összehasonlítása
Számítógépek, telefonok és táblagépek olyan eszközök elsősorban interaktív információk. Telefonok és táblagépek körül akkumulátor élettartama maximalizálva explicit módon vannak optimalizálva. Lehetőleg kikapcsolása részben Ha egy személy való interakció nem azonnal, vagy ha nem nyújt szolgáltatásokat, például zene lejátszása, vagy a tulajdonos irányítsa egy adott helyen. Rendszerek szempontjából ezek az információk technológia eszközök főként, személyek felé proxyként működő. "Személyek rákötni" arra vonatkozóan, műveletek és a "személyek érzékelők" bemeneti gyűjtése. 

Speciális eszközöket, az összetett gyári éles sorok több ezer összetevőket tartalmaz, egyszerű hőmérséklet-érzékelők eltérőek. Ezek az eszközök sokkal több hatóköre rendeltetésüket, és akkor is, ha a felhasználói felület egyes biztosítanak, nagy mértékben hatóköre való együttműködésre vagy integrálni kell a fizikai világ eszközök. Azok mérésére és környezeti körülmények jelentést, szelepek kapcsolja, vezérlését servos, riasztások hangkártya, fény kapcsoló és számos egyéb feladatok elvégzéséhez. Azok a Súgó gombra működnek, amelyek olyan információkat eszköz esetében túl általános, olcsóbbá túl, túl nagy vagy túl rideg. A konkrét, meghatározott célú azonnal határozzák meg, hogy a műszaki tervezést, is a rendelkezésre álló pénzügyi költségvetési éles és ütemezett élettartama műveletet. Ez a két kulcsfontosságú tényező kombinációja korlátozza az elérhető működési energia költségvetési, fizikai kezdjen, és így rendelkezésre álló tár, számítási és biztonsági képességeket.  

Ha valami "is megnőnek hibás" az automatikus és a távoli vezérelhető eszközök, például fizikai hibák vagy a vezérlő logikai hibák willful jogosulatlan behatolás és módosítását. A termelési sok meg kell semmisíteni, épületek esetleg looted, vagy írása és személyek lehet, hogy sérült, vagy még akkor is, die. Ez az, természetesen teljes különböző osztály kárt valaki maxing kimenő korlátja egy lopott hitelkártya-nál. A biztonsági eszköztár áthelyezése művelet eszközök és parancsok, amelyek helyezi át, a következőkre végül eredményezi érzékelő adatokat elektronikus kereskedelmi vagy banki adathierarchia nagyobbnak kell lennie. 

### <a name="device-control-and-device-data-interactions"></a>Eszköz vezérlő és eszköz adatok interakciók
Speciális csatlakoztatott eszközök lehetséges interakció területére és interakció kombinációját, amelyek figyelembe kell venni egy keretrendszert biztosít ezen eszközök digitális hozzáférésének biztonságossá tétele jelentős számú rendelkezik. "Digitális hozzáférés" kifejezés itt megkülönböztetni a közvetlen interakció keresztül végzett műveleteket biztonsági amennyiben a fizikai hozzáférés-vezérlés használatával. Például üzembe helyezése az eszköz ellátott zárolást ajtón. Fizikai hozzáférés nem lehet nem engedélyezett a szoftver- és hardverkonfigurációiról, amíg intézkedéseket is bevezető rendszer zavaró tényező való fizikai hozzáférés megakadályozása. 

A kommunikáció minták tájékozódáshoz azt követően áttekintjük "eszköz control" és "eszköz adatok" hitelesítésszolgáltatóéval azonos szintű figyelmét modellezni közben. "Eszköz control" semmilyen információt, amely bármelyik fél által biztosított egy eszközön, azzal a céllal, módosítása vagy felé állapotában vagy a környezet állapotát annak viselkedését befolyásoló meghatározhassa. "Eszköz adatok" meghatározhassa semmilyen információt, amely az eszköz állapotát és a megfigyelt állapotáról a környezet más felek bocsát ki. 

## <a name="threat-modeling-the-azure-iot-reference-architecture"></a>Az Azure IoT-referenciaarchitektúra modellezési fenyegetés
A Microsoft Azure IoT a modellezési fenyegetés ehhez a fent vázolt keretében használja. Az alábbi szakaszban ezért használjuk Azure IoT-Referenciaarchitektúra konkrét példája bemutatják, gondolja át az IoT modellezési fenyegetés és a fenyegetések azonosított megoldására. A mi esetünkben felismertük fókusz négy fő területet:

* Eszközök és az adatforrás,
* Adatok átvitel
* Eszköz és az események feldolgozásával, és
* Bemutató

![Az Azure IoT modellezési fenyegetés](media/iot-security-architecture/iot-security-architecture-fig2.png) 

Az alábbi ábrán a Microsoft fenyegetések modellezése eszköz által használt adatfolyam-Diagram modell segítségével a Microsoft IoT-architektúra egyszerűsített nézetét biztosítja:

![Az Azure IoT eszközzel MS fenyegetések modellezése modellezési fenyegetés](media/iot-security-architecture/iot-security-architecture-fig3.png)

Fontos megjegyezni, hogy a architektúra elválasztja az eszköz- és átjáró lehetőségeket. Ez lehetővé teszi, hogy a felhasználót, hogy kihasználja az átjáró eszközök, amelyek nagyobb biztonságot nyújt: képesek kommunikálni a átjáró biztonságos protokollal, amelyhez általában kisebb feldolgozási terhelés, hogy a natív eszköz -, például egy termosztát - önállóan biztosítani. Az Azure-szolgáltatások zóna feltételezzük, hogy az átjáró az Azure IoT-központ szolgáltatás által képviselt.

### <a name="device-and-data-sourcesdata-transport"></a>Eszközök és adatok források/adatok átvitel
Ez a szakasz a fent vázolt modellezni identitásalapú architektúra felderíti, és áttekintést hogyan jelenleg éppen aktuális néhány rejlő fontos szempont. A fő elemei a fenyegetések modellezése tárgyaljuk:

* (Azok alatt a vezérlő és a külső elemek) folyamatok
* Kommunikációs (más néven adatfolyamok)
* Tárhely (más néven adattároló)

#### <a name="processes"></a>Folyamatok
Az egyes kategóriák az Azure IoT-architektúra leírt, az azt próbálja számos különböző fenyegetések mérséklésére között szerepel az/adatait különböző szakaszokat: folyamat, a kommunikáció és a tároló. Az alábbiakban azt áttekintést a leggyakrabban a "folyamat" kategória, hogyan ezek sikerült legjobb szüntethető áttekintése után: 

**(S)-címek hamisítását**: támadó egy eszközről, akár szoftveres vagy hardveres szinten, előfordulhat, hogy kibontása kriptográfiai kulcsokat tárol, és ezt követően a rendszer egy másik fizikai vagy virtuális eszköz identitás alatt az eszköz a kulcsokat tárol hozzáférés foglalt a. Egy jó ábra, amely bekapcsolja a TV és népszerű prankster eszközök távoli szabályozza.

**Letiltja a szolgáltatást (D)**: egy eszköz nem működik, vagy történő kommunikációhoz választógomb-használati vagy kivágáshoz fenyegetéseknek zavarja alkalmas megjeleníthetők. Például a felügyeleti fényképezőgép, amelyekről a szándékosan kiejtett power vagy a hálózati kapcsolat nem küld adatokat, minden.

**(T) illetéktelen**: támadó részben vagy egészben helyettesítheti az eszközön futó szoftver kihasználhatják a valódi identitás, az eszköz, ha a kulcs anyagot vagy a titkosítási kulcs anyagok okozó létesítményekben álltak rendelkezésre a tiltott program a kicserélt szoftver lehetővé. Például egy támadó előfordulhat, hogy kihasználja elfogására és ne jelenjen meg többé a kommunikációs elérési úton az eszköz adatait, és cserélje le a lopott kulcsokat tárol a hitelesített hamis adatok kibontott kulcsokat tárol.

**Információk felfedése (I)**: Ha az eszközt úgy szoftvert futtat, úgy szoftverhez sikerült potenciálisan szivárgás lépett fel adatokat a jogosulatlan felek. Például egy támadó előfordulhat, hogy kihasználja behelyezése maga az eszköz és a vezérlő vagy mező átjáró vagy siphon ki adatokat az átjáró közötti kommunikáció elérési kibontott kulcsokat tárol.

**A jogosultság E illetéktelen**: egy eszköz, amelyet megadott funkciót is kényszeríthető, valami mással. Például, hogy nyissa meg a fele módon van programozott szelep is megtevésztve egészen megnyitásához.

| **Összetevő** | **Fenyegetések** | **Megoldás** | **Kockázati** | **Megvalósítása** |
| --- | --- | --- | --- | --- |
| Eszköz |S |Identitás hozzárendelése az eszközt, és az eszköz hitelesítése |Eszköz vagy az eszköz része cseréje egy másik eszköz. Hogyan azt, hogy azt a megfelelő eszközre van szó? |Az eszköz használata a Transport Layer Security (TLS) vagy az IPSec-hitelesítéséhez. Infrastruktúra támogatnia kell a előmegosztott kulccsal (PSK), amely nem tudja kezelni a teljes aszimmetrikus titkosítási eszközökön. Használja ki az Azure AD [OAuth](http://www.rfc-editor.org/in-notes/internet-drafts/draft-ietf-ace-oauth-authz-01.txt) |
| TRID |Alkalmazni tamperproof mechanizmusok az eszközön például oly módon, hogy nagyon nehéz a kulcsokat és más kriptográfiai anyag kibontani az eszköz nem lehetséges. |A kockázat, ha valaki van illetéktelen módosítását az eszköz (fizikai zavaró tényező). Hogyan történik a Microsoft, az eszköz nem illetéktelenül módosítva. |A leghatékonyabb megoldás egy platformmegbízhatósági modul (TPM) képesség, amely lehetővé teszi a kulcsok tárolása, amelyből a kulcsok nem olvasható, de csak akkor használható, amelyek a kulcsot használják, de soha nem fedjük fel a kulcs titkosítási műveletek különleges-chip áramkört. Az eszköz titkosítása memória. Az eszköz kulcskezelést. A kód aláírása. | |
| E |Ha az eszköz hozzáférés-vezérlés. Hitelesítési séma. |Ha az eszköz lehetővé teszi, hogy az egyes műveletek végrehajtása a külső forrásból, vagy akár sérült biztonságú érzékelők parancsok, engedélyezi a támadás műveletek elvégzésére, egyéb módon nem érhető el. |Ha az eszköz a hitelesítési séma | |
| A mező átjáró |S |Az átjáró (tanúsítványalapú, PSK, a jogcím-alapú,...) mezőben átjáró hitelesítése |Ha valaki mező átjáró hamis, majd azt is jelenthet maga mint bármely olyan eszközről. |A TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Ugyanazokat általános – eszközök kulcs tárolására és a tanúkiszolgáló aggályokat legjobb esetben van TPM használatát. Az IPSec támogatja a vezeték nélküli érzékelő hálózatok (WSN) bővítmény 6LowPAN. |
| TRID |A mező átjáró (TPM?) hamisítás elleni védelme |Információk felfedése és az adatok illetéktelen módosítását hamisításra épülő támadásoknak, amely megtéveszteni a felhő átjáró számbavétele, mező-átjáróra van szó okozhat |Memória titkosítás, a TPM által, hitelesítés. | |
| E |Hozzáférés-vezérlési mechanizmus mező átjáró | | | |

Íme néhány példa a ebbe a kategóriába tartozó fenyegetések:

Címhamisítást: Támadó előfordulhat, hogy kinyerése kriptográfiai kulcsokat tárol egy eszközt, vagy a szoftver vagy hardverszintű, és ezt követően a rendszer egy másik fizikai vagy virtuális eszköz identitás alatt az eszköz a kulcsokat tárol vették a hozzáférés.

**Szolgáltatásmegtagadást**: egy eszköz nem működik, vagy történő kommunikációhoz választógomb-használati vagy kivágáshoz fenyegetéseknek zavarja alkalmas megjeleníthetők. Például a felügyeleti fényképezőgép, amelyekről a szándékosan kiejtett power vagy a hálózati kapcsolat nem küld adatokat, minden.

**Illetéktelen**: támadó részben vagy egészben helyettesítheti az eszközön futó szoftver kihasználhatják a valódi identitás, az eszköz, ha a kulcs anyagot vagy a titkosítási kulcs anyagok okozó létesítményekben álltak rendelkezésre a tiltott program a kicserélt szoftver lehetővé.

**Illetéktelen**: egy felügyeleti kamera, amely egy üres Előszoba látható pontszámot képe látható ilyen Előszoba fényképe célzó sikerült. Füst vagy tűz érzékelő sikerült készítőnek tekintene valaki rendelkezik egy világosabb rajta. Mindkét esetben az eszköz valószínűleg technikailag teljesen megbízható-e a rendszer felé, de úgy adatokat fog jelenteni.

**Illetéktelen**: támadó előfordulhat, hogy kihasználja a elfogására és ne jelenjen meg többé a kommunikációs elérési úton az eszköz adatait, és cserélje le a lopott kulcsokat tárol a hitelesített hamis adatok kibontott kulcsokat tárol.

**Illetéktelen**: támadó részben vagy teljesen helyettesítheti az eszközön futó szoftver kihasználhatják a valódi identitás, az eszköz, ha a kulcs anyagot vagy a titkosítási kulcs anyagok okozó létesítményekben álltak rendelkezésre a tiltott program a kicserélt szoftver lehetővé.

**Információk felfedése**: Ha az eszközt úgy szoftvert futtat, úgy szoftverhez sikerült potenciálisan szivárgás lépett fel adatokat a jogosulatlan felek.

**Információk felfedése**: támadó előfordulhat, hogy kihasználja a behelyezése maga az eszköz és a tartományvezérlő vagy mező átjáró vagy siphon információk ki felhő-átjáró közötti kommunikáció elérési kibontott kulcsokat tárol.

**Szolgáltatásmegtagadást**: az eszköz ki van kapcsolva, vagy egy módba kapcsolva, ahol kommunikáció nincs lehetőség (ez utóbbi érték az ipari gépekről sok szándékos).

**Illetéktelen**: az eszköz képes arra, hogy ismeretlen a rendszerbe (kívül ismert hitelesítési paraméterek) állapotban működjön, és így adja meg hibásan lesznek értelmezve adatok

**Jogok kiterjesztése**: egy eszköz, amelyet megadott funkciót is kényszeríthető, valami mással. Például, hogy nyissa meg a fele módon van programozott szelep is megtevésztve egészen megnyitásához.

**Szolgáltatásmegtagadást**: az eszközt be kell kapcsolni az olyan állapotban, amelyben kommunikáció nem lehetséges.

**Illetéktelen**: az eszköz képes arra, hogy ismeretlen a rendszerbe (kívül ismert hitelesítési paraméterek) állapotban működjön, és így biztosít az adatokat, hibásan lesznek értelmezve.

**-Címek hamisítását vagy Tampering/Repudiation**: Ha nem biztonságos (ez utóbbi érték ritkán fogyasztói távoli vezérlők esetében) a támadó névtelenül kezelhető az eszköz állapotát. Egy jó ábra, amely bekapcsolja a TV és népszerű prankster eszközök távoli szabályozza.

#### <a name="communication"></a>Kommunikáció
Eszközök, eszközök és a mező átjárók és az eszköz és a felhő átjáró közötti kommunikáció görbe körül fenyegetéseket. Az alábbi táblázat néhány útmutatás körül nyitott sockets meg az eszköz és a VPN rendelkezik:

| **Összetevő** | **Fenyegetések** | **Megoldás** | **Kockázati** | **Megvalósítása** |
| --- | --- | --- | --- | --- |
| Az IoT-központ eszköz |TID |(D) A TLS (PSK/RSA) a forgalom titkosításához |Lehallgatást, vagy zavaró az eszköz és az átjáró közötti kommunikáció |A biztonsági protokoll szintjén. Az egyéni protokollok igazolnia kell a rájönni arra, hogyan lehet megvédeni őket. A legtöbb esetben a kommunikáció akkor történik meg az adott eszközről az IoT hubhoz (eszköz kezdeményezi a kapcsolatot). |
| Eszköz eszköz |TID |(D) TLS (PSK/RSA) a forgalom titkosításához. |Az eszközök közötti átvitel során adatainak olvasása. Az adatok illetéktelen módosításának. Túl van terhelve az eszközt az új kapcsolatok |A biztonsági protokoll szint (MQTT/AMQP/HTTP/CoAP. Az egyéni protokollok igazolnia kell a rájönni arra, hogyan lehet megvédeni őket. A megoldás a DoS fenyegetés felhő vagy mező átjárón keresztül eszközök partnert, és lehet őket az ügyfelek felé a hálózat csak act. A társviszony-létesítést azt eredményezheti, hogy az átjáró által lett közvetítőalapú után a partnerek között közvetlen kapcsolat |
| Külső entitás eszköz |TID |Az eszközhöz külső entitás erős párosítás |A kapcsolat az eszköz lehallgatás. A kommunikációs zavarja a eszköz |Az eszköz NFC/Bluetooth LE a külső entitás biztonságosan párosításhoz. Az eszköz (tényleges) működési panel vezérlése |
| A mező átjáró átjáró |TID |TLS (PSK/RSA) a forgalom titkosításához. |Lehallgatást, vagy zavaró az eszköz és az átjáró közötti kommunikáció |Biztonsági szint (MQTT/AMQP/HTTP/CoAP) protokollt. Az egyéni protokollok igazolnia kell a rájönni arra, hogyan lehet megvédeni őket. |
| Eszköz átjáró |TID |TLS (PSK/RSA) a forgalom titkosításához. |Lehallgatást, vagy zavaró az eszköz és az átjáró közötti kommunikáció |Biztonsági szint (MQTT/AMQP/HTTP/CoAP) protokollt. Az egyéni protokollok igazolnia kell a rájönni arra, hogyan lehet megvédeni őket. |

Íme néhány példa a ebbe a kategóriába tartozó fenyegetések:

**Szolgáltatásmegtagadást**: korlátozott eszközök általában alatt álló DoS amikor azokat aktívan figyelni a bejövő kapcsolatok vagy kéretlen datagramok a hálózaton, mert megnyithat egy támadó sok kapcsolattal párhuzamosan és nem szolgáltatás őket vagy nagyon lassan szolgáltatás őket, vagy lehet, hogy az eszköz árasztott kéretlen forgalommal. Mindkét esetben az eszköz hatékonyan megjeleníthetők működésképtelenné válik a hálózaton.

**Címek hamisítása, az információk felfedése**: korlátozott és speciális eszközre gyakran rendelkezik egy összes biztonsági létesítményekben például jelszó vagy PIN-kód védelmet, vagy teljesen támaszkodnak megbízó a hálózaton, ami azt jelenti, számára biztosított információk elérését, amikor egy eszköz ugyanazon a hálózaton, és erre a hálózatra gyakran csak védik a megosztott kulcs. Amely azt jelenti, hogy a megosztott titkot az eszköz vagy a hálózati felfedett, ha lehetséges, hogy az eszköz vezérléséhez, és vegye figyelembe az adatok az eszközről a kibocsátott.  

**-Címek hamisítását**: támadó előfordulhat, hogy intercept vagy részben bírálja felül a szórás és a kezdeményező (a középső man) hamisításának

**Illetéktelen**: támadó előfordulhat, hogy intercept vagy részben bírálja felül a szórás, és hamis információk küldése 

**Információk felfedése:** egy támadó előfordulhat, hogy kihallgassa a közvetítés és engedély nélkül információkhoz **szolgáltatásmegtagadást:** egy támadó előfordulhat, hogy a szórási jel dzsem, és a megtagadási információk terjesztési

#### <a name="storage"></a>Storage
Minden eszköz és a mező átjáró storage (az adatok, az operációs rendszer lemezkép tárolási Queuing ideiglenes) rendelkezik.

| **Összetevő** | **Fenyegetések** | **Megoldás** | **Kockázati** | **Megvalósítása** |
| --- | --- | --- | --- | --- |
| Eszköz tárhelyén |TRID |Tárolás titkosítását, a naplók aláírása |A tároló (személyazonosításra alkalmas adatokat), telemetriai adatok illetéktelen módosításának adatok olvasása. Illetéktelenül aszinkron vagy gyorsítótárazott parancs vezérlő adatokat. Konfiguráció vagy a belső vezérlőprogram frissítési csomagokat illetéktelenül során gyorsítótárazva, vagy helyileg várólistára vezethet feltörésének az operációs rendszer és/vagy a rendszer összetevők |Titkosítás, az üzenethitelesítő kódot (MAC) vagy a digitális aláírás. Ha lehetséges, erős hozzáférés keresztül erőforrásokhoz való hozzáférést vezérlő listák (ACL) vagy hozzáférési engedélyekkel. |
| Az eszköz operációs rendszere kép |TRID | |Az operációs rendszer illetéktelenül / az operációs rendszer összetevőit cseréje |Csak olvasható az operációs rendszer partíció aláírt operációsrendszer-lemezképek, a titkosítás |
| A mező átjárói tárolás (az adatok queuing) |TRID |Tárolás titkosítását, a naplók aláírása |A tároló (személyazonosításra alkalmas adatokat), telemetriai adatok illetéktelen módosításának adatok olvasása illetéktelenül módosítsák a várólistára vagy gyorsítótárazott parancs vezérlő adatokat. (Az eszközök vagy mező átjáró szánt) konfigurációja vagy a belső vezérlőprogram frissítési csomagokat illetéktelenül során gyorsítótárazva, vagy helyileg várólistára vezethet feltörésének az operációs rendszer és/vagy a rendszer összetevők |A BitLocker |
| A mező az átjáró operációsrendszer-lemezképek |TRID | |Az operációs rendszer illetéktelenül / az operációs rendszer összetevőit cseréje |Csak olvasható az operációs rendszer partíció aláírt operációsrendszer-lemezképek, a titkosítás |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Eszköz és az esemény feldolgozása/felhő átjáró zóna
Egy átjáró a rendszer, amely lehetővé teszi a távoli kommunikációs eszközök vagy több különböző helyére a mező átjárók és nyilvános hálózati helyet, általában egy felhőalapú vezérlő és az adatok elemző rendszer, az ilyen rendszerek összevonási felé között. Bizonyos esetekben egy felhőátjáróhoz azonnal megkönnyíthetik hozzáférés speciális eszközökre például táblagépek és telefonok részére. A környezetben, az itt tárgyalt célja, hogy "a felhő" hivatkozik egy dedikált adatfeldolgozási rendszer, amely nincs kötve a csatlakoztatott eszközök vagy mező átjárók ugyanazon a helyen, és ahol a működési intézkedések megakadályozása megcélzott fizikai hozzáféréssel, de nem feltétlenül "nyilvános felhő" infrastruktúrára.  Egy felhőátjáróhoz potenciálisan társítja a hálózati virtualizálási átmeneti területre az átjáró és az összes csatlakoztatott eszközök vagy bármely más hálózati forgalmaktól mező átjárók háromszintű. A felhő átjáró sem egy eszköz, sem a feldolgozási vagy a tárolási létesítmény eszköz adatok; azok a felhő-átjáróval való kapcsolat. A felhő átjáró felhő magához az átjáróhoz valamint mező átjárók és az eszköz közvetlenül vagy közvetve csatlakoztatva tartozik.

Átjáró többnyire egyéni beépített, amelyhez a mező átjáró és az eszközök csatlakozni kitett végpontokon szolgáltatásként futó szoftver. Ilyen úgy kell megtervezni a biztonságot szem előtt. Kövesse az [SDL](http://www.microsoft.com/sdl) folyamatok tervezéséről és kiépítéséről ezt a szolgáltatást. 

#### <a name="services-zone"></a>Szolgáltatások zóna
A rendszer (vagy a tartományvezérlő) nem egy szoftveres megoldás, amely egy eszközt, vagy egy mező átjáróval vagy a felhő-átjáró egy vagy több eszköz ellenőrzésére és/vagy gyűjtése és/vagy tárolására és/vagy eszköz adatelemzés bemutató vagy későbbi ellenőrzési célból felületek. Vezérlő rendszereket, amely személyek való együttműködéshez azonnal elősegíti az ismertető a hatókör csak entitást. A kivétel: az eszközök, például egy kapcsoló, amely lehetővé teszi, hogy egy személy, hogy az eszköz kikapcsolása vagy más tulajdonságainak módosítása, és amelynek nincs digitálisan elérhető funkcionális egyenértékű köztes fizikai vezérlő felületek. 

Köztes fizikai vezérlő felületek azok hol tetszőleges logika szabályozó korlátozza a fizikai vezérlő felület függvény úgy, hogy megfelelője távolról kezdeményezhetők, vagy távoli bemeneti bemeneti ütközik elkerülhetők – ilyen intermediated vezérlő felületek elméleti szinten van csatolva egy helyi verziókezelő rendszer, amely kihasználja a azonos alapul szolgáló bármely más távvezérlési rendszert, hogy az eszköz párhuzamosan is kapcsolódhat. A felhőre lehet olvasni a felső veszélyek [felhőalapú biztonsági Alliance (CSA)](https://cloudsecurityalliance.org/research/top-threats/) lap.

## <a name="additional-resources"></a>További források
További információt a következő cikkekben talál:

* [SDL fenyegetés modellezési eszköz](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [A Microsoft Azure IoT-referenciaarchitektúra](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)

## <a name="see-also"></a>Lásd még:
Az IoT-megoldásból biztosításával kapcsolatos további tudnivalókért lásd: [IoT üzembe][lnk-security-deployment].

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [Előre konfigurált prediktív karbantartási megoldás áttekintése][lnk-predictive-overview]
* [Gyakran ismételt kérdések az IoT Suite-ról][lnk-faq]

Az IoT-központ biztonsági olvashat [IoT-központ való hozzáférés szabályozása] [ lnk-devguide-security] az IoT Hub fejlesztői útmutató.

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md

[lnk-security-deployment]: iot-suite-security-deployment.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
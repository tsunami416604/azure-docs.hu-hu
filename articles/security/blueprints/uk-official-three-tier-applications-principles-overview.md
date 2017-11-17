---
title: "Az Azure tervezetének Automation - nemzeti számítógépes biztonsági központ biztonsági alapelvei áttekintése"
description: "Az Azure tervezetének Automation - nemzeti számítógépes biztonsági központ biztonsági alapelvei áttekintése"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: bbf58215a4d236c70bf988cbfa1c8491055b5183
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>Nemzeti számítógépes biztonsági központ biztonsági alapelvei áttekintése


> [!NOTE]
> Biztonsági alapelvek által a nemzeti számítógépes biztonsági központ (NCSC) vannak meghatározva. Tekintse meg a tesztelési eljárások NCSC dokumentációjában és útmutatást biztosít minden rendszerbiztonsági tagot.



## <a name="ncsc-cloud-security-principle-1"></a>NCSC felhő rendszerbiztonsági tagot 1
### <a name="data-in-transit-protection"></a>Adatok védelmére átvitel közben
Felhasználói adatok hálózatokon áthaladó illetéktelen módosítás és a lehallgatás elleni megfelelően védeni kell.

Ez egy kombinációját kell elérni:

- hálózati védelem – a támadó képes adatlopási megtagadása
- titkosítás - megtagadása a támadó képes adatokat olvasni.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | Az Azure tervezetének konfigurálja az erőforrások csak biztonságos protokollok használatával kommunikálni. Az Alkalmazásátjáró WAF összetevőjét fogadjanak észlelésében külső használja a HTTPS és a TLS és a háttérkészlet csak a HTTPS és a TLS protokollt használó kommunikációra van konfigurálva. Távoli asztali szolgáltatások biztonságos kapcsolatok használatára van konfigurálva. VPN webes forgalom AppGateway és az Azure közötti biztonságossá tételére szolgál. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Azure szabványos Transport Layer Security (TLS) 1.2-es protokollt használ 2048 bites RSA/SHA-256 titkosítás kulccsal rendelkező által CESG/NCSC, ajánlott az ügyfél és a felhő között, mind belső Azure rendszerek közötti kommunikáció titkosításához és datacentres. Például ha rendszergazdák a Microsoft Azure portál használatával kezelheti a szolgáltatás a munkahelyén, a portál és a felügyeleti eszközök között továbbított adatok küld egy TLS titkosított csatornán keresztül. Egy szabványos webböngészővel Outlook.com-os csatlakozó e-mail felhasználó, a HTTPS-kapcsolatot biztosít egy biztonságos csatornát fogadására és e-mailek küldéséhez.<br /> <br /> Az Azure kínál az ügyfelek a saját adatok és a forgalom védelmét biztosító lehetőségek közül. A tanúsítvány Azure épített funkciók rugalmasan rendszergazdák tanúsítványok és titkosítási kulcsokat a rendszer, egyes szolgáltatások, secure shell (SSH) munkamenetek, virtuális magánhálózati (VPN) kapcsolatok konfigurálása Távoli asztal (RDP) kapcsolatokat, és egyéb funkciókat. <br /><br /> A fejlesztők a Microsoft .NET-keretrendszer beépített kriptográfiai szolgáltatókat (CSP) a Advanced Encryption Standard (AES) algoritmusok, például ellenőrzése digitális feladatokhoz kezelésére biztonságos kivonatoló algoritmus (SHA-2) funkcióit és eléréséhez aláírások. Az Azure Key Vault segítségével az ügyfelek védelme a titkosítási kulcsok és titkos hardveres biztonsági modulokkal (HSM) tárolja őket. |


 ## <a name="ncsc-cloud-security-principle-2"></a>NCSC felhő rendszerbiztonsági tagot 2
### <a name="asset-protection-and-resilience"></a>Eszköz védelmét, és a rugalmasság
Felhasználói adatok és az eszközök tárolásához vagy feldolgozása, fizikai illetéktelen módosítások ellen, adatvesztés, sérülés vagy lefoglalása meg kell védeni.

A szempontot figyelembe kell venni a következők:

1. Fizikai helyét és jogi joghatóság alá
2. Datacentre biztonsági
3. Rest-védelmi adatok
4. Adatok Sanitisation
5. Berendezések kivezetési
6. Fizikai rugalmasság és a rendelkezésre állás


 ## <a name="ncsc-cloud-security-principle-21"></a>NCSC felhő rendszerbiztonsági tagot 2.1
### <a name="physical-location-and-legal-jurisdiction"></a>Fizikai helyét és jogi joghatóság alá
Ahhoz, hogy ismerje meg a jogi körülmények között, amely alatt az adatok érhető el. meg kell adnia a helyeken, ahol tárolni, feldolgozott és felügyelt Ön hozzájárulása nélkül.
Tartalma is kell tudni, hogyan adatkezelési szabályozza a szolgáltatáson belül lépnek érvénybe, UK jogszabályok viszonyítva. Felhasználói adatok védelme a nem megfelelő, vagy jogszabályi és szabályozásoknak szankció reputational kárt okozhat.


**Feladatkörök:**`Customer`

> [!NOTE]
> Azure-szolgáltatásokkal – telepítve vannak-e, és a felhasználók beállíthatják az egyes Azure-szolgáltatások felhasználói adatok tárolása csak egy régiót. A Microsoft Azure globális szintű rendelkezésre állásának és megbízhatóságának biztosítása érdekében világszerte elérhető datacentres listáját tartalmazza. Minden Azure datacentres hitelesített az ISO/IEC 27001:2013 ellen. A UK földrajzi 2 régiók áll: Egyesült Királyság déli régiója és Egyesült Királyság nyugati régiója.

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének kérni fogja a rendszergazda mely-terület az Azure-erőforrások telepítése. A központi telepítéshez javasolt régiók a következők: Egyesült Királyság déli régiója vagy Egyesült Királyság nyugati régiója. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Nem alkalmazható |


 ## <a name="ncsc-cloud-security-principle-22"></a>NCSC felhő rendszerbiztonsági tagot 2.2
### <a name="datacentre-security"></a>Datacentre biztonsági
Helyek használható felhőalapú szolgáltatásokhoz kell fizikai védelmet biztosít a jogosulatlan hozzáférés, illetéktelen módosítás, ellopása vagy rendszerek újrakonfigurálása. Nem megfelelő védelmet a nyilvánosságra, módosítás vagy adatvesztést eredményezhet.


**Feladatkörök:**`Microsoft Azure`


|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure datacentres; datacentre biztonsági védelmi intézkedések megvalósítva, és a Microsoft Azure felügyelni. Ez az elv a Microsoft Azure-ból örökölt. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft Azure ezen elv megvalósítja az ügyfeleik nevében. A Microsoft Azure fut földrajzilag elosztott Microsoft létesítményekben, lemezterület és segédprogramok megosztása más Microsoft online services. Minden egyes 24 x 7 x 365 futtatásra tervezték, és különböző szabványos védett műveletek áramszünet esetén, a fizikai behatolás és a hálózati kimaradások elleni védelme. Ezek datacentres ipari szabványok (például ISO 27001) a fizikai biztonság és a rendelkezésre állási felel meg. Ezeket felügyelt, figyeli, és a Microsoft operations személyzet felügyelt. <br /> <br /> Lehet, hogy Azure-ügyfél abban, hogy fizikai biztonsági intézkedéseinek, minden Azure datacentres tanúsítványok rendelkezik, az ISO/IEC 27001:2013 standard összes datacentres Azure miatt. A UK földrajzi 2 régiók áll: Egyesült Királyság déli régiója és Egyesült Királyság nyugati régiója. |


 ## <a name="ncsc-cloud-security-principle-23"></a>NCSC felhő rendszerbiztonsági tagot 2.3
### <a name="data-at-rest-protection"></a>Rest-védelmi adatok
Annak érdekében, hogy nincs jogosulatlan felek infrastruktúra fizikai hozzáféréssel rendelkező, a szolgáltatáson belül tárolt felhasználói adatok meg kell védeni, függetlenül a tárolási adathordozókon, amelyen tartani. Nélkül megfelelő intézkedéseket a helyen adatok véletlenül szoftveradatokban elvetett, elveszett vagy ellopott adathordozón.


**Feladatkörök:**`Shared`

> [!NOTE]
> Minden titkosítási megoldások a Microsoft Azure által biztosított, a felhasználók könnyen integrálható az Azure Key Vault, ami lehetővé teszi a titkosítási kulcsok kezelésének.

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének megoldás által telepített összes blob-tároló sértetlenségét és bizalmasságát használata Azure Storage szolgáltatás titkosítási (SSE) védett. SSE megvédi az Azure storage-fiókok, 256 bites AES titkosítással belül inaktív adatok. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Az Azure számos különböző titkosítási képességeit, így ügyfelei rugalmasan válassza ki az igényeinek leginkább megfelelő megoldást kínál. Az Azure Key Vault segítségével az ügyfelek könnyedén és költség hatékonyan adatok titkosítása a felhőalapú alkalmazások és szolgáltatások által használt kulcsok irányítást tarthat fenn. Az Azure Disk Encryption lehetővé teszi az ügyfelek virtuális gépek titkosításához. Az Azure Storage szolgáltatás titkosítási lehetővé teszi egy ügyfél tárfiókjával csoportosítson összes adatot titkosítják. |


 ## <a name="ncsc-cloud-security-principle-24"></a>NCSC felhő rendszerbiztonsági tagot 2.4
### <a name="data-sanitisation"></a>Adatok Sanitisation
Kiépítés, áttelepítés és való üzembe helyezési erőforrások esetén nem lehetnek jogosulatlan hozzáférés felhasználói adatokhoz.

Az adatok nem megfelelő tisztítási okozhat:

- Felhasználói adatok adatmegőrzés alatt a szolgáltató által határozatlan ideig
- Felhasználói adatok, az erőforrások újrafelhasznált alatt érhető el más felhasználók számára a szolgáltatás
- Felhasználói adatok elvesztését vagy elvetett, elveszett vagy ellopott adathordozón közzé.


**Feladatkörök:**`Microsoft Azure`


|||
|---|---|
| **Ügyfél** | Microsoft Azure szerződéses biztosítékok vonatkozó adatok végleges törlése az Azure biztosít. Ez az elv, Microsoft Azure öröklődött. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft Azure a NIST SP800-88r1 kivezetési folyamatot követi a FIPS-199 mérsékelt igazítva adatbesorolási. Az NIST biztonságos törlése megközelítés (keresztül merevlemez-meghajtó belső vezérlőprogramját) esetén, amelyek támogatják ezt biztosít. A Microsoft nem takaríthatók merevlemezek megsemmisít őket, és ez a beállítás a helyreállítási információk nem lehetséges (például darabokra, zúzására szolgálnak, pulverize vagy hamvasszuk). A megfelelő módon történő elhelyezésének az eszköz típusa határozza meg. Rögzíti a bontási megmaradnak. Minden Microsoft Azure-szolgáltatások jóváhagyott media tárolási és kivezetési szolgáltatások használatára. <br />  <br /> Lejárati vagy szolgáltatás előfizetés megszüntetése esetén az ügyfél lépjen kapcsolatba a Microsofttal, és kérje meg őket, hogy: <br /><br /> (1) tiltsa le a felhasználói fiókot, és törölje az ügyféladatok; vagy <br /> (2) adatok megőrzése mellett a tárolja az online szolgáltatással vagy az ügyfél előfizetéséhez (a "megőrzési időtartam") megszűnése után legalább 90 nappal egy korlátozott függvény fiókot úgy, hogy az ügyfél lehet, hogy kinyeri az adatokat. A megőrzési időszak lejárta után a Microsoft tiltsa le a felhasználói fiókot, és minden adatot törölnie. Gyorsítótárazott vagy biztonsági másolatok kiürítendő a megőrzési időszak vége számított 30 napon belül. |


 ## <a name="ncsc-cloud-security-principle-25"></a>NCSC felhő rendszerbiztonsági tagot 2,5
### <a name="equipment-disposal"></a>Berendezések kivezetési
Ha a szolgáltatás biztosításához használt elérte hasznos élettartama végén, azt kell távolítható el, amelyek nem feltörésére szolgáltatás, vagy a szolgáltatásban tárolt felhasználói adatok.


**Feladatkörök:**`Microsoft Azure`


|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure datacentres; berendezések kivezetési eljárások megvalósítva, és a Microsoft Azure felügyelni. Ez az elv a Microsoft Azure-ból örökölt. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft Azure ezen elv megvalósítja az ügyfeleik nevében. Esetén a rendszer záró életciklusa működési csoporthoz kövesse szigorú adatkezelési eljárások és hardver értékesítési folyamatok nem tartalmazhat ügyféladatok hardvert biztosítja, amelyek segítségével a Microsoft legyen elérhető a nem megbízható entitások számára. A Microsoft Azure a NIST SP800-88r1 kivezetési folyamatot követi a FIPS-199 mérsékelt igazítva adatbesorolási. Az NIST biztonságos törlése megközelítés (keresztül merevlemez-meghajtó belső vezérlőprogramját) esetén, amelyek támogatják ezt biztosít. A Microsoft nem takaríthatók merevlemezek megsemmisít őket, és ez a beállítás a helyreállítási információk nem lehetséges (például darabokra, zúzására szolgálnak, pulverize vagy hamvasszuk). A megfelelő módon történő elhelyezésének az eszköz típusa határozza meg. Rögzíti a bontási megmaradnak. Minden Microsoft Azure-szolgáltatások jóváhagyott media tárolási és kivezetési szolgáltatások használatára. |


 ## <a name="ncsc-cloud-security-principle-26"></a>NCSC felhő rendszerbiztonsági tagot 2.6
### <a name="physical-resilience-and-availability"></a>Fizikai rugalmasság és a rendelkezésre állás
Szolgáltatások rugalmasságának megvalósítása, amely hatással lesznek képesek megfelelően működik-e hibák, események vagy támadások különböző szintű rendelkeznek. A rendelkezésre állás nélkül szolgáltatás elérhetetlenné válhatnak, potenciálisan hosszan, függetlenül az üzleti hatását.


**Feladatkörök:**`Shared`

> [!NOTE]
> Ha az ügyfél a Microsoft Azure engedélyezése az Azure Site Recovery és az adatok egy másik grafikusan földrajzi helyen datacentre alternatív tárolási megfelelően konfigurálja, a Microsoft Azure ügyfél telepített erőforrások folyamatos működését is támogatja.

|||
|---|---|
| **Ügyfél** | Az ügyfél egy másik tárolási hely felelős. Az ellenőrzés végrehajtása kimutatások eleget kell tennie az ügyfél képes váratlan esemény fog működni. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft Azure UK nemzeti számítógépes biztonsági központ (NCSC) jóváhagyott datacentres földrajzilag különböző helyeken (Egyesült Királyság déli régiója és Egyesült Királyság nyugati régiója) annak rugalmasság és a rendelkezésre állás érdekében, hogy rendelkezik. Az ügyfél felelőssége, hogy egy másik régióban Azure Site Recovery szolgáltatással tartalékkapacitás lesz. Amennyiben az Azure Site Recovery konfigurált, Azure indítása és leállítása a másodlagos hely zökkenőmentes átmenet az ügyfél szolgáltatások. |


 ## <a name="ncsc-cloud-security-principle-3"></a>NCSC felhő rendszerbiztonsági tagot 3
### <a name="separation-between-users"></a>Felhasználók elkülönítése
A szolgáltatás a rosszindulatú vagy veszélyeztetett biztonságú felhasználó nem lehet hatással a szolgáltatás vagy egy másik adatok tudni.

Felhasználó elkülönítése befolyásoló tényezők a következők:

- Ha a elválasztási vezérlők valósíthatók meg-a jelentősen befolyásolja a szolgáltatásmodell (pl. IaaS, PaaS, SaaS) által
- ki a szolgáltatást a megosztani kívánt – Ez határozza meg a telepítési modell (pl. public, private vagy közösségi felhő)
- elérhető elválasztási vezérlők végrehajtásának megbízhatósági szintjét

> [!NOTE]
> Az infrastruktúra-szolgáltatási szolgáltatásban érdemes számítási, tárolási és hálózati összetevők által biztosított elkülönítése. Is SaaS és PaaS szolgáltatások IaaS építeni öröklik előfordulhat, hogy az alkalmazás mögötti infrastruktúra-szolgáltatási infrastruktúra elválasztási tulajdonságok némelyike.

**Feladatkörök:**`Microsoft Azure`


|||
|---|---|
| **Ügyfél** | Microsoft Azure gondoskodik arról, hogy minden felhasználóhoz, nehogy egy rosszindulatú vagy veszélyeztetett biztonságú felhasználói a szolgáltatás vagy az adatok egy másik elkülönítési. Ez az elv, Microsoft Azure öröklődött. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Ügyfél felhőkiszolgálók virtuális folyamatban, mert a fizikai elválasztását paradigma már nem érvényes. A Microsoft Azure azonosításához, és a teljesítményszámláló használatából adódó kockázatok csökkentéséről több-bérlős környezetben úgy lett kialakítva. Adatok tárolási és feldolgozási logikailag elkülönített Azure Active Directory használatával felhasználói között, és kifejezetten fejlesztett több-bérlős szolgáltatások esetén funkciót, amelynek célja, hogy győződjön meg arról, hogy megosztott Azure datacentres tárolt felhasználói adatok egy másik nem érhető el szervezet. <br /> <br /> Alapvető bármely megosztott architektúra minden felhasználóhoz, nehogy egy rosszindulatú vagy veszélyeztetett biztonságú felhasználói a szolgáltatás vagy az adatok egy másik megadott elkülönítési. <br /> <br /> Microsoft kapcsolatos további tudnivalókért bérlői elválasztási lásd: a teljes leírását a [Azure tervezetének - NCSC biztonsági alapelvei - ügyfél feladatkörei mátrix](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-4"></a>NCSC felhő rendszerbiztonsági tagot 4
### <a name="governance-framework"></a>Cégirányítási keretrendszer
A szolgáltató egy biztonsági irányítás keretrendszert, amely koordinálja, és arra utasítja a szolgáltatás és az adatok a felügyeleti kell rendelkeznie. Bármely technikai vezérlők rendszerbe kívül ezt a keretrendszert alapvetően késlekedés lesz.
Egy hatékony irányítás keretrendszer járó biztosíthatja, hogy ezen eljárás csoporthoz, a fizikai és technikai vezérlők továbbra is működni szolgáltatás élettartama során. Azt is válaszolnia kell a szolgáltatás, technológiai fejlesztéseket és új fenyegetéseket megjelenésének változásai.


**Feladatkörök:**`Microsoft Azure`


|||
|---|---|
| **Ügyfél** | A Microsoft Azure tart fenn a dokumentált biztonsági irányítás keretrendszere, amely az Azure-szolgáltatásokhoz. Ez az elv, Microsoft Azure öröklődött. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A megfelelőségi keretrendszer szabványos módszertana megfelelőségi tartományok definiálása, meghatározni, hogy mely célok érvényesek egy adott csapat vagy az eszköz, és hogyan tartomány célkitűzéseit kiiktatása részletességgel alkalmazni kell a rögzítés tartalmaz egy az ipari szabványok, szabályozások vagy üzleti követelmények adott halmazát. A keretrendszer van leképezve vezérlők több szabályozó szabványok, amely lehetővé teszi, hogy a Microsoft tervezése és olyan gyakori vezérlők, ezáltal helyeinél a megfelelőségi szabályzat számos különböző ma használatával szolgáltatásokat és azokat a jövőben fejleszteni. <br /> <br /> Microsoft megfelelőségi folyamatok megkönnyíti az ügyfelek számára több szolgáltatásban való megfelelés eléréséhez, és saját változó hatékonyan igényeihez. Biztonság növelése érdekében technológia és a hatályos megfelelőségi folyamatok együtt, karbantartása és bontsa ki a külső tanúsítványokról széles skáláját Microsoft engedélyezése. Ezekről a tanúsítványokról segít a felhasználóknak, azok az ügyfelek, ellenőrök és szabályozó megfelelőségi készültségi bemutatása. <br /> <br />  Azure megfelel a nemzetközi, valamint a területi és az iparág-specifikus megfelelőségi követelményeket, például ISO 27001 FedRAMP, SOC 1 vagy SOC 2 széles körét. A szigorú biztonsági vezérlőkben ezeknek a szabványoknak való megfelelés ellenőrzése szigorú külső naplózás, amely bemutatja a Azure-szolgáltatások dolgozni, és megfelelnek a világszínvonalú ipari szabványok, tanúsítványok, tanúsítványok és engedélyek által. <br /> <br /> Azure egy megfelelőségi biztosításával kapcsolatos stratégia, amelynek segítségével az ügyfelek cím üzleti célkitűzéseiket, valamint az ipari szabványok és a szabályzat úgy van kialakítva. A biztonsági megfelelőség keretrendszer magában foglalja a vizsgálati és naplózási fázisok, biztonsági elemzés, kockázati kezelésében bevált gyakorlatok és biztonsági teljesítményteszt elemzés tanúsítványokat és a tanúsítványok eléréséhez. <br /> <br /> A Microsoft megfelelést a megfelelőségi kapcsolatos további tudnivalókért keretrendszerek lásd: a teljes leírását a [Azure tervezetének - NCSC biztonsági alapelvei - ügyfél feladatkörei mátrix](https://aka.ms/blueprintuk-gcrm).|


 ## <a name="ncsc-cloud-security-principle-5"></a>NCSC felhő rendszerbiztonsági tagot 5
### <a name="operational-security"></a>A működési biztonság
A szolgáltatásnak kell üzemeltetett és biztonságosan kezelendő károsan befolyásolhatja, észlelése vagy támadások megelőzése érdekében. Jó működési biztonsági elvégzéséhez nem szükséges összetett, bürokratikus, sok időt vesz igénybe vagy költséges folyamat.

Nincsenek négy elemet kell figyelembe venni:

- Konfigurációs és a változáskezelés – biztosítania kell, hogy a rendszer megfelelően tesztelte és megtörtént engedélyezett. Változtatásokat kell váratlanul módosítja biztonsági tulajdonságok
- A biztonsági rés felügyeleti - kell azonosítani és a bennük foglalt összetevők biztonsági problémák elhárítása érdekében
- Védelmi figyelés - mértékeket el kell helyezni, hogy a támadások és a szolgáltatás a jogosulatlan tevékenységek észlelése
- Az Incidenskezelés - győződjön meg arról, incidensek válaszol, és egy biztonságos, elérhető szolgáltatás helyreállítása




 ## <a name="ncsc-cloud-security-principle-51"></a>NCSC felhő rendszerbiztonsági tagot 5.1
### <a name="configuration-and-change-management"></a>Konfigurációs és a változáskezelés
Az eszközöket, amelyek a szolgáltatás, és a konfigurációkat és a függőségek pontos képet kell rendelkeznie.
Módosítások, amelyek befolyásolhatják a szolgáltatás biztonsági azonosított legyen, és kezeli. Jogosulatlan módosításokat kell észlelhető.
Ha módosítás hatékonyan nem kezelt, biztonsági rések akaratlanul vihetők szolgáltatáshoz. És még akkor is, ha nincs meg a biztonsági rést, akkor előfordulhat, hogy nem kell teljesen problémák elhárításáról.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | Az Azure Resource Manager-sablonok és a kapcsolódó erőforrások az Azure tervezetének alkotó képviselő "kódú" alapkonfiguráció telepített architektúrájának. A megoldás azonban használhat konfiguráció-ellenőrzés a github webhelyen valósul meg. <br /> <br /> Az Azure Active Directory-fiók jogosultságokat biztosító szigorú szerepkörök hozzárendelése a felhasználók a szerepköralapú hozzáférés-vezérlés használatával megvalósított szabályozhatja, hogy mely felhasználók keresztül tekintheti meg és vezérlés telepített erőforrásokhoz. Active Directory jogosultságot szerepköralapú hozzáférés-vezérlés segítségével felhasználókat rendelhet a biztonsági csoportokat lehet létrehozni. Ezekből a biztonsági csoportokból szabályozza, a végrehajtandó műveleteket, a felhasználók is igénybe vehet, az operációs rendszer konfigurálása tekintetében. Ezek a szerepkör-alapú rendszerek bővíthető az ügyfél kritikus igényeinek. <br /> <br /> Meg kell felelnie a elve az, hogy további konfigurációs szükséges az ügyfél üzemi használatra. Ezeket a konfigurációkat, az ügyfél változáskezelési folyamatot részének kell lennie kell. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure ellenőrzi, és frissíti a konfigurációs beállításokat és az eredeti konfigurációk hardver-, szoftver- és hálózati eszközök évente. Módosítások fejlesztett, tesztelt, és az éles környezetben érkező fejlesztési vagy tesztelési környezetben előtt jóvá. <br /> <br />A Microsoft Azure érvényes a változás- és kiadás eljárással hardver- és hálózati eszköz összetevők, írja be a Microsoft Azure szoftverösszetevők (pl. operációs rendszer, háló, RDFE, XStore stb.) és a rendszerindítási konfigurációs alapterv konfigurációk A Microsoft Azure éles környezetben, az alábbiakban leírt módon. <br /> <br /> Az Azure-alapú szolgáltatások számára szükséges alapterv konfigurációkat felülvizsgálata az Azure biztonsági és megfelelőségi csoport és a csoportokat a tesztelés előtt az éles szolgáltatás részeként. |


 ## <a name="ncsc-cloud-security-principle-52"></a>NCSC felhő rendszerbiztonsági tagot 5.2.
### <a name="vulnerability-management"></a>A biztonsági rés kezelése
Szolgáltatók azonosítása, osztályozhatja és biztonsági rések helyen rendelkeznie kell egy felügyeleti folyamatot. Szolgáltatások, amelyek nem, gyorsan lesz téve a támadás veszélyének nyilvánosan ismert módszerek és eszközök használatával.


**Feladatkörök:**`Customer`


|||
|---|---|
| **Ügyfél** | Az ügyfél felelős biztonsági rések keresése az ügyfél telepített erőforrások (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között). Az implementáció kimutatások eleget kell tennie a biztonsági rés vizsgálat végrehajtásához használt eszközök. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Biztonsági frissítés management megvédi rendszerek a ismert biztonsági rések. Az Azure által használt integrált telepítési rendszerek kezeléséhez terjesztési és biztonsági frissítések Microsoft-szoftverek telepítését. Azure egyben a a Microsoft Security Response központ (MSRC), amely azonosítja, figyeli, válaszol-e, és oldja fel a biztonsági események erőforrásait igénybe, és a felhő biztonsági rések az óra körül az év egyes napjaira. |


 ## <a name="ncsc-cloud-security-principle-53"></a>NCSC felhő rendszerbiztonsági tagot 5.3.
### <a name="protective-monitoring"></a>Védelmi figyelése
Amely hatékonyan nem figyeli a szolgáltatás támadás, visszaélés és meghibásodása (sikeres és sikertelen) támadások észlelését várhatóan nem lesz. Ennek eredményeképpen akkor nem képes gyorsan reagál a környezetek és az adatok potenciális támadások.


**Feladatkörök:**`Customer`


|||
|---|---|
| **Ügyfél** | Az ügyfél felelős figyelési ügyfél telepített erőforrások (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között). Az ellenőrzés végrehajtása kimutatások eleget kell tennie a figyelheti, észlelésében és kezelésében támadások, visszaélés és meghibásodása mechanizmusokat. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft Azure biztonsági követelmények aktív figyelés definiálva van. Csoportok konfigurálása aktív Hálózatfigyelő eszközök a követelményeknek megfelelően. Aktív felügyeleti eszközök közé tartozik, a figyelési ügynök (MA) és a System Center Operations Manager (SCOM), a Microsoft Azure biztonsági csoporthoz azonnali beavatkozást igénylő esetekben adhat a valós idejű riasztások konfigurált. |


 ## <a name="ncsc-cloud-security-principle-54"></a>NCSC felhő rendszerbiztonsági tagot 5.4.
### <a name="incident-management"></a>Incidenskezelés
Kivéve gondosan tervezett incidenskezelés folyamatok vannak érvényben, gyenge döntések valószínűleg kerül sor, amikor incidensek történik, a felhasználókra gyakorolt teljes hatás potenciálisan súlyosbodott.
Ezek a folyamatok nem szükséges összetett vagy leírás nagy mennyiségű, de jó incidenskezelés fog minimalizálása a hatás biztonságra, megbízhatóságra és egy olyan környezeti problémákat a felhasználók számára.


**Feladatkörök:**`Shared`

> [!NOTE]
> Azokban az esetekben, ahol felhasználói biztonsági incidensek hatással lehet a Microsoft Azure biztonsági állapotát az ügyfél felelős a Microsoft Azure értesítésére.

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős létrehozó egy incidenskezelési folyamat ügyfél telepített erőforrásokat (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között). Az implementáció kimutatások eleget kell tennie jelentési események és riasztások, időben történő incidens válaszok támogatása, és információt továbbítja a PGA és egyéb HMG szervezetekkel. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft javítást alkalmazott egy biztonsági incidenskezelési folyamat megkönnyítésére koordinált választ adhasson az eseményekre történjen, egyet. <br /> <br /> Ha Microsoft tudomást bármely berendezései vagy létesítményekben, vagy ilyen berendezések vagy adatvesztés, nyilvánosságra vagy megváltoztatása ügyféladatok létesítményekben jogosulatlan elérésére tárolt ügyféladatok jogosulatlan elérését, a Microsoft megállapította, hogy a következőket hajtja végre: <br /> <br />   -Azonnal értesíti az ügyfél a biztonsági incidens; <br /> -Azonnal vizsgálja meg a biztonsági incidens, és adja meg az ügyfél részletes információt a biztonsági incidens; és <br /> -Lépéseket ésszerű és gyors által okozott hatások mérsékléséhez és a biztonsági incidens egy károk minimalizálása érdekében.  <br />  <br /> Az incidenskezelés keretrendszer definiált szerepkörök és felelősségek lefoglalt létrejött. A Windows Azure biztonsági incidens (WASIM) felügyeleti csoport biztonsági incidensek kezelése, ideértve a eszkalációs és specialistája csapatok szükség esetén bevonásának biztosítása felelős. Az Azure üzemeltetési vezetők is vizsgálati és az egyéb funkciók-támogatással rendelkező biztonsági és adatvédelmi incidensek megoldási felügyeletéért felelős. <br /> <br /> A Microsoft Incidensmegoldási kapcsolatos további tudnivalókért folyamatok lásd: a teljes leírását a [Azure tervezetének - NCSC biztonsági alapelvei - ügyfél feladatkörei mátrix](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-6"></a>NCSC felhő rendszerbiztonsági tagot 6
### <a name="personnel-security"></a>A személyzet biztonsága
Ahol a service provider személyzet rendelkezik hozzáféréssel az adatok és a rendszer megbízhatóságát az vetett bizalmat magas fokú szüksége van. Támogatott alapos szűrés, megfelelő képzésben csökkenti a véletlen vagy rosszindulatú sérült biztonság esetén a service provider személyzet valószínűségét.
A szolgáltató kell a biztonsági ellenőrzést és rendszeres biztonsági képzés hatálya tanácsadási csoporthoz. Ezek a szerepkörök személynek tisztában kell lennie azzal hogy feladatainak. Szolgáltatók kell egyértelművé teszi hogyan azok képernyőn, és kezelni a személyzet belül kiemelt szerepköröket.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | Az ügyfél felelős személyek szűrési és ügyfél telepített erőforrásokhoz való hozzáférés biztosítható a rendszeres biztonsági képzés egyéni felhasználók számára. Az implementáció kimutatások eleget kell tennie a szűrési feltételeket a szerepkörök és biztonsági képzés gyakoriságát. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft Azure személyzet az Azure-szolgáltatások működtetéséhez és ügyfél-támogatási szolgálatához (vagy a platform műveletek, a hibaelhárítás és a technikai támogatási szolgálathoz segítő Microsoft alvállalkozók) változni Microsoft szabvány háttér (vagy egyenértékű) ellenőrizze, hogy értékelje ki alkalmazott oktatási, alkalmazási és büntetőjogi előzményeit. A átvilágítást körben UK kormánya BPSS/BS7858 követelményeinek összhangban legyenek. Nem kifejezetten tartoznak a formális identitás-ellenőrzés.  <br /> <br /> Microsoft titoktartási rendelkezések alkalmazott és alvállalkozó szerződései tartalmazzák. Az összes megfelelő a Microsoft munkatársait és alvállalkozóink részt vesz egy támogatott Microsoft Azure biztonsági-képzési programot, amely arról értesíti az információ-biztonság feladataik személyzete. <br /> <br /> Microsoft Azure-szolgáltatások személyzeti vagy a biztonság megsértése véglegesítése és/vagy a szabályt sértő információk biztonsági házirend gyanús alvállalkozóink egy vizsgálat során, és legfeljebb megfelelő fegyelmi és többek között a következőket lezárást vonatkoznak. Ha a körülmények indokolják, Microsoft előfordulhat, hogy az ügyet eljárásra jog érvényesítési révén. <br /> <br /> A rendszer átvilágítást és biztonsági oktatási kiegészítve, Microsoft megelőző defenzív és reaktív vezérlők jogosulatlan fejlesztői és/vagy a felügyeleti tevékenységek, beleértve a következő elleni védelem érdekében kombinációi telepíti mechanizmusok: <br />  <br /> -Szoros hozzáférés-vezérlést a bizalmas adatokat, beleértve a követelményeket a kéttényezős intelligenskártya-alapú hitelesítés bizalmas műveletek végrehajtásához. <br /> -Vezérlők, amelyek javítják a kártevő szándékú tevékenységek felismerése független kombinációi. <br /> -Figyelési, naplózási és jelentéskészítési több szinten. |


 ## <a name="ncsc-cloud-security-principle-7"></a>NCSC felhő rendszerbiztonsági tagot 7
### <a name="secure-development"></a>Biztonságos fejlesztési
Szolgáltatások kell kell megtervezni és fejlesztett azonosításához, és azok biztonsági veszélyek mérséklése. Lehet, hogy azok, amelyek nem ki van téve a biztonsági problémákat sikerült hibát okoz az adatok, szolgáltatáskimaradást okozó vagy más rosszindulatú tevékenységhez engedélyezése.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | A virtuális gépeket az Azure tervezetének által telepített Windows operációs rendszer futtatását. Windows biztosít a valós idejű fájl sértetlenségének ellenőrzése, a védelem és a Windows vagy a Windows Resource védelem (WRP) funkció, amely lehetővé teszi a valós idejű engedélyezett Windows rendszer frissítések részeként telepített core rendszer fájlok helyreállítása integritás-ellenőrzése. <br /> <br /> Windows rendelkezik védelem megakadályozza a kód végrehajtása korlátozott memória helyeken: nem hajtható végre (NX), a cím terület elrendezés Véletlenszerűsítésének (ASLR) és az Adatvégrehajtás megakadályozása (DEP). <br /> <br /> Az Azure tervezetének telepíti az összes telepített Windows virtuális gép a Microsoft Windows Defender készletével megvalósított állomásalapú kártevőirtó védelmet. A Windows Defender automatikusan frissíti a kiadásban elérhető legyen, mindkét a kártevőirtó motor és a védelmi aláírások van konfigurálva. <br /> <br /> Meg kell felelnie a elve az, hogy további konfigurációs szükséges az ügyfél üzemi használatra. Ezeket a konfigurációkat, az ügyfél biztonságos fejlesztési folyamat részének kell lennie kell. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft biztonsági fejlesztési életciklus (SDL) biztosít egy hatékony fenyegetések modellezése folyamat fenyegetések és a szoftverek és -szolgáltatások biztonsági réseit azonosításához. Fenyegetések modellezése team feladatát, beleértve az operations manager, a program/több, a fejlesztők és a tesztelők webhelyről, és egy kulcs biztonsági elemzés feladatok végrehajtására a meghatározásához jelöli. Csoport tagjai eszközzel a SDL fenyegetések modellezése a modell összes szolgáltatások és projektek, amikor azok beépített, mind az új szolgáltatásokat és funkciókat frissítésekor. Fenyegetés-modellek a támadási felületet és az összes kód által írt, vagy egy harmadik féltől származó licencelt elérhetővé tett összes kódot lefedik, és fontolja meg az összes bizalmi kapcsolat határain. A STRIDE rendszer (Spoofing, Tampering, Repudiation, információfelfedés, szolgáltatásmegtagadás és jogok kiterjesztése) azonosításához, és hatással lehet az ügyfelek előtt hárítsa el a biztonsági fenyegetések a tervezési folyamat korai szakaszaiban szolgál. |


 ## <a name="ncsc-cloud-security-principle-8"></a>NCSC felhő rendszerbiztonsági tagot 8
### <a name="supply-chain-security"></a>Ellátási lánc biztonsági
A szolgáltató győződjön meg arról, hogy az ellátási lánc kielégítően összes támogatja a biztonsági elveket, amelyeket a szolgáltatás jogcímeket kell megvalósítani.
Felhőszolgáltatások gyakran számítson arra, hogy külső gyártótól származó termékek és szolgáltatások. Következésképpen a elv nincs megvalósítva, ha ellátási lánc sérült biztonság esetén is ellentétes a szolgáltatás biztonsági és hatással más biztonsági elveket végrehajtására.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | A felhasználói dokumentáció biztonságos ellátási lánc a harmadik fél szerezte be, szoftverek és operációs rendszerek az Azure-előfizetés használatban felelős. Az implementáció kimutatások eleget kell tennie a ellátási lánc dokumentáció által azonosított eljárások követésével kivétel. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft Cloud ellátási lánc (MCSC) csoport áll hat egyedi csoportok minden hozzájáruló Azure ellátási lánc fenyegetésekkel szembeni hatékony védelmét.  <br />  <br /> -Beszerzési <br /> -Ügyfél műveletek <br /> -A központi telepítés minősége <br /> -Szolgáltató kezelése <br /> -Szemben <br />  <br /> A Microsoft MCSC csoport kapcsolatos további tudnivalókért lásd: a teljes leírását a [Azure tervezetének - NCSC biztonsági alapelvei - ügyfél feladatkörei mátrix](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-9"></a>NCSC felhő rendszerbiztonsági tagot 9
### <a name="secure-user-management"></a>Biztonságos felhasználók kezelése
A szolgáltató kell elérhetővé az eszközök biztonságosan kezelheti a szolgáltatás. Felügyeleti felületek és eljárások a biztonsági sorompó, megakadályozza a jogosulatlan hozzáférés és az erőforrások, alkalmazások és adatok átalakítására fontos részét képezik.

A szempontot figyelembe kell venni a következők:

- Felhasználói felületek és támogatási csatornáit hitelesítés
- Felügyeleti felületek belül elkülönítését és a hozzáférés-vezérlés



 ## <a name="ncsc-cloud-security-principle-91"></a>NCSC felhő rendszerbiztonsági tagot 9.1
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>Hitelesítés felhasználói felületek és támogatási csatornáit belül
Ahhoz, hogy a biztonságos szolgáltatás, a felhasználók számára szükséges felügyeleti műveletek végrehajtását, mielőtt hitelesítés karbantartása jelentés hibáit, vagy a kérelem módosításait a szolgáltatásba.
Ezek a tevékenységek is elvégezhető, a szolgáltatás felügyeleti webes portálon keresztül, vagy egyéb csatornákon, például a telefonos vagy e-mailt. Valószínűleg új szolgáltatáselemek kiépítés, a felhasználói fiókok kezelése és a felhasználói adatok kezelése feladatokat tartalmazza.
Szolgáltatók kell, hogy egy biztonsági hatással összes kezelési kérelem biztonságos és hitelesített csatornákon keresztül el kell végezni. Felhasználókat a rendszer nem erősen hitelesíti majd egy felhasználók megszerezhetnének képesek lehetnek sikeres végrehajtásához az privilegizált műveleteket, aláásva ezáltal a szolgáltatás vagy az adatok biztonságát.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | Amikor a rendszergazdák hozzáférnek a szervezet által használt Azure-erőforrások kezeléséhez a Microsoft Azure portálon, a portál és a felügyeleti eszközök között továbbított adatok továbbítása 2048 bites RSA használatával titkosított Transport Layer Security (TLS) csatornát / SHA-256 titkosítási kulcsokat, CESG/NCSC által javasolt módon.  <br /> <br /> Az Azure tervezetének Windows-hitelesítés, a távoli asztal és a BitLocker funkcióit használja. Ezek az összetevők beállítható úgy, hogy a titkosítási modulok FIPS 140 érvényesítése támaszkodnak. <br /> <br /> Az Azure tervezetének kikényszeríti a logikai hozzáférési engedélyek kényszeríti ki az Azure Active Directory felhasználók hozzárendelése szerepkörökhöz, Active Directory-felhasználók biztonsági csoportokra való hozzárendelésével szerepköralapú hozzáférés-vezérlés használatával, és a Windows operációs rendszer szintű szabályozza. Az Azure Active Directory-szerepkörök hozzárendelve a felhasználókhoz, vagy csoportok az Azure erőforrás-, csoport vagy előfizetés erőforrásokhoz való logikai hozzáférés szabályozása. Active Directory biztonsági csoportokat az operációs rendszer szintű erőforrások és a funkciók való logikai hozzáférést. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Az ügyfelek felügyelete az Azure-erőforrások az Azure-portálon, amely az összes virtuális gépek, adatbázisok, felhőszolgáltatások hozzáférést tesz lehetővé, és más erőforrások, a felhasználói fiókjához beállított. Webes elérés az Azure portálon szabványos Transport Layer Security (TLS) 1.2-es kapcsolatok 2048 bites RSA/SHA-256 titkosítási kulcsok használata ajánlott által CESG/NCSC által védett. Szerepköralapú hozzáférés-vezérlést ahhoz, hogy az ügyfelek Azure kezelési források korlátozott hozzáférést biztosít bizonyos felhasználók és csoportok találhatók. |


 ## <a name="ncsc-cloud-security-principle-92"></a>NCSC felhő rendszerbiztonsági tagot 9.2
### <a name="separation-and-access-control-within-management-interfaces"></a>Elkülönítés és a hozzáférés-vezérlés belül felügyeleti felületek
Sok felhőszolgáltatások webes alkalmazásokat és API-k kezelése. Ezek a kapcsolatok a szolgáltatás biztonsági kulcs részét képezik. Ha a felhasználók nem megfelelően elkülönül egymástól felületek belül, egy felhasználó esetleg hatással a szolgáltatás, vagy módosíthatja az adatokat egy másik.
A kiemelt rendszergazdai fiókok valószínűleg nagy mennyiségű adatot a hozzáférést. Egyéni felhasználók számára feltétlenül szükséges engedélyei korlátozási segíthet a rosszindulatú felhasználók, hitelesítő adatokkal való visszaélés vagy sérült biztonságú eszközök által okozott kárt korlátozza.
Szerepköralapú hozzáférés-vezérlés ennek elérése mechanizmust biztosít, és valószínűleg különösen fontos lehetővé teszi a felhasználók nagyobb telepítések kezelése.
Felügyeleti felületek alacsonyabb hálózatokhoz (például közösségi helyett a nyilvános hálózatok) kitettségének megnehezíti a támadók éri el, és a támadás, mivel ezen hálózatok eléréséhez először kell. A hálózatok különböző típusaihoz adapterek kitettségének kockázata értékelő az útmutatások elv 11.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | Az Azure tervezetének Alkalmazásátjáró telepíti, terheléselosztóhoz, és konfigurálja a hálózati biztonsági csoportszabályok vezérlésére commutations határok külső és belső alhálózatok között. Felhasználói funkció rendszer felügyeleti funkció a logikai hozzáférés-vezérlést és a rendszer-architektúra érvényesítése keresztül elkülönül. Rendszer-felügyeleti funkciók felületek nem azonosak a felhasználói felületek elembe. Minden felügyeleti kapcsolat biztonságos megerősített (jumpbox) található gazdagépet a hálózati biztonsági csoportszabályok felügyeleti alhálózat megfelelő éles erőforrásokhoz való hozzáférés korlátozása keresztül történik. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A felhasználó elkülönítése elkülönítése beépített Azure maga. Az Azure Active Directory (Azure AD vagy aad-ben) segítségével minden felhasználó hitelesíti magát az Azure-portálon csak áttekinthetők és felügyelhetők a jogosultak erőforrásokhoz való hozzáférést biztosítanak. Ennek eredményeképpen különböző felhasználói fiókok szigorúan egymástól a közös Azure-portálon kezelt elkülönített. |


 ## <a name="ncsc-cloud-security-principle-10"></a>NCSC felhő rendszerbiztonsági tagot 10
### <a name="identity-and-authentication"></a>Identitás- és hitelesítési
Hitelesített, és a felhatalmazott személyek hozzáférésének service interfaces kell korlátozható.
Ezek csatolókat gyenge hitelesítési lehetővé teheti a rendszerek, ami azt eredményezi, ellopása vagy módosítása az adatok módosításait a szolgáltatás, vagy egy szolgáltatásmegtagadási jogosulatlan elérésére.
Hitelesítési legfontosabb, a biztonságos csatornákat keresztül kell történik. E-mailek, a HTTP vagy a telefon ki téve a hozzáférés és manipulatív támadásoknak.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | Az Azure tervezetének fiókok kezelése az Active Directory alkalmaz. Az Azure tervezetének által telepített erőforrásokhoz való hozzáférést a beépített Kerberos-funkciók Azure Active Directory, az Active Directory és a Windows operációs rendszer által védett a ismétléses támadások. A Kerberos-hitelesítés, az ügyfél által küldött a hitelesítő további adatokat tartalmaz, például egy titkosított IP-listájában, a ügyfél időbélyegeket és a jegy élettartamát. Ha a rendszer játssza vissza a csomagot, a Timestamp típusú be van jelölve. Ha a Timestamp típusú korábbi, mint vagy ugyanaz, mint a korábbi hitelesítő, a csomag elutasítva. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Azure nyomon követéséhez identitását, valamint integrálhatja identitástárolók, amelyek esetleg már használja a szolgáltatásokat biztosít. Az Azure AD egy olyan átfogó identitás- és hozzáférés felügyeleti szolgáltatás a felhőben, amelyek segítségével biztonságos hozzáférés a helyszíni adatokat és a felhőalapú alkalmazásokhoz. Az Azure AD is egyszerűbbé teszi a felhasználók és csoportok kombinálásával core címtárszolgáltatások, speciális identitás irányítás, biztonsági és alkalmazáshozzáférés-kezeléshez. |


 ## <a name="ncsc-cloud-security-principle-11"></a>NCSC felhő rendszerbiztonsági tagot 11
### <a name="external-interface-protection"></a>Külső adapterén védelme
A szolgáltatás minden külső vagy kevésbé megbízható kapcsolat legyen azonosítani és megfelelően védeni.
Ha az illesztőfelületek metódusainak némelyike (például felügyeleti felületek) saját az illetéktelen behatolásoknak a hatás jelentősebb lehet.
Különböző modell segítségével teszi közzé a kockázat különböző szintű vállalati rendszerekhez felhő-szolgáltatásokhoz.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | Az Azure tervezetének telepíti egy külön webkiszolgáló alhálózati, adatbázis alhálózati, Active Directory-alhálózathoz és felügyeleti alhálózati architektúrát erőforrásokat. Alhálózatok logikailag el egymástól az egyes alhálózatok korlátozzák a forgalmat, amely csak szükséges rendszer- és felügyeleti funkciókat az alhálózatok közötti alkalmazott hálózati biztonsági csoportszabályok (pl., külső forgalom nem érik el az adatbázist, kezelési, vagy az Active Directory alhálózatok).  <br /> <br /> Alkalmazásátjáró ügyfél telepített webalkalmazás külső kapcsolatok kezelésére van telepítve. A kezelési hozzáférés külső kapcsolatok telepített felügyeleti alhálózatot a hálózati biztonsági szabály engedélyezett IP-címek külső kapcsolatok jumpbox (megerősített állomás) korlátozódnak. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft védett metódus meghívja "Piros adapterek összevonása" az Azure biztonsági vezérlők és a folyamatok tesztelése rendszeres behatolást vagy a biztonság növelése érdekében. A vörös csapatának a teljes munkaidejű alkalmazottak belül, amely a megcélzott Microsoft csoportja és állandó támadások elleni Microsoft infrastruktúrát, platformokat, és alkalmazások, de nem end-ügyfelek alkalmazások és adatok. <br /> <br /> A Microsoft piros adapterek összevonásával kapcsolatos további tudnivalókért és a kék összevonás leírása erőfeszítéseket lásd: a teljes leírását a [Azure tervezetének - NCSC biztonsági alapelvei - ügyfél feladatkörei mátrix](https://aka.ms/blueprintuk-gcrm).  |


 ## <a name="ncsc-cloud-security-principle-12"></a>NCSC felhő rendszerbiztonsági tagot 12
### <a name="secure-service-administration"></a>Biztonságos felügyeletét
Egy felhőszolgáltatás felügyeleti rendszerek fog rendelkeznek magas szintű jogosultságokkal való hozzáférés. A biztonsági sérülés kellene jelentős hatása, beleértve az eszközöket, biztonsági vezérlők megkerülése és ellopni vagy nagy mennyiségű adatot kezelheti.
Tervezésére, megvalósítása és kezelése a felügyeleti rendszerek vállalati jó gyakorlat, kövesse a nagy értékű támadók elismerése.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | Az ügyfél felügyeletére, az Azure-előfizetés és az ügyfél telepített erőforrások (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között) biztonságos munkaállomás biztosításáért felelős. Az implementáció kimutatások eleget kell tennie a mechanizmusok, használja az ügyfél telepített erőforrások kiaknázása kockázatok csökkentése érdekében. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft Azure operations csoporthoz kell használnia a biztonságos felügyeleti munkaállomás (fűrészek; szintű hozzáféréssel rendelkező munkaállomások vagy láb is ismert). A FŰRÉSZ megoldás, az neves ajánlott külön rendszergazdai és felhasználói fiókok használata a felügyeleti csoporthoz való kiterjesztését. Ez az eljárás egy külön-külön hozzárendelt rendszergazdai fiókot, amely nem teljesen a felhasználó általános jogú felhasználói fiók használ. LÁTOTT buildek az adott fiók elválasztási gyakorlat, adja meg a megbízható munkaállomás a kényes fiókok. |


 ## <a name="ncsc-cloud-security-principle-13"></a>NCSC felhő rendszerbiztonsági tagot 13
### <a name="audit-information-for-users"></a>A felhasználók számára a naplóinformációkat
Amennyiben az a naplózási való figyelése a szolgáltatás és az adatok eléréséhez szükséges benne tárolt kell lennie. Milyen típusú naplózási információk elérhető egy közvetlen hatással lesz a észleli és nem megfelelő vagy rosszindulatú tevékenységek ésszerű határidőket belül válaszol.


**Feladatkörök:**`Shared`


|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által naplózott eseményeket közé tartoznak az Azure tevékenységi naplóit telepített erőforrások, az operációs rendszer szintű naplókat és az Active Directory-naplók ellenőrzi. Ezek eseménynaplók közé tartoznak az események bekövetkezésekor meghatározásához elegendő információt, az esemény forrását, az esemény, és más részletes információkat, amely támogatja a biztonsági események vizsgálat eredményeit. Az ügyfelek kritikus igényeihez naplózandó további események is kiválaszthat. Az összes Azure-erőforrások rendelkezik az Azure portálon elérhető naplók. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Az Azure Naplóelemzés rögzíti a szervezetek rendszerek és hálózatok belül előforduló, amint az előfordulásuk időpontjában naplózhatja őket, mielőtt bárki képes illetéktelenül módosítani ezeket az eseményeket gyűjt, és lehetővé teszi, hogy az elemzés különböző típusú adatok adatok több számítógép között. Azure lehetővé teszi az ügyfelek hajtható végre biztonsági az eseménygenerálás és a gyűjtés a Azure IaaS és PaaS szerepkörök az előfizetések központi tárolóban. Ezek az összegyűjtött események exportálhatja a helyi biztonsági információkat és az esemény (SIEM) rendszerek figyelése. Miután az adatátvitel tárhelyre, többféleképpen diagnosztikai adatok megtekintéséhez. <br /> <br /> Az Azure beépített diagnosztika segíthet a hibakeresés. Az Azure-ban központilag telepített alkalmazásokat az operációs rendszer biztonsági események sorozatát alapértelmezés szerint engedélyezve vannak. Ügyfelek hozzáadása, eltávolítása vagy módosítása az operációs rendszer naplózási házirend testreszabásával naplózandó események. <br /> <br /> Magas szinten meglehetősen egyszerű és egyszerű naplók gyűjtésére Windows esemény továbbítási (WEF) vagy a további speciális Azure Diagnostics Ha Windows-alapú virtuális gépek vannak telepítve az Azure IaaS használatával. Emellett Azure Diagnostics beállítható úgy, hogy a naplók és események gyűjtése PaaS szerepkörpéldányok. IaaS-alapú virtuális gépek használata esetén az ügyfél egyszerűen konfigurálja, és lehetővé teszi, hogy a kívánt biztonsági események lehetővé teszik az azok helyszíni datacentre naplózás bejelentkezni Windows-kiszolgálók ugyanolyan módon. Webes alkalmazásokhoz akkor is lehet engedélyezni az IIS naplózás, ha az elsődleges alkalmazás és a központi telepítés az Azure-ban. Az ügyfelek mindig biztonsági adatot tárolhat tárfiók támogatott földrajzi helyeken az általuk választott adatok közös joghatóság alá követelményeinek megfelelően. |


 ## <a name="ncsc-cloud-security-principle-14"></a>NCSC felhő rendszerbiztonsági tagot 14
### <a name="secure-use-of-the-service"></a>A szolgáltatás biztonságos használata
A felhőszolgáltatások és a rajtuk tárolt adatok biztonsági is csökkent, ha a szolgáltatás nem megfelelően használja. Egyes feladatkörök következésképpen a szolgáltatás a ahhoz, hogy az adatok megfelelő védelemben használatakor fog rendelkezni.
Az üzembe helyezési modellel, a felhőalapú szolgáltatás, és a forgatókönyvet, ahol a szolgáltatás használni kívánt a felelősség mértékben függ. Az egyes szolgáltatások funkciók is szem előtt. Például hogyan egy tartalomkézbesítési hálózat védje a titkos kulcsot, vagy hogyan fizetési felhőszolgáltatóként észleli a csalárd tranzakciókat fontos biztonsági szempontok a fedi le a biztonsági alapelvei általános szempontok felül.  
Az IaaS és PaaS ajánlatokat Ön felelősséggel tartozik az adatok és a munkaterhelések biztonsági jelentős aspektusait. Például ha akkor be kell szereznie egy infrastruktúra-szolgáltatási számítási példány, általában lehet modern operációs rendszer telepítése, biztonságosan beállítása az adott operációs rendszer, biztonságosan telepítése a futó alkalmazások és annak a példánynak alkalmazása keresztül is karbantartása felelős javítások vagy a karbantartási feladat végrehajtása.


**Feladatkörök:**`Customer`

> [!NOTE]
> Az ügyfél az Azure Security Center segítségével megakadályozásához észleli, és a láthatóság növelésével és szabályozhatják, az Azure-erőforrások biztonsági fenyegetések válaszolni. Az Azure biztonsági központ biztosít integrált biztonsági monitorozást és Azure-előfizetések, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és számos biztonsági megoldással együttműködik.

|||
|---|---|
| **Ügyfél** | Az Azure Resource Manager-sablonok és a kapcsolódó erőforrások az Azure tervezetének alkotó egy jellegű védelmi módszert követve biztonsági. Meg kell felelnie a elve az, hogy további konfigurációs termelés (például adatbázis-felügyeleti szoftver, a webes alkalmazás telepítése) az ügyfél használata szükséges. |
| **Szolgáltató&nbsp;(Microsoft&nbsp;Azure)** | Nem alkalmazható |
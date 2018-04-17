---
title: Az Azure támogatási feldolgozási tervezetének - vizsgálati követelmények
description: PCI DSS követelmény 11
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: db9f1022ecb3b727f08bb6f232a8df55476e0755
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>PCI DSS-kompatibilis környezetben tesztelési követelményei 
## <a name="pci-dss-requirement-11"></a>PCI DSS követelmény 11

**Rendszeresen tesztelniük a biztonsági rendszerek és folyamatok**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Folyamatosan biztonsági rések kell felderíteni rosszindulatú támadók és kutatók és alatt bevezetett új szoftver. Rendszer összetevőit, folyamatok és egyéni szoftverfrissítési kell vizsgálni gyakran biztosításához biztonsági vezérlők továbbra is egy változó környezetnek megfelelően.

## <a name="pci-dss-requirement-111"></a>PCI DSS követelmény 11.1

**11.1** megvalósítása folyamatok próba vezeték nélküli hozzáférési pontok (802.11), és észleli, és azonosítsa az összes engedélyezett, és a vezeték nélküli hozzáférési pontok negyedévente nem engedélyezett.

> [!NOTE]
> A folyamat esetleg használt módszerek közé tartozik azonban nem korlátozott a vezeték nélküli hálózati vizsgálatokat, fizikai vagy logikai ellenőrzések rendszerösszetevők és az infrastruktúra, a hálózati hozzáférés-vezérlés (NAC) vagy a vezeték nélküli Azonosítók/IP-CÍMEK.
Bármelyik módszert használnak, azok elegendőnek kell lennie ismeri fel és azonosítja az engedélyezett és a nem hitelesített eszközöket.


**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure nem engedélyezi, és vezeték nélküli kapcsolatok engedélyezése az Azure hálózati környezetben. Belső biztonsági csoportok rendszeresen ellenőrzi a vezeték nélküli jelek engedélyezetlen negyedévente és engedélyezetlen jelek a vizsgált és eltávolítása. Ügyfelek központi telepítése az Azure környezetben vezeték nélküli technológia nem engedélyezettek. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Vezeték nélküli és SNMP nem valósult meg a megoldást.|



### <a name="pci-dss-requirement-1111"></a>PCI DSS követelmény 11.1.1

**11.1.1** készletnyilvántartás vezetése a hitelesített vezeték nélküli hozzáférési pontok, beleértve a dokumentált üzleti indoklásának.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 11.1](#pci-dss-requirement-11-1). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Vezeték nélküli és SNMP nem valósult meg a megoldást.|



### <a name="pci-dss-requirement-1112"></a>PCI DSS követelmény 11.1.2 csoportok foglalják magukban

**11.1.2 csoportok foglalják magukban** azokat az eljárásokat, incidensekre adott reakciók, abban az esetben, ha nem hitelesített vezeték nélküli hozzáférési pontok észlel.


**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 11.1](#pci-dss-requirement-11-1). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Vezeték nélküli és SNMP nem valósult meg a megoldást.|



## <a name="pci-dss-requirement-112"></a>PCI DSS követelmény 11.2

**11.2** futtatása belső és külső hálózati biztonsági rések vizsgálatokat végez, legalább negyedévente, és minden jelentős változás (például a rendszer új összetevő telepítését, hálózati topológia, a tűzfal szabály módosítások, a termék változásai hálózati után frissítések). 

> [!NOTE]
> Több vizsgálat jelentések esetében jelenítse meg, hogy az összes rendszer beolvasásakor, és az összes alkalmazható biztonsági rések javított negyedéves vizsgálati folyamatával egyesíthetők. További dokumentáció lehet kell ellenőrzése nem szervizelt biztonsági rések jelenleg folyamatban vannak.
> A kezdeti PCI DSS megfelelőségét, már nem szükséges, hogy az vizsgálatok áthaladó négy negyedévet kell végezni, ha a végző ellenőrzi 1) a legutóbbi vizsgálat volt a sikeres vizsgálat, 2) az entitás van dokumentálva, házirendek és eljárások igénylő negyedéves vizsgálatát, és 3) a vizsgálati eredmények feljegyzett biztonsági rések látható módon egy re-scan(s) javítását. Az ezt követő év után tekintse át a kezdeti PCI DSS, az vizsgálatok áthaladó négy negyedévet kell történt.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure negyedéves belső és külső biztonsági ellenőrzéseket hajt végre. Vizsgálatok minősített személyzet hajtja végre. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló lett toll tesztelni, és a biztonsági rés beolvasott "," annak érdekében. A toll teszteredmények megkettőzhetők, például nmap vagy pentest-tools.com közös eszközök segítségével. A toll vizsgálati eredményeket nyújt vezetnek eredményre támadási felületét, nincs kihasználható elemekhez. Emellett [az Azure Security Center](https://azure.microsoft.com/services/security-center/) és [Azure Advisor](/azure/advisor/advisor-security-recommendations) adja meg a szervizelés és a biztonsági információk.|



### <a name="pci-dss-requirement-1121"></a>PCI DSS követelmény 11.2.1 programcsomag

**11.2.1 programcsomag** negyedéves belső biztonsági rés összetevőjére. Biztonsági réseit, és végezze el a megvizsgálja az összes "magas kockázatú" biztonsági rés, megtörténik az entitás biztonsági rés (/ követelmény 6.1) prioritás szerinti ellenőrzése. Vizsgálatok kell elvégezni a személyzet minősített.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure ellenőrzéseket a hatókör alapul szolgáló infrastruktúra biztonsági réseket hajt végre. A Microsoft Azure valósítja meg a biztonsági rések keresése a kiszolgálói operációs rendszerek, adatbázisok és a megfelelő biztonsági rés beolvasó eszközök rendelkező hálózati eszközöket. Az Azure-webalkalmazások megoldások keresése megfelelő iparági ellenőrizve. A biztonsági rés vizsgálatok negyedévente történik.<br /><br />Megvizsgálja minden rendszer szemben az összes "magas kockázatú" biztonsági rés (követelmény 6.1 meghatározott) megoldásáig igény szerint történik. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló lett toll tesztelni, és a biztonsági rés beolvasott "," annak érdekében. A toll teszteredmények megkettőzhetők, például nmap vagy pentest-tools.com közös eszközök segítségével. A toll vizsgálati eredményeket nyújt vezetnek eredményre támadási felületét, nincs kihasználható elemekhez. Emellett [az Azure Security Center](https://azure.microsoft.com/services/security-center/) és [Azure Advisor](/azure/advisor/advisor-security-recommendations) adja meg a szervizelés és a biztonsági információk.|



### <a name="pci-dss-requirement-1122"></a>PCI DSS követelmény 11.2.2

**11.2.2** végezheti el a negyedéves külső biztonsági rés vizsgálatokat, keresztül egy jóváhagyott vizsgálatát szállító (ASV) a fizetési kártya iparági biztonsági szabványok Tanács (PCI SSC). Hajtsa végre, megvizsgálja, amíg nem érhetők el a sikeres vizsgálat igény szerint. 

> [!NOTE]
> Negyedéves külső biztonsági vizsgálatok által egy jóváhagyott vizsgálatát szállító (ASV), a fizetési kártya iparági biztonsági szabványok Tanács (PCI SSC) kell elvégezni.
> Olvassa el az ASV Program útmutatóját közzé a PCI SSC webhelyen vizsgálat ügyfél feladatkörei, vizsgálat előkészítése, stb.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure külső ellenőrzéseket a hatókör alapul szolgáló infrastruktúra, amely a kívülről elérhető legyen a biztonsági réseket hajt végre. Vizsgálatok által egy jóváhagyott vizsgálata szállító (ASV) történik.<br /><br />A Microsoft Azure MSRC/OSSC havi javítás értesítések előfizet, és biztonsági rések legalább negyedéves keres. Azonosított biztonsági rések értékelése, és egy meghatározott ütemterv alapján a kockázati szinteket illetően szervizelt.<br /><br />Minden egyes megcélzott negyedév átfogó biztonsági rést keres a rangsorolt a Microsoft Azure-környezet összetevői végez biztonsági rések azonosítása. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ha központi telepítése a Contoso webes tároló, a bemutató ügyfelei felelősek az negyedéves külső biztonsági rés végrehajtása megvizsgálja, és megvizsgálja elleni PaaS példányainak a kártya tulajdonosát adatok környezetükben (CDE), egy jóváhagyott vizsgálatát szállító (ASV) használatával szükség szerint a fizetési kártya iparági biztonsági szabványok Tanács jóvá.<br /><br />|



### <a name="pci-dss-requirement-1123"></a>PCI DSS követelmény 11.2.3 programcsomag

**11.2.3 programcsomag** hajtsa végre a belső és külső vizsgálatokat, és szükség esetén minden lényeges módosítását követően megvizsgálja. Vizsgálatok kell elvégezni a személyzet minősített.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Eredmények jelentett érdekelt felek és javítási követi nyomon az Azure biztonsági csapatának megszüntetésre keresztül. Az Azure vizsgálati eredmények alapján titoktartási ügyfelekkel lehet osztani. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelősek negyedéves belső és külső biztonsági rés vizsgálatokat végez, és megvizsgálja, szemben a CDE összes PaaS-példány igény szerint. Vizsgálat után a hatókör környezetben jelentős módosításokat kell végezni.<br /><br />Vizsgálatok a szervezeti függetlenség az ASV vagy a személyzet kell elvégezni.|



## <a name="pci-dss-requirement-113"></a>PCI DSS követelmény 11.3

**11.3** valósítja meg a behatolást vagy a biztonság tesztelésének módszere, amely a következőket tartalmazza:
- Megközelítések (például NIST SP800-115) tesztelés iparágban elfogadott behatolás alapján
- A teljes CDE szegélyhálózat és a kritikus rendszerek érvényességének tartalmazza
- Tesztelés és a hálózaton kívülről érkező tartalmazza
- Tartalmazza a tesztelés bármely Szegmentálás és hatókör-csökkentési vezérlők ellenőrzése
- Alkalmazásszintű behatolás tesztek felvenni, minimális követelmény 6.5 felsorolt biztonsági rések meghatározása
- Határozza meg a hálózati réteg behatolást vagy a biztonság tesztek tartalmazza a hálózati funkciók, valamint az operációs rendszereket támogató összetevők
- Tekintse át és a fenyegetések és az utolsó 12 hónapon belül észlelt biztonsági rések figyelembevételével tartalmazza
- Adja meg a vizsgálati eredmények és a javítási tevékenységek eredményeit behatolás megőrzése

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure-szolgáltatások segítségével a külső gyártótól származó behatolás érvényesíti a OWASP (webes alkalmazás biztonsági projekt megnyitása) alapján tesztelés első tíz CREST hitelesített tesztelők webhelyről használatával. A kockázat nyilvántartásba, amely van naplózva, és tekintse át biztonsági eljárásokat a megfelelőség biztosítása rendszeresen keresztül vizsgálata követi. <br /><br />Microsoft is piros összevonást használ a Microsoft által kezelt infrastruktúra, szolgáltatások és alkalmazások ellen. Nincs záró ügyféladatok szándékosan megcélzott piros Összevonás és élő webhelyet behatolást vagy a biztonság a tesztelés során. A tesztek alapján a Microsoft Azure-infrastruktúra és a platformok, valamint a Microsoft által alkalmazások és adatok vannak. Ügyfél bérlők, alkalmazások és az Azure-ban tárolt adatok soha nem célozzák meg.<br /><br />A Microsoft Azure alkalmazott rendszer assessment tervének kialakításához, és végezze el a vezérlők értékelése független értékelésében. Vezérlők értékelések évente történik, és az eredmények feleket jelentik. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló lett toll tesztelni, és a biztonsági rés beolvasott "," annak érdekében. A toll teszteredmények megkettőzhetők, például nmap vagy pentest-tools.com közös eszközök segítségével. A toll vizsgálati eredményeket nyújt vezetnek eredményre támadási felületét, nincs kihasználható elemekhez. Emellett [az Azure Security Center](https://azure.microsoft.com/services/security-center/) és [Azure Advisor](/azure/advisor/advisor-security-recommendations) adja meg a szervizelés és a biztonsági információk.|



### <a name="pci-dss-requirement-1131"></a>PCI DSS követelmény 11.3.1

**11.3.1** végrehajtása *külső* behatolást vagy a biztonság tesztelve legalább évente és bármely jelentős infrastruktúra vagy az alkalmazásfrissítés vagy módosítását (például az operációs rendszer frissítése egy alárendelt hálózati hozzáadni a környezetben, vagy a környezet hozzáadott webkiszolgáló).

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 11.3](#pci-dss-requirement-11-3). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló lett toll tesztelni, és a biztonsági rés beolvasott "," annak érdekében. A toll teszteredmények megkettőzhetők, például nmap vagy pentest-tools.com közös eszközök segítségével. A toll vizsgálati eredményeket nyújt vezetnek eredményre támadási felületét, nincs kihasználható elemekhez. Emellett [az Azure Security Center](https://azure.microsoft.com/services/security-center/) és [Azure Advisor](/azure/advisor/advisor-security-recommendations) adja meg a szervizelés és a biztonsági információk.|



### <a name="pci-dss-requirement-1132"></a>PCI DSS követelmény 11.3.2 programcsomag

**11.3.2 programcsomag** végrehajtása *belső* behatolást vagy a biztonság tesztelve legalább évente és bármely jelentős infrastruktúra vagy az alkalmazásfrissítés vagy módosítását (például az operációs rendszer frissítése egy alárendelt hálózati hozzáadni a környezetben, vagy a környezet hozzáadott webkiszolgáló).

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure szerződéseket független értékelő behatolást vagy a biztonság teszteléséhez a Microsoft Azure-határ. Piros-csapat gyakorlatokat is rendszeresen készül, és eredmények használ a biztonsági javítások. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló lett toll tesztelni, és a biztonsági rés beolvasott "," annak érdekében. A toll teszteredmények megkettőzhetők, például nmap vagy pentest-tools.com közös eszközök segítségével. A toll vizsgálati eredményeket nyújt vezetnek eredményre támadási felületét, nincs kihasználható elemekhez. Emellett [az Azure Security Center](https://azure.microsoft.com/services/security-center/) és [Azure Advisor](/azure/advisor/advisor-security-recommendations) adja meg a szervizelés és a biztonsági információk.|



### <a name="pci-dss-requirement-1133"></a>PCI DSS követelmény 11.3.3

**11.3.3** behatolást vagy a biztonság tesztelés során talált kihasználható biztonsági rések kijavítására sor és tesztelési meg kell ismételni a javításokat ellenőrzése.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Eljárások bevezetett az ismert biztonsági rések a Microsoft Azure platform összetevő figyelésére. <br /><br /><br /><br />Minden egyes megcélzott negyedév átfogó biztonsági rést keres a rangsorolt összetevői az Azure éles környezetben történik a biztonsági rések azonosítása. Eredmények jelentett érdekelt felek és javítási követi nyomon a csapat megszüntetésre keresztül. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | [Az Azure Security Center](https://azure.microsoft.com/services/security-center/) és [Azure Advisor](/azure/advisor/advisor-security-recommendations), szervizelés, és a biztonsági információk lettek megadva a használatával győződjön meg arról, hogy az összes fennálló problémákat a Contoso webes tároló bemutató CDE a szervizelt nyújt.|



### <a name="pci-dss-requirement-1134"></a>PCI DSS követelmény 11.3.4

**11.3.4** Szegmentálás elkülönítése más hálózati CDE használata esetén tesztek kerülnek végrehajtásra behatolást vagy a biztonság legalább évente és után módosításait Szegmentálás vezérlők/módszerek, és ellenőrizze, hogy a szegmentálási módszerek működési és hatékony, és Különítse el a CDE rendszerek minden kimenő érhető rendszert.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Eljárások bevezetett az ismert biztonsági rések a Microsoft Azure platform összetevő figyelésére. <br /><br /><br /><br />Minden egyes megcélzott negyedév átfogó biztonsági rést keres a rangsorolt összetevői az Azure éles környezetben történik a biztonsági rések azonosítása. Eredmények jelentett érdekelt felek és javítási követi nyomon a csapat megszüntetésre keresztül. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | [Az Azure Security Center](https://azure.microsoft.com/services/security-center/) és [Azure Advisor](/azure/advisor/advisor-security-recommendations), szervizelés, és a biztonsági információk lettek megadva a használatával győződjön meg arról, hogy az összes fennálló problémákat a Contoso webes tároló bemutató CDE a szervizelt nyújt.|



### <a name="pci-dss-requirement-11341"></a>PCI DSS követelmény 11.3.4.1

**11.3.4.1** *szolgáltatók csak további követelményei:* Szegmentálás használata esetén győződjön meg arról, a szegmentálási tesztelés behatolás elvégzésével PCI DSS hatókör szabályozza a legalább hat, hónap és módosítások után Szegmentálás vezérlők/metódusok.

> [!NOTE]
> Ez a követelmény a legjobb 2018 január 31-ig elteltével követelmény válik.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 11.3.4](#pci-dss-requirement-11-3-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | [Az Azure Security Center](https://azure.microsoft.com/services/security-center/) és [Azure Advisor](/azure/advisor/advisor-security-recommendations), szervizelés, és a biztonsági információk lettek megadva a használatával győződjön meg arról, hogy az összes fennálló problémákat a Contoso webes tároló bemutató CDE a szervizelt nyújt.|



## <a name="pci-dss-requirement-114"></a>PCI DSS követelmény 11,4

**11,4** -behatolásérzékelési és/vagy a behatolás-megelőzési technikák segítségével észlelése és/vagy a hálózati behatolás megelőzése. Figyelje a szervezet határain a kártya tulajdonosát adatok környezet, valamint a kritikus időpontokban, a kártya tulajdonosát adatok környezetben minden forgalmat, és riasztást küldjön a csoporthoz, és feltételezett biztonság sérüléseinek.
Behatolásérzékelési és megelőzési motorok, alapkonfigurációk és aláírások naprakészen tartása.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure végez valós idejű elemzési események a működési környezetben, és közel valós idejű riasztásokat az esetlegesen veszélyeztető a rendszer eseményekről készítése Azonosítók rendszerek. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló egy PaaS szolgáltatás, és hálózati behatolás felderítésére és megelőzésére Azure felelősségi hivatkozik. [Az Azure Security Center](https://azure.microsoft.com/services/security-center/) és [Azure Advisor](/azure/advisor/advisor-security-recommendations) adja meg a szervizelés és a behatolás lehetőséget.|



## <a name="pci-dss-requirement-115"></a>PCI DSS követelmény 11.5

**11.5** riasztási csoporthoz, illetéktelen módosítás (beleértve a módosításokat, kiegészítéseket és törlés) a kritikus rendszerfájlok, a konfigurációs fájlok vagy a tartalom és a módosítás-észlelési módszer (például fájl-integritási Hálózatfigyelő eszközök) telepítése fájlok; és úgy konfigurálja a kritikus fontosságú fájl összehasonlítást hetente legalább végrehajtásához szoftvert. 

> [!NOTE]
> Változás-észlelési célokra kritikus fájlok általában azokat, amelyek rendszeresen módosítsa, de utalhat, hogy a módosítását a rendszer biztonsági sérülésén vagy a biztonsági sérülés kockázatát. Változás-észlelési módszerek például a fájl-integritási figyelési termékek általában rendelkeznek előre konfigurált a kapcsolódó operációs rendszer kritikus fájljait. Egyéni alkalmazások, például más kritikus fontosságú fájlokat kell kiértékeli, és határozza meg az entitás (Ez azt jelenti, hogy a kereskedelmi vagy a service provider).

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure tart fenn, és értesíti a potenciális változások és tevékenységek is hatással lehetnek a biztonsági ügyfelek vagy a szolgáltatások egy online szolgáltatás irányítópulton keresztül történő rendelkezésre állását. A biztonsági kötelezettségeit, és a Microsoft Azure-ügyfelek biztonsági kötelezettségeit változtatások a Microsoft Azure-webhelyen időben.<br /><br />Telepítés vagy a Microsoft Azure éles környezetben korlátozódik szoftver módosítását engedélyezett felügyeleti csoporthoz, és a következő módosítása felügyeleti eljárások. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A webes tároló contoso bemutató egy PaaS szolgáltatás és címváltozásának felderítését Log Analytics segítségével van megvalósítva. További információkért lásd: [PCI útmutatást - alkalmazáshiba megoldások](payment-processing-blueprint.md#management-solutions).<br /><br />|



### <a name="pci-dss-requirement-1151"></a>PCI DSS követelmény 11.5.1

**11.5.1** alkalmazzon olyan folyamatot, az észlelésének megoldás által előállított riasztások válaszolni.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure figyelési esemény szabályokat a magas kockázatú műveletekhez, és az eszközök figyelését nagyobb mennyiségű biztosítanak. Azure által felügyelt hálózati eszközök számára meghatározott biztonsági előírásoknak való megfelelés figyeli. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló riasztások módosítások Naplóelemzési megvalósítása által biztosított. További információkért lásd: [PCI útmutatást - alkalmazáshiba megoldások](payment-processing-blueprint.md#management-solutions).<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>PCI DSS követelmény 11.6

**11.6** győződjön meg arról, hogy a biztonsági házirendek és a biztonsági figyelést és tesztelési műveleti eljárásokká dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló riasztások módosítások Naplóelemzési megvalósítása által biztosított. További információkért lásd: [PCI útmutatást - alkalmazáshiba megoldások](payment-processing-blueprint.md#management-solutions).<br /><br /><br /><br />|





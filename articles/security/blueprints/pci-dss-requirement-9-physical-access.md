---
title: Az Azure támogatási feldolgozási tervezetének - fizikai hozzáférési követelmények
description: PCI DSS követelmény 9
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: b702f3d3a06b1d47c2853ab51ccf4872441da03d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>Fizikai hozzáférési követelmények PCI DSS-kompatibilis környezetben 
## <a name="pci-dss-requirement-9"></a>PCI DSS követelmény 9

**Kártya tulajdonosát adatok való fizikai hozzáférés korlátozása**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Minden fizikai hozzáférést adatok vagy a rendszer, hogy house kártya tulajdonosát adatok egyéni felhasználók számára a lehetőséget, eszközök és adatok elérésére, és távolítsa el a rendszer vagy hardcopies biztosít, és megfelelően korlátozva kell lennie. Követelmény 9 alkalmazásában "helyszínen személyzet" vonatkozik, teljes és Részmunkaidős alkalmazottak, ideiglenes alkalmazottai, alvállalkozói és tanácsadóknak szól, akik az entitáshoz helyszíni fizikailag jelen vannak. Egy "látogató" a szállító, a Vendég bármely helyszínen csoporthoz, a szolgáltatás munkavállalók vagy bárki, aki be kell írnia a létesítmény rövid időre általában nem egy napnál hosszabb hivatkozik. "Media" az összes dokumentum és elektronikus kártya tulajdonosát adatokat tartalmazó adathordozó hivatkozik.

## <a name="pci-dss-requirement-91"></a>PCI DSS követelmény 9.1

**9.1** megfelelő létesítmény bejegyzés vezérlők rendszerek korlátozás és a figyelő a fizikai hozzáférést a kártya tulajdonosát adatok környezetben használható.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure a végrehajtási, a kényszerítése, és a fizikai biztonság adatközpontok, felügyeletéért felelős. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-911"></a>PCI DSS követelmény 9.1.1

**9.1.1** videó kamerák vagy vezérlő mechanizmusok (vagy mindkettő) történő érzékeny területekre történő egyes fizikai hozzáférés figyelésére használható. Tekintse át a gyűjtött adatokat, és más bejegyzések függ. Tárolásához legalább három hónapig csak jog egyéb korlátozza.

> [!NOTE]
> "Az érzékeny területekre" bármely adatközpontban, a kiszolgáló hely vagy a bármely területet, amely a rendszerek, tárolásához, feldolgozni, illetve kártya tulajdonosát adatokat hivatkozik. Ez nem tartalmazza a nyilvánosan elérhető területeken, ahol csak POS terminálok jelen van, például egy viszonteladótól pénztáros területein.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure a végrehajtási, érvényesítése és figyelési zárt láncú TV és a adatközpontok biometrikus hozzáférés mechanizmusai felelős. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-912"></a>PCI DSS követelmény 9.1.2

**9.1.2** nyilvánosan elérhető hálózat csatlakozók való hozzáférés korlátozása a fizikai és/vagy logikai vezérlőelemeket kell megvalósítani. 

Például hálózati csatlakozók található nyilvános területeihez és látogatók számára hozzáférhető sikerült letiltásra kerül, és csak akkor engedélyezett, ha a hálózati hozzáférés engedélyezése explicit módon. Másik lehetőségként folyamatok végrehajtható győződjön meg arról, hogy a látogatók vannak kísért minden alkalommal területeken az aktív hálózati csatlakozók.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nincs nyilvánosan elérhető hálózat csatlakozók a Microsoft Azure platform belül van. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-913"></a>PCI DSS követelmény 9.1.3

**9.1.3** vezeték nélküli hozzáférési pontok, az átjárók, a kézi eszközök, a hálózati/kommunikációs hardver és a telekommunikációs sorok való fizikai hozzáférés korlátozása.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Fizikai hálózati hardver szorosan vezérli hozzáférés listák Microsoft Azure-hozzáférést, több űrlapos hitelesítés, fizikai határok bejegyzést, és az üzleti követelményt kell jóvá kell hagyni a berendezéshez való hozzáféréshez. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



## <a name="pci-dss-requirement-92"></a>PCI DSS követelmény 9.2

**9.2** fejlesztése eljárások egyszerűen megkülönböztetni a helyszínen csoporthoz és a látogatók, tartalmazza:
- Helyszínen csoporthoz és látogatók (például hozzárendelése jelvény) azonosítása
- Hozzáférési követelmények módosításai
- Visszavonni vagy leállítja a helyszínen csoporthoz és lejárt látogató azonosító (például az azonosító jelvény).

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure a végrehajtási, érvényesítése és biztonsági és alkalmazott vagy alvállalkozó azonosító fizikai hozzáférés figyelése adatközpontok meglátogatásakor felelős. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



## <a name="pci-dss-requirement-93"></a>PCI DSS követelmény 9.3 alatt

**9.3** hozzáférést fizikai helyszínen csoporthoz, és az érzékeny területekre a következőképpen:
- Hozzáférés engedélyezve legyen, és egyes feladatok függvény alapján.
- Hozzáférés megszüntetése után azonnal visszavonva, és az összes fizikai hozzáférés mechanizmus, például a kulcsokat, hozzáférés-kártyák, stb., adott vissza, vagy le van tiltva.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Hozzáférési engedélyek a Microsoft azon adatközpontjainak a legalacsonyabb jogosultsági szint elve alapján adatközpont csapat által jóváhagyott engedélyezett hozzáférési lista szabályozza. A hozzáférés-vezérlési lista tekintse át, ellenőrzése és frissítése negyedévente.<br /><br />A Microsoft Azure adatközpontjaiban fizikai hozzáférést biztosító eszközök használatára, például a szegélyhálózat kapuk, elektronikus hozzáférést jelvény olvasók, biometrikus olvasók, man-trapek/portálok és víruskereső fázis biztonsági eszközök. Hozzáférés jelvény eszközök folyamatosan figyeli. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



## <a name="pci-dss-requirement-94"></a>PCI DSS követelmény 9.4

**9.4** azokat az eljárásokat, és engedélyezik a látogatók. Eljárások a következőket kell tartalmaznia.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure felelős, az előre jóváhagyott kézbesítések végrehajtó érkezik egy biztonságos betöltése fiókba, amely fizikailag elkülönített származó információk feldolgozás, és arra jogosult személyek által figyelt. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-941"></a>PCI DSS követelmény 9.4.1

**9.4.1** látogatók megadása előtt engedélyezett, és kísért minden alkalommal területeken, ahol kártya tulajdonosát adatainak feldolgozása vagy fenntartott belül.


**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure felelős, az előre jóváhagyott kézbesítések végrehajtó érkezik egy biztonságos betöltése fiókba, amely fizikailag elkülönített származó információk feldolgozás, és arra jogosult személyek által figyelt. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-942"></a>PCI DSS követelmény 9.4.2

**9.4.2** látogatók azonosított, és egy jelvény vagy egyéb azonosítási, amely lejár, és láthatóan megkülönbözteti a látogatók a helyszínen csoporthoz.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lehet, hogy a Microsoft data access központjában előre jóváhagyott, és jogosult látogatók szükséges, hogy be fizikai biztonsági érkezési helyén, és adja meg a azonosítója érvényes igazolása előtt. Jelvények egyértelműen jelzik az alkalmazottak. Alvállalkozói látogatók ideiglenes badges, hogy a létesítmény elhagyását követően le kell kaphat. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-943"></a>PCI DSS követelmény 9.4.3

**9.4.3** látogatók felkérést kap a jelvényt vagy a azonosító, mielőtt elhagynák a létesítményekhez vagy lejárati időpontjában lemondani.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft-létesítményben induláskor jelvények lemondani látogatók szükségesek. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-944"></a>PCI DSS követelmény 9.4.4

**9.4.4** látogató adott területre a napló a létesítmény, valamint a számítógép kiszolgálótermek és adatközpontok, ahol kártya tulajdonosát adatok tárolt és továbbított adatokról látogató tevékenység fizikai napló karbantartásához használt.
A dokumentum a látogató nevét, a vállalkozás képviselt és a helyszínen személyzet bejelentkezési a fizikai hozzáférés engedélyezése.
Ez a napló legalább három hónapos, továbbra is csak jog egyéb korlátozza.

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure mint egy fizikai napló látogató tevékenység a létesítmény, valamint a számítógép kiszolgálótermek és az adatközpontok, ahol kártya tulajdonosát adatok tárolt és továbbított adatokról a látogató adott területre a napló karbantartásáért felelős. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



## <a name="pci-dss-requirement-95"></a>PCI DSS követelmény 9,5

**9,5** fizikailag is biztosítsa, minden adathordozót.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-951"></a>PCI DSS követelmény 9.5.1

**9.5.1** egy biztonságos helyre, lehetőség szerint egy telephelytől távoli létesítmény, például egy másik vagy tartalék helyet, vagy egy kereskedelmi tárolási létesítmény media biztonsági másolatok tárolására. Tekintse át a hely biztonsági legalább évente.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-96"></a>PCI DSS követelmény 9.6

**9.6** átfogóan bármilyen típusú adathordozók, például az alábbiakat a belső vagy külső terjesztése szigorú szabályozhatja.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-961"></a>PCI DSS követelmény 9.6.1

**9.6.1** media besorolását, így az adatok érzékenységének is meg lehet határozni.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-962"></a>PCI DSS követelmény 9.6.2

**9.6.2** küldje az adathordozót biztonságos courier vagy más kézbesítési módszert, amely pontosan követhető nyomon.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-963"></a>PCI DSS követelmény 9.6.3

**9.6.3** ellenőrizze, hogy felügyeleti jóvá minden adathordozó áthelyeznek egy védett terület (beleértve a amikor media terjesztenek egyéni felhasználók számára).

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-97"></a>PCI DSS követelmény 9.7

**9.7** szigorú ellenőrzése alatt tartja a tárolási és adathordozó kisegítő karbantartása.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-971"></a>PCI DSS követelmény 9.7.1

**9.7.1** megfelelően az összes adathordozó készlet naplók karbantartása, és legalább évente olyan adathordozó-készletek.


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-98"></a>PCI DSS követelmény 9.8

**9.8** szüntesse meg az adathordozó, már nem szükség esetén üzleti vagy jogi okokból az alábbiak szerint.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-981"></a>PCI DSS követelmény 9.8.1

**9.8.1** zúzására szolgálnak, hamvasszuk vagy pulp nyomtatott anyagokat, így nem dokumentumoldalakra kártya tulajdonosát adatokat. A tároló, amelyek megsemmisítését használt biztonságos.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatot tárol az Azure SQL Database. A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-982"></a>PCI DSS követelmény 9.8.2

**9.8.2** kártya tulajdonosát elektronikus adathordozón található adatok helyreállíthatatlan leképezési, hogy nem dokumentumoldalakra kártya tulajdonosát adatokat.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Adatok megsemmisítésének technikák az adatok objektumtípust, törlés, függenek, hogy előfizetések, tárolási, virtuális gépeket vagy adatbázisokat. A Microsoft Azure több-bérlős környezetben alapos figyelmet kell fordítani, hogy egy felhasználói adatokat nem engedélyezett "memóriavesztés" egy másik ügyféladatok, vagy amikor az ügyfél törli az adatokat, más ügyfélre (beleértve a legtöbb esetben az ügyfél aki egyszer az adatok tulajdonosa) is hozzáférhetnek a törölt adatokat.<br /><br />A Microsoft Azure NIST 800-88 iránymutatást Media tisztítási, amely gondoskodik arról, hogy az egyszerű probléma megoldásának nem szándékos kiadott követi. Ezeket az irányelveket elektronikus és a fizikai tisztítási foglalnia. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló teljes mértékben központi telepítése során használt erőforráscsoport törlésével törölhetők.|



## <a name="pci-dss-requirement-99"></a>PCI DSS követelmény 9.9

**9.9** fizetőkártyák adatainak keresztül az illetéktelen módosítást és a helyettesítő kártya közvetlen fizikai interakcióba rögzítő eszközök védelme.

> [!NOTE]
> Ezek a követelmények az kártya-jelenleg tranzakció (Ez azt jelenti, hogy a kártyalehúzás vagy dip) eladási használt kártya-olvasási eszközökre vonatkoznak. Ez a követelmény nem javasolt kézi kulcs-bejegyzés összetevők, például számítógép billentyűzetek és POS keypads vonatkozik. 

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes rendszer naplózni Azure használja.<br /><br />A Naplóelemzési változások kiterjedt naplózás biztosít. Módosítások tekintse át, és pontossága ellenőrizni. Részletesebb útmutatásért lásd: [útmutatást PCI - naplózás és naplózási](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-991"></a>PCI DSS követelmény 9.9.1

**9.9.1** karbantartása eszközöket naprakész listáját. A lista a következőket kell tartalmaznia:
- Ellenőrizze, az eszköz modell
- Eszköz (például a webhely vagy a létesítmény, ahol az eszköz nem található cím) helye
- Eszköz sorozatszámát vagy más módon egyedi azonosító

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít egy referencia-architektúrában és az összes olyan szolgáltatás, a központi telepítési dokumentációjában használja listáját.|



### <a name="pci-dss-requirement-992"></a>PCI DSS követelmény 9.9.2

**9.9.2** rendszeres időközönként vizsgálja meg az eszköz felületek illetéktelen módosítását (például kártya skimmers eszközökre hozzáadása), vagy helyettesítő (például a sorozatszámot vagy más eszköz ellenőrzésével azt jellemzőkkel rendelkezik nem lett kicserélve a egy csalárd eszközt használ).

> [!NOTE]
> Jelentkezik, hogy egy eszköz esetleg illetéktelenül vagy helyettesített például váratlan mellékletek vagy az eszköz, hiányzó vagy módosított biztonsági címkék, megszakadt vagy különböző színű kis-és nagybetűk, vagy a sorozatszámot vagy az egyéb módosítások csatlakoztatott kábelek Külső megjelölés.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-993"></a>PCI DSS követelmény 9.9.3

**9.9.3** képzés biztosítása csoporthoz, és vegye figyelembe megkísérelt illetéktelen vagy cseréje az eszközök számára. Képzési a következőket kell tartalmaznia:
- Bármely külső személyek magát, hogy azok módosítására, vagy eszközök hibáinak elhárítása hozzáférés biztosítása előtt javítási vagy karbantartási csoporthoz identitásának ellenőrzéséhez.
- Nem telepítéséhez, cseréje vagy eszközöket ellenőrzése nélkül.
- Vegye figyelembe az eszközök (például ismeretlen személyek kihúzásával, vagy nyissa meg az eszközök kísérletet) körül gyanús viselkedését.
- Gyanús viselkedését és eszköz illetéktelen módosítását vagy a megfelelő helyettesítő jelentést (például, hogy a kezelő vagy biztonsági tisztviselő).

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|



## <a name="pci-dss-requirement-910"></a>PCI DSS követelmény 9.10

**9.10** győződjön meg arról, hogy a biztonsági házirendek és üzemeltetési eljárások kártya tulajdonosát adatok való fizikai hozzáférés korlátozására dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható.|





---
title: "FedRAMP Azure tervezetének Automation - adathordozó védelme"
description: "Webes alkalmazásokhoz, a FedRAMP - adathordozó védelme"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9cb33abc7ab88aaa54a77308c7863f8128f2e6c7
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="media-protection-mp"></a>Média védelme (MP)

> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.

## <a name="nist-800-53-control-mp-1"></a>A NIST 800-53 vezérlő MP-1

#### <a name="media-protection-policy-and-procedures"></a>Media védelmi házirend- és eljárások

**MP-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] egy media védelmi házirendje, amely célra, hatókör, szerepkörök, feladatok, felügyeleti kötelezettségvállalás, koordinációs szervezeti entitásokat, és a megfelelőségi; és eljárásokat azzal, hogy a media védelmi házirendet és a hozzá tartozó adathordozó védelmére vezérlők; végrehajtásának megkönnyítése és ellenőrzi, és frissíti az aktuális media védelmi házirend [hozzárendelés: szervezet által meghatározott gyakoriság]; és media védelmi eljárásai [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű media védelmi házirendet és eljárások a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-mp-2"></a>A NIST 800-53 vezérlő MP-2

#### <a name="media-access"></a>Media Access

**MP-2** a szervezet korlátozza a hozzáférést [hozzárendelés: digitális és/vagy nem digitális media szervezet által meghatározott típusú] való [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure media access révén a Microsoft biztonsági házirend van megvalósítva. Logikai hozzáférés digitális adathordozóra Active Directory csoportházirend-objektumok (GPO-k a AD) és a biztonsági csoportok segítségével szabályozható. A datacenter hozzáférési folyamat összes adathordozó való fizikai hozzáférés korlátozza. Hozzáférés korlátozódik használhatják, akik valós üzleti célú adatok eléréséhez. Tekintse meg a PE-3, fizikai hozzáférés-vezérlés, kapcsolatban további részleteket a helyen a datacenter hozzáférés-vezérlést. Az eszköz védelmi szabványos határozza meg a védelmet, a titkosítás, integritás és rendelkezésre állásának információs eszközeit a Microsoft Azure adatközpontjaiban belül védelméhez szükséges. |


 ## <a name="nist-800-53-control-mp-3a"></a>A NIST 800-53 vezérlő MP-3.a

#### <a name="media-marking"></a>Adathordozó-jelölés

**MP-3.a** a szervezet, amely jelzi, a terjesztési korlátozások vonatkoznak, figyelmeztetések, és a megfelelő biztonsági jelölések (ha vannak) az adatok kezelésével rendszer adathordozó jelöli meg.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure jelöli meg a Microsoft adatközpontjaiban HBI, MBI vagy LBI belül eszközök (magas, közepes vagy alacsony Üzletmenetre gyakorolt hatás) megjelölését, amelyhez különböző szintű biztonságot és óvintézkedéseket kezelése szükséges. Eszköz tulajdonosait van szükség az eszközök, amelyek egy Microsoft adatközpontba belül vannak tárolva. Tekintse meg eszköz besorolás szabványos és eszköz védelmi szabványos további információt. |


 ## <a name="nist-800-53-control-mp-3b"></a>A NIST 800-53 vezérlő MP-3.b

#### <a name="media-marking"></a>Adathordozó-jelölés

**MP-3.b** a szervezet mentesíti [hozzárendelés: rendszer adathordozó szervezet által meghatározott típusú] jelölheti meg, amíg az adathordozó lépje [hozzárendelés: szervezet által meghatározott szabályozott területek].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure megköveteli az eszköz tulajdonosait, hogy egy eszköz besorolással rendelkező eszközeik hozzárendelése, és nem eszközök mentesülnek ezt a követelményt. A Microsoft datacenter környezet eszközök kiszolgálók, a hálózati eszközök és a mágneses alatt. Más digitális adathordozójának, például USB flash/USB-meghajtók, külső cserélhető meghajtók esetében vagy CD/DVD-k nem használnak. Nem-digitális adathordozó nem használatos az adatközpontban. |


 ## <a name="nist-800-53-control-mp-4a"></a>A NIST 800-53 vezérlő MP-4.a

#### <a name="media-storage"></a>Tároló adathordozó

**MP-4.a** a szervezet fizikailag vezérli, és tárolja biztonságos helyen [hozzárendelés: digitális és/vagy nem digitális media szervezet által meghatározott típusú] belül [hozzárendelés: szervezet által meghatározott szabályozott területek].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure digitális adathordozójának eszközök fizikailag és biztonságosan tárolt adatblokkokra datacenter közös elhelyezés helyiségekben. A Microsoft adatközpontjaiban rendelkezik rétege fizikai hozzáférés-vezérlést (hozzáférés jelvény, biometrikus; lásd a fizikai hozzáférés-vezérléssel kapcsolatos további részletekért lásd a PE-3), valamint a biztonságos tárolásához. A digitális adathordozójának magában foglalja a kiszolgálók, a hálózati eszközök és a biztonsági mentéshez használt mágneses szalagok. Nem-digitális adathordozó nem szerepel az Adatközpont-környezeten. |


 ## <a name="nist-800-53-control-mp-4b"></a>A NIST 800-53 vezérlő MP-4.b

#### <a name="media-storage"></a>Tároló adathordozó

**MP-4.b** a szervezet rendszer adathordozó védi, addig, amíg az adathordozót megsemmisülnek vagy berendezések technikák és eljárások megtisztított használata engedélyezett.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure digitális adathordozójának eszközök védettek a Microsoft datacenter colocations keresztül fizikai hozzáférés-vezérlést (PE-3) és a logikai hozzáférés-vezérlés (IA-2) az eszköz élettartama idején. Microsoft Azure eszközök nincs bejelölve, törlődnek, vagy megsemmisül szervizcsomaggal NIST 800-88 előtt az eszközök következetes metódusával. Az eszköz megsemmisítése a Microsoft Azure helyszínen eszköz megsemmisítése szolgáltatások használja. |


 ## <a name="nist-800-53-control-mp-5a"></a>A NIST 800-53 vezérlő MP-5.a

#### <a name="media-transport"></a>Szállítási adathordozó

**MP-5.a** a szervezet védi, és szabályozza [hozzárendelés: rendszer adathordozó szervezet által meghatározott típusú] ellenőrzött területek kívül kerüljön [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure digitális média a Microsoft adatközpontjaiban közé tartozik, a kiszolgálók, a hálózati eszközök, és mágneses mentést tartalmazó szalagok és lemezek megfelelő. A Microsoft adatközpontjaiban ne használjon nem digitális adathordozó. Microsoft használja, amely az adatközponton kívülről szállítása adathordozók védelméhez három módszer: 1) biztonságos átviteli, 2) titkosítási 3), törlése, vagy szüntesse meg. |


 ## <a name="nist-800-53-control-mp-5b"></a>A NIST 800-53 vezérlő MP-5.b

#### <a name="media-transport"></a>Szállítási adathordozó

**MP-5.b** a szervezet az elszámolási kötelezettségéről szóló információkat rendszer adathordozó ellenőrzött területeken kívül kerüljön tart fenn.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure tart fenn elszámolási kötelezettségéről szóló útmutató segítségével datacenter hagyja a NIST SP 800-88 eszközök: konzisztens tisztításokat/kiürítése, eszköz megsemmisítése, titkosítás, pontos felvétele a leltárba, nyomon követési és felügyeleti lánc fenntartása védelméről kerüljön. |


 ## <a name="nist-800-53-control-mp-5c"></a>A NIST 800-53 vezérlő MP-5.c

#### <a name="media-transport"></a>Szállítási adathordozó

**MP-5.c** a szervezet dokumentumokat az átviteli információk rendszer adathordozó társított tevékenységet.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure tart fenn a rekordok készlet előtt átviteli, nyomon követését és felügyeleti lánc fenntartása védelméről átviteli, eszköz tisztítás vagy-ürítés, eszköz megsemmisítése, eszközök és a készlet érvényesítése után átvitel során. |


 ## <a name="nist-800-53-control-mp-5d"></a>A NIST 800-53 vezérlő MP-5.d

#### <a name="media-transport"></a>Szállítási adathordozó

**MP-5.d** a szervezet korlátozza az átviteli információk rendszer adathordozóján az arra jogosult személyek társított tevékenységet.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure felügyeleti lánc védelme révén arra jogosult személyek eszköz átviteli tevékenységeit korlátozza. Zárolások, használatának igazoló bélyegzőket védelmet, és az eszköz készletek adatérvényesítést igénylő gondoskodik arról, hogy csak az arra jogosult személyek az eszköz átvitel részt. |


 ### <a name="nist-800-53-control-mp-5-4"></a>A NIST 800-53 vezérlő MP-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>Szállítási adathordozó |} Titkosítási védelem

**MP-5 (4)** a információk rendszer megvalósítja a bizalmas és ellenőrzött területeken kívül kerüljön digitális adathordozón tárolt adatok sértetlenségének védelme érdekében titkosítási mechanizmus.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | Alkalmazza a Microsoft Azure adatok védelmi szolgáltatás (terjesztési pontok) kezelése a kriptográfiai kulcsok modullal a FIPS 140-2-3 érvényesített a blokkszintű titkosítás (#1694 cert) és a hardveres biztonsági MODULT (#1178 cert) a mágneses szalagokon AES 256 bites titkosított adatok védelmét. |


 ## <a name="nist-800-53-control-mp-6a"></a>A NIST 800-53 vezérlő MP-6.a

#### <a name="media-sanitization"></a>Media tisztítási

**MP-6.a** a szervezet sanitizes [hozzárendelés: szervezet által megadott információk rendszer adathordozóján] kívül szervezeti vezérlő felszabadítása előtt, vagy kiadását újbóli használata [hozzárendelés: szervezet által meghatározott tisztítási technikák és eljárások] alkalmazható szövetségi és szervezeti szabványok és házirendek megfelelően.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure-nak a Microsoft Azure datacenter környezetben kell tisztítani/törlődnek a Microsoft Azure jóváhagyott eszközökkel és a NIST SP konzisztens módon digitális adathordozójának 800-88 irányelvek a Media tisztítási újrafelhasznált vagy a ártalmatlanítani előtt . Nem-digitális adathordozó nem a datacenter-környezetben a Microsoft Azure használják. |


 ## <a name="nist-800-53-control-mp-6b"></a>A NIST 800-53 vezérlő MP-6.b

#### <a name="media-sanitization"></a>Media tisztítási

**MP-6.b** a szervezet tisztítási mechanizmusok erőssége és integritásának arányos a Biztonság kategória vagy információknak az funkcióit használja.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure nagybetűhasználatának/kiürítése adatok összhangban NIST 800-88 szervizcsomaggal, és amelyeket arányos az eszköz a Microsoft Azure eszközbesorolás az adategységek törlését és folyamatok használja. Megsemmisítése igénylő eszközök a Microsoft Azure használja a helyszínen eszköz megsemmisítése szolgáltatások. |


 ### <a name="nist-800-53-control-mp-6-1"></a>A NIST 800-53 vezérlő MP-6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>Media tisztítási |} Tekintse át / jóváhagyása / nyomon / dokumentum / ellenőrzése

**Felügyeleti pont – 6 (1)** a szervezet ellenőrzi, hogy jóvá, nyomon követi, dokumentumokat és media tisztítási és kivezetési műveletek ellenőrzi.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure javítást alkalmazott media tisztítási eljárások útmutatás a NIST SP 800-88 a rendszer a eszköz besorolás szabványos és az eszköz védelmi szabványos. Minden mágneses vagy elektronikus úton tisztítani/kiüríti következő NIST SP 800-88 előírásoknak a Azure-objektum besorolását. Azure adategységek törlését, a rendkívüli protokoll megoldások (EPS) használja. EPS szoftver által támogatott NIST SP 800-88 követelmények tisztítására és végleges törlés/secure törlését a. |


 ### <a name="nist-800-53-control-mp-6-2"></a>A NIST 800-53 vezérlő MP-6 (2)

#### <a name="media-sanitization--equipment-testing"></a>Media tisztítási |} Berendezések tesztelése

**Felügyeleti pont – 6 (2)** a szervezet tesztek tisztítási berendezések és eljárások [hozzárendelés: szervezet által meghatározott gyakoriság] ellenőrizheti, hogy a tervezett tisztítási érik.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure adategységek törlését és folyamatok használja adatok összhangban NIST 800-88 szervizcsomaggal nagybetűhasználatának/kiürítése. 180 nap alatt, a Tartományvezérlők műveletek teszteli, a Microsoft Azure adategységek törlését és az Eltávolítás folyamata. A teszt Tartományvezérlők műveletek ellenőrzi annak ellenőrzéséhez, hogy az adatok az adatok törlését egységek fertőtlenítsék-e tesztelt merevlemezek bírósági elemzéshez keresztül elért a tervezett tisztítási |


 ### <a name="nist-800-53-control-mp-6-3"></a>A NIST 800-53 vezérlő MP-6 (3)

#### <a name="media-sanitization--nondestructive-techniques"></a>Media tisztítási |} Visszafordítható technikák

**Felügyeleti pont – 6 (3)** a szervezet visszafordítható tisztítási technikák vonatkozik hordozható tárolóeszközök előtt ilyen típusú eszközök gazdarendszerhez csatlakozik az információkat a következő esetekben: [hozzárendelés: szervezet által meghatározott hordozható tárolóeszközök tisztítási igénylő esetekben].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure gondoskodik arról, hogy Azure adatközpontjaiban hajtsa végre az eszközök és a cserélhető adathordozó biztonsági eljárás az adatok Center szolgáltatások futtatása könyv leállítja, nehogy a fertőzés a kormányzati környezet hordozható tárolóeszközök kártevő által. Az eljárás határozza meg, hogy a következő műveleteket kell-e venni az USB-meghajtókat a kormányzati környezet használata előtt: <br /> (1) formázza az USB-meghajtók megvásárlásakor a meghajtók rendszer először a gyártó vagy a szállító, első használata előtt, vagy egy másik eszköz újrafelhasználását. <br /> (2) semmilyen használandó kormányzati kijelölt területén kártevők, mielőtt kilépteti a meghajtó a területre USB-meghajtó vizsgálata. <br /> (3) után egy kormányzati kijelölt területen meghajtót használ, mielőtt elhagynák a terület a meghajtó formázása. <br /> Az eszközök és a cserélhető adathordozó biztonsági eljárást is szükséges, hogy minden elveszett, elvetett, ellopott vagy rossz helyen lévő görgetőgomb meghajtók soha nem lesznek Azure adatközpontjaiban területére, de ehelyett kell katalogizálni és meg kell semmisíteni. |


 ## <a name="nist-800-53-control-mp-7"></a>A NIST 800-53 vezérlő MP-7

#### <a name="media-use"></a>-E használni

**MP-7** a szervezet [kijelölés: korlátozza; Ez a beállítás letiltja] használata [hozzárendelés: rendszer adathordozó szervezet által meghatározott típusú] a [hozzárendelés: szervezet által megadott információk rendszerek vagy rendszerösszetevők] használatával [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure megköveteli az eszköz tulajdonosait, hogy egy eszköz besorolással rendelkező eszközeik hozzárendelése, és nem eszközök ezt a követelményt alól. A Microsoft Azure datacenter környezetben eszközök tekintse meg a kiszolgálók és hálózati eszközök. Más digitális adathordozójának, például USB flash/USB-meghajtók speciális házirendeket és az eszközök kezelésének eljárásairól kezeli. Nem használja a CD/DVD-n. Nem-digitális adathordozó nem használatos az adatközpontban. A Microsoft Azure-adatközponti környezetben digitális adathordozójának használatát figyelt 24 x 7 zárt láncú TV érvényességének keresztül. További részletekért ellenőrizze a PE-06. |


 ### <a name="nist-800-53-control-mp-7-1"></a>A NIST 800-53 vezérlő MP-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>Media használata |} Tulajdonos nélkül tilthatja le

**Felügyeleti pont – 7 (1)** a szervezet a szervezeti adatok rendszerek tiltja hordozható tárolóeszközök használatát, ha ilyen típusú eszközök nem azonosítható tulajdonosa.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt adathordozó telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | Microsoft írható, cserélhető adathordozó korlátozza, amely explicit módon jóváhagyta a Tartományvezérlők eszközök és a cserélhető adathordozó eljárás adatközpont-kezelés adathordozóra. Személyes tulajdonban lévő, vagy még nem azonosítható tulajdonos Media tilos egy éles területen, a Microsoft Datacenter munkahelyi szabályok és szabályozások dokumentum leírtaknak megfelelően. |

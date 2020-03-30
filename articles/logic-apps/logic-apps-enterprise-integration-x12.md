---
title: X12 üzenetek a B2B integrációhoz
description: Exchange X12 üzenetek EDI formátumban a B2B vállalati integrációhoz az Azure Logic Apps alkalmazásokkal a vállalati integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: cbf0a1f033ddafc68debab8de26dff29d73cc98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651474"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12-üzenetek a B2B vállalati integrációhoz az Azure Logic Apps vállalati integrációs csomaggal

Mielőtt X12-üzeneteket cserélhetne az Azure Logic Apps szolgáltatáshoz, létre kell hoznia egy X12-szerződést, és a szerződést az integrációs fiókban kell tárolnia. Az x12-es szerződés létrehozásának lépéseit az alábbiakban olvashatja.

> [!NOTE]
> Ez az oldal az Azure Logic Apps X12-funkcióit ismerteti. További információ: [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Előkészületek

Itt vannak a szükséges elemek:

* Az Azure-előfizetéshez már definiált és társított [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md)
* Legalább két [partner,](../logic-apps/logic-apps-enterprise-integration-partners.md) amely az integrációs fiókban van definiálva, és az X12 azonosítóval konfigurálva az **Üzleti identitások** csoportban    
* Az integrációs fiókba feltölthető szükséges [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md)

Miután [létrehozott egy integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [partnereket adott hozzá](logic-apps-enterprise-integration-partners.md), és rendelkeznie kíván egy használni kívánt [sémával,](../logic-apps/logic-apps-enterprise-integration-schemas.md) az alábbi lépések végrehajtásával létrehozhat egy X12-es szerződést.

## <a name="create-an-x12-agreement"></a>X12-es szerződés létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com "Azure portál") 

2. Az Azure főmenüjében válassza a **Minden szolgáltatás**lehetőséget. 
   A keresőmezőbe írja be az "integráció" szót, majd válassza **az Integrációs fiókok lehetőséget.**  

   ![Az integrációs fiók megkeresése](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Ha nem jelenik meg **az Összes szolgáltatás,** előfordulhat, hogy először ki kell bontania a menüt. Az összecsukott menü tetején válassza a **Megjelenítés menüt**.

3. Az **Integrációs fiókok csoportban**válassza ki azt az integrációs fiókot, amelyhez hozzá szeretné adni a szerződést.

   ![Válassza ki az integrációs fiókot, ahol létre szeretné hozni a megállapodást](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Válassza **az Áttekintés**lehetőséget, majd válassza a Megállapodások **csempét.** 
   Ha nem rendelkezik Megállapodások csempével, először adja hozzá a csempét. 

   ![Válassza a "Megállapodások" csempe](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. A **Megállapodások csoportban**válassza **a Hozzáadás**lehetőséget.

   ![Válassza a "Hozzáadás" lehetőséget](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. A **Hozzáadás**csoportban adja meg a szerződés **nevét.** 
   A szerződés típusához válassza az **X12**lehetőséget. 
   Válassza ki a **házigazda partnert,** **a fogadóidentitást**, **a vendégpartnert**és a **vendégidentitást** a szerződéshez. 
   További részletek a tulajdonról ebben a lépésben található táblázatban találhatók.

    ![A megállapodás részleteinek megadása](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Tulajdonság | Leírás |
    | --- | --- |
    | Név |A megállapodás neve |
    | Megállapodás típusa | X12-nek kell lennie |
    | Fogadó partner |A megállapodáshoz házigazda és vendégpartner is szükséges. A gazdapartner a szerződést konfiguráló szervezetet jelöli. |
    | Állomás identitása |A gazdapartner azonosítója |
    | Vendégpartner |A megállapodáshoz házigazda és vendégpartner is szükséges. A vendégpartner képviseli azt a szervezetet, amely a fogadó partnerrel üzleti kapcsolatban áll. |
    | Vendég identitása |A vendégpartner azonosítója |
    | Fogadási beállítások |Ezek a tulajdonságok a megállapodás által fogadott összes üzenetre vonatkoznak. |
    | Beállítások küldése |Ezek a tulajdonságok a megállapodás által küldött összes üzenetre vonatkoznak. |  

   > [!NOTE]
   > Az X12-megállapodás feloldása a feladó minősítőjének és azonosítójának, valamint a partnerben és a bejövő üzenetben meghatározott fogadóminősítőnek és-azonosítónak való megfeleltetéstől függ. Ha ezek az értékek megváltoznak a partnerén, frissítse a szerződést is.

## <a name="configure-how-your-agreement-handles-received-messages"></a>A szerződés által fogadott üzenetek kezelésének konfigurálása

Most, hogy beállította a szerződés tulajdonságait, beállíthatja, hogy ez a megállapodás hogyan azonosítja és kezelje a partnertől a jelen szerződés keretében kapott bejövő üzeneteket.

1.  A **Hozzáadás**csoportban válassza a **Fogadási beállítások lehetőséget.**
Konfigurálja ezeket a tulajdonságokat az Önnel üzeneteket cserélő partnerrel kötött megállapodás alapján. A tulajdonságleírásokat lásd az ebben a szakaszban található táblázatokban.

    **A fogadási beállítások** a következő szakaszokba vannak rendezve: azonosítók, nyugtázás, sémák, borítékok, vezérlőszámok, érvényesítések és belső beállítások.

2. Miután elkészült, az **OK**gombra választva mentse a beállításokat.

Most már a megállapodás készen áll a kiválasztott beállításoknak megfelelő bejövő üzenetek kezelésére.

### <a name="identifiers"></a>Azonosítók

![Azonosító tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Tulajdonság | Leírás |
| --- | --- |
| ISA1 (engedélyezési minősítő) |Válassza ki az Engedélyezési minősítő értékét a legördülő listából. |
| ISA2 (ISA2) |Választható. Adja meg az engedélyezési adatok értékét. Ha az ISA1 érték értéke nem 00, adjon meg legalább egy alfanumerikus karaktert, és legfeljebb 10-et. |
| ISA3 (biztonsági minősítő) |Válassza ki a Biztonsági minősítő értékét a legördülő listából. |
| ISA4 |Választható. Adja meg a Biztonsági adatok értékét. Ha az ISA3-hoz megadott érték nem 00, adjon meg legalább egy alfanumerikus karaktert, és legfeljebb 10-et. |

### <a name="acknowledgment"></a>Elismerés

![Nyugtázási tulajdonságok beállítása](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Tulajdonság | Leírás |
| --- | --- |
| TA1-re kell számítani |Technikai nyugtát ad vissza a csomópont feladójának |
| BE várható |Funkcionális nyugtát ad vissza a csomópont feladójának. Ezután válassza ki, hogy a 997-es vagy a 999-es nyugtát a sémaverzió alapján szeretné-e kiválasztani. |
| AK2/IK2 hurok belefoglalása |Lehetővé teszi az AK2 hurkok létrehozását az elfogadott tranzakciókészletek funkcionális nyugtázásában |

### <a name="schemas"></a>Sémák

Válasszon sémát minden tranzakciótípushoz (ST1) és feladói alkalmazáshoz (GS2). A fogadási folyamat szétszereli a bejövő üzenetet úgy, hogy a bejövő üzenetben lévő ST1 és GS2 értékeket egyezteti az itt megadott értékekkel, valamint a bejövő üzenet sémáját az itt beállított sémával.

![Séma kijelölése](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Verzió |Az X12-es verzió kiválasztása |
| Tranzakció típusa (ST01) |A tranzakciótípus kiválasztása |
| Feladó alkalmazása (GS02) |A feladóalkalmazás kiválasztása |
| Séma |Jelölje ki a használni kívánt sémafájlt. Sémák kerülnek az integrációs fiókba. |

> [!NOTE]
> Konfigurálja az [integrációs fiókba](../logic-apps/logic-apps-enterprise-integration-accounts.md)feltöltött szükséges [sémát.](../logic-apps/logic-apps-enterprise-integration-schemas.md)

### <a name="envelopes"></a>Borítékok

![Adja meg az elválasztót egy tranzakciókészletben: válassza a Szabványos azonosító vagy az Ismétléselválasztó lehetőséget.](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Tulajdonság | Leírás |
| --- | --- |
| ISA11 használat |Megadja a tranzakciókészletben használandó elválasztót: <p>Válassza **a Szabványos azonosító** lehetőséget, ha a decimális jelöléshez pont (.) van, nem pedig az EDI fogadási folyamatbejövő dokumentumának decimális jelölése. <p>Válassza **az Ismétlés elválasztó** lehetőséget egy egyszerű adatelem vagy ismétlődő adatstruktúra ismételt előfordulásának elválasztójának megadásához. Például általában a karát (^) lesz az ismétléselválasztó. HipAA sémák esetén csak a karát használható. |

### <a name="control-numbers"></a>Vezérlőszámok

![Adja meg, hogyan kezelje a vezérlőszám-ismétlődéseket](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Csomópont-ellenőrzési szám ismétlődések tiltása |Ismétlődő csomópontok blokkolása. Ellenőrzi a bankközi ellenőrzőszámot (ISA13) a fogadott adatcsere-vezérlőszámhoz. Ha egyezést észlel, a fogadási folyamat nem dolgozza fel a csomópontot. Megadhatja az ellenőrzés végrehajtásához szükséges napok számát, ha minden egyes alkalommal megadja *az Ismétlődő ISA13 ellenőrzése értéket .* |
| Csoportvezérlőszám ismétlődésének leengedése |Ismétlődő csoportvezérlőszámokkal rendelkező csomópontok blokkolása. |
| A Tranzakciókészlet vezérlőszámának duplikált példányai |Az ismétlődő tranzakciókészlet-vezérlőszámokkal rendelkező csomópontok blokkolása. |

### <a name="validation"></a>Ellenőrzés

![A fogadott üzenetek érvényesítési tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Az egyes ellenőrzési sorok befejezésekor a program automatikusan hozzáad egy másikat. Ha nem ad meg szabályokat, akkor az érvényesítés az "Alapértelmezett" sort használja.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet típusa |Válassza ki az EDI-üzenet típusát. |
| EDI érvényesítése |Edi-érvényesítést végezhet a séma EDI tulajdonságai, hosszkorlátozásai, üres adatelemei és záró elválasztók által meghatározott adattípusokon. |
| Kiterjesztett érvényesítés |Ha az adattípus nem EDI, az érvényesítés az adatelem követelményén és az engedélyezett ismétlésen, enumeráláson és adatelemhossz-érvényesítésen (min/max) történik. |
| Kezdő/záró nullák engedélyezése |Őrizze meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| Sortávolság/záró nullák vágása |A kezdő vagy záró nulla és szóköz eltávolítása. |
| Záró elválasztó házirend |Záró elválasztók létrehozása. <p>Válassza **a Nem engedélyezett** lehetőséget, ha meg szeretné tiltani a záró határolókat és elválasztókat a fogadott csomópontban. Ha a csomópontnak záró határolói és elválasztói vannak, akkor az áttér és a csomópont érvénytelennek minősül. <p>Válassza **a Választható** lehetőséget a záró határolókkal és elválasztókkal rendelkező vagy anélkül eső csomópontok fogadásához. <p>Válassza a **Kötelező lehetőséget,** ha a csomópontnak záró határolókkal és elválasztókkal kell rendelkeznie. |

### <a name="internal-settings"></a>Belső beállítások

![Belső beállítások kiválasztása](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Implikált decimális formátum átalakítása "Nn" alap 10 numerikus értékké |Az "Nn" formátummal megadott EDI-számot alap-10 numerikus értékké alakítja. |
| Üres XML-címkék létrehozása, ha a záró elválasztók engedélyezettek |Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy a csomópont-küldő üres XML-címkéket tartalmazzon a záró elválasztókhoz. |
| Csomópont felosztása tranzakciókészletekként - tranzakciókészletek felfüggesztése hiba esetén|A tranzakciókészletben beállított tranzakciókat külön XML-dokumentumba elemzi a megfelelő boríték nak a tranzakciókészletre való alkalmazásával. Csak azokat a tranzakciókat függeszti fel, amelyekben az ellenőrzés sikertelen. |
| Csomópont felosztása tranzakciókészletekként - a csomópont felfüggesztése hiba esetén|A megfelelő boríték alkalmazásával elemzi a csomópontban beállított tranzakciókat egy külön XML-dokumentumba. A teljes adatcserét felfüggeszti, ha a csomópont egy vagy több tranzakciókészlete nem érvényesíti az ellenőrzést. | 
| Adatcsere megőrzése - tranzakciókészletek felfüggesztése hiba esetén |Érintetlenül hagyja a csomópontot, létrehoz egy XML-dokumentumot a teljes kötegelt csomóponthoz. Csak azokat a tranzakciókészleteket függeszti fel, amelyek nem érvényesítése sikertelen, miközben folytatja az összes többi tranzakciókészlet feldolgozását. |
| Csomópont megőrzése - a csomópont felfüggesztése hiba esetén |Érintetlenül hagyja a csomópontot, létrehoz egy XML-dokumentumot a teljes kötegelt csomóponthoz. Felfüggeszti a teljes adatcserét, ha a csomópont egy vagy több tranzakciókészlete sikertelen az érvényesítésben. |

## <a name="configure-how-your-agreement-sends-messages"></a>A szerződés üzenetküldési módjának beállítása

Beállíthatja, hogy ez a szerződés hogyan azonosítsa és kezelje a partnerének a jelen szerződés keretében küldött kimenő üzeneteket.

1.  A **Hozzáadás**csoportban válassza a **Küldési beállítások lehetőséget.**
Konfigurálja ezeket a tulajdonságokat az Önnel üzeneteket cserélő partnerével kötött megállapodás alapján. A tulajdonságleírásokat lásd az ebben a szakaszban található táblázatokban.

    **A Küldési beállítások** a következő szakaszokba vannak rendezve: azonosítók, nyugtázás, sémák, borítékok, karakterkészletek és elválasztók, vezérlőszámok és érvényesítés.

2. Miután elkészült, az **OK**gombra választva mentse a beállításokat.

Most már a megállapodás készen áll a kiválasztott beállításoknak megfelelő kimenő üzenetek kezelésére.

### <a name="identifiers"></a>Azonosítók

![Azonosító tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Engedélyezési minősítő (ISA1) |Válassza ki az Engedélyezési minősítő értékét a legördülő listából. |
| ISA2 (ISA2) |Adja meg az engedélyezési adatok értékét. Ha ez az érték nem 00, akkor adjon meg legalább egy alfanumerikus karaktert, és legfeljebb 10-et. |
| Biztonsági minősítő (ISA3) |Válassza ki a Biztonsági minősítő értékét a legördülő listából. |
| ISA4 |Adja meg a Biztonsági adatok értékét. Ha ez az érték nem 00, akkor az Érték (ISA4) mezőben adjon meg legalább egy alfanumerikus értéket és legfeljebb 10 értéket. |

### <a name="acknowledgment"></a>Elismerés

![Nyugtázási tulajdonságok beállítása](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Tulajdonság | Leírás |
| --- | --- |
| TA1-re kell számítani |Technikai nyugtázás (TA1) visszaküldése a csomópont küldőjének. Ezzel a beállítással megadhatja, hogy az üzenetet küldő gazdapartner nyugtát kér a megállapodásvendégpartnertől. Ezeket a nyugtákat a fogadó partner a szerződés fogadási beállításai alapján várja el. |
| BE várható |Funkcionális nyugta (FA) visszaadása a csomópont feladójának. Válassza ki, hogy a 997-es vagy a 999-es nyugtázást a munka sémaverziói alapján szeretné-e kiválasztani. Ezeket a nyugtákat a fogadó partner a szerződés fogadási beállításai alapján várja el. |
| BE verzió |A BE verzió kiválasztása |

### <a name="schemas"></a>Sémák

![A használni kívánt séma kiválasztása](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Verzió |Az X12-es verzió kiválasztása |
| Tranzakció típusa (ST01) |A tranzakciótípus kiválasztása |
| Séma |Válassza ki a használni kívánt sémát. A sémák az integrációs fiókban találhatók. Ha először a sémát választja, az automatikusan konfigurálja a verzió- és tranzakciótípust  |

> [!NOTE]
> Konfigurálja az [integrációs fiókba](../logic-apps/logic-apps-enterprise-integration-accounts.md)feltöltött szükséges [sémát.](../logic-apps/logic-apps-enterprise-integration-schemas.md)

### <a name="envelopes"></a>Borítékok

![Adja meg az elválasztót egy tranzakciókészletben: válassza a Szabványos azonosító vagy az Ismétléselválasztó lehetőséget.](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Tulajdonság | Leírás |
| --- | --- |
| ISA11 használat |Megadja a tranzakciókészletben használandó elválasztót: <p>Válassza **a Szabványos azonosító** lehetőséget, ha a decimális jelöléshez pont (.) van, nem pedig az EDI fogadási folyamatbejövő dokumentumának decimális jelölése. <p>Válassza **az Ismétlés elválasztó** lehetőséget egy egyszerű adatelem vagy ismétlődő adatstruktúra ismételt előfordulásának elválasztójának megadásához. Például általában a karát (^) lesz az ismétléselválasztó. HipAA sémák esetén csak a karát használható. |

### <a name="control-numbers"></a>Vezérlőszámok

![Vezérlőszám tulajdonságainak megadása](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Ellenőrzési verzió száma (ISA12) |Válassza ki az X12 szabvány verzióját |
| Használati mutató (ISA15) |Válassza ki a csomópont környezetét.  Az értékek információk, termelési adatok vagy tesztadatok |
| Séma |GS- és ST-szegmensek létrehozása egy X12 kódolású adatcserében, amelyet a Küldési folyamatnak küld. |
| GS1 |Nem kötelező, válasszon értéket a funkcionális kódhoz a legördülő listából |
| GS2 között |Nem kötelező, alkalmazásküldő |
| GS3 között |Opcionális, alkalmazásfogadó |
| GS4 |Opcionális, válassza a CCYYMMDD vagy YYMMDD |
| GS5 |Nem kötelező, válassza a HHMM, HHMMSS vagy HHMMSSdd |
| GS7 |Nem kötelező, válasszon értéket a felelős ügynökség számára a legördülő listából |
| GS8 között |Nem kötelező, a dokumentum verziója |
| Csomópont-ellenőrzési szám (ISA13) |Kötelező, adja meg az interchange vezérlőszám értéktartományát. Adjon meg egy legalább 1 és legfeljebb 99999999999 9999999 numerikus értéket. |
| Csoportvezérlő száma (GS06) |Kötelező, adja meg a csoportvezérlő szám számtartományát. Adjon meg egy legalább 1 és legfeljebb 99999999999 9999999 numerikus értéket. |
| Tranzakciókészlet vezérlőszáma (ST02) |Kötelező, adja meg a Tranzakciókészlet vezérlőszám számának számtartományát. Adjon meg egy legalább 1 és legfeljebb 99999999999 numerikus értéktartományt. |
| Előtag |Nem kötelező, a nyugtázásban használt tranzakciókészlet-vezérlőszámok tartományára van kijelölve. Adja meg a középső két mező numerikus értékét, és az elő- és utótagmezők alfanumerikus értékét (ha szükséges). A középső mezők kötelezőek, és tartalmazzák a vezérlőszám minimális és maximális értékeit |
| Utótag |Nem kötelező, a nyugtázásban használt tranzakciókészlet-vezérlőszámok tartományára van kijelölve. Adja meg a középső két mező numerikus értékét, és az elő- és utótagmezők alfanumerikus értékét (ha szükséges). A középső mezők kötelezőek, és tartalmazzák a vezérlőszám minimális és maximális értékeit |

### <a name="character-sets-and-separators"></a>Karakterkészletek és elválasztók

A karakterkészleten kívül minden üzenettípushoz más-más határolójelet is megadhat. Ha egy adott üzenetsémához nincs megadva karakterkészlet, akkor a rendszer az alapértelmezett karakterkészletet használja.

![Üzenettípusok határolóinak megadása](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Használandó karakterkészlet |A tulajdonságok érvényesítéséhez jelölje ki az X12 karakterkészletet. A lehetőségek: Basic, Extended és UTF8. |
| Séma |Válasszon ki egy sémát a legördülő listából. Az egyes sorok befejezése után a program automatikusan új sort ad hozzá. A kijelölt sémához válassza ki a használni kívánt elválasztó készletet az alábbi elválasztó leírások alapján. |
| Bevitel típusa |Válasszon egy bemeneti típust a legördülő listából. |
| Komponens elválasztó |Az összetett adatelemek szétválasztásához írjon be egyetlen karaktert. |
| Adatelem-elválasztó |Ha az egyszerű adatelemeket az összetett adatelemeken belül szeretné elválasztani, írjon be egyetlen karaktert. |
| Helyettesítő karakter |A kimenő X12-üzenet létrehozásakor adjon meg egy helyettesítő karaktert, amely a hasznos adat összes elválasztó karakterének cseréjére szolgál. |
| Szegmens terminátor |Az EDI szegmens végét jelző karaktert írjon be. |
| Utótag |Válassza ki a szegmensazonosítóhoz használt karaktert. Ha utótagot jelöl ki, akkor a szegmensterminátor adatelem üres lehet. Ha a szegmensterminátor üresen marad, akkor ki kell jelölnie egy utótagot. |

> [!TIP]
> Különleges karakterértékek megadásához szerkesztse a szerződést JSON-ként, és adja meg a speciális karakter ASCII értékét.

### <a name="validation"></a>Ellenőrzés

![Az üzenetek küldésének érvényesítési tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Az egyes ellenőrzési sorok befejezésekor a program automatikusan hozzáad egy másikat. Ha nem ad meg szabályokat, akkor az érvényesítés az "Alapértelmezett" sort használja.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet típusa |Válassza ki az EDI-üzenet típusát. |
| EDI érvényesítése |Edi-érvényesítést végezhet a séma EDI tulajdonságai, hosszkorlátozásai, üres adatelemei és záró elválasztók által meghatározott adattípusokon. |
| Kiterjesztett érvényesítés |Ha az adattípus nem EDI, az érvényesítés az adatelem követelményén és az engedélyezett ismétlésen, enumeráláson és adatelemhossz-érvényesítésen (min/max) történik. |
| Kezdő/záró nullák engedélyezése |Őrizze meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| Sortávolság/záró nullák vágása |A kezdő vagy záró nulla karakterek eltávolítása. |
| Záró elválasztó házirend |Záró elválasztók létrehozása. <p>Válassza **a Nem engedélyezett** lehetőséget, ha le szeretné tiltani a záró határolókat és elválasztókat az elküldött csomópontban. Ha a csomópontnak záró határolói és elválasztói vannak, akkor az áttér és a csomópont érvénytelennek minősül. <p>Válassza **a Választható** lehetőséget, ha az interpikátorokat záró határolókkal vagy elválasztókkal vagy anélkül szeretné küldeni. <p>Válassza a **Kötelező** lehetőséget, ha az elküldött interklennek záró határolókkal és elválasztókkal kell rendelkeznie. |

## <a name="find-your-created-agreement"></a>A létrehozott szerződés megkeresése

1.  Miután befejezte a szerződés összes tulajdonságának beállítását, a **Hozzáadás** lapon válassza az **OK** gombot a szerződés létrehozásának befejezéséhez és az integrációs fiókhoz való visszatéréshez.

    Az újonnan hozzáadott megállapodás most megjelenik a **Megállapodások** listában.

2.  A megállapodásokat az integrációs fiók áttekintésében is megtekintheti. Az integrációs fiók **menüjében válassza az Áttekintés parancsot,** majd válassza a **Megállapodások csempét.**

    ![Válassza a "Megállapodások" csempe](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az összekötő Swagger-fájljában leírt műveleteket és korlátokat az [összekötő referencialapján](https://docs.microsoft.com/connectors/x12/)találja. 

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
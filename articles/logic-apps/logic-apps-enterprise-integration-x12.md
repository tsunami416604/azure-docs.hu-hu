---
title: X12-üzenetek a B2B-integrációhoz – Azure Logic Apps
description: Az Exchange X12-üzeneteinek összevonása EDI formátumban a VÁLLALATKÖZI vállalati integrációhoz Azure Logic Appsban Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: 8bc5c458240925af1fdd74ebc9b2d4c3db71fb05
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679998"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12-üzenetek a B2B vállalati integrációhoz Azure Logic Appsban Enterprise Integration Pack

Mielőtt X12 üzeneteket Azure Logic Appshoz, létre kell hoznia egy X12-szerződést, és az integrációs fiókban tárolnia kell a szerződést. Az alábbi lépéseket követve hozhat létre X12-szerződéseket.

> [!NOTE]
> Ez az oldal a Azure Logic Apps X12 funkcióit ismerteti. További információ: [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Előkészületek

Az alábbi elemek szükségesek:

* Az Azure-előfizetéshez már definiált és társított [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md)
* Legalább két, az integrációs fiókban definiált [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) , amely a X12 azonosítóval van konfigurálva az **üzleti identitások** területen    
* Egy szükséges [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , amelyet feltöltheti az integrációs fiókjába

Miután [létrehozott egy integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [partnerek hozzáadásával](logic-apps-enterprise-integration-partners.md)és a használni kívánt [sémával](../logic-apps/logic-apps-enterprise-integration-schemas.md) rendelkezik, a következő lépésekkel hozhat létre X12-szerződést.

## <a name="create-an-x12-agreement"></a>X12-szerződés létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com "Azure Portal"). 

2. Az Azure fő menüjében válassza a **minden szolgáltatás**lehetőséget. 
   A keresőmezőbe írja be az "integráció" kifejezést, majd válassza az **integrációs fiókok**elemet.  

   ![Integrációs fiók megkeresése](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Ha az **összes szolgáltatás** nem jelenik meg, lehetséges, hogy először ki kell bontania a menüt. Az összecsukott menü tetején válassza a **Megjelenítés menü**lehetőséget.

3. Az **integrációs fiókok**területen válassza ki azt az integrációs fiókot, amelyhez hozzá szeretné adni a szerződést.

   ![Válassza ki az integrációs fiókot, ahol létre kívánja hozni a szerződést](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Válassza az **Áttekintés**, majd a **szerződések** csempét. 
   Ha nem rendelkezik szerződések csempével, először adja hozzá a csempét. 

   ![Válassza a "szerződések" csempét](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. A **szerződések**területen válassza a **Hozzáadás**lehetőséget.

   ![Válassza a "Hozzáadás" lehetőséget.](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. A **Hozzáadás**területen adja meg a szerződés **nevét** . 
   A szerződés típusa mezőben válassza a **X12**lehetőséget. 
   Válassza ki a **gazdagép partnerét**, a **gazdagép identitását**, a **vendég partnert**és a **vendég identitását** a szerződéshez. 
   További részletekért tekintse meg az ebben a lépésben található táblázatot.

    ![Szerződés részleteinek megadása](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Tulajdonság | Leírás |
    | --- | --- |
    | Név |A szerződés neve |
    | Szerződés típusa | X12 kell lennie |
    | Gazda partner |Egy szerződéshez a gazdagép és a vendég partner is szükséges. A fogadó partner a szerződést konfiguráló szervezetet jelöli. |
    | Gazdagép identitása |A gazdagép-partner azonosítója |
    | Vendég partner |Egy szerződéshez a gazdagép és a vendég partner is szükséges. A vendég partner a gazda partnerrel üzleti tevékenységet folytató szervezetet jelöli. |
    | Vendég identitás |A vendég partner azonosítója |
    | Fogadási beállítások |Ezek a tulajdonságok a szerződések által fogadott összes üzenetre érvényesek. |
    | Küldési beállítások |Ezek a tulajdonságok a Szerződés által küldött összes üzenetre érvényesek. |  

   > [!NOTE]
   > Az X12-szerződés megoldása függ a küldő minősítőtől és azonosítótól, valamint a partner és a bejövő üzenetben definiált fogadó minősítőtől és azonosítótól. Ha ezek az értékek megváltoznak a partner esetében, frissítse a szerződést is.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Annak konfigurálása, hogy a szerződés hogyan kezelje a fogadott üzeneteket

Most, hogy beállította a szerződés tulajdonságait, beállíthatja, hogy a jelen Szerződés hogyan azonosítsa és kezeli a partnertől érkező bejövő üzeneteket a jelen szerződés alapján.

1.  A **Hozzáadás**területen válassza a **fogadási beállítások**lehetőséget.
Konfigurálja ezeket a tulajdonságokat a partnerrel kötött szerződése alapján, amely üzeneteket cserél Önnel. A tulajdonságok leírását a jelen szakasz tábláiban találja.

    A **fogadási beállítások** a következő csoportokba vannak rendezve: azonosítók, visszaigazolások, sémák, borítékok, ellenőrzési számok, érvényességek és belső beállítások.

2. Ha elkészült, győződjön meg arról, hogy az **OK gombra**kattintva menti a beállításokat.

A szerződés most már készen áll a kiválasztott beállításoknak megfelelő bejövő üzenetek kezelésére.

### <a name="identifiers"></a>Azonosítók

![Azonosító tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Tulajdonság | Leírás |
| --- | --- |
| ISA1 (engedélyezési minősítő) |Válassza ki az engedélyezési minősítő értékét a legördülő listából. |
| ISA2 |Választható. Adja meg az engedélyezési információ értékét. Ha a ISA1 megadott érték nem a 00, adjon meg legalább egy alfanumerikus karaktert és legfeljebb 10 értéket. |
| ISA3 (biztonsági minősítő) |Válassza ki a biztonsági minősítő értékét a legördülő listából. |
| ISA4 |Választható. Adja meg a biztonsági információ értékét. Ha a ISA3 megadott érték nem a 00, adjon meg legalább egy alfanumerikus karaktert és legfeljebb 10 értéket. |

### <a name="acknowledgment"></a>Tudomásul vétele

![Nyugtázási tulajdonságok beállítása](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Várt TA1 |Technikai nyugtát ad vissza az adatcsere küldőjének |
| A FA várható |Funkcionális visszaigazolást ad vissza az adatcsere feladójának. Ezután válassza ki, hogy a 997-es vagy az 999-es nyugtát szeretné-e használni a séma verziószáma alapján. |
| AK2/IK2 hurok belefoglalása |Lehetővé teszi a AK2-hurkok létrehozását a funkcionális visszaigazolásokban az elfogadott tranzakciótípusok esetében |

### <a name="schemas"></a>Sémák

Válasszon sémát az egyes tranzakciótípusok (ST1-EK) és a küldő alkalmazások (GS2) számára. A fogadási folyamat kibontja a bejövő üzenetet úgy, hogy az itt megadott értékekkel egyezteti a bejövő üzenet ST1 és GS2 értékeit, valamint a beérkező üzenet sémáját az itt beállított sémával.

![Séma kiválasztása](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Verzió |Válassza ki a X12 verzióját |
| Tranzakció típusa (ST01) |Válassza ki a tranzakció típusát |
| Küldő alkalmazás (GS02) |Feladó alkalmazás kiválasztása |
| Séma |Válassza ki a használni kívánt sémafájl-fájlt. A sémák bekerülnek az integrációs fiókjába. |

> [!NOTE]
> Konfigurálja az [integrációs fiókba](../logic-apps/logic-apps-enterprise-integration-accounts.md)feltöltött szükséges [sémát](../logic-apps/logic-apps-enterprise-integration-schemas.md) .

### <a name="envelopes"></a>Borítékoknak

![Adja meg az elválasztót egy tranzakciónaplóban: válassza a standard azonosítót vagy a ismétlődési elválasztó elemet.](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Tulajdonság | Leírás |
| --- | --- |
| ISA11-használat |Meghatározza a tranzakciónaplóban használandó elválasztó karaktert: <p>Válassza a **szabványos azonosító** lehetőséget, hogy egy pontot (.) használjon decimális jelöléshez, nem pedig a bejövő dokumentum decimális jelölését az EDI fogadási folyamatában. <p>Válassza a **ismétlő elválasztó** lehetőséget egy egyszerű adatelem vagy ismétlődő adatstruktúra ismétlődő előfordulásainak megadásához. Például általában a Carat (^) van használatban ismétlődési elválasztóként. HIPAA sémák esetében csak a Carat használatát használhatja. |

### <a name="control-numbers"></a>Vezérlőelemek száma

![Válassza ki, hogyan szeretné kezelni a vezérlési szám ismétlődéseit](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Az adatcsere-vezérlési szám másodpéldányának letiltása |Duplikált változások blokkolása. Ellenőrzi a beérkező adatcsere-vezérlő számát (ISA13). Ha a rendszer egyezést észlel, a fogadási folyamat nem dolgozza fel az adatcserét. Megadhatja, hogy az ellenőrzés végrehajtásához hány nap elteltével adjon meg egy értéket az *ismétlődő ISA13 (nap) ellenőrzéséhez*. |
| Csoport vezérlőelem-szám másodpéldányának letiltása |Duplikált csoport típusú vezérlőelemekkel rendelkező adatmódosítások letiltása. |
| A tranzakciónapló-vezérlők számának duplikálása nem engedélyezett |Duplikált tranzakciós csoport vezérlőelem-számokkal rendelkező változások blokkolása. |

### <a name="validations"></a>Érvényesítések

![Fogadott üzenetek érvényesítési tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Az egyes ellenőrzési sorok elvégzése után a rendszer automatikusan hozzáadja a másikat. Ha nem ad meg szabályokat, az érvényesítés az "alapértelmezett" sort használja.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet típusa |Válassza ki az EDI-üzenet típusát. |
| EDI-ellenőrzés |A séma EDI-tulajdonságai, a hossz-korlátozások, az üres adatelemek és a záró elválasztók által definiált adattípusokra vonatkozó EDI-érvényesítés végrehajtása. |
| Kiterjesztett ellenőrzés |Ha az adattípus nem EDI, az érvényesítés az adatelem követelménye és az engedélyezett ismétlődés, enumerálás és adatelem hossza ellenőrzés (min/max) alapján történik. |
| Kezdő/záró nullák engedélyezése |Tartsa meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| A kezdő/záró nullák körülvágása |A kezdő vagy záró nulla és a szóköz karakter eltávolítása. |
| Záró elválasztó házirend |Záró elválasztók előállítása. <p>Válassza a **nem engedélyezett** lehetőséget a záró határolójelek és elválasztók tiltásához a fogadott adatcserében. Ha a csomópont záró határolójeleket és elválasztó karaktereket tartalmaz, a rendszer érvénytelenként deklarálja a csomópontot. <p>Válassza a **választható** lehetőséget, ha el szeretné fogadni a módosításokat a záró határolójelekkel és elválasztókkal együtt vagy anélkül. <p>Válassza a **kötelező** lehetőséget, ha a csomópontnak záró határolójelekkel és elválasztókkal kell rendelkeznie. |

### <a name="internal-settings"></a>Belső beállítások

![Belső beállítások kiválasztása](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Tulajdonság | Leírás |
| --- | --- |
| A feltételes decimális formátum "nn" értékének konvertálása egy 10 számértékre |Egy "nn" formátumú, alap-10 numerikus értékre alakítja át az "nn" formátummal megadott EDI-számot. |
| Üres XML-címkék létrehozása, ha a záró elválasztók engedélyezettek |Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy az adatcsere küldője üres XML-címkéket tartalmazzon a záró elválasztók számára. |
| Adatcsere felosztása tranzakciónaplóként – hiba esetén a tranzakciós készletek felfüggesztése|A rendszer az adatcsere során beállított összes tranzakciót egy külön XML-dokumentumba elemezve a megfelelő borítékot alkalmazza a tranzakciós készletre. Csak azokat a tranzakciókat felfüggeszti, amelyekben az ellenőrzés sikertelen. |
| Adatcsere felosztása tranzakciós készletként – hiba miatti adatcsere felfüggesztése|A megfelelő boríték alkalmazásával elemzi az egyes csomópontokon beállított összes tranzakciót egy külön XML-dokumentumba. Felfüggeszti a teljes áttelepítést, ha egy vagy több tranzakció-készlet nem sikerül érvényesítést hajt végre. | 
| Adatcsere megőrzése – tranzakciók felfüggesztése hiba esetén |Érintetlenül hagyja az adatcserét, egy XML-dokumentumot hoz létre a teljes kötegelt átváltáshoz. Csak azokat a tranzakciós készleteket felfüggeszti, amelyek sikertelenek az ellenőrzés során, miközben folytatja az összes többi tranzakció feldolgozását. |
| Adatcsere megőrzése – az adatcsere felfüggesztése hiba esetén |Érintetlenül hagyja az adatcserét, egy XML-dokumentumot hoz létre a teljes kötegelt átváltáshoz. Felfüggeszti a teljes adatcsomópontot, ha egy vagy több tranzakciós készlet nem sikerül érvényesítést hajt végre. |

## <a name="configure-how-your-agreement-sends-messages"></a>Annak konfigurálása, hogy a szerződés hogyan küldjön üzeneteket

Beállíthatja, hogy a jelen Szerződés hogyan azonosítsa és kezeli a partnernek e szerződés keretében küldött kimenő üzeneteket.

1.  A **Hozzáadás**területen válassza a **küldési beállítások**lehetőséget.
Konfigurálja ezeket a tulajdonságokat a partnerrel kötött szerződése alapján, akik üzeneteket cserélnek Önnel. A tulajdonságok leírását a jelen szakasz tábláiban találja.

    A **küldési beállítások** a következő részekre vannak rendezve: azonosítók, visszaigazolások, sémák, borítékok, karakterkészletek és elválasztók, vezérlőelemek száma és érvényesítése.

2. Ha elkészült, győződjön meg arról, hogy az **OK gombra**kattintva menti a beállításokat.

A szerződés most már készen áll a kiválasztott beállításoknak megfelelő kimenő üzenetek kezelésére.

### <a name="identifiers"></a>Azonosítók

![Azonosító tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Engedélyezési minősítő (ISA1) |Válassza ki az engedélyezési minősítő értékét a legördülő listából. |
| ISA2 |Adja meg az engedélyezési információ értékét. Ha ez az érték nem 00, akkor adjon meg legalább egy alfanumerikus karaktert és legfeljebb 10 értéket. |
| Biztonsági minősítő (ISA3) |Válassza ki a biztonsági minősítő értékét a legördülő listából. |
| ISA4 |Adja meg a biztonsági információ értékét. Ha ez az érték nem a 00, az érték (ISA4) szövegmezőnél adjon meg legalább egy alfanumerikus értéket és legfeljebb 10 értéket. |

### <a name="acknowledgment"></a>Tudomásul vétele

![Nyugtázási tulajdonságok beállítása](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Várt TA1 |Technikai nyugtázás (TA1) visszaküldése a bankközi feladónak. Ez a beállítás azt határozza meg, hogy az üzenetet küldő gazda partner visszaigazolást kér a szerződésben szereplő vendég partnertől. Ezeket a nyugtákat a fogadó partner a szerződés fogadási beállításai alapján várta. |
| A FA várható |Funkcionális nyugtázás (FA) visszaadása az adatcsere küldőjének. Válassza ki, hogy a 997-es vagy az 999-es nyugtát szeretné-e használni a séma azon verziói alapján, amelyeken dolgozik. Ezeket a nyugtákat a fogadó partner a szerződés fogadási beállításai alapján várta. |
| FA verziója |Válassza ki a be verziót |

### <a name="schemas"></a>Sémák

![Válassza ki a használni kívánt sémát](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Verzió |Válassza ki a X12 verzióját |
| Tranzakció típusa (ST01) |Válassza ki a tranzakció típusát |
| SÉMA |Válassza ki a használni kívánt sémát. A sémák az integrációs fiókban találhatók. Ha először a séma lehetőséget választja, a automatikusan konfigurálja a verziót és a tranzakció típusát  |

> [!NOTE]
> Konfigurálja az [integrációs fiókba](../logic-apps/logic-apps-enterprise-integration-accounts.md)feltöltött szükséges [sémát](../logic-apps/logic-apps-enterprise-integration-schemas.md) .

### <a name="envelopes"></a>Borítékoknak

![Adja meg az elválasztót egy tranzakciónaplóban: válassza a standard azonosítót vagy a ismétlődési elválasztó elemet.](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Tulajdonság | Leírás |
| --- | --- |
| ISA11-használat |Meghatározza a tranzakciónaplóban használandó elválasztó karaktert: <p>Válassza a **szabványos azonosító** lehetőséget, hogy egy pontot (.) használjon decimális jelöléshez, nem pedig a bejövő dokumentum decimális jelölését az EDI fogadási folyamatában. <p>Válassza a **ismétlő elválasztó** lehetőséget egy egyszerű adatelem vagy ismétlődő adatstruktúra ismétlődő előfordulásainak megadásához. Például általában a Carat (^) van használatban ismétlődési elválasztóként. HIPAA sémák esetében csak a Carat használatát használhatja. |

### <a name="control-numbers"></a>Vezérlőelemek száma

![Vezérlőelem számának tulajdonságainak megadása](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Vezérlő verziószáma (ISA12) |Válassza ki a X12 standard verzióját |
| Használati jelző (ISA15) |Válassza ki a csomópont környezetét.  Az értékek az adatok, a termelési adatok vagy a tesztelési adatok. |
| Séma |A GS és a ST szegmenseket hozza létre egy X12-kódolású adatcsere számára, amelyet a küldési folyamatnak küld. |
| GS1 |Nem kötelező, válassza ki a funkcionális kód értékét a legördülő listából. |
| GS2 |Nem kötelező, az alkalmazás küldője |
| GS3 |Opcionális, alkalmazás-fogadó |
| GS4 |Nem kötelező, válassza a CCYYMMDD vagy a ÉÉHHNN lehetőséget. |
| GS5 |Nem kötelező, válassza a óópp, a HHMMSS vagy a HHMMSSdd lehetőséget. |
| GS7 |Nem kötelező, válasszon egy értéket a felelős ügynökség számára a legördülő listából. |
| GS8 |A dokumentum verziója nem kötelező |
| Adatcsere-vezérlési szám (ISA13) |Kötelező megadni az adatcsere-vezérlő számának tartományát. Adjon meg egy numerikus értéket legalább 1 értékkel, legfeljebb 999999999 |
| Csoport vezérlőelem száma (GS06) |Kötelező megadni a csoport vezérlőelem számának tartományát. Adjon meg egy numerikus értéket legalább 1 értékkel, legfeljebb 999999999 |
| Tranzakciónapló-vezérlő száma (ST02) |Kötelező, adja meg a tranzakciónapló-vezérlő számának tartományát. Adja meg a numerikus értékek tartományát legalább 1 értékkel, legfeljebb 999999999 |
| Körzetszám |Nem kötelező, amelyet a rendszer a visszaigazoláshoz használt tranzakciótípusok vezérlőelem-számok tartományához jelöl. Adjon meg egy numerikus értéket a középső két mezőhöz, valamint egy alfanumerikus értéket (ha szükséges) az előtag és az utótag mezőhöz. A középső mezőket kötelező megadni, és tartalmaznia kell a vezérlő számának minimális és maximális értékét. |
| Utótag |Nem kötelező, amelyet a rendszer a visszaigazolásban használt tranzakciótípusok vezérlőelem-számok tartományához jelöl. Adjon meg egy numerikus értéket a középső két mezőhöz, és egy alfanumerikus értéket (ha szükséges) az előtag és az utótag mezőhöz. A középső mezőket kötelező megadni, és tartalmaznia kell a vezérlő számának minimális és maximális értékét. |

### <a name="character-sets-and-separators"></a>Karakterkészletek és elválasztók

A karakterkészleten kívül más határolójeleket is megadhat az egyes üzenetek típusaihoz. Ha egy karakterkészlet nincs megadva egy adott üzenet sémához, akkor a rendszer az alapértelmezett karakterkészletet használja.

![Határolójelek megadása az üzenetek típusaihoz](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Használandó karakterkészlet |A tulajdonságok érvényesítéséhez válassza ki a X12 karakterkészletet. A lehetőségek az alapszintű, a kiterjesztett és az UTF8. |
| Séma |Válasszon ki egy sémát a legördülő listából. Az egyes sorok elvégzése után automatikusan létrejön egy új sor. A kiválasztott séma esetében válassza ki a használni kívánt elválasztó készletet az alábbi elválasztó leírások alapján. |
| Bemenet típusa |Válasszon egy bemeneti típust a legördülő listából. |
| Összetevő-elválasztó |Az összetett adatelemek elkülönítéséhez adjon meg egyetlen karaktert. |
| Adatelem-elválasztó |Az összetett adatelemeken belüli egyszerű adatelemek elkülönítéséhez adjon meg egyetlen karaktert. |
| Helyettesítő karakter |Adja meg azt a helyettesítő karaktert, amellyel a rendszer az összes elválasztó karaktert lecseréli a hasznos adatokhoz a kimenő X12 üzenet létrehozásakor. |
| Szegmens vége |Egy EDI-szegmens végének jelzéséhez adjon meg egy karaktert. |
| Utótag |Válassza ki a szegmens azonosítójával használt karaktert. Ha kijelöl egy utótagot, a szegmens lezáró adatelem üres is lehet. Ha a szegmens lezáró üresen marad, akkor meg kell jelölnie egy utótagot. |

> [!TIP]
> Speciális karakteres értékek biztosításához szerkessze a szerződést JSON-ként, és adja meg a speciális karakter ASCII-értékét.

### <a name="validation"></a>Ellenőrzés

![Az üzenetek küldéséhez szükséges ellenőrzési tulajdonságok megadása](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Az egyes ellenőrzési sorok elvégzése után a rendszer automatikusan hozzáadja a másikat. Ha nem ad meg szabályokat, az érvényesítés az "alapértelmezett" sort használja.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet típusa |Válassza ki az EDI-üzenet típusát. |
| EDI-ellenőrzés |A séma EDI-tulajdonságai, a hossz-korlátozások, az üres adatelemek és a záró elválasztók által definiált adattípusokra vonatkozó EDI-érvényesítés végrehajtása. |
| Kiterjesztett ellenőrzés |Ha az adattípus nem EDI, az érvényesítés az adatelem követelménye és az engedélyezett ismétlődés, enumerálás és adatelem hossza ellenőrzés (min/max) alapján történik. |
| Kezdő/záró nullák engedélyezése |Tartsa meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| A kezdő/záró nullák körülvágása |A kezdő vagy záró nulla karakterek eltávolítása. |
| Záró elválasztó házirend |Záró elválasztók előállítása. <p>Válassza a **nem engedélyezett** lehetőséget a záró elválasztók és elválasztók tiltásához az eljuttatott adatcserében. Ha a csomópont záró határolójeleket és elválasztó karaktereket tartalmaz, a rendszer érvénytelenként deklarálja a csomópontot. <p>Válassza a **választható** lehetőséget, ha az egymás utáni határolójeleket és elválasztókat is el szeretné küldeni. <p>Válassza a **kötelező** lehetőséget, ha az eljuttatott adatcsere záró határolójelekkel és elválasztókkal kell rendelkeznie. |

## <a name="find-your-created-agreement"></a>A létrehozott szerződés megkeresése

1.  Miután befejezte az összes szerződési tulajdonság beállítását, a **Hozzáadás** lapon kattintson az **OK** gombra a szerződés létrehozásához és az integrációs fiókhoz való visszatéréshez.

    Az újonnan hozzáadott szerződés most megjelenik a **szerződések** listájában.

2.  A szerződéseit az integrációs fiók áttekintésében is megtekintheti. Az integrációs fiók menüben válassza az **Áttekintés**, majd a **szerződések** csempét.

    ![Válassza a "szerződések" csempét](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>A hencegés megtekintése
Tekintse meg a [hencegés részleteit](/connectors/x12/). 

## <a name="learn-more"></a>További információ
* [További információ a Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Tudnivalók a Enterprise Integration Pack")  


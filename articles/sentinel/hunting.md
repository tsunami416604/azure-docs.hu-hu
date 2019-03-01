---
title: Vadászat képességek az Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan használható az Azure-Sentinel vadászat képességeket.
services: sentinel
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b03221802d5727c333cfc9a6927abb2eafca1a0d
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992956"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Az Azure-on Előzetesben Sentinel-fenyegetések Hunt

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha Ön egy vizsgáló kívánó proaktív szemlélet érvényesítését kapcsolatos biztonsági fenyegetéseket keres, az Azure hatékony Sentinel-hunting egyszerre a szervezet biztonsági fenyegetések hunt keresést és lekérdezéseket eszközöket. Azonban a rendszereket és biztonsági berendezéseket létrehozhat hegyekbe adatok elemzése és jelentéssel bíró események szűrése nehéz lehet. Segítségével biztonsági elemzők keresse meg proaktív módon, amely szerint a security-alkalmazásokkal, az Azure-Sentinel nem észlelhető új anomáliák "beépített vadászat lekérdezések vezetheti be a megfelelő kérdések problémák keresése a hálózaton már rendelkezik az adatok. 

Például egy beépített lekérdezési biztosít az infrastruktúra legnagyobb ritka processzorához adatait – nem szeretné egy riasztás kapcsolatos minden alkalommal, amikor futnak, teljes mértékben álcázva lehet, de érdemes vessen egy pillantást a lekérdezést, előfordulhat, hogy lássa, th ere a szokatlan semmit. 



Az Azure-Sentinel vadászat kihasználhatja a következő funkciók:

- Beépített lekérdezéseket: Az első lépésekhez, a kiindulási oldal előre betöltött lekérdezést példákat úgy tervezték, hogy elindult, és megismerkedhet a táblák és a lekérdezési nyelvet biztosít. A Microsoft biztonsági kutatói folyamatosan, hozzáadás, új lekérdezések által fejlesztett beépített vadászat a lekérdezések és finomhangolási meglévő lekérdezi az új észlelési és döntse el, hol kell elkezdeni az vadászat belépési pont tudja biztosítani a új támadások alapjait. 

- Hatékony lekérdezési nyelvet, az IntelliSense használatával: Épülnek, amely lehetővé teszi Önnek kell tennie a vadászat szintre.

- A saját Könyvjelzők létrehozása: A vadászat során egyezés vagy megállapításokat, irányítópultok, illetve, amelyek szokatlan vagy gyanús tevékenységek is származhatnak. Annak érdekében, hogy azok az elemek megjelölni, így is visszatérhet őket a későbbiekben, a könyvjelző funkció használata. A könyvjelzők lehetővé teszi elemek mentése későbbi használatra, a vizsgálati eset létrehozása használandó. A könyvjelzők kapcsolatos további információkért lásd: [vadászat könyvjelzőkön] használatát.

- Notebookok használatával automatizálhatja a vizsgálat: Notebookok hasonlóak a részletes, lépésről lépésre bemutatjuk a vizsgálat és hunt hozhat létre forgatókönyveket.  Notebookok magába foglalja az összes vadászat lépéseit egy újrafelhasználható forgatókönyv, amelyek a szervezet megoszthatók másokkal. 
- A tárolt adatok lekérdezése: Az adatokat a táblákban, hogy a lekérdezés érhető el. Ha például lekérdezheti a folyamat-létrehozás, DNS-események és sok más eseménytípusok.

- Közösségi mutató hivatkozások: A nagyobb Közösség kihasználva további lekérdezések és adatforrások keresésére.
 
## <a name="get-started-hunting"></a>Első lépések vadászat

1. Az Azure-Sentinel-portálon kattintson a **vadászat**.
  ![Az Azure Sentinel elindul vadászat](media/tutorial-hunting/hunting-start.png)

2. Amikor megnyitja a **vadászat** lap összes vadászat lekérdezések egyetlen táblában jelennek meg. A táblázat felsorolja a biztonsági elemzőknek, valamint minden olyan további lekérdezési létrehozása vagy módosítása a Microsoft csapata által írt összes lekérdezés. Egyes lekérdezések milyen azt hunts számára, és milyen típusú adatok futtatott leírását. Ezeket a sablonokat azok különböző üzleti taktika szerint vannak csoportosítva, – a jobb oldalon az ikonok kategorizálása a fenyegetések, például a kezdeti access, az adatmegőrzés és kiszűrése típusát. Szűrheti a vadászat lekérdezés sablonok használatával a mezőket. Minden lekérdezés mentheti a Kedvencek közé. Lekérdezés mentése a Kedvencek közé, amelyet a lekérdezés automatikusan fut minden alkalommal, amikor a **vadászat** lapon érhető el. Hozzon létre saját vadászat lekérdezés vagy a Klónozás és a egy meglévő vadászat lekérdezés sablon testreszabása. 
 
2. Kattintson a **lekérdezés futtatása** a vadászat a lekérdezés részletei lapon használatával lekérdezéseket futtathat az vadászat oldal elhagyása nélkül.  Az egyezések számát a táblán belül jelenik meg. Tekintse át a vadászat lekérdezések és a találatok listáját. Tekintse meg, melyik szakasza a számítógépes törlési lánc az egyezés társítva.

3. Hajtsa végre az alapul szolgáló lekérdezés át a lekérdezés részleteket tartalmazó ablaktáblán, vagy kattintson a **lekérdezés eredményének megtekintéséhez** , nyissa meg a lekérdezés a Log Analyticsben. A lap alján tekintse át a lekérdezés egyezéseket.

4.  Kattintson a sort, majd válassza a **Hozzáadás könyvjelző** kell vizsgálni – a sorokat adhat hozzá ehhez, amelyeket gyanúsnak tűnik. 

5. Ezután térjen vissza a fő **vadászat** lapon, majd kattintson a **könyvjelzők** lapján megtekintheti a gyanús tevékenységeket. 

6. Jelöljön ki egy könyvjelzőt, és kattintson a **vizsgálat** a hibavizsgálati felület megnyitásához. A könyvjelzők szűrheti. Például ha kampány lekérdezéskapcsolatokról, is létrehoz egy címkét a kampány és szűrjön rá a kampány alapján minden könyvjelző.

1. Miután felderített, mely vadászat lekérdezés lehetséges támadások értékes betekintést nyújt, egyéni észlelési szabályok alapján a lekérdezést, és felszíni információkat riasztásai a biztonsági incidens válaszadók is létrehozhat.

 

## <a name="query-language"></a>Lekérdezés nyelve 

Az Azure-Sentinel vadászat Azure Log Analytics lekérdezési nyelv alapján történik. A lekérdezési nyelvet és a támogatott operátorok további információkért lásd: [lekérdezési nyelv leírása](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Nyilvános vadászat lekérdezés GitHub-adattár

Tekintse meg a [vadászat lekérdezés tárház](https://github.com/Azure/Orion). Járul hozzá, és a felhasználók által megosztott példalekérdezés használja.

 

## <a name="sample-query"></a>Mintalekérdezés

Egy tipikus lekérdezés operátorok elválasztott sorozatát követ táblanév előtaggal kezdődik \|.

Kezdő a táblát a fenti példában a nevet a SecurityEvent, és szükség szerint adja hozzá a védőeszközön elemeket.

1. Tekintse át az előző hét nap csak rekordokat az idő szűrő megadásához.

2. Szűrő hozzáadása csak az 4688-as Azonosítójú jeleníti meg a lekérdezésben.

3. Szűrő hozzáadása a parancssor cscript.exe csak egy példányát tartalmazza a lekérdezésben.

4. Csak az oszlopok érdeklik az ismerkedést, és korlátozza az eredményeket 1000 és az kattintson a projektre **lekérdezés futtatása**.
5. Kattintson a zöld háromszögnek, és futtassa a lekérdezést. A lekérdezés teszteléséhez, és futtassa a rendellenes viselkedés kereséséhez.

## <a name="useful-operators"></a>Hasznos operátorok

A lekérdezési nyelv hatékony, és a számos elérhető operátorok rendelkezik, néhány hasznos operátorok itt találhatók:

**ahol** -részhalmazát alkotják, egy predikátum megfelelő sorokat a tábla szűrése.

**Összegzés** -előállít egy táblát, amely a bemeneti tábla tartalmának összesíti.

**Csatlakozás** – az új tábla kialakításához egyező értékek, az egyes táblákból a megadott oszlopoknak alapján két tábla sorainak egyesítése.

**száma** – a bemeneti rekordhalmazhoz a rekordok számának visszaadása.

**felső** -lépjen vissza az első N rekordok a megadott oszlopok alapján rendezi.

**korlát** – a megadott számú sort adja vissza.

**projekt** – válassza ki az oszlopokat tartalmazzák, nevezze át, vagy dobja el, és új számított oszlop beszúrása.

**kiterjesztheti** – számított oszlopok létrehozása, és azokat az eredményhalmaz bővítése.

**makeset** – a csoport adja vissza, amely a kifejezés különböző értékek tömbje dinamikus (JSON)

**Keresés** – keresse meg a táblák egy készlete közötti predikátum egyező sorok.

## <a name="save-a-query"></a>Lekérdezés mentése

Hozzon létre vagy módosítsa a lekérdezést, és mentse a saját lekérdezését vagy megoszthassa ugyanabban a bérlőben lévő felhasználók számára.

   ![Lekérdezés mentése](./media/tutorial-hunting/save-query.png)

Hozzon létre egy új vadászat lekérdezést:

1. Kattintson a **új lekérdezés** válassza **mentése**.
2. Az üres mezőket kitölteni, és válassza ki **mentése**.

 ![Új lekérdezés](./media/tutorial-hunting/new-query.png)

Klónozza, és módosítsa a meglévő vadászat lekérdezést:

1. Válassza ki a vadászat lekérdezést a módosítani kívánt tábla.
2. Válassza ki a három pontra (...) a lekérdezés szeretné módosítani, és válassza a sor **lekérdezés klónozása**.

 ![lekérdezés klónozása](./media/tutorial-hunting/clone-query.png)
 

3.  Módosítsa a lekérdezést, és válasszon **létrehozás**.

 ![Egyéni lekérdezés](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan futtathat egy vadászat vizsgálat Sentinel-Azure. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:


- [Automatizált vadászat kampányok notebookok használata](notebooks.md)
- [Könyvjelzőkkel mentése közben vadászat hasznos információkat nyerhet ki](bookmarks.md)
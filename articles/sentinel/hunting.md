---
title: Vadászati lehetőségek az Azure Sentinelben| Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan használhatja az Azure Sentinel vadászati képességek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: 54ddf6818b95a4037188ab222501ddfa69b28149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587898"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Az Azure Sentinel segítségével fenyegetésekre vadászik

Ha Ön olyan nyomozó, aki proaktív módon szeretne biztonsági fenyegetéseket keresni, az Azure Sentinel hatékony vadászati kereső- és lekérdezési eszközökkel vadászik a szervezet adatforrásaiban a biztonsági fenyegetésekre. A rendszerek és a biztonsági berendezések azonban olyan adathegyeket hoznak létre, amelyeket nehéz elemezni és értelmes eseményekké szűrni. Annak érdekében, hogy a biztonsági elemzők proaktív módon keressék a biztonsági alkalmazások által nem észlelt új anomáliákat, az Azure Sentinel beépített vadászati lekérdezései segítségével felteheti a megfelelő kérdéseket a hálózaton már meglévő adatokkal kapcsolatos problémák megtalálásához. 

Egy beépített lekérdezés például adatokat szolgáltat az infrastruktúrán futó legritkább folyamatokról – nem szeretne riasztást kapni minden egyes futtatáskor, lehet, hogy teljesen ártatlan, de érdemes időnként megnézni a lekérdezést, hogy Van valami szokatlan. 



Az Azure Sentinel-vadászattal a következő lehetőségeket használhatja ki:

- Beépített lekérdezések: A kezdéshez a kezdőlap előre betöltött lekérdezési példákat tartalmaz, amelyek célja a kezdés, és megismerheti kitaszela a táblákat és a lekérdezés nyelvét. Ezeket a beépített vadászati lekérdezéseket a Microsoft biztonsági kutatói folyamatosan fejlesztik, új lekérdezéseket adnak hozzá, és finomhangolják a meglévő lekérdezéseket, hogy egy belépési pontot biztosítsanak az új észlelések kereséséhez és annak kiderítéséhez, hogy hol kezdjenek vadászni a új támadások kezdeteit. 

- Hatékony lekérdezési nyelv az IntelliSense segítségével: Egy olyan lekérdezési nyelvre épül, amely biztosítja azt a rugalmasságot, amely a vadászathoz a következő szintre emeli a vadászatot.

- Készítse el saját könyvjelzőit: A vadászati folyamat során szokatlannak vagy gyanúsnak tűnő találatokkal vagy találatokkal, irányítópultokkal vagy tevékenységekkel találkozhat. Annak érdekében, hogy megjelölje ezeket az elemeket, hogy a jövőben visszatérhessen hozzájuk, használja a könyvjelző funkciót. A könyvjelzők lehetővé teszik az elemek későbbi mentését, hogy egy incidenst hozzanak létre a vizsgálathoz. A könyvjelzőkről a [Könyvjelzők használata vadászatkor című témakörben](hunting.md)talál további információt.
- Jegyzetfüzetek használatával automatizálhatja a vizsgálatot: A jegyzetfüzetek olyan, mint a részletes forgatókönyvek, amelyeket a vizsgálat és a vadászat lépésein keresztül hozhat létre.  A jegyzetfüzetek az összes vadászati lépést egy újrafelhasználható forgatókönyvbe foglalják, amely megosztható a szervezet többi tagja számára. 
- A tárolt adatok lekérdezése: Az adatok a táblákban is elérhetők a lekérdezéshez. Lekérdezheti például a folyamat létrehozását, a DNS-eseményeket és sok más eseménytípust.

- Közösségi hivatkozások: Használja ki a nagyobb közösség erejét további lekérdezések és adatforrások megkereséséhez.
 
## <a name="get-started-hunting"></a>Vadászat megkezdése

1. Az Azure Sentinel portálon kattintson a **Vadászat gombra.**
  ![Az Azure Sentinel elkezd vadászni](media/tutorial-hunting/hunting-start.png)

2. A **Vadászat** oldal megnyitásakor az összes vadászati lekérdezés egyetlen táblában jelenik meg. A táblázat felsorolja a Microsoft biztonsági elemzőinek csapata által írt összes lekérdezést, valamint a létrehozott vagy módosított további lekérdezéseket. Minden lekérdezés leírást ad arról, hogy mire vadászik, és milyen típusú adatokon fut. Ezek a sablonok vannak csoportosítva a különböző taktika - az ikonok a jobb kategorizálni a típusú fenyegetés, mint például a kezdeti hozzáférés, perzisztencia, és a kiszivárgás. Ezeket a vadászlekérdezési sablonokat bármelyik mező vel szűrheti. Bármilyen lekérdezést menthet a kedvencekközé. Ha a lekérdezést a kedvencek közé menti, a lekérdezés automatikusan elindul a **Vadászat** lap minden egyes elérésekor. Létrehozhat saját vadászlekérdezést vagy klónt, és testreszabhat egy meglévő vadászati lekérdezéssablont. 
 
2. Kattintson a **Lekérdezés futtatása** a vadászati lekérdezés részletei lapon bármilyen lekérdezés futtatásához elhagyása nélkül a vadászati oldalon.  Az egyezések száma megjelenik a táblázatban. Tekintse át a vadászati lekérdezések listáját és azok egyezéseit. Nézd meg, hogy a gyilkos lánc melyik szakaszához van társítva a meccs.

3. A lekérdezés részletei ablaktáblában gyorsan áttekintheti az alapul szolgáló lekérdezést, vagy a **Lekérdezés eredményének megtekintése** gombra kattintva nyissa meg a lekérdezést a Log Analytics szolgáltatásban. Alul tekintse át a lekérdezés egyezéseit.

4.  Kattintson a sorra, és válassza a **Könyvjelző hozzáadása** lehetőséget a vizsgálandó sorok hozzáadásához - ezt megteheti minden gyanúsnak tűnő ért. 

5. Ezután lépjen vissza a **fő Vadászat** oldalra, és kattintson a **Könyvjelzők** fülre az összes gyanús tevékenység megtekintéséhez. 

6. Jelöljön ki egy könyvjelzőt, majd kattintson a **Vizsgálat** gombra a vizsgálati élmény megnyitásához. Szűrheti a könyvjelzőket. Ha például egy kampányt vizsgál, létrehozhat egy címkét a kampányhoz, majd szűrheti az összes könyvjelzőt a kampány alapján.

1. Miután felfedezte, hogy melyik vadászati lekérdezés nyújt nagy értékű betekintést a lehetséges támadásokba, egyéni észlelési szabályokat is létrehozhat a lekérdezés alapján, és ezeket az elemzéseket a biztonsági incidensek válaszadóinak riasztásaként is felszínre hozhatja.

 

## <a name="query-language"></a>Lekérdezés nyelve 

Az Azure Sentinel ben való vadászat a Kusto lekérdezési nyelvén alapul. A lekérdezés nyelvéről és a támogatott operátorokról a [Lekérdezésnyelv – referencia című](https://docs.loganalytics.io/docs/Language-Reference/)témakörben talál további információt.

## <a name="public-hunting-query-github-repository"></a>Nyilvános vadászati lekérdezés GitHub-tárház

Nézze meg a [Vadászat lekérdezési tárház](https://github.com/Azure/Orion). Az ügyfeleink által megosztott példalekérdezésekkel járuljon hozzá és használjon.

 

## <a name="sample-query"></a>Mintalekérdezés

Egy tipikus lekérdezés táblanévvel kezdődik, amelyet operátorok \|sorozata követ .

A fenti példában kezdje a tábla neve SecurityEvent és adja vezetékes elemeket, ha szükséges.

1. Adjon meg egy időszűrőt, amely csak az előző hét nap rekordjait tekinti át.

2. Adjon hozzá egy szűrőt a lekérdezéshez, hogy csak a 4688-as eseményazonosítót jelenítse meg.

3. Adjon hozzá egy szűrőt a Parancssor lekérdezéséhez, hogy csak a cscript.exe példányait tartalmazza.

4. Csak azokat az oszlopokat vetítse ki, amelyek feltárása érdekli, és korlátozza az eredményeket 1000-re, és kattintson a **Lekérdezés futtatása gombra.**
5. Kattintson a zöld háromszögre, és futtassa a lekérdezést. Tesztelheti a lekérdezést, és futtathatja, hogy a rendellenes viselkedést keressen.

## <a name="useful-operators"></a>Hasznos operátorok

A lekérdezési nyelv hatékony, és sok rendelkezésre álló operátorok, néhány hasznos operátorok itt felsorolt:

**ahol** - A tábla szűrése a predikátumnak megfelelő sorok részhalmazára.

**összegzés** – Olyan tábla előállítása, amely összesíti a bemeneti tábla tartalmát.

**illesztés** – Két tábla sorainak egyesítése új tábla létrehozásához az egyes táblák megadott oszlopainak értékeivel.

**count** - A bemeneti rekordkészletrekordjainak számát adja vissza.

**top** - Az első N rekordokat adja vissza a megadott oszlopok szerint rendezve.

**limit** - A megadott számú sorig visszaad.

**project** - Jelölje ki a felvenni kívánt oszlopokat, nevezze át vagy ejtse át, és szúrjon be új számított oszlopokat.

**extend** - Hozzon létre számított oszlopokat, és fűzze hozzá őket az eredményhalmazhoz.

**makeet** - A Kif által a csoportban felhozott különböző értékek dinamikus (JSON) tömbjének visszaadása

**find** - olyan sorok keresése, amelyek egyeznek egy predikátumban egy táblakészletben.

## <a name="save-a-query"></a>Lekérdezés mentése

Létrehozhat vagy módosíthat egy lekérdezést, és saját lekérdezésként mentheti, vagy megoszthatja azokat a bérlőkkel azonos környezetben lévő felhasználókkal.

   ![Lekérdezés mentése](./media/tutorial-hunting/save-query.png)

Hozzon létre egy új vadászati lekérdezést:

1. Kattintson **az Új lekérdezés gombra,** és válassza a **Mentés**lehetőséget.
2. Töltse ki az összes üres mezőt, és válassza a **Mentés gombot.**

   ![Új lekérdezés](./media/tutorial-hunting/new-query.png)

Klónozzon és módosítson egy meglévő vadászati lekérdezést:

1. Jelölje ki a módosítani kívánt táblában a vadászati lekérdezést.
2. Jelölje ki a három pontot (...) a módosítani kívánt lekérdezés sorában, és válassza a **Klónozó lekérdezés**lehetőséget.

   ![klónlekérdezés](./media/tutorial-hunting/clone-query.png)
 

3. Módosítsa a lekérdezést, és válassza a **Létrehozás lehetőséget.**

   ![egyéni lekérdezés](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan futtathatja a vadászati vizsgálatot az Azure Sentinel használatával. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:


- [Automatikus vadászati kampányok jegyzetfüzetei használata](notebooks.md)
- [Használjon könyvjelzőket, hogy érdekes információkat mentsen vadászat közben](bookmarks.md)

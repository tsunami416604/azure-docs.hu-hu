---
title: Egyéni mezők az Azure Monitorban (előzetes verzió) | Microsoft dokumentumok
description: Az Azure Monitor Egyéni mezők szolgáltatáslehetővé teszi, hogy saját kereshető mezőket hozzon létre a Log Analytics-munkaterület rekordjaiból, amelyek hozzáadódnak az összegyűjtött rekord tulajdonságaihoz.  Ez a cikk ismerteti az egyéni mező létrehozásának folyamatát, és részletes forgatókönyvet biztosít egy mintaeseményhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: bfb0a73631564c96a4af745fe9d7540a3a84f9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655361"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Egyéni mezők létrehozása a Log Analytics-munkaterületen az Azure Monitorban (előzetes verzió)

> [!NOTE]
> Ez a cikk ismerteti, hogyan elemezheti a szöveges adatokat a Log Analytics-munkaterületen, ahogyan azok gyűjtése történik. Javasoljuk, hogy a szöveges adatokat egy lekérdezésszűrőben elemezze, miután azokat az [Azure Monitor elemzési szöveges adataiban](../log-query/parse-text.md)leírt útmutatás szerint gyűjtöttük. Számos előnye van az egyéni mezők használatával szemben.

Az Azure Monitor **Egyéni mezők** szolgáltatáslehetővé teszi, hogy a meglévő rekordokat a Log Analytics-munkaterületen a saját kereshető mezők hozzáadásával bővítse ki.  Az egyéni mezők automatikusan kitöltődnek az ugyanabban a rekordban lévő más tulajdonságokból kinyert adatokból.

![Áttekintés](media/custom-fields/overview.png)

Az alábbi mintarekord például hasznos adatokat temet el az esemény leírásában. Az adatok külön tulajdonságba történő kibontása lehetővé teszi az ilyen műveletek, például a rendezés és a szűrés számára.

![Mintakivonat](media/custom-fields/sample-extract.png)

> [!NOTE]
> Az előzetes verzióban legfeljebb 100 egyéni mező lehet a munkaterületen.  Ez a korlát kilesz bontva, amikor ez a funkció eléri az általános elérhetőséget.

## <a name="creating-a-custom-field"></a>Egyéni mező létrehozása
Amikor egyéni mezőt hoz létre, a Log Analytics-nek meg kell értenie, hogy mely adatokat használja az érték feltöltéséhez.  A Microsoft Research flashextract nevű technológiáját használja az adatok gyors azonosítására.  Ahelyett, hogy explicit utasításokat kellene megadnia, az Azure Monitor megismeri a megadott példákból kinyerni kívánt adatokat.

A következő szakaszok az egyéni mezők létrehozásának eljárását ismertetik.  A cikk alján egy példa kivonása egy forgatókönyv.

> [!NOTE]
> Az egyéni mező a megadott feltételeknek megfelelő rekordok hozzáadásaként kerül a Log Analytics-munkaterületre, így csak az egyéni mező létrehozása után gyűjtött rekordokon jelenik meg.  Az egyéni mező nem lesz hozzáadva azokhoz a rekordokhoz, amelyek létrehozásukkor már az adattárban vannak.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>1. lépés – Azonosítsa azokat a rekordokat, amelyek az egyéni mezővel rendelkeznek
Az első lépés az egyéni mezőt kapó rekordok azonosítása.  Kezdje egy [szabványos naplólekérdezéssel,](../log-query/log-query-overview.md) majd válasszon ki egy rekordot, amelyből az Azure Monitor tanulni fog.  Ha azt adja meg, hogy az adatokat egyéni mezőbe fogja kinyerni, megnyílik a **Mezőkibontás varázsló,** ahol érvényesítheti és finomíthatja a feltételeket.

1. Nyissa meg **a Naplók lehetőséget,** és lekérdezéssel olvassa be az egyéni mezővel rendelkezni kívánt [rekordokat.](../log-query/log-query-overview.md)
2. Válasszon ki egy rekordot, amelyet a Log Analytics modellként használ az adatok kinyeréséhez az egyéni mező feltöltéséhez.  Azonosítani fogja a rekordból kinyerni kívánt adatokat, és a Log Analytics ezt az információt használja az összes hasonló rekord egyéni mezőjének feltöltéséhez szükséges logika meghatározásához.
3. Bontsa ki a rekord tulajdonságait, kattintson a rekord felső tulajdonságától balra lévő ellipszisre, és válassza a **Mezők kinyerése a alkalmazásból**lehetőséget.
4. Megnyílik a **Mezőkivonás varázsló,** és a kiválasztott rekord megjelenik a **Fő példa** oszlopban.  Az egyéni mező definiálva lesz azokhoz a rekordokhoz, amelyek a kijelölt tulajdonságokban azonos értékekkel rendelkeznek.  
5. Ha a kijelölés nem pontosan az, amit szeretne, a feltételek szűkítéséhez jelöljön ki további mezőket.  A feltételek mezőértékeinek módosításához meg kell szakítania és ki kell választania egy másik rekordot, amely megfelel a kívánt feltételeknek.

### <a name="step-2---perform-initial-extract"></a>2. lépés - Végezze el a kezdeti kivonatot.
Miután azonosította azokat a rekordokat, amelyek rendelkeznek az egyéni mezővel, azonosíthatja a kinyerni kívánt adatokat.  A Log Analytics ezeket az adatokat a hasonló bejegyzésekhasonló mintáinak azonosítására használja.  Ezt követő lépésben ellenőrizheti az eredményeket, és további részleteket adhat meg a Log Analytics elemzéséhez.

1. Jelölje ki azt a szöveget a mintarekordban, amelyet az egyéni mező feltöltésére szeretne feldolgozni.  Ezután megjelenik egy párbeszédpanel, amely megadja a mező nevét és adattípusát, és végrehajtja a kezdeti kivonatot.  A ** \_CF** karakterek automatikusan hozzáfűzésre kerülnek.
2. Kattintson a **Kibontás** gombra az összegyűjtött rekordok elemzéséhez.  
3. Az **Összefoglaló** és **a Keresési eredmények** szakasz a kivonat eredményeit jeleníti meg, így ellenőrizheti annak pontosságát.  **Az összegzés** megjeleníti a rekordok azonosítására használt feltételeket és az egyes azonosított adatértékek számát.  **A Keresési eredmények a** feltételeknek megfelelő rekordok részletes listáját tartalmazza.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>3. lépés - Ellenőrizze a kivonat pontosságát, és hozzon létre egyéni mezőt
Miután végrehajtotta a kezdeti kivonatot, a Log Analytics megjeleníti az eredményeket a már összegyűjtött adatok alapján.  Ha az eredmények pontosnak tűnnek, akkor létrehozhatja az egyéni mezőt további munka nélkül.  Ha nem, akkor finomíthatja az eredményeket, hogy a Log Analytics javíthatja a logikáját.

1. Ha a kezdeti kivonat bármely értéke nem megfelelő, kattintson a **Szerkesztés** ikonra egy pontatlan rekord mellett, és válassza **a Kiemelés módosítása** lehetőséget a kijelölés módosításához.
2. A program a fő **példa**alatti **További példák** szakaszba másolja a bejegyzést.  Itt módosíthatja a kiemelést, hogy a Log Analytics megértse, milyen kijelölést kellett volna választania.
3. Kattintson **a Kibontás gombra,** ha ezt az új információt az összes meglévő rekord kiértékeléséhez használhatja.  Az eredmények módosíthatók az új intelligencia alapján módosított rekordoktól eltérő rekordok esetén.
4. Folytassa a javítások hozzáadását, amíg a kivonat összes rekordja helyesen azonosítja az adatokat az új egyéni mező feltöltéséhez.
5. Kattintson **a Kivonat mentése gombra,** ha elégedett az eredménnyel.  Az egyéni mező most már definiálva van, de még egyetlen rekordhoz sem kerül hozzáadva.
6. Várja meg, amíg a megadott feltételeknek megfelelő új rekordokat összegyűjti, majd futtassa újra a naplókeresést. Az új rekordoknak egyéni mezővel kell rendelkezniük.
7. Használja az egyéni mezőt, mint bármely más rekord tulajdonságot.  Segítségével összesítheti és csoportosíthatja az adatokat, és akár új elemzési adatokat is hozhat létre.

## <a name="viewing-custom-fields"></a>Egyéni mezők megtekintése
A felügyeleti csoport összes egyéni mezőjének listáját az Azure Portalon a Log Analytics-munkaterület **Speciális beállítások** menüjében tekintheti meg.  Válassza **az Adatok,** majd az **Egyéni mezők lehetőséget** a munkaterület összes egyéni mezőjének listájához.  

![Egyéni mezők](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Egyéni mező eltávolítása
Az egyéni mezők kétféleképpen távolíthatók el.  Az első az **Eltávolítás** lehetőség az egyes mezőknél a teljes lista megtekintésekor a fent leírt módon.  A másik módszer egy rekord beolvasása és a mező bal oldalán található gombra való kattintás.  A menüben lehetőség van az egyéni mező eltávolítására.

## <a name="sample-walkthrough"></a>Mintaútmutató
A következő szakasz egy teljes példát mutat be egy egyéni mező létrehozására.  Ez a példa kinyeri a szolgáltatás nevét a Windows-események, amelyek jelzik a szolgáltatás változó állapotát.  Ez a Service Control Manager által a Windows rendszer indításakor létrehozott eseményeken alapul.  Ha követni szeretné ezt a példát, [akkor a Rendszernapló információs eseményeit kell gyűjtenie.](data-sources-windows-events.md)

A következő lekérdezést adjuk meg, hogy visszaadhassuk az összes eseményt a Service Control Manager-től, amely 7036-os eseményazonosítóval rendelkezik, amely egy szolgáltatás indítását vagy leállítását jelző esemény.

![Lekérdezés](media/custom-fields/query.png)

Ezután kiválasztunk és kibővítünk minden rekordot a 7036-os eseményazonosítóval.

![Forrásrekord](media/custom-fields/source-record.png)

Egyéni mezőket úgy határozunk meg, hogy a legfelső tulajdonság melletti ellipszisre kattintunk.

![Mezők kibontása](media/custom-fields/extract-fields.png)

Megnyílik a **Mezőkibontás varázsló,** és az **EventLog** és az **EventID** mezők ki vannak jelölve a **Fő példa** oszlopban.  Ez azt jelzi, hogy az egyéni mező a Rendszernapló 7036-os eseményazonosítójú eseményeihez lesz definiálva.  Ez elegendő, így nem kell más mezőket kijelölnünk.

![Fő példa](media/custom-fields/main-example.png)

Kiemeljük a szolgáltatás nevét a **RenderedDescription** tulajdonságban, és a **Szolgáltatás** segítségével azonosítjuk a szolgáltatás nevét.  Az egyéni mező neve **Service_CF**lesz. A mező típusa ebben az esetben egy karakterlánc, így hagyjuk, hogy változatlan.

![Mező címe](media/custom-fields/field-title.png)

Azt látjuk, hogy a szolgáltatás neve azonosítva megfelelően egyes rekordok, de nem mások számára.   A **keresési eredmények** azt mutatják, hogy a **WMI-teljesítményadapter** nevének egy része nincs kijelölve.  Az **összegzés** azt mutatja, hogy egy rekord azonosította **a Modultelepítőt** a Windows **Modules Installer**helyett.  

![Keresési eredmények](media/custom-fields/search-results-01.png)

Kezdjük a **WMI Performance Adapter** rekord.  Rákattintunk a szerkesztés ikonra, majd **módosítjuk ezt a kiemelést**.  

![Kiemelés módosítása](media/custom-fields/modify-highlight.png)

Növeljük a kiemelést, hogy tartalmazza a **WMI** szót, majd futtassuk újra a kivonatot.  

![További példa](media/custom-fields/additional-example-01.png)

Láthatjuk, hogy a **WMI Performance Adapter** bejegyzéseit kijavították, és a Log Analytics is felhasználta ezeket az információkat a Windows Module Installer rekordjainak **javítására.**

![Keresési eredmények](media/custom-fields/search-results-02.png)

Most már futtathatunk egy lekérdezést, amely ellenőrzi, hogy **Service_CF** jön létre, de még nem került hozzá egyetlen rekordhoz sem. Ennek az az oka, hogy az egyéni mező nem működik a meglévő bejegyzések ellen, ezért meg kell várnunk az új rekordok gyűjtését.

![Kezdeti szám](media/custom-fields/initial-count.png)

Egy idő eltelte után az új események gyűjtése után láthatjuk, hogy a **Service_CF** mező most hozzáadódik a feltételeknek megfelelő rekordokhoz.

![Végeredmények](media/custom-fields/final-results.png)

Most már használhatjuk az egyéni mezőt, mint bármely más rekord tulajdonság.  Ennek illusztrálására létrehozunk egy lekérdezést, amely az új **Service_CF** mező szerint csoportosítja, hogy megvizsgáljuk, mely szolgáltatások a legaktívabbak.

![Csoportosítás lekérdezés szerint](media/custom-fields/query-group.png)

## <a name="next-steps"></a>További lépések
* Információ a [naplólekérdezésekről,](../log-query/log-query-overview.md) amelyek egyéni mezőket használnak a feltételekhez.
* Az egyéni mezők használatával elemezett [egyéni naplófájlok](data-sources-custom-logs.md) figyelése.


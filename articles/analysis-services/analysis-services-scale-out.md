---
title: Az Azure Analysis Services horizontális felskálázás |} A Microsoft Docs
description: Horizontális felskálázás az Azure Analysis Services-kiszolgálók replikálása
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dd89d9645d2054f301ed999121fefc417ea5c6fa
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293906"
---
# <a name="azure-analysis-services-scale-out"></a>Az Azure Analysis Services horizontális felskálázás

Horizontális felskálázás, az ügyfél lekérdezések elosztható több *lekérdezésreplikára* a egy *lekérdezési készlet*, feladatok esetén a válaszidők csökkentése. A lekérdezési készletből feldolgozásához, biztosítva az ügyfél lekérdezések nem negatívan érinti feldolgozási műveletek is egymástól. Horizontális felskálázás az Azure Portalon vagy az Analysis Services REST API használatával konfigurálhatja.

Horizontális felskálázás kiszolgálók a standard tarifacsomagban érhető el. Minden egyes lekérdezés replika költsége megegyezik a kiszolgáló számoljuk fel. Minden lekérdezési replikák és a kiszolgáló ugyanabban a régióban jönnek létre. Az a régió, a kiszolgáló konfigurálható lekérdezési replikák száma korlátozott. További tudnivalókért lásd: [rendelkezésre állása régiónként](analysis-services-overview.md#availability-by-region). Horizontális felskálázás nem növeli a kiszolgáló rendelkezésre álló memória mennyisége. Növelje a memória, váltson magasabb szintű csomagra kell. 

## <a name="why-scale-out"></a>Miért érdemes kibővített?

Egy tipikus server központi telepítés esetén egy kiszolgáló feldolgozó kiszolgáló és a lekérdezés-kiszolgálóként is szolgál. Ha az ügyfél-lekérdezéseket futtassanak a kiszolgálón modellek száma meghaladja a lekérdezés feldolgozása egységek (QPU) csomagot a server, vagy a modell feldolgozása befejeződik, a lekérdezési számítási feladatok egy időben, a teljesítmény csökkenhet. 

Horizontális felskálázás, létrehozhat egy lekérdezési készlet legfeljebb hét további lekérdezési replika erőforrásaival (nyolc összesen, beleértve a *elsődleges* kiszolgáló). A lekérdezési készlet QPU figyelembevételével kritikus időpontokban replikák száma is méretezheti, és elkülönítheti a lekérdezési készletből egy feldolgozó kiszolgáló bármikor. 

Rendelkezik az adott lekérdezési készletben lekérdezési replikák számától függetlenül tranzakciófeldolgozási nem oszlanak meg lekérdezési replikák között. A feldolgozó kiszolgáló az elsődleges kiszolgáló szolgál. Lekérdezési replikák szolgálják, csak a modell olyan adatbázisai között az elsődleges kiszolgáló és a lekérdezési készlet minden egyes replikát szinkronizálja a lekérdezéseket. 

Horizontális felskálázás, ha a lekérdezési készletből növekményes hozzáadni kívánt új lekérdezési replikákra vonatkozó akár öt percet is igénybe vehet. Ha minden új lekérdezési replikák fel, és fut, az új ügyfélkapcsolatokat is a lekérdezési készlet erőforrásainak kell osztani. Meglévő ügyfélkapcsolatok nem változnak, jelenleg csatlakoznak-erőforrásból. Skálázás az, ha bármely meglévő ügyfélkapcsolatok éppen eltávolítják a lekérdezési készlet lekérdezési készlet erőforrás megszűnik. Az ügyfelek egy lekérdezési készlet fennmaradó erőforrást csatlakozhat.

## <a name="how-it-works"></a>Működés

Horizontális felskálázás az első alkalommal konfigurálásakor vannak-e az elsődleges kiszolgálón modell olyan adatbázisai *automatikusan* egy új lekérdezési készlet új replikák szinkronizálva. Automatikus szinkronizálás csak egyszer kerül sor. Automatikus szinkronizálás során az elsődleges kiszolgáló adatfájlok (titkosítása inaktív állapotban a blob storage) egy másik helyen, a blob storage-ban tárolt is titkosítva lesz másolva. A lekérdezési készlet replika van, majd *hidratált* fájlokat a második csoporton származó adatokkal. 

Az automatikus szinkronizálás esetén, horizontális felskálázás egy kiszolgáló először hajt végre, amíg manuális szinkronizálást is elvégezheti. Szinkronizálás biztosítja az adatok a lekérdezési készlet replikákon egyeznek az elsődleges kiszolgáló. Az elsődleges kiszolgálón (frissítése) modellek feldolgozásakor a szinkronizálást kell végrehajtani *után* feldolgozási műveletek befejeződtek. A szinkronizálás frissített adatokat másol az elsődleges kiszolgáló fájlok blob Storage-fájlokat a második csoporton. A lekérdezési készlet replikák majd vannak hidratált fájlok blob storage-ban a második csoporton származó frissített adatokkal. 

Egy későbbi horizontális felskálázási művelet végrehajtása, ha például a lekérdezési készlet két öt, a replikák számának növelése az új replikákat vannak hidratált fájlok blob storage-ban a második csoporton származó adatokkal. Nincs a Nincs szinkronizálás. Ha hajtsa végre a szinkronizálást, a horizontális skálázás, a lekérdezési készletből az új replika után kétszer - redundáns hidratálási hidratált. Egy későbbi horizontális felskálázási művelet végrehajtásakor fontos szem előtt tartani:

* Hajtsa végre a szinkronizálást *a horizontális felskálázási művelet előtt* hozzáadott replikára redundáns hidratálási elkerülése érdekében.

* Ha mindkét feldolgozási automatizálása *és* horizontális felskálázási műveletek, fontos, hogy először dolgozhatja fel az adatokat az elsődleges kiszolgálón, majd hajtsa végre a szinkronizálást, és végezze el a horizontális felskálázási művelet. Ez a sorozat további QPU- és memória-erőforrások csak minimális hatással van.

* Szinkronizálás engedélyezett, akkor is, ha nincs a replikákat a lekérdezési készletben. Egy vagy több replikát az új adatokat az elsődleges kiszolgálón egy feldolgozási műveletből nulláról is kiterjesztése, ha a replika. a lekérdezési készlet először hajtsa végre a szinkronizálást, és majd horizontális felskálázás. Redundáns hidratálási az újonnan hozzáadott replikák szinkronizálása előtt horizontális felskálázás elkerülhető.

* Amikor egy modell adatbázis törlésével az elsődleges kiszolgálóról, akkor nem automatikusan törlődnek a replikákat a lekérdezési készlet. El kell végeznie egy szinkronizálási művelet, amely a fájl/s az adatbázishoz tartozó távolít el a replika megosztott blob tárolási helyét, és ezután törli a modelladatbázisnál, a lekérdezési készlet a replikákon.

* Amikor az elsődleges kiszolgálón egy adatbázis átnevezése, van egy további lépés szükséges az adatbázis megfelelően szinkronizálva a replikákat. Után átnevezése, hajtsa végre a szinkronizálást, adja meg a `-Database` paraméter a régi adatbázis nevével. A szinkronizálás replikákat eltávolítja az adatbázis és a fájlok a régi nevére. Hajtsa végre egy másik szinkronizálási megadása a `-Database` paramétert az új adatbázis nevével. A második szinkronizálás az újonnan elnevezett adatbázist átmásolja a fájlokat a második csoporton és hydrates esetleges replikákat. Ezek a szinkronizálások nem hajtható végre, a portálon a szinkronizálás modell parancs használatával.

### <a name="separate-processing-from-query-pool"></a>A lekérdezési készletből külön feldolgozása

Maximális teljesítményt az feldolgozási és lekérdezési műveleteket is beállíthatja a lekérdezési készletből a feldolgozó kiszolgáló külön. Során elválasztott, csak a lekérdezési készlet lekérdezési replikák meglévő és új kapcsolatok vannak hozzárendelve. Ha feldolgozási műveletek csak igénybe vehetnek egy rövid idő alatt, választhat, a feldolgozó kiszolgáló csak ennyi ideig tart feldolgozás és a szinkronizálási műveletek végrehajtására, majd adja hozzá újra üzembe a lekérdezési készletből, a lekérdezési készletből külön. 

## <a name="monitor-qpu-usage"></a>QPU-használat monitorozása

Annak megállapításához, ha horizontális felskálázás az a kiszolgálóra szükség, figyeli a kiszolgáló Azure Portalon metrikák használatával. A QPU rendszeresen lefoglalja ki, ha az azt jelenti,-lekérdezéseket futtassanak a modellek száma meghaladja a QPU-korlát a terv. A lekérdezési készlet feladat várólista hossza metrika is nő, ha a lekérdezési szál készlet várólistában lévő lekérdezések száma meghaladja a rendelkezésre álló QPU. 

Tekintse meg egy másik jól használható metrikaként átlagos QPU ServerResourceType által. Ez a metrika az elsődleges kiszolgáló, az adott a lekérdezési készletből átlagos QPU hasonlítja össze. 

### <a name="to-configure-qpu-by-serverresourcetype"></a>QPU által ServerResourceType konfigurálása
1. A metrikák grafikont, kattintson **metrika hozzáadása**. 
2. A **erőforrás**, majd válassza ki a kiszolgálót, a **METRIKA NÉVTÉR**válassza **Analysis Services standard mérőszámok**, ezt a **METRIKA**, Válassza ki **QPU**, majd **ÖSSZESÍTÉSI**válassza **átlagos**. 
3. Kattintson a **alkalmazni a felosztás**. 
4. A **értékek**válassza **ServerResourceType**.  

További tudnivalókért lásd: [A kiszolgáló metrikáinak monitorozása](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Horizontális felskálázás konfigurálása

### <a name="in-azure-portal"></a>Az Azure Portalon

1. Kattintson a portál **kibővített**. A csúszka segítségével válassza ki a lekérdezés replika kiszolgálók számát. Replikák úgy dönt, az a szám, a meglévő kiszolgáló mellett.

2. A **a lekérdezési készlettől a feldolgozó kiszolgáló elkülönítése**, jelölje be a feldolgozó kiszolgáló kizárása lekérdezés kiszolgálók igen. Ügyfél [kapcsolatok](#connections) az alapértelmezett kapcsolati karakterlánc használatával (nélkül `:rw`) a lekérdezési készletből a replikákat a rendszer átirányítja. 

   ![Horizontális felskálázás csúszka](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kattintson a **mentése** az új lekérdezés replika kiszolgálók. 

Horizontális felskálázás egy kiszolgáló először konfigurálni, az elsődleges kiszolgálón modellek automatikusan szinkronizálva legyenek a lekérdezési készlet replikák. Automatikus szinkronizálás csak akkor történik meg, miután először konfigurálásakor egy vagy több replikát, kibővített. Az ugyanazon a kiszolgálón a replikák száma érintő későbbi változások *nem vált egy másik automatikus szinkronizálás*. Automatikus szinkronizálás nem kerül sor újra, még akkor is, ha a kiszolgálón a replikák nulla és replikák tetszőleges számú majd újra kibővített. 

## <a name="synchronize"></a>Szinkronizálása 

Szinkronizálási műveleteket kell elvégezni, manuálisan vagy a REST API-val.

### <a name="in-azure-portal"></a>Az Azure Portalon

A **áttekintése** > modell > **Synchronize modell**.

![Horizontális felskálázás csúszka](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

Használja a **szinkronizálási** műveletet.

#### <a name="synchronize-a-model"></a>A modell szinkronizálása   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Szinkronizálási állapotának beolvasása  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell-lel, mielőtt [telepítése vagy frissítése a legújabb Azure PowerShell-modul](/powershell/azure/install-az-ps). 

Szinkronizálási futtatásához használja [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Lekérdezési replikák száma beállításához használja [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Adja meg a választható `-ReadonlyReplicaCount` paraméter.

## <a name="connections"></a>Kapcsolatok

A kiszolgáló áttekintés oldalán a rendszer két kiszolgáló nevét. Ha még nem konfigurálta a kiszolgáló kibővített, mindkét kiszolgáló neve azonos működik. Miután konfigurálja a kiszolgáló kibővített, kell a kapcsolat típusától függően a megfelelő kiszolgáló nevének megadásához. 

Például a Power BI Desktop, Excel és egyéni alkalmazások használatát a végfelhasználói ügyfélkapcsolatok **kiszolgálónév**. 

Az ssms-ben, az SSDT és kapcsolati karakterláncokat a PowerShell, Azure-függvényalkalmazás és az AMO, használjon **felügyeleti kiszolgáló neve**. A felügyeleti kiszolgáló nevét tartalmazza, egy speciális `:rw` (olvasás / írás) minősítője. Az összes feldolgozási műveletei fordulhat elő, a (elsődleges) felügyeleti kiszolgálón.

![Kiszolgáló neve](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Hibaelhárítás

**A probléma leírása:** Felhasználók hibaüzenet **-kiszolgáló nem található "\<a kiszolgáló nevét >" példány "ReadOnly" kapcsolati módban.**

**Megoldás:** Amikor kiválasztja a **a lekérdezési készlettől a feldolgozó kiszolgáló elkülönítése** beállítás, az alapértelmezett kapcsolati karakterlánc használatával Ügyfélkapcsolatok (nélkül `:rw`) lekérdezési készlet replikákat a rendszer átirányítja. A lekérdezési készlet replikák vannak nem még online hogy a szinkronizálás még nem fejeződtek be, ha az átirányított ügyfélkapcsolatok sikertelen lehet. Sikertelen kapcsolatok megelőzése érdekében kell lennie legalább két kiszolgálót a lekérdezési készletből szinkronizálás végrehajtása során. Minden kiszolgálón külön-külön szinkronizálása, míg mások is online maradnak. Ha nem rendelkezik a feldolgozási kiszolgálóval a lekérdezési készlet feldolgozása során, ha szeretné, távolítsa el a készletből feldolgozásra, és adja hozzá azt vissza a készletbe feldolgozás befejeződése után, de a szinkronizálás előtt. A memória és a QPU-metrikák használatával szinkronizálási állapotát figyeli.

## <a name="related-information"></a>Kapcsolódó információk

[A kiszolgáló metrikáinak monitorozása](analysis-services-monitor.md)   
[Azure Analysis Services kezelése](analysis-services-manage.md) 

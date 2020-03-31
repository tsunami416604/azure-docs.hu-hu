---
title: Ellenőrzőpont és a helyreállítási koncepciók visszajátszása az Azure Stream Analytics szolgáltatásban
description: Ez a cikk az Azure Stream Analytics ellenőrzőpont- és újrajátszási fogalmait ismerteti.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f5bb2b97d7da770828c2f4f03167483ad2044c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426395"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Ellenőrzőpont- és visszajátszási fogalmak az Azure Stream Analytics-feladatokban
Ez a cikk ismerteti a belső ellenőrzőpont és a visszajátszás fogalmak az Azure Stream Analytics, és ezek milyen hatással vannak a feladat-helyreállításra. Minden alkalommal, amikor egy Stream Analytics-feladat fut, az állapotadatok belső legfeljebb megmaradnak. Ezt az állapotinformációt a rendszer rendszeresen menti egy ellenőrzőponton. Bizonyos esetekben az ellenőrzőpont-adatok at a feladat helyreállításához használja a feladat helyreállítása, ha egy feladat hiba vagy frissítés történik. Más körülmények között az ellenőrzőpont nem használható a helyreállításhoz, és újrakell játszani.

## <a name="stateful-query-logicin-temporal-elements"></a>Állapotalapú lekérdezési logika időbeli elemekben
Az Azure Stream Analytics-feladat egyik egyedülálló képessége az állapotalapú feldolgozás, például ablakos aggregátumok, időbeli illesztések és időbeli analitikus függvények végrehajtása. Ezen operátorok mindegyike megőrzi az állapotadatokat a feladat futtatásakor.A lekérdezési elemek maximális ablakmérete hét nap. 

A temporális ablak koncepciója több Stream Analytics-lekérdezési elemben jelenik meg:
1. Ablakos aggregátumok (GROUP BY of Tumbling, Hopping és Sliding ablakok)

2. Időbeli illesztések (JOIN with DATEDIFF)

3. Időbeli analitikus függvények (ISFIRST, LAST és LAG LIMIT DURATION-vel)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Feladat-helyreállítás csomóponthiba esetén, beleértve az operációs rendszer frissítését is
Minden alkalommal, amikor egy Stream Analytics-feladat fut, belsőleg horizontálisan ki van osztva, hogy több munkavégző csomóponton keresztül dolgozzon. Minden munkavégző csomópont állapota ellenőrzőpontnéhány percenként, amely segít a rendszer helyreállítása, ha hiba történik.

Időnként előfordulhat, hogy egy adott munkavégző csomópont meghibásodik, vagy az operációs rendszer frissítése az adott munkavégző csomóponton fordulhat elő. Az automatikus helyreállításhoz a Stream Analytics beszerez egy új kifogástalan állapotú csomópontot, és a korábbi munkavégző csomópont állapota visszaáll a legújabb elérhető ellenőrzőpontról. A munka folytatásához egy kis mennyiségű visszajátszás szükséges az állapot visszaállításához az ellenőrzőpont tól. Általában a visszaállítási rés csak néhány perc. Ha elegendő streamelési egység van kiválasztva a feladathoz, a visszajátszást gyorsan el kell végezni. 

Egy teljesen párhuzamos lekérdezésben a munkavégző csomópont meghibásodása után a felzárkózáshoz szükséges idő arányos a következőkkel:

[a bemeneti esemény sebessége] x [a hézag hossza] / [a feldolgozási partíciók száma]

Ha a csomóponthiba és az operációs rendszer frissítése miatt jelentős feldolgozási késleltetést észlel, fontolja meg a lekérdezés teljes párhuzamosvá tételét, és méretezze a feladatot több streamelési egység lefoglalásához. További információ: [Az Azure Stream Analytics-feladat méretezése az átviteli forgalom növelése érdekében](stream-analytics-scale-jobs.md)című témakörben talál.

A jelenlegi Stream Analytics nem jelenít meg jelentést, ha ilyen típusú helyreállítási folyamat zajlik.

## <a name="job-recovery-from-a-service-upgrade"></a>Feladat-helyreállítás szolgáltatásfrissítésből 
A Microsoft időnként frissíti azOkat a bináris fájlokat, amelyek az Azure-szolgáltatás ban a Stream Analytics-feladatokat futtatják. Ilyenkor a felhasználók futó feladatai újabb verzióra frissülnek, és a feladat automatikusan újraindul. 

Jelenleg a helyreállítási ellenőrzőpont formátuma nem marad meg a frissítések között. Ennek eredményeképpen a streamelési lekérdezés állapotát teljes mértékben vissza kell állítani a visszajátszási technikával. Annak érdekében, hogy a Stream Analytics-feladatok pontosan ugyanazt a bemenetet játszhassák le, fontos, hogy a forrásadatok adatmegőrzési szabályzatát legalább a lekérdezés ablakméretére állítsa be. Ennek elmulasztása helytelen vagy részleges eredményeket eredményezhet a szolgáltatás frissítése során, mivel előfordulhat, hogy a forrásadatok nem maradnak meg elég messze ahhoz, hogy tartalmazzák a teljes ablakméretet.

Általánosságban elmondható, hogy a szükséges visszajátszás mértéke arányos az ablak méretének és az átlagos eseménysebességnek a szorzatával. Például egy 1000 esemény/másodperces bemeneti sebességgel rendelkező feladat esetén az egy óránál nagyobb ablakméret nagy visszajátszási mérettel rendelkezik. Legfeljebb egy órányi adatot kell újra feldolgozni az állapot inicializálásához, hogy teljes és helyes eredményeket hozhassanak, ami hosszabb ideig késleltetheti a kimenetet (nincs kimenet). Az ablakok vagy más időbeli operátorok nélküli lekérdezések , például `JOIN` vagy `LAG`a , nulla visszajátszással rendelkeznének.

## <a name="estimate-replay-catch-up-time"></a>Becsült visszajátszási felzárkózási idő
A szolgáltatásfrissítés miatti késés hosszának becsléséhez kövesse az alábbi módszert:

1. Töltse be a bemeneti Event Hub elegendő adattal, hogy fedezze a legnagyobb ablak mérete a lekérdezés, a várt eseménysebesség. Az események időbélyegének közel kell lennie a falióra idejéhez ebben az időszakban, mintha élő bemeneti hírcsatorna lenne. Ha például van egy 3 napos ablak a lekérdezésben, küldjön eseményeket az Event Hub három napig, és továbbra is küldeseményeket. 

2. Indítsa el a feladatot a **Most** kezdőidőpontként. 

3. Mérje meg a kezdési időpont és az első kimenet létrejötte közötti időt. Az idő durva, hogy mennyi késés tamás a feladat során a szolgáltatás frissítése során.

4. Ha a késleltetés túl hosszú, próbálja meg particionálni a feladatot, és növelje a SUs-ok számát, így a terhelés több csomópontra oszlik. Másik lehetőségként fontolja meg az ablakméretek csökkentését a lekérdezésben, és további összesítést vagy más állapotalapú feldolgozást hajtson végre a Stream Analytics-feladat által az alsóbb rétegben (például az Azure SQL-adatbázis használatával) előállított kimeneten.

A kritikus fontosságú feladatok frissítése során az általános szolgáltatásstabilitási probléma érdekében fontolja meg az ismétlődő feladatok futtatását a párosított Azure-régiókban. További információt a [Garanciastream-elemzésfeladat megbízhatósága a szolgáltatásfrissítések során című](stream-analytics-job-reliability.md)témakörben talál.

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Feladat-helyreállítás egy felhasználó által kezdeményezett leállítási és indítási
A lekérdezés szintaxisának szerkesztéséhez egy streamelési feladaton, vagy a bemenetek és kimenetek módosításához a feladatot le kell állítani a módosítások végrehajtása és a feladatterv frissítése érdekében. Ilyen esetekben, amikor egy felhasználó leállítja a streamelési feladat, és újraindítja, a helyreállítási forgatókönyv hasonló a szolgáltatás frissítése. 

Ellenőrzőpont-adatok nem használhatók a felhasználó által kezdeményezett feladat újraindításához. A kimenet idúra idáig történő becsléséhez használja az előző szakaszban leírt eljárást, és alkalmazzon hasonló kockázatcsökkentést, ha a késleltetés túl hosszú.

## <a name="next-steps"></a>További lépések
A megbízhatósággal és méretezhetőséggel kapcsolatos további információkért lásd az alábbi cikkeket:
- [Oktatóanyag: Riasztások beállítása az Azure Stream Analytics-feladatokhoz](stream-analytics-set-up-alerts.md)
- [Azure Stream Analytics-feladat méretezése az átviteli forgalom növelése érdekében](stream-analytics-scale-jobs.md)
- [A Stream Analytics-feladat megbízhatóságának garantálása a szolgáltatásfrissítések során](stream-analytics-job-reliability.md)

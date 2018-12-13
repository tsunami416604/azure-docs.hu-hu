---
title: Ellenőrzőpont és visszajátszás feladat-helyreállítással kapcsolatos fogalmakról, az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti az ellenőrzőpont és visszajátszás feladat helyreállítással kapcsolatos fogalmakról az Azure Stream Analytics szolgáltatásban.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 9dcfbd4b5fcc8462c88b16f585424166ecd3d499
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088255"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Az Azure Stream Analytics-feladatok ellenőrzőpont és visszajátszás fogalmak
Ez a cikk a belső ellenőrzőpont és visszajátszás kapcsolatos fogalmakat ismerteti az Azure Stream Analytics és a hatás azokat, rendelkezik a helyreállítási feladat. Minden alkalommal, amikor egy Stream Analytics-feladat fut, állam tárolt adatok belső használatra. Adott állapot adatait rendszeresen menteni a egy ellenőrzőpontot. Bizonyos esetekben az ellenőrzőpont adatok feladat helyreállítási feladat hiba vagy frissítés esetén. Más esetekben az ellenőrzőpont nem használható helyreállításhoz, és a egy újrajátszás szükség.

## <a name="stateful-query-logicin-temporal-elements"></a>Állapot-nyilvántartó lekérdezés logikája historikus elemek
Az Azure Stream Analytics-feladat az egyedi képességét egyik állapot-nyilvántartó feldolgozó, például az ablakos összesítéseket, az időalapú illesztéseket és a historikus elemzési funkciók végrehajtásához. Ezen operátorok mindegyike állapotadatokat megőrzi, a feladat futtatásakor. Ezen lekérdezési elemek maximális ablak mérete hét nap. 

A historikus időszak fogalma a több Stream Analytics lekérdezési elemeket jelenik meg:
1. Aggregálást (csoport által az Átfedésmentes, segítségével tehetjük meg, és késleltetett windows)

2. A historikus illesztések (DATEDIFF csatlakoztatás)

3. A historikus elemzési funkciók (ISFIRST, utolsó és a LIMIT DURATION LAG)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Tárolócsomópont-hiba, beleértve az operációs rendszer frissítése feladat helyreállítása
Minden alkalommal, amikor egy Stream Analytics-feladat fut, belső, horizontálisan munka során több munkavégző csomóponton. Egyes feldolgozó csomópontok állapota alkulcsaihoz néhány percenként, ami segít, hogy a rendszer, ha hiba történik.

Időnként adott munkavégző csomópont meghiúsulhat, vagy az operációs rendszer frissítése fordulhat elő, a feldolgozó csomóponton. Automatikus helyreállítás, a Stream Analytics egy új kifogástalan állapotú csomópontra beszerzi, és a korábbi munkavégző csomópont állapot a legújabb elérhető ellenőrzőpont visszaállítása megtörtént. Folytathatja a munkát, ismétlés kisebb mennyiségű szükség, az állapot visszaállítása az idő, amikor az ellenőrzőpont készül. A visszaállítási gap rendszerint csak néhány percig. Ha elegendő folyamatos átviteli egységek ki van jelölve, a feladat, a visszajátszás gyorsan kell elvégezni. 

Egy teljes körűen párhuzamos lekérdezés ideig tart a munkavégző csomópont hibája után olvasásra arányos:

[a bemeneti események száma] x [gap hossza] / [a partíciók feldolgozása száma]

Ha minden eddiginél megfigyelte jelentős feldolgozási késedelem miatt Csomóponthiba, és az operációs rendszer frissítése, a lekérdezés teljes mértékben teszi párhuzamos és méretezhető, több Streamelési egység lefoglalása a feladat. További információkért lásd: [méretezése az Azure Stream Analytics-feladat, növelheti a teljesítményt](stream-analytics-scale-jobs.md).

Aktuális Stream Analytics nem egy jelentés megjelenítése, ha az ilyen helyreállítási folyamat lefolyása.

## <a name="job-recovery-from-a-service-upgrade"></a>Feladat helyreállítás egy szolgáltatás frissítése 
A Microsoft időnként a bináris fájlokat, amelyek a Stream Analytics-feladatok futnak az Azure-szolgáltatás frissíti. A következő esetekben felhasználók futó feladatok frissítése újabb verzióra, és a feladat automatikusan újraindul. 

A helyreállítási ellenőrzőpont formátum jelenleg nem őrződnek meg frissítései között. Ennek eredményeképpen a streamelési lekérdezés állapotát vissza kell állítani visszajátszását módszer használatával. Annak érdekében, hogy a Stream Analytics-feladatok visszajátszani pontosan ugyanaz előtt fontos, hogy az adatforrás esetén a megtartási házirendben beállításban legalább a bemeneti adatok az ablak méreteit a lekérdezést. Ezt elmulasztja, akkor eredményezhet helytelen vagy részleges eredményeket szolgáltatás a frissítés során, mivel a forrásadatok nem tarthatók elegendő vissza a teljes méretének tartalmazza.

Általában a visszajátszás szükséges az események átlagos száma szorozva az ablak méretével arányos. Például, 1000 esemény / másodperc, a bemeneti sebesség feladat egy ablak mérete nagyobb, mint egy óra tekinthető nagy visszajátszását mérete. Legfeljebb egy órányi adat újra feldolgozott, így teljes eredményezhet, és megfelelő eredményeket, ami miatt késik a output (kimenet nincs) inicializálása az állapot néhány hosszabb időre lehet szükség lehet. Például a lekérdezések nem windows- vagy egyéb az ideiglenes operátorok `JOIN` vagy `LAG`, nulla visszajátszását lenne.

## <a name="estimate-replay-catch-up-time"></a>Ismétlés utólagos idő becslése
Megbecsülheti egy szolgáltatás frissítése miatt a késés hossza, kövesse ezt a módszert:

1. Töltse be a bemeneti Event Hub elegendő adatokkal, hogy biztosítsák a legnagyobb ablakméret várt esemény mellett a lekérdezésben. Az események időbélyeg úgy kell megközelíti a valós idő, hogy bizonyos idő, egész, hogy a csatorna élő bemeneti. Például ha a lekérdezés egy 3 napos időszak, események küldése eseményközpontba három nap, és továbbra is küldje az eseményeket. 

2. A feladat használatának megkezdéséhez **most** a kezdési idő szerint. 

3. A kezdési időpont, amikor jön létre az első kimeneti között eltelt idő mérjük. Az idő hozzávetőleges az mennyi késleltetési a feladat járna szolgáltatás frissítése során.

4. Ha a késleltetés túl hosszú, próbálja meg a feladat particionálja, és növelje az SUS-t, így a terhelés van terjednek több csomópontot. Azt is megteheti érdemes csökkenteni az ablak méretek a lekérdezésben, és hajtsa végre további összesítés vagy más állapotalapú, a kimenet a (például Azure SQL database-t használó) az alsóbb rétegbeli fogadó a Stream Analytics-feladat által előállított feldolgozását.

Általános szolgáltatás stabilitásának szempont a működés szempontjából kritikus feladatok a frissítés során érdemes lehet az ismétlődő feladatok futtatása az Azure párosított régiók. További információkért lásd: [garancia Stream Analytics-feladat megbízhatóság szolgáltatás frissítései során](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>A helyreállítási feladat egy felhasználó által kezdeményezett leállítása és indítása
A lekérdezés szintaxisa a folyamatos átviteli feladatok szerkesztheti, vagy módosíthatja a bemenetet és kimenetet, hajtsa végre a módosításokat, és frissítse a feladat tervezési le kell állítani a feladatnak szüksége van. Ilyen esetekben amikor a felhasználó a streamelési feladat leállítása, és ismét elindítja a helyreállítási forgatókönyvben hasonlít a szolgáltatás frissítése. 

Ellenőrzőpont-adatok nem használható a feladat felhasználó által kezdeményezett újraindítás. Kimenet késleltetése becsléséhez ilyen újraindítás során, használja ugyanazt az eljárást az előző szakaszban leírtak szerint, és hasonló a kockázatcsökkentés alkalmazni, ha a késleltetés túl hosszú.

## <a name="next-steps"></a>További lépések
A megbízhatóság és méretezhetőség további információkért tanulmányozza a következő cikkeket:
- [Oktatóanyag: Állítsa be a riasztásokat az Azure Stream Analytics-feladatok](stream-analytics-set-up-alerts.md)
- [Azure Stream Analytics-feladat, növelheti a teljesítményt méretezése](stream-analytics-scale-jobs.md)
- [Garantáljuk a Stream Analytics-feladatok megbízhatósága szolgáltatás frissítései során](stream-analytics-job-reliability.md)

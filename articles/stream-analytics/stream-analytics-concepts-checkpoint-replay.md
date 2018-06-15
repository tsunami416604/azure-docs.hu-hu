---
title: Ellenőrzőpont- és ismétlés sikertelen feladat-helyreállítással kapcsolatos fogalmakról Azure Stream Analytics
description: Ez a cikk ismerteti az ellenőrzőpont- és a visszajátszás feladat helyreállítással kapcsolatos fogalmakról Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: 1a7cb6c5d9c3383b127ce38ae21bb2dc811e1f2e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31529484"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Azure Stream Analytics-feladatok ellenőrzőpont- és a visszajátszás jellemzői
Ez a cikk ismerteti a belső ellenőrzőpont- és a visszajátszás fogalmakat Azure Stream Analytics és a hatás azokat a helyreállítási feladat rendelkezik. Minden alkalommal, amikor egy Stream Analytics-feladat fut, állapot tárolt adatok belső. Állapot adatokat rendszeres időközönként menti a egy ellenőrzőpontot. Bizonyos esetekben az ellenőrzőpont adatok feladat helyreállítás a feladat hibáját vagy a frissítés esetén. Más esetekben az ellenőrzőpont nem használható a helyreállításhoz, és a visszajátszás szükség.

## <a name="stateful-query-logic-in-temporal-elements"></a>Historikus elemek állapottartó lekérdezés a logikai
Az Azure Stream Analytics-feladat az egyedi képesség egyik állapot-nyilvántartó műveleteket, például az ablakos összesítéseket, időalapú illesztéseket és historikus analitikai függvények végrehajtásához. Ezen operátorok mindegyikének tartja állapotadatokat, a feladat futtatásakor. A lekérdezés egyikkel maximális ablak mérete hét nap. 

Az ideiglenes ablakot fogalma több Stream Analytics lekérdezési elemek jelenik meg:
1. Ablakos összesítéseket (csoport által az Átfedésmentes, Hopping, és a késleltetett windows)

2. A historikus illesztések (DATEDIFF csatlakoztatás)

3. A historikus analitikai függvények (ISFIRST, LAST és a LIMIT DURATION LAG)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>A csomópont szemben, beleértve az operációs rendszer frissítése feladat helyreállítási
Minden alkalommal, amikor egy Stream Analytics-feladat fut, belső azt van horizontálisan több feldolgozó csomópontok közötti használathoz. Egyes feldolgozó csomópontok állapota alkulcsaihoz néhány percenként, amelyek segítségével a rendszer, ha hiba lép fel.

Esetenként egy adott munkavégző csomópont sikertelenek lehetnek, vagy az operációs rendszer frissítése akkor fordulhat elő, az adott munkavégző csomópont. Automatikusan helyreállítani, a Stream Analytics egy új kifogástalan csomópont szerez be, és a korábbi munkavégző csomópont állapot visszaállítása megtörtént a rendelkezésre álló legújabb ellenőrzőponttól. A munka folytatásához a visszajátszás kis mennyiségű szükség a állapot visszaállításához a óta, amikor az ellenőrzőpontok. A visszaállítási résnek rendszerint csak néhány perc múlva. Elegendő a Streaming Units választja ki a feladatot, ha a visszajátszás gyorsan kell elvégezni. 

A teljes párhuzamos lekérdezések a munkavégző csomópont hibája után szinkronizálásához szükséges idő arányos:

[a bemeneti események száma] x [hiány hossza] / [a feldolgozása a partíciók száma]

Ha valaha is erőforrásigények jelentős feldolgozási késedelem csomópont hibája miatt, és az operációs rendszer frissítése, fontolja meg, így teljes mértékben a lekérdezés párhuzamos, és a méret a feladat több adatfolyam-egységek lefoglalni. További információkért lásd: [skálázása az Azure Stream Analytics-feladat átviteli sebesség növelése](stream-analytics-scale-jobs.md).

Aktuális Stream Analytics nem egy jelentés megjelenítése, ha az ilyen helyreállítási folyamat lefolyása.

## <a name="job-recovery-from-a-service-upgrade"></a>A szolgáltatás frissítése feladat helyreállítás 
A Microsoft alkalmanként frissíti a bináris fájlokat a Stream Analytics-feladatok futtatása az Azure szolgáltatásban. A következő esetekben felhasználók futó feladatok frissítése újabb verzióra, és a feladat automatikusan újraindul. 

A helyreállítási ellenőrzőpont formátum jelenleg nem őrződik meg frissítések között. Ennek eredményeképpen a folyamatos átviteli lekérdezés állapotát kell visszaállítani, teljes egészében a visszajátszás technikával. Annak érdekében, hogy a Stream Analytics-feladatok játszhat a pontos ugyanazt a bemeneti előtt fontos, hogy az adatforrás az adatmegőrzési beállításban legalább az ablak méretének a lekérdezésben. Ha ezt elmulasztja, azt eredményezheti, hibás vagy hiányos eredményeket szolgáltatás a frissítés során, mert az adatok nem tarthatók feldolgozásáig vissza a teljes méretének tartalmazza.

Általában a visszajátszás szükséges mérete megszorozza a események átlagos száma az ablak méretének arányos. Tegyük fel, 1000 esemény, bemeneti sebesség feladat egy nagy ismétlési mérete tekinthető egy ablak mérete nagyobb, mint egy óra. A visszajátszás nagy méretű lekérdezésekhez néhány hosszú időn keresztül a késleltetett output (kimenet nincs) jelenhet meg. 

## <a name="estimate-replay-catch-up-time"></a>Ismétlési utólagos idő becslése
A szolgáltatás frissítése miatt a késés hossza becsléséhez, kövesse az ezzel a módszerrel:

1. A bemeneti Eseményközpont megfelelő adatokkal, amelyek a lekérdezésben, a várt események száma a legnagyobb ablakméret betölteni. Az események időbélyeg úgy kell megközelíti a valós idő adott időn belül, egy élő bemeneti adatcsatorna esetén. Például ha a lekérdezés egy 3 napos időszak, események küldése eseményközpontba három teljes napig, és továbbra is küldi az eseményeket. 

2. A feladat indíthatja **most** a kezdési idő szerint. 

3. A kezdési időpontot és az első kimeneti létrehozásának közötti idő mérését. Az idő nyers az mennyi késleltetési a feladat akkor merülnek fel, a szolgáltatás frissítése során.

4. Ha a késés túl hosszú, próbálja meg a feladat partícióazonosító és SUS-t, az számának növeléséhez, így a terhelés további csomópontokra felülbírálásokkal. Azt is megteheti próbálja meg csökkenteni az ablak mérete a lekérdezésből, és összesítés vagy más állapotalapú alkalmazások és szolgáltatások (például Azure SQL-adatbázis) alárendelt fogadó a Stream Analytics-feladat által létrehozott kimenet feldolgozási további végrehajtására.

Általános szolgáltatás stabilitásának probléma, amely kritikus kritikus feladatok a frissítés során fontolja meg párosított Azure régiókban futó ismétlődő feladatok. További információkért lásd: [garancia Stream Analytics-feladat megbízhatóság során szolgáltatásfrissítések](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>A helyreállítási feladat a felhasználó által kezdeményezett, leállítása és elindítása
A lekérdezés szintaxisa a folyamatos átviteli feladatnak szerkesztéséhez, vagy úgy, hogy bemenetekhez és kimenetekhez, a feladat kell hajtsa végre a módosításokat, és végezze el a feladat tervezési le kell állítani. Ilyen esetekben amikor a felhasználó a folyamatos átviteli feladat leállítása, és ismét elindul, a helyreállítási helyzetet hasonlít a szolgáltatás frissítése. 

Ellenőrzőpont-adatok nem használható a felhasználó által kezdeményezett feladat újraindítása. A késés kimeneti becsléséhez ilyen újraindítás közben, ugyanezt az eljárást használja az előző szakaszban leírtak szerint, és hasonló megoldás alkalmazni, ha a késés túl hosszú.

## <a name="next-steps"></a>További lépések
A megbízhatósági és méretezhetőségi további információkért lásd: ezek a cikkek:
- [Oktatóanyag: Azure Stream Analytics-feladatok riasztások beállítása](stream-analytics-set-up-alerts.md)
- [Egy Azure Stream Analytics-feladat átviteli sebesség növelése méretezése](stream-analytics-scale-jobs.md)
- [Stream Analytics-feladat megbízhatóságának biztosítása szolgáltatás frissítései során](stream-analytics-job-reliability.md)

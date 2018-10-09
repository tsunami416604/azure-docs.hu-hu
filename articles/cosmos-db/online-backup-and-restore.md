---
title: Online biztonsági mentés és visszaállítás Azure Cosmos DB-vel |} A Microsoft Docs
description: Útmutató az automatikus biztonsági mentési és visszaállítási egy Azure Cosmos DB-adatbázisban.
keywords: biztonsági mentés és visszaállítás, az online biztonsági mentés
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: 580c7410119a26ed3601c7c6ee020a13029339fe
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867799"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Automatikus online biztonsági mentés és visszaállítás Azure Cosmos DB-vel
Az Azure Cosmos DB az összes biztonsági mentések a rendszeres időközönként automatikusan vesz igénybe. Az automatikus biztonsági mentést készít a teljesítmény vagy az adatbázis-műveleteket rendelkezésre állásának befolyásolása nélkül. A biztonsági mentések külön-külön tárolja, egy másik storage szolgáltatásban, és ezeket a biztonsági mentéseket globálisan replikálva vannak a regionális katasztrófa szembeni ellenálló-képesség. Ha véletlenül törli a Cosmos DB-tárolóhoz, és később megkövetelése az adat-helyreállítás szánt forgatókönyvek az automatikus biztonsági mentést.  

Ez a cikk a redundáns adattárolás érdekében és a Cosmos DB-ben rendelkezésre állási rövid összefoglalásképpen kezdődik, és majd a biztonsági mentések ismerteti. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>A Cosmos DB használatával – egy röviden összefoglaljuk magas rendelkezésre állás
A cosmos DB tervezték [globálisan elosztott](distribute-data-globally.md) – lehetővé teszi a méretezhető átviteli sebesség mellett szabályzat driven feladatátvételi és átlátható többkiszolgálós API-k több Azure-régiók között. Az Azure Cosmos DB-ajánlatok [99,99 %-os SLA szerinti rendelkezésre állásának](https://azure.microsoft.com/support/legal/sla/cosmos-db) minden egyrégiós és többrégiós fiókokat az összes enyhe konzisztencia, valamint 99,999 %-os olvasási rendelkezésre állás minden többrégiós adatbázisfiókhoz. Az Azure Cosmos DB az összes írási művelet előtt az ügyfél bosszankodnak véglegesítésére, helyi lemezekre szerint a kvórum replikák helyi adatközponton belül. A Cosmos DB magas rendelkezésre állású helyi tároló támaszkodik, és nem függ semmilyen külső tárolási technológiákat. Emellett ha a fiókot is egynél több Azure-régióhoz tartozik, az írási a rendszer replikálja más régióban. Az adatátviteli és hozzáférési adatok méretezni alacsony késéssel, rendelkezhet, az olvasási régióban az adatbázis-fiókjához társított, tetszés szerint. Több replika tartósan megőrzi a minden olvasási régióban az (replikált) adatokat.  

Ahogyan az alábbi ábrán, van-e egy Cosmos DB-tárolók [horizontálisan particionált](partition-data.md). Az alábbi ábrán egy kör "Partíció" helyén, és mindegyik partíció egy replikakészlethez keresztül magas rendelkezésre állású legyen. Ez az a helyi terjesztési belül (az X tengely kimaradásával) egyetlen Azure-régióban. További mindegyik partíció (az annak megfelelő replikakészlethez) van majd globálisan elosztott (például az az ábrán is látható a három régiókban – USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA és közép-India) az adatbázis-fiókjához társított több régióban. A "partíció"beállítása van egy globálisan elosztott entitást alkotó minden régióban (az Y tengely kimaradásával) az adatok több példányát. Az adatbázisfiókhoz társított régiók prioritást rendelhet, és a Cosmos DB transzparens módon átadja a következő régiónak, katasztrófa esetén. Manuálisan is szimulálhatja a feladatátvételt, az alkalmazás-végpontok rendelkezésre állásának teszteléséhez.  

Az alábbi ábrán a magas fokú redundancia a Cosmos DB használatával.

![Magas fokú redundancia Cosmos DB-vel](./media/online-backup-and-restore/redundancy.png)

![Magas fokú redundancia Cosmos DB-vel](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Teljes körű, automatikus, online biztonsági mentések
Hoppá a törölt saját tároló vagy az adatbázis! A Cosmos DB használatával nem csak az adatokat, de az adatok biztonsági mentését is hatékonyan redundáns és rugalmas regionális katasztrófák. A automatikus biztonsági mentése jelenleg körülbelül négy óránként megnyílik, és a legújabb két biztonsági mentést mindig vannak tárolva. Ha az adatok véletlenül eldobott vagy sérült állapotba kerül, lépjen kapcsolatba [az Azure-támogatás](https://azure.microsoft.com/support/options/) nyolc órán belül. 

A biztonsági mentések teljesítményére vagy az adatbázis-műveleteket rendelkezésre állásának befolyásolása nélkül kerül. A cosmos DB a háttérben vesz igénybe a biztonsági mentés felhasználásához a fenntartott egységek és a teljesítményt érintő nélkül, és az adatbázis rendelkezésre állásának befolyásolása nélkül. 

A Cosmos dB-ben tárolt adatokat, ellentétben az automatikus biztonsági mentést az Azure Blob Storage szolgáltatás vannak tárolva. Az alacsony késés és hatékony feltöltési biztosításához a pillanatképet a biztonsági másolat egy példányát az Azure Blob storage-ban és a Cosmos DB-fiókot, az aktuális írási régióba ugyanabban a régióban töltenek fel. Regionális katasztrófa szembeni ellenálló-képesség egy másik régióba georedundáns tárolás (GRS) keresztül minden egyes pillanatképet a biztonsági mentési adatok Azure Blob Storage-ban újra replikálja. Az alábbi ábrán látható, hogy a teljes Cosmos DB-tárolóhoz (az összes három elsődleges partíció USA nyugati RÉGIÓJA, az ebben a példában) van biztonsági mentése egy távoli Azure Blob Storage-fiókot az USA nyugati Régiójában és a GRS replikálja az USA keleti RÉGIÓJA. 

Az alábbi ábrán a rendszeres teljes biztonsági mentést az összes Cosmos DB-entitások GRS Azure Storage-ban.

![Rendszeres teljes biztonsági mentést az összes Cosmos DB-entitások GRS Azure Storage-ban](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Biztonsági másolat megőrzési idejének
A fentiekben ismertetettek szerint az Azure Cosmos DB pillanatképeket készít az adatok négy óránként a partíció szintjén. Egy adott időpontban csak az utolsó két pillanatfelvételek megmaradnak. Azonban ha a tárolót, illetve az adatbázis törlődik, Azure Cosmos DB összes törölt partícióra belül az adott tároló adatbázis meglévő pillanatképeinek 30 napig őrzi meg.

Az SQL API-hoz, ha meg szeretné tartani a saját pillanatképek használható JSON lehetőség az adatok exportálása az Azure Cosmos DB-ben [adatáttelepítési eszközét](import-data.md#export-to-json-file) további biztonsági mentések ütemezéséhez.

> [!NOTE]
> Ha Ön "Kiépítése átviteli több adatbázis szintjén tárolók" – Ne feledje, a visszaállítás teljes adatbázis fiók szintjén történik. Ön Emellett ellenőrizze bizalommal a segélyszolgálatnak 8 órán belül, ha véletlenül törölte a tárolót. Adatok nem állítható vissza, ha 8 órán belül nem forduljon az ügyfélszolgálathoz. 



## <a name="restoring-a-database-from-an-online-backup"></a>Adatbázis visszaállítása az online biztonsági mentés

Ha véletlenül törli az adatbázis vagy -tároló, [küldjön egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) vagy [hívja meg az Azure-támogatás](https://azure.microsoft.com/support/options/) az adatok biztonsági másolatból történő visszaállítását a legutóbbi automatikus. Az Azure-támogatás érhető el a kiválasztott csomagok, például csak a Standard, fejlesztői, a támogatás nem érhető el az alapszintű csomaggal. Különböző támogatási csomagok kapcsolatos további információkért lásd: [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/) lapot. 

Ha az adatbázis visszaállításához (tartalmazza az esetekben, ahol a rendszer törli-e a tárolóban lévő dokumentumok) adatok sérülése probléma miatt, tekintse meg kell [adatsérülés kezelése](#handling-data-corruption) további lépéseket a sérült adatok elkerülése érdekében szükség szerint felülírják a meglévő biztonsági másolatok. Egy adott vissza kell állítani a biztonsági mentési pillanatkép a Cosmos DB van szükség, hogy az adatok a biztonsági mentési ciklust, hogy a pillanatkép idejére elérhető volt.

> [!NOTE]
> Gyűjtemények és adatbázisok csak egy kéréseinek visszaállítása után vissza tudja állítani. Az ügyfél responsbility arra, hogy a tároló vagy az adatbázis törlése az adatok visszaállítása után azonnal. Ha nem törli a visszaállított adatbázisokat vagy gyűjteményeket, azok áfatartalma a visszaállított gyűjtemény vagy az adatbázis költsége számítunk fel. Tehát nagyon fontos azonnal törli őket. 

## <a name="handling-data-corruption"></a>Adatsérülés kezelése

Az Azure Cosmos DB megőrzi az adatbázis-fiókot minden partíció utolsó két biztonsági mentéseit. Ez a modell jól működik, ha egy tárolót (gyűjteményt dokumentumok, gráf, tábla), vagy egy adatbázis véletlenül törölt, mivel a legutóbbi verziók egyikével állíthatók vissza. Azonban abban az esetben, amikor a felhasználók vezethet be olyan adatok sérülés, lehet, hogy az Azure Cosmos DB nélkül is működőképesek maradhatnak az adatsérülés, és lehetséges, hogy a sérülés előfordulhat, hogy rendelkezik felülírja a meglévő biztonsági másolatok. 

Amint sérülés észlel, a felhasználó törölje a sérült tárolót (gyűjteményt/graph vagy tábla), hogy a biztonsági mentések felülírásra kerüljön a sérült adatok védettek. És a legfontosabb Support kapcsolatba, és egy jegyet az adott kérelem súlyossága 2 előléptetése. 

Az alábbi ábrán a véletlen törlés és a tárolóban lévő adatok frissítése az Azure Portalon keresztül container(collection/graph/table) visszaállításhoz a támogatási kérelem létrehozása

![Állítsa vissza a garanciafeltételeknek frissítési tárolója, vagy törölje az adatokat a Cosmos DB-ben](./media/online-backup-and-restore/backup-restore-support.png)

Amikor a visszaállítás történik az ilyen helyzetekben - adatainak visszaállítása egy másik fiókba (utótagja a "-vissza") és a tároló. A visszaállítás nem történik meg, hogy egy alkalommal adja meg a vásárlói adatok érvényesítése és szükség szerint az adatok áthelyezéséhez. A visszaállított tároló-e ugyanabban a régióban, illetve azonos indexelési szabályzatok. Felhasználó, aki a előfizetés rendszergazdai vagy társadminisztrátori láthatja a visszaállított fiók.


> [!NOTE]
> Ha az adatok rögzítésére sérülés, vagy távolítsa el őket, kérjük vegye figyelembe a csak tesztelésre, hamarosan ahogyan az a tevékenység visszaállítani, tárolók vagy adatbázis ára extra – kiosztott átviteli sebesség alapján. 
## <a name="next-steps"></a>További lépések

Az adatbázis több adatközpontban replikációt, tekintse meg [terjesztheti az adatait globálisan Cosmos DB-vel](distribute-data-globally.md). 

A fájl forduljon az Azure ügyfélszolgálatához [jegyet az Azure Portalról](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


---
title: Biztonsági mentés hosszú távú megőrzése
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Ismerje meg, hogyan támogatja a Azure SQL Database és az SQL felügyelt példánya a teljes adatbázis biztonsági másolatainak tárolását akár 10 évig a hosszú távú adatmegőrzési házirend segítségével.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 0e562b92db16456956ff2fe1cbec0f1addde87ef
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048286"
---
# <a name="long-term-retention---azure-sql-database--sql-managed-instance"></a>Hosszú távú adatmegőrzés – Azure SQL Database & SQL felügyelt példánya
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Számos alkalmazás rendelkezik olyan szabályozási, megfelelőségi vagy egyéb üzleti céllal, amely megköveteli, hogy az adatbázis biztonsági másolatait a Azure SQL Database és az Azure SQL felügyelt példányának [automatikus biztonsági mentései](automated-backups-overview.md)által biztosított 7-35 napon túl őrizze meg. A hosszú távú adatmegőrzés (LTR) szolgáltatással a megadott SQL Database és SQL felügyelt példányok teljes biztonsági másolatait tárolhatja az Azure Blob Storage-ban az olvasási hozzáférésű geo-redundáns tárolással akár 10 évig is. Ezután visszaállíthatja a biztonsági mentést új adatbázisként. Az Azure Storage redundanciával kapcsolatos további információkért lásd: [Azure Storage redundancia](../../storage/common/storage-redundancy.md). 

A hosszú idejű adatmegőrzés engedélyezhető a Azure SQL Database számára, és az Azure SQL felügyelt példányának korlátozott nyilvános előzetes verziója. Ez a cikk a hosszú távú adatmegőrzés fogalmi áttekintését tartalmazza. A hosszú távú adatmegőrzés konfigurálásáról lásd: [Azure SQL Database ltr konfigurálása](long-term-backup-retention-configure.md) és az [Azure SQL felügyelt példányok ltr konfigurálása](../managed-instance/long-term-backup-retention-configure.md). 

> [!NOTE]
> Az SQL-ügynök feladatainak használatával a csak a 35 napnál nem régebben ütemezett [biztonsági mentéseket](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) ÜTEMEZHET a ltr helyett.


## <a name="how-long-term-retention-works"></a>A hosszú távú adatmegőrzés működése
     
A biztonsági másolatok hosszú távú megőrzése (LTR) az [automatikusan létrehozott](automated-backups-overview.md) teljes adatbázis biztonsági másolatait használja az időponthoz kötött visszaállítás (PITR) engedélyezéséhez. Ha egy LTR házirend konfigurálva van, a rendszer a biztonsági másolatokat különböző blobokra másolja a hosszú távú tároláshoz. A másolás olyan háttérben futó feladat, amely nem befolyásolja az adatbázis számítási feladatait. Az egyes SQL-adatbázisok LTR házirendje azt is megadhatja, hogy milyen gyakran legyenek létrehozva a LTR biztonsági mentések.

A LTR engedélyezéséhez adjon meg egy szabályzatot négy paraméter kombinációjának használatával: heti biztonsági másolatok megőrzése (W), havi biztonsági másolatok megőrzése (M), éves biztonsági másolat megőrzése (Y) és év hete (WeekOfYear). Ha a W lehetőséget választja, a rendszer minden héten a hosszú távú tárterületre másolja a biztonsági mentést. Az M érték megadásával az egyes hónapok első biztonsági másolatát a rendszer a hosszú távú tárterületre másolja. Ha az Y értéket adja meg, a WeekOfYear által megadott hét során a rendszer a hosszú távú tárterületre másolja az egyik biztonsági mentést. Ha a megadott WeekOfYear a házirend konfigurálásakor a múltban van, akkor az első LTR biztonsági mentés a következő évben jön létre. Minden biztonsági mentés a LTR biztonsági mentésének létrehozásakor konfigurált házirend-paramétereknek megfelelően hosszú távú tárolóban marad.

> [!NOTE]
> A LTR szabályzat módosítása csak a jövőbeli biztonsági másolatokra vonatkozik. Ha például a heti biztonsági másolatok megőrzése (W), a havi biztonsági másolatok megőrzése (M) vagy az éves biztonsági másolatok megőrzése (Y) módosul, az új adatmegőrzési beállítás csak az új biztonsági másolatokra lesz érvényes. A meglévő biztonsági másolatok megőrzése nem lesz módosítva. Ha a régi LTR biztonsági mentéseket a megőrzési időtartam lejárta előtt szeretné törölni, [manuálisan kell törölnie a biztonsági mentéseket](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Példák a LTR házirendre:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Az egyes évek harmadik teljes biztonsági mentését öt évig őrzi meg a rendszer.
   
- W = 0, M = 3, Y = 0

   Az egyes hónapok első teljes biztonsági mentését három hónapig őrzi meg a rendszer.

- W = 12, M = 0, Y = 0

   Minden heti teljes biztonsági mentés 12 hétig lesz tárolva.

- W = 6, M = 12, Y = 10, WeekOfYear = 16

   Minden heti teljes biztonsági mentés hat hétig tart. Minden hónap első teljes biztonsági mentésének kivételével, amelyet 12 hónapig tartanak. Az év 16. hetében készített teljes biztonsági mentés kivételével, amelyet a rendszer 10 évig tart. 

A következő táblázat a hosszú távú biztonsági mentések ritmusát és lejáratát mutatja be a következő szabályzathoz:

W = 12 hét (84 nap), M = 12 hónap (365 nap), Y = 10 év (3650 nap), WeekOfYear = 15 (hét április 15. után)

   ![ltr példa](./media/long-term-retention-overview/ltr-example.png)


Ha módosítja a fenti szabályzatot, és a W = 0 értéket állítja be (heti biztonsági mentések nélkül), a biztonsági másolatok lépésszám a Kiemelt dátumok szerint a fenti táblázatban látható módon változik. A biztonsági másolatok megtartásához szükséges tárolási mennyiség ennek megfelelően csökken. 

> [!IMPORTANT]
> Az egyes LTR biztonsági mentések időzítését az Azure vezérli. Manuálisan nem hozhat létre LTR biztonsági másolatot, vagy szabályozhatja a biztonsági másolat létrehozásának időzítését. Egy LTR házirend konfigurálását követően akár 7 nappal is eltarthat, amíg az első LTR biztonsági mentés megjelenik az elérhető biztonsági másolatok listáján.  


## <a name="geo-replication-and-long-term-backup-retention"></a>Földrajzi replikálás és hosszú távú biztonsági mentés megőrzése

Ha aktív geo-replikációs vagy feladatátvételi csoportokat használ üzleti folytonossági megoldásként, elő kell készítenie a végleges feladatátvételeket, és konfigurálnia kell ugyanazt a LTR-házirendet a másodlagos adatbázisra vagy példányra. A LTR tárolási díja nem növekszik, mivel a biztonsági másolatok nem jönnek létre a formátumú másodlagos zónák. A biztonsági mentések csak akkor jönnek létre, ha a másodlagos válik elsődlegesként a biztonsági másolatok létrehozásakor. Biztosítja a LTR biztonsági mentések nem megszakított generációját a feladatátvétel indításakor, és az elsődleges áthelyezést a másodlagos régióba. 

> [!NOTE]
> Amikor az eredeti elsődleges adatbázis helyreállítja a feladatátvételt okozó kimaradást, az új másodlagos lesz. Ezért a biztonsági másolat létrehozása nem folytatódik, és a meglévő LTR-házirend mindaddig nem lép érvénybe, amíg a rendszer újra nem válik. 

## <a name="managed-instance-support"></a>Felügyelt példányok támogatása

A biztonsági másolatok hosszú távú megőrzése az Azure SQL felügyelt példányain a következő korlátozásokat használja:

- **Korlátozott nyilvános előzetes** verzió – ez az előzetes verzió csak az EA-és CSP-előfizetések esetében érhető el, és korlátozott rendelkezésre állást biztosít.  
- [**Csak PowerShell**](../managed-instance/long-term-backup-retention-configure.md) – jelenleg nincs Azure Portal támogatás. A LTR engedélyezni kell a PowerShell használatával. 

A regisztráció igényléséhez hozzon létre egy [Azure-támogatási jegyet](https://azure.microsoft.com/support/create-ticket/). A probléma típusa beállításnál válassza a technikai probléma, SQL Database felügyelt példány lehetőséget, majd a probléma típusa beállításnál válassza a **biztonsági mentés, visszaállítás és Üzletmenet-folytonosság/hosszú távú biztonsági mentés**lehetőséget. A kérelemben adja meg, hogy a felügyelt példányhoz a LTR korlátozott nyilvános előzetes verziójában szeretne regisztrálni.

## <a name="configure-long-term-backup-retention"></a>A biztonsági másolatok hosszú távú megőrzésének konfigurálása

A biztonsági másolatok hosszú távú megőrzését a Azure Portal és a PowerShell segítségével konfigurálhatja egy Azure SQL Databasehoz, a PowerShell-t pedig egy felügyelt Azure SQL-példányhoz. Ha egy adatbázist szeretne visszaállítani a LTR-tárolóból, kiválaszthatja az időbélyeg alapján egy adott biztonsági mentést. Az adatbázis visszaállítható bármely meglévő kiszolgálóra vagy felügyelt példányra az eredeti adatbázissal megegyező előfizetésben.

A Azure Portal vagy a PowerShell használatával megtudhatja [Azure SQL Database](long-term-backup-retention-configure.md) , hogyan konfigurálhatja a hosszú távú adatmegőrzést, illetve hogyan állíthatja vissza az adatbázist egy Azure SQL Database biztonsági másolatból

Ha meg szeretné tudni, hogyan konfigurálhatja a hosszú távú adatmegőrzést, illetve hogyan állíthatja vissza az adatbázist egy Azure SQL felügyelt példány biztonsági másolatából a PowerShell használatával, tekintse meg az [Azure SQL felügyelt példányok hosszú távú biztonsági mentésének kezelése](../managed-instance/long-term-backup-retention-configure.md)című 

Ha egy adatbázist szeretne visszaállítani a LTR-tárolóból, kiválaszthatja az időbélyeg alapján egy adott biztonsági mentést. Az adatbázis az eredeti adatbázissal megegyező előfizetéshez tartozó meglévő kiszolgálókra állítható vissza. Ha szeretné megtudni, hogyan állíthatja vissza az adatbázist egy LTR biztonsági másolatból, a Azure Portal vagy a PowerShell használatával, tekintse meg a [Azure SQL Database hosszú távú biztonsági mentés kezelése](long-term-backup-retention-configure.md)című témakört. A kérelemben adja meg, hogy a felügyelt példányhoz tartozó LTR korlátozott nyilvános előzetes verziójával szeretne-e regisztrálni.

## <a name="next-steps"></a>További lépések

Mivel az adatbázis biztonsági mentései védik az adatokat a véletlen sérüléstől vagy törléstől, fontos szerepet játszanak az üzletmenet folytonossága és a vész-helyreállítási stratégia. SQL Database az üzletmenet-folytonossági megoldásokkal kapcsolatos további információkért lásd: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).

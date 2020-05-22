---
title: Biztonsági mentés hosszú távú megőrzése
description: Ismerje meg, hogyan támogatja a Azure SQL Database a teljes adatbázis biztonsági másolatainak tárolását akár 10 évig a hosszú távú adatmegőrzési házirend segítségével.
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
ms.openlocfilehash: 6fc5fab9ae61c8c8ade9260b32078ffa430b077f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771400"
---
# <a name="azure-sql-database-long-term-retention"></a>Azure SQL Database hosszú távú adatmegőrzés

Számos alkalmazás rendelkezik olyan szabályozással, megfelelőséggel vagy más üzleti céllal, amely megköveteli az adatbázis biztonsági másolatainak megőrzését Azure SQL Database [automatikus biztonsági mentések](sql-database-automated-backups.md)által biztosított 7-35 napon túl. A hosszú távú adatmegőrzés (LTR) szolgáltatással a megadott SQL Database-adatbázisok teljes biztonsági mentését tárolhatja az Azure Blob Storage-ban az olvasási hozzáférésű geo-redundáns tárolással akár 10 évig is. Ezután visszaállíthatja a biztonsági mentést új adatbázisként. Az Azure Storage redundanciával kapcsolatos további információkért lásd: [Azure Storage redundancia](../storage/common/storage-redundancy.md). 

Az Egypéldányos és a készletezett adatbázisokhoz is engedélyezhető a hosszú idő megőrzése, és a Azure SQL Database felügyelt példányok esetében korlátozott nyilvános előzetes verzióban érhető el. 

> [!NOTE]
> Az SQL-ügynök feladatainak használatával a csak a 35 napnál nem régebben ütemezett [biztonsági mentéseket](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) ÜTEMEZHET a ltr helyett.


## <a name="how-sql-database-long-term-retention-works"></a>A hosszú távú megőrzés működése az SQL Database-ben

A biztonsági másolatok hosszú távú megőrzése (LTR) az [automatikusan létrehozott](sql-database-automated-backups.md) teljes adatbázis biztonsági másolatait használja az időponthoz kötött visszaállítás (PITR) engedélyezéséhez. Ha egy LTR házirend konfigurálva van, a rendszer a biztonsági másolatokat különböző blobokra másolja a hosszú távú tároláshoz. A másolás olyan háttérben futó feladat, amely nem befolyásolja az adatbázis számítási feladatait. Az egyes SQL-adatbázisok LTR házirendje azt is megadhatja, hogy milyen gyakran legyenek létrehozva a LTR biztonsági mentések.

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

   ![ltr példa](./media/sql-database-long-term-retention/ltr-example.png)



Ha módosítja a fenti szabályzatot, és a W = 0 értéket állítja be (heti biztonsági mentések nélkül), a biztonsági másolatok lépésszám a Kiemelt dátumok szerint a fenti táblázatban látható módon változik. A biztonsági másolatok megtartásához szükséges tárolási mennyiség ennek megfelelően csökken. 

> [!IMPORTANT]
> Az egyes LTR biztonsági mentések időzítését Azure SQL Database szabályozza. Nem hozhat létre manuálisan LTR biztonsági mentést, vagy szabályozhatja a biztonsági másolat létrehozásának időzítését. Egy LTR házirend konfigurálását követően akár 7 nappal is eltarthat, amíg az első LTR biztonsági mentés megjelenik az elérhető biztonsági másolatok listáján.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Földrajzi replikálás és hosszú távú biztonsági mentés megőrzése

Ha aktív geo-replikációs vagy feladatátvételi csoportokat használ üzleti folytonossági megoldásként, elő kell készítenie a végleges feladatátvételeket, és konfigurálnia kell ugyanazt a LTR-házirendet a Geo-másodlagos adatbázison. A LTR tárolási díja nem növekszik, mivel a biztonsági másolatok nem jönnek létre a formátumú másodlagos zónák. A biztonsági mentések csak akkor lesznek létrehozva, ha a másodlagos elsődleges lesz. Biztosítja a LTR biztonsági mentések nem megszakított generációját a feladatátvétel indításakor, és az elsődleges áthelyezést a másodlagos régióba. 

> [!NOTE]
> Amikor az eredeti elsődleges adatbázis helyreállítja a feladatátvételt okozó kimaradást, az új másodlagos lesz. Ezért a biztonsági másolat létrehozása nem folytatódik, és a meglévő LTR-házirend mindaddig nem lép érvénybe, amíg a rendszer újra nem válik. 

## <a name="managed-instance-support"></a>Felügyelt példányok támogatása

A biztonsági másolatok hosszú távú megőrzésének Azure SQL Database felügyelt példányokkal való használata a következő korlátozásokkal jár:

- **Korlátozott nyilvános előzetes** verzió – ez az előzetes verzió csak az EA-és CSP-előfizetések esetében érhető el, és korlátozott rendelkezésre állást biztosít.  
- [**Csak PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) – jelenleg nincs Azure Portal támogatás. A LTR engedélyezni kell a PowerShell használatával. 

A regisztráció igényléséhez hozzon létre egy [Azure-támogatási jegyet](https://azure.microsoft.com/support/create-ticket/). A probléma típusa beállításnál válassza a technikai probléma, SQL Database felügyelt példány lehetőséget, majd a probléma típusa beállításnál válassza a **biztonsági mentés, visszaállítás és Üzletmenet-folytonosság/hosszú távú biztonsági mentés**lehetőséget. A kérelemben adja meg, hogy a felügyelt példányhoz a LTR korlátozott nyilvános előzetes verziójában szeretne regisztrálni.

## <a name="configure-long-term-backup-retention"></a>A biztonsági másolatok hosszú távú megőrzésének konfigurálása

Ha meg szeretné tudni, hogyan konfigurálhatja a hosszú távú adatmegőrzést a Azure Portal vagy a PowerShell használatával, tekintse meg a [Azure SQL Database hosszú távú biztonsági mentés kezelése](sql-database-long-term-backup-retention-configure.md)című témakört.

## <a name="restore-database-from-ltr-backup"></a>Adatbázis visszaállítása a LTR biztonsági mentésből

Ha egy adatbázist szeretne visszaállítani a LTR-tárolóból, kiválaszthatja az időbélyeg alapján egy adott biztonsági mentést. Az adatbázis az eredeti adatbázissal megegyező előfizetéshez tartozó meglévő kiszolgálókra állítható vissza. Ha szeretné megtudni, hogyan állíthatja vissza az adatbázist egy LTR biztonsági másolatból, a Azure Portal vagy a PowerShell használatával, tekintse meg a [Azure SQL Database hosszú távú biztonsági mentés kezelése](sql-database-long-term-backup-retention-configure.md)című témakört. A kérelemben adja meg, hogy a felügyelt példányhoz tartozó LTR korlátozott nyilvános előzetes verziójával szeretne-e regisztrálni.

## <a name="next-steps"></a>További lépések

Mivel az adatbázis biztonsági mentései védik az adatokat a véletlen sérüléstől vagy törléstől, fontos szerepet játszanak az üzletmenet folytonossága és a vész-helyreállítási stratégia. SQL Database az üzletmenet-folytonossági megoldásokkal kapcsolatos további információkért lásd: az [üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).

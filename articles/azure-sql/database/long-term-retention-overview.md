---
title: Biztonsági mentés hosszú távú megőrzése
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Ismerje meg, hogyan Azure SQL Database & Azure SQL felügyelt példánya támogatja a teljes adatbázis biztonsági másolatainak tárolását akár 10 évig a hosszú távú adatmegőrzési házirend segítségével.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 05/18/2019
ms.openlocfilehash: 8250fc39fe58168ddc13b7bcf5c040b57d5e92fb
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782620"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>Hosszú távú adatmegőrzés – Azure SQL Database és az Azure SQL felügyelt példánya

Számos alkalmazás rendelkezik olyan szabályozási, megfelelőségi vagy egyéb üzleti céllal, amely megköveteli, hogy az adatbázis biztonsági másolatait a Azure SQL Database és az Azure SQL felügyelt példányának [automatikus biztonsági mentései](automated-backups-overview.md)által biztosított 7-35 napon túl őrizze meg. A hosszú távú adatmegőrzés (LTR) szolgáltatással a megadott SQL Database és SQL felügyelt példányok teljes biztonsági mentéseit tárolhatja az Azure Blob Storage-ban, a [konfigurált redundanciával](automated-backups-overview.md#backup-storage-redundancy) akár 10 évig is. Ezután visszaállíthatja a biztonsági mentést új adatbázisként.

A hosszú idejű adatmegőrzés engedélyezhető a Azure SQL Database számára, és az Azure SQL felügyelt példányának korlátozott nyilvános előzetes verziója. Ez a cikk a hosszú távú adatmegőrzés fogalmi áttekintését tartalmazza. A hosszú távú adatmegőrzés konfigurálásáról lásd: [Azure SQL Database ltr konfigurálása](long-term-backup-retention-configure.md) és az [Azure SQL felügyelt példányok ltr konfigurálása](../managed-instance/long-term-backup-retention-configure.md). 

> [!NOTE]
> Az SQL-ügynök feladatainak használatával a csak a 35 napnál nem régebben ütemezett [biztonsági mentéseket](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) ÜTEMEZHET a ltr helyett.


## <a name="how-long-term-retention-works"></a>A hosszú távú adatmegőrzés működése
     
A biztonsági másolatok hosszú távú megőrzése (LTR) az [automatikusan létrehozott](automated-backups-overview.md) teljes adatbázis biztonsági másolatait használja az időponthoz kötött visszaállítás (PITR) engedélyezéséhez. Ha egy LTR házirend konfigurálva van, a rendszer a biztonsági másolatokat különböző blobokra másolja a hosszú távú tároláshoz. A másolás olyan háttérben futó feladat, amely nem befolyásolja az adatbázis számítási feladatait. A SQL Database egyes adatbázisaihoz tartozó LTR szabályzat azt is megadhatja, hogy milyen gyakran legyenek létrehozva a LTR biztonsági mentések.

A LTR engedélyezéséhez adjon meg egy szabályzatot négy paraméter kombinációjának használatával: heti biztonsági másolatok megőrzése (W), havi biztonsági másolatok megőrzése (M), éves biztonsági másolat megőrzése (Y) és év hete (WeekOfYear). Ha a W lehetőséget választja, a rendszer minden héten a hosszú távú tárterületre másolja a biztonsági mentést. Az M érték megadásával az egyes hónapok első biztonsági másolatát a rendszer a hosszú távú tárterületre másolja. Ha az Y értéket adja meg, a WeekOfYear által megadott hét során a rendszer a hosszú távú tárterületre másolja az egyik biztonsági mentést. Ha a megadott WeekOfYear a házirend konfigurálásakor a múltban van, akkor az első LTR biztonsági mentés a következő évben jön létre. Minden biztonsági mentés a LTR biztonsági mentésének létrehozásakor konfigurált házirend-paramétereknek megfelelően hosszú távú tárolóban marad.

> [!NOTE]
> A LTR szabályzat módosítása csak a jövőbeli biztonsági másolatokra vonatkozik. Ha például a heti biztonsági másolatok megőrzése (W), a havi biztonsági másolatok megőrzése (M) vagy az éves biztonsági másolatok megőrzése (Y) módosul, az új adatmegőrzési beállítás csak az új biztonsági másolatokra lesz érvényes. A meglévő biztonsági másolatok megőrzése nem lesz módosítva. Ha a régi LTR biztonsági mentéseket a megőrzési időtartam lejárta előtt szeretné törölni, [manuálisan kell törölnie a biztonsági mentéseket](./long-term-backup-retention-configure.md#delete-ltr-backups).
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

Ha aktív geo-replikációs vagy feladatátvételi csoportokat használ üzleti folytonossági megoldásként, elő kell készítenie a végleges feladatátvételeket, és konfigurálnia kell ugyanazt a LTR-házirendet a másodlagos adatbázisra vagy példányra. A LTR tárolási díja nem növekszik, mert a formátumú másodlagos zónák nem jönnek létre biztonsági másolatok. A biztonsági mentések csak akkor jönnek létre, ha a másodlagos válik elsődlegesként a biztonsági másolatok létrehozásakor. Biztosítja a LTR biztonsági mentések nem megszakított generációját a feladatátvétel indításakor, és az elsődleges áthelyezést a másodlagos régióba. 

> [!NOTE]
> Amikor az eredeti elsődleges adatbázis helyreállítja a feladatátvételt okozó kimaradást, az új másodlagos lesz. Ezért a biztonsági másolat létrehozása nem folytatódik, és a meglévő LTR-házirend mindaddig nem lép érvénybe, amíg a rendszer újra nem válik. 

## <a name="sql-managed-instance-support"></a>Az SQL Managed Instance támogatása

A biztonsági másolatok hosszú távú megőrzése és az Azure SQL felügyelt példányának használata a következő korlátozásokkal jár:

- **Korlátozott nyilvános előzetes** verzió – ez az előzetes verzió csak az EA-és CSP-előfizetések esetében érhető el, és korlátozott rendelkezésre állást biztosít.  
- [**Csak PowerShell**](../managed-instance/long-term-backup-retention-configure.md) – jelenleg nincs Azure Portal támogatás. A LTR engedélyezni kell a PowerShell használatával. 

A regisztráció igényléséhez hozzon létre egy [Azure-támogatási jegyet](https://azure.microsoft.com/support/create-ticket/). A probléma típusa beállításnál válassza a technikai probléma lehetőséget, válassza az SQL felügyelt példány lehetőséget, és a probléma típusa beállításnál válassza a **biztonsági mentés, visszaállítás és Üzletmenet-folytonosság/hosszú távú biztonsági mentés** lehetőséget. A kérelemben adja meg azt az állapotot, amelyet az SQL felügyelt példányának LTR korlátozott nyilvános előzetes verziójára szeretne regisztrálni.

## <a name="configure-long-term-backup-retention"></a>A biztonsági másolatok hosszú távú megőrzésének konfigurálása

A biztonsági másolatok hosszú távú megőrzését a Azure Portal és a PowerShell használatával konfigurálhatja a Azure SQL Databasehoz, valamint a PowerShellt az Azure SQL felügyelt példányaihoz. Ha egy adatbázist szeretne visszaállítani a LTR-tárolóból, kiválaszthatja az időbélyeg alapján egy adott biztonsági mentést. Az adatbázis visszaállítható bármely meglévő kiszolgálóra vagy felügyelt példányra az eredeti adatbázissal megegyező előfizetésben.

A Azure Portal vagy a PowerShell használatával megtudhatja [Azure SQL Database](long-term-backup-retention-configure.md) , hogyan konfigurálhatja a hosszú távú adatmegőrzést, illetve hogyan állíthatja vissza az adatbázist SQL Database biztonsági másolatból

Ha meg szeretné tudni, hogyan konfigurálhatja a hosszú távú adatmegőrzést, illetve hogyan állíthatja vissza az adatbázist az SQL felügyelt példányának biztonsági másolatából a PowerShell használatával, tekintse meg az [Azure SQL felügyelt példány hosszú távú biztonsági mentésének kezelése](../managed-instance/long-term-backup-retention-configure.md)

Ha egy adatbázist szeretne visszaállítani a LTR-tárolóból, kiválaszthatja az időbélyeg alapján egy adott biztonsági mentést. Az adatbázis az eredeti adatbázissal megegyező előfizetéshez tartozó meglévő kiszolgálókra állítható vissza. Ha szeretné megtudni, hogyan állíthatja vissza az adatbázist egy LTR biztonsági másolatból, a Azure Portal vagy a PowerShell használatával, tekintse meg a [Azure SQL Database hosszú távú biztonsági mentés kezelése](long-term-backup-retention-configure.md)című témakört. A kérelemben adja meg azt az állapotot, amelyet a LTR for SQL felügyelt példányának korlátozott nyilvános előzetes verziójában szeretne regisztrálni.

## <a name="next-steps"></a>Következő lépések

Mivel az adatbázis biztonsági mentései védik az adatokat a véletlen sérüléstől vagy törléstől, fontos szerepet játszanak az üzletmenet folytonossága és a vész-helyreállítási stratégia. SQL Database az üzletmenet-folytonossági megoldásokkal kapcsolatos további információkért lásd: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).

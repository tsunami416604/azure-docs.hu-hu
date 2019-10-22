---
title: Importálás Azure SQL Database
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Visual Interface adatimportálás modult az adatok Azure SQL Databaseból való lekéréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694609"
---
# <a name="import-from-azure-sql-database"></a>Importálás Azure SQL Database

Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Visual Interface [adatimportálási](import-data.md) modulja az adatok Azure SQL Databaseból való lekéréséhez.  

Az adatok adatbázisból történő importálásához meg kell adnia a kiszolgáló nevét és az adatbázis nevét, valamint egy olyan SQL-utasítást, amely meghatározza a táblát, nézetet vagy lekérdezést.  

Általánosságban elmondható, hogy az Azure-adatbázisokban tárolt adattárolás drágább, mint a táblázatok vagy Blobok használata az Azure-ban. Az előfizetés típusától függően korlátozható az adatbázisban tárolható adatmennyiség korlátai is. Azonban nincs tranzakciós díj SQL Azure adatbázisra, így a lehetőség ideális a kisebb mennyiségű gyakran használt információ, például az adatkeresési táblák vagy az Adatszótárak gyors eléréséhez.

Az adatok Azure-adatbázisban való tárolása akkor is ajánlott, ha az adatokat még az olvasás előtt szűrni kell, vagy az előrejelzések vagy metrikák mentését vissza szeretné állítani az adatbázisba a jelentéskészítéshez.

## <a name="how-to-import-data-from-azure-sql-database"></a>Adatok importálása Azure SQL Databaseból

1. Adja hozzá az [adatimportálási](import-data.md) modult a folyamathoz. Ezt a modult a Visual Interface (adatbevitel és kimenet) kategóriában találja.

1. Az **adatforrás**területen válassza a **Azure SQL Database**lehetőséget.

1. Adja meg a következő beállításokat a Azure SQL Databasera vonatkozóan.

    **Adatbázis-kiszolgáló neve**: írja be az Azure által generált kiszolgálónevet. Általában ez a Form `<generated_identifier>.database.windows.net`.

    **Adatbázis neve**: írja be a megadott kiszolgálón található meglévő adatbázis nevét.

    **Kiszolgáló felhasználói fiókjának neve**: írja be egy olyan fiók felhasználónevét, amely hozzáférési engedélyekkel rendelkezik az adatbázishoz.

    **Kiszolgáló felhasználói fiókjának jelszava**: adja meg a megadott felhasználói fiók jelszavát.

    **Adatbázis-lekérdezés**: írjon be vagy illesszen be egy SQL-utasítást, amely leírja az olvasni kívánt adatok leírását. Mindig érvényesítse az SQL-utasítást, és ellenőrizze a lekérdezés eredményeit előre, egy eszköz, például a Visual Studio Server Explorer vagy a SQL Server Data Tools használatával.

1. Ha a Azure Machine Learning beolvasott adatkészlet nem változik a folyamat futtatása között, válassza a **gyorsítótárazott eredmények használata** lehetőséget.

    Ha be van jelölve, ha a modul paramétereinek nincsenek más módosításai, a folyamat a modul első futtatásakor betölti az adatokat, majd ezt követően az adatkészlet gyorsítótárazott verzióját használja.

    Ha újra szeretné tölteni az adatkészletet a folyamat minden egyes iterációján, törölje ezt a beállítást. Az adatkészletet a rendszer minden alkalommal visszatölti a forrásból, amikor minden paraméter módosul az [importálási adatok](import-data.md)között.

1. A folyamat futtatása.

    Mivel az [importálási](import-data.md) művelet betölti az értékeket a Visual Interface szolgáltatásba, a forrás-adatbázisban használt adattípustól függően bizonyos implicit típusú átalakítás is elvégezhető.

## <a name="results"></a>Eredmények

Ha elkészült az importálással, kattintson a kimeneti adatkészletre, és válassza a **Megjelenítés** lehetőséget, hogy megtekintse az adatokat az importálás sikeres volt-e.

Igény szerint az adatkészletet és a hozzá tartozó metaadatokat a Visual Interface eszközeivel is módosíthatja:

- A [metaadatok szerkesztése](edit-metadata.md) lehetőséggel módosíthatja az oszlopnevek, átalakíthat egy oszlopot más adattípusra, illetve jelezheti, hogy mely oszlopok címkék vagy szolgáltatások.

- Oszlopok részhalmazának kiválasztásához használja [az adatkészletben az Oszlopok kiválasztása lehetőséget](select-columns-in-dataset.md) .

- A [Partition és a Sample](partition-and-sample.md) használatával válassza el az adatkészletet a feltételek alapján, vagy töltse le az első n sort.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 

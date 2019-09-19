---
title: Az Azure SQL Database gyakori csatlakozási problémáinak hibaelhárítása
description: A Azure SQL Database gyakori kapcsolódási hibáinak azonosítására és elhárítására szolgáló lépések.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 01/25/2019
ms.openlocfilehash: cd0ab6d89d88c594d283dc0718c0f58ebb98bf43
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090797"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>A Azure SQL Database kapcsolódási problémáinak elhárítása

Ha a Azure SQL Databasehoz való csatlakozás meghiúsul, [hibaüzeneteket](sql-database-develop-error-messages.md)kap. Ez a cikk egy központi témakör, amely segít a Azure SQL Database kapcsolódási problémák elhárításában. Bemutatja a kapcsolódási problémák [gyakori okait](#cause) , és a [hibaelhárítási eszközt](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) javasolja a probléma azonosításához, valamint hibaelhárítási lépéseket tesz lehetővé az [átmeneti hibák](#troubleshoot-transient-errors) és az [állandó vagy nem átmeneti hibák megoldásához. ](#troubleshoot-persistent-errors). 

Ha megtapasztalja a kapcsolatok problémáit, próbálkozzon a cikkben ismertetett hibaelhárítási lépésekkel.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Ok

A kapcsolódási problémákat a következők bármelyike okozhatta:

* Nem sikerült alkalmazni az ajánlott eljárásokat és tervezési irányelveket az alkalmazás tervezési folyamata során.  Az első lépésekhez tekintse meg [SQL Database fejlesztés áttekintését](sql-database-develop-overview.md) .
* Újrakonfigurálás Azure SQL Database
* Tűzfalbeállítások
* A kapcsolatok időtúllépése
* Helytelen bejelentkezési adatok
* Néhány Azure SQL Database erőforráshoz elérte a maximális korlátot

A Azure SQL Databasehoz való kapcsolódási problémák általában a következőképpen sorolhatók be:

* [Átmeneti hibák (rövid életű vagy időszakos)](#troubleshoot-transient-errors)
* [Állandó vagy nem átmeneti hibák (a rendszeresen ismétlődő hibák)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Próbálja ki a hibaelhárítási Azure SQL Database kapcsolódási problémákat

Ha egy adott csatlakoztatási hibába ütközik, próbálja meg [ezt az eszközt](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), amellyel gyorsan azonosíthatja és megoldhatja a problémát.

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibák elhárítása

Amikor egy alkalmazás egy Azure SQL Database-adatbázishoz csatlakozik, a következő hibaüzenet jelenik meg:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Ez a hibaüzenet általában átmeneti (rövid életű).
> 
> 

Ez a hiba akkor fordul elő, amikor az adatbázist áthelyezi (vagy újrakonfigurálták), és az alkalmazás elveszíti a kapcsolatát az adatbázissal. Az adatbázis-újrakonfigurálási események egy tervezett esemény (például egy szoftverfrissítés) vagy egy nem tervezett esemény (például egy folyamat összeomlása vagy terheléselosztás) miatt történnek. A legtöbb újrakonfigurálási esemény általában rövid életű, és legfeljebb 60 másodpercnél rövidebb idő alatt elvégezhető. Ezek az események azonban esetenként hosszabb időt vehetnek igénybe, például ha egy nagy tranzakció hosszan futó helyreállítást okoz.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Az átmeneti kapcsolódási problémák megoldásának lépései

1. Győződjön meg arról, hogy a [Microsoft Azure szolgáltatás irányítópultján](https://azure.microsoft.com/status) az alkalmazás által jelentett hibák ideje alatt bekövetkezett összes ismert leállás látható.
2. A felhőalapú szolgáltatásokhoz, például a Azure SQL Databasehoz csatlakozó alkalmazásoknak rendszeres újrakonfigurálási eseményeket kell elvárniuk, és az újrapróbálkozási logikát kell végrehajtaniuk a hibák kezeléséhez, ahelyett, hogy ezeket a hibákat felhasználja a felhasználóknak További információért és általános újrapróbálkozási stratégiákért tekintse át az [átmeneti hibák](sql-database-connectivity-issues.md) szakaszt, valamint az ajánlott eljárásokat és tervezési irányelveket [SQL Database fejlesztés áttekintésében](sql-database-develop-overview.md) . Ezt követően tekintse meg a [SQL Database és SQL Server](sql-database-libraries.md) a következő témakörben talál példákat:
3. Mivel az adatbázis megközelíti az erőforrás-korlátozásokat, úgy tűnhet, hogy átmeneti kapcsolódási probléma. Lásd: [erőforrás-korlátok](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Ha a kapcsolódási problémák továbbra is fennállnak, vagy ha az alkalmazás az időtartamot meghaladja a 60 másodpercnél, vagy ha egy adott nap több előfordulását látja a hibával, az Azure-támogatási kérést az Azure- **támogatás beszerzése** lehetőség választásával kérheti le. [ Támogatási](https://azure.microsoft.com/support/options) webhely.

## <a name="troubleshoot-persistent-errors"></a>Állandó hibák elhárítása
Ha az alkalmazás tartósan nem tud csatlakozni a Azure SQL Databasehoz, általában a következők egyikével kapcsolatos problémát jelez:

* Tűzfal-konfiguráció. Az Azure SQL Database vagy az ügyféloldali tűzfal blokkolja a Azure SQL Database kapcsolatait.
* Hálózati újrakonfigurálás az ügyféloldali oldalon: például egy új IP-cím vagy egy proxykiszolgáló.
* Felhasználói hiba: például a hibás típusú kapcsolatok paraméterei, például a kiszolgáló neve a kapcsolatok karakterláncában.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Az állandó csatlakozási problémák megoldásának lépései
1. [Tűzfalszabályok](sql-database-configure-firewall-settings.md) beállítása az ügyfél IP-címének engedélyezéséhez. Ideiglenes tesztelési célból állítson be egy tűzfalszabály használatát a 0.0.0.0 értékkel a kezdő IP-címtartományként, és használja a 255.255.255.255-t a záró IP-címtartományként. Ekkor megnyílik a kiszolgáló minden IP-címre. Ha ezzel feloldja a kapcsolódási problémát, távolítsa el ezt a szabályt, és hozzon létre egy tűzfalszabályot a megfelelő korlátozott IP-címhez vagy címtartomány létrehozásához. 
2. Az ügyfél és az Internet közötti összes tűzfalon ellenőrizze, hogy a 1433-es port nyitva van-e a kimenő kapcsolatok számára. Tekintse át [a Windows tűzfal konfigurálását, hogy engedélyezze SQL Server hozzáférését](https://msdn.microsoft.com/library/cc646023.aspx) és a [hibrid identitás szükséges portokat és protokollokat](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) a Azure Active Directory hitelesítéshez szükséges további portokhoz kapcsolódó további mutatókhoz.
3. Ellenőrizze a kapcsolatok karakterláncát és az egyéb kapcsolatbeállításokat. Tekintse meg a kapcsolódási karakterlánc szakaszt a [kapcsolódási problémák témakörben](sql-database-connectivity-issues.md#connections-to-sql-database).
4. A szolgáltatás állapotának keresése az irányítópulton. Ha úgy gondolja, hogy van egy regionális leállás, a [helyreállítás](sql-database-disaster-recovery.md) egy új régióba való helyreállítás lépéseiből című témakörben talál további információt.

## <a name="next-steps"></a>További lépések
* [Keresés a dokumentációban Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [A Azure SQL Database szolgáltatás legújabb frissítéseinek megtekintése](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>További források
* [SQL Database fejlesztés áttekintése](sql-database-develop-overview.md)
* [Általános átmeneti hibák kezelésére vonatkozó útmutató](../best-practices-retry-general.md)
* [SQL Database és SQL Serverhoz tartozó kapcsolatok kódtárai](sql-database-libraries.md)


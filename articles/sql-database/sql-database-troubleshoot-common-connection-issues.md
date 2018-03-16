---
title: "Az Azure SQL Database gyakori csatlakozási problémáinak hibaelhárítása"
description: "Azonosítása és elhárítása közös kapcsolat az Azure SQL Database lépéseit."
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 11/03/2017
ms.author: daleche
ms.openlocfilehash: 56ebe6876d621e190b4e808804113ff80cb3f50c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Az Azure SQL Database kapcsolati problémák elhárítása
Ha az Azure SQL Database létesített kapcsolat megszakad, [hibaüzenetek](sql-database-develop-error-messages.md). Ez a cikk központi Ez a témakör segítséget nyújt az Azure SQL Database kapcsolódási problémáinak elhárítása. Okozna [gyakori okainak](#cause) kapcsolódási problémák javasolja [hibaelhárításhoz](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) , amely segít az identitás a problémát, és megoldásuk megoldására [átmeneti hibák](#troubleshoot-transient-errors) és [állandó vagy nem átmeneti hiba](#troubleshoot-persistent-errors). 

Ha problémák lépnek fel a kapcsolatot, próbálja meg a kapcsolatos problémák elhárítása ebben a cikkben leírt lépéseket.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Ok
A következő oka lehet kapcsolódási problémák léptek fel:

* Nem sikerült alkalmazni az ajánlott eljárások és tervezési útmutató az alkalmazások kialakítása során.  Lásd: [SQL adatbázis-fejlesztői áttekintés](sql-database-develop-overview.md) a kezdéshez.
* Azure SQL Database reconfiguration
* Tűzfalbeállítások
* A kapcsolat időkorlátja
* Helytelen bejelentkezési adatok
* Az egyes Azure SQL Database-erőforrásokat elérte a maximális korlátot

Általában az Azure SQL Database-kapcsolódási problémák besorolhatók az alábbiak szerint:

* [Átmeneti hibák (rövid élettartamú vagy időszakos)](#troubleshoot-transient-errors)
* [Állandó nem átmeneti hiba (hibák, rendszeresen ismétlődő)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Próbálja ki a hibaelhárítót az Azure SQL adatbázis-csatlakozási problémák
Ha egy adott kapcsolat hiba merül fel, próbálkozzon [ezzel az eszközzel](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), amely segítségével gyorsan identitás és a probléma megoldásához.

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibáinak elhárítása

Amikor egy alkalmazás csatlakozik egy Azure SQL-adatbázis, a következő hibaüzenet jelenhet meg:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Ez a hibaüzenet akkor általában átmeneti jellegű (rövid élettartamú).
> 
> 

Ez a hiba akkor fordul elő, amikor folyamatban van az Azure-adatbázis áthelyezése (vagy újrakonfigurálása) és az alkalmazás megszakad a kapcsolat az SQL-adatbázishoz. SQL adatbázis újrakonfigurálás-események esetén a tervezett események (például szoftverfrissítésre) vagy egy nem tervezett esemény (például egy folyamat összeomlási, vagy a terheléselosztás) miatt. A legtöbb újrakonfigurálás események általában rövid élettartamú, és be kell 60 másodpercnél nagyobb, mint. Azonban ezek az események időnként több időt vesz igénybe fejeződik be, például ha a nagy tranzakció a hosszan futó helyreállítás miatt.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Átmeneti problémák elhárításának

1. Ellenőrizze a [Microsoft Azure szolgáltatás irányítópultját](https://azure.microsoft.com/status) a bármely ismert leállások során, ami a jelenített az alkalmazás időszakban történt.
2. Alkalmazások, amelyek egy felhőalapú szolgáltatás csatlakoznak, például az Azure SQL Database kell rendszeres újrakonfigurálás események várható és megvalósításához újrapróbálkozási logika ezeket a hibákat, helyett felszínre hozza alkalmazáshibák ezek a felhasználók kezeléséhez. Tekintse át a [átmeneti hibák](sql-database-connectivity-issues.md) szakasz és ajánlott eljárások és tervezési irányelveket: [SQL adatbázis-fejlesztői áttekintés](sql-database-develop-overview.md) további információkat, valamint általános próbálja meg újból a stratégiák. Majd, tekintse meg a kódot minták [adatkapcsolattárak SQL Database és SQL Server](sql-database-libraries.md) a részletekért.
3. Egy adatbázis megközelíti a teljes erőforrás kapacitásukkal működjenek, mivel azt is tűnik, hogy egy átmeneti kapcsolati probléma. Lásd: [teljesítménnyel kapcsolatos problémák elhárításakor](sql-database-troubleshoot-performance.md).
4. Ha továbbra is, vagy ha, amelynek az alkalmazás a hiba lép fel, mint 60 másodperc, vagy ha egy adott napon többször tartalmazza a hiba jelenik meg a fájl az Azure támogatási kérelmet kiválasztásával **beolvasása támogatja**a a [Azure támogatási](https://azure.microsoft.com/support/options) hely.

## <a name="troubleshoot-persistent-errors"></a>Állandó hibák elhárítása
Ha az alkalmazás tartósan nem Azure SQL adatbázishoz való kapcsolódáshoz, általában jelez problémát a következők egyikét:

* Tűzfal-konfiguráció. Az Azure SQL adatbázis vagy az ügyféloldali tűzfal blokkolja az Azure SQL Database-kapcsolatok.
* Ügyféloldali újrakonfigurálás hálózati: például egy új IP-címet vagy proxykiszolgálón.
* Felhasználói hiba történt: például elírt kapcsolódási paraméterek, például a kiszolgáló nevét a kapcsolati karakterláncban.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Állandó kapcsolódási problémák elhárításának
1. Állítson be [tűzfal-szabályok](sql-database-configure-firewall-settings.md) lehetővé teszi az ügyfél IP-címet. Ideiglenes tesztelési célokra beállítása egy tűzfalszabályt 0.0.0.0 használja, mint a kezdő IP-címtartomány és 255.255.255.255 használja, mint a záró IP-címtartományt. Ekkor megnyílik a kiszolgáló minden IP-cím. Ha a hálózati probléma így megoldódik, ez a szabály eltávolítása, és hozzon létre egy tűzfalszabályt egy megfelelően korlátozott IP-cím vagy a címtartományt. 
2. Az ügyfél és az Internet közötti összes tűzfalnak ellenőrizze, hogy a 1433-as port kimenő kapcsolatok nyitva-e. Felülvizsgálati [SQL Server hozzáférés engedélyezése a Windows tűzfal beállításainak](https://msdn.microsoft.com/library/cc646023.aspx) és [hibrid identitás szükséges portok és protokollok](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) a további portokat, meg kell nyitnia az Azure-hoz kapcsolódó további mutatók Active Directory-hitelesítéssel.
3. Ellenőrizze a kapcsolati karakterlánc és más csatlakozási beállításait. A kapcsolati karakterlánc című a [kapcsolódási problémák a témakör](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Ellenőrizze a szolgáltatás állapotát az irányítópulton. Ha úgy gondolja, hogy nincs regionális kimaradás, lásd: [kimaradás helyreállíthatók](sql-database-disaster-recovery.md) egy új területet a helyreállítás lépéseit.

## <a name="next-steps"></a>További lépések
* [Az Azure SQL Database teljesítménnyel kapcsolatos problémák elhárítása](sql-database-troubleshoot-performance.md)
* [Keresés a dokumentációban a Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Az Azure SQL Database szolgáltatáshoz a legújabb frissítések megtekintése](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>További források
* [SQL adatbázis-fejlesztői áttekintés](sql-database-develop-overview.md)
* [Általános átmeneti hiba-kezelési útmutatás](../best-practices-retry-general.md)
* [Adatkapcsolattárak SQL Database és SQL Server](sql-database-libraries.md)


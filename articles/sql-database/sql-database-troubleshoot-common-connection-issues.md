---
title: Az Azure SQL Database gyakori csatlakozási problémáinak hibaelhárítása
description: Azonosíthatja és az Azure SQL Database gyakori csatlakozási hibák megoldásához lépéseket.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: daleche
ms.reviewer: jrasnik
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: d278fd6ed06b58db052154e632e565de36853e77
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464891"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Az Azure SQL Database-kapcsolatok problémáinak hibaelhárítása

Az Azure SQL Database-adatbázishoz létesített kapcsolat megszakad, amikor [hibaüzenetek](sql-database-develop-error-messages.md). Ez a cikk a program központi, amely segítséget nyújt az Azure SQL Database kapcsolati problémák elhárításához. Azt mutatja be [gyakori okok](#cause) kapcsolódási problémák, akkor javasolja [hibaelhárítási eszköz](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) , amely segít identitás a problémát, és megoldásuk megoldásához [átmeneti hibák](#troubleshoot-transient-errors) és [állandó és nem átmeneti hibák](#troubleshoot-persistent-errors). 

Ha csatlakozási hibákat tapasztal, próbálja meg a hibaelhárítás ebben a cikkben leírt lépéseket.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Ok

Csatlakozási problémák oka lehet az alábbiak szerint:

* Hiba a alkalmazni az ajánlott eljárások és tervezési útmutató az alkalmazás tervezési folyamat során.  Lásd: [SQL Database fejlesztési áttekintése](sql-database-develop-overview.md) a kezdéshez.
* Az Azure SQL-adatbázis újrakonfigurálása
* Tűzfalbeállítások
* A kapcsolat időkorlátja
* Helytelen bejelentkezési információk
* Az egyes Azure SQL Database-erőforrásokat elérte a maximális korlátját

Általában a kapcsolódási problémák az Azure SQL Database segítségével osztályozzák:

* [Átmeneti hibák (rövid ideig tartó vagy időszakos)](#troubleshoot-transient-errors)
* [Állandó és nem átmeneti hibák (, rendszeresen ismétlődő hibák)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Próbálja ki az Azure SQL Database kapcsolati hibák elhárítója

Ha egy adott kapcsolat hibát tapasztal, próbálja meg [ezzel az eszközzel](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), amely segítségével gyorsan identitás és a probléma megoldása érdekében.

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibák elhárítása

Amikor egy alkalmazás egy Azure SQL Database-adatbázishoz csatlakozik, a következő hibaüzenet jelenhet meg:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Ez a hibaüzenet akkor általában átmeneti jellegű (ideiglenes).
> 
> 

Ez a hiba akkor fordul elő, ha folyamatban van, az adatbázis áthelyezése (vagy konfigurálja újra) és az alkalmazás megszakad a kapcsolat, az adatbázishoz. Adatbázis újrakonfigurálása események történnek egy esemény (például szoftverfrissítésre) vagy nem tervezett esemény (például egy folyamat összeomlása vagy terheléselosztás) miatt. A legtöbb újrakonfigurálás események általában rövid ideig tartó, és legfeljebb 60 másodpercnél rövidebb a elvégzésének. Azonban ezek az események időnként több időt vesz igénybe fejeződik be, például ha a olyan nagy tranzakciót egy hosszú ideig futó helyreállítás miatt.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Átmeneti kapcsolódási problémák megoldásának lépései

1. Ellenőrizze a [a Microsoft Azure-szolgáltatások irányítópultját](https://azure.microsoft.com/status) ismert szolgáltatáskimaradások, mely során a jelenített meg az alkalmazás az idő alatt bekövetkezett.
2. Az alkalmazásokat, amelyek csatlakozni egy felhőalapú szolgáltatás, például Azure SQL Database rendszeres újrakonfigurálás események várható kell, és megvalósítása újrapróbálkozási logika kezelje ezeket a hibákat, felszínre hozza a ezeket az adatokat az alkalmazáshibák felhasználók helyett. Tekintse át a [átmeneti hibák](sql-database-connectivity-issues.md) szakasz és az ajánlott eljárások és tervezési útmutató a [SQL Database fejlesztési áttekintése](sql-database-develop-overview.md) kapcsolatos további információkért és általános újrapróbálkozási stratégiák. Tekintse meg a Kódminták [adatkapcsolattárak az SQL Database és SQL Server](sql-database-libraries.md) bírálattal.
3. Egy adatbázis megközelíti az erőforrás-korlátozások, ahogy azt a is úgy tűnik, hogy egy átmeneti kapcsolati probléma. Lásd: [erőforráskorlátok](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Ha csatlakozási problémák továbbra is, vagy ha, amelynek az alkalmazás a hiba lép fel, mint 60 másodperc, vagy ha a hiba több példányban jelenik meg egy adott napon egy Azure-támogatáskérést fájl kiválasztásával **első támogatja**a a [Azure-támogatási](https://azure.microsoft.com/support/options) hely.

## <a name="troubleshoot-persistent-errors"></a>Az állandó hibák elhárítása
Ha az alkalmazás nem állandó csatlakozás az Azure SQL Database, azt általában azt jelzi, hogy probléma az alábbi lehetőségek közül:

* Tűzfal-konfiguráció. Az Azure SQL database vagy az ügyféloldali tűzfal nem blokkolja az Azure SQL Database-kapcsolatokat.
* Az ügyféloldalon újrakonfigurálás hálózati: például az új IP-cím vagy a proxykiszolgálót.
* Felhasználói hiba: például elírt kapcsolati paramétereket, például a kiszolgáló nevét a kapcsolati karakterláncban.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Állandó kapcsolattal összefüggő problémák megoldásának lépései
1. Állítsa be a [tűzfalszabályok](sql-database-configure-firewall-settings.md) lehetővé teszi az ügyfél IP-címet. Ideiglenes tesztelési célból beállítása egy tűzfalszabályt 0.0.0.0 használja, mint a kezdő IP-címtartományt, és a 255.255.255.255 használja, mint a záró IP-címtartományt. Ekkor megnyílik a kiszolgáló összes IP-címet. A kapcsolódási probléma így megoldódik, amennyiben ez a szabály eltávolításához, és hozzon létre egy tűzfalszabályt megfelelően korlátozott IP-címet vagy címtartományt. 
2. Az ügyfél és az Internet közötti összes tűzfalnak győződjön meg arról, hogy 1433-as port meg nyitva a kimenő kapcsolatok számára. Felülvizsgálat [SQL Server-hozzáféréshez a Windows tűzfal beállításainak](https://msdn.microsoft.com/library/cc646023.aspx) és [hibrid identitáshoz szükséges portok és protokollok](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) a további portokat, meg kell nyitnia az Azure-hoz kapcsolódó további mutatók Active Directory-hitelesítéssel.
3. Ellenőrizze a kapcsolati karakterláncot, és egyéb kapcsolati beállításokat. A kapcsolati karakterlánc című a [csatlakozási problémák témakör](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Ellenőrizze a szolgáltatás állapotát az irányítópulton. Ha úgy gondolja, hogy van egy regionális kimaradás, [helyreállítás szolgáltatáskimaradás](sql-database-disaster-recovery.md) a lépéseket egy új régióban helyreállításához.

## <a name="next-steps"></a>További lépések
* [Keresés a dokumentációban a Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Az Azure SQL Database szolgáltatás legújabb frissítéseinek megtekintése](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>További források
* [Az SQL Database fejlesztési áttekintése](sql-database-develop-overview.md)
* [Átmeneti hibakezelési vonatkozó általános útmutató](../best-practices-retry-general.md)
* [Adatkapcsolattárak az SQL Database és SQL Server](sql-database-libraries.md)


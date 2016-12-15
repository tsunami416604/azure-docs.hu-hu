---
title: "Az SQL Database-tűzfalszabályok áttekintése | Microsoft Docs"
description: "Ismerje meg, hogyan konfigurálhat egy SQL Database-tűzfalat a hozzáférések kezelése céljából kiszolgálói és adatbázisszintű tűzfalszabályokkal."
keywords: "adatbázistűzfal"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: auth and access
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/23/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: b5417e02f2c4e50c89afcfa007ccdd208775f374


---
# <a name="overview-of-azure-sql-database-firewall-rules"></a>Az Azure SQL Database-tűzfalszabályok áttekintése 
> [!div class="op_single_selector"]
> * [Áttekintés](sql-database-firewall-configure.md)
> * [Azure Portal](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

A Microsoft Azure SQL Database egy relációs adatbázis-szolgáltatást nyújt az Azure és egyéb internetalapú alkalmazások számára. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. Tűzfalszabályok kiszolgáló- és adatbázisszinten is létrehozhatók.

* **Kiszolgálószintű tűzfalszabályok:** Ezek a szabályok hozzáférést biztosítanak az ügyfelek számára a teljes Azure SQL Serverhez, azaz az egyazon logikai kiszolgálón található összes adatbázishoz. Ezek a szabályok a **fő** adatbázisban vannak tárolva. A kiszolgálószintű tűzfalszabályok a portálon vagy Transact-SQL utasításokkal konfigurálhatók.
* **Adatbázisszintű tűzfalszabályok:** Ezek a szabályok hozzáférést biztosítanak ügyfelek számára az Azure SQL Serveren belül található egyes adatbázisokhoz. Ezek a szabályok külön létrehozhatók minden adatbázishoz, és az egyes adatbázisokban vannak tárolva. (Létrehozhatók adatbázisszintű tűzfalszabályok a **fő** adatbázishoz is.) Ezek a szabályok akkor hasznosak, ha az egyazon logikai kiszolgálón található adatbázisok közül bizonyos (biztonságos) adatbázisokhoz való hozzáférést szeretné korlátozni. Az adatbázisszintű tűzfalszabályok kizárólag Transact-SQL utasításokkal konfigurálhatók.

**Javaslat:** A Microsoft a biztonság és az adatbázis hordozhatóságának fokozása érdekében adatbázisszintű tűzfalszabályok használatát javasolja, amikor erre lehetőség van. Kiszolgálószintű tűzfalszabályokat használhat a rendszergazdákra vonatkozóan, vagy ha több, azonos hozzáférési követelményt támasztó adatbázissal rendelkezik, és nem szeretne időt tölteni az egyes adatbázisok egyenként való konfigurálásával.

> [!Note]
> Az üzletmenet folytonossága és a hordozható adatbázisok közötti kapcsolatról [a vészhelyreállítás hitelesítési követelményeit](sql-database-geo-replication-security-config.md)ismertető cikkből tájékozódhat.
>

## <a name="firewall-overview"></a>Tűzfal áttekintése
Kezdetben a tűzfal az Azure SQL Server összes Transact-SQL-hozzáférését blokkolja. Az Azure SQL Server használatának megkezdéséhez lépjen be az Azure Portalra, és határozzon meg egy vagy több kiszolgálószintű tűzfalszabályt, amely engedélyezi a hozzáférést az Azure SQL Serverhez. A tűzfalszabályok segítségével adja meg, hogy az internet felől mely IP-címtartományok engedélyezettek, valamint hogy az Azure-alkalmazások megpróbálhatnak-e csatlakozni az Azure SQL Serverhez.

Ha az Azure SQL Serveren csak egyetlen adatbázishoz szeretne hozzáférést biztosítani, akkor az adott adatbázishoz egy adatbázisszintű szabályt kell létrehoznia. Adjon meg egy olyan IP-címtartományt az adatbázis tűzfalszabályához, amely a kiszolgálószintű tűzfalszabályban megadott IP-címtartományon kívül van, és ügyeljen arra, hogy az ügyfél IP-címe az adatbázisszintű szabályban megadott tartományon belülre essen.

Az internetről és az Azure-ból érkező kapcsolódási kísérleteknek először át kell jutniuk a tűzfalon, mielőtt elérnék az Azure SQL Servert vagy az SQL Database-t, ahogyan az a következő ábrán látható:

   ![A tűzfal-konfigurációt bemutató ábra.][1]

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
Amikor egy számítógép megpróbál csatlakozni az adatbázis-kiszolgálóhoz az internetről, a tűzfal először a tűzfalszabályok teljes készletében ellenőrzi a kérés eredeti IP-címét.

* Ha a kérés IP-címe a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikében található, a tűzfal engedélyezi a csatlakozást az Azure SQL Database-kiszolgálóhoz.
* Ha a kérés IP-címe nem található meg a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikében sem, a tűzfal ellenőrzi az adatbázisszintű tűzfalszabályokat. Ha a kérés IP-címe az adatbázisszintű tűzfalszabályokban megadott tartományok egyikében található, a tűzfal csak ahhoz az adatbázishoz engedélyezi a csatlakozást, amelyik megegyező adatbázisszintű szabállyal rendelkezik.
* Ha a kérés IP-címe nem található meg a kiszolgálószintű vagy adatbázisszintű tűzfalszabályokban megadott tartományok egyikében sem, akkor a csatlakozási kísérlet meghiúsul.

> [!NOTE]
> Ha a helyi számítógépről szeretné elérni az Azure SQL Database-t, akkor ellenőrizze, hogy a hálózaton és a helyi számítógépen lévő tűzfal engedélyezi-e a kimenő kommunikációt az 1433-as TCP-porton keresztül.
> 

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Annak engedélyezéséhez, hogy az Azure-alkalmazások csatlakozhassanak az Azure SQL Serverhez, engedélyezni kell az Azure-kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Ha a csatlakozási kísérlet nem engedélyezett, a kérés nem éri el az Azure SQL Database-kiszolgálót.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

> [!NOTE]
>  További információért tekintse meg az [ADO.NET 4.5 és az SQL Database 1433-ason túli portjaival](sql-database-develop-direct-route-ports-adonet-v12.md) foglalkozó cikk az **SQL Database belső és külső kapcsolatait** ismertető szakaszát.
>  

## <a name="creating-the-first-server-level-firewall-rule"></a>Az első kiszolgálószintű tűzfalszabály létrehozása
Az első kiszolgálószintű tűzfalbeállítás az [Azure Portalon](https://portal.azure.com/), vagy szoftveresen, a REST API-val vagy az Azure PowerShell-lel hozható létre. A további kiszolgálószintű tűzfalszabályok ugyanezekkel a módszerekkel, vagy a Transact-SQL-lel hozhatók létre és kezelhetők. A teljesítmény javítása érdekében a kiszolgálószintű tűzfalszabályokat átmenetileg adatbázisszinten is gyorsítótárazza a rendszer. A gyorsítótár frissítésével kapcsolatban lásd: [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). További információk a kiszolgálószintű tűzfalszabályokról: [Útmutató: Azure SQL Server-tűzfal konfigurálása az Azure Portalon](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Adatbázisszintű tűzfalszabályok létrehozása
Az első kiszolgálószintű tűzfalszabály létrehozása után lehetséges, hogy korlátozni kívánja a hozzáférést bizonyos adatbázisokhoz. Ha egy olyan IP-címtartományt ad meg az adatbázisszintű tűzfalszabályban, amely kívül esik a kiszolgálószintű tűzfalszabályban megadott tartományon, akkor csak az adatbázisszinten megadott tartományba eső IP-címekkel rendelkező ügyfelek érhetik el az adatbázist. Egy adatbázishoz legfeljebb 128 adatbázisszintű tűzfalszabály adható meg. A főadatbázisra és a felhasználói adatbázisokra vonatkozó adatbázisszintű tűzfalszabályok a Transact-SQL-lel hozhatók létre és kezelhetők. További információk az adatbázisszintű tűzfalszabályok konfigurálásáról: [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
A tűzfalszabályok az Azure Portal mellett szoftveresen, a Transact-SQL, a REST API és az Azure PowerShell használatával is kezelhetők. A következő táblázatok ismertetik az egyes módszerek esetén használható parancsokat.

### <a name="transact-sql"></a>Transact-SQL
| Katalógusnézet vagy tárolt eljárás | Szint | Leírás |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályok megjelenítése |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Adatbázis |Az aktuális adatbázisszintű tűzfalszabályok megjelenítése |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Adatbázis |Adatbázisszintű tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Adatbázisok |Adatbázisszintű tűzfalszabályok eltávolítása |

### <a name="rest-api"></a>REST API
| API | Szint | Leírás |
| --- | --- | --- |
| [List Firewall Rules](https://msdn.microsoft.com/library/azure/dn505715.aspx) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályok megjelenítése |
| [Create Firewall Rule](https://msdn.microsoft.com/library/azure/dn505712.aspx) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok létrehozása vagy frissítése |
| [Set Firewall Rule](https://msdn.microsoft.com/library/azure/dn505707.aspx) |Kiszolgáló |Meglévő kiszolgálószintű tűzfalszabály tulajdonságainak frissítése |
| [Delete Firewall Rule](https://msdn.microsoft.com/library/azure/dn505706.aspx) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |

### <a name="azure-powershell"></a>Azure PowerShell
| Parancsmag | Szint | Leírás |
| --- | --- | --- |
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályokat adja vissza |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) |Kiszolgáló |Új kiszolgálószintű tűzfalszabály létrehozása |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) |Kiszolgáló |Meglévő kiszolgálószintű tűzfalszabály tulajdonságainak frissítése |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |

> [!NOTE]
> A tűzfalbeállításokon végrehajtott módosítások érvénybe lépéséig akár öt perc is eltelhet.
> 
> 

## <a name="troubleshooting-the-database-firewall"></a>Az adatbázistűzfal hibaelhárítása
A következő szempontokat vegye figyelembe, ha a Microsoft Azure SQL Database szolgáltatáshoz való hozzáférése nem a várt módon működik:

* **Helyi tűzfal konfigurációja:** Mielőtt a számítógépe hozzáférne az Azure SQL Database szolgáltatáshoz, lehet, hogy létre kell hoznia egy tűzfalkivételt a számítógépén az 1433-as TCP-port számára. Ha az Azure-felhő határain belül létesít kapcsolatokat, előfordulhat, hogy további portokat is meg kell nyitnia. További információért tekintse meg az [ADO.NET 4.5 és az SQL Database V12 1433-ason túli portjaival](sql-database-develop-direct-route-ports-adonet-v12.md) foglalkozó cikk az **SQL Database V12 belső és külső kapcsolatait** ismertető szakaszát.
* **Hálózati címfordítás (NAT):** A NAT miatt a számítógépe által az Azure SQL Database szolgáltatáshoz való csatlakozáshoz használt IP-cím eltérhet a számítógép IP-konfigurációs beállításai között megjelenő IP-címtől. A számítógép által az Azure-hoz való csatlakozáshoz használt IP-cím megtekintéséhez jelentkezzen be a portálra, és lépjen az adatbázist futtató kiszolgáló **Konfigurálás** lapjára. Az **Engedélyezett IP-címek** szakasz alatt jelenik meg az **Ügyfél aktuális IP-címe**. Kattintson a **Hozzáadás** lehetőségre az **Engedélyezett IP-címek** között, ha szeretné engedélyezni a számítógép számára a kiszolgáló elérését.
* **Az engedélyezési lista módosításai még nem léptek érvénybe:** Az Azure SQL Database tűzfalkonfigurációján végzett módosítások érvénybe lépéséig akár öt perc is eltelhet.
* **A bejelentkezés nem engedélyezett, vagy helytelen jelszó lett használva:** Ha egy bejelentkezés nem rendelkezik engedélyekkel az Azure SQL Database-kiszolgálón, vagy a használt jelszó érvénytelen, akkor a rendszer megtagadja az Azure SQL Database-kiszolgálóhoz való csatlakozást. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat. A bejelentkezések előkészítésével kapcsolatos további információkért lásd az adatbázisok, bejelentkezések és felhasználók Azure SQL Database-ben történő kezelésével foglalkozó cikket.
* **Dinamikus IP-cím**: Ha az internetkapcsolata dinamikus IP-címkezeléssel rendelkezik, és problémákat okoz a tűzfalon való átjutás, próbálja ki a következő megoldások valamelyikét:
  
  * Kérje el az internetszolgáltatójától az Azure SQL Database-kiszolgáló eléréséhez használt ügyfélszámítógépeihez társított IP-címtartományt, majd adja meg ezt az IP-címtartományt egy tűzfalszabályként.
  * Állítson be statikus IP-címeket az ügyfélszámítógépei számára, majd adja meg az IP-címeket tűzfalszabályokként.

## <a name="next-steps"></a>Következő lépések
A kiszolgálószintű és adatbázisszintű tűzfalszabályok létrehozásával kapcsolatban tekintse meg az alábbi cikkeket:

* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása az Azure Portalon](sql-database-configure-firewall-settings.md)
* [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok konfigurálása a T-SQL használatával](sql-database-configure-firewall-settings-tsql.md)
* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása a PowerShell-lel](sql-database-configure-firewall-settings-powershell.md)
* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása a REST API használatával](sql-database-configure-firewall-settings-rest.md)

Az adatbázisok létrehozásával foglalkozó oktatóanyag: [SQL Database-adatbázis létrehozása pillanatok alatt az Azure Portalon](sql-database-get-started.md).
Ha nyílt forráskódú vagy külső alkalmazásokból szeretne kapcsolódni az Azure SQL Database-hez, lásd az [SQL Database gyors üzembe helyezési ügyfélkódmintáival](https://msdn.microsoft.com/library/azure/ee336282.aspx) foglalkozó cikket.
Az adatbázisok megnyitásával kapcsolatban az [adatbázis-hozzáférés és a bejelentkezési biztonság kezelésével](https://msdn.microsoft.com/library/azure/ee336235.aspx) foglalkozó cikkben tájékozódhat.

## <a name="additional-resources"></a>További források
* [Az adatbázis biztonságossá tétele](sql-database-security.md)
* [Security Center az SQL Server adatbázismotorhoz és az Azure SQL Database-hez](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png



<!--HONumber=Dec16_HO1-->



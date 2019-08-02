---
title: A Azure SQL Database DNS-aliasa | Microsoft Docs
description: Az alkalmazások csatlakozni tudnak a Azure SQL Database-kiszolgáló nevéhez. Eközben megváltoztathatja az aliasok SQL Databaseét bármikor, a tesztelés megkönnyítéséhez és így tovább.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi, ayolubek, jrasnick
ms.date: 06/26/2019
ms.openlocfilehash: 3d0a4b5890ed5758f4045459815fb4ebbffe75c6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68550656"
---
# <a name="dns-alias-for-azure-sql-database"></a>A Azure SQL Database DNS-aliasa

Azure SQL Database DNS-kiszolgálóval rendelkezik. A PowerShell és a REST API-k fogadják a [DNS-aliasok létrehozásához és kezeléséhez szükséges hívásokat](#anchor-powershell-code-62x) a SQL Database-kiszolgálónévhez.

A Azure SQL Database kiszolgáló nevének helyett *DNS-aliast* lehet használni. Az ügyfélalkalmazások használhatják az aliast a kapcsolataik karakterláncában. A DNS-alias olyan fordítási réteget biztosít, amely különböző kiszolgálókra irányítja át az ügyfélszoftvereket. Ebben a rétegben az összes ügyfél és a hozzájuk tartozó kapcsolódási karakterláncok megtalálásának és szerkesztésének nehézségei merülnek fel.

A DNS-aliasok gyakori felhasználási módjai a következők lehetnek:

- Hozzon létre egy könnyen megjegyezhető nevet egy Azure-SQL Server számára.
- A kezdeti fejlesztés során az alias egy teszt SQL Database-kiszolgálóra is hivatkozhat. Ha az alkalmazás életbe lép, módosíthatja az aliast, hogy az éles kiszolgálóra hivatkozzon. A tesztről a termelésre való áttérés nem igényli a konfigurációk módosítását az adatbázis-kiszolgálóhoz csatlakozó több ügyfél esetében.
- Tegyük fel, hogy az alkalmazás egyetlen adatbázisát áthelyezi egy másik SQL Database-kiszolgálóra. Itt módosíthatja az aliast anélkül, hogy módosítania kellene számos ügyfél konfigurációját.
- Regionális leállás esetén a Geo-visszaállítás használatával helyreállíthatja az adatbázist egy másik kiszolgálón és régióban. A meglévő alias módosítható úgy, hogy az új kiszolgálóra mutasson, hogy a meglévő ügyfélalkalmazás újra csatlakozhasson hozzá. 

## <a name="domain-name-system-dns-of-the-internet"></a>Az Internet Domain Name System (DNS)

Az Internet a DNS-re támaszkodik. A DNS lefordítja a felhasználóbarát neveket a Azure SQL Database-kiszolgáló nevére.

## <a name="scenarios-with-one-dns-alias"></a>Forgatókönyvek egyetlen DNS-aliassal

Tegyük fel, hogy a rendszerét egy új Azure SQL Database-kiszolgálóra kell váltania. A múltban az összes ügyfélprogram összes kapcsolódási karakterláncának megkereséséhez és frissítéséhez szükséges. Ha azonban a kapcsolatok karakterlánca DNS-aliast használ, csak az alias tulajdonságot kell frissíteni.

A Azure SQL Database DNS-alias szolgáltatása a következő helyzetekben nyújt segítséget:

### <a name="test-to-production"></a>Tesztelés éles környezetben

Az ügyfélalkalmazások fejlesztésének megkezdése előtt használjon DNS-aliast a kapcsolódási karakterláncokban. Az alias tulajdonságai a Azure SQL Database-kiszolgáló tesztelési verziójára mutatnak.

Később, amikor az új rendszer éles környezetben él, frissítheti az alias tulajdonságait úgy, hogy az éles SQL Database kiszolgálóra mutasson. Nincs szükség az ügyfélalkalmazások módosítására.

### <a name="cross-region-support"></a>Régiók közötti támogatás

A vész-helyreállítás a SQL Database-kiszolgálót egy másik földrajzi régióra is átválthatja. A DNS-aliast használó rendszerek esetében az összes ügyfél kapcsolódási karakterláncának megkeresése és frissítése elkerülhető. Ehelyett frissítheti az aliast úgy, hogy az új SQL Database-kiszolgálót használja, amely most már az adatbázist futtatja.

## <a name="properties-of-a-dns-alias"></a>DNS-alias tulajdonságai

A következő tulajdonságok a SQL Database-kiszolgáló minden DNS-aliasára érvényesek:

- *Egyedi név:* Minden létrehozott alias egyedi az összes Azure SQL Database-kiszolgálón, ugyanúgy, mint a kiszolgálók nevei.
- *A kiszolgáló megadása kötelező:* Nem hozható létre DNS-alias, ha pontosan egy kiszolgálóra hivatkozik, és a kiszolgálónak már léteznie kell. A frissített aliasoknak mindig pontosan egy meglévő kiszolgálóra kell hivatkoznia.
  - SQL Database-kiszolgáló eldobásakor az Azure-rendszer az összes olyan DNS-aliast is eldobja, amely a kiszolgálóra hivatkozik.
- *Nincs a régióhoz kötve:* A DNS-aliasok nincsenek régióhoz kötve. Bármely DNS-alias frissíthető úgy, hogy egy Azure SQL Database-kiszolgálóra hivatkozzon, amely bármely földrajzi régióban található.
  - Ha azonban egy alias frissítése egy másik kiszolgálóra hivatkozik, mindkét kiszolgálónak ugyanabban az Azure-előfizetésbenkell lennie.
- *Engedélyek* A DNS-aliasok kezeléséhez a felhasználónak *kiszolgálói közreműködői* engedélyekkel kell rendelkeznie vagy magasabbnak kell lennie. További információ: Bevezetés a [szerepköralapú Access Control használatába a Azure Portal](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>A DNS-aliasok kezelése

A REST API-k és a PowerShell-parancsmagok is elérhetők, így lehetővé válik a DNS-aliasok programozott kezelése.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API-k a DNS-aliasok kezeléséhez

A REST API-k dokumentációja a következő webes hely közelében érhető el:

- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)

Emellett a REST API-k a GitHubon is megtekinthetők:

- [Azure SQL Database kiszolgáló, DNS-alias REST API-k](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell a DNS-aliasok kezeléséhez

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A REST API-kat meghívó PowerShell-parancsmagok érhetők el.

A DNS-aliasok kezeléséhez használt PowerShell-parancsmagok például a következő címen vannak dokumentálva:

- [PowerShell DNS-aliashoz Azure SQL Database](dns-alias-powershell.md)

A kódban használt parancsmagok a következők:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Új DNS-Alias létrehozása a Azure SQL Database szolgáltatási rendszerben. Az alias az 1. Azure SQL Database kiszolgálóra hivatkozik.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Az SQL DB Server 1 rendszerhez rendelt összes DNS-alias beolvasása és listázása.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Módosítja annak a kiszolgálónak a nevét, amelyre az alias hivatkozik, az 1. kiszolgálóról az SQL DB Server 2 értékre.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Távolítsa el a DNS-aliast az SQL DB Server 2-ből az alias nevével.

## <a name="limitations-during-preview"></a>Korlátozások az előzetes verzióban

A DNS-aliasok jelenléte a következő korlátozásokkal jár:

- *Legfeljebb 2 perc késés:* A DNS-alias frissítése vagy eltávolítása akár 2 percet is igénybe vehet.
  - A rövid késleltetéstől függetlenül az alias azonnal leállítja a hivatkozó ügyfélkapcsolatokat a régi kiszolgálóval.
- *DNS-címkeresés:* Egyelőre az egyetlen mérvadó módszer annak ellenőrzéséhez, hogy egy adott DNS-alias melyik kiszolgálóra hivatkozik a [DNS-címkeresés](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup)végrehajtásával.
- _A tábla naplózása nem támogatott:_ Nem használhat DNS-aliast olyan Azure SQL Database-kiszolgálón, amelyen engedélyezve van a *tábla naplózása* egy adatbázison.
  - A tábla naplózása elavult.
  - Javasoljuk, hogy térjen át a [blob](sql-database-auditing.md)-naplózásra.

## <a name="related-resources"></a>Kapcsolódó erőforrások

- [A Azure SQL Database üzleti folytonosságának áttekintése](sql-database-business-continuity.md), beleértve a vész-helyreállítást is.

## <a name="next-steps"></a>További lépések

- [PowerShell DNS-aliashoz Azure SQL Database](dns-alias-powershell.md)

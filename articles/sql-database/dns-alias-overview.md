---
title: DNS-alias
description: Az alkalmazások csatlakozhatnak az Azure SQL Database-kiszolgáló nevének aliasához. Eközben módosíthatja az SQL Database az alias mutat bármikor, a tesztelés megkönnyítése érdekében, és így tovább.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 05fa542a0ad1c72f73148eefd304a9771798598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820609"
---
# <a name="dns-alias-for-azure-sql-database"></a>AZ Azure SQL Database DNS-aliasa

Az Azure SQL Database dns-kiszolgálóval rendelkezik. A PowerShell- és REST-API-k hívásokat fogadnak az SQL Database-kiszolgáló nevéhez [tartozó DNS-aliasok létrehozásához és kezeléséhez.](#anchor-powershell-code-62x)

Az Azure SQL Database-kiszolgáló neve helyett *DNS-alias* használható. Az ügyfélprogramok használhatják az aliast a kapcsolati karakterláncokban. A DNS-alias fordítási réteget biztosít, amely átirányíthatja az ügyfélprogramokat különböző kiszolgálókra. Ez a réteg megkíméli Önt a nehézségeket kelljen megtalálni és szerkeszteni az összes ügyfél és a kapcsolat húrok.

A DNS-aliasok gyakori felhasználási felhasználási céljai a következő esetek:

- Hozzon létre egy könnyen megjegyezhető nevet egy Azure SQL Server számára.
- A kezdeti fejlesztés során az alias hivatkozhat egy teszt SQL Database-kiszolgálóra. Amikor az alkalmazás éles állapotba kerül, módosíthatja az aliast, hogy az éles kiszolgálóra hivatkozzon. A tesztről az éles környezetre való áttérés nem igényel módosítást a konfigurációkon, amelyek több ügyfelet csatlakoztatnak az adatbázis-kiszolgálóhoz.
- Tegyük fel, hogy az alkalmazás egyetlen adatbázisa átkerül egy másik SQL Database-kiszolgálóra. Itt több ügyfél konfigurációjának módosítása nélkül módosíthatja az aliast.
- A regionális kimaradás során geo-visszaállítás segítségével helyreállíthatja az adatbázist egy másik kiszolgálón és régióban. Módosíthatja a meglévő aliast úgy, hogy az új kiszolgálóra mutasson, hogy a meglévő ügyfélalkalmazás újra csatlakozhasson hozzá. 

## <a name="domain-name-system-dns-of-the-internet"></a>Az internet DNS-rendszere

Az internet a DNS-re támaszkodik. A DNS lefordítja a rövid neveket az Azure SQL Database-kiszolgáló nevére.

## <a name="scenarios-with-one-dns-alias"></a>Egyetlen DNS-aliassal rendelkező esetek

Tegyük fel, hogy át kell váltania a rendszert egy új Azure SQL Database-kiszolgálóra. A múltban meg kellett találnia és frissítenie kellett minden kapcsolati karakterláncot minden ügyfélprogramban. De most, ha a kapcsolati karakterláncok DNS-aliast használnak, csak egy alias tulajdonságot kell frissíteni.

Az Azure SQL Database DNS-aliasszolgáltatása a következő esetekben segíthet:

### <a name="test-to-production"></a>Tesztelés a termelésig

Amikor elkezdi fejleszteni az ügyfélprogramokat, a kapcsolati karakterláncokban DNS-aliast kell használniuk. Az alias tulajdonságai az Azure SQL Database-kiszolgáló tesztverziójára mutatnak.

Később, amikor az új rendszer éles környezetben éles környezetben lép fel, frissítheti az alias tulajdonságait, hogy az éles SQL Database-kiszolgálóra mutasson. Nincs szükség az ügyfélprogramok módosítására.

### <a name="cross-region-support"></a>Régiók közötti támogatás

A vészhelyreállítás áthelyezheti az SQL Database-kiszolgálót egy másik földrajzi régióba. A DNS-aliast használó rendszer esetében elkerülhető az összes ügyfél összes kapcsolati karakterláncának megkeresése és frissítése. Ehelyett frissítheti az aliast, hogy az új SQL Database-kiszolgálóra hivatkozzon, amely most az adatbázist üzemelteti.

## <a name="properties-of-a-dns-alias"></a>DNS-alias tulajdonságai

Az SQL Database-kiszolgáló minden DNS-aliasára a következő tulajdonságok vonatkoznak:

- *Egyedi név:* Minden létrehozott aliasnév egyedi az összes Azure SQL Database-kiszolgálón, csakúgy, mint a kiszolgálónevek.
- *Kiszolgáló szükséges:* Dns-alias csak akkor hozható létre, ha pontosan egy kiszolgálóra hivatkozik, és a kiszolgálónak már léteznie kell. A frissített aliasnak mindig pontosan egy meglévő kiszolgálóra kell hivatkoznia.
  - Sql Database-kiszolgáló eldobásakor az Azure-rendszer a kiszolgálóra hivatkozó összes DNS-aliast is eldobja.
- *Nem kötődik egyetlen régióhoz sem:* A DNS-aliasok nincsenek régióhoz kötve. Bármely DNS-alias frissíthető, hogy egy Azure SQL Database-kiszolgálóra hivatkozzon, amely bármely földrajzi régióban található.
  - Azonban egy alias frissítésekor egy másik kiszolgálóra való hivatkozáskor mindkét kiszolgálónak ugyanabban az *Azure-előfizetésben*kell lennie.
- *Engedélyek:* A DNS-alias kezeléséhez a felhasználónak *kiszolgálóközreműködői* engedélyekkel vagy magasabb szintű jogosultságokkal kell rendelkeznie. További információ: [A szerepköralapú hozzáférés-vezérlés az Azure Portalon](../role-based-access-control/overview.md)című témakörben található.

## <a name="manage-your-dns-aliases"></a>A DNS-aliasok kezelése

Mind a REST API-k, mind a PowerShell-parancsmagok lehetővé teszik a DNS-aliasok programozott kezelését.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API-k a DNS-aliasok kezeléséhez

A REST API-k dokumentációja a következő webhely közelében érhető el:

- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)

Emellett a REST API-k a GitHubon is láthatók:

- [Azure SQL Database server, DNS alias REST API-k](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell a DNS-aliasok kezeléséhez

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

PowerShell-parancsmagok érhetők el, amelyek meghívják a REST API-kat.

A DNS-aliasok kezelésére használt PowerShell-parancsmagok kódpéldája a következő helyen van dokumentálva:

- [PowerShell dns aliashoz az Azure SQL Database-hez](dns-alias-powershell.md)

A példakódpéldában használt parancsmagok a következők:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Új DNS-aliast hoz létre az Azure SQL Database szolgáltatásrendszerben. Az alias az Azure SQL Database Server 1-re hivatkozik.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Az SQL DB 1 kiszolgálóhoz rendelt összes DNS-alias bekéselése és listázása.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Módosítja azt a kiszolgálónevet, amelyre az alias hivatkozik, az 1-es kiszolgálótól az SQL DB 2 kiszolgálóig.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Távolítsa el a DNS-aliast az SQL DB kiszolgálóról 2, az alias nevének használatával.

## <a name="limitations-during-preview"></a>Korlátozások az előnézet során

Jelenleg a DNS-alias a következő korlátozásokkal rendelkezik:

- *Legfeljebb 2 perces késés:* A DNS-alias frissítése vagy eltávolítása akár 2 percet is igénybe vesz.
  - A rövid késleltetéstől függetlenül az alias azonnal leállítja az ügyfélkapcsolatok nak az örökölt kiszolgálóra való átvetését.
- *DNS-keresése:* Egyelőre az egyetlen mérvadó módja annak, hogy ellenőrizze, hogy egy adott DNS-alias milyen kiszolgálóra hivatkozik, az a [DNS-keresése](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- _A táblanaplózás nem támogatott:_ Nem használhat DNS-aliast olyan Azure SQL Database-kiszolgálón, amelyen *a táblanaplózás* engedélyezve van az adatbázisban.
  - A táblanaplózás elavult.
  - Javasoljuk, hogy lépjen át a [Blob Auditing térre.](sql-database-auditing.md)

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [Az Azure SQL Database üzletmenet-folytonosságának áttekintése,](sql-database-business-continuity.md)beleértve a vészhelyreállítást is.

## <a name="next-steps"></a>További lépések

- [PowerShell dns aliashoz az Azure SQL Database-hez](dns-alias-powershell.md)

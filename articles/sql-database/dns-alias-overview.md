---
title: DNS-alias az Azure SQL Database |} Microsoft Docs
description: "Az alkalmazások alias nevét az Azure SQL Database-kiszolgálóhoz csatlakozhat. Eközben a alias mutat bármikor, és így tovább tesztelés elősegítése érdekében az SQL-adatbázis bármikor módosíthatja."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: DNS alias
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 01/31/2018
ms.reviewer: dmalik;ayolubek
ms.author: genemi
ms.openlocfilehash: ef888763918447b9e5f97288d90e287441a11cc6
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="dns-alias-for-azure-sql-database"></a>Az Azure SQL Database DNS-alias

Az Azure SQL Database van tartománynévrendszer (DNS) kiszolgáló. PowerShell és a REST API-k elfogadása [létrehozásához és kezeléséhez a DNS-aliasokat Hívások](#anchor-powershell-code-62x) számára az SQL adatbázis-kiszolgáló nevét.

A *DNS-alias* az Azure SQL adatbázis-kiszolgáló neve helyett használható. Ügyfélprogramok használhatják az alias a kapcsolati karakterláncokat. A DNS alias biztosít, amelyek különböző kiszolgálókra irányíthatja át a ügyfélprogramok fordítási réteget. Ez a réteg megkíméli és az ügyfelek és a kapcsolati karakterláncok szerkesztése nehézségeit.

A DNS-alias gyakori használati többek között a következő esetekben:

- Hozzon létre egy könnyen megjegyezhető nevet az Azure SQL Server.
- Kezdeti fejlesztése során az alias jelentheti a test SQL adatbázis-kiszolgáló. Amikor az alkalmazás élő, módosíthatja az alias lehet hivatkozni az üzemi kiszolgálón. Teszt termelési az áttérést a konfigurációinak módosításairól több az ügyfeleknek nem kell, hogy az adatbázis-kiszolgálóhoz.
- Tegyük fel, hogy az alkalmazás csak adatbázis áthelyezése egy másik SQL Database-kiszolgálóhoz. Itt módosíthatja a alias több ügyfél beállításait a módosítása nélkül.

#### <a name="domain-name-system-dns-of-the-internet"></a>Tartománynévrendszer (DNS) az Internet

Az interneten a DNS támaszkodik. A DNS a rövid nevek fordítja le az Azure SQL adatbázis-kiszolgáló nevét.

## <a name="scenarios-with-one-dns-alias"></a>Forgatókönyvek rendszert egy DNS-alias

Tegyük fel, a rendszer vált egy új Azure SQL Database-kiszolgálóhoz. A múltban kellett található és frissíteni a minden ügyfél programban minden kapcsolati karakterláncot. Azonban, ha a kapcsolati karakterláncok használata DNS-alias, csak egy aliast tulajdonság frissíteni kell.

Az Azure SQL-adatbázis DNS-alias szolgáltatás segítségével a következő esetekben:

#### <a name="test-to-production"></a>Ellenőrizze, hogy az üzemi

A ügyfélprogramok elkezdje kell őket a DNS-alias használata a kapcsolati karakterláncokat. Az alias pont tulajdonságai között ahhoz, hogy az Azure SQL Database-kiszolgálóhoz teszt verziójára elvégezte.

Később új kerül élő éles környezetben, amikor az aliast, mutasson az üzemi SQL adatbázis-kiszolgáló tulajdonságainak frissítheti. Szükség a ügyfélprogramok nem változik.

#### <a name="cross-region-support"></a>Kereszt-terület támogatása

Vész-helyreállítási előfordulhat, hogy a különböző földrajzi régió mellett az SQL Database-kiszolgálóhoz. A rendszer használt DNS-alias, mint a kell megkeresnie és az összes kapcsolati karakterláncait frissítheti az összes ügyfél elkerülhető. Ehelyett utal, hogy az új SQL-adatbázis most tároló kiszolgáló nevét az adatbázis alias segítségével frissítheti.




## <a name="properties-of-a-dns-alias"></a>A DNS-alias tulajdonságairól

Az SQL Database-kiszolgálóhoz tartozó minden DNS-alias alkalmazása a következő tulajdonságokkal:

- *Egyedi nevet:* minden aliasnév hoz létre egyedi Azure SQL Database-kiszolgálók közötti, ugyanúgy, mint a kiszolgáló nevét.

- *Kiszolgálóra szükség:* A DNS alias hozható létre, ha pontosan egy kiszolgáló hivatkozik, és a kiszolgáló már léteznie kell. Frissített alias mindig kell hivatkoznia pontosan egy meglévő kiszolgálóra.
    - Amikor egy SQL Database-kiszolgálóhoz, az Azure rendszer is csökken, tekintse meg a kiszolgáló összes DNS-alias.

- *Minden régió nincs kötve:* DNS-aliasok nem kötött egy régiót. A DNS-aliasokat lehet hivatkozni egy Azure SQL adatbázis-kiszolgáló, amely a földrajzi régióban található lehet frissíteni.
    - Azonban egy másik kiszolgálóra utal alias frissítésekor mindkét kiszolgáló már léteznie kell az azonos Azure *előfizetés*.

- *Engedélyek:* tudja felügyelni a DNS-aliast, a felhasználónak rendelkeznie kell *Server közreműködői* engedélyeket, vagy újabb verzióját. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure-portálon az első lépései](../active-directory/role-based-access-control-what-is.md).





## <a name="manage-your-dns-aliases"></a>A DNS-aliasokat kezelése

Ahhoz, hogy a DNS-aliasokat programozott módon kezelése mind a REST API-kat, és a PowerShell-parancsmagok érhetők el.

#### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API-k, a DNS-aliasokat kezeléséhez

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

A REST API-k dokumentációjában mellett a következő webhelyen érhető el:
- [Az Azure SQL Database REST API-n](https://docs.microsoft.com/rest/api/sql/)

Emellett a REST API-k a Githubon: láthatók:
- [Az Azure SQL Database-kiszolgálóhoz, a DNS alias REST API-k](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>A DNS-aliasokat kezelésére szolgáló PowerShell

PowerShell-parancsmagok érhetők el, amely a REST API-k hívása.

Egy példa a PowerShell-parancsmagok felügyelik a DNS-aliasokat részletes ismertetését lásd:
- [A DNS-Alias az Azure SQL Database PowerShell](dns-alias-powershell.md)


A példakódban a parancsmagok a következők:
- [Új AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): egy új DNS-alias létrehozása az Azure SQL Database szolgáltatás rendszerben. Az alias az Azure SQL Database-kiszolgálóhoz 1 hivatkozik.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Get, és az SQL adatbázis-kiszolgálót 1 rendelt összes DNS-alias listája.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): módosítja a kiszolgáló nevét, amely az alias konfigurálva van tekintse meg, SQL DB server 2 1-kiszolgálóról.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): a DNS-alias eltávolítása SQL adatbázis-kiszolgálót, 2, a-alias nevét.


Az előző parancsmagok lettek hozzáadva a **AzureRM.Sql** 5.1.1-es modul verziójától kezdve modul.




## <a name="limitations-during-preview"></a>Előzetes korlátozásai

Jelenleg a DNS alias rendelkezik a következő korlátozások vonatkoznak:

- *Legfeljebb 2 perces késleltetés:* frissíti vagy eltávolítja a DNS-alias legfeljebb 2 percet vesz igénybe.
    - Bármely rövid késleltetés, függetlenül az alias azonnal leállítja az ügyfélkapcsolatokat átállítja a korábbi kiszolgáló utaló.

- *DNS-címkeresés:* egyelőre csak mérvadó úgy a végrehajtásával ellenőrizze, milyen kiszolgáló, egy adott DNS alias hivatkozik egy [DNS-címkeresés](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).

- *[Tábla naplózás nem támogatott](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* egy Azure SQL adatbázis-kiszolgáló, amely rendelkezik a DNS-alias nem használható *tábla naplózás* adatbázison engedélyezve van.
    - Tábla naplózás elavult.
    - Azt javasoljuk, hogy helyez át [Blobnaplózási funkció](sql-database-auditing.md).




## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [Az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md), beleértve a vész-helyreállítási.



## <a name="next-steps"></a>További lépések

- [A DNS-Alias az Azure SQL Database PowerShell](dns-alias-powershell.md)


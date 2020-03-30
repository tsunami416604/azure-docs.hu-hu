---
title: Azure SQL VM-kapcsolat a keresési indexeléshez
titleSuffix: Azure Cognitive Search
description: Engedélyezze a titkosított kapcsolatokat, és konfigurálja a tűzfalat, hogy engedélyezze az SQL Server-kiszolgálóhoz való csatlakozást egy Azure-beli virtuális gépen (VM) az Azure Cognitive Search indexelőjétől.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1ab2b7860e8a75da5f8acef2fc4fa54d4b73a30d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256963"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Kapcsolat konfigurálása egy Azure Cognitive Search indexelőtől az SQL Server kiszolgálóhoz egy Azure virtuális gépen

Az [Azure Cognitive Search indexelők használatával történő csatlakoztatása](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)című részében az indexelők létrehozása az SQL Server ellen az Azure virtuális **gépeken** (vagy röviden **SQL Azure virtuális gépeken)** az Azure Cognitive Search által támogatott, de van néhány biztonsággal kapcsolatos előfeltétel, amelyet először is el kell intézni. 

Az Azure Cognitive Search és az SQL Server közötti kapcsolatok egy virtuális gépen nyilvános internetkapcsolat. Itt is minden olyan biztonsági intézkedés érvényes, amelyet általában ezeken a kapcsolatokon követne:

+ Szerezzen be egy tanúsítványt egy [hitelesítésszolgáltatótól](https://en.wikipedia.org/wiki/Certificate_authority#Providers) az Sql Server-példány teljesen minősített tartománynevéhez az Azure virtuális gépen.
+ Telepítse a tanúsítványt a virtuális gépre, majd engedélyezze és konfigurálja a titkosított kapcsolatokat a virtuális gépen a jelen cikkben található utasítások alapján.

## <a name="enable-encrypted-connections"></a>Titkosított kapcsolatok engedélyezése
Az Azure Cognitive Search egy titkosított csatornát igényel az összes indexelő kérelmek nyilvános internetkapcsolaton keresztül. Ez a szakasz felsorolja a műveletek hez szükséges lépéseket.

1. Ellenőrizze a tanúsítvány tulajdonságait, hogy ellenőrizze, hogy a tulajdonos neve az Azure vm teljesen minősített tartományneve (FQDN). A tulajdonságok megtekintéséhez használhat olyan eszközt, mint a CertUtils vagy a Certificates beépülő modul. A teljes tartománynevet a Virtuálisgép-szolgáltatás szolgáltatás Essentials szakaszából, a **Nyilvános IP-cím/DNS-névcímke** mezőben, az [Azure Portalon](https://portal.azure.com/)kaphatja be.
   
   * Az újabb **Erőforrás-kezelő** sablonnal létrehozott virtuális gépek esetében a teljes tartományneve`<your-VM-name>.<region>.cloudapp.azure.com`
   * A **klasszikus** virtuális gépként létrehozott régebbi virtuális gépek esetében a `<your-cloud-service-name.cloudapp.net>`teljes tartományneve a .

2. Konfigurálja úgy az SQL Server kiszolgálót, hogy a tanúsítványt a Rendszerleíróadatbázis-szerkesztő (regedit) használatával használja. 
   
    Bár az SQL Server Configuration Manager gyakran használják ezt a feladatot, nem használhatja ebben a forgatókönyvben. Nem fogja megtalálni az importált tanúsítványt, mert a virtuális gép teljes tartományneve az Azure-ban nem egyezik meg a virtuális gép által meghatározott fqdn-nal (a tartományt a helyi számítógépként vagy a hálózati tartományként azonosítja, amelyhez csatlakozik). Ha a nevek nem egyeznek, a regedit segítségével adja meg a tanúsítványt.
   
   * A regedit ben keresse meg `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`a rendszerleíró kulcsot: .
     
     A `[MSSQL13.MSSQLSERVER]` rész verzió- és példánynévtől függően változik. 
   * Állítsa be a **tanúsítványkulcs** értékét a virtuális gépre importált TLS/SSL tanúsítvány **ujjlenyomatára.**
     
     Számos módja van, hogy az ujjlenyomat, néhány jobb, mint mások. Ha az MMC **Tanúsítványok** beépülő moduljából másolja, valószínűleg egy láthatatlan kezdő karaktert fog felvenni a [jelen támogatási cikkben leírtak szerint,](https://support.microsoft.com/kb/2023869/)ami hibát eredményez a kapcsolat megkísérlésekén. A probléma megoldásai több féle megoldásra is rendelkezésre állnak. A legegyszerűbb az, hogy backspace felett, majd írja be újra az első karakter az ujjlenyomat, hogy távolítsa el a főszereplő a kulcs érték mező regedit. Másik lehetőségként egy másik eszközt is használhat az ujjlenyomat másolásához.

3. Engedélyek megadása a szolgáltatásfiókhoz. 
   
    Győződjön meg arról, hogy az SQL Server szolgáltatásfiók megfelelő engedéllyel rendelkezik a TLS/SSL tanúsítvány titkos kulcsához. Ha figyelmen kívül hagyja ezt a lépést, az SQL Server nem indul el. Ehhez a feladathoz **használhatja** a Tanúsítványok beépülő modult vagy a **CertUtils-t.**
    
4. Indítsa újra az SQL Server szolgáltatást.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>SQL Server-kapcsolat konfigurálása a virtuális gépben
Miután beállította az Azure Cognitive Search által igényelt titkosított kapcsolatot, további konfigurációs lépések vannak az Azure virtuális gépekSQL Server-kiszolgálóhoz. Ha még nem tette meg, a következő lépés a konfiguráció befejezése az alábbi cikkek valamelyikével:

* Az **Erőforrás-kezelő** virtuális gépe esetén olvassa el a [Csatlakozás sql server virtuális géphez az Azure-ban az Erőforrás-kezelő használatával](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md)című témakört. 
* Klasszikus **virtuális** gép esetén olvassa el [a Csatlakozás SQL Server virtuális géphez az Azure Classic szolgáltatásban című témakört.](../virtual-machines/windows/classic/sql-connect.md)

Különösen, felülvizsgálja a szakasz minden cikkben a "csatlakozás az interneten keresztül".

## <a name="configure-the-network-security-group-nsg"></a>A hálózati biztonsági csoport (NSG) konfigurálása
Nem szokatlan, hogy konfigurálja az NSG és a megfelelő Azure-végpont vagy hozzáférés-vezérlési lista (ACL) az Azure virtuális gép elérhetővé tétele más felek számára. Valószínűleg már korábban is megtette, hogy a saját alkalmazáslogikája csatlakozzon az SQL Azure virtuális géphez. Nem különbözik az Azure Cognitive Search-kapcsolat az SQL Azure virtuális gép. 

Az alábbi hivatkozások útmutatást nyújtanak a virtuális gépek központi telepítések NSG-konfigurációjához. Ezeket az utasításokat az ACL egy Azure Cognitive Search-végpont ip-címe alapján.

> [!NOTE]
> A háttérben a [Mi a hálózati biztonsági csoport?](../virtual-network/security-overview.md)
> 
> 

* Az **Erőforrás-kezelő** virtuális gépéről a [NSG-k létrehozása ARM-telepítésekhez című témakörben](../virtual-network/tutorial-filter-network-traffic.md)talál. 
* Klasszikus **virtuális** gép, [lásd: NSG-k létrehozása a klasszikus központi telepítések.](../virtual-network/virtual-networks-create-nsg-classic-ps.md)

Az IP-címzés néhány olyan kihívást jelenthet, amelyeket könnyen lelehet küzdeni, ha tisztában van a problémával és a lehetséges megoldásokkal. A fennmaradó szakaszok javaslatokat tartalmaznak az ACL-ben az IP-címekkel kapcsolatos problémák kezelésére.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Az Azure Cognitive Search elérésének korlátozása
Azt javasoljuk, hogy korlátozza a hozzáférést a keresési szolgáltatás IP-címéhez `AzureCognitiveSearch` és az ACL-ben lévő [szolgáltatáscímkéjéhez](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) való hozzáférést ahelyett, hogy az SQL Azure virtuális gépeit minden kapcsolatkérés számára megnyitná.

Az IP-címet a keresési szolgáltatás teljes tartománynévének `<your-search-service-name>.search.windows.net`(például) pingelésével találhatja meg.

A `AzureCognitiveSearch` [szolgáltatáscímke](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) IP-címtartományát a [Letölthető JSON-fájlok](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) vagy a [Service Tag Discovery API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)segítségével találhatja meg. Az IP-címtartomány hetente frissül.

#### <a name="managing-ip-address-fluctuations"></a>Az IP-cím ingadozásainak kezelése
Ha a keresési szolgáltatás csak egy keresési egységgel rendelkezik (azaz egy replika és egy partíció), az IP-cím megváltozik a szolgáltatás rutinújraindítása során, és egy meglévő ACL-t érvénytelenít a keresési szolgáltatás IP-címével.

A későbbi kapcsolódási hiba elkerülése érdekében az azure cognitive search több replika és egy partíció használata. Ezzel növeli a költségeket, de megoldja az IP-cím problémáját is. Az Azure Cognitive Search, IP-címek nem változnak, ha több keresési egység.

A second approach is to allow the connection to fail, and then reconfigure the ACLs in the NSG. Átlagosan néhány hetente várhatóan az IP-címek megváltoznak. Az ügyfelek, akik nem ellenőrzött indexelés ritkán, ez a megközelítés lehet életképes.

A harmadik életképes (de nem különösen biztonságos) megközelítés az Azure-régió IP-címtartományának megadása, ahol a keresési szolgáltatás ki van építve. Az Azure Datacenter IP-tartományaiban közzéteszik azoknak az IP-tartományoknak a listáját, amelyekből nyilvános IP-címeket rendelnek hozzá az Azure [Datacenter IP-tartományaihoz.](https://www.microsoft.com/download/details.aspx?id=41653) 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Az Azure Cognitive Search portál IP-címeinek felvétele
Ha az Azure Portalon egy indexelő létrehozásához használja, az Azure Cognitive Search portál logikájának is szüksége van az SQL Azure virtuális géphez való hozzáférésre a létrehozás idáig. Az Azure Cognitive Search portál IP-címei pingeléssel `stamp2.search.ext.azure.com`találhatók.

## <a name="next-steps"></a>További lépések
A konfiguráció az útból, most már megadhatja az SQL Server az Azure virtuális gép, mint az Azure Cognitive Search indexelő adatforrása. További [információ: Az Azure SQL Database csatlakoztatása az Azure Cognitive Search indexelők használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) című témakörben olvashat bővebben.


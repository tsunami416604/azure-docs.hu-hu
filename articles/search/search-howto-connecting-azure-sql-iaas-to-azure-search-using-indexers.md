---
title: Azure SQL VM-kapcsolatok a keresés indexeléséhez
titleSuffix: Azure Cognitive Search
description: Engedélyezze a titkosított kapcsolatokat, és konfigurálja a tűzfalat úgy, hogy engedélyezze az Azure-beli virtuális gépeken (VM) SQL Server való csatlakozást az Azure-beli Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 12e642e59a1341926a0c4d66533465cecfc21709
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863138"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Azure Cognitive Search indexelő használatával létesített kapcsolatok konfigurálása Azure-beli virtuális gépeken való SQL Server

Ahogy azt az [Azure SQL Database az Azure-Cognitive Search az indexelő használatával való csatlakoztatása](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)során feltettük, az Azure-beli **virtuális gépeken** (vagy a rövid **SQL Azure virtuális gépeken** ) lévő SQL Server indexeket az Azure Cognitive Search támogatja, de a biztonsággal kapcsolatos előfeltételekre is van lehetőség. 

Az Azure Cognitive Search és a virtuális gép SQL Server közötti kapcsolatai nyilvános internetkapcsolattal rendelkeznek. Ezen kapcsolatok esetében általában az alábbi biztonsági intézkedések érvényesek:

+ Szerezze be a tanúsítványokat egy hitelesítésszolgáltató [szolgáltatótól](https://en.wikipedia.org/wiki/Certificate_authority#Providers) az Azure-beli virtuális gépen SQL Server példány teljes tartománynevéhez.
+ Telepítse a tanúsítványt a virtuális gépre, majd engedélyezze és konfigurálja a titkosított kapcsolatokat a virtuális gépen a jelen cikk útmutatásai alapján.

## <a name="enable-encrypted-connections"></a>Titkosított kapcsolatok engedélyezése
Az Azure Cognitive Search minden indexelő kérelemhez titkosított csatornát igényel nyilvános internetkapcsolaton keresztül. Ez a szakasz a működésének lépéseit sorolja fel.

1. Ellenőrizze a tanúsítvány tulajdonságait annak ellenőrzéséhez, hogy a tulajdonos neve az Azure-beli virtuális gép teljes tartományneve (FQDN). A tulajdonságok megtekintéséhez használhatja a CertUtils vagy a Certificates beépülő modult. A teljes tartománynevet a virtuálisgép-szolgáltatás panel Essentials szakaszának "a **nyilvános IP-cím/DNS-név címkéje** " mezőjében érheti el a [Azure Portal](https://portal.azure.com/).
   
   * Az újabb **Resource Manager** -sablonnal létrehozott virtuális gépek esetében a teljes tartománynév `<your-VM-name>.<region>.cloudapp.azure.com`
   * A **klasszikus** virtuális gépként létrehozott régebbi virtuális gépek esetében a teljes tartománynevet `<your-cloud-service-name.cloudapp.net>`formázza a rendszer.

2. Konfigurálja SQL Server a tanúsítvány használatára a Beállításszerkesztő (Regedit) használatával. 
   
    Bár a feladathoz gyakran SQL Server Konfigurációkezelő van használatban, ezt a forgatókönyvet nem használhatja. Nem találja az importált tanúsítványt, mert az Azure-beli virtuális gép teljes tartományneve nem egyezik meg a virtuális gép által meghatározott teljes tartománynévvel (a tartományt a helyi számítógépnek vagy a hálózati tartománynak, amelyhez csatlakozik). Ha a nevek nem egyeznek, a regedit használatával adhatja meg a tanúsítványt.
   
   * A regeditben keresse meg a következő beállításkulcsot: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     A `[MSSQL13.MSSQLSERVER]` rész a verzió és a példány neve alapján változik. 
   * Állítsa be a **tanúsítvány** kulcsának értékét a virtuális GÉPRE importált SSL-tanúsítvány **ujjlenyomatára** .
     
     Több módon is megszerezheti az ujjlenyomatot, ami jobb, mint a többi. Ha a **tanúsítványkezelő** beépülő modulból másolja azt az MMC-ben, akkor valószínűleg egy láthatatlan vezető karaktert fog felvenni a [jelen támogatási cikkben leírtak szerint](https://support.microsoft.com/kb/2023869/), ami hibát eredményez a kapcsolódás megkísérlése során. A probléma megoldásához több Áthidaló megoldás is létezik. A legkönnyebben a BACKSPACE billentyűt lenyomva, majd újra be kell írnia az ujjlenyomat első karakterét, hogy eltávolítsa a kezdő karaktert a regedit kulcs értéke mezőjében. Másik megoldásként másik eszközt is használhat az ujjlenyomat másolásához.

3. Engedélyek megadása a szolgáltatásfiók számára. 
   
    Győződjön meg arról, hogy az SQL Server-szolgáltatásfiók megfelelő jogosultságot kap az SSL-tanúsítvány titkos kulcsához. Ha figyelmen kívül hagyja ezt a lépést, SQL Server nem fog elindulni. Ehhez a feladathoz használhatja a **tanúsítványok** beépülő modult vagy a **certutilt** .
    
4. Indítsa újra az SQL Server szolgáltatást.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>SQL Server-kapcsolat konfigurálása a virtuális gépen
Az Azure Cognitive Search által igényelt titkosított kapcsolatok beállítása után az Azure-beli virtuális gépeken SQL Server további konfigurációs lépések is elérhetők. Ha még nem tette meg, a következő lépés a konfiguráció befejezése az alábbi cikkek valamelyikének használatával:

* **Resource Manager** -alapú virtuális gépek esetén lásd: [Kapcsolódás SQL Server virtuális géphez az Azure-ban a Resource Manager használatával](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* **Klasszikus** virtuális gép esetén lásd: [Kapcsolódás SQL Server virtuális géphez a klasszikus Azure](../virtual-machines/windows/classic/sql-connect.md)-ban.

Tekintse át a "kapcsolódás az internethez" című cikket az egyes cikkekben.

## <a name="configure-the-network-security-group-nsg"></a>A hálózati biztonsági csoport (NSG) konfigurálása
Nem szokatlan, hogy konfigurálja a NSG és a megfelelő Azure-végpontot vagy Access Control listát (ACL) ahhoz, hogy az Azure-beli virtuális gép elérhető legyen a többi fél számára. Ezt csak akkor teheti meg, ha engedélyezi a saját alkalmazás logikáját a SQL Azure virtuális géphez való kapcsolódáshoz. Nem különbözik az Azure Cognitive Search a SQL Azure virtuális géphez való kapcsolódáshoz. 

Az alábbi hivatkozások a virtuális gépek központi telepítésének NSG-konfigurációját ismertetik. Ezek az utasítások az Azure Cognitive Search végpontok IP-címe alapján történő hozzáférés-vezérlésére használhatók.

> [!NOTE]
> A háttérben tekintse meg a [Mi az a hálózati biztonsági csoport?](../virtual-network/security-overview.md) című témakört.
> 
> 

* **Resource Manager** -alapú virtuális gépek esetén lásd: [NSG létrehozása ARM](../virtual-network/tutorial-filter-network-traffic.md)-környezetekhez. 
* **Klasszikus** virtuális gép esetén lásd: [NSG létrehozása klasszikus központi telepítésekhez](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Az IP-címzés néhány olyan kihívást jelenthet, amely könnyen leküzdhető, ha tisztában van a problémával és a lehetséges megkerülő megoldásokkal. A fennmaradó szakaszokban az ACL IP-címeivel kapcsolatos problémák kezelésére vonatkozó ajánlásokat talál.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Az Azure-Cognitive Search való hozzáférés korlátozása
Javasoljuk, hogy a SQL Azure virtuális gépeknek az összes kapcsolódási kérelemhez való megnyitása helyett korlátozza a keresési szolgáltatás IP-címéhez való hozzáférést és az `AzureCognitiveSearch` [szolgáltatási címke](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) IP-címének elérését.

A keresési szolgáltatás teljes tartománynevének (például `<your-search-service-name>.search.windows.net`) pingelésével megtekintheti az IP-címet.

A `AzureCognitiveSearch` [szolgáltatási címke](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) IP-címének megkeresése az adott régióban, ahol az Azure Cognitive Search szolgáltatás a [letölthető JSON-fájlokkal](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) vagy a [Service tag Discovery API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)-val érhető el. Az IP-címtartomány hetente frissül.

#### <a name="managing-ip-address-fluctuations"></a>IP-címek ingadozásának kezelése
Ha a keresési szolgáltatásnak csak egy keresési egysége van (azaz egy replika és egy partíció), az IP-cím a rutin szolgáltatás újraindításakor módosul, és a keresési szolgáltatás IP-címével érvényteleníti a meglévő ACL-t.

A következő kapcsolódási hiba elkerülésének egyik módja, ha egynél több replikát és egy partíciót használ az Azure Cognitive Searchban. Ezzel növeli a költségeket, de az IP-cím problémáját is megoldja. Az Azure Cognitive Searchban az IP-címek nem változnak, ha egynél több keresési egység van.

A második módszer a sikertelen kapcsolódás engedélyezése, majd a NSG található ACL-ek újrakonfigurálása. Átlagosan néhány hetente megváltozhatnak az IP-címek. Az olyan ügyfelek számára, akik nem rendszeresen felügyelt indexelést végeznek, ez a megközelítés életképes lehet.

A harmadik életképes (de nem különösen biztonságos) módszer az az Azure-régió IP-címtartomány megadására szolgál, ahol a keresési szolgáltatás kiépítve van. A nyilvános IP-címeket az Azure-erőforrások számára kiosztott IP-tartományok listája az [Azure-adatközpont IP-tartományokban](https://www.microsoft.com/download/details.aspx?id=41653)jelenik meg. 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Az Azure Cognitive Search-portál IP-címeinek belefoglalása
Ha az Azure Portalt használja az indexelő létrehozásához, az Azure Cognitive Search Portal Logic is hozzáférést igényel a SQL Azure virtuális géphez a létrehozás ideje alatt. Az Azure Cognitive Search-portál IP-címei a `stamp2.search.ext.azure.com`pingelésével is megtalálhatók.

## <a name="next-steps"></a>Következő lépések
Az Azure-beli Cognitive Search indexelő adatforrásaként megadhat egy SQL Servert az Azure-beli virtuális gépen. További információkért lásd: [Azure SQL Database csatlakoztatása az Azure Cognitive Searchhoz az indexelő használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .


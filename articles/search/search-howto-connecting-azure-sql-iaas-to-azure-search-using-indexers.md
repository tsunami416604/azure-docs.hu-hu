---
title: Azure SQL virtuális gépek virtuálisgép-kapcsolatai a keresés indexeléséhez – Azure Search
description: Engedélyezze a titkosított kapcsolatokat, és konfigurálja a tűzfalat úgy, hogy lehetővé tegye a csatlakozást egy Azure-beli virtuális gépen (VM) lévő SQL Server a Azure Search indexelő eszközéről.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7629750da8f58c2c62f15102b60b5b562689f087
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656704"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Azure Search indexelő közötti kapcsolatok konfigurálása Azure-beli virtuális gépeken való SQL Server
Ahogy azt a [Azure SQL Database összekapcsolása a Azure Search indexelő használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)című szakaszban leírtak szerint, az Azure-beli virtuális gépeken (vagy a rövid **SQL Azure virtuális gépeken** ) **lévő SQL Server** -indexeket az Azure Search támogatja, de van néhány, a biztonsággal kapcsolatos előfeltétel Először is figyeljen. 

A Azure Search és a virtuális gép SQL Server közötti kapcsolatai nyilvános internetkapcsolattal rendelkeznek. Ezen kapcsolatok esetében általában az alábbi biztonsági intézkedések érvényesek:

+ Szerezze be a tanúsítványokat [](https://en.wikipedia.org/wiki/Certificate_authority#Providers) egy hitelesítésszolgáltató szolgáltatótól az Azure-beli virtuális gépen SQL Server példány teljes tartománynevéhez.
+ Telepítse a tanúsítványt a virtuális gépre, majd engedélyezze és konfigurálja a titkosított kapcsolatokat a virtuális gépen a jelen cikk útmutatásai alapján.

## <a name="enable-encrypted-connections"></a>Titkosított kapcsolatok engedélyezése
Azure Search a nyilvános internetkapcsolaton keresztül minden indexelő kérelemhez titkosított csatornát igényel. Ez a szakasz a működésének lépéseit sorolja fel.

1. Ellenőrizze a tanúsítvány tulajdonságait annak ellenőrzéséhez, hogy a tulajdonos neve az Azure-beli virtuális gép teljes tartományneve (FQDN). A tulajdonságok megtekintéséhez használhatja a CertUtils vagy a Certificates beépülő modult. A teljes tartománynevet a virtuálisgép-szolgáltatás panel Essentials szakaszának "a **nyilvános IP-cím/DNS-név címkéje** " mezőjében érheti el a [Azure Portal](https://portal.azure.com/).
   
   * Az újabb **Resource Manager** -sablonnal létrehozott virtuális gépek esetében a teljes tartománynevet a következő formátumban kell formázni:`<your-VM-name>.<region>.cloudapp.azure.com`
   * A **klasszikus** virtuális gépként létrehozott régebbi virtuális gépek esetében a teljes tartománynév a `<your-cloud-service-name.cloudapp.net>`következőként van formázva:.

2. Konfigurálja SQL Server a tanúsítvány használatára a Beállításszerkesztő (Regedit) használatával. 
   
    Bár a feladathoz gyakran SQL Server Konfigurációkezelő van használatban, ezt a forgatókönyvet nem használhatja. Nem találja az importált tanúsítványt, mert az Azure-beli virtuális gép teljes tartományneve nem egyezik meg a virtuális gép által meghatározott teljes tartománynévvel (a tartományt a helyi számítógépnek vagy a hálózati tartománynak, amelyhez csatlakozik). Ha a nevek nem egyeznek, a regedit használatával adhatja meg a tanúsítványt.
   
   * A regeditben keresse meg a következő beállításkulcsot `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`:.
     
     A `[MSSQL13.MSSQLSERVER]` rész a verzió és a példány neve alapján változik. 
   * Állítsa be a **tanúsítvány** kulcsának értékét a virtuális GÉPRE importált SSL-tanúsítvány **ujjlenyomatára** .
     
     Több módon is megszerezheti az ujjlenyomatot, ami jobb, mint a többi. Ha a **tanúsítványkezelő** beépülő modulból másolja azt az MMC-ben, akkor valószínűleg egy láthatatlan vezető karaktert fog felvenni a [jelen támogatási cikkben leírtak szerint](https://support.microsoft.com/kb/2023869/), ami hibát eredményez a kapcsolódás megkísérlése során. A probléma megoldásához több Áthidaló megoldás is létezik. A legkönnyebben a BACKSPACE billentyűt lenyomva, majd újra be kell írnia az ujjlenyomat első karakterét, hogy eltávolítsa a kezdő karaktert a regedit kulcs értéke mezőjében. Másik megoldásként másik eszközt is használhat az ujjlenyomat másolásához.

3. Engedélyek megadása a szolgáltatásfiók számára. 
   
    Győződjön meg arról, hogy az SQL Server-szolgáltatásfiók megfelelő jogosultságot kap az SSL-tanúsítvány titkos kulcsához. Ha figyelmen kívül hagyja ezt a lépést, SQL Server nem fog elindulni. Ehhez a feladathoz használhatja a **tanúsítványok** beépülő modult vagy a **certutilt** .
    
4. Indítsa újra az SQL Server szolgáltatást.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>SQL Server-kapcsolat konfigurálása a virtuális gépen
A Azure Search által igényelt titkosított kapcsolatok beállítása után az Azure-beli virtuális gépeken SQL Server további konfigurációs lépések is elérhetők. Ha még nem tette meg, a következő lépés a konfiguráció befejezése az alábbi cikkek valamelyikének használatával:

* **Resource Manager** -alapú virtuális gépek esetén lásd: [Kapcsolódás SQL Server virtuális géphez az Azure-ban a Resource Manager használatával](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* **Klasszikus** virtuális gép esetén lásd: [Kapcsolódás SQL Server virtuális géphez a klasszikus Azure](../virtual-machines/windows/classic/sql-connect.md)-ban.

Tekintse át a "kapcsolódás az internethez" című cikket az egyes cikkekben.

## <a name="configure-the-network-security-group-nsg"></a>A hálózati biztonsági csoport (NSG) konfigurálása
Nem szokatlan, hogy konfigurálja a NSG és a megfelelő Azure-végpontot vagy Access Control listát (ACL) ahhoz, hogy az Azure-beli virtuális gép elérhető legyen a többi fél számára. Ezt csak akkor teheti meg, ha engedélyezi a saját alkalmazás logikáját a SQL Azure virtuális géphez való kapcsolódáshoz. Nem különbözik a SQL Azure virtuális géphez Azure Search-kapcsolatban. 

Az alábbi hivatkozások a virtuális gépek központi telepítésének NSG-konfigurációját ismertetik. Ezekkel az utasításokkal a Azure Search végpontot az IP-címe alapján ACL-ként használhatja.

> [!NOTE]
> A háttérben tekintse meg a [Mi az a hálózati biztonsági csoport?](../virtual-network/security-overview.md) című témakört.
> 
> 

* **Resource Manager** -alapú virtuális gépek esetén lásd: [NSG létrehozása ARM](../virtual-network/tutorial-filter-network-traffic.md)-környezetekhez. 
* **Klasszikus** virtuális gép esetén lásd: [NSG létrehozása klasszikus központi telepítésekhez](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Az IP-címzés néhány olyan kihívást jelenthet, amely könnyen leküzdhető, ha tisztában van a problémával és a lehetséges megkerülő megoldásokkal. A fennmaradó szakaszokban az ACL IP-címeivel kapcsolatos problémák kezelésére vonatkozó ajánlásokat talál.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>A keresési szolgáltatás IP-címéhez való hozzáférés korlátozása
Határozottan javasoljuk, hogy a keresési szolgáltatás IP-címéhez való hozzáférést az ACL-ben korlátozza ahelyett, hogy a SQL Azure virtuális gépek tárva-nyitva legyenek a kapcsolódási kérelmek számára. Az IP-címet könnyedén megtalálhatja a keresési szolgáltatás teljes tartománynevének (például `<your-search-service-name>.search.windows.net`) pingelésével.

#### <a name="managing-ip-address-fluctuations"></a>IP-címek ingadozásának kezelése
Ha a keresési szolgáltatásnak csak egy keresési egysége van (azaz egy replika és egy partíció), az IP-cím a rutin szolgáltatás újraindításakor módosul, és a keresési szolgáltatás IP-címével érvényteleníti a meglévő ACL-t.

A következő kapcsolódási hiba elkerülésének egyik módja, ha több replikát és egy partíciót használ Azure Searchban. Ezzel növeli a költségeket, de az IP-cím problémáját is megoldja. Azure Search az IP-címek nem változnak, ha egynél több keresési egység van.

A második módszer a sikertelen kapcsolódás engedélyezése, majd a NSG található ACL-ek újrakonfigurálása. Átlagosan néhány hetente megváltozhatnak az IP-címek. Az olyan ügyfelek számára, akik nem rendszeresen felügyelt indexelést végeznek, ez a megközelítés életképes lehet.

A harmadik életképes (de nem különösen biztonságos) módszer az az Azure-régió IP-címtartomány megadására szolgál, ahol a keresési szolgáltatás kiépítve van. A nyilvános IP-címeket az Azure-erőforrások számára kiosztott IP-tartományok listája az [Azure-adatközpont IP-tartományokban](https://www.microsoft.com/download/details.aspx?id=41653)jelenik meg. 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>A Azure Search-portál IP-címeinek belefoglalása
Ha az Azure Portalt használja az indexelő létrehozásához, Azure Search portál logikájának a létrehozás ideje alatt is hozzá kell férnie a SQL Azure virtuális géphez. Az Azure Search portál IP-címei a pingeléssel `stamp2.search.ext.azure.com`találhatók meg.

## <a name="next-steps"></a>További lépések
A konfigurációt kihasználva mostantól megadhat egy SQL Servert az Azure-beli virtuális gépen egy Azure Search indexelő adatforrásaként. További információkért lásd: [Azure SQL Database csatlakoztatása Azure Search az indexelő használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .


---
title: Az Azure SQL virtuális gép virtuális gép kapcsolatának a keresési indexelő - Azure-keresés
description: Engedélyezze a titkosított kapcsolatokat, és konfigurálja a tűzfalat a kapcsolatok engedélyezése, hogy az SQL Server-beli virtuális gépen (VM) az Azure Search-indexelőt.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2efc0b76c8556894119ed3f6dd216234414cf313
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732355"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Az SQL Server az Azure Search indexelők a kapcsolat konfigurálása egy Azure-beli virtuális gépen
Leírtaknak [csatlakoztatása az Azure SQL Database az Azure Search indexelők használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), szemben az indexelők létrehozása **SQL Server Azure virtuális gépeken** (vagy **SQL Azure-beli virtuális gépek** röviden) használata támogatott az Azure Search de van néhány biztonsággal kapcsolatos előfeltételek irányuló első. 

SQL Server virtuális gépen az Azure Search kapcsolatainak egy nyilvános internetkapcsolat. A biztonsági intézkedéseket, ezek a kapcsolatok általában követnie mindegyikét itt is:

+ A tanúsítvány beszerzése egy [hitelesítésszolgáltató szolgáltató](https://en.wikipedia.org/wiki/Certificate_authority#Providers) teljesen minősített tartománynév az Azure virtuális gépek esetében.
+ Telepítse a tanúsítványt a virtuális Gépet, majd engedélyezése és a virtuális Gépet ebben a cikkben szereplő utasítások segítségével a konfigurálása a titkosított kapcsolatokat.

## <a name="enable-encrypted-connections"></a>Engedélyezze a titkosított kapcsolatokat
Az Azure Search egy titkosított csatornán szükséges összes indexelő kérelem nyilvános internetkapcsolaton keresztül. Ez a szakasz felsorolja a lépéseket, hogy ezt a munkát.

1. Ellenőrizze a tanúsítványt, ellenőrizze a tulajdonos nevét a teljesen minősített tartománynevét (FQDN) az Azure virtuális gépek tulajdonságait. Egy eszköz, például CertUtils vagy a tanúsítványok beépülő modul segítségével megtekintheti a tulajdonságait. Megtekintheti a teljes Tartománynevet az Essentials szakaszban a virtuális gép szolgáltatás paneljén, az **nyilvános IP-cím/DNS-névcímke** mezőbe, a [az Azure portal](https://portal.azure.com/).
   
   * Az újabb használatával létrehozott virtuális gépek **Resource Manager** sablonban vannak formázva, a teljes tartománynév `<your-VM-name>.<region>.cloudapp.azure.com`. 
   * A régebbi virtuális gépeket létrehozni, mivel egy **klasszikus** virtuális Gépen vannak formázva, a teljes tartománynév `<your-cloud-service-name.cloudapp.net>`. 
2. SQL Server használatával a beállításjegyzék-szerkesztővel (regedit) a tanúsítvány használatára konfigurálható. 
   
    Ez a feladat gyakran használják az SQL Server Configuration Manager, bár ebben a forgatókönyvben nem használható. Az importált tanúsítvány, nem található, mert az Azure-beli virtuális gép teljes Tartományneve nem egyezik meg a teljes tartománynév, a virtuális gép (azonosítja a tartományt a helyi számítógépen vagy a hálózati tartomány, amelyhez csatlakozik) határoz meg. Ha nevei nem egyeznek, a regedit használatával adja meg a tanúsítványt.
   
   * A regedit, keresse meg a beállításkulcs: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     A `[MSSQL13.MSSQLSERVER]` rész verzióján és példányán neve alapján változik. 
   * Az értékét állítsa be a **tanúsítvány** kulcs a **ujjlenyomat** a virtuális gép az importált SSL-tanúsítvány.
     
     Többféleképpen is lehet az ujjlenyomat beolvasása néhány jobban, mint mások. Ha másolja azt a **tanúsítványok** beépülő MMC-ben, akkor valószínűleg kiesik láthatatlan bevezető jellegű [támogatási cikkben leírtak szerint](https://support.microsoft.com/kb/2023869/), amely hibát eredményez a kapcsolat megkísérlésekor . Számos kell megadni. a probléma kijavítása létezik. A legegyszerűbb, ha backspace keresztül, és írja be újra az ujjlenyomat eltávolítása a vezető karaktert a kulcs értékét a mező a regedit első karaktere. Másik lehetőségként egy másik eszköz használatával másolja az ujjlenyomatot.
3. Adja meg a szolgáltatás fiók engedélyeket. 
   
    Ellenőrizze, hogy az SQL Server szolgáltatási fiókja SSL-tanúsítvány a titkos kulcsot a megfelelő engedélyt kap. Ha Ön eltéveszthetők, ezt a lépést, az SQL Server nem indul el. Használhatja a **tanúsítványok** beépülő modul vagy **CertUtils** erre a célra.
4. Indítsa újra az SQL Server szolgáltatást.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Az SQL Server-kapcsolat konfigurálása a virtuális gépen
Miután beállította a titkosított kapcsolatot hoz létre az Azure Search által igényelt, nincsenek további konfigurációs lépések belső SQL Server Azure virtuális gépeken. Ha még nem tette meg, a következő lépés befejezéséhez vagy egy, az alábbi cikkek segítségével konfigurációs:

* Az egy **Resource Manager** virtuális Gépet, tekintse meg [Csatlakozás SQL Server virtuális gépekhez az Azure Resource Manager használatával](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Az egy **klasszikus** virtuális Gépet, tekintse meg [csatlakozás az SQL Server virtuális gép a klasszikus Azure](../virtual-machines/windows/classic/sql-connect.md).

Mindenekelőtt tekintse át a minden egyes cikk "az interneten keresztül összekötő" című szakaszában.

## <a name="configure-the-network-security-group-nsg"></a>A hálózati biztonsági csoport (NSG) konfigurálása
Már nem ritka, hogy az NSG-t és a megfelelő Azure-végpont vagy hozzáférés-vezérlési lista (ACL) az Azure virtuális gép elérhetővé a többi fél konfigurálása. Valószínűleg ez előtt, hogy az SQL Azure virtuális Géphez való csatlakozáshoz a saját alkalmazáslogika végezze el. Nem különbözik, az Azure Search-kapcsolatot az SQL Azure virtuális gép számára. 

Az alábbi hivatkozások a virtuális gépek üzembe helyezése az NSG konfigurációs utasításokkal. Kövesse ezeket az utasításokat az ACL-t egy Azure Search-végpont az IP-címe alapján.

> [!NOTE]
> A háttér-információkért lásd: [Mi az a hálózati biztonsági csoport?](../virtual-network/security-overview.md)
> 
> 

* Az egy **Resource Manager** virtuális Gépet, tekintse meg [NSG-k létrehozása az ARM üzemelő példányok](../virtual-network/tutorial-filter-network-traffic.md). 
* Az egy **klasszikus** virtuális Gépet, tekintse meg [NSG-k létrehozása a klasszikus üzembe helyezéssel](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

IP-címkezelés jelenthetnek néhány kihívásokat, amely egyszerűen elhárítja a Ha a probléma és kerülő megoldásait. Fennmaradó javaslatokkal IP-címek hozzáférés-vezérlési kapcsolatos problémák kezelésére.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Korlátozza a hozzáférést a keresési szolgáltatás IP-címe
Határozottan javasoljuk, hogy az ACL-t, már nem kell az SQL Azure-beli virtuális gépek nyílt, bármilyen csatlakozási kérések a search szolgáltatás az IP-címre korlátozhatja a hozzáférést. Megtalálhatja az IP-cím pingelésével a teljes Tartománynevet (például `<your-search-service-name>.search.windows.net`) a keresési szolgáltatás.

#### <a name="managing-ip-address-fluctuations"></a>IP-cím ingadozások által megkövetelt kezelése
Ha a keresési szolgáltatás csak egy keresési egységet (azaz egy replika és a egy partíció), az IP-cím változik rutin szolgáltatás újraindul, a keresési szolgáltatás IP-címmel rendelkező meglévő ACL érvénytelenítése során.

Egyik módja a későbbi csatlakozási hiba elkerülése érdekében, hogy egynél több replika és a egy partíció használata az Azure Search szolgáltatásban. Ez növeli a költségeket, de az is megoldást kínál az IP-cím probléma. Az Azure Search szolgáltatásban IP-címek ne módosítsa, ha egynél több keresési egység.

A második megközelítéssel, hogy a csatlakozás nem sikerül, és újra kell konfigurálnia az NSG ACL-ek engedélyezése. Átlagosan várható IP-címek módosítása néhány hetente. Azok a vásárlóknak, akik tegye ellenőrzött indexelő alkalmi alapon akkor lehet működőképes.

A harmadik életképes (de nem a különösen biztonságos) megközelítés, hogy adja meg az IP-címtartományt, ahol a keresési szolgáltatás ki van építve az Azure-régióban. Azure-erőforrások számára kiosztott nyilvános IP-címet, amelyről IP-címtartományok listája közzé van téve [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Az Azure Search portál IP-címeket tartalmazza.
Az Azure portal használatakor egy indexelő létrehozása Azure Search portál logikát is hozzá kell férnie az SQL Azure virtuális gép létrehozásának ideje alatt. Az Azure search portál IP-címek találhatók pingelésével `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>További lépések
A konfiguráció így most már megadhatja egy SQL Server Azure virtuális gépen az Azure Search indexelők adatok forrásaként. Lásd: [csatlakoztatása az Azure SQL Database az Azure Search indexelők használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) további információt.


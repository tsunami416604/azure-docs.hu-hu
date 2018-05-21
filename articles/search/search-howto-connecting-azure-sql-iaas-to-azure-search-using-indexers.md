---
title: Azure Search SQL Virtuálisgép-kapcsolat |} Microsoft Docs
description: Engedélyezze a titkosított kapcsolatokat, és a tűzfalon engedélyezni az SQL Server egy Azure virtuális gépen (VM) az Azure Search indexelőt konfigurálása.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: heidist
ms.openlocfilehash: 7800e83891cb336bb896299b8fd4d6b3ba590178
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Egy kapcsolat egy Azure Search-indexelőt, és az SQL Server konfigurálása az Azure virtuális gépen
Leírtaknak megfelelően [csatlakozás az Azure SQL Database az Azure Search használatával az indexelők](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), elleni indexelők létrehozása **az Azure virtuális gépeken futó SQL Server** (vagy **SQL Azure virtuális gépek** röviden) Azure Search által támogatott, de néhány biztonsági Előfeltételek az első végrehajtására. 

**Tevékenység időtartama:** körülbelül 30 percet, feltéve, hogy már telepített egy tanúsítványt a virtuális Gépen.

## <a name="enable-encrypted-connections"></a>Engedélyezze a titkosított kapcsolatokat
Az Azure Search van szükség, egy nyilvános internetkapcsolaton keresztül egy titkosított csatornán indexelő minden olyan kérelem esetében. Ez a rész felsorolja a lépéseket működnek.

1. Ellenőrizze a tanúsítványt, ellenőrizze a tulajdonos nevét a teljesen minősített tartományneve (FQDN) az Azure virtuális gép tulajdonságait. Egy eszköz, például CertUtils vagy a tanúsítványok beépülő modul segítségével a tulajdonságok megtekintéséhez. Letölthető a teljes Tartománynevet a virtuális gép szolgáltatás panelre Essentials szakaszban, a a **nyilvános IP-cím/DNS-névcímke** mező mellett a [Azure-portálon](https://portal.azure.com/).
   
   * Az újabb használatával létrehozott virtuális gépek **erőforrás-kezelő** sablon, a teljes tartománynév formátuma `<your-VM-name>.<region>.cloudapp.azure.com`. 
   * Régebbi virtuális gépek jönnek létre például egy **klasszikus** VM, a teljes tartománynév formátuma `<your-cloud-service-name.cloudapp.net>`. 
2. SQL Server a Beállításszerkesztő (regedit) használatával. a tanúsítvány használatára konfigurálja. 
   
    Bár ez a feladat gyakran használják az SQL Server Configuration Manager, a forgatókönyv nem használható. Az importált tanúsítvány, nem található, mert az Azure virtuális gép teljes Tartományneve nem egyezik meg a teljes tartománynév, a virtuális gép (azonosítja a tartomány vagy a helyi számítógépen, vagy a hálózati tartomány, amelyhez csatlakozik) alapján. Nevei nem egyeznek meg, amikor a regedit használatával adja meg a tanúsítványt.
   
   * A regedit, keresse meg a beállításkulcs a: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     A `[MSSQL13.MSSQLSERVER]` része verzióján és példányán neve alapján változik. 
   * Állítsa a **tanúsítvány** kulcs a **ujjlenyomat** a virtuális gép az importált SSL-tanúsítvány.
     
     Az ujjlenyomat beolvasása néhány jobban számos módon lehet. Ha másolja le a **tanúsítványok** beépülő MMC-ben, akkor valószínűleg felveszi egy láthatatlan kezdő karakter [támogatási cikkben leírtak szerint](https://support.microsoft.com/kb/2023869/), amelyek hibát eredményez a kapcsolat tett kísérlet során. Néhány lehetséges megoldások a probléma kijavítása létezik. A legegyszerűbb, hogy backspace keresztül, és írja be újra az ujjlenyomat regedit kulcsérték mezőjében a kezdő karakter eltávolítása az első karakter. Egy másik eszköz segítségével azt is megteheti, másolja le az ujjlenyomatot.
3. Adja meg a szolgáltatás fiók engedélyeket. 
   
    Győződjön meg arról, hogy az SQL Server szolgáltatásfiókja megfelelő engedélyekkel az SSL-tanúsítvány a titkos kulcsot kap. Ha eltéveszthetők ezt a lépést, az SQL Server nem fog elindulni. Használhatja a **tanúsítványok** beépülő modul vagy **CertUtils** ehhez a feladathoz.
4. Indítsa újra az SQL Server szolgáltatást.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>A virtuális gép az SQL Server-kapcsolat konfigurálása
Miután beállította az Azure Search által igényelt titkosított kapcsolati, nincsenek további konfigurációs lépések az Azure virtuális gépeken futó SQL Server belső. Ha még nem tette meg, a következő lépés befejezéséhez a konfigurációval vagy az egyik ezek a cikkek:

* Az egy **erőforrás-kezelő** virtuális gép, lásd: [csatlakozás az SQL Server virtuális gép az Azure Resource Manager használatával](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Az egy **klasszikus** virtuális gép, lásd: [csatlakozás az SQL Server virtuális gép Azure klasszikus](../virtual-machines/windows/classic/sql-connect.md).

Különösen tekintse át az egyes cikk "az interneten keresztül csatlakozó" című szakasza.

## <a name="configure-the-network-security-group-nsg"></a>Hálózati biztonsági csoport (NSG) konfigurálása
Nincs szokatlan konfigurálása az NSG és megfelelő Azure-végpont vagy a hozzáférés-vezérlési lista (ACL) az Azure virtuális gép más felek számára. Valószínűleg ezt a, mielőtt engedélyezi a saját alkalmazáslogikát, hogy az SQL Azure virtuális gép csatlakozni. Nem különbözik, az Azure Search-kapcsolat az SQL Azure virtuális géphez. 

A lenti hivatkozásokra kattintva utasításokkal láthatja el a Virtuálisgép-telepítések NSG-konfigurációban. Használja az IP-címe alapján Azure Search a végpont ACL ezeket az utasításokat.

> [!NOTE]
> Háttér, lásd: [Mi az a hálózati biztonsági csoport?](../virtual-network/security-overview.md)
> 
> 

* Az egy **erőforrás-kezelő** virtuális gép, lásd: [NSG-k létrehozása az ARM-telepítések](../virtual-network/tutorial-filter-network-traffic.md). 
* Az egy **klasszikus** virtuális gép, lásd: [NSG-k létrehozása a klasszikus telepítések](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

IP-címeit, amelyek könnyen vannak megoldásához, ha a probléma és a lehetséges megoldások tudatában néhány kihívást jelenthet. A fennmaradó szakaszokban javaslatok IP-címek a hozzáférés-vezérlési listában kapcsolatos problémák kezelésére.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Korlátozza a hozzáférést a keresési szolgáltatás IP-címe
Erősen ajánlott a hozzáférés korlátozása a hozzáférés-vezérlési listában tételére SQL Azure virtuális gépeken, nyílt bármely kapcsolatkérelmeknek helyett a keresőszolgáltatása IP-címét. Könnyedén megtalálhatja az IP-cím történő megpingelésével a teljes Tartománynevet (például `<your-search-service-name>.search.windows.net`), a keresési szolgáltatáshoz.

#### <a name="managing-ip-address-fluctuations"></a>IP-cím ingadozását kezelése
Ha a keresési szolgáltatást (Ez azt jelenti, hogy egy másodpéldány és egy partíció) csak egy keresési egység, az IP-cím rutin szolgáltatás újraindul, a keresési szolgáltatás IP-címmel meglévő ACL érvénytelenítése során fogja módosítani.

Egy az ezt követő csatlakozási hiba elkerülése érdekében módja az Azure Search egynél több replika és egy partíció használjon. Ez növeli a költségeket, de azt is megoldja a IP-cím problémát. Az Azure Search IP-címek ne módosítsa, ha egynél több keresési egység.

Egy második megoldás, a kapcsolat nem sikerül, és konfigurálja újra az NSG ACL engedélyezéséhez. IP-címek módosítása néhány hetente átlagosan számíthat. Azok számára, aki elvégzi a alkalomszerű alapon ellenőrzött indexelő Ez a megközelítés életképes lehet.

Egy harmadik életképes (de nem a különösen biztonságos) megoldás, az IP-címtartományt az Azure-régió, ahol a keresési szolgáltatás ki van építve, ha. Nyilvános IP-címek, amelyből Azure-erőforrások számára kiosztott IP-címtartománylista közzé van téve a [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Tartalmazza az Azure Search portál IP-címek
Ha az Azure portál segítségével hozzon létre egy indexelőt, Azure Search portál logikát is létrehozás közbeni hozzá kell férnie az SQL Azure virtuális gép. Az Azure search portál IP-címek található történő megpingelésével `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>További lépések
A bevezetés konfigurációval megadhat egy SQL Server Azure virtuális gépen egy Azure Search-indexelőt adatok forrásaként. Lásd: [csatlakozás az Azure SQL Database az Azure Search használatával az indexelők](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) további információt.


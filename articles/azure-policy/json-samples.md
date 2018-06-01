---
title: Szabályzatsablonok példái
description: JSON-minták az Azure Policy-hez
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/17/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 4b9096c1fb0d9ee74849e259a6e0af2486c5d29b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195124"
---
# <a name="templates-for-azure-policy"></a>Az Azure Policy sablonjai

A következő táblázat az Azure Policy json sablonjaira mutató hivatkozásokat tartalmaz. Ezek a minták az [Azure Policy mintaadattárában](https://github.com/Azure/azure-policy) találhatók.

| | |
|---|---|
|**Számítás**||
| [Jóváhagyott VM-lemezképek](scripts/allowed-custom-images.md) | Ehhez csak jóváhagyott egyéni rendszerképek helyezhetők üzembe a környezetben. Meg kell adnia egy jóváhagyott lemezazonosítókat tartalmazó tömböt. |
| [Felügyelt lemezeket nem használó virtuális gépek naplózása](scripts/create-vm-managed-disk.md) | Naplózza, ha felügyelt lemezt nem használó virtuális gép jön létre.|
| [Naplózás hiányzó bővítmény esetén](scripts/audit-ext-not-exist.md) | Naplózza, ha egy bővítmény nincs telepítve egy virtuális gépen. Meg kell adnia a bővítmény kiadóját és típusát a bővítmény telepítési állapotának ellenőrzéséhez. |
| [Egyéni VM-lemezképek engedélyezése egy adott erőforráscsoportból](scripts/allow-custom-vm-image.md) |  Ehhez az egyéni rendszerképeknek jóváhagyott erőforráscsoportból kell származniuk. Meg kell adnia a jóváhagyott erőforráscsoport nevét. |
| [A hibrid használati előny megtagadása](scripts/deny-hybrid-use.md) | Megtiltja az Azure Hybrid Use Benefit (AHUB) használatát. Akkor használja, amikor nem szeretné engedélyezni a helyszíni licencek használatát. |
| [Nem engedélyezett virtuálisgép-bővítmények](scripts/not-allowed-vm-ext.md) | Megtiltja a megadott bővítmények használatát. Meg kell adnia egy tiltott bővítménytípusokat tartalmazó tömböt. |
| [Csak bizonyos platformú VM-lemezkép használatának engedélyezése](scripts/allow-certain-vm-image.md) | Megköveteli az UbuntuServer egy bizonyos verziójának használatát a virtuális gépektől. |
| [Virtuális gép létrehozása felügyelt lemez használatával](scripts/use-managed-disk-vm.md) | Kezelt lemezek használatát követeli meg a virtuális gépektől.|
|**Monitorozás**||
| [Diagnosztikai beállítás naplózása](scripts/audit-diag-setting.md) | Naplózza, ha a diagnosztikai beállítások nincsenek engedélyezve a megadott erőforrástípusokhoz. Adjon meg egy erőforrástípus-tömböt, amelyhez ellenőrizni szeretné, hogy engedélyezve vannak-e a diagnosztikai beállítások. |
|**Elnevezési és szövegkonvenciók**||
| [Több névminta engedélyezése](scripts/allow-multiple-name-patterns.md) | Számos névminta egyike használatának engedélyezése az erőforrásokhoz. |
| [Hasonló minta szükséges](scripts/enforce-like-pattern.md) | Az erőforrások neveinek meg kell felelnie egy minta hasonló feltételének. |
| [Egyező minta szükséges](scripts/enforce-match-pattern.md) | Az erőforrások neveinek meg kell egyezniük az elnevezési mintával. |
| [A címke és a minta egyezése szükséges](scripts/enforce-tag-match-pattern.md) | A címkék értékének igazodniuk kell valamely szövegmintához. |
|**Hálózat**||
| [Engedélyezett Application Gateway termékváltozatok](scripts/allowed-app-gate-sku.md) | Ehhez az alkalmazásátjáróknak jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [Naplózás, ha a Network Watcher nincs engedélyezve egy régióban](scripts/net-watch-not-enabled.md) | Naplót készít, ha a Network Watcher nincs engedélyezve egy adott régióban. Megadhatja a régió nevét annak ellenőrzéséhez, hogy a Network Watcher engedélyezve van-e. |
| [NSG X minden hálózati adapteren](scripts/nsg-on-nic.md) | Előírja, hogy minden virtuális hálózati adapterhez egy adott hálózati biztonsági csoportot kell használni. A használandó hálózati biztonsági csoport azonosítóját Ön határozza meg. |
| [NSG X minden alhálózaton](scripts/nsg-on-subnet.md) | Előírja, hogy minden virtuális alhálózathoz egy adott hálózati biztonsági csoportot kell használni. A használandó hálózati biztonsági csoport azonosítóját Ön határozza meg. |
| [Engedélyezett Express Route-sávszélesség](scripts/allowed-er-band.md) | Ehhez az Express Route-útvonalaknak adott sávszélességet kell használniuk. Meg kell adnia egy tömböt, mely tartalmazza az Express Route-útvonalakhoz használható termékváltozatokat. |
| [Engedélyezett társviszony-létesítési helyek Express Route-útvonalakhoz](scripts/allowed-peering-er.md) | Ehhez az Express Route-útvonalaknak a megadott társviszony-létesítési helyeket kell használnia. Meg kell adnia egy engedélyezett társviszony-létesítési helyeket tartalmazó tömböt. |
| [Engedélyezett termékváltozatok Express Route-útvonalakhoz](scripts/allowed-er-skus.md) | Ehhez az Express Route-útvonalaknak egy jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy engedélyezett termékváltozatokat tartalmazó tömböt. |
| [Engedélyezett termékváltozatok terheléselosztókhoz](scripts/allowed-lb-skus.md) | Ehhez a terheléselosztóknak egy jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy engedélyezett termékváltozatokat tartalmazó tömböt. |
| [Hálózati társviszony tiltása egy erőforráscsoportban lévő hálózatban](scripts/no-peering-er-net.md) | Megtiltja hálózati társviszony társítását egy megadott erőforráscsoportban lévő hálózattal. Segít meggátolni a központi felügyelt hálózati infrastruktúrához való kapcsolódást. Meg kell adnia annak az erőforráscsoportnak a nevét, amelyben meg kívánja gátolni a társítást. |
| [Felhasználó által megadott útválasztási táblázatok tiltása](scripts/no-user-def-route-table.md)  |Megtiltja a virtuális hálózatok felhasználó által megadott útválasztási táblázattal való üzembe helyezését. |
| [Engedélyezett termékváltozatok virtuális hálózati átjárókhoz](scripts/allowed-vn-gate-sku.md) | Ehhez a virtuális hálózati átjáróknak egy jóváhagyott termékváltozatot és átjárótípust kell használniuk. Meg kell adnia a jóváhagyott termékváltozatok tömbjét és a jóváhagyott átjárótípusok tömbjét. |
| [Jóváhagyott alhálózat használata a virtuális gépek hálózati adaptereihez](scripts/use-approved-subnet-vm-nics.md) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott alhálózatot kell használniuk. A jóváhagyott alhálózat azonosítóját Ön határozza meg. |
| [Jóváhagyott virtuális hálózat használata a virtuális gépek hálózati adaptereihez](scripts/use-approved-vnet-vm-nics.md) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott virtuális hálózatot kell használniuk. A jóváhagyott virtuális hálózat azonosítóját Ön határozza meg. |
|**Címkék**||
| [Számlázási címkék szabályzatának kezdeményezése](scripts/billing-tags-policy-init.md) | A költséghely és a terméknév címkének a megadott értékekkel kell rendelkeznie. A példa beépített szabályzatokat használ a szükséges címkék hozzáadásához és előírásához. A címkék szükséges értékeit Ön adja meg.  |
| [Címke és a hozzá tartozó érték kényszerítése erőforráscsoportok esetében](scripts/enforce-tag-rg.md) | Címke és érték szükséges az erőforráscsoportokhoz. Ön adja meg a kötelező címkenevet és -értéket.  |
|**SQL**||
| [SQL-adatbázisszintű naplózási beállítás naplózása](scripts/audit-sql-db-audit-setting.md) | Naplózza az SQL-adatbázis naplózási beállításait, ha a beállítások eltérnek a megadott beállítástól. Meg kell adnia egy értéket annak meghatározásához, hogy a naplózási beállításnak engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [Transzparens adattitkosítás állapotának naplózása](scripts/audit-trans-data-enc-status.md) | Naplózza, ha az SQL-adatbázis transzparens adattitkosítása nincs engedélyezve.  |
| [Adatbázisszintű fenyegetésészlelési beállítás naplózása](scripts/audit-db-threat-det-setting.md) | Naplózza az SQL-adatbázis biztonsági riasztási szabályzatait, ha a szabályzatok nem a megadott állapotra vannak beállítva. Meg kell adnia egy értéket annak meghatározásához, hogy a fenyegetésészlelésnek engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [SQL-kiszolgálói szintű naplózási beállítások naplózása](scripts/audit-sql-ser-leve-audit-setting.md) | Naplózza az SQL-kiszolgáló naplózási beállításait, ha a beállítások eltérnek a megadott beállítástól. Meg kell adnia egy értéket annak meghatározásához, hogy a naplózási beállításnak engedélyezett vagy letiltott állapotúnak kell-e lennie. |
| [Kiszolgálószintű fenyegetésészlelési beállítás naplózása](scripts/audit-sql-ser-threat-det-setting.md) | Naplózza az SQL-adatbázis biztonsági riasztási szabályzatait, ha a szabályzatok nem a megadott állapotra vannak beállítva. Meg kell adnia egy értéket annak meghatározásához, hogy a fenyegetésészlelésnek engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [Azure Active Directory-rendszergazda hiányának naplózása](scripts/audit-no-aad-admin.md) | Naplózza, ha nincs Azure Active Directory-rendszergazda hozzárendelve az SQL-kiszolgálóhoz. |
| [Engedélyezett termékváltozatok SQL-adatbázisokhoz](scripts/allowed-sql-db-skus.md) | Ehhez az SQL-adatbázisoknak egy jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy engedélyezett termékváltozat-azonosítókat tartalmazó tömböt vagy egy engedélyezett termékváltozat-neveket tartalmazó tömböt. |
|**Storage**||
| [Tárfiókok és virtuális gépek engedélyezett termékváltozatai](scripts/allowed-skus-storage.md) | Ehhez a tárfiókoknak és a virtuális gépeknek engedélyezett termékváltozatokat kell használniuk. Beépített szabályzatok használatával biztosítja az engedélyezett termékváltozatok alkalmazását. Megadhatja az engedélyezett virtuálisgép-termékváltozatok és az engedélyezett tárfiók-termékváltozatok tömbjét. |
| [HTTPS-adatforgalom kényszerítése a tárfiókok számára](scripts/ensure-https-stor-acct.md) | Ehhez a tárfiókoknak HTTPS-forgalmat kell használniuk.  |
| [A ritkán használt adatok hozzáférési szintje használatának tiltása a tárfiókok számára](scripts/deny-cool-access-tiering.md) | Megtiltja a ritkán használt adatok hozzáférési szintjének használatát a Blob Storage-fiókokban.  |
| [Fájltitkosítás megkövetelése a tárfiókokban](scripts/ensure-store-file-enc.md) | Megköveteli a fájltitkosítás engedélyezését a tárfiókokban.  |
|**Beépített szabályzat**||
| [Engedélyezett helyek](scripts/allowed-locs.md) | Előírja, hogy az erőforrások kizárólag az engedélyezett helyeken helyezhetők üzembe. Az engedélyezett helyek tömbjét Ön határozza meg.  |
| [Engedélyezett erőforrástípusok](scripts/allowed-res-types.md) | Biztosítja, hogy kizárólag a jóváhagyott erőforrástípusok legyenek üzembe helyezve. Az engedélyezett erőforrástípusok tömbjét Ön határozza meg.  |
| [Engedélyezett tárfiók-termékváltozatok](scripts/allowed-stor-acct-skus.md) | Ehhez a tárfiókoknak jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [Címke és a címke alapértelmezett értékének alkalmazása](scripts/apply-tag-def-val.md) | Hozzáadja a megadott címkenevet és -értéket, ha a címke nincs megadva. Az alkalmazandó címkenevet és -értéket Ön határozza meg.  |
| [SQL Database-adatbázis titkosításának naplózása](scripts/sql-database-encryption-audit.md) | Naplózza, ha az SQL Database-adatbázisban nincs engedélyezve a transzparens adattitkosítás. |
| [SQL-kiszolgáló naplózási beállításainak naplózása](scripts/sql-server-audit.md) | Az alapján naplózza az SQL-kiszolgálót, hogy engedélyezve vannak-e rajta a naplózási beállítások. |
| [Data Lake Store-titkosítás megkövetelése](scripts/enforce-datalakestore-encryption.md) | Elutasít minden olyan Data Lake Store-fiókot, ahol nincs engedélyezve a titkosítás. |
| [Címke és a hozzá tartozó érték kényszerítése](scripts/enforce-tag-val.md) | Kötelező megadni hozzá a címkenevet és -értéket. Ön adja meg a kikényszerítendő címkenevet és -értéket.  |
| [Nem engedélyezett erőforrástípusok](scripts/not-allowed-res-type.md) | Megakadályozza a megadott erőforrástípusok üzembe helyezését. A letiltandó erőforrástípusok tömbjét Ön határozza meg.  |
| [Az SQL Server 12.0-s verziójának megkövetelése](scripts/req-sql-12.md) | Megköveteli, hogy az SQL-kiszolgálók a 12.0-s verziót használják.  |
| [Tárfiók-titkosítás megkövetelése](scripts/req-store-acct-enc.md) | Megköveteli, hogy a tárfiók blobtitkosítást használjon.  |

---
title: "Csoportházirend-sablon minták |} Microsoft Docs"
description: "Azure-házirend JSON-minták"
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: na
ms.topic: samples
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 11/13/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 4ac9696028b9f24341a630d630b583ac5041cee0
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="templates-for-azure-policy"></a>Sablonok az Azure-házirend

A következő táblázat az Azure-házirend json sablonok hivatkozásait tartalmazza. Ezeket a mintákat találhatók a [Azure házirend minták tárház](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Számítás**||
| [Virtuális gép által jóváhagyott lemezképeket](scripts/allowed-custom-images.md) | Telepítésére van szükség, hogy csak a jóváhagyott egyéni képek a környezetben. Megadja a jóváhagyott kép azonosítók tömbjét. |
| [Naplózási, amikor virtuális gép nem használja a kezelt lemez](scripts/create-vm-managed-disk.md) | Ha egy virtuális gépet hoz létre, amely nem használja a felügyelt lemezek eseményeket.|
| [Naplózási, ha a kiterjesztés nem létezik.](scripts/audit-ext-not-exist.md) | Ha egy bővítmény nincs telepítve a virtuális gépekkel eseményeket. Megadhatja, hogy a kiterjesztés közzétevő és típusát, ellenőrizze, hogy telepítették. |
| [Az erőforráscsoport egyéni Virtuálisgép-lemezkép engedélyezése](scripts/allow-custom-vm-image.md) |  Megköveteli, hogy az egyéni lemezképek származnia egy jóváhagyott erőforráscsoportot. Megadhatja a jóváhagyott erőforráscsoport nevét. |
| [Hibrid használata juttatás megtagadása](scripts/deny-hybrid-use.md) | Ez a beállítás letiltja az Azure hibrid használja juttatás (AHUB) használja. Akkor használja, ha nem szeretné, hogy a helyszíni licencek használatához. |
| [Nem engedélyezett a Virtuálisgép-bővítmények](scripts/not-allowed-vm-ext.md) | Ez a beállítás letiltja a megadott bővítmények használatát. Megadja a tiltott típusokat tartalmazó tömb. |
| [Csak az egyes virtuális gép platformlemezkép engedélyezése](scripts/allow-certain-vm-image.md) | Megköveteli, hogy a virtuális gépek UbuntuServer meghatározott verzióját használja. |
| [Felügyelt lemezes virtuális gép létrehozása](scripts/use-managed-disk-vm.md) | Szükséges, hogy a virtuális gépek felügyelt lemezt használja.|
|**Figyelés**||
| [Diagnosztikai naplózás beállítása](scripts/audit-diag-setting.md) | Ha a diagnosztikai beállítások nincs engedélyezve a megadott típusú erőforrások eseményeket. Megadja annak ellenőrzése, hogy engedélyezve vannak-e a diagnosztikai beállítások tömbjét. |
|**Név és a szöveges konvenciók**||
| [Több mintában engedélyezése](scripts/allow-multiple-name-patterns.md) | Sok mintában használt erőforrások engedélyezése |
| [A like megkövetelése](scripts/enforce-like-pattern.md) | Győződjön meg arról, erőforrásnevek felelnek meg hasonló mintát. |
| [Igényelnek-egyeztetési minta](scripts/enforce-match-pattern.md) | Győződjön meg arról, erőforrás megfelel-e az elnevezési minta. |
| [Címke-egyeztetési minta megkövetelése](scripts/enforce-tag-match-pattern.md) | Győződjön meg arról, hogy a címke értéke megegyezik-e egy szöveges mintával. |
|**Hálózat**||
| [Engedélyezett alkalmazás Gateway SKU-n](scripts/allowed-app-gate-sku.md) | Szükséges, hogy alkalmazásátjárót használja-e egy jóváhagyott Termékváltozat. Megadhatja a jóváhagyott termékváltozatok tömbjét. |
| [Ha régió nem engedélyezett a hálózati figyelőt naplózása](scripts/net-watch-not-enabled.md) | Naplózási feladatok végrehajtását, ha a megadott régió nem engedélyezett a hálózati figyelőt. Megadhatja, hogy ellenőrizze, hogy engedélyezve van-e hálózati figyelőt a régió nevét. |
| [Minden hálózati adapter által NSG X](scripts/nsg-on-nic.md) | Megköveteli, hogy egy adott hálózati biztonsági csoportot használt minden virtuális hálózati adapteren. Megadhatja a hálózati biztonsági csoport azonosítója. |
| [NSG X minden alhálózaton](scripts/nsg-on-subnet.md) | Megköveteli, hogy egy adott hálózati biztonsági csoportot minden virtuális alhálózatban használt. Megadhatja a hálózati biztonsági csoport azonosítója. |
| [Engedélyezett Express Route sávszélesség](scripts/allowed-er-band.md) | Megköveteli, hogy a kifejezett útvonalakat sávszélesség megadott készletének használata. Megadja az Express Route megadható termékváltozatok tömbjét. |
| [Engedélyezett az Express Route társviszony-létesítési helye](scripts/allowed-peering-er.md) | Megköveteli, hogy az Express útvonalak használata megadva társviszony-létesítési helyek. Megadja az engedélyezett társviszony-létesítési helyek tömbjét. |
| [Engedélyezett Expressroute termékváltozatok](scripts/allowed-er-skus.md) | Verziójához egy jóváhagyott Termékváltozata Express útvonalakat használni. Megadja az engedélyezett termékváltozatok tömbjét. |
| [Engedélyezett a Load Balancer termékváltozatok](scripts/allowed-lb-skus.md) | Szükséges, hogy a terheléselosztó egy jóváhagyott SKU használja. Megadja az engedélyezett termékváltozatok tömbjét. |
| [Egyetlen hálózathoz sem társviszony-létesítést úgy ER hálózati](scripts/no-peering-er-net.md) | Ez a beállítás letiltja a társítás hálózathoz megadott erőforráscsoport-társviszony létesítése – hálózati. Amellyel meggátolhatja, központi felügyelt hálózati infrastruktúra-kapcsolatot. Megadhatja a társítás megelőzése érdekében az erőforráscsoport nevét. |
| [Nincs felhasználó által megadott útvonal tábla](scripts/no-user-def-route-table.md)  |Megakadályozza, hogy a virtuális hálózatok üzembe helyezéséhez egy felhasználó által definiált útválasztási táblázathoz. |
| [Engedélyezett a virtuális hálózati átjáró-termékváltozat](scripts/no-user-def-route-table.md) | Megköveteli, hogy a virtuális hálózati átjárók egy jóváhagyott SKU és átjáró típusú használja. Megadhatja a jóváhagyott termékváltozatok tömbje és jóváhagyott átjáró típusok egy tömbjét. |
| [Virtuális gép hálózati adapterek használatát a jóváhagyott IP-alhálózatot](scripts/use-approved-subnet-vm-nics.md) | Szükséges, hogy a hálózati adapterek egy jóváhagyott alhálózat használja. Megadja a jóváhagyott alhálózati Azonosítóját. |
| [Jóváhagyott vNet használja a virtuális gép hálózati illesztő](scripts/use-approved-vnet-vm-nics.md) | Megköveteli, hogy a hálózati adapterek jóváhagyott virtuális hálózat használata. Megadja a jóváhagyott virtuális hálózat Azonosítójává. |
|**Címkék**||
| [Számlázási címkék házirend kezdeményezés](scripts/billing-tags-policy-init.md) | Költség center és a termék nevét megadott kód paraméterértékek szükségesek. Beépített házirendek vonatkoznak, és kényszerítse a szükséges címkéket használ. Megadhatja a szükséges értékeket a címkék.  |
| [Címke és az értékét az erőforráscsoportokkal](scripts/enforce-tag-rg.md) | Címke és erőforráscsoport érték szükséges. Megadhatja a szükséges címke nevét és értékét.  |
|**SQL**||
| [SQL-adatbázis szintjét naplózási naplózása](scripts/audit-sql-db-audit-setting.md) | SQL-adatbázis naplózási beállításainak eseményeket, ha ezek a beállítások nem egyezik a megadott értéket. Adjon meg egy értéket, amely jelzi, hogy naplózási beállításai engedélyezve legyen-e vagy le van tiltva.  |
| [Naplózási átlátható titkosítási állapot](scripts/audit-trans-data-enc-status.md) | SQL adatbázis átlátható adattitkosítás eseményeket, ha engedélyezve van.  |
| [Naplózási DB szintű fenyegetések észlelése beállítás](scripts/audit-db-threat-det-setting.md) | SQL-adatbázis biztonsági riasztási házirendek eseményeket, ha ezek a házirendek nem meghatározott állapotú. Adjon meg egy értéket, amely jelzi, hogy fenyegetésészlelés engedélyezve vagy letiltva.  |
| [SQL Server szint naplózási naplózása](scripts/audit-sql-ser-leve-audit-setting.md) | SQL server naplózási beállításai eseményeket, ha ezek a beállítások nem egyezik a megadott értéket. Adjon meg egy értéket, amely jelzi, hogy naplózási beállításai engedélyezve legyen-e vagy le van tiltva. |
| [Naplózási szint fenyegetések észlelése beállítást](scripts/audit-sql-ser-threat-det-setting.md) | SQL-adatbázis biztonsági riasztási házirendek eseményeket, ha ezek a házirendek nem meghatározott állapotú. Adjon meg egy értéket, amely jelzi, hogy fenyegetésészlelés engedélyezve vagy letiltva.  |
| [Azure Active Directory-rendszergazda nélkül naplózása](scripts/audit-no-aad-admin.md) | Ha az SQL-kiszolgáló nem Azure Active Directory rendszergazdája naplózási. |
| [SQL DB termékváltozatok engedélyezett](scripts/allowed-sql-db-skus.md) | Szükséges SQL-adatbázisok használata egy jóváhagyott Termékváltozat. Megadja az engedélyezett SKU-azonosítók tömbje vagy engedélyezett SKU nevének tömbjét. |
|**Tárolás**||
| [A Storage-fiókok és a virtuális gépek engedélyezett termékváltozatok](scripts/allowed-skus-storage.md) | Verziójához jóváhagyott termékváltozatok storage-fiókok és a virtuális gépek használni. Beépített házirendet használja, hogy jóvá SKU. Megadhatja, hogy engedélyezett virtuális gépek termékváltozatok tömbje és jóváhagyott tárfiók termékváltozatok tömbjét. |
| [Ellenőrizze a tárfiók csak a https-forgalom](scripts/ensure-https-stor-acct.md) | Storage-fiókok használatához a HTTPS-forgalmat igényel.  |
| [Storage-fiókok rétegezéséhez ritkán használt adatok elérésének megtagadása](scripts/deny-cool-access-tiering.md) | Ez a beállítás letiltja a ritkán használt adatok hozzáférési tárolótömbökhöz blob storage-fiókok használatát.  |
| [Ellenőrizze a tárolási fájlok titkosítását](scripts/ensure-store-file-enc.md) | Megköveteli, hogy a fájl titkosítás engedélyezve van a storage-fiókok.  |
|**Beépített házirend**||
| [Engedélyezett helyek](scripts/allowed-locs.md) | Megköveteli, hogy az összes erőforrás telepítve vannak-e a jóváhagyott helyekre. Megadja az engedélyezett helyek tömb.  |
| [Engedélyezett erőforrástípusok](scripts/allowed-res-types.md) | Biztosítja, hogy csak a jóváhagyott erőforrástípusok vannak telepítve. Megadja az engedélyezett típusok egy tömbjét.  |
| [A tárfiók termékváltozatok engedélyezett](scripts/allowed-stor-acct-skus.md) | Megköveteli, hogy a storage-fiókok egy jóváhagyott SKU használja. Megadhatja a jóváhagyott termékváltozatok tömbjét. |
| [Alkalmazza a címke és az alapértelmezett értékre](scripts/apply-tag-def-val.md) | Hozzáfűz egy megadott tag nevét és értékét, ha a címke nem áll rendelkezésre. Megadhatja a címke nevét és az érték érvényes.  |
| [Címke és annak értéke](scripts/enforce-tag-val.md) | Egy megadott tag neve és értéke szükséges. Megadhatja a címke nevét és értékét érvényesítését.  |
| [Nem engedélyezett típusú erőforrások](scripts/not-allowed-res-type.md) | Ez a beállítás letiltja a megadott erőforrás típusú központi telepítés. Megadhat letilthatja az erőforrástípusok tömbjét.  |
| [SQL Server verziója 12.0 megkövetelése](scripts/req-sql-12.md) | Szükséges SQL-kiszolgálók 12.0 verzióját használja.  |
| [Tárolási fiók titkosításának kötelezővé tétele](scripts/req-store-acct-enc.md) | A tárolási fiók használata blob-titkosítást igényel.  |

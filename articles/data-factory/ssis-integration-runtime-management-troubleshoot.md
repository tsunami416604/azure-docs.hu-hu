---
title: A Azure Data Factory SSIS Integration Runtime felügyeletének hibáinak megoldása | Microsoft Docs
description: Ez a cikk hibaelhárítási útmutatást nyújt a SSIS Integration Runtime (SSIS IR) felügyeleti problémáival kapcsolatban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253019"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>A SSIS Integration Runtime-kezelés hibáinak megoldása Azure Data Factory

Ez a dokumentum a SSIS Integration Runtime (SSIS IR) felügyeleti problémáira vonatkozó hibaelhárítási útmutatókat tartalmaz.

## <a name="overview"></a>Áttekintés

Hibaüzenet jelenik meg az ADF-portálon, vagy ha bármilyen probléma merül fel a SSIS integrációjának kiépítésével vagy megszüntetésével kapcsolatban, akkor a PowerShell-parancsmagból lesz visszaküldve. A hiba formátuma mindig egy részletes hibaüzenettel rendelkező hibakód.

Ez azt jelenti, hogy a szolgáltatásnak van némi átmeneti problémája, ha a hibakód InternalServerError. Később újra megpróbálkozhat a művelettel. Ha az Újrapróbálkozás nem segít, lépjen kapcsolatba Azure Data Factory támogatási csapatával.

Három fő külső függőség is okozhat hibákat: Azure SQL Database kiszolgáló vagy felügyelt példány, egyéni telepítési parancsfájl és Virtual Network konfiguráció, ha a hibakód nem InternalServerError.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database kiszolgáló vagy felügyelt példány problémái

Azure SQL Database-kiszolgáló vagy felügyelt példány szükséges, ha a SSIS IR-t a SSIS-katalógus adatbázisával kívánja kiépíteni. A Azure SQL Database-kiszolgálónak vagy a felügyelt példánynak elérhetőnek kell lennie a SSIS IR-nek. A Azure SQL Database-kiszolgáló vagy a felügyelt példány fiókjának rendelkeznie kell engedéllyel a SSIS-katalógus adatbázisának létrehozásához (SSISDB). Ha bármilyen hiba merül fel, a részletes SQL-kivételt jelző hibaüzenet jelenik meg az ADF-portálon. A hibakódok hibaelhárításához kövesse az alábbi lépéseket.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ez a probléma akkor fordulhat elő, ha egy új SSIS IR-t vagy az IR-t futtat.

A következő okok okozhatják, ha a hiba az IR-kiépítés során megjelenik, és részletes SqlException üzenetet kap a hibaüzenetben.

* Hálózati csatlakoztatási probléma. Győződjön meg arról, hogy a SQL Server vagy a felügyelt példány állomásneve elérhető, és nincs olyan tűzfal-vagy NSG-blokk, amely SSIS az IR-t a kiszolgáló eléréséhez.
* A bejelentkezés sikertelen volt, és az SQL-hitelesítés használatban van. Ez azt jelenti, hogy a megadott fiók nem tud bejelentkezni a SQL Serverba. Győződjön meg arról, hogy a megfelelő felhasználói fiók van megadva.
* A bejelentkezés sikertelen volt, és a rendszer HRE hitelesítést (felügyelt identitást) használ. Adja hozzá a gyár felügyelt identitását egy HRE-csoporthoz, és tegye elérhetővé a felügyelt identitás hozzáférési engedélyeit a katalógus adatbázis-kiszolgálójához.
* A kapcsolat időtúllépése, mindig a biztonsággal kapcsolatos konfiguráció miatt. Javasoljuk, hogy hozzon létre egy új virtuális gépet, a virtuális gépet ugyanahhoz a VNet csatlakoztatja, ha az IR egy VNet, majd telepítse a SSMS-t, és ellenőrizze a Azure SQL Database-kiszolgáló vagy a felügyelt példány állapotát.

Egyéb problémák esetén tekintse meg a részletes SQL-kivételt jelző hibaüzenetet, és javítsa ki a hibaüzenetben bemutatott problémát. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba Azure SQL Database-kiszolgálóval vagy a felügyelt példány támogatási csapatával.

Előfordulhat, hogy bizonyos hálózati biztonsági csoportok vagy tűzfalak módosulnak, ha az IR futtatása során hiba jelenik meg, ami azt eredményezi, hogy a SSIS IR feldolgozó csomópontja már nem fér hozzá a Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz. Oldja fel a SSIS IR feldolgozó csomópontot a Azure SQL Database-kiszolgáló vagy a felügyelt példány eléréséhez.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

A hibaüzenet a következőhöz hasonló: "a SSISDB adatbázis elérte a méretre vonatkozó kvótát. Particionálja vagy törölje az adatvesztést, indexeli az indexeket, vagy a lehetséges megoldásokról a dokumentációban tájékozódhat. A lehetséges megoldások a következők:
* Növelje a SSISDB méretét.
* Módosítsa a SSISDB konfigurációját, hogy csökkentse a méretet, például:
   * A megőrzési időtartam és a projekt-verziók számának csökkentése.
   * A napló megőrzési idejének csökkentése.
   * A napló alapértelmezett szintjének módosítása és így tovább.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Ez a hiba azt jelenti, hogy a Azure SQL Database-kiszolgálónak vagy a felügyelt példánynak már van egy másik IR által létrehozott és használt SSISDB. Egy másik Azure SQL Database-kiszolgálót vagy felügyelt példányt kell megadnia, vagy törölnie kell a meglévő SSISDB, és újra kell indítania az új IR-t.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Ezt a hibát az alábbi okok okozhatják:

* A SSIS IR-hez konfigurált felhasználói fióknak nincs engedélye az adatbázis létrehozására. Megadhatja, hogy a felhasználó jogosult legyen az adatbázis létrehozására.
* Hozza létre az adatbázis időtúllépését, például a végrehajtás időtúllépését, az adatbázis-művelet időtúllépését, és így tovább. Később újra megtudhatja, hogy megoldódott-e a probléma. Ha az Újrapróbálkozás nem sikerül, lépjen kapcsolatba a Azure SQL Database-kiszolgálóval vagy a felügyelt példány támogatási csoportjával.

Egyéb problémák esetén olvassa el az SQL-kivételt jelző hibaüzenetet, és javítsa ki a hibaüzenetben említett problémát. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba Azure SQL Database kiszolgálóval vagy a felügyelt példány támogatási csapatával.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

A hibaüzenet a következőhöz hasonló: "érvénytelen objektumnév" Catalog. catalog_properties ".", vagy már rendelkezik egy SSISDB nevű adatbázissal, de a SSIS IR nem hozza létre, vagy az adatbázis érvénytelen állapotban van, amelyet az utolsó SSIS IR-kiépítés hibát okoz. Elhúzhatja a meglévő adatbázist a SSISDB néven, vagy konfigurálhat egy új Azure SQL Database-kiszolgálót vagy felügyelt példányt az IR-hez.

## <a name="custom-setup"></a>Egyéni telepítés

Az egyéni telepítő felületet biztosít a saját telepítési lépéseinek hozzáadásához a SSIS IR üzembe helyezése vagy újrakonfigurálása során. További információ: [a telepítő testreszabása az Azure-SSIS Integration Runtime számára](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Győződjön meg arról, hogy a tároló csak a szükséges egyéni telepítőfájlokat tartalmazza, mivel a tárolóban lévő összes fájl le lesz töltve a SSIS IR Worker csomópontra. Javasoljuk, hogy tesztelje az egyéni telepítési parancsfájlt egy helyi gépen a parancsfájl-végrehajtási hibák kijavításához, mielőtt futtatja a szkriptet a SSIS IR-ben.

Az egyéni telepítési parancsfájl-tárolót a rendszer az IR futtatása során is ellenőrzi, mivel a SSIS IR rendszeresen frissül, és újra el kell érnie a tárolót az egyéni telepítési parancsfájl letöltéséhez és a telepítéshez. Az ellenőrzésbe beletartozik, hogy a tároló elérhető-e, és hogy létezik-e a Main. cmd fájl.

Az egyéni beállításokkal kapcsolatos hibák esetén a Code CustomSetupScriptFailure hibaüzenet jelenik meg, és az alhibakódot tartalmazó hibaüzenetet fogja látni.  Az alárendelt hibakódok hibaelhárításához kövesse az alábbi lépéseket.  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Ez azt jelenti, hogy az SSIS IR nem fér hozzá az Azure Blob-tárolóhoz az egyéni telepítéshez. Győződjön meg arról, hogy a tároló SAS URI-ja elérhető, és nem járt le.

Először állítsa le az IR-t, ha az IR fut állapotban van, konfigurálja újra az IR-t az új egyéni telepítési tároló SAS URI azonosítóval, majd indítsa újra az IR-t.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Ez azt jelenti, hogy a SSIS IR nem találja a blob-tárolóban az egyéni telepítési parancsfájlt (Main. cmd). Győződjön meg arról, hogy a Main. cmd létezik a tárolóban, amely az egyéni telepítés belépési pontja.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Ez azt jelenti, hogy nem sikerült végrehajtani az egyéni telepítési parancsfájlt (Main. cmd), a parancsfájlt a helyi gépen is kipróbálhatja, vagy megtekintheti az egyéni telepítési végrehajtási naplókat a blob-tárolóban.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Ez azt jelenti, hogy az egyéni telepítési parancsfájl időtúllépését hajtja végre. Győződjön meg arról, hogy a blob-tároló csak a szükséges egyéni telepítőfájlokat tartalmazza. A blob-tárolóban is megtekintheti az egyéni telepítési végrehajtási naplókat. Az egyéni beállítás maximális időtartama 45 percen belül van beállítva, és a maximális időtartam magában foglalja az összes fájl letöltésének idejét a tárolóból, és telepíti őket a SSIS IR-be. Ha hosszabb időszakra van szükség, akkor egy támogatási jegyet kell felhívnia.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Ez azt jelenti, hogy nem sikerült feltölteni az egyéni telepítési végrehajtási naplókat a blob-tárolóba, mert az SSIS IR nem rendelkezik írási engedéllyel a blob-tárolóhoz, vagy valamilyen tárolási vagy hálózati probléma. Ha az egyéni telepítés sikeres, a hiba nem befolyásolja a SSIS függvényt, de a naplók hiányoznak. Ha az egyéni telepítés más hibával meghiúsult, és nem sikerült feltölteni a naplót, először ezt a hibát fogjuk jelenteni, így a napló feltölthető elemzésre, és a probléma megoldása után a rendszer több megadott hibát jelez. Ha a probléma nem oldódik meg az Újrapróbálkozás után, lépjen kapcsolatba Azure Data Factory támogatási csapatával.

## <a name="virtual-network-configuration"></a>Virtuális hálózati konfiguráció

Ha a SSIS IR-t egy Virtual Networkba (VNet) csatlakoztatja, a felhasználói előfizetés alatt a VNet használja. További információkért lásd: [Az Azure SSIS Integration Runtime csatlakoztatása egy virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Ha VNet kapcsolatos probléma merül fel, az alábbi hibaüzenet jelenik meg

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Lehetséges, hogy az oka eltérő lehet. Az alárendelt hibakódok hibaelhárításához kövesse az alábbi lépéseket.

### <a name="forbidden"></a>Tiltott

A hibaüzenet a következőhöz hasonló: "az aktuális fiókhoz nincs engedélyezve a denetid. A Microsoft. batch erőforrás-szolgáltató nincs regisztrálva a VNet azonos előfizetésében. "

Ez azt jelenti, Azure Batch nem fér hozzá a VNet. Regisztrálja a Microsoft. batch erőforrás-szolgáltatót a VNet azonos előfizetése alatt.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

A hibaüzenet a következőhöz hasonló: "vagy a megadott VNet nem létezik, vagy a Batch szolgáltatás nem fér hozzá" vagy "a megadott alhálózat XXX nem létezik".

Ez azt jelenti, hogy a VNet nem létezik, vagy Azure Batch szolgáltatás nem fér hozzá, vagy a megadott alhálózat nem létezik. Győződjön meg arról, hogy a VNet és az alhálózat létezik, és Azure Batch férhet hozzájuk.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Az üzenet a következőhöz hasonló: "nem sikerült kiépíteni Integration Runtime a vnet-ben. Ha a DNS-kiszolgáló vagy a NSG beállításai konfigurálva vannak, ellenőrizze, hogy a DNS-kiszolgáló elérhető-e, és hogy a NSG megfelelően van-e konfigurálva.

Valószínű, hogy rendelkezik a DNS-kiszolgáló vagy a NSG beállításainak testreszabott konfigurációjával, ami miatt a SSIS IR által igényelt Azure-kiszolgáló neve nem oldható fel, vagy nem érhető el. További információ: [SSIS IR VNet konfigurációs](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) dokumentum. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba Azure Data Factory támogatási csapatával.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

A SSIS IR automatikusan frissül rendszeresen, és új Azure Batch készlet jön létre a frissítés során, a régi Azure Batch készlet pedig törölve lesz, a régi készlethez kapcsolódó VNet pedig törlődik, és az új VNet kapcsolódó erőforrás lesz létrehozva a előfizetés. Ez a hiba azt jelenti, hogy nem sikerült törölni a régi készlethez kapcsolódó VNet-erőforrást, mert az előfizetés vagy az erőforráscsoport szintjén törli a zárolást. Segítség a törlési zárolás eltávolításához.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

A SSIS IR-kiépítés sikertelen volt, mert az OK oka, és ha hiba történik, a rendszer az összes létrehozott erőforrást törli. A VNet-erőforrásokat azonban nem sikerült törölni, mert az előfizetés vagy az erőforráscsoport szintjén erőforrás-törlési zárolás van. Távolítsa el a törlési zárolást, és indítsa újra az IR-t.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

A SSIS IR leállításakor a rendszer az VNet-hez kapcsolódó összes erőforrást törli, de a törlés sikertelen volt, mert az előfizetés vagy az erőforráscsoport szintjén erőforrás-törlési zárolás történt. Segítsen eltávolítani a törlési zárolást, és próbálkozzon újra a leállítássel.

### <a name="nodeunavailable"></a>NodeUnavailable

Ez a hiba akkor fordul elő, ha az IR fut, az azt jelenti, hogy az IR állapota megszakad, és nem Kifogástalan állapotba kerül, ezért a DNS-kiszolgáló vagy a NSG konfigurációja módosult, mert a SSIS IR nem tud kapcsolódni a függő szolgáltatáshoz, a DNS-kiszolgáló vagy a NSG konfigurációs problémáinak kijavításához. További információ: [SSIS IR VNet-konfiguráció](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba Azure Data Factory támogatási csapatával.

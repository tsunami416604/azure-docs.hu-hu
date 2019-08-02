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
ms.openlocfilehash: 8abffdf443e26c03c38c12a3947a47a94157c9da
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609621"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>A SSIS Integration Runtime-kezelés hibáinak megoldása Azure Data Factory

Ez a cikk hibaelhárítási útmutatást nyújt az Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), más néven SSIS IR kezelési problémáira.

## <a name="overview"></a>Áttekintés

Ha bármilyen probléma merül fel a SSIS IR kiépítése vagy megszüntetése során, hibaüzenet jelenik meg a Microsoft Azure Data Factory portálon, vagy egy PowerShell-parancsmag által visszaadott hibát. A hiba mindig egy részletes hibaüzenettel rendelkező hibakód formájában jelenik meg.

Ha a hibakód InternalServerError, a szolgáltatás átmeneti problémákba ütközik, és később újra kell próbálkoznia a művelettel. Ha egy újrapróbálkozás nem segít, lépjen kapcsolatba a Azure Data Factory támogatási csapatával.

Ellenkező esetben a három fő külső függőség hibákhoz vezethet: egy Azure SQL Database-kiszolgáló vagy egy felügyelt példány, egy egyéni telepítési parancsfájl és egy virtuális hálózati konfiguráció.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database kiszolgáló vagy felügyelt példány problémái

Ha a SSIS IR-t egy SSIS-katalógus-adatbázissal szeretné kiépíteni, akkor szükség van egy Azure SQL Database-kiszolgálóra vagy felügyelt példányra. A SSIS IR-nek képesnek kell lennie hozzáférni a Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz. Emellett a Azure SQL Database-kiszolgáló vagy a felügyelt példány fiókjának engedéllyel kell rendelkeznie SSIS-katalógus-adatbázis (SSISDB) létrehozásához. Ha hiba történik, egy részletes SQL-kivételt tartalmazó hibakód jelenik meg a Data Factory-portálon. Az alábbi listában található információk segítségével elháríthatja a hibakódokat.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ez a probléma akkor fordulhat elő, ha új SSIS-t épít ki, vagy ha az IR fut. Ha ezt a hibát tapasztalja az IR kiépítés során, akkor a következő problémák valamelyikét jelző hibaüzenet jelenik meg:

* Hálózati csatlakoztatási probléma. Győződjön meg arról, hogy a SQL Server vagy a felügyelt példány állomásneve elérhető. Azt is ellenőrizze, hogy a tűzfal vagy a hálózati biztonsági csoport (NSG) nem blokkolja-e a kiszolgálóhoz való SSIS IR-hozzáférést.
* A bejelentkezés sikertelen volt az SQL-hitelesítés során. A megadott fiók nem tud bejelentkezni a SQL Server adatbázisba. Győződjön meg arról, hogy a megfelelő felhasználói fiókot adja meg.
* A bejelentkezés sikertelen volt Microsoft Azure Active Directory (Azure AD) hitelesítés (felügyelt identitás) során. Adja hozzá a gyár felügyelt identitását egy HRE-csoporthoz, és győződjön meg arról, hogy a felügyelt identitás rendelkezik hozzáférési engedélyekkel a katalógus adatbázis-kiszolgálójához.
* Kapcsolat időtúllépése. Ezt a hibát mindig egy biztonsággal kapcsolatos konfiguráció okozta. Javasoljuk, hogy:
  1. Hozzon létre egy új virtuális gépet.
  1. Csatlakoztassa a virtuális GÉPET ugyanahhoz a Microsoft Azure Virtual Networkhoz, ha az IR virtuális hálózaton van.
  1. Telepítse a SSMS, és győződjön meg arról, hogy a Azure SQL Database-kiszolgáló vagy a felügyelt példány állapota.

Egyéb problémák esetén javítsa ki az SQL-kivételek részletes hibaüzenetét. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba a Azure SQL Database-kiszolgálóval vagy a felügyelt példány támogatási csoportjával.

Ha a hiba akkor jelenik meg, ha az IR fut, a hálózati biztonsági csoport vagy a tűzfal módosításai valószínűleg meggátolják, hogy a SSIS IR feldolgozó csomópont hozzáférjen a Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz. Oldja fel a SSIS IR feldolgozó csomópontját, hogy az hozzáférhessen a Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

A következő hibaüzenetek jelenhetnek meg: "A" SSISDB "adatbázis elérte a méretre vonatkozó kvótát. Particionálja vagy törölje az adatvesztést, indexeli az indexeket, vagy a lehetséges megoldásokról a dokumentációban tájékozódhat. 

A lehetséges megoldások a következők:
* Növelje a SSISDB kvótájának méretét.
* Módosítsa a SSISDB konfigurációját a méret csökkentése érdekében:
   * A megőrzési időtartam és a projekt-verziók számának csökkentése.
   * A napló megőrzési idejének csökkentése.
   * A napló alapértelmezett szintjének módosítása.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Ez a hiba azt jelenti, hogy a Azure SQL Database-kiszolgálónak vagy a felügyelt példánynak már van SSISDB, és azt egy másik IR használja. Meg kell adnia egy másik Azure SQL Database-kiszolgálót vagy felügyelt példányt, vagy törölni kell a meglévő SSISDB, és újra kell indítania az új IR-t.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Ez a hiba a következő okok egyike miatt fordulhat elő:

* A SSIS IR-hez konfigurált felhasználói fiók nem rendelkezik engedéllyel az adatbázis létrehozásához. Engedélyt adhat a felhasználónak az adatbázis létrehozásához.
* Időtúllépés történik az adatbázisok létrehozásakor, például végrehajtási időtúllépés vagy adatbázis-művelet időtúllépése esetén. Később próbálja megismételni a műveletet. Ha az Újrapróbálkozás nem sikerül, lépjen kapcsolatba a Azure SQL Database-kiszolgálóval vagy a felügyelt példány támogatási csoportjával.

Egyéb problémák esetén keresse meg az SQL-kivételt jelző hibaüzenetet, és javítsa ki a hiba részleteiben említett problémát. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba a Azure SQL Database-kiszolgálóval vagy a felügyelt példány támogatási csoportjával.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Az ilyen típusú hibaüzenet így néz ki: "Érvénytelen objektumnév:" Catalog. catalog_properties ". Ebben az esetben vagy már rendelkezik egy SSISDB nevű adatbázissal, de nem a SSIS IR hozta létre, vagy az adatbázis érvénytelen állapotban van, amelyet az utolsó SSIS IR-kiépítés során hibák okoztak. A meglévő adatbázist elhúzhatja a SSISDB néven, vagy beállíthat egy új Azure SQL Database-kiszolgálót vagy felügyelt példányt az IR-hez.

## <a name="custom-setup-issues"></a>Egyéni telepítési problémák

Az egyéni telepítő felületet biztosít a saját telepítési lépéseinek hozzáadásához a SSIS IR üzembe helyezése vagy újrakonfigurálása során. További információ: [Az Azure-SSIS Integration Runtime telepítőjének testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Győződjön meg arról, hogy a tároló csak a szükséges egyéni telepítőfájlokat tartalmazza; a tároló összes fájlja le lesz töltve a SSIS IR Worker csomópontra. Azt javasoljuk, hogy a parancsfájlnak a SSIS IR-ben való futtatása előtt tesztelje az egyéni telepítési parancsfájlt egy helyi gépen a parancsfájl-végrehajtási hibák javításához.

Az egyéni telepítési parancsfájl-tárolót a rendszer ellenőrzi, hogy az IR fut-e, mivel a SSIS IR frissítése rendszeresen megtörténik. Ehhez a frissítéshez hozzá kell férnie a tárolóhoz az egyéni telepítési parancsfájl letöltéséhez, majd újra kell telepítenie. A folyamat azt is ellenőrzi, hogy a tároló elérhető-e, illetve hogy létezik-e a Main. cmd fájl.

Az egyéni telepítéssel kapcsolatos bármilyen hiba esetén egy CustomSetupScriptFailure-hibakód jelenik meg, például a CustomSetupScriptBlobContainerInaccessible vagy a CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Ez a hiba azt jelenti, hogy a SSIS IR nem fér hozzá az Azure Blob-tárolóhoz az egyéni telepítéshez. Győződjön meg arról, hogy a tároló SAS URI-ja elérhető, és nem járt le.

Állítsa le az IR-t, ha az fut, konfigurálja újra az IR-t az új egyéni telepítési tároló SAS URI azonosítóval, majd indítsa újra az IR-t.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Ez a hiba azt jelenti, hogy a SSIS IR nem talál egyéni telepítési parancsfájlt (Main. cmd) a blob-tárolóban. Győződjön meg arról, hogy a Main. cmd létezik a tárolóban, amely az egyéni telepítés belépési pontja.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Ez a hiba azt jelenti, hogy az egyéni telepítési parancsfájl (Main. cmd) végrehajtása sikertelen volt. Először próbálja ki a parancsfájlt a helyi gépen, vagy tekintse meg az egyéni telepítési végrehajtási naplókat a blob-tárolón.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Ez a hiba az egyéni telepítési parancsfájl végrehajtásának időtúllépését jelzi. Győződjön meg arról, hogy a blob-tároló csak a szükséges egyéni telepítőfájlokat tartalmazza. A blob-tárolóban is ellenőriznie kell az egyéni telepítési végrehajtási naplókat. Az egyéni telepítés maximális időtartama 45 perc, mielőtt időtúllépés történik, és a maximális időtartam magában foglalja az összes fájl letöltésének idejét a tárolóból, és telepíti őket a SSIS IR-re. Ha hosszabb időszakra van szüksége, emeljen egy támogatási jegyet.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Ez a hiba azt jelenti, hogy az egyéni telepítési végrehajtási naplók blob-tárolóba való feltöltésére tett kísérlet sikertelen volt. Ez a probléma akkor fordul elő, ha a SSIS IR nem rendelkezik írási engedéllyel a blob-tárolóhoz, illetve tárolási vagy hálózati problémák miatt. Ha az egyéni telepítés sikeres, a hiba nem befolyásolja a SSIS függvényt, de a naplók hiányoznak. Ha az egyéni telepítés egy másik hibával meghiúsul, és a napló nincs feltöltve, a rendszer először ezt a hibát fogja jelenteni, hogy a naplót fel lehessen tölteni az elemzéshez. Emellett a probléma megoldása után minden további konkrét problémát jelenteni fogunk. Ha a probléma egy újrapróbálkozás után nem oldható fel, forduljon a Azure Data Factory támogatási csapatához.

## <a name="virtual-network-configuration"></a>Virtuális hálózati konfiguráció

Ha a SSIS IR-t az Azure Virtual Networkhoz csatlakoztatja, a SSIS IR a felhasználói előfizetéshez tartozó virtuális hálózatot használja. További információt az [Azure-SSIS Integration Runtime csatlakoztatása virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)című témakörben talál.

Virtual Network kapcsolatos probléma esetén a következő hibák valamelyikét fogja látni.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Ez a hiba többféle okból is bekövetkezhet. A hibák megoldásához tekintse meg a [tiltott](#forbidden), a [InvalidPropertyValue](#invalidpropertyvalue)és a [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) szakaszt.

### <a name="forbidden"></a>Tiltott

Az ilyen típusú hiba a következőhöz hasonló lehet: "A denetid nincs engedélyezve az aktuális fiókhoz. A Microsoft. batch erőforrás-szolgáltató nincs regisztrálva a VNet azonos előfizetésében. "

Ezek a részletek azt jelentik, hogy Azure Batch nem fér hozzá a virtuális hálózathoz. Regisztrálja a Microsoft. batch erőforrás-szolgáltatót ugyanahhoz az előfizetéshez, mint a Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Az ilyen típusú hibák a következőkhöz hasonlóak lehetnek: 

- "A megadott VNet nem létezik, vagy a Batch szolgáltatásnak nincs hozzáférése."
- "A megadott alhálózat (XXX) nem létezik."

Ezek a hibák azt jelentik, hogy a virtuális hálózat nem létezik, az Azure Batch szolgáltatás nem fér hozzá, vagy a megadott alhálózat nem létezik. Győződjön meg arról, hogy a virtuális hálózat és az alhálózat létezik, és hogy Azure Batch el tudja érni őket.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Az ilyen típusú hibaüzenet a következőképpen néz ki: "Nem sikerült kiépíteni a Integration Runtimet a VNet-ben. Ha a DNS-kiszolgáló vagy a NSG beállításai konfigurálva vannak, ellenőrizze, hogy a DNS-kiszolgáló elérhető-e, és hogy a NSG megfelelően van-e konfigurálva. "

Ebben az esetben valószínűleg a DNS-kiszolgáló vagy a NSG beállításainak testreszabott konfigurációja van, ami megakadályozza, hogy az SSIS IR-kiszolgáló nevét megoldják vagy hozzáférjenek. További információ: [SSIS IR Virtual Network Configuration](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba a Azure Data Factory támogatási csapatával.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

A SSIS IR-t rendszeresen automatikusan frissíti a rendszer. A rendszer a frissítés során új Azure Batch készletet hoz létre, a régi Azure Batch-készletet pedig törli. A régi készlet Virtual Network kapcsolódó erőforrásait is törli a rendszer, és az új Virtual Network kapcsolódó erőforrások az előfizetése alatt jönnek létre. Ez a hiba azt jelenti, hogy az előfizetéshez vagy az erőforráscsoport szintjéhez tartozó törlési zárolás miatt nem sikerült törölni a régi készlethez Virtual Network kapcsolódó erőforrásokat. Mivel az ügyfél szabályozza és beállítja a törlési zárolást, ebben az esetben el kell távolítania a törlési zárolást.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Ha az SSIS IR-kiépítés meghiúsul, a rendszer az összes létrehozott erőforrást törli. Ha azonban van erőforrás-törlési zárolás az előfizetés vagy az erőforráscsoport szintjén, Virtual Network erőforrás nem törlődik a várt módon. A hiba kijavításához távolítsa el a törlési zárolást, és indítsa újra az IR-t.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Ha leállítja a SSIS IR-t, a rendszer a Virtual Networkhoz kapcsolódó összes erőforrást törli. A törlés azonban sikertelen lehet, ha az előfizetés vagy az erőforráscsoport szintjén van erőforrás-törlési zárolás. Itt is az ügyfél vezérli és beállítja a törlési zárolást. Ezért el kell távolítania a törlési zárolást, majd újra le kell állítania a SSIS IR-t.

### <a name="nodeunavailable"></a>NodeUnavailable

Ez a hiba akkor fordul elő, ha az IR fut, és az azt jelenti, hogy az IR állapota sérült. Ezt a hibát mindig a DNS-kiszolgáló vagy a NSG konfigurációjának változása okozta, amely blokkolja a SSIS IR-t a szükséges szolgáltatáshoz való csatlakozáskor. Mivel a DNS-kiszolgáló és a NSG konfigurációját az ügyfél vezérli, az ügyfélnek ki kell javítania a letiltási problémákat a végén. További információ: [SSIS IR Virtual Network Configuration](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba a Azure Data Factory támogatási csapatával.

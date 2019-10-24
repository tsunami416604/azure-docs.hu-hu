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
ms.openlocfilehash: d16267e104d753770dc40ce99b0f56e5c749b2d0
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72752170"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>A SSIS Integration Runtime-kezelés hibáinak megoldása Azure Data Factory

Ez a cikk hibaelhárítási útmutatást nyújt az Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), más néven SSIS IR kezelési problémáira.

## <a name="overview"></a>Áttekintés

Ha bármilyen probléma merül fel a SSIS IR kiépítése vagy megszüntetése során, hibaüzenet jelenik meg a Microsoft Azure Data Factory portálon, vagy egy PowerShell-parancsmag által visszaadott hibát. A hiba mindig egy részletes hibaüzenettel rendelkező hibakód formájában jelenik meg.

Ha a hibakód InternalServerError, a szolgáltatás átmeneti problémákba ütközik, és később újra kell próbálkoznia a művelettel. Ha egy újrapróbálkozás nem segít, lépjen kapcsolatba a Azure Data Factory támogatási csapatával.

Ellenkező esetben a három fő külső függőség hibákhoz vezethet: egy Azure SQL Database-kiszolgáló vagy egy felügyelt példány, egy egyéni telepítési parancsfájl és egy virtuális hálózati konfiguráció.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database kiszolgáló vagy felügyelt példány problémái

Azure SQL Database-kiszolgálóra vagy felügyelt példányra van szükség, ha SSIS IR-t helyez üzembe egy SSIS-katalógusadatbázissal. Az SSIS IR-nek hozzáféréssel kell rendelkeznie az Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz. Az Azure SQL Database-kiszolgáló vagy a felügyelt példány fiókjának is szüksége van engedélyre az SSIS-katalógusadatbázis (SSISDB) létrehozásához. Ha hiba történik, a Data Factory portálján megjelenik a hibakód és egy, az SQL-kivétellel kapcsolatos részletes üzenet. Az alábbi listában található információkkal értelmezheti a hibakódokat.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ezt a hibát akkor láthatja, ha új SSIS IR-t helyez üzembe, vagy miközben az IR fut. Ha ezt a hibát az IR üzembe helyezése során tapasztalja, akkor az SQL-kivétellel kapcsolatos részletes üzenetet találhat a hibaüzenetben, amely a következő problémák valamelyikét jelzi:

* Hálózati kapcsolati hiba. Ellenőrizze, hogy az SQL Server vagy a felügyelt példány gazdagépneve elérhető-e. Azt is ellenőrizze, hogy nem blokkolja-e tűzfal vagy hálózati biztonsági csoport (NSG) az SSIS IR-t a kiszolgáló elérése során.
* A bejelentkezés sikertelen volt az SQL-hitelesítés során. A megadott fiókkal nem lehet bejelentkezni az SQL Server-adatbázisba. Ügyeljen rá, hogy helyes felhasználói fiókot adjon meg.
* A bejelentkezés sikertelen volt a Microsoft Azure Active Directory- (Azure AD-) hitelesítés során (felügyelt identitás). Adja hozzá a gyár felügyelt identitását egy AAD-csoporthoz, és győződjön meg arról, hogy a felügyelt identitás rendelkezik hozzáférési engedélyekkel a katalógus adatbázis-kiszolgálójához.
* Kapcsolati időtúllépés. Ezt a hibát mindig egy biztonsággal kapcsolatos konfiguráció okozza. A következő megoldást javasoljuk:
  1. Hozzon létre egy új virtuális gépet.
  1. Csatlakoztassa a virtuális GÉPET ugyanahhoz a Microsoft Azure Virtual Networkhoz, ha az IR virtuális hálózaton van.
  1. Telepítse a SSMS, és győződjön meg arról, hogy a Azure SQL Database-kiszolgáló vagy a felügyelt példány állapota.

Egyéb problémák esetén javítsa ki az SQL-kivétellel kapcsolatos részletes hibaüzenetben látható hibát. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az Azure SQL Database-kiszolgáló vagy a felügyelt példány támogatási csapatával.

Ha a hiba akkor jelentkezik, amikor az IR fut, akkor valószínűleg a hálózati biztonsági csoport vagy a tűzfal módosításai miatt nem fér hozzá az SSIS IR munkavégző csomópontja az Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz. Oldja fel az SSIS IR munkavégző csomópontjára vonatkozó zárolást, hogy az hozzáférhessen az Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

A következő hibaüzenet jelenhet meg: "a (z) SSISDB adatbázis elérte a méretre vonatkozó kvótát. Particionálja vagy törölje az adatvesztést, indexeli az indexeket, vagy a lehetséges megoldásokról a dokumentációban tájékozódhat. 

A lehetséges megoldások a következők:
* Növelje a SSISDB kvótájának méretét.
* Módosítsa az SSISDB konfigurációját a méret csökkentéséhez a következőképpen:
   * A megőrzési időtartam és a projekt-verziók számának csökkentése.
   * A napló megőrzési idejének csökkentése.
   * A napló alapértelmezett szintjének módosítása.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Ez a hiba azt jelenti, hogy az Azure SQL Database-kiszolgáló vagy a felügyelt példány már rendelkezik SSISDB adatbázissal, és azt egy másik IR használja. Meg kell adnia egy másik Azure SQL Database-kiszolgálót vagy felügyelt példányt, vagy törölnie kell a meglévő SSISDB adatbázist, és újra kell indítania az új IR-t.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Ez a hiba a következő okok valamelyike miatt jelentkezhet:

* Az SSIS IR-hez konfigurált felhasználói fiók nem rendelkezik engedéllyel az adatbázis létrehozásához. Engedélyt adhat a felhasználónak az adatbázis létrehozásához.
* Időtúllépés (például végrehajtási időtúllépés vagy adatbázis-műveleti időtúllépés) történik az adatbázis létrehozása során. Próbálja meg később újra végrehajtani a műveletet. Ha az újrapróbálkozás nem működik, lépjen kapcsolatba az Azure SQL Database-kiszolgáló vagy a felügyelt példány támogatási csapatával.

Egyéb problémák esetén tekintse meg az SQL-kivétel hibaüzenetét, és javítsa ki a hiba részleteiben szereplő problémát. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az Azure SQL Database-kiszolgáló vagy a felügyelt példány támogatási csapatával.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Az ilyen típusú hibaüzenet így néz ki: "érvénytelen objektumnév" Catalog. catalog_properties ". Ebben az esetben vagy már rendelkezik egy SSISDB nevű adatbázissal, de nem a SSIS IR hozta létre, vagy az adatbázis érvénytelen állapotban van, amelyet az utolsó SSIS IR-kiépítés során hibák okoztak. Elvetheti az SSISDB nevű meglévő adatbázist, vagy új Azure SQL Database-kiszolgálót vagy felügyelt példányt konfigurálhat az IR-hez.

## <a name="custom-setup-issues"></a>Egyéni telepítési problémák

Az egyéni telepítés során használható felülten hozzáadhatja saját telepítési lépései az SSIS IR üzembe helyezése vagy újrakonfigurálása során. További információ: [Az Azure SSIS IR telepítésének testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Győződjön meg arról, hogy a tároló csak a szükséges egyéni telepítőfájlokat tartalmazza; a tároló összes fájlja le lesz töltve az SSIS IR feldolgozó csomópontjára. Azt javasoljuk, hogy a szkript az SSIS IR-ban való futtatása előtt tesztelje az egyéni telepítési szkriptet egy helyi gépen a szkriptvégrehajtási hibák javítása érdekében.

Az egyéni telepítési szkripttárolót a rendszer az integrációs modul futása közben ellenőrzi, mivel az SSIS IR rendszeresen frissül. Ennek a frissítésnek hozzá kell férnie a tárolóhoz az egyéni telepítési szkript letöltéséhez és újratelepítéséhez. A folyamat azt is ellenőrzi, hogy a tároló elérhető-e, illetve hogy létezik-e a main.cmd fájl.

Az egyéni telepítéssel kapcsolatos bármilyen hiba esetén egy CustomSetupScriptFailure-hibakód jelenik meg, például a CustomSetupScriptBlobContainerInaccessible vagy a CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Ez a hiba azt jelenti, hogy az SSIS IR nem fér hozzá az Azure Blob-tárolóhoz az egyéni telepítés céljából. Győződjön meg róla, hogy a tároló SAS URI-ja elérhető és érvényes.

Ha az integrációs modul fut, állítsa le, konfigurálja újra az új egyéni telepítési tároló SAS URI-jával, majd indítsa újra.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Ez a hiba azt jelenti, hogy az SSIS IR nem talál egyéni telepítési szkriptet (main.cmd) a blobtárolóban. Győződjön meg arról, hogy a main.cmd létezik a tárolóban, amely belépési pontként szolgál az egyéni telepítéshez.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Ez a hiba azt jelenti, hogy az egyéni telepítési szkript (main.cmd) végrehajtása sikertelen volt. Először próbálja ki a helyi gépén a szkriptet, vagy ellenőrizze az egyéni telepítés végrehajtási naplóit a blobtárolóban.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Ez a hiba az egyéni telepítési szkript végrehajtásának időtúllépését jelzi. Győződjön meg arról, hogy a parancsfájl csendesen hajtható végre, és nincs szükség interaktív bevitelre, és győződjön meg arról, hogy a blob-tároló csak a szükséges egyéni telepítőfájlokat tartalmazza. Először a parancsfájlt kell tesztelni a helyi gépen. Az egyéni telepítés végrehajtási naplóit is ellenőriznie kell a blobtárolóban. Az egyéni telepítés maximális időtartama 45 perc, mielőtt időtúllépés történik, amely magában foglalja az összes fájl a tárolóból való letöltéséhez, majd az SSIS IR-ban való telepítéséhez szükséges időt is. Ha hosszabb időre van szüksége, hozzon létre egy támogatási jegyet.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Ez a hiba azt jelenti, hogy az egyéni telepítés végrehajtási naplóinak a blobtárolóba való feltöltésére tett kísérlet sikertelen volt. Ez a probléma akkor fordul elő, ha a SSIS IR nem rendelkezik írási engedéllyel a blobtárolóhoz, vagy ha tárolási vagy hálózati problémák állnak fenn. Ha az egyéni telepítés sikeres, a hiba nem befolyásolja a SSIS működését, de a naplók hiányozni fognak. Ha az egyéni telepítés egy másik hiba miatt meghiúsul, és a napló nincs feltöltve, ezt a hibát jelentjük először, hogy a naplót fel lehessen tölteni az elemzéshez. Emellett a probléma megoldása után minden további konkrét problémát jelenteni fogunk. Ha ez a probléma nem oldódik meg az újrapróbálkozás után, lépjen kapcsolatba az Azure Data Factory támogatási csapatával.

## <a name="virtual-network-configuration"></a>Virtuális hálózati konfiguráció

Amikor az SSIS integrációs modult az Azure Virtual Networkhöz csatlakoztatja, a SSIS IR a felhasználói előfizetéshez tartozó virtuális hálózatot használja. További információért tekintse meg [az Azure-SSIS IR virtuális hálózathoz történő csatlakoztatásával](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) foglalkozó cikket.

Ha virtuális hálózattal kapcsolatos probléma jelentkezik, az alábbi hibák egyike jelenik meg.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Ennek a hibának több különböző okból jelentkezhet. A hiba elhárításához tekintse meg a [Forbidden](#forbidden), az [InvalidPropertyValue](#invalidpropertyvalue) és a [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) szakaszt.

### <a name="forbidden"></a>Forbidden

Ez a hiba a következőhöz hasonló lehet: "a denetid nincs engedélyezve az aktuális fiókhoz. A Microsoft. batch erőforrás-szolgáltató nincs regisztrálva a VNet azonos előfizetésében. "

Ezek a részletek azt jelentik, hogy az Azure Batch nem fér hozzá a virtuális hálózathoz. Regisztrálja a Microsoft.Batch erőforrás-szolgáltatót ugyanabban az előfizetésében, amelybe a virtuális hálózat is tartozik.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Az ilyen típusú hiba az alábbiakhoz lehet hasonló: 

- "A megadott VNet nem létezik, vagy a Batch szolgáltatásnak nincs hozzáférése."
- "A megadott alhálózat (XXX) nem létezik."

Ezek a hibák azt jelentik, hogy a virtuális hálózat nem létezik, az Azure Batch szolgáltatás nem fér hozzá, vagy a megadott alhálózat nem létezik. Győződjön meg arról, hogy a virtuális hálózat és az alhálózat létezik, illetve hogy az Azure Batch hozzájuk tud férni.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

A következő hibaüzenet így néz ki: "nem sikerült kiépíteni a Integration Runtime a VNet. Ha a DNS-kiszolgáló vagy a NSG beállításai konfigurálva vannak, ellenőrizze, hogy a DNS-kiszolgáló elérhető-e, és hogy a NSG megfelelően van-e konfigurálva. "

Ebben az esetben valószínűleg egyéni konfigurációt alkalmazott a DNS-kiszolgáló vagy a hálózati biztonsági csoport beállításait illetően, amely megakadályozza az SSIS IR által igényelt Azure-kiszolgálónév feloldását vagy elérését. További információ: [Az SSIS IR virtuális hálózatának konfigurálása](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az Azure Data Factory támogatási csapatával.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Az SSIS IR rendszeres időközönként automatikusan frissül. A frissítés során létrejön egy új Azure Batch-készlet, a régi Azure Batch-készlet pedig törlődik. A régi készlet virtuális hálózathoz kapcsolódó erőforrásait is törli a rendszer, az új virtuális hálózathoz kapcsolódó erőforrások pedig az előfizetése alatt jönnek létre. Ez a hiba azt jelenti, hogy az előfizetés vagy az erőforráscsoport szintjén érvényben lévő törlési zárolás miatt nem sikerült törölni a régi készlet virtuális hálózathoz kapcsolódó erőforrásait. Mivel az ügyfél szabályozza és állítja be a törlési zárolást, ilyen esetben neki kell eltávolítania a törlési zárolást.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Ha az SSIS IR üzembe helyezése meghiúsul, a rendszer az összes létrehozott erőforrást törli. Ha viszont van egy erőforrástörlési zárolás az előfizetés vagy az erőforráscsoport szintjén, a virtuális hálózat erőforrásai nem törlődnek a várt módon. A hiba kijavításához oldja fel a törlési zárolást, majd indítsa újra az integrációs modult.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Ha leállítja a SSIS IR-t, a rendszer a virtuális hálózathoz kapcsolódó összes erőforrást törli. A törlés azonban meghiúsulhat, ha előfizetés vagy az erőforráscsoport szintjén erőforrástörlési zárolás van életben. Itt is az ügyfél szabályozza és állítja be a törlési zárolást. Ezért el kell távolítaniuk a törlési zárolást, majd újra le kell állítaniuk a SSIS IR-t.

### <a name="nodeunavailable"></a>NodeUnavailable

Ez a hiba akkor fordul elő, ha az integrációs modul fut, és azt jelenti, hogy az integrációs modul állapota nem megfelelő. Ezt a hibát mindig a DNS-kiszolgáló vagy a hálózati biztonsági csoport konfigurációjának változása okozza, amely blokkolja a SSIS IR-t a szükséges szolgáltatáshoz való csatlakozáskor. Mivel a DNS-kiszolgáló és a hálózati biztonsági csoport konfigurációját az ügyfél szabályozza, az ügyfélnek kell kijavítania a blokkolást okozó problémákat a saját oldaláról. További információ: [Az SSIS IR virtuális hálózatának konfigurálása](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az Azure Data Factory támogatási csapatával.

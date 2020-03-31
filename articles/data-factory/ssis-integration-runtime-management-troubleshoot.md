---
title: SSIS-integrációs futásidejű felügyelet – problémamegoldás
description: Ez a cikk hibaelhárítási útmutatást nyújt az SSIS-integrációs futásidejű (SSIS IR) felügyeleti problémáihoz
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 52b1d93935e6428563c72361655893ffddf8a507
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74941855"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>SSIS-integrációs futásidejű felügyelet – problémamegoldás az Azure Data Factoryban

Ez a cikk hibaelhárítási útmutatást nyújt az Azure-SQL Server Integration Services (SSIS) integrációs futásidejű (IR), más néven SSIS IR felügyeleti problémáihoz.

## <a name="overview"></a>Áttekintés

Ha az SSIS-ir kiépítése vagy megszüntetése során bármilyen probléma lép fel, hibaüzenet jelenik meg a Microsoft Azure Data Factory portálon, vagy egy PowerShell-parancsmagból visszaadott hiba. A hiba mindig egy hibakód formátumában jelenik meg, részletes hibaüzenettel.

Ha a hibakód InternalServerError, a szolgáltatás átmeneti problémákat, és próbálkozzon újra a művelet később. Ha egy újrapróbálkozás nem segít, forduljon az Azure Data Factory támogatási csapatához.

Ellenkező esetben három fő külső függőség okozhat hibákat: egy Azure SQL Database-kiszolgáló vagy felügyelt példány, egy egyéni beállítási parancsfájl és egy virtuális hálózati konfiguráció.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database-kiszolgálóval vagy felügyelt példányokkal kapcsolatos problémák

Azure SQL Database-kiszolgálóra vagy felügyelt példányra van szükség, ha SSIS IR-t helyez üzembe egy SSIS-katalógusadatbázissal. Az SSIS IR-nek hozzáféréssel kell rendelkeznie az Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz. Az Azure SQL Database-kiszolgáló vagy a felügyelt példány fiókjának is szüksége van engedélyre az SSIS-katalógusadatbázis (SSISDB) létrehozásához. Ha hiba történik, a Data Factory portálján megjelenik a hibakód és egy, az SQL-kivétellel kapcsolatos részletes üzenet. Az alábbi listában található információkkal értelmezheti a hibakódokat.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ezt a hibát akkor láthatja, ha új SSIS IR-t helyez üzembe, vagy miközben az IR fut. Ha ezt a hibát az IR üzembe helyezése során tapasztalja, akkor az SQL-kivétellel kapcsolatos részletes üzenetet találhat a hibaüzenetben, amely a következő problémák valamelyikét jelzi:

* Hálózati kapcsolati hiba. Ellenőrizze, hogy az SQL Server vagy a felügyelt példány gazdagépneve elérhető-e. Azt is ellenőrizze, hogy nem blokkolja-e tűzfal vagy hálózati biztonsági csoport (NSG) az SSIS IR-t a kiszolgáló elérése során.
* A bejelentkezés sikertelen volt az SQL-hitelesítés során. A megadott fiókkal nem lehet bejelentkezni az SQL Server-adatbázisba. Ügyeljen rá, hogy helyes felhasználói fiókot adjon meg.
* A bejelentkezés sikertelen volt a Microsoft Azure Active Directory- (Azure AD-) hitelesítés során (felügyelt identitás). Adja hozzá a gyár felügyelt identitását egy AAD-csoporthoz, és győződjön meg arról, hogy a felügyelt identitás rendelkezik hozzáférési engedélyekkel a katalógus adatbázis-kiszolgálójához.
* Kapcsolati időtúllépés. Ezt a hibát mindig egy biztonsággal kapcsolatos konfiguráció okozza. A következő megoldást javasoljuk:
  1. Hozzon létre egy új virtuális gép.
  1. Csatlakozzon a virtuális gépugyanahhoz a Microsoft Azure virtuális infravörös hálózathoz, ha az infravörös kapcsolat egy virtuális hálózatban van.
  1. Telepítse az SSMS-t, és ellenőrizze az Azure SQL Database-kiszolgálót vagy a felügyelt példány állapotát.

Egyéb problémák esetén javítsa ki az SQL-kivétellel kapcsolatos részletes hibaüzenetben látható hibát. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az Azure SQL Database-kiszolgáló vagy a felügyelt példány támogatási csapatával.

Ha a hiba akkor jelentkezik, amikor az IR fut, akkor valószínűleg a hálózati biztonsági csoport vagy a tűzfal módosításai miatt nem fér hozzá az SSIS IR munkavégző csomópontja az Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz. Oldja fel az SSIS IR munkavégző csomópontjára vonatkozó zárolást, hogy az hozzáférhessen az Azure SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Így nézhet ki ez a hibaüzenet: "Az adatbázis "SSISDB" elérte a méretkvótáját. Adatok felosztása vagy törlése, indexek eldobása vagy a dokumentációban a lehetséges megoldásokért." 

A lehetséges megoldások a következők:
* Növelje az SSISDB kvótaméretét.
* Módosítsa az SSISDB konfigurációját a méret csökkentéséhez a következőképpen:
   * A megőrzési időszak és a projektverziók számának csökkentése.
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

Ez a hibaüzenet így néz ki: "Érvénytelen objektumnév :'catalog.catalog_properties". Ebben az esetben vagy már rendelkezik Egy SSISDB nevű adatbázissal, de azt nem az SSIS IR hozta létre, vagy az adatbázis érvénytelen állapotban van, amelyet a legutóbbi SSIS infravörös kiépítés hibái okoznak. Elvetheti az SSISDB nevű meglévő adatbázist, vagy új Azure SQL Database-kiszolgálót vagy felügyelt példányt konfigurálhat az IR-hez.

## <a name="custom-setup-issues"></a>Egyéni beállítási problémák

Az egyéni telepítés során használható felülten hozzáadhatja saját telepítési lépései az SSIS IR üzembe helyezése vagy újrakonfigurálása során. További információ: [Az Azure SSIS IR telepítésének testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Győződjön meg arról, hogy a tároló csak a szükséges egyéni telepítőfájlokat tartalmazza; a tároló összes fájlja le lesz töltve az SSIS IR feldolgozó csomópontjára. Azt javasoljuk, hogy a szkript az SSIS IR-ban való futtatása előtt tesztelje az egyéni telepítési szkriptet egy helyi gépen a szkriptvégrehajtási hibák javítása érdekében.

Az egyéni telepítési szkripttárolót a rendszer az integrációs modul futása közben ellenőrzi, mivel az SSIS IR rendszeresen frissül. Ennek a frissítésnek hozzá kell férnie a tárolóhoz az egyéni telepítési szkript letöltéséhez és újratelepítéséhez. A folyamat azt is ellenőrzi, hogy a tároló elérhető-e, illetve hogy létezik-e a main.cmd fájl.

Minden olyan hiba esetén, amely egyéni telepítést tartalmaz, egy CustomSetupScriptFailure hibakódot fog látni alkóddal, például CustomSetupScriptBlobContainerInaccessible vagy CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Ez a hiba azt jelenti, hogy az SSIS IR nem fér hozzá az Azure Blob-tárolóhoz az egyéni telepítés céljából. Győződjön meg róla, hogy a tároló SAS URI-ja elérhető és érvényes.

Ha az integrációs modul fut, állítsa le, konfigurálja újra az új egyéni telepítési tároló SAS URI-jával, majd indítsa újra.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Ez a hiba azt jelenti, hogy az SSIS IR nem talál egyéni telepítési szkriptet (main.cmd) a blobtárolóban. Győződjön meg arról, hogy a main.cmd létezik a tárolóban, amely belépési pontként szolgál az egyéni telepítéshez.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Ez a hiba azt jelenti, hogy az egyéni telepítési szkript (main.cmd) végrehajtása sikertelen volt. Először próbálja ki a helyi gépén a szkriptet, vagy ellenőrizze az egyéni telepítés végrehajtási naplóit a blobtárolóban.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Ez a hiba az egyéni telepítési szkript végrehajtásának időtúllépését jelzi. Győződjön meg arról, hogy a szkript végrehajtható csendes módban, felhasználói beavatkozás nélkül, és hogy a blobtároló csak a szükséges egyéni telepítési fájlokat tartalmazza. Javasoljuk, hogy a szkriptet először tesztelje a helyi számítógépen. Az egyéni telepítés végrehajtási naplóit is ellenőriznie kell a blobtárolóban. Az egyéni telepítés maximális időtartama 45 perc, mielőtt időtúllépés történik, amely magában foglalja az összes fájl a tárolóból való letöltéséhez, majd az SSIS IR-ban való telepítéséhez szükséges időt is. Ha hosszabb időre van szüksége, hozzon létre egy támogatási jegyet.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Ez a hiba azt jelenti, hogy az egyéni telepítés végrehajtási naplóinak a blobtárolóba való feltöltésére tett kísérlet sikertelen volt. Ez a probléma akkor fordul elő, ha a SSIS IR nem rendelkezik írási engedéllyel a blobtárolóhoz, vagy ha tárolási vagy hálózati problémák állnak fenn. Ha az egyéni telepítés sikeres, a hiba nem befolyásolja a SSIS működését, de a naplók hiányozni fognak. Ha az egyéni telepítés egy másik hiba miatt meghiúsul, és a napló nincs feltöltve, ezt a hibát jelentjük először, hogy a naplót fel lehessen tölteni az elemzéshez. Emellett a probléma megoldása után minden további konkrét problémát jelenteni fogunk. Ha ez a probléma nem oldódik meg az újrapróbálkozás után, lépjen kapcsolatba az Azure Data Factory támogatási csapatával.

## <a name="virtual-network-configuration"></a>Virtuális hálózat konfigurációja

Amikor az SSIS integrációs modult az Azure Virtual Networkhöz csatlakoztatja, a SSIS IR a felhasználói előfizetéshez tartozó virtuális hálózatot használja. További információért tekintse meg [az Azure-SSIS IR virtuális hálózathoz történő csatlakoztatásával](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) foglalkozó cikket.

Ha virtuális hálózattal kapcsolatos probléma jelentkezik, az alábbi hibák egyike jelenik meg.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Ennek a hibának több különböző okból jelentkezhet. A hiba elhárításához tekintse meg a [Forbidden](#forbidden), az [InvalidPropertyValue](#invalidpropertyvalue) és a [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) szakaszt.

### <a name="forbidden"></a>Forbidden

Ez a fajta hiba a következőhöz hasonlíthat: "Az AlnetId nincs engedélyezve az aktuális fiókhoz. A Microsoft.Batch erőforrás-szolgáltató nincs regisztrálva a Virtuális hálózat azonos előfizetése alatt."

Ezek a részletek azt jelentik, hogy az Azure Batch nem fér hozzá a virtuális hálózathoz. Regisztrálja a Microsoft.Batch erőforrás-szolgáltatót ugyanabban az előfizetésében, amelybe a virtuális hálózat is tartozik.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Az ilyen típusú hiba az alábbiakhoz lehet hasonló: 

- "A megadott virtuális hálózat nem létezik, vagy a Batch szolgáltatás nem fér hozzá."
- "A megadott xxx alhálózat nem létezik."

Ezek a hibák azt jelentik, hogy a virtuális hálózat nem létezik, az Azure Batch szolgáltatás nem fér hozzá, vagy a megadott alhálózat nem létezik. Győződjön meg arról, hogy a virtuális hálózat és az alhálózat létezik, illetve hogy az Azure Batch hozzájuk tud férni.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Ez a fajta hibaüzenet a következőkre néz ki: "Nem sikerült kiépíteni az integrációs futásidejűt a virtuális hálózatban. Ha a DNS-kiszolgáló vagy az NSG-beállítások konfigurálva vannak, győződjön meg arról, hogy a DNS-kiszolgáló elérhető, és az NSG megfelelően van konfigurálva."

Ebben az esetben valószínűleg egyéni konfigurációt alkalmazott a DNS-kiszolgáló vagy a hálózati biztonsági csoport beállításait illetően, amely megakadályozza az SSIS IR által igényelt Azure-kiszolgálónév feloldását vagy elérését. További információ: [Az SSIS IR virtuális hálózatának konfigurálása](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az Azure Data Factory támogatási csapatával.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Az SSIS IR rendszeres időközönként automatikusan frissül. A frissítés során létrejön egy új Azure Batch-készlet, a régi Azure Batch-készlet pedig törlődik. A régi készlet virtuális hálózathoz kapcsolódó erőforrásait is törli a rendszer, az új virtuális hálózathoz kapcsolódó erőforrások pedig az előfizetése alatt jönnek létre. Ez a hiba azt jelenti, hogy az előfizetés vagy az erőforráscsoport szintjén érvényben lévő törlési zárolás miatt nem sikerült törölni a régi készlet virtuális hálózathoz kapcsolódó erőforrásait. Mivel az ügyfél szabályozza és állítja be a törlési zárolást, ilyen esetben neki kell eltávolítania a törlési zárolást.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Ha az SSIS IR üzembe helyezése meghiúsul, a rendszer az összes létrehozott erőforrást törli. Ha viszont van egy erőforrástörlési zárolás az előfizetés vagy az erőforráscsoport szintjén, a virtuális hálózat erőforrásai nem törlődnek a várt módon. A hiba kijavításához oldja fel a törlési zárolást, majd indítsa újra az integrációs modult.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Ha leállítja a SSIS IR-t, a rendszer a virtuális hálózathoz kapcsolódó összes erőforrást törli. A törlés azonban meghiúsulhat, ha előfizetés vagy az erőforráscsoport szintjén erőforrástörlési zárolás van életben. Itt is az ügyfél szabályozza és állítja be a törlési zárolást. Ezért el kell távolítaniuk a törlési zárolást, majd újra le kell állítaniuk a SSIS IR-t.

### <a name="nodeunavailable"></a>NodeUnavailable

Ez a hiba akkor fordul elő, ha az integrációs modul fut, és azt jelenti, hogy az integrációs modul állapota nem megfelelő. Ezt a hibát mindig a DNS-kiszolgáló vagy a hálózati biztonsági csoport konfigurációjának változása okozza, amely blokkolja a SSIS IR-t a szükséges szolgáltatáshoz való csatlakozáskor. Mivel a DNS-kiszolgáló és a hálózati biztonsági csoport konfigurációját az ügyfél szabályozza, az ügyfélnek kell kijavítania a blokkolást okozó problémákat a saját oldaláról. További információ: [Az SSIS IR virtuális hálózatának konfigurálása](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az Azure Data Factory támogatási csapatával.

## <a name="static-public-ip-addresses-configuration"></a>Statikus nyilvános IP-címek konfigurációja

Amikor csatlakozik az Azure-SSIS IR-hez az Azure Virtuális hálózathoz, saját statikus nyilvános IP-címeket is hozhat létre az infravörös rendszerhez, hogy az infravörös hozzáférés olyan adatforrásokhoz férjen hozzá, amelyek korlátozzák a hozzáférést az adott IP-címekhez. További információért tekintse meg [az Azure-SSIS IR virtuális hálózathoz történő csatlakoztatásával](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) foglalkozó cikket.

A fenti virtuális hálózati problémák mellett statikus nyilvános IP-címekkel kapcsolatos problémákat is megtapasztalhat. Kérjen segítséget a következő hibákból.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>Érvénytelen PublicIPSpecified

Ez a hiba az Azure-SSIS ir indításakor számos okból fordulhat elő:

| Hibaüzenet | Megoldás|
|:--- |:--- |
| A megadott statikus nyilvános IP-cím már használatban van, kérjük, adjon meg két nem használt is az Azure-SSIS-integrációs futásidejű. | Válasszon ki két nem használt statikus nyilvános IP-címet, vagy távolítsa el a megadott nyilvános IP-címre mutató aktuális hivatkozásokat, majd indítsa újra az Azure-SSIS IR-t. |
| A megadott statikus nyilvános IP-cím nem rendelkezik DNS-névvel, kérjük, adja meg kettő dns-nevét az Azure-SSIS-integrációs futásidejű. | A nyilvános IP-cím DNS-nevét az Azure Portalon is beállíthatja, ahogy az alábbi képen látható. A konkrét lépések a következők: (1) Nyissa meg az Azure portalt, és lépjen a nyilvános IP-cím erőforrásoldalára; (2) Válassza ki a **Konfiguráció** szakaszt, és állítsa be a DNS-nevet, majd kattintson a **Mentés** gombra; (3) Indítsa újra az Azure-SSIS IR.(3) Indítsa újra az Azure-SSIS IR.(3) Indítsa újra az Azure-SSIS IR |
| A megadott virtuális hálózat és statikus nyilvános IP-címek az Azure-SSIS-integrációs futásidejű kell lennie ugyanazon a helyen. | Az Azure Network követelményeinek megfelelően a statikus nyilvános IP-cím és a virtuális hálózat ugyanazon a helyen és előfizetésben kell lennie. Adjon meg két érvényes statikus nyilvános IP-címet, és indítsa újra az Azure-SSIS IR-t. |
| A megadott statikus nyilvános IP-cím alapvető, kérjük, két szabványos at az Azure-SSIS-integrációs futásidejű. | Segítségként tekintse meg [a nyilvános IP-cím sk-jait.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) |

![Azure-SSIS integrációs modul](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedAStart közben

Ha az Azure-SSIS infravörös kiépítés sikertelen, az összes létrehozott erőforrás törlődik. Ha azonban az előfizetés vagy az erőforráscsoport (amely a statikus nyilvános IP-címet tartalmazza) erőforrás-törlési zárolása van, a hálózati erőforrások nem törlődnek a várt módon. A hiba kijavításához távolítsa el a törlési zárolást, és indítsa újra az infravörös állapotot.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Ha leállítja az Azure-SSIS IR-t, a nyilvános IP-címet tartalmazó erőforráscsoportban létrehozott összes hálózati erőforrás törlődik. De a törlés sikertelen lehet, ha van egy erőforrás törlési zárolása az előfizetés vagy erőforráscsoport (amely tartalmazza a statikus nyilvános IP-cím) szinten. Távolítsa el a törlési zárolást, és indítsa újra az infravörös rendszert.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedAfrissítés közben

Az Azure-SSIS ir rendszeresen automatikusan frissül. Új infravörös csomópontok jönnek létre a frissítés során, és a régi csomópontok törlődnek. Emellett a régi csomópontok létrehozott hálózati erőforrásai (például a terheléselosztó és a hálózati biztonsági csoport) törlődnek, és az új hálózati erőforrások az előfizetés alatt jönnek létre. Ez a hiba azt jelenti, hogy a régi csomópontok hálózati erőforrásainak törlése sikertelen volt az előfizetés vagy erőforráscsoport (amely a statikus nyilvános IP-címet) szintjén lévő törlési zárolás miatt nem sikerült. Távolítsa el a törlési zárolást, hogy megtisztíthassuk a régi csomópontokat, és felszabadíthassuk a régi csomópontok statikus nyilvános IP-címét. Ellenkező esetben a statikus nyilvános IP-cím nem adható ki, és nem tudjuk tovább frissíteni az infravörös rendszert.

### <a name="publicipnotusableduringupgrade"></a>Nyilvános IPNotUsableAfrissítés közben

Ha saját statikus nyilvános IP-címeket szeretne hozni, két nyilvános IP-címet kell megadni. Ezek közül az egyik et az infravörös csomópontok azonnali létrehozására fogják használni, és egy másikat fog használni az infravörös frissítés során. Ez a hiba akkor fordulhat elő, ha a másik nyilvános IP-cím nem használható a frissítés során. A lehetséges okok az [InvalidPublicIPSpecified című](#InvalidPublicIPSpecified) részben keressék meg.
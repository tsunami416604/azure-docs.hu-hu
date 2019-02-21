---
title: Virtuális hálózatok végpontok, valamint egyetlen vagy készletezett Azure SQL-adatbázisok szabályait |} A Microsoft Docs
description: Egy alhálózat jelölhetnek egy virtuális hálózati szolgáltatásvégpontot. Ezután a végpontot, az ACL-t az Azure SQL Database virtuális hálózati szabály. SQL-adatbázist, majd az összes virtuális gép és az alhálózat más csomópontok érkező kommunikációt fogad.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: vanto, genemi
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: 6ded590ac5a9c30655d8ed19c370ce476d1c9631
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456282"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Az adatbázis-kiszolgálók virtuális hálózati Szolgáltatásvégpontok és szabályok használata

*Virtuális hálózati szabályok* van egy tűzfal biztonsági funkció, amely szabályozza, hogy az adatbázis-kiszolgáló az önálló adatbázisok és rugalmas készlet az Azure-ban [SQL Database](sql-database-technical-overview.md) vagy az adatbázisok a [SQL Data Az adatraktár](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) virtuális hálózatok adott alhálózatain érkező kommunikációt fogad el. Ez a cikk elmagyarázza, hogy miért a virtuális hálózati szabály szolgáltatást néha a legjobb megoldás az, hogy biztonságosan lehetővé teszi a kommunikációt az Azure SQL Database és az SQL Data Warehouse.

> [!IMPORTANT]
> Ez a cikk az Azure SQL-kiszolgálóhoz, és az SQL Database és az SQL Data Warehouse-adatbázisok az Azure SQL-kiszolgálón létrehozott vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database. Ebben a cikkben leírtak *nem* a alkalmazni egy **felügyelt példány** üzembe helyezése az Azure SQL Database, mert nem rendelkezik egy hozzá társított végpontot.

Hozzon létre egy virtuális hálózati szabályt, hogy először lennie kell egy [virtuális hálózati szolgáltatásvégpont] [ vm-virtual-network-service-endpoints-overview-649d] a szabályhoz való hivatkozáshoz.

## <a name="how-to-create-a-virtual-network-rule"></a>Virtuális hálózati szabály létrehozása

Ha csak hozzon létre egy virtuális hálózati szabályt, áttérhet a lépéseket és magyarázat [a cikk későbbi részében](#anchor-how-to-by-using-firewall-portal-59j).

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminológia és leírás

**Virtuális hálózat:** Az Azure-előfizetéséhez társított virtuális hálózatok is rendelkezhet.

**Alhálózat:** Egy virtuális hálózatot tartalmaz **alhálózatok**. Minden olyan Azure virtuális gépeken (VM), amely rendelkezik hozzárendelt alhálózatok. Egy alhálózaton több virtuális gép vagy más számítási csomópontokon is tartalmazhat. A számítási csomópontot, amely a virtuális hálózatán kívüli nem a virtuális hálózat eléréséhez, ha nem konfigurál, hogy engedélyezze a hozzáférést a biztonsági.

**Virtuális hálózati szolgáltatásvégpont:** A [virtuális hálózati szolgáltatásvégpont] [ vm-virtual-network-service-endpoints-overview-649d] egy alhálózat, amelynek a következők: egy vagy több hivatalos Azure-szolgáltatás nevét. Ebben a cikkben azt érdeklő nevét **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve.

**Virtuális hálózati szabályt:** Az SQL Database-kiszolgáló egy virtuális hálózati szabályt egy alhálózatot, amely szerepel az SQL Database-kiszolgálóhoz, hozzáférés-vezérlési lista (ACL). Az SQL Database hozzáférés-vezérlési is az alhálózat tartalmaznia kell a **Microsoft.Sql** írja be a nevet.

Egy virtuális hálózati szabályt arról tájékoztatja, hogy az SQL Database-kiszolgálóhoz, az alhálózaton található minden egyes csomópontjáról-kommunikáció fogadására.

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Egy virtuális hálózati szabályt előnyei

Amíg nem tesz semmit, a virtuális gépeket az alhálózatok nem tud kommunikálni az SQL-adatbázis. Egy műveletet, amely létrehozza a kommunikációt egy virtuális hálózati szabály létrehozása. A közösségértékek a VNet szabály módszer kiválasztása szükség van a versengő, a tűzfal által kínált biztonsági beállításokat érintő összehasonlítása és a kontraszt megbeszélésre.

### <a name="a-allow-access-to-azure-services"></a>A. Azure-szolgáltatásokhoz való hozzáférés engedélyezése

A tűzfal panelnek egy **be-és kikapcsolása** feliratú gomb **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**. A **ON** beállítás lehetővé teszi, hogy az összes Azure IP-címek és az összes Azure-alhálózatok által kezdeményezett kommunikáció. Ezek az Azure IP-címek vagy az alhálózatok előfordulhat, hogy nem kell tulajdonában. Ez **ON** beállítás valószínűleg több nyitva, mint azt szeretné, hogy az SQL Database lennie. A virtuális hálózati szabály funkció lehetővé teszi a sok ennél a részletes.

### <a name="b-ip-rules"></a>B. IP-szabályok

Az SQL Database-tűzfalat lehetővé teszi, hogy adja meg az IP-címtartományok, amelyről kommunikációt fogad az SQL Database-be. Ez a megközelítés is megfelel a stabil, amelyek túlmutatnak az Azure magánhálózati IP-címek. Számos csomópontot az Azure privát hálózaton belül vannak konfigurálva, de *dinamikus* IP-címeket. Dinamikus IP-címek megváltozhatnak, például a virtuális gép újraindításakor. Adja meg a dinamikus IP-cím egy tűzfalszabályt, éles környezetben való folly lenne.

Az IP-beállítás akkor is maradványérték megszerzésével a *statikus* IP-címet a virtuális gép számára. További információkért lásd: [egy virtuális gép magánhálózati IP-címek konfigurálása az Azure portal használatával][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Azonban a statikus IP-megközelítés válhat kezelése bonyolult, és költséges méretekben. A virtuális hálózati szabályok használata egyszerűbb, létrehozására és kezelésére.

> [!NOTE]
> Egy alhálózaton nem lehet még a SQL Database. Ha az Azure SQL Database-kiszolgáló a virtuális hálózat egy alhálózat csomópont volt, a virtuális hálózaton lévő összes csomópont sikerült kommunikálni az SQL-adatbázis. Ebben az esetben a virtuális gépek képes kommunikálni az SQL Database virtuális hálózati szabályt vagy IP-szabályok nélkül.

Azonban 2017. szeptember, az Azure SQL Database szolgáltatás még nincs a szolgáltatások egy alhálózathoz rendelhető.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>A virtuális hálózati szabályok részleteit

Ebben a szakaszban a virtuális hálózati szabályok több részleteit ismerteti.

### <a name="only-one-geographic-region"></a>Csak egy földrajzi régióban

Minden egyes virtuális hálózati szolgáltatásvégpont csak egy Azure-régióra vonatkozik. A végpont nem engedélyezi a más régiókban az alhálózat-kommunikációt fogad.

Minden olyan virtuális hálózati szabály korlátozódik a régiót, amelyben a mögöttes végpont vonatkozik.

### <a name="server-level-not-database-level"></a>Kiszolgálószintű, nem adatbázisszintű

A teljes Azure SQL Database-kiszolgáló nem csak egy adott adatbázist a kiszolgálón, minden egyes virtuális hálózati szabály vonatkozik. Más szóval virtuális hálózati szabályt alkalmazza, a kiszolgáló szintjén, nem az adatbázis szintjén.

- Ezzel szemben az IP-szabályok vagy szintjén is alkalmazható.

### <a name="security-administration-roles"></a>Biztonsági felügyeleti szerepkörök

Nincs a felügyeleti virtuális hálózati Szolgáltatásvégpontok, a biztonsági szerepkörök elkülönítése. A művelet szükség az egyes a következő szerepkörök:

- **Hálózati rendszergazda:** &nbsp; Kapcsolja be a végponthoz.
- **Adatbázis-rendszergazda:** &nbsp; A hozzáférés-vezérlési lista (ACL) a megadott alhálózat hozzáadása az SQL Database-kiszolgáló frissítése.

*Az RBAC alternatív:*

A hálózati rendszergazda és az adatbázis-rendszergazdai szerepkörök kezelése a virtuális hálózati szabályok szükségesnél valamivel rendelkezik. Csak azok a funkciók egy része van szükség.

Lehetősége van a [szerepköralapú hozzáférés-vezérlés (RBAC)] [ rbac-what-is-813s] az Azure-ban, amely csak a szükséges funkcióinak részét, rendelkezik egy egyéni szerepkör létrehozása. Az egyéni szerepkör használható helyett használata esetén a hálózati rendszergazda vagy az adatbázis rendszergazdájával. A biztonsági fenyegetéseknek felületének alacsonyabb, ha egy felhasználó hozzá egy egyéni biztonsági szerepkört, és a felhasználót hozzáadnia a két fő rendszergazdai szerepkör.

> [!NOTE]
> Bizonyos esetekben az Azure SQL Database és az alhálózatok közötti virtuális hálózatok különböző előfizetésekben találhatóak. Ezekben az esetekben biztosítania kell a következő beállításokat:
> - Mindkét előfizetés ugyanahhoz az Azure Active Directory-bérlőhöz kell tartoznia.
> - A felhasználók számára a szükséges műveleteket, például-Szolgáltatásvégpontok engedélyezését, és a egy virtuális hálózat alhálózat hozzáadása az adott kiszolgálóhoz kezdeményezni.
> - Mindkét előfizetéshez rendelkeznie kell a Microsoft.Sql szolgáltató regisztrálva.

## <a name="limitations"></a>Korlátozások

Az Azure SQL Database a virtuális hálózati szabályok funkció a következő korlátozások vonatkoznak:

- Személyes IP-címet a virtuális hálózat/alhálózat a webes alkalmazás is le lehet képezni. Akkor is, ha a Szolgáltatásvégpontok a megadott virtuális hálózat/alhálózat vannak bekapcsolva, a kiszolgáló és a webes alkalmazás közötti kapcsolatait lesz egy Azure nyilvános IP-forráshoz, nem egy virtuális hálózat/alhálózat forrás. Ahhoz, hogy a kapcsolat webalkalmazás és a egy kiszolgálóra, amelyen a VNet tűzfalszabályai, kell **engedélyezése Azure-szolgáltatások kiszolgálói hozzáférésének** a kiszolgálón.

- Az SQL Database-tűzfal, az egyes virtuális hálózati szabályt hivatkozik egy alhálózatra. Ezen hivatkozott alhálózatok ugyanabban a földrajzi régióban az SQL-adatbázist futtató kiszolgálón kell futnia.

- Minden Azure SQL Database-kiszolgáló legfeljebb 128 ACL-bejegyzések bármely megadott virtuális hálózat lehet.

- A virtuális hálózati szabályok alkalmazása csak az Azure Resource Managerbeli virtuális hálózat; és ne [klasszikus üzemi modellben] [ arm-deployment-model-568f] hálózatok.

- Ne tudják bekapcsolni a virtuális hálózati Szolgáltatásvégpontok az Azure SQL Database emellett lehetővé teszi a MySQL és PostgreSQL Azure szolgáltatás végpontjait. Azonban a végpontok ON, a MySQL, illetve PostgreSQL példányokhoz való csatlakozáshoz a végpontról, a kísérletek sikertelenek lehetnek.
  - Az alapul szolgáló oka, hogy a MySQL és a PostgreSQL valószínűleg nem rendelkezik konfigurált virtuális hálózati szabályt. Konfigurálnia kell egy virtuális hálózati szabályt az Azure Database for MySQL-hez, és a PostgreSQL és a kapcsolat sikeres lesz.

- A tűzfal IP-címtartományok alkalmazása a következő hálózati elemek, de a virtuális hálózati szabályok nem:
  - [Site-to-Site (S2S) virtuális magánhálózati (VPN)][vpn-gateway-indexmd-608y]
  - A helyszíni keresztül [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>A Szolgáltatásvégpontok használatának szempontjai

A Szolgáltatásvégpontok Azure SQL Database használatakor, tekintse át az alábbiakat:

- **Az Azure SQL Database nyilvános IP-címek kimenő kötelező**: Hálózati biztonsági csoportok (NSG) kell megnyitnia az Azure SQL Database IP-címek engedélyezi a csatlakozást. Ezt megteheti az NSG-t is [Szolgáltatáscímkék](../virtual-network/security-overview.md#service-tags) az Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Ha használ [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a helyszíni eredetű nyilvános társviszony-létesítéshez vagy Microsoft társviszony-létesítés, lesz azonosítania kell a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. Microsoft-társviszony-létesítés esetén a használt NAT IP-cím(ek)et vagy az ügyfél vagy a szolgáltató adja meg. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).
  
Ahhoz, hogy az Azure SQL Database a kapcsolatcsoport érkező kommunikációt, létre kell hoznia a nyilvános IP-címek a NAT IP hálózati szabályok

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>"Engedélyezi az Azure-szolgáltatások kiszolgálói hozzáférésének" eltávolításának következményei

Hány felhasználó el kívánja távolítani **engedélyezése Azure-szolgáltatások kiszolgálói hozzáférésének** az Azure SQL-kiszolgálók, és cserélje le a VNet tűzfalszabály.
Azonban ennek eltávolítása hatással van a következő funkciókat:

### <a name="import-export-service"></a>Az importálási-exportálási szolgáltatás

Az Azure SQL Database importálás exportálása szolgáltatás fut az Azure-beli virtuális gépeken. Ezek a virtuális gépek nem szerepelnek a virtuális hálózathoz, és ezért lekérése az Azure IP-címet, az adatbázishoz való csatlakozáskor. Az Eltávolítás **engedélyezése Azure-szolgáltatások kiszolgálói hozzáférésének** ezek a virtuális gépek nem tudják az adatbázisok eléréséhez.
Megkerülheti a problémát. Futtassa a BACPAC importálási, vagy a DACFx API segítségével közvetlenül a kódba exportálása. Győződjön meg arról, hogy ez telepítve van egy virtuális Gépet, amely a virtuális hálózat – alhálózat, amelyhez rendelkezik a tűzfalszabály beállításához.

### <a name="sql-database-query-editor"></a>Az SQL Database Query-szerkesztő

Az Azure SQL Database Query-szerkesztő helyezünk üzembe az Azure-beli virtuális gépeken. Ezek a virtuális gépek nem szerepelnek a virtuális hálózathoz. Ezért a virtuális gépek lekérése egy Azure IP-cím, az adatbázishoz való csatlakozáskor. Az Eltávolítás **engedélyezése Azure-szolgáltatások kiszolgálói hozzáférésének**, a virtuális gépek nem tudják elérni az adatbázisok.

### <a name="table-auditing"></a>Táblanaplózás

Jelenleg két módja van az SQL Database naplózás engedélyezéséhez. Táblanaplózás sikertelen lesz, miután engedélyezte a Szolgáltatásvégpontok az Azure SQL-kiszolgálón. Kockázatcsökkentési itt, hogy a blobnaplózást.

### <a name="impact-on-data-sync"></a>Impact on Data Sync

Az Azure SQL Database a Data Sync szolgáltatást, amely csatlakozik az Azure IP-címek használatával adatbázisok rendelkezik. A Szolgáltatásvégpontok használatakor az valószínű, hogy Ön ki fog kapcsolni **engedélyezése Azure-szolgáltatások kiszolgálói hozzáférésének** az SQL Database-kiszolgálóhoz való hozzáférést. Ez megszakítja a Data Sync szolgáltatást.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Virtuális hálózati Szolgáltatásvégpontok használatával és az Azure storage hatása

Az Azure Storage ugyanazokat a funkciókat, amely lehetővé teszi, hogy korlátozza a kapcsolatot az Azure Storage-fiókkal van megvalósítva. Ha ez a funkció használata az Azure SQL Server által használt Azure Storage-fiókot választja, problémákat futtathatja. Egy listát és az Azure SQL Database és az Azure SQL Data Warehouse szolgáltatást, amely érinti ez a hozzászólás mellett van.

### <a name="azure-sql-data-warehouse-polybase"></a>Az Azure SQL Data Warehouse PolyBase

PolyBase az adatok betöltése az Azure SQL Data Warehouse-bA az Azure Storage-fiókok gyakran használatos. Ha az Azure Storage-fiókot, amely adatokat tölt be korlátozza a hozzáférést csak a VNet-alhálózatok halmaza, kapcsolat és a fiók a PolyBase megszakadnak. Mindkét PolyBase engedélyezéséhez importálása és exportálása a forgatókönyvek az Azure SQL Data Warehouse legyen védett virtuális hálózat az Azure-tárolóhoz való kapcsolódás, kövesse az alábbi lépéseket:

#### <a name="prerequisites"></a>Előfeltételek

1.  Ez az Azure PowerShell telepítése [útmutató](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.  Ha rendelkezik egy általános célú v1- vagy blob storage-fiókot, először frissítenie kell, általános célú v2 ez [útmutató](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Rendelkeznie kell **engedélyezése megbízható Microsoft-szolgáltatások a tárfiók** kapcsolva az Azure Storage-fiók **tűzfalak és virtuális hálózatok** beállítások menüjében. Ebben [útmutató](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) további információt.
 
#### <a name="steps"></a>Lépések
1.  A PowerShellben **az SQL Database-kiszolgáló regisztrálása** az Azure Active Directory (AAD):

    ```powershell
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId your-subscriptionId
    Set-AzureRmSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
    ```
    
 1. Hozzon létre egy **általános célú v2-Tárfiók** ez [útmutató](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

    > [!NOTE]
    > - Ha rendelkezik egy általános célú v1- vagy blob storage-fiók, meg kell **először frissítse a v2** ez [útmutató](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
    > - Az Azure Data Lake Storage Gen2 kapcsolatos ismert problémákat, tekintse meg ezt [útmutató](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1.  Lépjen a storage-fiók alatt **hozzáférés-vezérlés (IAM)**, és kattintson a **szerepkör-hozzárendelés hozzáadása**. Rendelje hozzá **Storage-Blobadatok Közreműködője (előzetes verzió)** RBAC szerepkör az SQL Database-kiszolgálóhoz.

    > [!NOTE] 
    > Csak a tulajdonosa a jogosultsággal rendelkező tagok ebben a lépésben hajthat végre. A különféle beépített szerepkörök az Azure-erőforrásokhoz, tekintse meg a [útmutató](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1.  **A Polybase-kapcsolat az Azure Storage-fiókba:**

    1. Hozzon létre egy adatbázist **[főkulcs](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql?view=sql-server-2017)** Ha még nem egy korábban létrehozott:
        ```SQL
        CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
        ```
    
    1. Az adatbázishoz kötődő hitelesítő adatok létrehozása **azonosító = "Felügyeltszolgáltatás-identitást"**:

        ```SQL
        CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
        ```
        > [!NOTE] 
        > - Adjon meg titkos kulcsot az Azure Storage-hozzáférési kulcsot, mert ezt a mechanizmust használ, nem kell [felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) a háttérben.
        > - SZOLGÁLTATÁSIDENTITÁS neve legyen **Managed Service Identity** a PolyBase csatlakozást dolgozhat Azure Storage-fiók védett virtuális hálózathoz.    
    
    1. Külső adatforrás létrehozása abfss: / / az általános célú v2-tárfiók a PolyBase való kapcsolódáshoz séma:

        ```SQL
        CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
        ```
        > [!NOTE] 
        > - Ha már rendelkezik külső táblák társított, általános célú v1- vagy blob storage-fiók, előbb dobja el a külső táblák, és ezután dobja el a megfelelő külső adatforrást. Végül létrehozhatja külső adatforrás abfss: / / séma, általános célú v2-tárfiók a fenti csatlakozik, és hozza létre újból az új külső adatforrást használó külső táblák. Használhat [létrehozása és közzététele parancsfájlok varázsló](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard?view=sql-server-2017) varázslójától létrehozása – a külső táblák megkönnyítése érdekében.
        > - További információ a abfss: / / séma, ebben [útmutató](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
        > - CREATE EXTERNAL DATA SOURCE további információkért tekintse meg a [útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
    1. Lekérdezés a normál használatával [külső táblák](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Az Azure SQL Database Blobnaplózás

Auditnaplók blobnaplózás leküldi a saját tárfiókját. Ha ezt a tárfiókot használ a virtuális hálózatok szolgáltatásvégponti funkciója kapcsolat az Azure SQL Database-ből a tárfiókhoz megszakad.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>A VNet tűzfalszabály nélkül szolgáltatják a virtuális hálózati Szolgáltatásvégpontok hozzáadása a kiszolgálóhoz

Nagyon régen Ez a szolgáltatás tovább lett fejlesztve, mielőtt kellett virtuális hálózati Szolgáltatásvégpontok bekapcsolása előtt a tűzfalon is Megvalósíthat egy élő virtuális hálózati szabályt. A végpontok kapcsolódik az Azure SQL Database egy megadott VNet-alhálózat. De most 2018 január, megkerülheti ezt a követelményt azzal a **IgnoreMissingVNetServiceEndpoint** jelzőt.

Csupán beállítása egy tűzfalszabályt nem segít a kiszolgáló védelmét. Meg kell is bekapcsolhatja virtuális hálózati Szolgáltatásvégpontok biztonsága érvénybe léptetéséhez. A Szolgáltatásvégpontok bekapcsolásakor, a virtuális hálózat alhálózatához teljesen állásidő, amíg be nem fejezi be-vagy kikapcsolása való áttérés, a. Ez különösen igaz nagy virtuális hálózatok kontextusában. Használhatja a **IgnoreMissingVNetServiceEndpoint** jelző csökkentése, vagy a hidegindítás okozta üzemkimaradást áttérés során.

Beállíthatja a **IgnoreMissingVNetServiceEndpoint** jelző PowerShell használatával. További információkért lásd: [egy virtuális hálózati szolgáltatásvégpont és a szabály létrehozása az Azure SQL Database PowerShell][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Hibák 40914 és 40615

Kapcsolódási hiba 40914 vonatkozik *virtuális hálózati szabályok*, a tűzfal ablaktáblán, az Azure Portalon a megadott módon. Hiba 40615 hasonlít, azzal a különbséggel vonatkozik *IP-cím szabályok* a tűzfalon.

### <a name="error-40914"></a>40914 hiba

*Üzenet szövege:* Nem nyitható meg a kiszolgáló "*[kiszolgálónév]*" a bejelentkezés által kért. Ügyfél számára nem engedélyezett a kiszolgálóhoz való hozzáféréshez.

*Hiba leírása:* Az ügyfél, amely rendelkezik a virtuális hálózat kiszolgálóvégpontok alhálózat szerepel. Azonban az Azure SQL Database-kiszolgáló nem virtuális hálózati szabályt, amely az alhálózat nem biztosít jogot az SQL-adatbázissal való kommunikációhoz.

*Hiba történt. megoldás:* A tűzfal panelen az Azure Portal használata a virtuális hálózati szabályok vezérlőelem [hozzáadása egy virtuális hálózati szabályt](#anchor-how-to-by-using-firewall-portal-59j) az alhálózat.

### <a name="error-40615"></a>40615 hiba

*Üzenet szövege:* Nem nyitható meg a kiszolgáló "{0}" a bejelentkezés által kért. Ügyfél IP-címmel rendelkező{1}"nem engedélyezett a kiszolgálóhoz való hozzáféréshez.

*Hiba leírása:* Az ügyfél IP-címek, amelyek nem jogosult az Azure SQL Database-kiszolgálóhoz való csatlakozáshoz csatlakozni próbál. A kiszolgáló tűzfalának nincs IP-cím szabály, amely lehetővé teszi, hogy egy ügyfél való kommunikációhoz megadott IP-címről az SQL Database rendelkezik.

*Hiba történt. megoldás:* Adja meg az ügyfél IP-címét egy IP-szabályt. Ezt úgy teheti meg a tűzfal panel az Azure Portalon.

Több SQL Database-hibaüzenetek listáját dokumentált [Itt][sql-database-develop-error-messages-419g].

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portálon hozhat létre egy virtuális hálózati szabály

Ez a szakasz bemutatja, hogyan használhatja a [az Azure portal] [ http-azure-portal-link-ref-477t] hozhat létre egy *virtuális hálózati szabályt* az Azure SQL Database-ben. A szabály arra utasítja a kommunikáció egy adott alhálózatról címkével ellátott, hogy elfogadja az SQL Database egy *virtuális hálózati szolgáltatásvégpont*.

> [!NOTE]
> Ha szeretne egy végpontot a virtuális hálózatok közötti tűzfalszabályok az Azure SQL Database-kiszolgáló hozzáadása, először győződjön meg arról, hogy az alhálózat be vannak kapcsolva a végpontok szolgáltatás.
>
> Ha a Szolgáltatásvégpontok nem az alhálózaton vannak kapcsolva, a portál kéri engedélyezheti őket. Kattintson a **engedélyezése** gomb, amelyre a szabály hozzáadása ugyanezen paneljén.

## <a name="powershell-alternative"></a>PowerShell alternatív

Egy PowerShell-parancsfájlt is létrehozhat a virtuális hálózati szabályok. A kulcsfontosságú parancsmag **új New-AzureRmSqlServerVirtualNetworkRule**. Ha szeretné megtudni, tekintse meg [egy virtuális hálózati szolgáltatásvégpont és a szabály létrehozása az Azure SQL Database PowerShell][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternatív REST API-val

Belsőleg az SQL virtuális hálózatok közötti műveleteket a PowerShell-parancsmagok hívja a REST API-k. Közvetlenül a REST API-k segítségével meghívhatja.

- [A virtuális hálózati szabályok: Műveletek][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Előfeltételek

Már rendelkeznie kell egy, az adott virtuális hálózati szolgáltatásvégpont címkéje alhálózatot *típusnév* releváns, az Azure SQL Database.

- A kapcsolódó végpont neve van **Microsoft.Sql**.
- Ha az alhálózat nem címkézhetők előfordulhat, hogy a típusnév, [ellenőrzéséhez az alhálózat egy végpont][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure-portál lépések

1. Jelentkezzen be az [Azure Portalra][http-azure-portal-link-ref-477t].

2. Keresse meg a portál **SQL Server-kiszolgálók** &gt; **tűzfal / virtuális hálózatok**.

3. Állítsa be a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** off vezérlő.

    > [!IMPORTANT]
    > Ha üresen hagyja a vezérlőt, állítsa be, az Azure SQL Database-kiszolgáló minden olyan alhálózat-kommunikációt fogad el. Hagyja a vezérlőt, állítsa be, előfordulhat, hogy egy biztonsági szempontból túlzott mértékű hozzáférést. A Microsoft Azure Virtual Network szolgáltatás végpont funkció az SQL Database, a virtuális hálózati szabály szolgáltatás együttes együtt csökkentheti a biztonsági támadási.

4. Kattintson a **+ Hozzáadás létező** szabályozhatja a a **virtuális hálózatok** szakaszban.

    ![Kattintson a Hozzáadás létező (alhálózat végpont, SQL szabály).][image-portal-firewall-vnet-add-existing-10-png]

5. Az új **Create/Update** panelen töltse ki az Azure-erőforrások nevei a vezérlőelemek.

    > [!TIP]
    > Meg kell adni a megfelelő **címelőtag** az alhálózathoz. Az érték a portálon találhatja meg.
    > Keresse meg **összes erőforrás** &gt; **minden** &gt; **virtuális hálózatok**. A szűrő jeleníti meg a virtuális hálózatok. Kattintson a virtuális hálózathoz, majd a **alhálózatok**. A **CÍMTARTOMÁNY** oszlopnak a címelőtag van szüksége.

    ![Töltse ki a mezőket az új szabályt.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Kattintson a **OK** gombra a panel alján.

7. A tűzfal panelen jelenik meg az eredményül kapott virtuális hálózati szabályt.

    ![Tekintse meg az új szabályt, a tűzfal panelen.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> A következő állapotok vagy állapotok a alkalmazni a szabályokat:
> - **Készen áll:** Azt jelzi, hogy sikeres volt-e, hogy kezdeményezte a műveletet.
> - **Nem sikerült:** Azt jelzi, hogy a művelet által kezdeményezett, nem sikerült.
> - **Törölve:** Csak a törlési művelet vonatkozik, és jelzi, hogy a szabály törölve lett, és már nem érvényes.
> - **InProgress:** Azt jelzi, hogy a művelet folyamatban van. A régi szabály érvényes, amíg a művelet ebben az állapotban van.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Az Azure virtuális hálózati Szolgáltatásvégpontok][vm-virtual-network-service-endpoints-overview-649d]
- [Az Azure SQL Database kiszolgálószintű és adatbázisszintű tűzfalszabályok][sql-db-firewall-rules-config-715d]

A virtuális hálózati szabály funkció az Azure SQL Database vált elérhetővé, a 2017 szeptember végéig.

## <a name="next-steps"></a>További lépések

- [Az Azure SQL Database virtuális hálózati szolgáltatásvégpont, majd egy virtuális hálózati szabály létrehozása a PowerShell használatával.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [A virtuális hálózati szabályok: Műveletek] [ rest-api-virtual-network-rules-operations-862r] REST API-kkal

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->

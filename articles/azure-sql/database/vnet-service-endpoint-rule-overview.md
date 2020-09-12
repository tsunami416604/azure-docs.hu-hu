---
title: VNet-végpontok és a Azure SQL Database adatbázisainak szabályai
description: Alhálózat megjelölése Virtual Network szolgáltatási végpontként. Ezt követően a végpontot virtuális hálózati szabályként adja meg az adatbázis-hozzáférés-vezérlési listához. Az adatbázis ezután fogadja a kommunikációt az alhálózaton lévő összes virtuális gépről és más csomópontról.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 827d66b51aa2080c4fb10209439d7781ddf787a7
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89436925"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Virtuális hálózati szolgáltatási végpontok és szabályok használata a Azure SQL Database-kiszolgálókon
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A *virtuális hálózati szabályok* egy tűzfal biztonsági funkciója, amely azt szabályozza, hogy az adatbázisok és a rugalmas készletek kiszolgálója [Azure SQL Database](sql-database-paas-overview.md) vagy az [Azure szinapszisban](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) lévő adatbázisaiban fogadja-e a virtuális hálózatok egyes alhálózatai által továbbított kommunikációt. Ez a cikk azt ismerteti, hogy miért érdemes a virtuális hálózati szabály funkciót időnként a Azure SQL Database és az Azure szinapszis Analytics (korábbi nevén SQL Data Warehouse) közötti kommunikáció biztonságos engedélyezésére.

> [!NOTE]
> Ez a cikk a Azure SQL Database és az Azure szinapszis Analytics szolgáltatásra is vonatkozik. Az egyszerűség kedvéért az "adatbázis" kifejezés a Azure SQL Database és az Azure szinapszis Analytics mindkét adatbázisára vonatkozik. Hasonlóképpen, a "Server"re mutató hivatkozások a Azure SQL Database és az Azure szinapszis Analytics szolgáltatást futtató [logikai SQL Serverre](logical-servers.md) hivatkoznak.

Virtuális hálózati szabály létrehozásához először egy [virtuális hálózati szolgáltatás végpontjának][vm-virtual-network-service-endpoints-overview-649d] kell lennie ahhoz, hogy a szabály hivatkozzon.

## <a name="how-to-create-a-virtual-network-rule"></a>Virtuális hálózati szabály létrehozása

Ha csak virtuális hálózati szabályt hoz létre, ugorjon a [cikk későbbi részében](#anchor-how-to-by-using-firewall-portal-59j)ismertetett lépésekre és magyarázatra.

## <a name="details-about-virtual-network-rules"></a>A virtuális hálózati szabályok részletei

Ez a szakasz a virtuális hálózati szabályokkal kapcsolatos néhány részletet ismerteti.

### <a name="only-one-geographic-region"></a>Csak egy földrajzi régió

Minden Virtual Network szolgáltatási végpont csak egy Azure-régióra vonatkozik. A végpont nem teszi lehetővé más régiók számára, hogy fogadják a kommunikációt az alhálózatból.

Bármely virtuális hálózati szabály arra a régióra korlátozódik, amelyre a mögöttes végpont vonatkozik.

### <a name="server-level-not-database-level"></a>Kiszolgáló szintű, nem adatbázis-szintű

Minden virtuális hálózati szabály a teljes kiszolgálóra vonatkozik, nem csupán egy adott adatbázisra a kiszolgálón. Más szóval a virtuális hálózati szabály a kiszolgáló szintjén, nem pedig az adatbázis szintjén érvényes.

- Ezzel szemben az IP-szabályok bármelyik szinten alkalmazhatók.

### <a name="security-administration-roles"></a>Biztonsági felügyeleti szerepkörök

A biztonsági szerepkörök elkülönítése Virtual Network szolgáltatási végpontok felügyelete alatt áll. A következő szerepkörök mindegyike esetében beavatkozásra van szükség:

- **Hálózati rendszergazda:** &nbsp; Kapcsolja be a végpontot.
- **Adatbázis-rendszergazda:** &nbsp; Frissítse a hozzáférés-vezérlési listát (ACL), hogy hozzáadja a megadott alhálózatot a kiszolgálóhoz.

*RBAC alternatíva:*

A hálózati rendszergazda és az adatbázis-rendszergazda szerepkörének több funkciója van, mint amennyi a virtuális hálózati szabályok kezeléséhez szükséges. A képességeinek csak egy részhalmazára van szükség.

Lehetősége van arra, hogy az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)][rbac-what-is-813s] használatával egyetlen egyéni szerepkört hozzon létre, amely csak a képességek megfelelő részhalmazával rendelkezik. Az egyéni szerepkör felhasználható a hálózati rendszergazda vagy az adatbázis-rendszergazda bevonása helyett. Ha egyéni szerepkörhöz ad hozzá felhasználót, és a másik két fő rendszergazdai szerepkörhöz hozzáadja a felhasználót, a biztonsági expozíció felülete alacsonyabb lesz.

> [!NOTE]
> Bizonyos esetekben a Azure SQL Database és a VNet-alhálózat adatbázisa különböző előfizetésekben található. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
>
> - Mindkét előfizetésnek ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia.
> - A felhasználó rendelkezik a szükséges engedélyekkel a műveletek elindításához, például a szolgáltatási végpontok engedélyezéséhez és egy VNet-alhálózat hozzáadásához az adott kiszolgálóhoz.
> - Mindkét előfizetéshez regisztrálni kell a Microsoft. SQL-szolgáltatót.

## <a name="limitations"></a>Korlátozások

Azure SQL Database esetében a virtuális hálózati szabályok funkció a következő korlátozásokkal rendelkezik:

- A Azure SQL Databaseban található adatbázis tűzfalában minden egyes virtuális hálózati szabály egy alhálózatra hivatkozik. Az összes hivatkozott alhálózatnak ugyanabban a földrajzi régióban kell futnia, amely az adatbázist üzemelteti.

- Minden kiszolgáló legfeljebb 128 ACL-bejegyzést tartalmazhat bármely adott virtuális hálózathoz.

- A virtuális hálózati szabályok csak Azure Resource Manager virtuális hálózatokra érvényesek; és nem a [klasszikus üzembe helyezési modell][arm-deployment-model-568f] hálózatait.

- A virtuális hálózati szolgáltatási végpontok bekapcsolásával Azure SQL Database a MySQL és a PostgreSQL Azure-szolgáltatások végpontját is engedélyezi. A-végpontok esetében azonban sikertelen lehet a végpontokról a MySQL-vagy PostgreSQL-példányokhoz való kapcsolódás.
  - A mögöttes ok az, hogy a MySQL és a PostgreSQL valószínűleg nincs konfigurálva virtuális hálózati szabály. Konfigurálnia kell egy virtuális hálózati szabályt a Azure Database for MySQL és a PostgreSQL-hez, és a kapcsolatok sikeresek lesznek.

- A tűzfalon az IP-címtartományok a következő hálózati elemekre vonatkoznak, a virtuális hálózati szabályok azonban nem:
  - [Helyek közötti (S2S) virtuális magánhálózat (VPN)][vpn-gateway-indexmd-608y]
  - Helyszíni [ExpressRoute][expressroute-indexmd-744v] -on keresztül

### <a name="considerations-when-using-service-endpoints"></a>A szolgáltatási végpontok használatának szempontjai

Ha Azure SQL Database szolgáltatási végpontokat használ, tekintse át a következő szempontokat:

- **Azure SQL Database nyilvános IP-címekre való kimenő forgalom szükséges**: hálózati biztonsági csoportokat (NSG) kell megnyitni ahhoz, hogy a kapcsolat lehetővé váljon a Azure SQL Database IP-címek számára. Ezt a Azure SQL Database NSG- [szolgáltatásának címkéi](../../virtual-network/security-overview.md#service-tags) segítségével teheti meg.

### <a name="expressroute"></a>ExpressRoute

Ha a [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -t használja a telephelyéről, a nyilvános és a Microsoft-partnerek számára, meg kell határoznia a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. Microsoft-társviszony-létesítés esetén a használt NAT IP-cím(ek)et vagy az ügyfél vagy a szolgáltató adja meg. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).
  
Ha engedélyezni szeretné az áramkörről a Azure SQL Database felé irányuló kommunikációt, létre kell hoznia az IP-hálózati szabályokat a NAT nyilvános IP-címeihez.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Az VNet szolgáltatásbeli végpontok használatának következményei az Azure Storage-ban

Az Azure Storage ugyanazt a funkciót implementálta, amellyel korlátozhatja az Azure Storage-fiók kapcsolatait. Ha egy olyan Azure Storage-fiókkal próbálja használni ezt a funkciót, amelyet az Azure SQL Database használ, problémákba ütközhet. A következő lista a Azure SQL Database és az Azure szinapszis Analytics azon funkcióit sorolja fel és tárgyalja, amelyekre hatással van.

### <a name="azure-synapse-polybase-and-copy-statement"></a>Azure szinapszis-alapanyag és-MÁSOLÁSi utasítás

A Base és a COPY utasítást általában arra használják, hogy az Azure szinapszis-elemzéseket az Azure Storage-fiókokba töltse be a nagy átviteli sebességű adatok betöltéséhez. Ha az Azure Storage-fiók, amelyről az adatok betöltése szükséges, csak VNet-alhálózatokhoz fér hozzá, akkor a rendszer a Base és a COPY utasítást is megszakítja. Az importálási és exportálási forgatókönyvek az Azure VNet-mel védett Azure Storage-hoz való MÁSOLÁSával és létrehozásával való használatának engedélyezéséhez kövesse az alábbi lépéseket:

#### <a name="prerequisites"></a>Előfeltételek

- Telepítse az Azure PowerShellt a kapcsolódó [útmutató](https://docs.microsoft.com/powershell/azure/install-az-ps) alapján.
- Ha általános célú v1 vagy Blob Storage-fiókja van, először frissítenie kell az általános célú v2 fiókra az [itt található útmutatások](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) szerint.
- Engedélyeznie kell, **hogy a megbízható Microsoft-szolgáltatások hozzáférjenek ehhez a Storage-fiókhoz** az Azure Storage **-fiók tűzfala és a virtuális hálózatok** beállítások menüjében. Ennek a konfigurációnak a engedélyezése lehetővé teszi a Base és a COPY utasítás számára, hogy erős hitelesítéssel kapcsolódjon a Storage-fiókhoz, ahol a hálózati forgalom az Azure-gerincen marad. További információt ebben az [útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) talál.

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Lépések

1. A PowerShellben **regisztrálja az** Azure szinapszist üzemeltető kiszolgálót Azure Active Directory (HRE) használatával:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Hozzon létre egy **általános célú v2 Storage-fiókot** az [útmutató](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)segítségével.

   > [!NOTE]
   >
   > - Ha rendelkezik általános célú v1-vagy blob Storage-fiókkal, először a **v2-re kell frissítenie** az [útmutató](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)segítségével.
   > - Azure Data Lake Storage Gen2 kapcsolatos ismert problémák esetén tekintse meg ezt az [útmutatót](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).

1. A Storage-fiók területen navigáljon a **Access Control (iam)** elemre, és válassza a **szerepkör-hozzárendelés hozzáadása**elemet. Rendelje hozzá a **Storage blob-adatközreműködő** Azure-szerepkört az Azure szinapszis Analytics szolgáltatást futtató kiszolgálóhoz, amelyet a következő #1 lépésben regisztrált: Azure Active Directory (HRE).

   > [!NOTE]
   > Ezt a lépést csak a Storage-fiók tulajdonosi jogosultsággal rendelkező tagjai hajthatják végre. A különböző Azure-beli beépített szerepkörökhöz tekintse meg ezt az [útmutatót](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Alapszintű kapcsolat az Azure Storage-fiókkal:**

   1. Hozzon létre egy adatbázis- **[főkulcsot](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** , ha még nem hozott létre egy korábbiat:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Adatbázis-hatókörű hitelesítő adat létrehozása az **Identity = ' Managed Service Identity '** használatával:

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Nincs szükség a titkos kulcs megadására az Azure Storage-hozzáférési kulccsal, mert ez a mechanizmus [felügyelt identitást](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) használ a borítók alatt.
       > - Az identitás nevének **"Managed Service Identity"** kell lennie a VNet által védett Azure Storage-fiókkal való együttműködéshez.

   1. Hozzon létre külső adatforrást `abfss://` sémával az általános célú v2 Storage-fiókhoz való csatlakozáshoz a következő paranccsal:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Ha már rendelkezik az általános célú v1-vagy blob Storage-fiókhoz társított külső táblákkal, először el kell dobnia ezeket a külső táblákat, majd el kell dobnia a megfelelő külső adatforrást. Ezután hozzon létre egy külső adatforrást az `abfss://` általános célú v2-es Storage-fiókhoz csatlakozó sémával a fentiek szerint, és hozza létre újra az összes külső táblázatot az új külső adatforrás használatával. Használhatja a [Parancsfájlok létrehozása és közzététele varázslót](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) a Create-scriptek létrehozásához az összes külső táblázat számára a könnyebb kezelhetőség érdekében.
       > - A sémával kapcsolatos további információkért `abfss://` tekintse meg ezt az [útmutatót](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - A külső ADATFORRÁS LÉTREHOZÁSával kapcsolatos további információkért tekintse meg ezt az [útmutatót](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Lekérdezés normál módon [külső táblák](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)használatával.

### <a name="azure-sql-database-blob-auditing"></a>BLOB-naplózás Azure SQL Database

A blob-naplózás leküldi a naplókat a saját Storage-fiókjába. Ha ez a Storage-fiók a VNet szolgáltatás-végpontok funkciót használja, akkor a Azure SQL Database és a Storage-fiók közötti kapcsolat megszakad.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNet-tűzfalszabály hozzáadása a kiszolgálóhoz a VNet szolgáltatás végpontjai bekapcsolása nélkül

Régen a funkció továbbfejlesztése előtt be kellett kapcsolni a VNet szolgáltatás-végpontokat, mielőtt megvalósítani lehetne az élő VNet-szabályt a tűzfalon. Az adott VNet kapcsolódó végpontok egy Azure SQL Database adatbázisához tartoznak. Most azonban január 2018-én megkerülheti ezt a követelményt a **IgnoreMissingVNetServiceEndpoint** jelző beállításával.

Egy tűzfalszabály beállítása nem segít a kiszolgáló biztonságossá tételében. A VNet szolgáltatás-végpontokat is be kell kapcsolni a biztonság érvénybe léptetéséhez. Ha bekapcsolja a szolgáltatási végpontokat, a VNet-alhálózat az állásidőt, amíg be nem fejeződik a kikapcsolás és a közötti átmenet. Ez különösen igaz a nagyméretű virtuális hálózatok kontextusában. A **IgnoreMissingVNetServiceEndpoint** jelzővel csökkentheti vagy törölheti az állásidőt az áttérés során.

A **IgnoreMissingVNetServiceEndpoint** jelzőt a PowerShell használatával állíthatja be. Részletekért lásd: [PowerShell Virtual Network szolgáltatási végpont és szabály létrehozása Azure SQL Databasehoz][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>40914 és 40615 hibák

A 40914-es kapcsolati hiba a Azure Portal tűzfal ablaktábláján megadott *virtuális hálózati szabályokra*vonatkozik. A 40615-es hiba hasonló, azzal a különbséggel, hogy a tűzfal *IP-címeire vonatkozó szabályokra* vonatkozik.

### <a name="error-40914"></a>40914-es hiba

*Üzenet szövege:* Nem nyitható meg a bejelentkezés által kért "*[Server-Name]*" kiszolgáló. Az ügyfél nem jogosult a kiszolgálóhoz való hozzáférésre.

*Hiba leírása:* Az ügyfél olyan alhálózatban található, amely virtuális hálózati kiszolgáló-végpontokkal rendelkezik. A kiszolgálónak azonban nincs olyan virtuális hálózati szabálya, amely engedélyezi az alhálózatnak az adatbázissal való kommunikációt.

*Hiba feloldása:* A Azure Portal tűzfal paneljén a virtuális hálózati szabályok vezérlőelem használatával [adjon hozzá egy virtuális hálózati szabályt](#anchor-how-to-by-using-firewall-portal-59j) az alhálózathoz.

### <a name="error-40615"></a>40615-es hiba

*Üzenet szövege:* Nem nyitható meg {0} a bejelentkezés által kért "" kiszolgáló. A (z) "" IP-címmel rendelkező ügyfél {1} nem jogosult a kiszolgálóhoz való hozzáférésre.

*Hiba leírása:* Az ügyfél olyan IP-címről próbál csatlakozni, amely nem rendelkezik jogosultsággal a kiszolgálóhoz való csatlakozásra. A kiszolgáló tűzfalának nincs olyan IP-cím szabálya, amely lehetővé teszi, hogy az ügyfél a megadott IP-címről kommunikáljon az adatbázissal.

*Hiba feloldása:* Adja meg az ügyfél IP-címét IP-szabályként. Ezt az Azure Portal Tűzfal panelén végezheti el.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="portal-can-create-a-virtual-network-rule"></a>A portál létrehozhat egy virtuális hálózati szabályt

Ez a szakasz azt szemlélteti, hogyan használható a [Azure Portal][http-azure-portal-link-ref-477t] egy *virtuális hálózati szabály* létrehozásához az adatbázisban a Azure SQL Databaseban. A szabály közli az adatbázissal, hogy fogadja a kommunikációt egy adott alhálózatból, amely *Virtual Network szolgáltatási végpontként*van megjelölve.

> [!NOTE]
> Ha szolgáltatási végpontot szeretne hozzáadni a kiszolgáló VNet, először győződjön meg arról, hogy a szolgáltatási végpontok be vannak kapcsolva az alhálózathoz.
>
> Ha a szolgáltatási végpontok nincsenek bekapcsolva az alhálózathoz, a portál felszólítja, hogy engedélyezze őket. Kattintson az **Engedélyezés** gombra ugyanazon a panelen, amelyen a szabályt hozzáadja.

## <a name="powershell-alternative"></a>PowerShell-alternatíva

A parancsfájlok virtuális hálózati szabályokat is létrehozhatnak a **New-AzSqlServerVirtualNetworkRule PowerShell-** parancsmag vagy [az az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal. Ha érdeklik, tekintse meg [a PowerShellt, és hozzon létre egy Virtual Network szolgáltatási végpontot és szabályt a Azure SQL Databasehoz][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternatív megoldás

Belsőleg a PowerShell-parancsmagok az SQL VNet műveleteihez REST API-kat hívnak. A REST API-kat közvetlenül is meghívhatja.

- [Virtual Network szabályok: műveletek][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Előfeltételek

Már rendelkeznie kell egy olyan alhálózattal, amely az adott Virtual Network szolgáltatás végpontjának Azure SQL Databasehoz tartozó *nevét adja* meg.

- A megfelelő végpont-típus neve a **Microsoft. SQL**.
- Ha az alhálózat nem címkézhető a típus nevével, tekintse [meg az alhálózat ellenőrzése végpontot][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal lépések

1. Jelentkezzen be az [Azure Portalra][http-azure-portal-link-ref-477t].

2. Keresse meg és válassza ki az **SQL-kiszolgálók**elemet, majd válassza ki a kiszolgálót. A **Biztonság**területen válassza a **tűzfalak és virtuális hálózatok**lehetőséget.

3. Állítsa be az **Azure-szolgáltatások hozzáférés engedélyezése** a kikapcsoláshoz lehetőséget.

    > [!IMPORTANT]
    > Ha a vezérlőt a be értékre állítja, akkor a kiszolgáló minden olyan IP-címről érkező kommunikációt fogad, amely az Azure-adatközpontok számára meghatározott tartományon belül ismert. Ha a vezérlőt a be értékre állítja, előfordulhat, hogy a biztonsági szempontból túlságosan nagy a hozzáférés. A Microsoft Azure Virtual Network szolgáltatási végpont funkciója, amely a SQL Database virtuális hálózati szabály funkciójával együttműködve csökkenti a biztonsági felületét.

4. Kattintson a **+ meglévő vezérlő hozzáadása** lehetőségre a **Virtual Networks (virtuális hálózatok** ) szakaszban.

    ![Kattintson a meglévő hozzáadása (alhálózat végpontja SQL-szabályként) elemre.][image-portal-firewall-vnet-add-existing-10-png]

5. Az új **Létrehozás/frissítés** panelen töltse ki a vezérlőket az Azure-erőforrások neveivel.

    > [!TIP]
    > Meg kell adnia az alhálózat helyes **címének előtagját** . Az értéket a portálon találja.
    > Navigáljon az összes **All resources** &gt; **All types** &gt; **virtuális hálózat**típusú erőforráshoz. A szűrő megjeleníti a virtuális hálózatokat. Kattintson a virtuális hálózatra, majd az **alhálózatok**elemre. A **címtartomány** oszlopban a szükséges előtag szerepel.

    ![Adja meg a mezőket az új szabályhoz.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Kattintson a panel alján található **OK** gombra.

7. Tekintse meg az eredményül kapott virtuális hálózati szabályt a tűzfal ablaktáblán.

    ![Tekintse meg az új szabályt a tűzfal ablaktáblán.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> A következő állapotok vagy állapotok érvényesek a szabályokra:
>
> - **Kész:** Azt jelzi, hogy az elindított művelet sikeresen befejeződött.
> - **Sikertelen:** Azt jelzi, hogy az Ön által kezdeményezett művelet meghiúsult.
> - **Törölve:** Csak a törlési műveletre vonatkozik, és azt jelzi, hogy a szabály törölve lett, és már nem érvényes.
> - **Inprogress:** Azt jelzi, hogy a művelet folyamatban van. A régi szabály akkor érvényes, ha a művelet ebben az állapotban van.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Azure Virtual Network szolgáltatásbeli végpontok][vm-virtual-network-service-endpoints-overview-649d]
- [Kiszolgálói szintű és adatbázis-szintű tűzfalszabályok][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Következő lépések

- [A PowerShell használatával hozzon létre egy virtuális hálózati szolgáltatási végpontot, majd egy virtuális hálózati szabályt a Azure SQL Databasehoz.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtual Network szabályok:][rest-api-virtual-network-rules-operations-862r] a REST API-kkal végzett műveletek

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->

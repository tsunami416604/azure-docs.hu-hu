---
title: Virtuális hálózat végpontjai és szabályok egy- és készletezésű adatbázisokhoz
description: Alhálózat megjelölése virtuális hálózati szolgáltatásvégpontként. Ezután a végpont, mint egy virtuális hálózati szabály az Azure SQL-adatbázis ACL. Az SQL Database ezután elfogadja az alhálózat összes virtuális gépéről és más csomópontjáról érkező kommunikációt.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 7032f9e8f57ea9400bf6a92f89b13fa1866f8fc1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414400"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Virtuális hálózati szolgáltatás végpontjainak és szabályainak használata adatbázis-kiszolgálókhoz

*A virtuális hálózati szabályok* egy tűzfalbiztonsági szolgáltatás, amely azt szabályozza, hogy az Azure [SQL Database-ben](sql-database-technical-overview.md) vagy az SQL [Data Warehouse-ban](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) lévő rugalmas készlet adatbázis-kiszolgálója elfogadja-e a virtuális hálózatok bizonyos alhálózataiból küldött kommunikációt. Ez a cikk ismerteti, hogy miért a virtuális hálózati szabály funkció néha a legjobb megoldás az Azure SQL Database és az SQL Data Warehouse közötti kommunikáció biztonságos engedélyezéséhez.

> [!IMPORTANT]
> Ez a cikk az Azure SQL-kiszolgálóra, valamint az Azure SQL-kiszolgálón létrehozott SQL Database és SQL Data Warehouse adatbázisokra is vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database. Ez a cikk *nem* vonatkozik az Azure SQL Database **felügyelt példányüzembe** helyezésére, mert nem rendelkezik szolgáltatás-végpontdal.

Virtuális hálózati szabály létrehozásához először rendelkeznie kell egy [virtuális hálózati szolgáltatás végpontjával][vm-virtual-network-service-endpoints-overview-649d] a szabályhoz.

## <a name="how-to-create-a-virtual-network-rule"></a>Virtuális hálózati szabály létrehozása

Ha csak virtuális hálózati szabályt hoz létre, a [cikk későbbi részében](#anchor-how-to-by-using-firewall-portal-59j)átugorhatja a lépéseket és a magyarázatot.

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>A virtuális hálózati szabályok részletei

Ez a szakasz a virtuális hálózati szabályokkal kapcsolatos számos részletet ismerteti.

### <a name="only-one-geographic-region"></a>Csak egy földrajzi régió

Minden virtuális hálózati szolgáltatás végpont csak egy Azure-régióban vonatkozik. A végpont nem teszi lehetővé, hogy más régiók fogadják az alhálózatkommunikációt.

Minden virtuális hálózati szabály arra a régióra korlátozódik, amelyaz alapul szolgáló végpontja vonatkozik.

### <a name="server-level-not-database-level"></a>Kiszolgálószintű, nem adatbázisszintű

Minden virtuális hálózati szabály a teljes Azure SQL Database-kiszolgálóra vonatkozik, nem csak a kiszolgálón lévő egyetlen adatbázisra. Más szóval a virtuális hálózati szabály a kiszolgáló szintjén érvényes, nem pedig az adatbázis szintjén.

- Ezzel szemben az IP-szabályok mindkét szinten alkalmazhatók.

### <a name="security-administration-roles"></a>Biztonsági felügyeleti szerepkörök

A virtuális hálózat szolgáltatás végpontjainak felügyeletében a biztonsági szerepkörök elkülönülnek. A következő szerepkörök mindegyikéből szükség van műveletre:

- **Hálózati rendszergazda:** &nbsp; Kapcsolja be a végpontot.
- **Adatbázis-felügyelet:** &nbsp; Frissítse a hozzáférés-vezérlési listát (ACL) a megadott alhálózat SQL adatbázis-kiszolgálóhoz való hozzáadásához.

*RBAC alternatíva:*

A hálózati rendszergazda és az adatbázis-rendszergazda szerepkörei több képességgel rendelkeznek, mint amennyi a virtuális hálózati szabályok kezeléséhez szükséges. Képességeiknek csak egy részhalmazára van szükség.

Lehetősége van [szerepköralapú hozzáférés-vezérlés (RBAC)][rbac-what-is-813s] használatával az Azure-ban egyetlen egyéni szerepkör, amely csak a szükséges részhalmaza képességeit. Az egyéni szerepkör a hálózati rendszergazda vagy az adatbázis-rendszergazda helyett használható. A biztonsági expozíció felülete alacsonyabb, ha egy felhasználót hozzáad egy egyéni szerepkörhöz, szemben a felhasználó másik két fő rendszergazdai szerepkörrel való hozzáadásával.

> [!NOTE]
> Bizonyos esetekben az Azure SQL Database és a virtuális hálózat-alhálózat különböző előfizetésekben vannak. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
>
> - Mindkét előfizetésnek ugyanabban az Azure Active Directory-bérlőben kell lennie.
> - A felhasználó rendelkezik a műveletek kezdeményezéséhez szükséges engedélyekkel, például a szolgáltatásvégpontok engedélyezéséhez és a virtuális hálózat adott kiszolgálóhoz való hozzáadásához szükséges engedélyekkel.
> - Mindkét előfizetéshez regisztrálni kell a Microsoft.Sql szolgáltatót.

## <a name="limitations"></a>Korlátozások

Az Azure SQL Database esetében a virtuális hálózati szabályok szolgáltatás a következő korlátozásokkal rendelkezik:

- Az SQL-adatbázis tűzfalán minden virtuális hálózati szabály egy alhálózatra hivatkozik. Mindezek a hivatkozott alhálózatok at ugyanabban a földrajzi régióban kell üzemeltetni, amely az SQL-adatbázist üzemelteti.

- Minden Egyes Azure SQL Database-kiszolgáló legfeljebb 128 ACL-bejegyzéssel rendelkezhet egy adott virtuális hálózathoz.

- A virtuális hálózati szabályok csak az Azure Resource Manager virtuális hálózataira vonatkoznak; és nem a [klasszikus üzembe helyezési modell][arm-deployment-model-568f] hálózatok.

- A virtuális hálózati szolgáltatás végpontjainak bekapcsolása az Azure SQL Database-re a MySQL és a PostgreSQL Azure-szolgáltatások végpontjait is lehetővé teszi. Azonban a végpontok ON, kísérletek csatlakozni a végpontok a MySQL vagy A PostgreSQL példányok sikertelen lehet.
  - Ennek az az oka, hogy a MySQL és a PostgreSQL valószínűleg nincs konfigurálva virtuális hálózati szabály. Be kell állítania egy virtuális hálózati szabályt az Azure Database for MySQL és a PostgreSQL számára, és a kapcsolat sikeres lesz.

- A tűzfalon az IP-címtartományok a következő hálózati elemekre vonatkoznak, de a virtuális hálózati szabályok nem:
  - [Helyek közötti (S2S) virtuális magánhálózat (VPN)][vpn-gateway-indexmd-608y]
  - Helyszíni műveletek az [ExpressRoute-on][expressroute-indexmd-744v] keresztül

### <a name="considerations-when-using-service-endpoints"></a>A szolgáltatásvégpontok használata során figyelembe vett szempontok

Az Azure SQL Database szolgáltatásvégpontjainak használatakor tekintse át az alábbi szempontokat:

- **Kimenő Azure SQL Database nyilvános IP-k :** Hálózati biztonsági csoportok (NSG-k) meg kell nyitni az Azure SQL Database IP-k a kapcsolat engedélyezéséhez. Ezt az Azure SQL Database NSG [szolgáltatáscímkék](../virtual-network/security-overview.md#service-tags) használatával teheti meg.

### <a name="expressroute"></a>ExpressRoute

Ha az [ExpressRoute-ot](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a helyiségeiből használja, nyilvános társviszony-létesítéshez vagy Microsoft-társviszony-létesítéshez, azonosítania kell a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. Microsoft-társviszony-létesítés esetén a használt NAT IP-cím(ek)et vagy az ügyfél vagy a szolgáltató adja meg. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).
  
Ahhoz, hogy lehetővé tegye a kapcsolatot az Azure SQL Database-kapcsolattal, létre kell hoznia az IP-hálózati szabályokat a nat nyilvános IP-címeihez.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>A VNet-szolgáltatásvégpontok azure-tárolóval való használatának hatása

Az Azure Storage ugyanazt a funkciót valósította meg, amely lehetővé teszi az Azure Storage-fiók hoz való kapcsolódás korlátozását. Ha úgy dönt, hogy ezt a funkciót egy Azure Storage-fiókkal használja, amelyet az Azure SQL Server használ, problémákba ütközhet. Következő egy listát, és a vita az Azure SQL Database és az Azure SQL Data Warehouse funkciók, amelyek hatással vannak ez.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse PolyBase

A PolyBase gyakran használható adatok azure Storage-fiókokból az Azure SQL Data Warehouse-ba történő betöltésére. Ha az Azure Storage-fiók, amely az adatok betöltése korlátozza a hozzáférést csak egy sor virtuális hálózat alhálózatok, a PolyBase és a fiók közötti kapcsolat megszakad. Ha engedélyezni szeretné a PolyBase importálási és exportálási forgatókönyveit az Azure SQL Data Warehouse virtuális hálózathoz való csatlakozásával, kövesse az alábbi lépéseket:

#### <a name="prerequisites"></a>Előfeltételek

- Telepítse az Azure PowerShellt ezzel az [útmutatóval.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Ha általános célú v1- vagy blobtár-fiókkal rendelkezik, először frissítenie kell az általános célú v2-re ezzel az [útmutatóval.](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)
- Az Azure Storage-fiók **tűzfalai és a virtuális hálózatok** beállításai menüben be van kapcsolva a megbízható **Microsoft-szolgáltatások hozzáférésének engedélyezése a tárfiókhoz.** További információt ebben az [útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) talál.

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

#### <a name="steps"></a>Lépések

1. A **PowerShellben regisztrálja az Azure SQL** Data Warehouse-példányt üzemeltető Azure SQL Server-példányt az Azure Active Directoryval (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Általános **célú v2 tárfiók** létrehozása ezzel az [útmutatóval.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

   > [!NOTE]
   > - Ha általános célú v1- vagy blobtár-fiókkal rendelkezik, **először frissítenie** kell a v2-re ezzel az [útmutatóval.](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)
   > - Az Azure Data Lake Storage Gen2 használatával kapcsolatos ismert problémákat ebben az [útmutatóban](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues)talál.

1. A tárfiók alatt keresse meg a **Hozzáférés-vezérlés (IAM)**, és kattintson **a Szerepkör-hozzárendelés hozzáadása gombra.** **Rendeljen Storage Blob Data Contributor** RBAC szerepkört az Azure SQL Server üzemelteti az Azure Active Data Warehouse, amely regisztrált az Azure Active Directory (AAD) az 1.

   > [!NOTE]
   > Ezt a lépést csak tulajdonosi jogosultsággal rendelkező tagok hajthatják végre. Az Azure-erőforrások különböző beépített szerepköreiről ezt az [útmutatót](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)ismerteti.
  
1. **Polibázis-kapcsolat az Azure Storage-fiókkal:**

   1. Hozzon létre **[adatbázis-főkulcsot,](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** ha még nem hozott létre korábban:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Adatbázis-hatókörrel rendelkező hitelesítő adatok létrehozása **IDENTITY = "Felügyelt szolgáltatásidentitás" használatával:**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       > - Nincs szükség secret megadása az Azure Storage hozzáférési kulcs, mert ez a mechanizmus [felügyelt identitást](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) használ a borítók alatt.
       > - Identity név kell **"felügyelt szolgáltatás identitása"** a PolyBase-kapcsolat, hogy működjön együtt az Azure Storage-fiók virtuális hálózatra védett.

   1. Hozzon létre `abfss://` külső adatforrást az általános célú v2 tárfiókhoz való csatlakozásra szolgáló sémával a PolyBase használatával:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       > - Ha már rendelkezik külső táblák kal az általános célú v1-es vagy blob storage-fiók, először dobja ezeket a külső táblákat, majd dobja a megfelelő külső adatforrás. Ezután hozzon `abfss://` létre külső adatforrást az általános célú v2 tárfiókhoz kapcsolódó sémával, a fentiek szerint, és hozza létre újra az összes külső táblát ezzel az új külső adatforrással. A [Parancsfájlok létrehozása és közzététele varázsló](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) segítségével az összes külső táblához létrehozhat parancsfájlokat a könnyebb ikon érdekében.
       > - A sémáról `abfss://` további információt ebben az [útmutatóban](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri)talál.
       > - A KÜLSŐ ADATFORRÁS LÉTREHOZÁSÁRÓL további információt ebben az [útmutatóban](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)talál.

   1. Lekérdezés a szokásos módon [külső táblák](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)használatával .

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL adatbázisblob-naplózás

Blob naplózási leküldések naplózási naplók at a saját tárfiókba. Ha ez a tárfiók a VNet-szolgáltatás végpontok szolgáltatást használja, majd az Azure SQL Database és a tárfiók közötti kapcsolat megszakad.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Virtuálishálózati tűzfalszabály hozzáadása a kiszolgálóhoz a Virtuálishálózati szolgáltatás végpontjainak bekapcsolása nélkül

Réges-régen, mielőtt ez a szolgáltatás javult, be kellett kapcsolnia a virtuális hálózat végpontjait, mielőtt egy élő virtuális hálózat szabályt valósíthatott volna meg a tűzfalon. A végpontok egy adott virtuális hálózat-alhálózatot egy Azure SQL-adatbázishoz kapcsoltak. De most, mint a január 2018, megkerülheti ezt a követelményt az **IgnoreMissingVNetServiceEndpoint** jelző beállításával.

A tűzfalszabály pusztán beállítása nem segít a kiszolgáló védelmében. A virtuális hálózat szolgáltatásvégpontjait is be kell kapcsolnia ahhoz, hogy a biztonság érvénybe lépjen. Amikor bekapcsolja a szolgáltatásvégpontokat, a virtuális hálózat alhálózata állásidőt tapasztal, amíg be nem fejezi a Ki és be állás között való átmenetet. Ez különösen igaz a nagy virtuális hálózatok összefüggésében. Az **IgnoreMissingVNetServiceEndpoint** jelzővel csökkentheti vagy megszüntetheti az állásidőt az átmenet során.

Az **IgnoreMissingVNetServiceEndpoint** jelző a PowerShell használatával állítható be. További információt a PowerShell virtuális [hálózati szolgáltatásvégpont és szabály létrehozása az Azure SQL Database számára című][sql-db-vnet-service-endpoint-rule-powershell-md-52d]témakörben talál.

## <a name="errors-40914-and-40615"></a>40914-es és 40615-ös hibák

A 40914-es csatlakozási hiba *a virtuális hálózati szabályokra*vonatkozik, az Azure Portal tűzfal ablaktábláján megadottak szerint. A 40615-ös hiba hasonló, kivéve, hogy a tűzfal *IP-címszabályaira* vonatkozik.

### <a name="error-40914"></a>40914-es hiba

*Üzenet szövege:* A bejelentkezés által kért kiszolgáló '*[kiszolgálónév]*' nem nyitható meg. Az ügyfél nem férhet hozzá a kiszolgálóhoz.

*Hiba leírása:* Az ügyfél olyan alhálózatban van, amely virtuális hálózati kiszolgáló végpontokkal rendelkezik. De az Azure SQL-adatbáziskiszolgáló nem rendelkezik olyan virtuális hálózati szabállyal, amely jogosultságot ad az alhálózatnak az SQL-adatbázissal való kommunikációra.

*Hibajavítás:* Az Azure Portal tűzfal ablaktábláján használja a virtuális hálózati szabályok vezérlőt [egy virtuális hálózati szabály hozzáadásához](#anchor-how-to-by-using-firewall-portal-59j) az alhálózathoz.

### <a name="error-40615"></a>40615-ös hiba

*Üzenet szövege:* A bejelentkezés{0}által kért kiszolgáló ' ' nem nyitható meg. Az IP-címmel{1}rendelkező ' ' ügyfél nem férhet hozzá a kiszolgálóhoz.

*Hiba leírása:* Az ügyfél olyan IP-címről próbál csatlakozni, amely nem jogosult az Azure SQL Database-kiszolgálóhoz való csatlakozásra. A kiszolgáló tűzfalán nincs olyan IP-cím-szabály, amely engedélyezné, hogy az adott IP-címről ügyfelek kommunikáljanak az SQL Database-zel.

*Hibajavítás:* Adja meg az ügyfél IP-címét IP-szabályként. Ezt az Azure Portal Tűzfal panelén végezheti el.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>A portál virtuális hálózati szabályt hozhat létre

Ez a szakasz bemutatja, hogyan használhatja az [Azure Portalon][http-azure-portal-link-ref-477t] egy *virtuális hálózati szabály* létrehozásához az Azure SQL-adatbázisban. A szabály arra utasítja az SQL-adatbázist, hogy fogadja el a virtuális hálózat végpontjaként címkézett adott alhálózat *kommunikációját.*

> [!NOTE]
> Ha egy szolgáltatásvégpontot kíván hozzáadni az Azure SQL Database-kiszolgáló virtuális hálózati tűzfalszabályaihoz, először győződjön meg arról, hogy a szolgáltatásvégpontok be vannak kapcsolva az alhálózathoz.
>
> Ha a szolgáltatás végpontjai nincsenek bekapcsolva az alhálózathoz, a portál megkéri, hogy engedélyezze őket. Kattintson az **Engedélyezés** gombra ugyanazon a panelen, amelyhez a szabályt hozzáadja.

## <a name="powershell-alternative"></a>PowerShell-alternatíva

A parancsfájlok virtuális hálózati szabályokat is létrehozhatnak a **New-AzSqlServerVirtualNetworkRule** vagy [az az hálózati vnet létrehozása segítségével.](/cli/azure/network/vnet#az-network-vnet-create) Ha érdekel, a [PowerShell a virtuális hálózati szolgáltatás végpontjának és szabályának létrehozásáról az Azure SQL Database-hez című témakörben található.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]

## <a name="rest-api-alternative"></a>REST API alternatív

Belsőleg a PowerShell-parancsmagok SQL virtuális hálózat műveletek meghívja a REST API-kat. A REST API-kat közvetlenül is meghívhatja.

- [Virtuális hálózati szabályok: Műveletek][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Előfeltételek

Már rendelkeznie kell egy alhálózattal, amely az Azure SQL Database-hez tartozó virtuális hálózat *szolgáltatásvégpont-típusnevével* van címkézve.

- A megfelelő végponttípus neve **Microsoft.Sql**.
- Ha az alhálózat nincs megjelölve a típusnévvel, [olvassa el A segédhálózat végpontjának ellenőrzése][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]című témakört.

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Az Azure Portal lépései

1. Jelentkezzen be az [Azure Portalra][http-azure-portal-link-ref-477t].

2. Keresse meg és jelölje ki **az SQL-kiszolgálókat,** majd jelölje ki a kiszolgálót. A **Biztonság**csoportban válassza a **Tűzfalak és a virtuális hálózatok**lehetőséget.

3. Az **Azure-szolgáltatások hoz való hozzáférés engedélyezése** vezérlőt állítsa KI-ra.

    > [!IMPORTANT]
    > Ha a vezérlő beállítása BE, az Azure SQL Database-kiszolgáló elfogadja a kommunikációt az Azure-határon belüli bármely alhálózatról, azaz az Azure-adatközpontok számára meghatározott tartományokban lévő IP-címek egyikéről. Ha a vezérlőt be állítja BE-re, biztonsági szempontból túlzott hozzáférés lehet. A Microsoft Azure virtuális hálózat szolgáltatás végpontja az SQL Database virtuális hálózati szabályfunkciójával együttműködve együttesen csökkentheti a biztonsági felület et.

4. Kattintson a **+ Meglévő** vezérlő hozzáadása elemre a **Virtuális hálózatok** szakaszban.

    ![Kattintson a meglévő hozzáadása (alhálózati végpont, SQL szabályként).][image-portal-firewall-vnet-add-existing-10-png]

5. Az új **Létrehozás/frissítés** ablaktáblán töltse ki a vezérlőket az Azure-erőforrások nevével.

    > [!TIP]
    > Az alhálózathoz a megfelelő **címelőtagot** kell megadnia. Az értéket a portálon találja.
    > Navigálás **Minden erőforrás minden** &gt; **típusú** &gt; **virtuális hálózatok**. A szűrő megjeleníti a virtuális hálózatokat. Kattintson a virtuális hálózatra, majd **az Alhálózatok parancsra.** A **CÍMTARTOMÁNY** oszlopban található a szükséges Cím előtag.

    ![Töltse ki az új szabály mezőit.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Kattintson az **ABLAKtábla** alján található OK gombra.

7. Tekintse meg az eredményül kapott virtuális hálózati szabályt a tűzfal ablaktábláján.

    ![Tekintse meg az új szabályt a tűzfal ablaktábláján.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> A következő állapotok vagy állapotok vonatkoznak a szabályokra:
>
> - **Kész:** Azt jelzi, hogy a kezdeményezett művelet sikeres volt.
> - **Sikertelen:** Azt jelzi, hogy a kezdeményezett művelet sikertelen volt.
> - **Törölve:** Csak a Törlés műveletre vonatkozik, és azt jelzi, hogy a szabály törölve lett, de már nem érvényes.
> - **InProgress:** Azt jelzi, hogy a művelet folyamatban van. A régi szabály akkor érvényes, ha a művelet ebben az állapotban van.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Az Azure virtuális hálózati szolgáltatásának végpontjai][vm-virtual-network-service-endpoints-overview-649d]
- [Az Azure SQL Database kiszolgálószintű és adatbázis-szintű tűzfalszabályok][sql-db-firewall-rules-config-715d]

Az Azure SQL Database virtuális hálózati szabályszolgáltatása 2017 szeptemberének végén vált elérhetővé.

## <a name="next-steps"></a>További lépések

- [A PowerShell segítségével hozzon létre egy virtuális hálózati szolgáltatás végpontját, majd egy virtuális hálózati szabályt az Azure SQL Database számára.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtuális hálózati szabályok: REST][rest-api-virtual-network-rules-operations-862r] API-kkal végzett műveletek

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]: ../expressroute/index.yml
[rbac-what-is-813s]:../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md
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

---
title: Hitelesítési mechanizmusok a COPY utasítással
description: Az adatmennyiség tömeges betöltésére szolgáló hitelesítési mechanizmusok körvonalazása
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: c7d6a0d289953376c6535f2401c9c77edb3205df
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994865"
---
# <a name="securely-load-data-using-synapse-sql"></a>Adattárolás biztonságos betöltése a szinapszis SQL használatával

Ez a cikk a [copy utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)biztonságos hitelesítési mechanizmusaival kapcsolatos példákat mutatja be és ismerteti. A MÁSOLÁSi utasítás az adatok tömeges betöltésének legrugalmasabb és biztonságos módja a szinapszis SQL-ben.
## <a name="supported-authentication-mechanisms"></a>Támogatott hitelesítési mechanizmusok

A következő mátrix ismerteti az egyes fájltípusok és a Storage-fiókok támogatott hitelesítési módszereit. Ez a forrás tárolási helyére és a hiba fájljának helyére vonatkozik.

|                      |                CSV                |              Parquet              |                ORC                |
| :------------------: | :-------------------------------: | :-------------------------------: | :-------------------------------: |
|  Azure Blob Storage  | SAS/MSI/EGYSZERŰ SZOLGÁLTATÁSNÉV/KULCS/HRE |              SAS/KULCS              |              SAS/KULCS              |
| Azure Data Lake Gen2 | SAS/MSI/EGYSZERŰ SZOLGÁLTATÁSNÉV/KULCS/HRE | SAS/MSI/EGYSZERŰ SZOLGÁLTATÁSNÉV/KULCS/HRE | SAS/MSI/EGYSZERŰ SZOLGÁLTATÁSNÉV/KULCS/HRE |

## <a name="a-storage-account-key-with-lf-as-the-row-terminator"></a>A. Storage-fiók kulcsa a LF-rel, a sor lezáró sorként


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Használja a hexadecimális értéket (0x0A) a soremelés/sortörési karakter megadásához. Megjegyzés: a COPY utasítás a "\n" karakterláncot "\r\n"-ként értelmezi (a kocsivissza karakter).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator"></a>B. Közös hozzáférésű aláírások (SAS) a CRLF, mint a sorok lezárója
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Ne adja meg a ROWTERMINATOR "\r\n" néven, amely "\r\r\n" lesz értelmezve, és elemzési problémákhoz vezethet

## <a name="c-managed-identity"></a>C. Felügyelt identitás

A felügyelt identitás hitelesítésére akkor van szükség, ha a Storage-fiók VNet van csatolva. 

### <a name="prerequisites"></a>Előfeltételek

1. Azure PowerShell telepítése az [útmutató](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)segítségével.
2. Ha rendelkezik általános célú v1-vagy blob Storage-fiókkal, először az [útmutató](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)használatával kell frissítenie az általános célú v2-re.
3. Engedélyeznie kell, **hogy a megbízható Microsoft-szolgáltatások hozzáférjenek ehhez a Storage-fiókhoz** az Azure Storage **-fiók tűzfala és a virtuális hálózatok** beállítások menüjében. További információért tekintse meg ezt az [útmutatót](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) .
#### <a name="steps"></a>Lépések

1. A PowerShellben **regisztrálja az SQL servert** Azure Active Directory (HRE) használatával:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. Hozzon létre egy **általános célú v2 Storage-fiókot** az [útmutató](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)segítségével.

   > [!NOTE]
   > Ha rendelkezik általános célú v1-vagy blob Storage-fiókkal, először a **v2-re kell frissítenie** az [útmutató](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)segítségével.

3. A Storage-fiók területen navigáljon a **Access Control (iam)** elemre, és válassza a **szerepkör-hozzárendelés hozzáadása**elemet. Rendeljen hozzá **Storage blob-Adattulajdonost, közreműködőt vagy olvasó** RBAC-szerepkört az SQL Serverhez.

   > [!NOTE]
   > Ezt a lépést csak a tulajdonosi jogosultsággal rendelkező tagok hajthatják végre. Az Azure-erőforrások különböző beépített szerepköreiért tekintse meg ezt az [útmutatót](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
   
4. Most már futtathatja a "felügyelt identitás" megadását megadó MÁSOLÁSi utasítást:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

> [!IMPORTANT]
>
> - Határozza meg a **Storage** **blob** -Adattulajdonost, közreműködőt vagy olvasó RBAC szerepkört. Ezek a szerepkörök eltérnek a tulajdonos, közreműködő és olvasó Azure beépített szerepköreitől. 

## <a name="d-azure-active-directory-authentication-aad"></a>D. Azure Active Directory hitelesítés (HRE)
#### <a name="steps"></a>Lépések

1. A Storage-fiók területen navigáljon a **Access Control (iam)** elemre, és válassza a **szerepkör-hozzárendelés hozzáadása**elemet. Rendeljen hozzá **Storage blob-Adattulajdonost, közreműködőt vagy olvasó** RBAC-szerepkört a HRE-felhasználóhoz. 

2. Konfigurálja az Azure AD-hitelesítést az alábbi [dokumentáción](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#create-an-azure-ad-administrator-for-azure-sql-server)keresztül. 

3. Kapcsolódjon az SQL-készlethez Active Directory használatával, ahol a MÁSOLÁSi utasítást a hitelesítő adatok megadása nélkül is futtathatja:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```

> [!IMPORTANT]
>
> - Határozza meg a **Storage** **blob** -Adattulajdonost, közreműködőt vagy olvasó RBAC szerepkört. Ezek a szerepkörök eltérnek a tulajdonos, közreműködő és olvasó Azure beépített szerepköreitől. 

## <a name="e-service-principal-authentication"></a>E. Egyszerű szolgáltatásnév hitelesítése
#### <a name="steps"></a>Lépések

1. [Azure Active Directory-(HRE-) alkalmazás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)
2. [Alkalmazás AZONOSÍTÓjának beolvasása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)
3. [A hitelesítési kulcs beszerzése](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret)
4. [A v1 OAuth 2,0 token végpontjának beolvasása](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Olvasási, írási és végrehajtási engedélyek kiosztása a HRE-alkalmazáshoz](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) a Storage-fiókban
6. Ezután futtathatja a COPY utasítást:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Az OAuth 2,0 token végpont **v1** -es verziójának használata

## <a name="next-steps"></a>További lépések

- A részletes szintaxissal kapcsolatban olvassa el a [copy utasítással](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) kapcsolatos cikket.
- Az ajánlott eljárások betöltéséhez tekintse meg az [adatgyűjtés áttekintése című](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) cikket

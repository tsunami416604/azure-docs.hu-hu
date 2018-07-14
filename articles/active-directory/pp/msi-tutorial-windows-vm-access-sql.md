---
title: Windows virtuális gép MSI használata az Azure SQL eléréséhez
description: Ez az oktatóanyag végigvezeti egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) Azure SQL eléréséhez használt folyamatán.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c3e2af8c6864204a4c373ac4e1c12090389ac32
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007426"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata Azure SQL eléréséhez

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az oktatóanyag bemutatja, hogyan használható a Felügyeltszolgáltatás-identitás (MSI) egy Windows virtuális gép (VM) egy Azure SQL-kiszolgáló eléréséhez. Felügyelt Szolgáltatásidentitások Azure automatikusan kezeli, és lehetővé teszi szolgáltatások, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy a hitelesítő adatok beszúrása a kódot a hitelesítéshez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egy Windows virtuális gép az MSI engedélyezéséhez 
> * A virtuális gép hozzáférést biztosít egy Azure SQL-kiszolgálóra
> * A virtuális gép identitásának használatával hozzáférési jogkivonatot kapjon, és ezzel egy Azure SQL-kiszolgáló lekérdezéséhez

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben az oktatóanyagban létrehozunk egy új Windows virtuális Gépet.  A meglévő virtuális gép MSI is engedélyezheti.

1.  Kattintson a **erőforrás létrehozása** a az Azure portal bal felső sarkában.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gépet.
4.  Válassza ki a megfelelő **előfizetés** a virtuális gép a legördülő listában.
5.  Jelölje be egy új **erőforráscsoport** , amelyben a virtuális gép létrehozásához, válassza ki a **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6.  Válassza ki a méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A beállítások oldalon hagyja változatlanul az alapértelmezett beállításokat, majd kattintson **OK**.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen az MSI engedélyezéséhez 

Virtuális gép MSI lehetővé teszi, hogy a hozzáférési tokenek beszerzése az Azure ad-ből anélkül, hogy hitelesítő adatok üzembe a kódot kellene. MSI engedélyezéséhez jelzi az Azure a virtuális gép létrehozása egy felügyelt identitás. Valójában MSI engedélyezéséhez két dolgot eredményez: a virtuális Gépen az MSI-Virtuálisgép-bővítmény telepíti, és lehetővé teszi, hogy az Azure Resource Manager MSI.

1.  Válassza ki a **virtuális gép** , hogy szeretné-e az MSI engedélyezéséhez.  
2.  A bal oldali navigációs sávon kattintson **konfigurációs**. 
3.  Látja **Felügyeltszolgáltatás-identitás**. Regisztráljon, és az MSI engedélyezéséhez, válassza ki a **Igen**, ha szeretné letiltani, válassza a nem. 
4.  Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.  
    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha szeretné ellenőrizni, és a virtuális gépen ellenőrizze, hogy mely bővítmények, kattintson a **bővítmények**. Ha az MSI engedélyezve van, majd **ManagedIdentityExtensionforWindows** megjelenik a listában.

    ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>A virtuális gép hozzáférést biztosítani egy adatbázis egy Azure SQL Serveren

Most is hozzáférést biztosít a virtuális gép egy Azure SQL server adatbázishoz.  Ebben a lépésben a meglévő SQL-kiszolgálót használ, vagy hozzon létre egy újat.  Hozzon létre egy új kiszolgálót, és az adatbázis az Azure portal használatával, kövesse a [Azure SQL-rövid](~/articles/sql-database/sql-database-get-started-portal.md). Még nincsenek rövid útmutatók, amelyek használják az Azure CLI-vel és az Azure PowerShell-lel a [Azure SQL-dokumentáció](~/articles/sql-database/index.yml).

A virtuális gép hozzáférést egy adatbázishoz három lépésből áll:
1.  Hozzon létre egy csoportot az Azure ad-ben, és adja hozzá a virtuális gép MSI a csoporthoz.
2.  Az SQL Server az Azure AD-hitelesítés engedélyezéséhez.
3.  Hozzon létre egy **felhasználó** az adatbázisban, amely az Azure AD-csoport jelöli.

> [!NOTE]
> Általában akkor kell létrehoznia, amely közvetlenül a virtuális gép MSI egy felhasználó.  Azure SQL jelenleg nem engedélyezi az Azure AD egyszerű szolgáltatás, amely a virtuális gép MSI le kell képezni egy felhasználó jelöli.  Támogatott Áthidaló megoldásként adja hozzá a virtuális gép MSI egy Azure AD-csoportot, majd egy felhasználó létrehozásához, amely a csoport jelöli az adatbázisban.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Hozzon létre egy csoportot az Azure ad-ben, és adja hozzá a virtuális gép MSI a csoport

Egy meglévő Azure AD-csoportot, vagy hozzon létre egy új Azure AD PowerShell használatával.  

Először telepítse a [az Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul. Majd jelentkezzen be `Connect-AzureAD`, és futtassa a következő parancsot létrehozni a csoportot, és mentse azt egy változóban:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

A kimenet a következő, a változó értékét is megvizsgálja hasonlóan néz ki:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Ezután adja hozzá a virtuális gép MSI a csoporthoz.  Az MSI kell **ObjectId**, amely akkor is lekérése Azure PowerShell-lel.  Először töltse le [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Majd jelentkezzen be `Connect-AzureRmAccount`, és futtassa a következő parancsokat:
- Győződjön meg arról, a munkamenet-környezet van beállítva, a kívánt Azure-előfizetéssel, ha több rendelkezik.
- Az Azure-előfizetéséhez elérhető erőforrások listázása, a ellenőrizze a megfelelő erőforráscsoportot és a egy virtuális gép neve.
- Az MSI-virtuális gép tulajdonságait, a megfelelő értékeket a GET `<RESOURCE-GROUP>` és `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

A kimenet a következőhöz hasonló, amely is megvizsgálja a virtuális gép MSI szolgáltatásnév Objektumazonosítóját szolgáltatás:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

A virtuális gép MSI adhat hozzá a csoporthoz.  Egyszerű szolgáltatás csak egy Azure AD PowerShell-lel csoportot adhat hozzá.  Futtassa ezt a parancsot:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Ha is megvizsgálja a csoporttagság később, a kimenet a következőképpen néz ki:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Az SQL Server az Azure AD-hitelesítés engedélyezése

Most, hogy létrehozta a csoport és a virtuális gép MSI hozzáadódik a tagság, [konfigurálása az Azure AD-hitelesítés az SQL Server](~/articles/sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) az alábbi lépéseket követve:

1.  Az Azure Portalon válassza ki a **SQL Server-kiszolgálók** a bal oldali navigációs sávon.
2.  Kattintson az SQL server az Azure AD-hitelesítés engedélyezni kell.
3.  Az a **beállítások** szakaszban kattintson a panel **Active Directory-rendszergazda**.
4.  A parancssávon kattintson **rendszergazda beállítása**.
5.  Válassza ki az Azure AD felhasználói fiók lehet végezni a kiszolgáló rendszergazdája, és kattintson a **válassza ki.**
6.  A parancssávon kattintson **mentéséhez.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Egy felhasználó létrehozásához az adatbázis, amely az Azure AD-csoport

A következő lépés kell [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Mielőtt elkezdené, is hasznos lehet az Azure AD-integrációval kapcsolatos háttérinformációk a következő cikkekben talál:

- [Univerzális hitelesítéssel az SQL Database és az SQL Data Warehouse (többtényezős hitelesítés támogatása ssms-ben)](~/articles/sql-database/sql-database-ssms-mfa-authentication.md)
- [Konfigurálhatja és kezelheti az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](~/articles/sql-database/sql-database-aad-authentication-configure.md)

1.  Indítsa el az SQL Server Management Studiót.
2.  Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adjon meg az SQL-kiszolgáló neve az a **kiszolgálónév** mező.
3.  Az a **hitelesítési** mezőben válassza **Active Directory - MFA-támogatással rendelkező univerzális**.
4.  Az a **felhasználónév** mezőben adja meg az Ön által beállított kiszolgálói rendszergazdaként, például az Azure AD-szolgáltatásfiók neve helen@woodgroveonline.com
5.  Kattintson a **Beállítások** gombra.
6.  Az a **csatlakozhat az adatbázishoz** mezőben adja meg a konfigurálni kívánt nem rendszer-adatbázis nevét.
7.  Kattintson a **Connect** (Csatlakozás) gombra.  A bejelentkezési folyamat befejezéséhez.
8.  Az a **Object Explorer**, bontsa ki a **adatbázisok** mappát.
9.  Kattintson a jobb gombbal egy felhasználói adatbázist, és kattintson a **új lekérdezés**.
10.  A lekérdezési ablakban adja meg a következő sort, és kattintson a **Execute** kattintson az eszköztár:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     A parancs sikeresen befejeződik, a felhasználó a csoport létrehozása.
11.  A lekérdezési ablakban törölje, adja meg a következő sort, és kattintson **Execute** kattintson az eszköztár:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     A parancs sikeresen befejeződik, a felhasználó jegykiadó olvassa el a teljes adatbázis lehetővé teszi.

A virtuális Gépen futó kód mostantól egy token beszerzéséhez az MSI, és a jogkivonat segítségével hitelesíti az SQL Server.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>A virtuális gép identitásának használatával hozzáférési jogkivonatot kapjon és meghívására az Azure SQL 

Az Azure SQL natív módon támogatja az Azure AD-hitelesítést, így közvetlenül fogadja a hozzáférési jogkivonatok beszerezni az MSI használatával.  Használja a **hozzáférési jogkivonat** metódusa az SQL-kapcsolat létrehozásával.  Az Azure SQL-integráció az Azure ad-vel része, és eltér a kapcsolati karakterláncot a hitelesítő adatok megadása.

Íme egy .net kód példa az SQL-hozzáférési token használatával egy kapcsolat megnyitásával.  Ezt a kódot kell futtatni lehessen a virtuális gép MSI-végpontot a virtuális gépen.  **.NET framework 4.6** vagy újabb verziója szükséges az access-token módszer használatához.  Cserélje le az értékeket az AZURE-SQL-kiszolgálónév és az adatbázis megfelelően.  Azure SQL-van, vegye figyelembe az erőforrás-azonosító "https://database.windows.net/".

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call MSI endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Azt is megteheti a végpontok közötti telepítés teszteléséhez írni, és a egy alkalmazás a virtuális gép üzembe helyezése nélkül gyorsan PowerShell használatával.

1.  A portálon lépjen a **virtuális gépek** , és nyissa meg a Windows virtuális gép és a a **áttekintése**, kattintson a **Connect**. 
2.  Adja meg a **felhasználónév** és **jelszó** számára, amelyhez hozzáadta a Windows virtuális gép létrehozásakor. 
3.  Most, hogy létrehozott egy **távoli asztali kapcsolat** nyissa meg a virtuális géppel **PowerShell** a távoli munkamenet. 
4.  A PowerShell-lel `Invoke-WebRequest`, indítson egy a helyi MSI-végpontot a hozzáférési jogkivonat beszerzése az Azure SQL.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    A válasz egy JSON-objektumból átalakítása egy PowerShell-objektumot. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Bontsa ki a hozzáférési jogkivonatot a válaszból.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Nyisson meg egy kapcsolatot az SQL Server. Ne feledje el az értékeket az AZURE-SQL-kiszolgálónév és az adatbázis.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Ezután hozzon létre, és a egy lekérdezést küldenek a kiszolgálónak.  Ne feledje, hogy táblához.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Vizsgálja meg az értékét `$DataSet.Tables[0]` a lekérdezés eredményeinek megtekintése.  Gratulálunk, egy virtuális gép MSI használata az adatbázis már lekérdezett és nélkül adja meg a hitelesítő adatokat!

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).
- Tudjon meg többet [Azure SQL-támogatás az Azure AD-hitelesítés](~/articles/sql-database/sql-database-aad-authentication.md).
- Tudjon meg többet [Azure SQL-támogatás az Azure AD-hitelesítés konfigurálása](~/articles/sql-database/sql-database-aad-authentication-configure.md).
- Tudjon meg többet [hitelesítés és hozzáférés az SQL server](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.

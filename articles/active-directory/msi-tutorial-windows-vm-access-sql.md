---
title: "Azure SQL eléréséhez használja a Windows virtuális gép MSI"
description: "Ez az oktatóanyag végigvezeti az Azure SQL eléréséhez használt egy Windows virtuális gép felügyelt szolgáltatás identitás (MSI)."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: 58c6b5e076e9546aa621c9d1aea8893edf4335c0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Egy Windows virtuális gép felügyelt szolgáltatás identitás (MSI) Azure SQL eléréséhez használja

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan felügyelt szolgáltatás identitásának (MSI) egy Windows virtuális gép (VM) az Azure SQL-kiszolgáló elérésére használhat. Felügyelt szolgáltatás-identitások Azure automatikusan kezeli, és lehetővé teszik, hogy az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat beszúrni a kódot támogató szolgáltatások hitelesítést. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gép Windows MSI engedélyezése 
> * A virtuális gép hozzáférést biztosíthat az Azure SQL-kiszolgáló
> * Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és használja az Azure SQL server lekérdezésére

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben az oktatóanyagban azt hozzon létre egy új Windows virtuális Gépet.  A meglévő virtuális MSI is engedélyezheti.

1.  Kattintson a **hozzon létre egy erőforrást** gomb az Azure portál bal felső sarkában található.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gép.
4.  Válassza ki a megfelelő **előfizetés** a virtuális gép meg a legördülő listában.
5.  Jelölje be egy új **erőforráscsoport** , amelyen a virtuális gép létrehozásához, **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6.  Adja meg a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A beállítások lapon hagyja az alapértelmezett beállításokat, majd kattintson **OK**.

    ![Kép helyettesítő szövege](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen lévő MSI engedélyezése 

A virtuális gép MSI hozzáférési jogkivonatok beolvasása az Azure AD meg szeretne adni a kód hitelesítő adatokat igénylő nélkül teszi lehetővé. MSI engedélyezése közli az Azure-hoz létre egy felügyelt a virtuális gép számára. A színfalak MSI engedélyezése két dolgot eredményez: az MSI-Virtuálisgép-bővítmény a virtuális Gépet telepít, és lehetővé teszi, hogy az Azure Resource Manager MSI.

1.  Válassza ki a **virtuális gép** , hogy szeretné-e engedélyezze MSI-t.  
2.  A bal oldali navigációs sávon kattintson **konfigurációs**. 
3.  Látni **Szolgáltatásidentitás felügyelt**. Regisztrálja, és engedélyezze a MSI-t, jelölje be **Igen**, ha szeretné letiltani, válassza a nem. 
4.  Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.  
    ![Kép helyettesítő szövege](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha szeretné ellenőrizni, és a virtuális gép mely bővítmények ellenőrizze, kattintson a **bővítmények**. Ha MSI engedélyezve van, majd **ManagedIdentityExtensionforWindows** listájában jelenik meg.

    ![Kép helyettesítő szövege](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>A virtuális gép hozzáférést biztosíthat az Azure SQL Server adatbázis

Most is a virtuális gép hozzáférést az Azure SQL server adatbázishoz.  Ebben a lépésben a meglévő SQL-kiszolgáló használata, vagy hozzon létre egy újat.  Hozzon létre egy új kiszolgálót és adatbázist, az Azure portál használatával, folytassa a [Azure SQL gyors üzembe helyezés](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Is vannak, amelyek az Azure CLI és az Azure PowerShell quickstarts a [Azure SQL-dokumentáció](https://docs.microsoft.com/azure/sql-database/).

A virtuális gép egy adatbázis elérését a három lépésben történik:
1.  Hozzon létre egy csoportot az Azure ad-ben, és adja hozzá a virtuális gép MSI a csoport.
2.  Az SQL Server az Azure AD-alapú hitelesítés engedélyezéséhez.
3.  Hozzon létre egy **felhasználó** az adatbázisban, amely az Azure AD-csoport jelöli.

> [!NOTE]
> Általában egy felhasználó, amely közvetlenül a virtuális gép MSI leképezve hozna létre.  Azure SQL jelenleg nem teszi lehetővé az Azure AD szolgáltatás egyszerű, amely a virtuális gép MSI való leképezése egy felhasználó jelöli.  Támogatott megoldás adja hozzá a virtuális gép MSI-t az Azure AD-csoporthoz, majd hozzon létre egy felhasználó az adatbázisban, amely a csoport jelöli.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Hozzon létre egy csoportot az Azure ad-ben, és adja hozzá a virtuális gép MSI a csoport

Egy meglévő Azure AD-csoportot, vagy hozzon létre egy új Azure AD PowerShell segítségével.  

Először telepítse a [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul. Majd jelentkezzen be `Connect-AzureAD`, és a csoport létrehozása a következő parancsot, és mentse azt egy változóban:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

A kimenet a következőhöz hasonló, amely megvizsgálja-e a változó értékét is:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Ezután adja hozzá a virtuális gép MSI a csoporthoz.  Az MSI kell **ObjectId**, mely akkor is beolvasása az Azure PowerShell.  Első lépésként töltse le [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Majd jelentkezzen be `Login-AzureRmAccount`, és futtassa a következő parancsokat:
- Győződjön meg arról, a munkamenet-környezet van beállítva a kívánt Azure-előfizetéssel, ha több.
- A rendelkezésre álló erőforrások az Azure-előfizetéshez a listában, a ellenőrizze a megfelelő erőforráscsoport és a virtuális gép nevét.
- Az MSI virtuális gép tulajdonságait, használja a megfelelő értékeket `<RESOURCE-GROUP>` és `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

A kimenet a következőhöz hasonló, amely megvizsgálja-e a szolgáltatás egyszerű objektum azonosítója a virtuális gép MSI is:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

A virtuális gép MSI most felvétele a csoportba.  Egy egyszerű szolgáltatás csak a csoport az Azure AD PowerShell adhat hozzá.  Futtassa ezt a parancsot:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Ha is megvizsgálja a csoporttagság később, a kimenete a következőképpen néz ki:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Az SQL Server az Azure AD-alapú hitelesítés engedélyezése

Most, hogy létrehozta a csoport és a virtuális gép MSI hozzáadva a tagsághoz, [konfigurálása az Azure AD-alapú hitelesítés, az SQL Server](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server) az alábbi lépéseket követve:

1.  Válassza ki az Azure-portálon **SQL Server-kiszolgálók** a bal oldali navigációs sávon.
2.  Kattintson az SQL server engedélyezni kell az Azure AD-alapú hitelesítés.
3.  A a **beállítások** szakaszban kattintson a panel **Active Directory-rendszergazda**.
4.  A parancssávon kattintson **-rendszergazda beállítása**.
5.  Válassza ki a kiszolgáló rendszergazdája, és kattintson egy Azure AD-felhasználói fiókot **kiválasztása.**
6.  A parancssávon kattintson **mentéséhez.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Egy felhasználó az adatbázisban, amely jelöli az Azure AD-csoport létrehozása

A következő lépésben kell [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Megkezdése előtt is hasznos lehet a következő cikkeket azzal kapcsolatos információ az Azure AD-integrációs áttekintése:

- [Univerzális hitelesítés használata az SQL-adatbázis és az SQL Data Warehouse (többtényezős hitelesítés támogatása SSMS)](/azure/sql-database/sql-database-ssms-mfa-authentication.md)
- [Konfigurálhatja és kezelheti az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure.md)

1.  Indítsa el az SQL Server Management Studiót.
2.  A a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg az SQL server név a **kiszolgálónév** mező.
3.  Az a **hitelesítési** mezőben válassza **Active Directory - MFA-támogatással rendelkező univerzális**.
4.  Az a **felhasználónév** mezőbe írja be az Azure AD-fiókot, amely a kiszolgáló rendszergazdája, mint például a neve helen@woodgroveonline.com
5.  Kattintson a **Beállítások** gombra.
6.  Az a **Kapcsolódás adatbázis** mezőbe írja be a konfigurálni kívánt rendszert nem tartalmazó adatbázis nevét.
7.  Kattintson a **Connect** (Csatlakozás) gombra.  A bejelentkezési folyamat elvégzése.
8.  Az a **Object Explorer**, bontsa ki a **adatbázisok** mappát.
9.  Kattintson a jobb gombbal egy felhasználói adatbázison, és kattintson a **új lekérdezés**.
10.  A lekérdezési ablakban adja meg a következő sort, és kattintson **Execute** az eszköztárban:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     A parancs sikeresen befejeződik, a felhasználó a csoport létrehozása.
11.  A lekérdezési ablakban törölje, írja be a következő sort, és kattintson **Execute** az eszköztárban:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     A parancs sikeresen befejeződik, a felhasználó a teljes adatbázis olvasás megadása.

A virtuális gépen futó most jogkivonat beszerzése az MSI-fájl, és a jogkivonat segítségével érheti el az SQL-kiszolgálót.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és hívja az Azure SQL használatával 

Az Azure SQL natív módon támogatja az Azure AD hitelesítési, így közvetlenül elfogadása jogkivonatot kapott MSI-fájl használatával.  Használja a **hozzáférési jogkivonat** metódusában SQL történő kapcsolódás lépésein.  Az Azure SQL-integráció az Azure ad-val része, és biztosítja a kapcsolati karakterlánc-felhasználó hitelesítő adatai eltérnek.

Íme egy .net kódot példát a hozzáférési token használatával SQL-kapcsolat megnyitása.  Ez a kód fogja tudni elérni a virtuális gép MSI-végpontot a virtuális Gépen kell futnia.  **A .net keretrendszer 4.6-os** vagy újabb verziója szükséges a hozzáférési token módszer használatával.  Cserélje le a AZURE-SQL-kiszolgálónév és a adatbázis ennek megfelelően.  Megjegyzés: az erőforrás-azonosítója az Azure SQL "https://database.windows.net/".

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

Másik lehetőségként gyors módja, ha a végpontok közötti telepítés írása, és a virtuális Gépen lévő alkalmazás telepítése nélkül tesztelheti PowerShell használatával.

1.  A portálon lépjen a **virtuális gépek** és nyissa meg a Windows rendszerű virtuális gép és a a **áttekintése**, kattintson a **Connect**. 
2.  Adja meg a **felhasználónév** és **jelszó** számára, amely hozzá van, a Windows virtuális gép létrehozása után. 
3.  Most, hogy létrehozott egy **távoli asztali kapcsolat** nyissa meg a virtuális gép **PowerShell** a távoli munkamenet. 
4.  A PowerShell használatával `Invoke-WebRequest`, indítson egy lekérdezést a helyi MSI végpont az Azure SQL megszerezni az olyan hozzáférési jogkivonatot.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    A válasz egy JSON-objektumból átalakítása egy PowerShell-objektum. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Bontsa ki a hozzáférési jogkivonat a választ.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Az SQL Server egy kapcsolat megnyitásához. Ne felejtse el cserélje le az értékeket a AZURE-SQL-kiszolgálónév és az adatbázis.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Ezután hozzon létre, és egy lekérdezést küldeni a kiszolgálónak.  Ne felejtse el tábla értékének cseréjét.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Vizsgálja meg az a `$DataSet.Tables[0]` a lekérdezés eredményeinek megtekintése.  Gratulálunk, már lekérdezett az adatbázist egy virtuális gép MSI használatával és anélkül, hogy hitelesítő adatok!

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](../active-directory/msi-overview.md).
- További információ [Azure SQL támogatása az Azure AD-alapú hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).
- További információ [konfigurálása az Azure SQL támogatása az Azure AD-alapú hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).
- További információ [hitelesítési és hozzáférés az SQL server](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.

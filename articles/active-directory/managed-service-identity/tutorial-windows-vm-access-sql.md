---
title: Windows rendszerű virtuális gép MSI használata az Azure SQL eléréséhez
description: Az oktatóanyag azt ismerteti, hogyan lehet hozzáférni az Azure SQL-hez egy Windows rendszerű VM-beli felügyeltszolgáltatás-identitással (MSI).
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 72452382c4fd2f9c1acb0d773da5c7ed014f9bda
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001932"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Oktatóanyag: Az Azure SQL elérése Windows VM-beli felügyeltszolgáltatás-identitással (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag azt mutatja be, hogyan használhat felügyeltszolgáltatás-identitást (MSI) egy Windows rendszerű virtuális gépen (VM) egy Azure SQL Server eléréséhez. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * MSI engedélyezése Windows VM-en 
> * Azure SQL Server-hozzáférés engedélyezése a VM számára
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd egy Azure SQL Server-kiszolgáló lekérdezése a jogkivonattal

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Egy Windows rendszerű virtuális gép létrehozása egy új erőforráscsoportban

Ebben az oktatóanyagban egy új Windows VM-et fog létrehozni.  A meglévő virtuális gépeken is engedélyezheti az MSI-t.

1.  Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. Az itt létrehozott **felhasználónév** és **jelszó** alkotják a virtuális gépre való bejelentkezéshez használt hitelesítő adatokat.
4.  Válassza ki a megfelelő **előfizetést** a virtuális géphez a legördülő menüben.
5.  A virtuális gép létrehozásához használni kívánt új **erőforráscsoport** kiválasztásához válassza az **Új létrehozása** lehetőséget. Amikor végzett, kattintson az **OK** gombra.
6.  Válassza ki a virtuális gép méretét. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A Beállítások lapon hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

    ![Helyettesítő képszöveg](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>MSI engedélyezése a virtuális gépen 

A VM MSI-vel anélkül kérhet le hozzáférési jogkivonatokat az Azure AD-ből, hogy hitelesítő adatokat kellene a kódba illesztenie. Az MSI engedélyezése arra utasítja az Azure-t, hogy hozzon létre egy felügyelt identitást a virtuális géphez. A háttérben az MSI engedélyezésének két következménye van: regisztrálja a virtuális gépet az Azure Active Directoryban a felügyelt identitása létrehozásához, és konfigurálja az identitást a virtuális gépen.

1.  Válassza ki azt a **virtuális gépet**, amelyen engedélyezni szeretné az MSI-t.  
2.  A bal oldali navigációs sávban kattintson a **Konfigurálás** elemre. 
3.  Megjelenik a **felügyeltszolgáltatás-identitás**. Az MSI regisztrálásához és engedélyezéséhez kattintson az **Igen**, a letiltásához a Nem gombra. 
4.  Mindenképp kattintson a **Mentés** gombra a konfiguráció mentéséhez.  
    ![Helyettesítő képszöveg](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Hozzáférés engedélyezése a virtuális gép számára egy Azure SQL Server-adatbázishoz

Most hozzáférést biztosíthat a virtuális gépnek egy Azure SQL Serveren található adatbázishoz.  Ehhez a lépéshez meglévő SQL-kiszolgálót használhat, de újat is létrehozhat.  Ha új kiszolgálót és adatbázist szeretne létrehozni az Azure Portalon, kövesse ennek az [Azure SQL rövid útmutatónak](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) a lépéseit. Az [Azure SQL dokumentációjában](https://docs.microsoft.com/azure/sql-database/) olyan rövid útmutatók is vannak, amelyek az Azure CLI-t és az Azure PowerShellt használják.

Három lépés kell hozzá, hogy a VM hozzá tudjon férni egy adatbázishoz:
1.  Létre kell hozni egy csoportot Azure AD-ben, amelybe fel kell venni tagként a VM MSI-t.
2.  Engedélyezni kell az Azure AD-hitelesítést az SQL-kiszolgáló számára.
3.  Létre kell hozni egy **tartalmazott felhasználót** az adatbázisban, amely az Azure AD-csoportot fogja képviselni.

> [!NOTE]
> Általában egy olyan tartalmazott felhasználót érdemes létrehozni, aki közvetlenül a VM MSI-jéhez van leképezve.  Az Azure SQL jelenleg nem engedélyez olyan Azure AD-szolgáltatásneveket, amelyek egy tartalmazott felhasználóra leképezni kívánt VM MSI-t képviselnek.  Támogatott áthidaló megoldásként a VM MSI-t egy Azure AD-csoport tagjává kell tenni, majd létre kell hozni a csoportot képviselő tartalmazott felhasználót az adatbázisban.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Csoport létrehozása az Azure AD-ban, és a VMI MSI felvétele a csoportba

Meglévő Azure AD-csoportot is használhat, de újat is létrehozhat az Azure AD PowerShell-lel.  

Először telepítse az [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modult. Ezután jelentkezzen be a `Connect-AzureAD` paranccsal, és futtassa a következő parancsot a csoport létrehozásához, majd mentse azt egy változóban:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

A kimenet a következőképpen néz ki, amely a változó értékét is megvizsgálja:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Ezután adja hozzá a VM MSI-t a csoporthoz.  Szüksége van az MSI **ObjectId** azonosítójára, amelyet az Azure PowerShell-lel kérhet le.  Először töltse le az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Ezután jelentkezzen be a `Connect-AzureRmAccount` paranccsal, és futtassa az alábbi parancsokat, amelyekkel:
- Meggyőződhet róla, hogy a munkamenet-környezet a kívánt Azure-előfizetéshez van beállítva, ha több előfizetéssel is rendelkezik.
- Listázhatja az Azure-előfizetésekben elérhető erőforrásokat annak ellenőrzéséhez, hogy az erőforráscsoport és a virtuális gép neve megfelelő-e.
- Lekérheti az MSI VM-tulajdonságait a `<RESOURCE-GROUP>` és a `<VM-NAME>` megfelelő értékeivel.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

A kimenet a következőképpen néz ki, amely a VM MSI szolgáltatásnevének objektumazonosítóját is megvizsgálja:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Most adja hozzá a VM MSI-t a csoporthoz.  Csak az Azure AD PowerShell-lel adhat szolgáltatásnevet egy csoporthoz.  Futtassa ezt a parancsot:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Ha később a csoporttagságot is megvizsgálja, a kimenet a következőképpen néz ki:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Azure AD-hitelesítés engedélyezése az SQL-kiszolgáló számára

Most, hogy létrehozta a csoportot, és a VM MSI-t a tagjává tette, az alábbi lépésekkel [konfigurálhatja az SQL-kiszolgáló Azure AD-hitelesítését](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server):

1.  Az Azure Portal bal oldali navigációs sávjában válassza az **SQL-kiszolgálók** elemet.
2.  Kattintson az Azure AD-hitelesítéshez engedélyezni kívánt SQL-kiszolgálóra.
3.  A panel **Beállítások** szakaszában kattintson az **Active Directory-rendszergazda** elemre.
4.  Kattintson a **Rendszergazda beállítása** elemre a parancssávon.
5.  Válassza ki azt az Azure AD felhasználói fiókot, amelyet a kiszolgáló rendszergazdájává szeretne tenni, és kattintson a **Kiválasztás** lehetőségre.
6.  Kattintson a **Mentés** gombra a parancssávon.

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Az Azure AD-csoportot képviselő tartalmazott felhasználó létrehozása az adatbázisban

A következő lépéshez a [Microsoft SQL Server Management Studióra](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) lesz szüksége. Mielőtt hozzálátna, hasznos lehet áttekinteni az Azure AD-integráció hátterével foglalkozó következő cikkeket:

- [Univerzális hitelesítés az SQL Database és az SQL Data Warehouse használatával (SSMS-támogatás az MFA-hoz)](/azure/sql-database/sql-database-ssms-mfa-authentication.md)
- [Azure Active Directory-hitelesítés konfigurálása és kezelése az SQL Database vagy az SQL Data Warehouse használatával](/azure/sql-database/sql-database-aad-authentication-configure.md)

1.  Indítsa el az SQL Server Management Studiót.
2.  A **Connect to Server** (Csatlakozás kiszolgálóhoz) párbeszédablakban írja be az SQL-kiszolgáló nevét a **Server name** (Kiszolgáló neve) mezőbe.
3.  Az **Authentication** (Hitelesítés) mezőben válassza ki az **Active Directory - Universal with MFA support** (Active Directory – univerzális, MFA-támogatással) lehetőséget.
4.  A **User name** (Felhasználónév) mezőbe írja be azon Azure AD-fiók nevét, amelyet a kiszolgáló rendszergazdájaként szeretne beállítani (például helen@woodgroveonline.com).
5.  Kattintson a **Beállítások** gombra.
6.  A **Connect to database** (Csatlakozás az adatbázishoz) mezőbe írja be a konfigurálni kívánt, nem rendszerszintű adatbázis nevét.
7.  Kattintson a **Connect** (Csatlakozás) gombra.  Fejezze be a bejelentkezést.
8.  Az **Object Explorerben** bontsa ki a **Databases** (Adatbázisok) mappát.
9.  Kattintson a jobb gombbal egy felhasználói adatbázisra, majd kattintson a **New Query** (Új lekérdezés) menüpontra.
10.  A lekérdezési ablakban írja be a következő sort, és kattintson az eszköztár **Execute** (Végrehajtás) gombjára:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     A parancsnak sikeresen futnia kell, és létre kell hoznia a csoport tartalmazott felhasználóját.
11.  Törölje a lekérdezési ablakot, írja be a következő sort, és kattintson az eszköztár **Execute** (Végrehajtás) gombjára:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     A parancsnak sikeresen futnia kell, így a tartalmazott felhasználó képes lesz a teljes adatbázis olvasására.

A virtuális gépen futó kód most le tud kérni egy jogkivonatot az MSI-ről, amellyel hitelesíthet az SQL-kiszolgálón.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure SQL meghívása a használatával 

Az Azure SQL natív támogatást nyújt az Azure AD-hitelesítésnek, így közvetlenül is elfogadhatja az MSI használatával beszerzett hozzáférési jogkivonatokat.  Az SQL-lel létesített kapcsolat létrehozásához használja az **access token** metódust.  Ez az Azure SQL és az Azure AD integrációjának része, és eltér attól a megoldástól, amikor a kapcsolati sztringen adja meg a hitelesítő adatokat.

Bemutatunk egy .Net-kódpéldát arra, hogyan létesíthető kapcsolat az SQL-kiszolgálóval egy hozzáférési jogkivonattal.  Ennek a kódnak kell futnia a virtuális gépen a VM MSI végpontjának eléréséhez.  A **.NET-keretrendszer 4.6-os** vagy újabb verziójára van szükség az access token (hozzáférési jogkivonat) metódus használatához.  Cserélje le az AZURE-SQL-SERVERNAME és a DATABASE értékét a megfelelő értékre.  Figyelje meg, hogy az Azure SQL erőforrás-azonosítója a „https://database.windows.net/”.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
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

A PowerShell használatával is gyorsan tesztelhető a végpontok közötti beállítás anélkül, hogy alkalmazást kellene írnia és üzembe helyeznie a virtuális gépen.

1.  A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre. 
2.  A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg. 
3.  Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a **PowerShellt** a távoli munkamenetben. 
4.  A Powershell `Invoke-WebRequest` parancsával küldjön kérést a helyi MSI-végpontra, hogy lekérjen egy hozzáférési jogkivonatot az Azure SQL-hez.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    Konvertálja a választ JSON-objektumból PowerShell-objektummá. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Nyerje ki a hozzáférési jogkivonatot a válaszból.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Nyisson meg egy kapcsolatot az SQL-kiszolgálóval. Ne felejtse el lecserélni az AZURE-SQL-SERVERNAME és a DATABASE értékét.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Ezután hozzon létre és küldjön el egy lekérdezést a kiszolgálónak.  Ne felejtse el lecserélni a TABLE értékét.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Vizsgálja meg a `$DataSet.Tables[0]` értékét, hogy áttekinthesse a lekérdezés eredményeit.  Ezzel lekérdezte az adatbázist egy VM MSI-vel, hitelesítő adatok megadása nélkül!

## <a name="next-steps"></a>További lépések

Az oktatóanyag bemutatta, hogyan hozhat létre felügyeltszolgáltatás-identitásokat az Azure SQL Server eléréséhez.  További információ az Azure SQL Serverről:

> [!div class="nextstepaction"]
>[Az Azure SQL Database szolgáltatás](/azure/sql-database/sql-database-technical-overview)

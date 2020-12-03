---
title: A PowerShell használata a Power BI regisztrálásához és vizsgálatához (előzetes verzió)
description: Ismerje meg, hogyan regisztrálhat és vizsgálhat meg egy Power BI-bérlőt az Azure hatáskörébe a PowerShell használatával.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552429"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Power BI regisztrálása és vizsgálata a PowerShell használatával az Azure hatáskörébe (előzetes verzió) 

Ez a cikk bemutatja, hogyan használható a PowerShell egy Power BI-bérlő vizsgálatára egy Azure-beli hatáskörébe-katalógusban.

## <a name="power-bi-authentication-background"></a>Power BI hitelesítési háttér

A hatáskörébe tartozó katalógusnak csatlakoznia kell a Power BI felügyeleti API-hoz a Power BI-bérlőben található összetevők vizsgálatához. A Power BI felügyeleti API jelenleg két típusú hitelesítést támogat:

- Felügyelt identitás (MSI).
- Delegált felhasználó hitelesítése.

> [!Note]
> Az MSI használata ajánlott, kivéve, ha delegált felhasználói hitelesítésre van szükség.

## <a name="create-a-security-group"></a>Biztonsági csoport létrehozása

Minden hatókör-katalógus saját, rendszerhez rendelt felügyelt identitással rendelkezik, amelyeknek hozzáférést kell adni a Power BI bérlőhöz a vizsgálat engedélyezéséhez. A katalógus neve felhasználható a Azure Portal identitásának megkereséséhez.

1. A [Azure Portal](https://portal.azure.com)keresse meg a Azure Active Directory.
1. Hozzon létre egy új biztonsági csoportot a Azure Active Directoryban, [és hozzon létre egy alapszintű csoportot, és vegyen fel tagokat a Azure Active Directory használatával](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Ezt a lépést kihagyhatja, ha már rendelkezik a használni kívánt biztonsági csoporttal.

1. Ügyeljen arra, hogy a **csoport típusaként** válassza a biztonság lehetőséget.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Biztonsági csoport típusa":::

1. Adja hozzá a katalógus felügyelt identitását ehhez a biztonsági csoporthoz a tagok kiválasztásával, majd a **Tagok hozzáadása** lehetőséggel.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="A katalógus felügyelt példányának felvétele a csoportba":::

1. Keresse meg a katalógust, és válassza ki.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Katalógus hozzáadása a kereséshez":::

1. Ekkor megjelenik egy sikeres értesítés, amely azt mutatja, hogy hozzá lett adva.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="A katalógus MSI-fájljának hozzáadása sikeres":::

## <a name="associate-the-security-group-with-power-bi"></a>A biztonsági csoport hozzárendelése a Power BI

1. Jelentkezzen be a [Power bi felügyeleti portálra](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). A funkcióhoz tartozó jelző hozzáfűzése:  `allowServicePrincipalsUseReadAdminAPIsUI=1` . Ez a jelző lehetővé teszi a biztonsági csoport hozzárendelését lehetővé tevő funkció használatát. Példa:

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > A bérlői beállítások lap megtekintéséhez Power BI rendszergazdának kell lennie.

1. A **fejlesztői beállítások** lehetőség kiválasztásával  >  **engedélyezheti az egyszerű szolgáltatásoknak az írásvédett Power bi API-k (előzetes verzió) használatát**.
1. Válassza az **egyes biztonsági csoportok** lehetőséget.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Kép: az egyszerű Power BI felügyeleti API-engedélyek beolvasásának engedélyezése a szolgáltatásoknak ":::

    > [!Caution]
    > Ha engedélyezi a létrehozott biztonsági csoportot (amelyet a saját adatkatalógus felügyelt identitása tagként használ) a csak olvasási jogosultsággal rendelkező Power BI felügyeleti API-k használatához, akkor azt is lehetővé teszi, hogy a rendszer a metaadatokat (például az irányítópultot, a jelentés nevét, a tulajdonosokat, a leírásokat stb.) a bérlő összes Power BI összetevője számára is hozzáférhessen. Miután behúzta a metaadatokat az Azure hatáskörébe, a hatáskörébe tartozó engedélyek, nem Power BI engedélyek, határozza meg, ki láthatja a metaadatokat.

    > [!Note]
    > A biztonsági csoportot eltávolíthatja a fejlesztői beállításokból, de a korábban kibontott metaadatokat a rendszer nem távolítja el a hatáskörébe tartozó fiókból. Ezt külön is törölheti.

## <a name="register-power-bi-and-set-up-a-scan"></a>Power BI regisztrálása és a vizsgálat beállítása

Most, hogy megadta a katalógus engedélyeit a Power BI bérlő felügyeleti API-hoz való kapcsolódáshoz, be kell állítania a vizsgálatot a katalógusban. Ehhez egy PowerShell-parancsfájlt kell konfigurálnia és futtatnia.

1. Töltse le és csomagolja ki az ADC PowerShell-parancsmagokat.
1. Konfigurálja a parancsfájlt úgy, hogy megadja a szkript tetején található hozzárendelések értékeit.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > A parancsok futtatásához közreműködőnek vagy tulajdonosnak kell lennie az előfizetésben.

1. Indítsa el a vizsgálatot a következő parancsfájl futtatásával:

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Power BI regisztrálása és vizsgálata

Az ajánlott hitelesítési módszer az MSI. Ha azonban egy olyan Power BI bérlőt szeretne beolvasni, amely egy másik Azure-bérlőn található, mint a katalógus, a delegált hitelesítést használja.

A delegált hitelesítéshez rendszergazdai felhasználói hitelesítő adatokkal, valamint Power BI rendszergazdai hitelesítő adatokkal kell rendelkeznie. Létre kell hoznia egy Azure-alkalmazást is, és meg kell adnia Power BI bérlő. ReadAll engedélyeket.

1. Keresse meg [Azure Portal](https://portal.azure.com) , és keressen az **alkalmazás regisztrációja** elemre.

1. **Alkalmazásregisztrációk** válassza az **+ új regisztráció** lehetőséget.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Az új Azure-alkalmazás regisztrációjának létrehozását bemutató kép":::

1. Adja meg az alkalmazás nevét.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="új alkalmazás regisztrálása":::

1. Az alkalmazás létrehozása után válassza az **API-engedélyek**, majd **a + engedély hozzáadása** lehetőséget.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Kép, amely bemutatja, hogyan adhat hozzá engedélyt az alkalmazáshoz":::

1. Válassza ki **Power bi szolgáltatást** a **kérelem API-engedélyein**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="A PBI szolgáltatás kijelölését bemutató kép":::

1. Válassza a **delegált engedélyek** és **bérlő. olvasás. összes** lehetőséget. Ezután válassza az **engedélyek hozzáadása** lehetőséget.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Az API-engedélyek igénylését bemutató kép":::

1. Adja meg a **rendszergazdai jóváhagyást**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="A rendszergazdai jóváhagyást megadó kép":::

1. Másolja az **alkalmazás (ügyfél) azonosítóját** és a **könyvtár (bérlő) azonosító** értékeit.  Ezeket az értékeket a vizsgálat beállításakor fogja használni.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Az ügyfél és a bérlői azonosítók másolását bemutató kép":::

1. A vizsgálat konfigurálása a PowerShellben. A parancsfájl kérni fogja a hitelesítő adatokat. A használni kívánt Azure-előfizetéshez legalább közreműködő szerepkörrel és a hatáskörébe tartozó adatforrás-rendszergazdai szerepkörrel kell rendelkeznie.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Futtassa a vizsgálatot.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Következő lépések

Az Azure-beli hatáskörébe való ismerkedéshez tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.

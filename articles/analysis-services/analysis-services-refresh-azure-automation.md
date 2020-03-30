---
title: Az Azure Analysis Services-modellek frissítése az Azure Automation segítségével | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan kódmodell frissítések et az Azure Analysis Services az Azure Automation használatával.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572353"
---
# <a name="refresh-with-azure-automation"></a>Frissítés az Azure Automationnel

Az Azure Automation és a PowerShell Runbookok használatával automatizált adatfrissítési műveleteket hajthat végre az Azure Analysis táblázatos modelljein.  

A cikkben szereplő példa a [PowerShell SqlServer modulokat](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)használja.

A minta PowerShell Runbook, amely bemutatja a modell frissítése a cikk későbbi részében található.  

## <a name="authentication"></a>Hitelesítés

Minden hívást hitelesíteni kell egy érvényes Azure Active Directory (OAuth 2) jogkivonattal.  Ebben a cikkben a példa egy egyszerű szolgáltatás (SPN) hitelesítéséhez az Azure Analysis Services.

Ha többet szeretne tudni az egyszerű szolgáltatás létrehozásáról, olvassa el az Egyszerű szolgáltatás létrehozása az Azure Portal használatával című [témakört.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A következő példa feltételezi, hogy az Azure Analysis Services tűzfal a le van tiltva. Ha a tűzfal engedélyezve van, akkor a kérelem kezdeményezőjének nyilvános IP-címét engedélyezési listán kell tartani a tűzfalon.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Telepítse az SqlServer-modulokat a PowerShell-galériából.

1. Az Azure Automation-fiókban kattintson a **Modulok**elemre, majd **a Tallózás gyűjteményben.**

2. A keresősávban keresse meg az **SqlServer**kifejezést.

    ![Keresési modulok](./media/analysis-services-refresh-azure-automation/1.png)

3. Válassza az SqlServer, majd **az Importálás**lehetőséget.
 
    ![Modul importálása](./media/analysis-services-refresh-azure-automation/2.png)

4. Kattintson az **OK** gombra.
 
### <a name="create-a-service-principal-spn"></a>Egyszerű szolgáltatás létrehozása

Az egyszerű szolgáltatás létrehozásáról az Egyszerű szolgáltatás létrehozása az Azure Portal használatával című [témakörben](../active-directory/develop/howto-create-service-principal-portal.md)olvashat.

### <a name="configure-permissions-in-azure-analysis-services"></a>Engedélyek konfigurálása az Azure Analysis Services szolgáltatásban
 
A létrehozott egyszerű szolgáltatásnak kiszolgálórendszergazdai engedélyekkel kell rendelkeznie a kiszolgálón. További információ: [Egyszerű szolgáltatás hozzáadása a kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md)című témakörben olvashat.

## <a name="design-the-azure-automation-runbook"></a>Az Azure Automation Runbook tervezése

1. Az Automation-fiókban hozzon létre egy **hitelesítő adatok erőforrást,** amely a szolgáltatásnév biztonságos tárolására szolgál.

    ![Hitelesítő adatok létrehozása](./media/analysis-services-refresh-azure-automation/6.png)

2. Adja meg a hitelesítő adatok adatait.  A **Felhasználónév**mezőbe írja be az **SPN ügyfélazonosítóját**a **Jelszó**mezőbe . **SPN Secret**

    ![Hitelesítő adatok létrehozása](./media/analysis-services-refresh-azure-automation/7.png)

3. Az automatizálási runbook importálása

    ![Runbook importálása](./media/analysis-services-refresh-azure-automation/8.png)

4. Keresse meg a **Refresh-Model.ps1** fájlt, adjon meg **egy nevet** és **egy leírást,** majd kattintson a **Létrehozás gombra.**

    ![Runbook importálása](./media/analysis-services-refresh-azure-automation/9.png)

5. A Runbook létrehozása után automatikusan szerkesztési módba lép.  Kattintson a **Publish** (Közzététel) elemre.

    ![Runbook közzététele](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > A korábban létrehozott hitelesítő adatokat a runbook a **Get-AutomationPSCredential** paranccsal olvassa be.  Ezt a parancsot ezután átad az **Invoke-ProcessASADatabase** PowerShell parancsnak a hitelesítés azure Analysis Services-be való végrehajtásához.

6. Tesztelje a runbookot a **Start**gombra kattintva.

    ![A Runbook indítása](./media/analysis-services-refresh-azure-automation/11.png)

7. Töltse ki a **DATABASENAME**, **ANALYSISSERVER**és **REFRESHTYPE** paramétereket, majd kattintson **az OK**gombra. A **WEBHOOKDATA** paraméter nem szükséges, ha a Runbook ot manuálisan futtatja.

    ![A Runbook indítása](./media/analysis-services-refresh-azure-automation/12.png)

Ha a Runbook végrehajtása sikeresen megtörtént, a következőhöz hasonló kimenetet fog kapni:

![Sikeres futtatás](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Önálló Azure Automation Runbook használata

A Runbook konfigurálható az Azure Analysis Services modell frissítésének ütemezett aktiválásához.

Ez a következőképpen konfigurálható:

1. Az Automation Runbookban kattintson az **Ütemezések gombra,** majd **az Ütemezés hozzáadása parancsra.**
 
    ![Ütemezés létrehozása](./media/analysis-services-refresh-azure-automation/14.png)

2. Kattintson **az Új** > **ütemezés ütemezése gombra,** majd töltse ki a részleteket.

    ![Ütemezés konfigurálása](./media/analysis-services-refresh-azure-automation/15.png)

3. Kattintson **a Létrehozás gombra.**

4. Töltse ki az ütemezés paramétereit. Ezek minden alkalommal használatosak lesznek, amikor a Runbook aktiválódik. A **WEBHOOKDATA** paramétert üresen kell hagyni, ha ütemezés szerint fut.

    ![Paraméterek konfigurálása](./media/analysis-services-refresh-azure-automation/16.png)

5. Kattintson az **OK** gombra.

## <a name="consume-with-data-factory"></a>Felhasználás az adatgyárral

A runbook az Azure Data Factory használatával először hozzon létre egy **Webhook** a runbook. A **Webhook** egy URL-címet biztosít, amely egy Azure Data Factory webes tevékenységen keresztül hívható.

> [!IMPORTANT]
> **Webhook**létrehozásához a Runbook állapotát közzé kell **tenni.**

1. Az Automation Runbookban kattintson a **Webhooks**, majd **a Webhook hozzáadása parancsra.**

   ![Webhook hozzáadása](./media/analysis-services-refresh-azure-automation/17.png)

2. Adjon nevet és lejárati nevet a Webhooknak.  A név csak azonosítja a Webhook belül az automation Runbook, nem képezi részét az URL-címet.

   >[!CAUTION]
   >Győződjön meg róla, hogy a varázsló bezárása előtt másolja az URL-címet, mivel a bezárás után nem tudja visszakapni.
    
   ![Webhook konfigurálása](./media/analysis-services-refresh-azure-automation/18.png)

    A webhook paraméterei üresek maradhatnak.  Az Azure Data Factory webes tevékenység konfigurálásakor a paraméterek et át lehet adni a webes hívás törzsébe.

3. A Data Factory ban konfiguráljon egy **webes tevékenységet**

### <a name="example"></a>Példa

   ![Példa webes tevékenységre](./media/analysis-services-refresh-azure-automation/19.png)

Az **URL-cím** a Webhookból létrehozott URL-cím.

A **test** egy JSON-dokumentum, amelynek a következő tulajdonságokat kell tartalmaznia:


|Tulajdonság  |Érték  |
|---------|---------|
|**AnalysisServicesAdatbázis**     |Az Azure Analysis Services adatbázisának neve <br/> Példa: AdventureWorksDB         |
|**AnalysisServicesServer**     |Az Azure Analysis Services kiszolgálóneve. <br/> Példa: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType (Adatbázis-frissítéstípusa)**     |A végrehajtandó frissítés típusa. <br/> Példa: Teljes         |

Példa JSON-törzsre:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Ezek a paraméterek a runbook PowerShell-parancsfájlban vannak definiálva.  A webes tevékenység végrehajtásakor a JSON-tartalom átadott WEBHOOKDATA.

Ez deszerializált és powershell-paraméterekként tárolódik, amelyeket ezután az Invoke-ProcesASDatabase PowerShell parancs használ.

![Deszerializált webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Hibrid feldolgozó használata az Azure Analysis Services szolgáltatással

Egy Azure virtuális gép statikus nyilvános IP-címet azure automation hibrid feldolgozóként használható.  Ez a nyilvános IP-cím ezután hozzáadható az Azure Analysis Services tűzfalához.

> [!IMPORTANT]
> Győződjön meg arról, hogy a virtuális gép nyilvános IP-címe statikusként van konfigurálva.
>
>Ha többet szeretne megtudni az Azure Automation hibrid feldolgozók konfigurálásáról, [olvassa el az erőforrások automatizálása az adatközpontban vagy a felhőben a hibrid runbook-feldolgozó használatával](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)című témakört.

A hibrid feldolgozó konfigurálása után hozzon létre egy webhookot a [Felhasználás adatgyárral](#consume-with-data-factory)című szakaszban leírtak szerint.  Az egyetlen különbség itt az, hogy válassza ki a **Futtatás** > **hibrid feldolgozó** n beállítás konfigurálásakor a Webhook.

Példa webhook hibrid feldolgozó:

![Példa hibrid feldolgozó webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>PowerShell-runbook minta

A következő kódrészlet egy példa arra, hogyan hajthatja végre az Azure Analysis Services modell frissítése egy PowerShell Runbook használatával.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>További lépések

[Minták](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)

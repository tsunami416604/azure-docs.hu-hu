---
title: Frissítse az Azure Analysis Services-minták az Azure Automation |} A Microsoft Docs
description: Ismerje meg, hogyan modell frissítések code az Azure Automation használatával.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: abbad97e29f60e5f135017cd43f9d30eba1805ca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142781"
---
# <a name="refresh-with-azure-automation"></a>Frissítés az Azure Automationnel

Az Azure Analysis táblázatos modellek az automatizált adatfrissítési műveletek elvégzése Azure Automation és PowerShell-Runbookok használatával.  

A példában ez a cikk a [PowerShell SqlServer-modulok](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

A cikk későbbi részében egy minta PowerShell-forgatókönyvet, amely bemutatja a modell frissítése van megadva.  

## <a name="authentication"></a>Hitelesítés

Összes hívás érvényes Azure Active Directory (OAuth 2) tokennel kell hitelesíteni.  Ebben a cikkben a példában egy egyszerű szolgáltatásnév (SPN) használatával hitelesíti az Azure Analysis Services.

Egyszerű szolgáltatás létrehozása kapcsolatos további információkért lásd:]

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az alábbi példa azt feltételezi, hogy az Azure Analysis Services tűzfala le van tiltva. Ha a tűzfal engedélyezve van, a kérés kezdeményezője nyilvános IP-címét kell engedélyezési listához hozzáadni kívánt a tűzfalon.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>PowerShell-galériából SqlServer-modulok telepítéséhez.

1. Az Azure Automation-fiókjában kattintson **modulok**, majd **Tallózás a katalógusban**.

2. A keresősávban keressen **SqlServer**.

    ![Modulok keresése](./media/analysis-services-refresh-azure-automation/1.png)

3. Válassza ki az SQL Server, majd kattintson a **importálás**.
 
    ![Modul importálása](./media/analysis-services-refresh-azure-automation/2.png)

4. Kattintson az **OK** gombra.
 
### <a name="create-a-service-principal-spn"></a>Szolgáltatásnév (SPN) létrehozása

Egyszerű szolgáltatás létrehozása kapcsolatos további információkért lásd: [egyszerű szolgáltatás létrehozása az Azure portal használatával](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Az Azure Analysis Services-engedélyek konfigurálása
 
Az egyszerű szolgáltatás létrehozása a kiszolgáló kiszolgáló-rendszergazdai engedélyekkel kell rendelkeznie. További tudnivalókért lásd: [szolgáltatásnév hozzáadása kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Az Azure Automation-Runbook tervezése

1. Az Automation-fiókot, hozzon létre egy **hitelesítő adatok** biztonságosan tárolni az egyszerű szolgáltatás használandó erőforrás.

    ![hitelesítő adat létrehozása](./media/analysis-services-refresh-azure-automation/6.png)

2. Adja meg, hogy a hitelesítő adatokat.  Az a **felhasználónév**, adja meg a **SPN ClientId**, az a **jelszó**, adja meg a **SPN titkos**.

    ![hitelesítő adat létrehozása](./media/analysis-services-refresh-azure-automation/7.png)

3. Az Automation-forgatókönyv importálása

    ![Runbook importálása](./media/analysis-services-refresh-azure-automation/8.png)

4. Keresse meg a **frissítés-Model.ps1** fájlt, adja meg egy **neve** és **leírás**, és kattintson a **létrehozás**.

    ![Runbook importálása](./media/analysis-services-refresh-azure-automation/9.png)

5. Amikor a Runbook létrejött, azt fogja automatikusan váltson szerkesztőmódra.  Kattintson a **Publish** (Közzététel) elemre.

    ![Runbook közzététele](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > A hitelesítő adatok erőforrás létrehozásának korábban a runbook által segítségével lekéri az **Get-AutomationPSCredential** parancsot.  Ez a parancs majd átadott a **Invoke-ProcessASADatabase** PowerShell-parancsot az Azure Analysis Services a hitelesítés végrehajtásához.

6. A runbook tesztelése kattintva **Start**.

    ![A Runbook indítása](./media/analysis-services-refresh-azure-automation/11.png)

7. Töltse ki a **DATABASENAME**, **ANALYSISSERVER**, és **REFRESHTYPE** paramétereket, és kattintson **OK**. A **WEBHOOKDATA** paraméter esetén nem szükséges manuálisan a Runbook futtatása.

    ![A Runbook indítása](./media/analysis-services-refresh-azure-automation/12.png)

A Runbook sikeresen végrehajtva, ha a következőhöz hasonló kimenetet fog kapni:

![Sikeres futtatás](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Egy önálló Azure Automation-Runbook használata

A Runbook beállítható úgy, hogy az Azure Analysis Services-modell frissítése ütemezett időközönként eseményindító.

A beállítás a következő:

1. Kattintson az Automation-forgatókönyv **ütemezések**, majd **ütemezés hozzáadása**.
 
    ![Ütemezés létrehozása](./media/analysis-services-refresh-azure-automation/14.png)

2. Kattintson a **ütemezés** > **új ütemezés létrehozása**, és adja meg az adatokat.

    ![Ütemezés beállítása](./media/analysis-services-refresh-azure-automation/15.png)

3. Kattintson a **Create** (Létrehozás) gombra.

4. Adja meg a paramétereket, az ütemezés számára. Ezeket fogja használni minden alkalommal, amikor elindítja a Runbookot. A **WEBHOOKDATA** paraméter üresen kell hagyni keresztül ütemezés futtatásakor.

    ![Paraméterek konfigurálása](./media/analysis-services-refresh-azure-automation/16.png)

5. Kattintson az **OK** gombra.

## <a name="consume-with-data-factory"></a>A Data Factory használata

A runbook használják az Azure Data Factory, először hozzon létre egy **Webhook** a runbookhoz. A **Webhook** egy URL-címet, amely egy Azure Data Factory webes tevékenység keresztül biztosít.

> [!IMPORTANT]
> Hozhat létre egy **Webhook**, a Runbook állapota lehet **közzétett**.

1. Az Automation-Runbook kattintson **Webhookok**, és kattintson a **Webhook hozzáadása**.

   ![Add Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. A Webhook adjon meg egy nevet és a egy lejárati.  A név csak azonosítja a Webhookot az Automation-forgatókönyv belül, azt az URL-cím nem alkotnak.

   >[!CAUTION]
   >Győződjön meg arról, mielőtt bezárná a varázslót, nem nyerheti vissza egyszer le van zárva, másolja az URL-címet.
    
   ![Configure Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    A webhook paramétereinek maradhat üresen.  Az Azure Data Factory webes tevékenység konfigurálásakor a paramétereket is kell megkérnie a webes hívás törzsét.

3. A Data Factory konfigurálása egy **webes tevékenység**

### <a name="example"></a>Példa

   ![Példa webes tevékenység](./media/analysis-services-refresh-azure-automation/19.png)

A **URL-cím** létrehozott a Webhook URL-címe.

A **törzs** JSON-dokumentumok, amelynek tartalmaznia kell a következő tulajdonságokkal:


|Tulajdonság  |Érték  |
|---------|---------|
|**AnalysisServicesDatabase**     |Az Azure Analysis Services-adatbázis neve <br/> Példa: AdventureWorksDB         |
|**AnalysisServicesServer**     |Az Azure Analysis Services-kiszolgáló neve. <br/> Például: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |A frissítés végrehajtásához típusát. <br/> Példa: Teljes         |

Példa JSON-törzse:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Ezeket a paramétereket a forgatókönyv PowerShell-parancsprogram vannak definiálva.  A webes tevékenység végrehajtásakor az átadott JSON-adattartalom WEBHOOKDATA.

Ez a deszerializálását és PowerShell-paramétereket, majd az Invoke-ProcesASDatabase PowerShell-paranccsal által használt tárolja.

![Deszerializált Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Az Azure Analysis Services-hibrid feldolgozók használata

Egy Azure virtuális gép statikus nyilvános IP-cím használható egy Azure Automation hibrid feldolgozó.  A nyilvános IP-cím az Azure Analysis Services tűzfala majd is hozzáadhatók.

> [!IMPORTANT]
> Győződjön meg arról, a virtuális gép nyilvános IP-címet a statikus van konfigurálva.
>
>Azure Automation hibrid feldolgozók konfigurálásával kapcsolatos további tudnivalókért lásd: [automatizálhatja az erőforrások az adatközpontban vagy a felhőben a hibrid Runbook-feldolgozó](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Ha egy hibrid feldolgozó már konfigurálva van, hozzon létre egy Webhookot a szakaszban leírt módon [felhasználás a Data Factory](#consume-with-data-factory).  Az egyetlen különbség, hogy válassza ki a **futtathatók** > **hibrid feldolgozó** lehetőséget a Webhook konfigurálása során.

Példa webhook hibrid feldolgozó használatával:

![Példa a hibrid feldolgozói Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Minta PowerShell-forgatókönyv

A következő kódrészletet a példa bemutatja, hogyan hajtsa végre a PowerShell-Runbook használatával az Azure Analysis Services a modellfrissítéshez.

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

[Példák](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)

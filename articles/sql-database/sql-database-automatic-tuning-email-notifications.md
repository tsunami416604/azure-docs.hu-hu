---
title: "Automatikus hangolása e-mail értesítések útmutató útmutató – az Azure SQL Database |} Microsoft Docs"
description: "Az Azure SQL-adatbázis SQL-lekérdezés elemzi, és automatikusan alkalmazkodik felhasználói munkaterhelés."
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/05/2018
ms.author: v-daljep
ms.openlocfilehash: a2799e45fbb54531289a89082f13e5ce0856c376
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="email-notifications-for-automatic-tuning"></a>E-mail értesítések automatikus hangolása

SQL-adatbázis hangolási javaslatok az Azure SQL Database által előállított [automatikus hangolása](sql-database-automatic-tuning.md). Ez a megoldás folyamatosan figyeli, és elemzi az SQL-adatbázisok biztosító munkaterhelések testreszabott hangolása minden egyes adatbázis indexlétrehozást, index törlésre és optimalizálási lekérdezés végrehajtási tervek kapcsolatos javaslatok.

SQL adatbázis automatikus hangolása javaslatok tekintheti meg a [Azure-portálon](sql-database-advisor-portal.md), a beolvasott [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) hívja, vagy használatával [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) és [ PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction) parancsok. Ez a cikk alapján automatikus hangolási javaslatok beolvasása egy PowerShell-parancsfájl használatával.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Értesítő e-mailek automatikus hangolási ajánlások automatizálásához

A következő megoldás automatizálja az automatikus hangolási javaslatokat tartalmazó értesítő e-mailek küldése. A megoldás leírt áll automatizálása a PowerShell-parancsfájl használatával hangolási javaslatok beolvasása az végrehajtásának [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), és az ütemezés automation e-mail kézbesítési feladat használ [Microsoft Flow ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Azure Automation-fiók létrehozása

Azure Automation használatához az első lépés egy automation-fiók létrehozása és konfigurálása az Azure-erőforrások használatához a PowerShell-parancsfájl végrehajtásának. Azure Automation és platformképességei kapcsolatos további információkért lásd: [Ismerkedés az Azure Automation szolgáltatásbeli](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Kövesse az alábbi lépéseket a a módszerrel kiválasztása és konfigurálása a piactérről Automation app Azure Automation-fiók létrehozásához:

- Jelentkezzen be az Azure-portálon
- Kattintson a "**+ hozzon létre egy erőforrást**" bal felső sarokban
- Keressen "**Automation**" (nyomja le az enter)
- Az automatizálási alkalmazás a keresési eredmények között kattintson

![Azure Automation szolgáltatásbeli hozzáadása](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Egyszer "Hozzon létre egy Automation-fiók" ablaktáblájában kattintson "**létrehozása**"
- A szükséges adatokat feltölteni: Adja meg az automation-fiók nevét, válassza ki az Azure-előfizetés-azonosító és az Azure a PowerShell-parancsprogram végrehajtása során használandó erőforrások
- Az a "**létrehozása Azure-beli futtató fiók**" lehetőséget, jelölje be **Igen** alapján mely PowerShell parancsfájl segítségével. Azure Automation futtató fiók típusú beállításához. Fióktípus kapcsolatos további információkért lásd: [futtató fiók](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Az automation-fiók létrehozása kössön kattintva **létrehozása**

> [!TIP]
> Pontosan megegyezik az Automation-alkalmazás létrehozásakor megadott jegyezze fel az Azure Automation-fiók neve, előfizetés-azonosító és erőforrások (például a másolás-beillesztés a Jegyzettömbbe). Ez az információ később van szüksége.
>

Ha több Azure-előfizetések, amelynek szeretné build azonos automatizálását, meg kell ismételni ezt a folyamatot a előfizetésekhez.

## <a name="update-azure-automation-modules"></a>Azure Automation-modul frissítéséhez

A PowerShell parancsfájl automatikus hangolása javaslat beolvasása [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Get-AzureRmResource) és [Get-AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction) parancsok mely Azure-modulok frissítése a 4-es vagy újabb verzió szükséges.

Kövesse az alábbi lépéseket Azure PowerShell-modulok frissítése:

- Lépjen be az Automation app ablaktáblán, és jelölje ki "**modulok**" a bal oldali menüben (görgessen lefelé, menüpont alatt megosztott erőforrások).
- A modulok ablaktáblájában kattintson a "**frissítés Azure modulok**" tetején, vagy várjon, amíg a "Azure modulok frissítve lett" üzenet jelenik meg. A folyamat eltarthat pár percig.

![Azure automation-modul frissítéséhez](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

AzureRM.Resources és AzureRM.Sql modulok kell lennie 4-es vagy újabb szükséges verziója.

## <a name="create-azure-automation-runbook"></a>Azure Automation-Runbook létrehozása

A következő lépés, ha egy Runbook az Azure Automationben, amelyben a PowerShell parancsfájl lekérésére javaslatok hangolása található.

Kövesse az alábbi lépéseket egy új Azure Automation-runbook létrehozása:

- Az előző lépésben létrehozott Azure Automation-fiók eléréséhez
- Egyszer az automatizálási fiók ablaktáblájában, kattintson a a "**Runbookok**" menüpont egy új Azure Automation-runbook létrehozása a PowerShell-parancsfájlt a bal oldalon található. Automatizálási runbookok létrehozásával kapcsolatos további tudnivalókért lásd: [új runbook létrehozásának](../automation/automation-creating-importing-runbook.md).
- Adja hozzá egy új runbookot, kattintson a a "**+ Hozzáadás runbook**" menüpont, és kattintson a a "**gyors létrehozása – hozzon létre egy új runbookot**".
- A Runbook panelen írja be a runbook nevét (jelen példában "**AutomaticTuningEmailAutomation**" használt), válassza ki a runbook **PowerShell** írási és olvasási leírása Ez a forgatókönyv mindössze egyetlen célra szorítkoznak ismertetik.
- Kattintson a **létrehozása** gombra egy új runbook létrehozásának befejezéséhez

![Azure automation-runbook hozzáadása](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Kövesse az alábbi lépéseket betölteni egy PowerShell-parancsfájlt a létrehozott runbook belül:

- Belül a "**PowerShell Runbook szerkesztése**"ablaktáblán válassza előbb"**RUNBOOKOK**" menü fán, és bontsa ki a nézet, amíg megjelenik a runbook neve (ebben a példában " **AutomaticTuningEmailAutomation**"). Válassza ki a runbookot.
- A "PowerShell Runbook szerkesztése" (1-es megkezdése) első sor másolja illessze be a következő PowerShell-parancsfájl kódot. A PowerShell parancsfájlpéldát – az első lépésekhez. Módosítsa a csomag a parancsfájlt az igényeinek.

A megadott PowerShell-parancsfájlokkal fejlécében, ki kell cserélni `<SUBSCRIPTION_ID_WITH_DATABASES>` az Azure-előfizetés-azonosítóval. Megtudhatja, hogyan lehet lekérni az Azure-előfizetése Azonosítóját, lásd: [beolvasása az Azure-előfizetés GUID](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/).

Több előfizetések esetén később is hozzáadhatja, vesszővel tagolt a parancsfájl fejlécében szereplő "$subscriptions" tulajdonságához.

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID 
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue 
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }                
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Kattintson a "**mentése**" gombra a parancsfájl mentése jobb felső sarokban. Ha elégedett a parancsfájlt, kattintson a "**közzététel**" gombra kattintva közzétenni a runbookot. 

A fő runbook ablaktáblán, ha szeretné, kattintson a a "**Start**" gombra kattintva **tesztelése** a parancsfájlt. Kattintson a "**kimeneti**" a parancsfájl végrehajtása eredményeinek megtekintése. A kimenet az e-mail tartalma lesz. Az alábbi képernyőfelvételen látható a kimenetét a parancsfájlból.

![A nézet automatikus hangolása javaslatok az Azure Automation szolgáltatásban futtatása](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Győződjön meg arról, a PowerShell-parancsfájlt az igényeinek megfelelően testre szabásával úgy, hogy a tartalmat.

A fenti lépéseket a PowerShell parancsfájl automatikus hangolási javaslatok beolvasása az Azure Automationben be van töltve. A következő lépés az automatizálását és az e-mail kézbesítési feladat ütemezése.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>A Microsoft Flow e-mail feladatok automatizálásához

A megoldás, mint az utolsó lépést, befejezéséhez hozzon létre egy automatizálási folyamat három műveletet (feladatok) álló Microsoft Flow: 

1. "**Azure Automation - feladat létrehozása**" – a PowerShell parancsfájl automatikus hangolása javaslatok az Azure Automation-forgatókönyv belül beolvasása végrehajtásához használatos
2. "**Azure Automation - feladat kimeneti Get**" – használatos kimeneti lekérése a végrehajtott PowerShell-parancsfájl
3. "**Office 365 Outlook – az e-mailek küldése**" – kimenő e-mailek küldéséhez használatos. E-mailek küldése ki az Office 365-fiókkal az egyéni, a folyamat létrehozása.

Microsoft Flow képességeivel kapcsolatos további tudnivalókért lásd: [Ismerkedés a Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Ez a lépés előfeltétele, hogy regisztráljon [Microsoft Flow](https://flow.microsoft.com) fiók és a bejelentkezéshez. Egyszer belül a megoldás lépések végrehajtásával állítson be egy **új folyamat**:

- Hozzáférés "**a adatfolyamok**" menüpont
- Belül a folyamatok, válassza a "**+ hozza létre az üres**" hivatkozásra a lap tetején
- Kattintson a hivatkozásra "**több száz összekötők és eseményindítók keresése**" a lap alján
- A a mezőben írja be "**ismétlődési**", és jelölje ki "**ütemezés - ismétlődési**" a keresési eredmények az e-mailek kézbesítését feladat futtatásának ütemezése.
- A gyakoriság mező ismétlődési ablaktáblában jelölje ki az ütemezési gyakorisága ebben az adatfolyamban hajtható végre, például a küldési automatikus e-mail minden perc, óra, nap, hét, stb.

A következő lépésre (létrehozása, get kimeneti, majd küldje el e-mailben) három feladatok hozzáadása az újonnan létrehozott ismétlődő folyamat. A folyamat ad hozzá a szükséges feladatok elvégzéséhez kövesse az alábbi lépéseket:

1. PowerShell parancsfájl hangolási javaslatok beolvasása végrehajtandó művelet létrehozása
- Jelölje ki "**+ új lépés**", utána pedig"**művelet hozzáadása**" ismétlődési folyamata ablaktáblájában
- A keresési mező típusban lévő "**automation**", és jelölje ki"**Azure Automation-feladat létrehozása**" a keresési eredmények
- A létrehozás feladat panelen a feladat tulajdonságainak konfigurálása. Ehhez a konfigurációhoz, szüksége lesz az Azure-előfizetés azonosítója, erőforráscsoport és az Automation-fiók részleteinek **korábban rögzített** , a **Automation-fiók panelen**. Ebben a szakaszban a rendelkezésre álló beállításokkal kapcsolatos további tudnivalókért lásd: [Azure Automation - feladat létrehozása](https://docs.microsoft.com/connectors/azureautomation/#Create_job).
- Ez a művelet létrehozásának kattintva "**mentés folyamata**"

2. Művelet kimeneti lekérése a végrehajtott PowerShell-parancsfájl létrehozása
- Jelölje ki "**+ új lépés**", utána pedig"**művelet hozzáadása**" ismétlődési folyamata ablaktáblájában
- A keresés bejegyezve típusa "**automation**", és jelölje ki"**Azure Automation-feladat kimeneti Get**" a keresési eredmények. Ebben a szakaszban a rendelkezésre álló beállításokkal kapcsolatos további tudnivalókért lásd: [Azure Automation-feladat kimeneti Get](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output).
- Feltöltése mezők kötelező (hasonlóan az előző feladat létrehozása) – feltöltése az Azure-előfizetés azonosítója, erőforráscsoport, és az Automation-fiók (ahogy az Automation-fiók panelen)
- Kattintson a mező "**Feladatazonosító**" számára a "**dinamikus tartalom**" menüre kattintva jelenik meg. Belül ebből a menüből válassza ki a beállítást "**Feladatazonosító**".
- Ez a művelet létrehozásának kattintva "**mentés folyamata**"

3. Az Office 365 integrálása révén e-mailt küldeni művelet létrehozása
- Jelölje ki "**+ új lépés**", utána pedig"**művelet hozzáadása**" ismétlődési folyamata ablaktáblájában
- A keresés bejegyezve típusa "**egy e-mailek küldése**", és jelölje ki"**Office 365 Outlook – az e-mailek küldése**" a keresési eredmények
- Az a "**való**" mezőben írja be az e-mail címet, amely kell az értesítő e-mail küldése
- Az a "**tulajdonos**" mező típusa az e-mail tárgyában csupa kisbetűvel, például "hangolási javaslatok automatikus e-mailes értesítés"
- Kattintson a mező "**törzs**" számára a "**dinamikus tartalom**" menüre kattintva jelenik meg. Az ebben a menüben belül a "**beolvasni a feladat kimenetére**", jelölje be"**tartalom**" 
- Ez a művelet létrehozásának kattintva "**mentés folyamata**"

> [!TIP]
> Automatikus e-mailek küldése különböző címzetteknek, hozzon létre külön adatfolyamok. A további adatfolyamok módosítsa a "To" mezőben a címzettek e-mail címét, és a "Tulajdonos" mező az e-mail tárgyát. Új runbookok létrehozása az Azure Automationben a testre szabott PowerShell-parancsfájlok (például az Azure előfizetés-azonosító módosítását) lehetővé teszi, hogy további testreszabási automatizált forgatókönyvek, például van például e külön megadott címzettek automatikus hangolása külön előfizetések javaslatok.
>

A fenti megjelenik az e-mail kézbesítési feladat munkafolyamat konfigurálásához szükséges lépéseket. A beépített három műveletek álló teljes folyamata az alábbi ábrán látható.

![A nézet automatikus hangolása e-mailek értesítések folyamata](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

A folyamat teszteléséhez kattintson a "**Futtatás most**" belül a folyamat ablak jobb felső sarokban.

Az automatizált feladatokkal, megjelenítő, küldött értesítő e-mailek sikeres futó statisztika a folyamat analytics ablaktáblán látható.

![Automatikus hangolási értesítő e-mailek folyamat fut](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

A folyamat analytics akkor hasznos, a figyelési feladat végrehajtások, sikeres, és ha szükséges.  Hibaelhárítás, esetén is előfordulhat, hogy gondoskodnia kell a PowerShell parancsfájl végrehajtási naplóban Azure Automation app keresztül érhető el.

Az automatikus e-mail a végső kimenetet a következő e-mail fogadása után kialakításához és futtatásához a megoldás hasonlít:

![Automatikus hangolási értesítő e-mailek kimenete minta](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

A PowerShell parancsfájl módosításával módosíthatja a kimeneti és az automatikus e-mailt az igényeinek megfelelően formázását.

Előfordulhat, hogy még jobban testre szabhatja a megoldás egy adott hangolási esemény, és több címzettnek több előfizetések vagy adatbázisok, attól függően, hogy az egyéni forgatókönyvek alapján értesítő e-mailek létrehozásához. 

## <a name="next-steps"></a>További lépések

- Ismerje meg, további on automatikus hangolása segíthetnek adatbázis teljesítményének javítása, lásd: [az Azure SQL-adatbázis automatikus hangolása](sql-database-automatic-tuning.md).
- Az Azure SQL Database a számítási feladatok kezelésére automatikus hangolása engedélyezéséről [engedélyezze az automatikus hangolással](sql-database-automatic-tuning-enable.md).
- Manuálisan tekintse át és az automatikus hangolással javaslatok alkalmazása [keresse meg és teljesítmény javaslatok alkalmazása](sql-database-advisor-portal.md).

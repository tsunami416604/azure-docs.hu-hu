---
title: Automatikus hangolás e-mail értesítések útmutató
description: Engedélyezze az e-mail értesítéseket az Azure SQL Database automatikus lekérdezéshangolásához.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1dbcf953ad5f70c6ddf2a73eef2ea712f1e1278c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632086"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-mail értesítések automatikus hangoláshoz

Az SQL Database hangolási javaslatait az Azure SQL Database [Automatikus hangolásgenerálja](sql-database-automatic-tuning.md)hozza létre. Ez a megoldás folyamatosan figyeli és elemzi az SQL-adatbázisok munkaterheléseit, és testreszabott hangolási javaslatokat nyújt az index létrehozásához, az indextörléséhez és a lekérdezésvégrehajtási tervek optimalizálásához kapcsolódó minden egyes adatbázishoz.

AZ SQL Database automatikus hangolási javaslatai megtekinthetők az [Azure Portalon,](sql-database-advisor-portal.md) [REST API-hívásokkal,](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) vagy [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) és [PowerShell-parancsokkal.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) Ez a cikk egy PowerShell-parancsfájl használatával automatikus hangolási javaslatok lekéréséhez alapul.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>E-mail értesítések automatizálása az automatikus hangolási javaslatokhoz

A következő megoldás automatizálja az automatikus hangolási javaslatokat tartalmazó e-mail értesítések küldését. A leírt megoldás a PowerShell-parancsfájl végrehajtásának automatizálása az [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)hangolási javaslatainak beolvasásához, valamint az e-mail kézbesítési feladat Ütemezésének automatizálása a Microsoft Flow [használatával.](https://flow.microsoft.com)

## <a name="create-azure-automation-account"></a>Azure Automation-fiók létrehozása

Az Azure Automation használatához az első lépés egy automatizálási fiók létrehozása és konfigurálása az Azure-erőforrásokkal a PowerShell-parancsfájl végrehajtásához. Ha többet szeretne megtudni az Azure Automationről és annak képességeiről, [olvassa el az Azure-automatizálás első lépéseit.](https://docs.microsoft.com/azure/automation/automation-offering-get-started)

Az Alábbi lépésekkel hozhatja létre az Azure Automation-fiókot az Automation alkalmazás Marketplace-ről történő kiválasztásának és konfigurálásának módszerével:

- Bejelentkezés az Azure-portálra
- Kattintson a "**+ Erőforrás létrehozása**" gombra a bal felső sarokban
- Keresés az "**Automation**" kifejezésre (nyomja meg az Enter billentyűt)
- Kattintson az Automation alkalmazásra a keresési eredmények között

![Azure-automatizálás hozzáadása](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Miután belépett az "Automatizálási fiók létrehozása" ablaktáblába, kattintson a "**Create**"
- A szükséges adatok feltöltése: adja meg ennek az automatizálási fióknak a nevét, válassza ki az Azure-előfizetés-azonosítóját és a PowerShell-parancsfájl-végrehajtáshoz használandó Azure-erőforrásokat.
- A "**Create Azure Run As fiók**" beállítás, válassza az **Igen** lehetőséget a PowerShell-parancsfájl futtatásához az Azure Automation használatával típusú fiók típusának konfigurálásához. Ha többet szeretne megtudni a fióktípusokról, olvassa el a [Futtatás másként fiók](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Az automatizálási fiók létrehozásának befejezése a **Létrehozás** gombra kattintva

> [!TIP]
> Rögzítse az Azure Automation-fiók nevét, az előfizetés-azonosítót és az erőforrásokat (például a jegyzettömbbe való másolást) pontosan úgy, ahogy azt az Automation alkalmazás létrehozása során beírták. Erre az információra később van szüksége.
>

Ha több Azure-előfizetéssel is rendelkezik, amelyekhez ugyanazt az automatizálást szeretné készíteni, meg kell ismételnie ezt a folyamatot a többi előfizetéséhez.

## <a name="update-azure-automation-modules"></a>Az Azure Automation-modulok frissítése

Az automatikus hangolási javaslat lekéréséhez a PowerShell-parancsfájl [get-azresource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) és [get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) parancsokat használ, amelyekhez az Azure Module 4-es és újabb verziója szükséges.

- Abban az esetben, ha az Azure-modulokfrissítésre van szükség, lásd: [Az modul támogatása az Azure Automationben.](../automation/az-modules.md)

## <a name="create-azure-automation-runbook"></a>Azure Automation Runbook létrehozása

A következő lépés egy Runbook létrehozása az Azure Automationben, amelyben a PowerShell-parancsfájl a hangolási javaslatok lekéréséhez található.

Új Azure Automation-runbook létrehozásához kövesse az alábbi lépéseket:

- Az előző lépésben létrehozott Azure Automation-fiók elérése
- Miután az automation-fiók ablaktáblán, kattintson a "**Runbookok**" menüpont a bal oldalon, hogy hozzon létre egy új Azure Automation runbook a PowerShell-parancsfájlt. Az automatizálási runbookok létrehozásáról az [Új runbook létrehozása című](../automation/manage-runbooks.md#creating-a-runbook)témakörben olvashat bővebben.
- Új runbook hozzáadásához kattintson a "**+Runbook hozzáadása**" menüre, majd kattintson a "**Gyors létrehozás – Új runbook létrehozása " parancsra.**
- A Runbook pane, írja be a nevét a runbook (ebben a példában használt "**AutomaticTuningEmailAutomation**" használja), válassza ki a runbook típusát **PowerShellként,** és írja le a runbook leírására célját.
- Kattintson a **Létrehozás** gombra az új runbook létrehozásának befejezéséhez

![Azure-automatizálási runbook hozzáadása](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Az alábbi lépésekkel tölthet be egy PowerShell-parancsfájlt a létrehozott runbookon belül:

- A "**PowerShell Runbook szerkesztése**" ablaktáblán belül válassza a "**RUNBOOKS**" lehetőséget a menüfán, és bontsa ki a nézetet, amíg meg nem jelenik a runbook neve (ebben a példában "**AutomaticTuningEmailAutomation**"). Válassza ki ezt a runbookot.
- A "PowerShell Runbook szerkesztése" első sorában (az 1-es számmal kezdve), másolja be a következő PowerShell-parancsfájlkódot. Ez a PowerShell-parancsfájl a kezdéshez is biztosított. Módosítsa a parancsfájlt az igényeinek megfelelően.

A megadott PowerShell-parancsfájl fejlécében le `<SUBSCRIPTION_ID_WITH_DATABASES>` kell cserélnie az Azure-előfizetés-azonosítóját. Az Azure-előfizetés-azonosító beolvasásáról az [Azure-előfizetés GUID-jának beszerzése.](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/)

Több előfizetés esetén hozzáadhatja őket a parancsfájl fejlécében lévő "$subscriptions" tulajdonsághoz vesszővel körülhatároltként.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

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
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
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

A parancsfájl mentéséhez kattintson a jobb felső sarokban található "**Mentés**" gombra. Ha elégedett a parancsfájllal, kattintson a "**Közzététel**" gombra a runbook közzétételéhez.

A fő runbook ablaktáblán a **"Start**" gombra kattintva **tesztelheti** a parancsfájlt. Kattintson a "**Kimenet**" a végrehajtott parancsfájl eredményeinek megtekintéséhez. Ez a kimenet lesz az e-mail tartalma. A minta kimeneta a szkript látható a következő screenshot.

![Nézet automatikus hangolási javaslatainak futtatása az Azure Automation segítségével](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Győződjön meg arról, hogy a powershell-parancsfájl testreszabásával a tartalom igényeinek.

A fenti lépésekkel a PowerShell-parancsfájl automatikus hangolási javaslatok betöltése az Azure Automationben. A következő lépés az e-mail kézbesítési feladat automatizálása és ütemezése.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Az e-mail feladatok automatizálása a Microsoft Flow segítségével

A megoldás befejezéséhez, mint az utolsó lépés, hozzon létre egy automatizálási folyamat a Microsoft Flow három műveletből (feladatok):

1. "**Azure Automation – Létrehozási feladat**" – a PowerShell-parancsfájl végrehajtásához használható az Automatikus hangolási javaslatok lekéréséhez az Azure Automation runbookon belül
2. "**Azure Automation - Get job output**" – a végrehajtott PowerShell-parancsfájl kimenetének lekéréséhez
3. "**Office 365 Outlook – E-mail küldése**" – e-mailek küldésére szolgál. Az e-mailek küldése a folyamatot létrehozó személy Office 365-fiókjával kerül kiküldésre.

A Microsoft Flow lehetőségeiről az Első lépések a Microsoft Flow szolgáltatással kapcsolatban ( Első lépések a [Microsoft Flow - témakörben)](https://docs.microsoft.com/flow/getting-started)olvashat bővebben.

Ennek a lépésnek az előfeltétele, hogy regisztráljon a [Microsoft Flow-fiókra,](https://flow.microsoft.com) és jelentkezzen be. Miután bejutott a megoldásba, kövesse az alábbi lépéseket egy **új folyamat beállításához:**

- Hozzáférés "**Saját folyamatok**" menüelem
- A Saját folyamatok on belül válassza a "**+Create from blank**" hivatkozást az oldal tetején
- Kattintson a **"Keresés több száz összekötők és eseményindítók"** az oldal alján
- A keresési eredmények között válassza az "**Ismétlődés**" típusú keresőmezőt, és válassza a "**Ütemezés – Ismétlődés**" lehetőséget a keresési eredmények között az e-mail kézbesítési feladat futtatásának ütemezéséhez.
- A Gyakoriság mező Ismétlődés ablaktáblájában válassza ki a végrehajtandó folyamat ütemezési gyakoriságát, például automatikus e-mail küldése minden percben, órában, napon, héten stb.

A következő lépés három feladat hozzáadása (létrehozás, kimenet beküldése és e-mail küldése) az újonnan létrehozott ismétlődő folyamathoz. A szükséges feladatok munkafolyamathoz való hozzáadásához hajtsa végre az alábbi lépéseket:

1. Hozzon létre műveletet a PowerShell-parancsfájl végrehajtásához a hangolási javaslatok lekéréséhez

   - Válassza a "**+Új lépés**" lehetőséget, majd a "**Művelet hozzáadása**" lehetőséget az Ismétlődési folyamat ablaktáblán
   - A keresési eredmények közül válassza az "**automation**" típusú keresőmezőben, és válassza az "**Azure Automation – Állás létrehozása**" lehetőséget a keresési eredmények közül
   - A Feladat létrehozása ablaktáblán konfigurálja a feladat tulajdonságait. Ehhez a konfigurációhoz szüksége lesz az Azure-előfizetés-azonosító, az Erőforráscsoport és az Automation-fiók részleteit, **amelyet korábban** rögzített az **Automation-fiók ablaktáblán.** Ha többet szeretne tudni az ebben a szakaszban elérhető lehetőségekről, olvassa el az Azure Automation – Create Job című [témakört.](https://docs.microsoft.com/connectors/azureautomation/#create-job)
   - A művelet létrehozásának befejezése a **"Folyamat mentése"** gombra kattintva

2. Művelet létrehozása a kimenet nek a végrehajtott PowerShell-parancsfájlból történő lekéréséhez

   - Válassza a "**+Új lépés**" lehetőséget, majd a "**Művelet hozzáadása**" lehetőséget az Ismétlődési folyamat ablaktáblán
   - A keresési iktatott típusú "**automatizálás**" és válassza a "**Azure Automation – Get job output**" a keresési eredmények között. Ha többet szeretne megtudni az ebben a szakaszban elérhető lehetőségekről, olvassa el az Azure Automation – Get job output című [témakört.](https://docs.microsoft.com/connectors/azureautomation/#get-job-output)
   - A szükséges mezők feltöltése (hasonlóan az előző feladat létrehozásához) – az Azure-előfizetés-azonosító, az Erőforráscsoport és az Automation-fiók feltöltése (az Automation-fiók ablaktáblán megadott módon)
   - Kattintson a "**Feladatazonosító**" mezőre a "**Dinamikus tartalom**" menümegjelenítéséhez. Ebből a menüből válassza a "**Feladatazonosító**" lehetőséget.
   - A művelet létrehozásának befejezése a **"Folyamat mentése"** gombra kattintva

3. Az Office 365-integrációval e-mailek küldésére irányuló művelet létrehozása

   - Válassza a "**+Új lépés**" lehetőséget, majd a "**Művelet hozzáadása**" lehetőséget az Ismétlődési folyamat ablaktáblán
   - A keresésikre iktatott **"küldjön egy e-mailt"** és válassza az "**Office 365 Outlook – E-mail küldése**" lehetőséget a keresési eredmények között
   - A **"To"** mezőben írja be azt az e-mail címet, amelyre az értesítő e-mailt el kell küldenie.
   - A **"Tárgy**" mező típusa az e-mail tárgyában, például "Automatikus hangolási javaslatok e-mail értesítés"
   - Kattintson a **"Body**" mezőre a "**Dinamikus tartalom**" menü megjelenítéséhez. Ebből a menüből a "Get job output "**(Feladat kimenetének bekerülése)** területen válassza a " Content "**(Tartalom)** lehetőséget.
   - A művelet létrehozásának befejezése a **"Folyamat mentése"** gombra kattintva

> [!TIP]
> Ha automatikus e-maileket szeretne küldeni a különböző címzetteknek, hozzon létre külön folyamatokat. Ezekben a további folyamatokban módosítsa a címzett e-mail címét a "Címzett" mezőben, és az e-mail tárgysorát a "Tárgy" mezőben. Új runbookok létrehozása az Azure Automationben testreszabott PowerShell-parancsfájlokkal (például az Azure-előfizetés-azonosító módosításával) lehetővé teszi az automatikus forgatókönyvek további testreszabását, például külön címzettek e-mailben való küldése a külön-külön előfizetések automatikus hangolási javaslatairól.
>

A fenti az e-mail kézbesítési feladat munkafolyamatának konfigurálásához szükséges lépéseket zárja le. A három műveletből álló teljes folyamat az alábbi képen látható.

![Az e-mail értesítések automatikus hangolásának megtekintése](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

A folyamat teszteléséhez kattintson a **"Futtatás most"** gombra a jobb felső sarokban a folyamatablakon belül.

Az automatikus feladatok futtatásának statisztikái, amelyek az elküldött e-mail értesítések sikeresek, a Flow analytics ablaktáblán láthatók.

![Folyamat futtatása az e-mailek automatikus hangolása esetén](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

A Flow-elemzés hasznos a feladat-végrehajtások sikeresstkövetéséhez, és ha szükséges a hibaelhárításhoz.  Hibaelhárítás esetén is érdemes megvizsgálni a PowerShell-parancsfájl végrehajtási napló érhető el az Azure Automation alkalmazáson keresztül.

Az automatikus e-mail végső kimenete a következő e-mailhez hasonlóan néz ki, amelyet a megoldás létrehozása és futtatása után kapott:

![E-mail-kimenet mintaa az e-mail értesítések automatikus hangolásából](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

A PowerShell-parancsfájl módosításával módosíthatja az automatikus e-mail kimenetét és formázását az igényeinek megfelelően.

További testreszabása a megoldás, hogy hozzon létre e-mail értesítések alapján egy adott hangolási esemény, és több címzett, több előfizetések vagy adatbázisok, az egyéni forgatókönyvek.

## <a name="next-steps"></a>További lépések

- További információ arról, hogy az automatikus hangolás hogyan segíthet az adatbázis teljesítményének javításában, olvassa el az [Automatikus hangolás az Azure SQL Database-ben című témakört.](sql-database-automatic-tuning.md)
- Ha engedélyezni szeretné az automatikus hangolást az Azure SQL Database-ben a számítási feladatok kezeléséhez, olvassa el az [Automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md)című témakört.
- Az Automatikus hangolási javaslatok manuális áttekintéséhez és alkalmazásához olvassa el a Teljesítményre vonatkozó javaslatok keresése és alkalmazása című [témakört.](sql-database-advisor-portal.md)

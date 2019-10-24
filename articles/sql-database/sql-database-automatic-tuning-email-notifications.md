---
title: E-mail-értesítések automatikus finomhangolása útmutató – Azure SQL Database | Microsoft Docs
description: E-mail-értesítések engedélyezése Azure SQL Database automatikus lekérdezés-hangoláshoz.
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
ms.openlocfilehash: df9390c00c34fce82de8cc17efb5cc3bce2e4e3d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569433"
---
# <a name="email-notifications-for-automatic-tuning"></a>Automatikus hangolással kapcsolatos e-mail-értesítések

A SQL Database hangolási javaslatok Azure SQL Database [automatikus hangolással](sql-database-automatic-tuning.md)jönnek létre. Ez a megoldás folyamatosan figyeli és elemzi az SQL-adatbázisok munkaterheléseit, amelyek testreszabott hangolási javaslatokat biztosítanak minden egyes adatbázishoz, amely az indexek létrehozásával, az indexelés törlésével és a lekérdezés-végrehajtási tervek optimalizálásával kapcsolatos.

SQL Database Automatikus hangolási javaslatok megtekinthetők a [Azure Portalban](sql-database-advisor-portal.md), [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) hívásokkal, illetve a [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) és a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) -parancsok használatával. Ez a cikk az Automatikus hangolási javaslatok beolvasására szolgáló PowerShell-parancsfájl használatával készült.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>E-mail-értesítések automatizálása Automatikus hangolási javaslatok esetén

Az alábbi megoldás automatizálja az Automatikus hangolási javaslatokat tartalmazó e-mail-értesítések küldését. A leírt megoldás egy PowerShell-parancsfájl végrehajtásának automatizálása a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)használatával történő hangolási javaslatok beolvasásához és az e-mailek kézbesítési feladatainak [Microsoft flow](https://flow.microsoft.com)használatával történő automatizálásához.

## <a name="create-azure-automation-account"></a>Azure Automation fiók létrehozása

A Azure Automation használatához az első lépés egy Automation-fiók létrehozása és konfigurálása az Azure-erőforrásokkal a PowerShell-parancsfájl végrehajtásához. Ha többet szeretne megtudni a Azure Automation és képességeiről, tekintse meg a [Bevezetés az Azure Automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started)használatába című témakört.

A következő lépésekkel hozhat létre Azure Automation fiókot az Automation-alkalmazás piactéren való kiválasztásának és konfigurálásának módszerével:

- Bejelentkezés a Azure Portalba
- Kattintson a " **+ erőforrás létrehozása**" elemre a bal felső sarokban
- Keressen rá az "**Automation**" kifejezésre (nyomja le az ENTER billentyűt)
- Kattintson az Automation alkalmazásra a keresési eredmények között

![Azure Automation hozzáadása](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Az "Automation-fiók létrehozása" panelen kattintson a "**Létrehozás**" gombra.
- Töltse fel a szükséges információkat: adja meg az Automation-fiók nevét, válassza ki a PowerShell-parancsfájl végrehajtásához használni kívánt Azure-előfizetési azonosítót és Azure-erőforrásokat.
- Az "Azure-beli**futtató fiók létrehozása**" beállításnál válassza az **Igen** lehetőséget a fiók típusának konfigurálásához, amely alatt a PowerShell-szkript fut Azure Automation segítségével. További információ a fiókok típusairól: [futtató fiók](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Az Automation-fiók létrehozásának befejezéséhez kattintson a **Létrehozás** gombra.

> [!TIP]
> Rögzítse a Azure Automation fiók nevét, az előfizetés AZONOSÍTÓját és az erőforrásokat (például másolás-beillesztés egy Jegyzettömbbe) pontosan az Automation-alkalmazás létrehozásakor megadott módon. Ezt az információt később kell megadnia.
>

Ha több Azure-előfizetéssel is rendelkezik, amelyek esetében ugyanazt az automatizálást szeretné felépíteni, akkor a többi előfizetéshez meg kell ismételnie ezt a folyamatot.

## <a name="update-azure-automation-modules"></a>Azure Automation modulok frissítése

Az Automatikus hangolási javaslat beolvasására szolgáló PowerShell-szkript a [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) és a [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) parancsokat használja, amelyekhez szükség van az Azure-modul 4-es vagy újabb verziójára.

- Ha az Azure-modulok frissítése szükséges, tekintse meg az [az modul-támogatás Azure Automationban](../automation/az-modules.md)című témakört.

## <a name="create-azure-automation-runbook"></a>Azure Automation Runbook létrehozása

A következő lépés egy olyan Runbook létrehozása Azure Automation belül, amelyben a hangolási javaslatok lekérésére szolgáló PowerShell-szkript található.

Új Azure Automation runbook létrehozásához kövesse az alábbi lépéseket:

- Hozzáférés az előző lépésben létrehozott Azure Automation fiókhoz
- Az Automation-fiók panelen kattintson a bal oldalon található "**runbookok**" menüelemre, és hozzon létre egy új Azure Automation Runbook a PowerShell-parancsfájllal. További információ az Automation-runbookok létrehozásáról: [új Runbook létrehozása](../automation/manage-runbooks.md#create-a-runbook).
- Új runbook hozzáadásához kattintson a " **+ Runbook hozzáadása**" menüpontra, majd kattintson a "**gyors létrehozás – új runbook létrehozása**" lehetőségre.
- A Runbook panelen írja be a Runbook nevét (a jelen példában a "**AutomaticTuningEmailAutomation**" kifejezést használja), válassza ki a Runbook típusát powershellként, és írja le a Runbook leírását, hogy leírja a célját.
- Kattintson a **Létrehozás** gombra egy új runbook létrehozásának befejezéséhez

![Azure Automation-runbook hozzáadása](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Kövesse az alábbi lépéseket egy PowerShell-szkript betöltéséhez a létrehozott runbook:

- A "**PowerShell-Runbook szerkesztése**" ablaktáblán a menüsávon válassza a "**runbookok**" elemet, és bontsa ki a nézetet, amíg meg nem jelenik a Runbook neve (ebben a példában "**AutomaticTuningEmailAutomation**"). Válassza ki ezt a runbook.
- A "PowerShell-Runbook szerkesztése" első sorában (az 1. számmal kezdődően) másolja be a következő PowerShell-szkript kódját. Ezt a PowerShell-szkriptet a következő módon biztosítjuk: a kezdéshez. Módosítsa a szkriptet az igényeinek megfelelően.

A megadott PowerShell-parancsfájl fejlécében le kell cserélnie `<SUBSCRIPTION_ID_WITH_DATABASES>` az Azure-előfizetés azonosítóját. Az Azure-előfizetés AZONOSÍTÓjának beszerzésével kapcsolatos további információkért lásd: [Azure-előfizetés GUID](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/)-azonosítójának beolvasása.

Több előfizetés esetén felveheti azokat vesszővel elválasztva a parancsfájl fejlécében lévő "$subscriptions" tulajdonságra.

```powershell
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

Kattintson a jobb felső sarokban található**Save (Mentés**) gombra a parancsfájl mentéséhez. Ha elégedett a parancsfájllal, kattintson a "**Közzététel**" gombra a runbook közzétételéhez.

A fő runbook panelen a "**Start**" gombra kattintva **ellenőrizheti a** parancsfájlt. A "**kimenet**" elemre kattintva megtekintheti a végrehajtott parancsfájl eredményeit. Ez a kimenet lesz az e-mail tartalma. A szkript mintájának kimenete a következő képernyőképen látható.

![Automatikus hangolási javaslatok megtekintése a Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Győződjön meg arról, hogy a PowerShell-szkript igényeinek megfelelően testreszabja a tartalmat.

A fenti lépésekkel az Automatikus hangolási javaslatok beolvasására szolgáló PowerShell-szkript betöltődik Azure Automationba. A következő lépés az e-mail kézbesítési feladatok automatizálása és ütemezve.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Az e-mail-feladatok automatizálása Microsoft Flow

A megoldás befejezéséhez az utolsó lépésként hozzon létre egy automatizálási folyamatot Microsoft Flow a három műveletből (feladatok) álló folyamatból:

1. "**Azure Automation – feladatok létrehozása**" – a PowerShell-szkript végrehajtásához használható az Automatikus hangolási javaslatok beolvasásához a Azure Automation runbook
2. "**Azure Automation – a feladatok kimenetének**beolvasása" – a végrehajtott PowerShell-parancsfájl kimenetének lekéréséhez használatos
3. "**Office 365 Outlook – E-mail küldése**" – e-mailek küldésére szolgál. Az e-maileket a folyamat létrehozásakor az Office 365-fiókkal küldi el a rendszer.

Microsoft Flow képességekkel kapcsolatos további tudnivalókért tekintse meg a [Microsoft flow első lépéseivel foglalkozó](https://docs.microsoft.com/flow/getting-started)témakört.

Ennek a lépésnek az előfeltétele, hogy regisztráljon a [Microsoft flow](https://flow.microsoft.com) fiókra, és jelentkezzen be. A megoldáson belül a következő lépésekkel állíthatja be az **új folyamatot**:

- Hozzáférés**a "saját folyamatok**" menüponthoz
- A saját folyamatok részen válassza az oldal tetején található " **+ Létrehozás üresből**" hivatkozást.
- Kattintson a lap alján található "**több száz összekötő és triggerek keresése**" hivatkozásra.
- A keresőmezőbe írja be az "**Ismétlődés**" kifejezést, majd a keresési eredmények közül válassza az "**ütemterv-ismétlődés**" lehetőséget az e-mail kézbesítési feladatok futtatásának ütemezése érdekében.
- A gyakoriság mező ismétlődés ablaktábláján válassza ki a folyamat végrehajtásának ütemezési gyakoriságát, például az automatikus e-mailek küldését percenként, óránként, naponta, hetente stb.

A következő lépés a három feladat hozzáadása (létrehozás, kimenet beolvasása és e-mailek küldése) az újonnan létrehozott ismétlődő folyamathoz. A szükséges feladatok a folyamathoz való hozzáadásához kövesse az alábbi lépéseket:

1. Művelet létrehozása a hangolási javaslatok beolvasására szolgáló PowerShell-parancsfájl végrehajtásához

   - Válassza az " **+ új lépés**" elemet, majd a "**művelet hozzáadása**" lehetőséget az ismétlődési folyamat ablaktáblán.
   - A keresőmezőbe írja be az "**Automation**" kifejezést, és válassza a "**Azure Automation – létrehozási feladatok**" elemet a keresési eredmények közül.
   - A feladatok létrehozása panelen konfigurálja a feladatok tulajdonságait. Ehhez a konfigurációhoz a korábban az **Automation-fiók panelen** **rögzített** Azure-előfizetési azonosító, erőforráscsoport és Automation-fiók adataira lesz szüksége. Ha többet szeretne megtudni az ebben a szakaszban elérhető beállításokról, olvassa el a [Azure Automation-feladatok létrehozása](https://docs.microsoft.com/connectors/azureautomation/#create-job)című témakört.
   - A művelet létrehozásához kattintson a "**folyamat mentése**" gombra.

2. Művelet létrehozása a végrehajtott PowerShell-parancsfájl kimenetének lekéréséhez

   - Válassza az " **+ új lépés**" elemet, majd a "**művelet hozzáadása**" lehetőséget az ismétlődési folyamat ablaktáblán.
   - A Keresés az "**Automation**" kifejezésre, és válassza a "**Azure Automation – a feladatok kimenetének**beolvasása" lehetőséget a keresési eredmények közül. További információ az ebben a szakaszban elérhető beállításokról: [Azure Automation – feladatok kimenetének](https://docs.microsoft.com/connectors/azureautomation/#get-job-output)beolvasása.
   - A szükséges mezők feltöltése (az előző feladathoz hasonlóan) – az Azure-előfizetési azonosító, erőforráscsoport és Automation-fiók feltöltése (az Automation-fiók panelen megadott módon)
   - Kattintson a "**feladatok azonosítója**" mezőre a "**dinamikus tartalom**" menü megjelenítéséhez. A menüben válassza a "**Job ID**" lehetőséget.
   - A művelet létrehozásához kattintson a "**folyamat mentése**" gombra.

3. Művelet létrehozása e-mailek küldéséhez az Office 365-integráció használatával

   - Válassza az " **+ új lépés**" elemet, majd a "**művelet hozzáadása**" lehetőséget az ismétlődési folyamat ablaktáblán.
   - Az "**e-mail küldése**" keresési típusban válassza az "**Office 365 Outlook – e-mail küldése**" lehetőséget a keresési eredmények közül.
   - A "**to**" mezőbe írja be az e-mail címet, amelyre el kell küldenie az értesítő e-mailt
   - A "**Tárgy**" mezőbe írja be az e-mail-cím tárgyát, például: "Automatikus hangolási javaslatok e-mail értesítés"
   - Kattintson a "**törzs**" mezőre a "**dinamikus tartalom**" menü megjelenítéséhez. A menüben a "**feladatok kimenetének**lekérése" területen válassza a "**tartalom**" lehetőséget.
   - A művelet létrehozásához kattintson a "**folyamat mentése**" gombra.

> [!TIP]
> Ha automatizált e-maileket szeretne küldeni a különböző címzetteknek, hozzon létre külön folyamatokat. Ezekben a további folyamatokban módosítsa a címzett e-mail-címét a "címzett" mezőbe, az e-mail tárgyát pedig a "subject" (tárgy) mezőbe. A testreszabott PowerShell-parancsfájlokkal (például az Azure-előfizetés AZONOSÍTÓjának módosításával) rendelkező Azure Automation új runbookok létrehozása lehetővé teszi az automatizált forgatókönyvek további testreszabását, például a különálló címzettek automatikus hangolással történő elküldését. javaslatok a különböző előfizetésekhez.
>

A fenti lépések az e-mail kézbesítési feladatok munkafolyamatának konfigurálásához szükségesek. A három létrehozott műveletből álló teljes folyamat az alábbi képen látható.

![E-mail-értesítések automatikus finomhangolásának megtekintése](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

A folyamat teszteléséhez kattintson a jobb felső sarokban található "**Futtatás most**" elemre a folyamat ablaktáblán.

Az automatizált feladatok futtatásának statisztikája, amely az e-mail-értesítések sikeres elküldését mutatja be, a flow Analytics panelről is megtekinthető.

![Az e-mailek automatikus hangolására szolgáló folyamat futtatása](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

A flow Analytics hasznos a feladatok végrehajtásának sikerességének figyeléséhez, és ha szükséges a hibaelhárításhoz.  Hibaelhárítás esetén érdemes megvizsgálni a PowerShell-parancsfájl futtatási naplóját Azure Automation alkalmazáson keresztül is.

Az automatizált e-mailek végső kimenete a megoldás kiépítése és futtatása után a következő e-mailekhez hasonlóan néz ki:

![Minta e-mail-kimenet Automatikus hangolási e-mail-értesítésekből](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

A PowerShell-szkript módosításával beállíthatja az automatikus e-mailek kimenetét és formázását az igényeinek megfelelően.

Előfordulhat, hogy a megoldás egy adott hangolási eseményen, illetve több címzettnél, több előfizetéshez vagy adatbázishoz való kiépítéséhez az egyéni forgatókönyvek függvényében tovább testreszabja a megoldást.

## <a name="next-steps"></a>További lépések

- További információ arról, hogy az automatikus hangolás hogyan segít az adatbázisok teljesítményének javításában: [Azure SQL Database automatikus finomhangolása](sql-database-automatic-tuning.md).
- A számítási feladatok kezeléséhez Azure SQL Database automatikus hangolásának engedélyezéséhez tekintse meg az [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md)című témakört.
- Az Automatikus hangolási javaslatok manuális áttekintéséhez és alkalmazásához tekintse [meg a teljesítményre vonatkozó javaslatok keresése és alkalmazása](sql-database-advisor-portal.md)című témakört.

---
title: Automatikus hangolási e-mail-értesítések a gyakorlati útmutató – Azure SQL Database |} A Microsoft Docs
description: Az Azure SQL Database-lekérdezések automatikus hangolási e-mail-értesítések engedélyezése.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 25a100a224984b0d5608ba933b7a4fa024c22c9d
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481161"
---
# <a name="email-notifications-for-automatic-tuning"></a>Az automatikus hangolási e-mail-értesítések

SQL-adatbázis hangolási ajánlásokat az Azure SQL Database által generált [az automatikus hangolás](sql-database-automatic-tuning.md). Ez a megoldás folyamatosan figyeli és elemzi a számítási feladatok SQL-adatbázisok biztosító testre szabott finomhangolási javaslatai az indexlétrehozást, index törlésre és lekérdezések végrehajtási tervét optimalizálása kapcsolatos minden egyes adatbázis.

Az SQL Database automatikus finomhangolási javaslatai lehet megtekinteni a [az Azure portal](sql-database-advisor-portal.md), a beolvasott [REST API-val](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) meghívja, vagy a [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) és [ PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) parancsokat. Ez a cikk alapján automatikus hangolási ajánlásokat lekérni egy PowerShell-parancsfájl használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>E-mail értesítések automatikus finomhangolási ajánlásait automatizálása

A következő megoldás automatizálja az automatikus finomhangolási ajánlásait tartalmazó e-mail-értesítések küldése. A bemutatott megoldás áll egy PowerShell-szkript használatával hangolási ajánlásokat beolvasása végrehajtásának automatizálása [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), és az ütemezés automation e-mail kézbesítési feladat használatával [Microsoft Flow ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Azure Automation-fiók létrehozása

Azure Automation használatához az első lépéseként automation-fiók létrehozása és konfigurálása, az Azure-erőforrások PowerShell-parancsprogram végrehajtásának használandó. Azure Automation vagy képességeivel kapcsolatos további információkért lásd: [első lépései az Azure automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Kövesse az alábbi lépéseket keresztül mód kiválasztása és konfigurálása az Automation-alkalmazások a Marketplace-ről Azure Automation-fiók létrehozása:

- Jelentkezzen be az Azure Portalon
- Kattintson a "**+ erőforrás létrehozása**" bal felső sarokban található
- Keresse meg "**Automation**" (nyomja le az enter)
- Az Automation-alkalmazásban, a keresési eredmények között kattintson

![Az Azure automation hozzáadása](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Egyszer "Hozzon létre egy Automation-fiók" ablaktáblájában kattintson "**létrehozás**"
- Töltse fel a szükséges adatokat: Adja meg az automation-fiók nevét, válassza ki az Azure-előfizetés Azonosítóját és az Azure-erőforrásokat az használható a PowerShell-parancsprogram végrehajtása
- Az a "**létrehozása Azure-beli futtató fiók**" beállításnál válassza **Igen** alatt melyik PowerShell parancsfájl segítségével az Azure Automation futtató fiók típusú beállításához. Fióktípus esetében kapcsolatos további információkért lásd: [futtató fiók](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Az automation-fiók létrehozása kötnek kattintva **létrehozása**

> [!TIP]
> Az Automation-alkalmazás létrehozásakor megadott pontosan jegyezze fel az Azure Automation-fiók neve, előfizetés-azonosító és erőforrások (például a másolás és beillesztés egy jegyzettömbfájlba). Később ezt az információra van szüksége.
>

Ha több Azure-előfizetést, amelynek szeretné az azonos buildelést, ismételje meg ezt a folyamatot a többi előfizetések szeretne.

## <a name="update-azure-automation-modules"></a>Azure Automation-modulok frissítése

Automatikus hangolás javaslat beolvasni a PowerShell-parancsfájlt használja [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) és [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) melyik Azure-modulok frissítése a 4. verziójú parancsok és újabb verziója szükséges.

Kövesse az alábbi lépéseket az Azure PowerShell-modulok frissítése:

- Az Automation alkalmazás panel eléréséhez, és válassza ki "**modulok**" a bal oldali menüben lévő (görgessen lefelé, ezt a menüelemet megosztott erőforrások alatt).
- A modulok panelen kattintson a "**frissítés az Azure-modulok**" tetején, vagy várjon, amíg az "Azure-modulok frissítve lett-e" üzenet jelenik meg. A folyamat eltarthat pár percig.

![Az Azure automation-modulok frissítése](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

## <a name="create-azure-automation-runbook"></a>Az Azure Automation-Runbook létrehozása

A következő lépés, hogy egy Runbook létrehozása az Azure Automationben, amelyben finomhangolási javaslatai lekéréséhez a PowerShell-parancsprogram található.

Kövesse az alábbi lépéseket egy új Azure Automation-runbook létrehozása:

- Az előző lépésben létrehozott Azure Automation-fiók eléréséhez.
- Egyszer az automatizálási fiók panelen, kattintson az a "**Runbookok**" menüpont egy új Azure Automation-runbook létrehozása a PowerShell-parancsfájlt a bal oldalon. Automation-runbook létrehozásával kapcsolatos további tudnivalókért lásd: [új runbook létrehozásának](../automation/manage-runbooks.md#create-a-runbook).
- Adjon hozzá egy új runbookot, kattintson a a "**+ forgatókönyv hozzáadása**" menüpont, és kattintson a "**gyors létrehozása – hozzon létre egy új runbookot**".
- A Runbook panelen írja be a runbook nevét (jelen példában "**AutomaticTuningEmailAutomation**" használt), válassza ki a runbook **PowerShell** írási és olvasási leírása Ez a runbook célra írja le.
- Kattintson a **létrehozás** gombra az új runbook létrehozásának befejezéséhez

![Az Azure automation-runbook hozzáadása](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Kövesse az alábbi lépéseket betölteni egy PowerShell-parancsfájlt a létrehozott runbook belül:

- Belül a "**PowerShell-alapú Runbook szerkesztése**"panelt, válassza"**RUNBOOKOK**" menüben fát, és bontsa ki a nézet, amíg meg nem látja a runbook nevét (ebben a példában " **AutomaticTuningEmailAutomation**"). Válassza ki a runbookot.
- Az első sort a "PowerShell-Runbook szerkesztése" (a számot 1-től induló), a másolás és beillesztés a következő PowerShell-parancsfájl kódot. A PowerShell parancsfájlpéldát – az első lépésekhez. Módosítsa a parancsfájlt, és a suite az igényeinek.

A fejlécben megadott PowerShell-szkript, ki kell cserélni `<SUBSCRIPTION_ID_WITH_DATABASES>` a az Azure-előfizetés-azonosítójára. Ismerje meg, hogyan kérheti le az Azure-előfizetése Azonosítóját, lásd: [Bevezetés az Azure-előfizetés GUID](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

Több előfizetés esetén később is hozzáadhatja, vesszővel tagolt a parancsfájl fejlécében "$subscriptions" tulajdonsághoz.

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

Kattintson a "**mentése**" gombra a jobb felső sarokban a parancsfájl mentéséhez. Ha elégedett a parancsfájlt, kattintson a "**közzététel**" gombra a runbook közzétételéhez.

A fő runbook ablaktáblán, kattintson a választhatja a "**Start**" gombra kattintva **tesztelése** a parancsfájl. Kattintson a "**kimeneti**" a szkript eredményeinek megtekintése. Ez a kimenet az e-mail tartalma lesz. A szkript kimenetében minta az alábbi képernyőképen látható.

![Futtatásnézet automatikus finomhangolási javaslatai az Azure Automationnel](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Győződjön meg arról, a tartalom módosítása a PowerShell-parancsfájlt az igényeinek megfelelően testre szabásával.

A fenti lépéseket, az automatikus hangolási ajánlásokat lekérni a PowerShell-parancsfájl betöltése az Azure Automationben. A következő lépés, hogy automatizálni, és az e-mailek kézbesítési feladat ütemezése.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>A Microsoft Flow az e-mail-feladatok automatizálása

A megoldás, mint az utolsó lépés végrehajtásához automation folyamat létrehozása a Microsoft Flow álló három műveletet (feladat):

1. "**Azure Automation - feladat létrehozása**" – automatikus finomhangolási javaslatai az Azure Automation-runbook belül beolvasni a PowerShell-parancsfájl végrehajtása használatos
2. "**Azure Automation - feladat kimeneti Get**" használt lekérnie a kimenetet a végrehajtott PowerShell-példaszkript –
3. "**Office 365 Outlook – e-mail küldése**" – küldje el e-mailek használatos. E-mailek az Office 365-fiókkal a folyamatot hoz létre az egyéni küldtünk.

Microsoft Flow képességeivel kapcsolatos további tudnivalókért lásd: [– első lépések a Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Ebben a lépésben előfeltétele, hogy regisztráljon [Microsoft Flow](https://flow.microsoft.com) fiókot, és jelentkezzen be. Egyszer belül a megoldást, és kövesse az alábbi lépéseket állíthat be egy **új folyamat**:

- Hozzáférés "**saját folyamatok**" menüpont
- Saját folyamatok belül válassza a "**+ üres folyamat létrehozása**" hivatkozásra a lap tetején
- Kattintson a hivatkozásra "**keresés több száz összekötő és trigger között**" a lap alján
- A Keresés mezőbe írja be "**ismétlődési**", és válassza ki "**ütemezés – ismétlődés**" az e-mailek kézbesítési feladat futtatásának ütemezése a keresési eredmények közül.
- A gyakoriság mezőjében az ismétlődési ablaktáblán jelölje ki az ütemezési gyakoriság ezen folyamat hajtható végre, mint például az automatikus küldése e-mailben minden perc, óra, nap, hét, stb.

A következő lépés, hogy az újonnan létrehozott ismétlődő folyamat hozzáadása (létrehozása, get-kimenet és küldése e-mailben) három feladat. Ehhez a szükséges feladatok hozzáadása a folyamathoz, kövesse az alábbi lépéseket:

1. Művelet, amely végrehajtja a hangolási javaslatokat beolvasni a PowerShell-parancsprogram létrehozása

   - Jelölje be "**+ új lépés**", majd a"**művelet hozzáadása**" ismétlődési folyamat ablaktáblájában
   - A Keresés mezőbe írja be "**automation**"és válassza a"**Azure Automation-feladat létrehozása**" a keresési eredmények közül
   - A létrehozás a feladat panelt konfigurálja a feladat tulajdonságai. Ehhez a konfigurációhoz, szüksége lesz az Azure-előfizetés Azonosítóját, erőforráscsoport és az Automation-fiók részletei **korábban rögzített** , a **Automation-fiók panelen**. Ez a szakasz az elérhető beállításokkal kapcsolatos további tudnivalókért lásd: [Azure Automation - feladat létrehozása](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Ez a művelet létrehozásának kattintva "**folyamat mentése**"

2. Művelet lekérnie a kimenetet a végrehajtott PowerShell-parancsprogram létrehozása

   - Jelölje be "**+ új lépés**", majd a"**művelet hozzáadása**" ismétlődési folyamat ablaktáblájában
   - A keresés mezőtípusokra "**automation**"és válassza a"**Azure Automation – Get-feladat kimeneti**" a keresési eredmények közül. Ez a szakasz az elérhető beállításokkal kapcsolatos további tudnivalókért lásd: [Azure Automation – Get-feladat kimeneti](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Töltse fel mezők szükséges (hasonló az előző feladat létrehozása) – töltse ki az Azure-előfizetés Azonosítóját, erőforráscsoport, és az Automation-fiók (mivel az Automation-fiók panel be)
   - Kattintson a mezőbe "**Feladatazonosító**" számára a "**dinamikus tartalom**" menü jelenik meg. A belül ebből a menüből válassza a "**Feladatazonosító**".
   - Ez a művelet létrehozásának kattintva "**folyamat mentése**"

3. Küldje el e-mailt Office 365-integráció használatával művelet létrehozása

   - Jelölje be "**+ új lépés**", majd a"**művelet hozzáadása**" ismétlődési folyamat ablaktáblájában
   - A keresés mezőtípusokra "**e-mail küldése**"és válassza a"**Office 365 Outlook – e-mail küldése**" a keresési eredmények közül
   - Az a "**való**" mezőbe írja be az e-mail-címet, amelyhez szeretne értesítő e-mail küldése
   - Az a "**tulajdonos**" mezőbe írja be az e-mail tárgya, például "automatikus finomhangolási ajánlásait e-mailes értesítés"
   - Kattintson a mezőbe "**törzs**" számára a "**dinamikus tartalom**" menü jelenik meg. Az ebben a menüben található alatt "**feladat kimenetének beolvasása**", jelölje be"**tartalom**"
   - Ez a művelet létrehozásának kattintva "**folyamat mentése**"

> [!TIP]
> Automatikus e-mailek küldése a különböző címzetteknek, hozzon létre külön folyamatokat. Ezek a további folyamatok módosítsa a "To" mező a címzett e-mail-címét, és e-mailek Tárgy mezőjében a "Tulajdonos" mező. Az Azure Automation az új runbookok létrehozása egyéni PowerShell-parancsfájlok (például az módosítása az Azure-előfizetés azonosítója) lehetővé teszi, hogy további testreszabási automatizált forgatókönyvek, például van például e-mailben automatikus finomhangolásával külön címzettek önálló előfizetések javaslatok.
>

A fenti azt állapítja meg az e-mailek kézbesítési feladat munkafolyamat konfigurálásához szükséges lépéseket. A beépített három műveletet álló teljes folyamat az alábbi képen látható.

![Automatikus hangolási e-mail értesítések folyamat megtekintése](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

A folyamat teszteléséhez, kattintson a "**Futtatás most**" belül a flow-panel jobb felső sarkában.

Az automatizált feladatokkal, sikeres, küldött e-mail-értesítések megjelenítése a futó statisztika a Flow analytics panelen láthatók.

![Automatikus hangolási e-mail-értesítések a folyamatnak a futtatásához](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

A Flow analytics hasznos lehet a sikeres, a feladat-végrehajtások figyelése, és ha szükséges.  Hibaelhárítás esetén is érdemes megvizsgálni a PowerShell parancsfájl végrehajtási napló az Azure Automation alkalmazáson keresztül érhető el.

A végső kimenetet az automatikus e-mail fogadása után ez a megoldás készítése és a következő e-mail hasonlóan néz ki:

![E-mailek mintakimenete automatikus hangolási e-mail-értesítések](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

A PowerShell-parancsfájl módosításával módosíthatja a kimenet és az automatikus e-mailt az igényeinek megfelelően formázását.

Előfordulhat, hogy további testre szabhatja a megoldást hozhat létre egy adott hangolási esemény alapján, és több címzettnek több előfizetést vagy adatbázisok függően az egyéni példahelyzetekre e-mail-értesítéseket.

## <a name="next-steps"></a>További lépések

- Ismerje meg, további az automatikus hangolás segíthetnek adatbázis-teljesítmény javítása, lásd: [az Azure SQL Database automatikus finomhangolása](sql-database-automatic-tuning.md).
- Engedélyezheti a a számítási feladatok kezelése Azure SQL Database automatikus finomhangolása [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md).
- Manuálisan ellenőrizze és az automatikus finomhangolási javaslatai vonatkoznak [keresse meg és teljesítménnyel kapcsolatos javaslatok alkalmazása](sql-database-advisor-portal.md).

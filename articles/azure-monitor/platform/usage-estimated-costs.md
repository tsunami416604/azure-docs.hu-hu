---
title: Figyelési használat és becsült költségek az Azure monitorban
description: Az Azure Monitor-használat és becsült költségek lapot a folyamat áttekintése
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 7911bd398b6760fb4f83382868f040382b86cd1f
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480542"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Használat és becsült költségek figyelése

> [!NOTE]
> Ez a cikk ismerteti a használat és becsült költségek megtekintése eltérő díjszabási modelleket a több Azure figyelési szolgáltatások között.  További kapcsolódó információt a következő cikkekben talál.
> - [Költségek kezelése a Log Analytics és az adatmennyiség szabályozásával](../../azure-monitor/platform/manage-cost-storage.md) azt ismerteti, hogyan tarthatja a költségeket az Adatmegőrzés időtartama módosításával.
> - [Adathasználat elemzése a Log Analytics](../../azure-monitor/platform/data-usage.md) azt ismerteti, hogyan elemezheti, és riasztást küldjön az adathasználatot.
> - [Az Application Insights árak és adatmennyiségek kezelése](../../azure-monitor/app/pricing.md) adathasználat elemzése az Application Insights a ismerteti.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A figyelő hubon, az Azure Portal a **felhasználás és becsült költségek** lap bemutatja, core, például a figyelési funkciók használata [riasztási, metrikákat, értesítéseket](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), és [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). A 2018 április előtt elérhető díjcsomagok az ügyfelek is magában foglalja az Insights keretében vásárolt Log Analytics-használat és elemzési ajánlat.

Ezen a lapon felhasználók megtekinthetik az erőforrás-használat az elmúlt 31 napra vonatkozó összesített száma előfizetésenként. Modulok részletes használati trendek megjelenítése a 31 napos időszakban. Nagy mennyiségű adatot kell együtt az ezt a becslést, ezért legyen türelmes, ahogy az oldal megfelelően töltődik be.

Ez a példa bemutatja a figyelési használat és becsült költségeit:

![Használat és becsült költségek portál képernyőképe](./media/usage-estimated-costs/001.png)

Válassza ki a hivatkozás megnyitásához egy diagram, amely az elmúlt 31 napos időszakban Alkalmazáshasználati trendek a havi használati oszlopban:

![Keret csomópontonként sáv diagram képernyőképe](./media/usage-estimated-costs/002.png)

Íme egy másik hasonló használat és költségek összegzése. Ez a példa bemutatja egy előfizetés az új 2018 április fogyasztásalapú díjszabási modellben. Megjegyzés: a csomópont-alapú számlázási hiánya. Adatbetöltés és a Log Analytics és az Application Insights megőrzési most egy új gyakori mérőszám alapján jelenti.

![Használat és becsült költségek portál képernyőképe – díjszabása 2018 április](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Új díjszabási modellben

A 2018 április egy [új figyelési díjszabási modellt jelent](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Ez a szolgáltatás felhőalapú mobilbarát fogyasztásalapú díjszabást. Csak fizet, amit ténylegesen használ csomópont-alapú kötelezettségvállalás nélkül. Információk az új díjszabási modell [riasztási, metrikákat, értesítéseket](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) és [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

A Log Analytics vagy az Application Insights 2018. április 2., után ügyfelek bevezetéséhez az új díjszabási modellre az egyetlen lehetőség. Azok a vásárlóknak, akik már használják ezeket a szolgáltatásokat az új díjszabási modellre való megadása nem kötelező.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Az új díjszabási modell hatásának felmérése
Az új díjszabási modell lesz a különböző hatással van a minden ügyfél saját figyelési használati minták alapján. 2018. április 2., mielőtt a Log Analytics vagy az Application Insights használó ügyfelek számára a **használat és becsült költségek** oldal az Azure monitorban bármilyen módosítás a költségek becslése, abban az esetben az új díjszabási modellre. Az előfizetés áthelyezése az új modellbe megoldást kínál. A legtöbb ügyfél számára előnyös lesz az új díjszabási modellre. A különösen magas használati mintái, illetve magasabb költségű régiókra rendelkező ügyfelek esetén ez nem lehet a helyzet.

Az előfizetések a költségeket, a választott becsült megtekintéséhez a **felhasználás és becsült költségek** lapra, jelölje be a kék szalagcím a lap tetején. Érdemes tennie ehhez az egy előfizetéshez egyszerre, mivel ez a szint, amelyen az új díjszabási modellben kell megoldania.

![Használat monitorozása és a becsült költségekkel kell számolnia az új díjszabási modell képernyőképe](./media/usage-estimated-costs/004.png)

Az új lap egy-egy zöld szalagcím a előzetes lap hasonló verzióját jeleníti meg:

![Használat monitorozása és becsült költségek a jelenlegi díjszabási modellben képernyőképe](./media/usage-estimated-costs/005.png)

Az oldal is egy másik készletét mérőszámok, amelyek megfelelnek az új díjszabási modellre mutatja be. Ez a lista a következő egy példa:

- Az Insight and Analytics\Overage csomópontonként
- Az Insight and Analytics\Included csomópontonként
- Alkalmazás Insights\Basic keretet túllépő adatmennyiség
- Alkalmazásadatok Insights\Included

Az új díjszabási modell nem rendelkezik a szolgáltatási keretbe foglalt adatmennyiség csomópont-alapú értékesítjük. Ezért ezek adatokat feldolgozó mérőszámok mostantól egy új common data Adatbetöltési fogyasztásmérő **megosztott Services\Data Adatbetöltési**. 

A Log Analytics vagy az Application Insights magasabb költségeket régiókban betöltött adatok egy másik módosítás van. Ezek nagy költségű régiókra adatait az új megfelelő regionális mérőszámra tér az jelenik meg. Például **(USA nyugati középső régiója) adatbetöltés**.

> [!NOTE]
> Az előfizetésenként becsült költségek nem tényező be / csomópont jogosultságok az Operations Management Suite (OMS) előfizetés fiókszintű. Ebben az esetben tekintse meg a fiók kapcsolattartójához az új díjszabási modell részletesebb leírását.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Új díjszabási modell és az Operations Management Suite-előfizetés jogosultságai

Ügyfelek, akik megvásárolták a Microsoft Operations Management Suite E1 és E2 jogosultak a csomópontonkénti adatokat feldolgozó jogosultságok [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) és [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Ezeket a jogosultságokat a Log Analytics-munkaterületek és Application Insights-erőforrások fogadásához egy adott előfizetésben: 

- Az előfizetéshez tartozó díjszabási modell a. április előtti 2018-as modell kell maradnia.
- Log Analytics-munkaterületek a "csomópontonként (OMS)" tarifacsomag kell használnia.
- Application Insights-erőforrásokat kell használnia a "Vállalati" tarifacsomagot.

A csomag, amely a szervezete vásárolt, a csomópontok számától függően néhány áthelyezése az új díjszabási modellre előfizetések előnyös lehet, de alapos megfontolás ehhez. Általában célszerű egyszerűen maradjon a előtti-április 2018-as modell fent leírtak szerint.

> [!WARNING]
> Ha a munkahelye megvásárolta a Microsoft Operations Management Suite E1 és E2, általában célszerű megtartani az előfizetések 2018 előtti-április díjszabási modellje. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Módosul, amikor telepít át az új díjszabási modellben

Az új díjszabási modellre egyszerűbbé teszi a Log Analytics és az Application Insights díjszabása csak egy egyetlen réteg (vagy tervezett) lehetőségeket. Előfizetés helyezi át az új díjszabási modell lesz:

- Módosítsa a tarifacsomagot egyes Log Analytics új GB-onkénti szintre (úgynevezett "pergb2018" az Azure Resource Manager)
- Minden Application Insights-erőforrások, a vállalati csomag a Basic csomag változik.

Költségbecslési bemutatja ezeket a módosításokat a hatásait.

> [!WARNING]
> Itt fontos megjegyzés az üzembe helyezése az Azure Resource Manager vagy a PowerShell használatakor [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) vagy [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) egy előfizetésben, hogy áttelepítette az új díjszabási modell. Ha megad egy árképzési szint/csomag eltérő "pergb2018" Log Analytics vagy az "Alapszintű" Application Insights, helyett elmulasztása miatt érvénytelen szint/tarifacsomagot, megadása a központi telepítés sikeres lesz **, de csak az érvényes fogja használni díjszabásiszint/** (ez nem vonatkozik a Log Analytics ingyenes szintre, ahol árképzési szint érvénytelen üzenetet jön létre).
>

## <a name="moving-to-the-new-pricing-model"></a>Az új díjszabási modellre áthelyezése

Ha már eldöntötte, hogy elfogadják az új díjszabási modell egy előfizetéshez, válassza ki a **díjszabási modell kiválasztása** lehetőséget a felső részén a **felhasználás és becsült költségek** oldalon:

![Használat monitorozása és a becsült költségekkel kell számolnia az új díjszabási modell képernyőképe](./media/usage-estimated-costs/006.png)

A **díjszabási modell kiválasztása** oldal jelenik meg. Azt az előfizetést, azt az előző lapon tekinthetők meg az egyes listáját jeleníti meg:

![Díjszabási modell kiválasztása képernyőképe](./media/usage-estimated-costs/007.png)

Szeretne áthelyezni egy előfizetés az új díjszabási modellben, csak adja meg a mezőbe, és válassza ki **mentése**. Akkor is lépjen vissza a régi díjszabási modell megegyező módon. Ne feledje, hogy előfizetés-tulajdonosi vagy közreműködői engedélyekkel kell módosítani a díjszabási modellt.

## <a name="automate-moving-to-the-new-pricing-model"></a>Az új díjszabási modellre való automatizálásához

Az alábbi parancsfájlok van szükség az Azure PowerShell-modul. Ellenőrizze, hogy a legújabb verziót, tekintse meg a [Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps).

Miután az Azure PowerShell legújabb verzióját, akkor először futtatni szeretné ``Connect-AzAccount``.

``` PowerShell
# To check if your subscription is eligible to adjust pricing models.
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Alatt isGrandFatherableSubscription eredménye IGAZ, azt jelzi, hogy áthelyezhető-e az előfizetés díjszabási modell közötti árképzési modellekkel. Egy értéket a optedInDate hiánya jelenti, hogy ehhez az előfizetéshez jelenleg a régi díjszabási modellre van beállítva.

```
isGrandFatherableSubscription optedInDate
----------------------------- -----------
                         True            
```

Ez az előfizetés áttelepítése az új díjszabási modellre, futtassa:

```powershell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
```

Ellenőrizze, hogy a módosítás volt sikeres újrafuttatása:

```powershell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Ha az áttelepítés sikeres volt, az eredmény hasonlóan kell kinéznie:

```
isGrandFatherableSubscription optedInDate                      
----------------------------- -----------                      
                         True 2018-05-31T13:52:43.3592081+00:00
```

A optInDate az időbélyeg, ha ez az előfizetés kérte, hogy az új díjszabási modellben már tartalmazza.

Ha szeretne visszatérni a régi díjszabási modell, futtatná:

```powershell
 $ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action rollbacktolegacypricingmodel `
 -Force
```

Ha az előző parancsfájlt, amely rendelkezik majd Újrafuttatja ``-Action listmigrationdate``, ekkor megjelenik egy üres optedInDate érték jelzi az előfizetés a régi díjszabási modellre való lett visszaadva.

Ha több előfizetéssel rendelkezik, hogy migrálni szeretne a ugyanazt bérlőhöz üzemelnek, amely létrehozhatja a saját variant megtalálhatja az alábbi parancsfájlok használatával:

```powershell
#Query tenant and create an array comprised of all of your tenants subscription ids
$TenantId = <Your-tenant-id>
$Tenant =Get-AzSubscription -TenantId $TenantId
$Subscriptions = $Tenant.Id
```

Ellenőrizze, hogy ha a bérlő összes előfizetés jogosultak az új díjszabási modellben, futtathatja:

```powershell
Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
}
```

Lehet, hogy a parancsfájl finomított további hozzon létre egy parancsfájlt, amely három tömbök állít elő. Egy tömb áll, amely az összes előfizetés-azonosító a rendelkező ```isGrandFatherableSubscription``` igaz értékre van állítva, és optedInDate jelenleg nem rendelkezik értékkel. Olyan előfizetéseket, jelenleg az új díjszabási modellben a második tömbje. És a egy harmadik tömb csak az előfizetés azonosítókat a bérlő nem jogosultak az új díjszabási modell használatával:

```powershell
[System.Collections.ArrayList]$Eligible= @{}
[System.Collections.ArrayList]$NewPricingEnabled = @{}
[System.Collections.ArrayList]$NotEligible = @{}

Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
$Result= Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force

     if ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $False)
     {
     $Eligible.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $True)
     {
     $NewPricingEnabled.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $False)
     {
     $NotEligible.add($id)
     }
}
```

> [!NOTE]
> Előfizetések számától függően a a fenti szkript futtatásához időbe telhet. A .add() módszer használata miatt a PowerShell-ablakban fog echo növekvő értékei elemek hozzáadásakor minden tömbbe.

Most, hogy az előfizetések, a három tömbök osztható meg alaposan tekintse át az eredményeket. Érdemes lehet, hogy készítsen biztonsági másolatot a tömbök a tartalmát, így könnyen visszaállíthatja a módosításokat a későbbiekben vissza kívánja. Ha úgy dönt, szeretett volna a jogosult előfizetéseket, amelyek a rendszer jelenleg a régi díjszabási modell az új díjszabási modell Ez a feladat most elvégezhető az átalakítás:

```powershell
Foreach ($id in $Eligible)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
}

```

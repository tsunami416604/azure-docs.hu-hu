---
title: Erőforrások, szerepkörök és hozzáférés-vezérlés az Azure Application Insightsban | Microsoft dokumentumok
description: A szervezet elemzési adatainak tulajdonosai, közreműködői és olvasói.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 1e57af269c4052d0dcd4a8f7970ca23017024299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473132"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Erőforrások, szerepkörök és hozzáférés-vezérlés az Application Insightsban

Az Azure [Application Insights][start] [szerepköralapú hozzáférés-vezérlésével](../../role-based-access-control/role-assignments-portal.md)szabályozhatja, hogy ki rendelkezik olvasással és frissítéssel az adatokhoz való hozzáféréssel a Microsoft Azure-ban.

> [!IMPORTANT]
> Hozzáférés hozzárendelése az **erőforráscsoport vagy előfizetés azon** felhasználóihoz, amelyhez az alkalmazás-erőforrás tartozik – nem magában az erőforrásban. Rendelje hozzá az **Application Insights összetevő közreműködői** szerepkörét. Ez biztosítja a webes tesztekhez és riasztásokhoz való hozzáférés egységes szabályozását az alkalmazás-erőforrással együtt. [További információ](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Erőforrások, csoportok és előfizetések

Először is, néhány meghatározás:

* **Erőforrás** – Egy Microsoft Azure-szolgáltatás példánya. Az Application Insights-erőforrás gyűjti, elemzi és megjeleníti az alkalmazásból küldött telemetriai adatokat.  Az Azure-erőforrások más típusai közé tartoznak a webalkalmazások, az adatbázisok és a virtuális gépek.
  
    Az erőforrások megtekintéséhez nyissa meg az [Azure Portalon,][portal]jelentkezzen be, és kattintson a Minden erőforrás elemre. Erőforrás megkereséséhez írja be a neve egy részét a szűrőmezőbe.
  
    ![Az Azure-erőforrások listája](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Erőforráscsoport**][group] – Minden erőforrás egy csoporthoz tartozik. A csoport kényelmes módja a kapcsolódó erőforrások kezelésének, különösen a hozzáférés-vezérléshez. Például egy erőforráscsoportba helyezhet egy webalkalmazást, egy Application Insights-erőforrást az alkalmazás figyelésére, és egy Storage-erőforrást az exportált adatok megtartásához.

* [**Előfizetés**](https://portal.azure.com) – Az Application Insights vagy más Azure-erőforrások használatához bejelentkezik egy Azure-előfizetésbe. Minden erőforráscsoport egy Azure-előfizetéshez tartozik, ahol kiválaszthatja az árcsomagot, és ha szervezeti előfizetésről van szó, válassza ki a tagokat és azok hozzáférési engedélyeit.
* [**Microsoft-fiók**][account] – A Microsoft Azure-előfizetésekbe, az XBox Liveba, a Outlook.com és más Microsoft-szolgáltatásokba való bejelentkezéshez használt felhasználónév és jelszó.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a>Hozzáférés szabályozása az erőforráscsoportban

Fontos megérteni, hogy az alkalmazáshoz létrehozott erőforrás mellett külön rejtett erőforrások is vannak a riasztásokhoz és a webes tesztekhez. Ugyanahhoz az [erőforráscsoporthoz](#resource-group) vannak csatolva, mint az Application Insights-erőforrás. Előfordulhat, hogy más Azure-szolgáltatásokat is behelyezett, például webhelyeket vagy tárhelyet.

Az erőforrásokhoz való hozzáférés szabályozásához ezért a következőket javasoljuk:

* Hozzáférés szabályozása az **erőforráscsoport vagy az előfizetés** szintjén.
* Rendelje hozzá az **Application Insights-összetevő közreműködői** szerepkört a felhasználókhoz. Ez lehetővé teszi számukra, hogy webes tesztek, riasztások és Az Application Insights-erőforrások szerkesztése anélkül, hogy a csoport más szolgáltatásaihoz való hozzáférést.

## <a name="to-provide-access-to-another-user"></a>Hozzáférés biztosítása egy másik felhasználószámára

Tulajdonosi jogosultságokkal kell rendelkeznie az előfizetéshez vagy az erőforráscsoporthoz.

A felhasználónak [Rendelkeznie kell Microsoft-fiókkal][account], vagy hozzáféréssel kell rendelkeznie [a szervezeti Microsoft-fiókjához.](../../active-directory/fundamentals/sign-up-organization.md) Hozzáférést biztosíthat az egyének, valamint az Azure Active Directoryban definiált felhasználói csoportok.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navigálás az erőforráscsoportra vagy közvetlenül magára az erőforrásra

Válassza a bal oldali menü **hozzáférés-vezérlési (IAM)** parancsát.

![Képernyőkép a Hozzáférés-vezérlés gombról az Azure Portalon](./media/resources-roles-access-control/0001-access-control.png)

**Válassza a Szerepkör-hozzárendelés hozzáadása lehetőséget.**

![Képernyőkép a Hozzáférés-vezérlés menüről, pirossal kiemelve a Hozzáadás gombbal](./media/resources-roles-access-control/0002-add.png)

Az **alábbi Engedélyek hozzáadása** nézet elsősorban az Application Insights-erőforrásokra vonatkozik, ha magasabb szintű, például erőforráscsoportok hozzáférés-vezérlési engedélyeit tekintette meg, további, nem Application Insights-központú szerepköröket láthat.

Az azure-beli szerepköralapú hozzáférés-vezérlés beépített szerepkörökre vonatkozó információk megtekintéséhez használja a [hivatalos referenciatartalmat.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

![Képernyőkép a hozzáférés-vezérlés felhasználói szerepkörlistájáról](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Szerepkör kiválasztása

Adott esetben a kapcsolódó hivatalos referenciadokumentációra hivatkozunk.

| Szerepkör | Az erőforráscsoportban |
| --- | --- |
| [Tulajdonos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Bármit megváltoztathat, beleértve a felhasználói hozzáférést is. |
| [Közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Bármit szerkeszthet, beleértve az összes erőforrást is. |
| [Az Application Insights-összetevő közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Szerkesztheti az Application Insights-erőforrásokat. |
| [Olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Megtekintheti, de nem változtat semmit. |
| [Az Application Insights pillanatkép-hibakereső](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Engedélyt ad a felhasználónak az Application Insights Snapshot Debugger szolgáltatásainak használatára. Vegye figyelembe, hogy ez a szerepkör nem szerepel sem a tulajdonos, sem a közreműködői szerepkörökben. |
| Az Azure Service központi telepítésének kiadáskezelési közreműködője | Közreműködői szerepkör az Azure Service Deploy szolgáltatáson keresztül üzembe helyezett szolgáltatásokhoz. |
| [Adatpurger](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | A személyes adatok tisztításának különleges szerepe. További információkért tekintse meg [a személyes adatokra vonatkozó útmutatónkat.](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data)   |
| ExpressRoute-rendszergazda | Gyorsútvonalak at hozhat létre és kezelhet.|
| [Log Analytics közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | A Log Analytics közreműködője letudja olvasni az összes figyelési adatot, és szerkesztheti a figyelési beállításokat. A figyelési beállítások szerkesztése magában foglalja a virtuális gépbővítmény virtuális gépekhez való hozzáadását; a tárfiók kulcsainak olvasása az Azure Storage-ból származó naplók gyűjteményének konfigurálásához; Automatizálási fiókok létrehozása és konfigurálása; megoldások hozzáadása; és konfigurálja az Azure-diagnosztikát az összes Azure-erőforráson.  |
| [Log Analytics olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | A Log Analytics Reader megtekintheti és keresheti az összes figyelési adatot, valamint megtekintheti a figyelési beállításokat, beleértve az Azure-diagnosztika konfigurációjának megtekintését az összes Azure-erőforráson. |
| mesterolvasó | Lehetővé teszi a felhasználó számára, hogy mindent megtekintsen, de ne módosítson. |
| [Közreműködő figyelése](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Képes olvasni az összes figyelési adatokat, és frissíti a figyelési beállításokat.|
| [Monitormetrika közzétevő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Engedélyezi a metrikák azure-erőforrásokon való közzétételét. |
| [Monitoring olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Képes olvasni az összes figyelési adatot. |
| Erőforrás-házirend közreműködője (előzetes verzió) | Az EA-tól visszatöltött felhasználók, akik jogosultak az erőforrás-házirend létrehozására/módosítására, a támogatási jegy létrehozására és az erőforrás/hierarchia olvasására.  |
| [Felhasználói hozzáférés adminisztrátora](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Lehetővé teszi a felhasználó számára, hogy kezelje a hozzáférést más felhasználók számára az Azure-erőforrásokhoz.|
| [Webhely közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Lehetővé teszi a webhelyek kezelését (nem webes sémákat), de nem férhet hozzájuk..|

A "szerkesztés" magában foglalja a létrehozást, a törlést és a frissítést:

* Források
* Webes tesztek
* Riasztások
* Folyamatos exportálás

#### <a name="select-the-user"></a>A felhasználó kijelölése

Ha a kívánt felhasználó nincs a címtárban, bárkit meghívhat, akinek Microsoft-fiókja van.
(Ha olyan szolgáltatásokat használnak, mint a Outlook.com, a OneDrive, a Windows Phone vagy az XBox Live, microsoftos fiókkal rendelkeznek.)

## <a name="related-content"></a>Kapcsolódó tartalom

* [Szerepköralapú hozzáférés-vezérlés az Azure-ban](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>PowerShell-lekérdezés a szerepkör-tagság meghatározásához

Mivel bizonyos szerepkörök értesítésekhez és e-mail értesítésekhez kapcsolhatók, hasznos lehet egy adott szerepkörhöz tartozó felhasználók listáját létrehozni. Az ilyen típusú listák létrehozásának elősegítésére a következő mintalekérdezéseket kínáljuk, amelyek az Ön egyedi igényeinek megfelelően módosíthatók:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Rendszergazdai szerepkörök teljes előfizetésének lekérdezése + közreműködői szerepkörök

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Lekérdezés egy adott Application Insights-erőforrás környezetében tulajdonosok és közreműködők számára

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Adott erőforráscsoport környezetében lekérdezés tulajdonosok és közreműködők számára

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

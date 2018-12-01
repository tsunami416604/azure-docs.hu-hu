---
title: Erőforrások, szerepkörök és hozzáférés-vezérlés az Azure Application Insightsban |} A Microsoft Docs
description: Tulajdonosoknak, közreműködőknek és olvasóknak a szervezet insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: mbullwin
ms.openlocfilehash: 0d327ac780f7881662c963013fe3828440294076
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725250"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Erőforrások, szerepkörök és hozzáférés-vezérlés az Application Insights

Szabályozhatja, hogy rendelkezik-e olvasási és frissítési hozzáférés az adatokhoz az Azure-ban [Application Insights][start], használatával [szerepköralapú hozzáférés-vezérlés a Microsoft Azure](../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Hozzáférés hozzárendelése a felhasználók számára a **erőforráscsoportba vagy előfizetésbe** , amelyhez az alkalmazás-erőforrás tartozik – nem található a erőforrásán. Rendelje hozzá a **Application Insights-összetevők közreműködője** szerepkör. Ez biztosítja, hogy a webes tesztek és a riasztások mellett az alkalmazás-erőforrás elérését egységes irányítását. [További információk](#access).

## <a name="resources-groups-and-subscriptions"></a>Erőforrások, csoportok és előfizetések

Első, némi definíciókat:

* **Erőforrás** – Microsoft Azure-szolgáltatás példánya egy. Az Application Insights-erőforrás gyűjti, elemzi és megjeleníti az alkalmazásból küldött telemetriai adatok.  Más típusú Azure-erőforrások közé tartozik, webalkalmazások, adatbázisok és virtuális gépeket.
  
    Az erőforrások megtekintéséhez nyissa meg a [az Azure portal][portal], jelentkezzen be, és kattintson az összes erőforrást. Egy erőforrás található, írja be a nevének egy részét a Szűrő mezőbe.
  
    ![Az Azure-erőforrások listája](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Erőforráscsoport** ] [ group] – minden erőforrás egy csoporthoz tartozik. Egy csoport kényelmes módja a kapcsolódó erőforrásokat, különösen a hozzáférés-vezérlés kezelése. Ha például helyezzen egy erőforráscsoportban elhelyezheti egy webalkalmazás, az alkalmazás figyelése az Application Insights-erőforrás és egy tárolási erőforrás exportált adatok.

* [**Előfizetés** ](https://portal.azure.com) – Azure-előfizetéssel jelentkezik be az Application Insights vagy más Azure-erőforrások használatára. Minden erőforráscsoport tartozik egy Azure-előfizetéssel, ahol az ár csomag kiválasztása és, ha egy szervezet előfizetését, válassza ki a tagok és a hozzáférési engedélyeik.
* [**Microsoft-fiók** ] [ account] – a felhasználónév és a Microsoft Azure előfizetések, XBox Live, Outlook.com-os és más Microsoft-szolgáltatásokba való bejelentkezéshez használt jelszó.

## <a name="access"></a> Hozzáférés vezérlése az erőforráscsoportban

Fontos megérteni, hogy az alkalmazás létrehozott erőforrást, kívül is külön rejtett riasztások és a webes tesztek erőforrásokat. Azonos vannak csatolva [erőforráscsoport](#resource-group) mint az alkalmazást. Előfordulhat, hogy Emellett más Azure-szolgáltatások az helyezett itt, például webhelyeket vagy a tároló.

Ezért javasoljuk, hogy ezekhez az erőforrásokhoz való hozzáférés szabályozásához:

* Szabályozhatja a hozzáférést a **erőforráscsoportba vagy előfizetésbe** szintjét.
* Rendelje hozzá a **Application Insights-összetevő közreműködői** szerepkör a felhasználók számára. Ez lehetővé teszi webes teszteket, a riasztások és az Application Insights-erőforrások, módosíthatják a csoport bármely egyéb szolgáltatásokhoz való hozzáférés biztosítása nélkül.

## <a name="to-provide-access-to-another-user"></a>Hozzáférést biztosít egy másik felhasználónak

Az előfizetés vagy az erőforráscsoport tulajdonosi jogosultsággal kell rendelkeznie.

A felhasználónak rendelkeznie kell egy [Microsoft Account][account], vagy a hozzáférést a [szervezeti Microsoft-Account](../active-directory/fundamentals/sign-up-organization.md). Megadhatja a hozzáférési egyének és az Azure Active Directoryban meghatározott felhasználói csoportok számára.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Keresse meg az erőforráscsoportot, vagy közvetlenül az erőforrásán

Válasszon **hozzáférés-vezérlés (IAM)** elemet a bal oldali menüben.

![Képernyőfelvétel a hozzáférési forráskezelés gomb az Azure Portalon](./media/app-insights-resources-roles-access-control/0001-access-control.png)

Válassza ki **szerepkör-hozzárendelés hozzáadása**

![Képernyőfelvétel a hozzáférési vezérlőelemének menüjében pirossal kiemelt Hozzáadás gomb](./media/app-insights-resources-roles-access-control/0002-add.png)

A **engedélyek hozzáadása** lásd alább a elsősorban jellemző az Application Insights-erőforrást a hozzáférés-vezérlési engedélyekkel az erőforráscsoportok, például magasabb szintű megtekintett jelennének meg további, nem alkalmazás Insights-központú szerepkörök.

Az összes Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök használata információinak megtekintése a [hivatalos segédanyagok](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Képernyőfelvétel: a hozzáférés-vezérlési felhasználói szerepkör lista](./media/app-insights-resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Szerepkörválasztás

Adott esetben arra mutató hivatkozás a társított hivatalos dokumentációjában.

| Szerepkör | Az erőforráscsoportban |
| --- | --- |
| [Tulajdonos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Bármit módosíthat, beleértve a felhasználói hozzáférést. |
| [Közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Szerkesztheti kiterjedhet, például az összes erőforrást. |
| [Application Insights-összetevő közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Szerkesztheti az Application Insights-erőforrások, webes teszteket és riasztások. |
| [Olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Megtekintheti, de nem bármin változtatni. |
| [Application Insights Snapshot Debugger](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Application Insights Snapshot Debugger szolgáltatásainak használatához a felhasználónak engedélyt ad. Vegye figyelembe, hogy ezt a szerepkört sem a tulajdonosa, sem a közreműködői szerepkör tartalmazza. |
| Azure-beli szolgáltatástelepítés kiadáskezelési közreműködője | Közreműködői szerepkör-szolgáltatásokhoz az Azure szolgáltatás üzembe helyezése az üzembe helyezést. |
| [Adatok Purger](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Személyes adatok végleges törlése a különleges szerepkörrel. Tekintse meg a [személyes adatokra vonatkozó útmutatás](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) további információt.   |
| Az ExpressRoute-rendszergazda | Létrehozhat a delete és express-útvonalak kezelése.|
| [Log Analytics-közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Log Analytics-közreműködő az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Figyelési beállítások szerkesztése magában foglalja a virtuális gépek; a Virtuálisgép-bővítmény hozzáadása tudni naplógyűjtemény konfigurálása céljából az Azure Storage; tárfiók kulcsainak beolvasását létrehozásáról és konfigurálásáról az Automation-fiókok; megoldások hozzáadását; és az Azure diagnostics konfigurálása az összes Azure-erőforrást.  |
| [Log Analytics olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | A Log Analytics-olvasó megtekintheti az összes figyelési adatot, és kereshet azokban, valamint megtekintheti a figyelési beállításokat, beleértve az összes Azure-erőforrás Azure-beli diagnosztikájának konfigurációját is. |
| masterreader | Lehetővé teszi, hogy a felhasználót, hogy mindent megtekinthessen, de nem végezhet módosításokat. |
| [Közreműködő figyelése](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Olvashatja az összes figyelési adatot, és frissítheti a figyelési beállításokat. |
| [Figyelési metrikák közzétevő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Lehetővé teszi a metrikákat az Azure-erőforrások közzétételét. |
| [Olvasó figyelése](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Olvashatja az összes figyelési adatot. |
| Erőforrás-szabályzati közreműködő (előnézet) | Erőforrás-szabályzat létrehozására/módosítására jogosultságokkal EA-beli visszatöltött felhasználók hozzon létre támogatási jegyet, és olvassa el az erőforrások/hierarchia.  |
| [Felhasználói hozzáférés rendszergazdája](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Lehetővé teszi a felhasználóknak más felhasználók Azure-erőforrásokhoz való hozzáférés kezelésére.|
| [Webhelyek Közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Lehetővé teszi websites (a webes tarifacsomagokét), de az azokhoz való hozzáférés kezelése...|

"Szerkesztés" tartalmazza, létrehozása, törlése és frissítése:

* További források
* Webtesztek
* Riasztások
* Folyamatos exportálás

#### <a name="select-the-user"></a>Válassza ki a felhasználót

Ha azt szeretné, a felhasználó nem a címtárban, meghívhatja munkatársait, bárki Microsoft-fiókkal.
(Ha használnak a szolgáltatások, például az Outlook.com, onedrive vállalati verzió, Windows Phone vagy XBox Live, rendelkeznek egy Microsoft-fiókjával.)

## <a name="related-content"></a>Kapcsolódó tartalom

* [Szerepköralapú hozzáférés-vezérlés az Azure-ban](../role-based-access-control/role-assignments-portal.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md

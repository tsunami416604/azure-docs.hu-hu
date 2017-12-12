---
title: "Erőforrások, szerepkörök és hozzáférés szabályozása Azure Application insightsban |} Microsoft Docs"
description: "Tulajdonosai, közreműködő szerepkörrel rendelkező személyek és a szervezet insights olvasói."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin
ms.openlocfilehash: 6e811c9b427469fa781cf1f5b7c7deff3a8e6eb3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Erőforrások, szerepkörök és hozzáférés-vezérlés az Application Insightsban
Szabályozhatja, aki rendelkezik-e olvasási és hozzáférési frissíteni az adatait az Azure-ban [Application Insights][start], használatával [Microsoft Azure szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md).

> [!IMPORTANT]
> Hozzáférést biztosít a felhasználók a **erőforráscsoportba vagy előfizetésbe** , amelyhez az alkalmazás erőforrás tartozik - magát az erőforrás nem található. Rendelje hozzá a **Application Insights-összetevővel kapcsolatos közreműködői** szerepkör. Ez biztosítja az egységes irányítását webes tesztjeinek használatát, és a riasztások mellett az alkalmazás erőforrás elérésére. [További információk](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>Erőforrások, csoportok és előfizetések
Első, néhány definíciók:

* **Erőforrás** - egy Microsoft Azure service-példányhoz. Az Application Insights-erőforrás gyűjti, elemzi és az alkalmazásból küldött telemetriai adatait jeleníti meg.  Más típusú Azure-erőforrások közé tartoznak a webalkalmazások, adatbázisok és virtuális gépek.
  
    Az erőforrások megtekintéséhez nyissa meg a [Azure Portal][portal], jelentkezzen be, majd kattintson az összes erőforrást. Egy erőforrás található, írja be a név egy részének a Szűrő mezőbe.
  
    ![Azure-erőforrások listája](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Erőforráscsoport** ] [ group] -minden erőforrás egy csoporthoz tartozik. Egy csoport kényelmesen felügyelhetők a kapcsolódó erőforrások, különösen a hozzáférés-vezérléshez. Például az egyik erőforráscsoportból ütemezésbe helyezheti egy webalkalmazást, az Application Insights-erőforrás a figyelheti az alkalmazást, és a tároló egyik erőforrásához exportált adatokat.

    ![A Tallózás, erőforráscsoportok, majd válasszon ki egy csoportot](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Előfizetés** ](https://portal.azure.com) - jelentkezzen be Azure-előfizetés az Application Insights vagy más Azure-erőforrások használatára. Minden erőforráscsoport tartozik egy Azure-előfizetéssel, ahol az ár csomag kiválasztása és, ha egy szervezet előfizetés, válassza ki a tagok és a hozzáférési engedélyeket.
* [**Microsoft-fiók** ] [ account] -felhasználónevét és jelszavát, amelyekkel bejelentkezhet a Microsoft Azure előfizetések, XBox Live-, Outlook.com-os és más Microsoft-szolgáltatásokban.

## <a name="access"></a>Elérés az erőforráscsoportban
Fontos megérteni, hogy az erőforrás alkalmazás létrehozta, kívül is külön rejtett erőforrások riasztások és a webes tesztjeinek használatát. Kapcsolódó azonos [erőforráscsoport](#resource-group) az alkalmazás. Előfordulhat, hogy is vezettek be más Azure-szolgáltatásokat az ott, például webhelyekhez vagy tároló.

![Az Application Insightsban erőforrások](./media/app-insights-resources-roles-access-control/00-resources.png)

Ezekhez az erőforrásokhoz való hozzáférés vezérlése érdekében ezért javasoljuk, hogy:

* Szabályozhatja a hozzáférést a **erőforráscsoportba vagy előfizetésbe** szintjét.
* Rendelje hozzá a **Application Insights-összetevővel kapcsolatos közreműködői** szerepkör a felhasználók számára. Ez lehetővé teszi webes tesztjeinek használatát, a riasztások és az Application Insights-erőforrások, módosíthatják a csoport minden más szolgáltatásokhoz való hozzáférés megadása nélkül.

## <a name="to-provide-access-to-another-user"></a>A másik felhasználó való hozzáférés biztosításához
Az előfizetés vagy az erőforráscsoport tulajdonosi jogosultsággal kell rendelkeznie.

A felhasználónak rendelkeznie kell egy [Microsoft Account][account], vagy nem érhető el a saját [szervezeti Microsoft Account](../active-directory/sign-up-organization.md). Egyéni felhasználók számára, valamint az Azure Active Directory felhasználói csoportok hozzáférést biztosíthat.

#### <a name="navigate-to-the-resource-group"></a>Keresse meg az erőforráscsoport
Nincs a felhasználó hozzáadása.

![Az alkalmazás erőforrás panelen nyissa meg a Essentials, nyissa meg az erőforráscsoportot és nincs válassza a beállítások/felhasználók. Kattintson az Add (Hozzáadás) parancsra.](./media/app-insights-resources-roles-access-control/01-add-user.png)

Vagy nem sikerült lépjen be egy másik szint és a felhasználó hozzáadása az előfizetéshez.

#### <a name="select-a-role"></a>Szerepkörválasztás
![Az új felhasználó szerepkör kiválasztása](./media/app-insights-resources-roles-access-control/03-role.png)

| Szerepkör | Az erőforráscsoporthoz tartozik |
| --- | --- |
| Tulajdonos |Módosíthatja semmit, beleértve a felhasználói hozzáférés |
| Közreműködő |Bármi, beleértve az összes erőforrás szerkesztése |
| Application Insights-összetevővel kapcsolatos közreműködői |Az Application Insights erőforrásokat, webes tesztjeinek használatát, és a riasztások szerkesztése |
| Olvasó |Megtekintheti, de nem bármit módosíthat |

"Szerkesztés" magában foglalja a létrehozása, törlése és frissítése:

* Erőforrások
* Webtesztek
* Riasztások
* Folyamatos exportálás

#### <a name="select-the-user"></a>Válassza ki a felhasználót

Ha a felhasználó nem szerepel a könyvtár, felajánlhatja bárki, aki a Microsoft-fiók.
(Például Outlook.com, OneDrive, Windows Phone vagy XBox Live services használata, ha rendelkeznek a Microsoft-fiók.)

## <a name="related-content"></a>Kapcsolódó tartalom

* [Szerepköralapú hozzáférés-vezérlés az Azure-ban](../active-directory/role-based-access-control-configure.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md

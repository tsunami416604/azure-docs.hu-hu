---
title: App Service terv kezelése – Azure | Microsoft Docs
description: Megtudhatja, hogyan hajthat végre különböző feladatokat egy App Service-csomag kezeléséhez.
keywords: App Service, Azure app Service, skálázás, app Service-csomag, módosítás, létrehozás, felügyelet, felügyelet
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 77d5e4ace14fb0071b6e01a01edbad0128382303
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639743"
---
# <a name="manage-an-app-service-plan-in-azure"></a>App Service-csomag kezelése az Azure-ban

Egy [Azure app Service csomag](overview-hosting-plans.md) biztosítja azokat az erőforrásokat, amelyeket egy app Service alkalmazásnak futtatnia kell. Ez az útmutató bemutatja, hogyan kezelhető egy App Service-csomag.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

> [!TIP]
> Ha App Service Environment rendelkezik, tekintse meg a [app Service terv létrehozása app Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Létrehozhat egy üres App Service-csomagot, vagy létrehozhat egy csomagot az alkalmazások létrehozásának részeként.

1. A [Azure Portal](https://portal.azure.com)válassza az **új** > **web + mobil**lehetőséget, majd válassza a **webalkalmazás** vagy más típusú app Service alkalmazás lehetőséget.

2. Válasszon ki egy meglévő App Service csomagot, vagy hozzon létre egy csomagot az új alkalmazáshoz.

   ![Hozzon létre egy alkalmazást a Azure Portalban.][createWebApp]

   Csomag létrehozása:

   a. Válassza a **[+] Create New (új létrehozása**) lehetőséget.

      ![Hozzon létre egy App Service tervet.][createASP] 

   b. **App Service csomag**esetében adja meg a csomag nevét.

   c. A **hely**mezőben válasszon ki egy megfelelő helyet.

   d. A **díjszabási**szinten válasszon ki egy megfelelő díjszabási szintet a szolgáltatáshoz. Az **összes megtekintése** elemre kattintva további díjszabási lehetőségeket jeleníthet meg, például az **ingyenes** és a **megosztott**lehetőséget. Miután kiválasztotta az árképzési szintet, kattintson a **kiválasztás** gombra.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Alkalmazás áthelyezése egy másik App Service tervbe

Az alkalmazások áthelyezhetők egy másik App Service csomagba, ha a forrás-és a megcélzott terv _ugyanabban az erőforráscsoport-és földrajzi régióban_található.

> [!NOTE]
> Az Azure minden új App Service tervet üzembe helyez egy üzembe helyezési egységben, amelyet egy webtárhelynek nevezünk. Az egyes régiók több webtárhelyet is tartalmazhatnak, de az alkalmazás csak az ugyanazon a webtérben létrehozott csomagok között tud mozogni. Az App Service Environment egy elkülönített webtárhely, így az alkalmazások áthelyezhetők a csomagok között ugyanazon a App Service Environment, de nem a különböző App Service környezetekben lévő csomagok között.
>
> A terv létrehozásakor nem adhatja meg a használni kívánt webtárhelyet, de lehetséges, hogy a terv ugyanabban a webtérben jön létre, mint egy meglévő csomag. Röviden, az azonos erőforráscsoport és régió kombinációval létrehozott összes csomag ugyanarra a webtárhelyre van telepítve. Ha például létrehozta az A és A B régióba tartozó csomagot, akkor az A és B régióba később létrehozott összes csomag ugyanarra a webtárhelyre lesz telepítve. Vegye figyelembe, hogy a csomagok létrehozása után nem helyezhetők át a webtárhelyek, így nem helyezhet át egy tervet "ugyanazon a webtárhelyre", mert egy másik erőforráscsoporthoz helyezi át.
> 

1. A [Azure Portal](https://portal.azure.com)navigáljon az áthelyezni kívánt alkalmazáshoz.

1. A menüben keresse meg a **app Service Plan** szakaszt.

1. Válassza a **módosítás app Service a terv** lehetőséget a **app Service tervezési** választó megnyitásához.

   ![App Service csomag kiválasztása.][change] 

1. Az **app Service-csomag** kiválasztása lapon válasszon ki egy meglévő csomagot, amelybe át szeretné helyezni az alkalmazást.   

A **app Service csomag kiválasztása** lapon csak azok a csomagok jelennek meg, amelyek ugyanabban az erőforráscsoporthoz és földrajzi régióban találhatók, mint az aktuális alkalmazás app Service terve.

Minden csomag saját díjszabással rendelkezik. Például, ha egy helyet egy **ingyenes** szintről **standard** csomagra helyez át, akkor minden alkalmazás hozzá van rendelve a **standard** szint funkcióinak és erőforrásainak használatához. Ha azonban egy alkalmazást egy magasabb rétegű csomagból egy alacsonyabb rétegű csomagra helyez át, azt jelenti, hogy már nem fér hozzá bizonyos funkciókhoz. Ha az alkalmazás olyan funkciót használ, amely nem érhető el a célhelyen, akkor hibaüzenet jelenik meg, amely azt mutatja, hogy melyik szolgáltatás nem érhető el. 

Ha például az egyik alkalmazás SSL-tanúsítványokat használ, a következő hibaüzenet jelenhet meg:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

Ebben az esetben, mielőtt az alkalmazást áthelyezi a célként megadott csomagra, a következőkre lesz szüksége:
- A célként megadott csomag díjszabási szintjét alapszintű vagy magasabbra méretezheti.
- Távolítsa el az összes SSL-kapcsolatot az alkalmazással.

## <a name="move-an-app-to-a-different-region"></a>Alkalmazás áthelyezése másik régióba

Az a régió, amelyben az alkalmazás fut, a App Service megtervezni. Azonban nem módosítható App Service csomag régiója. Ha az alkalmazást egy másik régióban szeretné futtatni, az egyik alternatíva az alkalmazások klónozása. A klónozás az alkalmazás egy példányát egy új vagy meglévő App Service tervbe hozza bármely régióban.

A klónozási **alkalmazást** a menü **fejlesztői eszközök** szakaszában találja.

> [!IMPORTANT]
> A klónozás bizonyos korlátozásokkal rendelkezik. Ezekről [Azure app Service alkalmazások klónozásával](app-service-web-app-cloning.md)kapcsolatos információkat olvashat.

## <a name="scale-an-app-service-plan"></a>App Service terv méretezése

Az App Service csomag díjszabási szintjeinek vertikális felskálázásához tekintse meg az alkalmazás vertikális felskálázása [Az Azure-ban](manage-scale-up.md)című témakört.

Az alkalmazások példányszámának felskálázásához tekintse meg a [Példányszám manuális vagy automatikus skálázása](../monitoring-and-diagnostics/insights-how-to-scale.md)című témakört.

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>App Service csomag törlése

Ha el szeretné kerülni a nem várt díjakat, akkor a App Service csomag utolsó alkalmazásának törlésekor App Service a csomagot is alapértelmezés szerint törli. Ha úgy dönt, hogy megtartja a tervet, érdemes módosítania az **ingyenes** szintet, hogy ne kelljen fizetnie.

> [!IMPORTANT]
> Azok a App Service csomagok, amelyeknek nincsenek hozzárendelt alkalmazásai, díjkötelesek, mert továbbra is fenntartják a konfigurált virtuálisgép-példányokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazások vertikális felskálázása az Azure-ban](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png

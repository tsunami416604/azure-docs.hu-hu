---
title: "Azure Cloud Explorer erőforrások kezelése |} Microsoft Docs"
description: "Ismerje meg, hogyan Cloud Explorer segítségével keresse meg és a Visual Studio Azure-erőforrások kezeléséhez."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/25/2017
ms.author: kraigb
ms.openlocfilehash: 6e6d8d559f0251b71bfa6d529ead82a246cb3235
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>A Visual Studio Cloud Explorer Azure fiókokhoz tartozó erőforrások kezelése
Cloud Explorer lehetővé teszi az Azure-erőforrások és csoportok megtekintése, vizsgálja meg a tulajdonságaikról és műveleteket kulcs fejlesztői diagnosztika a Visual studióban. 

Például a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040), Cloud Explorer épül, az Azure Resource Manager-készletben. Ezért Cloud Explorer megértette erőforrások, például Azure erőforráscsoport-sablonok és a Logic Apps alkalmazásokat és API-alkalmazások például az Azure-szolgáltatásokat, valamint a [szerepköralapú hozzáférés-vezérlés](active-directory/role-based-access-control-configure.md) (RBAC). 

## <a name="prerequisites"></a>Előfeltételek
- [A Visual Studio 2017](https://www.visualstudio.com/downloads/) rendelkező a **Azure számítási** kiválasztva, vagy a Visual Studio egy korábbi verzióját a [Microsoft Azure SDK for .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- A Microsoft Azure-fiók – Ha nincs fiókja, akkor [regisztráljon egy ingyenes próbaverzióra](http://go.microsoft.com/fwlink/?LinkId=623901) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Válassza ki, ha Cloud Explorer **nézet** > **Cloud Explorer** menüsávjában.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Adja hozzá az Azure Cloud Explorer fiókot
Az Azure-fiókkal társított erőforrások megtekintéséhez előbb a fiókot kell hozzáadnia a Cloud Explorer. 

1. A **Cloud Explorer**, jelölje be **Azure-fiók beállításai**.

    ![Cloud Explorer Azure fiók beállításainak ikonja](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Válassza ki **új fiókot felveszi**. 

    ![Cloud Explorer-fiók hozzáadása hivatkozás](media/vs-azure-tools-resources-managing-with-cloud-explorer/add-account-link.png)

1. Jelentkezzen be az Azure-fiók amelynek tallózni kívánt erőforrásokat. 

1. Miután bejelentkezett az Azure-fiók, az adott fiókhoz tartozó előfizetések megjelenítéséhez. Válassza ki a kívánt keresse meg és válassza ki a fiók-előfizetések jelölőnégyzeteit **alkalmaz**. 
 
    ![Cloud Explorer: válassza ki az Azure-előfizetések megjelenítéséhez](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. Miután kiválasztotta az előfizetéseket, amelyek tallózni kívánt erőforrásokat, ezen előfizetések és az erőforrások megjelenítéséhez, a Cloud Explorerben.

    ![Cloud Explorer erőforrás Azure-fiókot listázása](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Távolítsa el az Azure-fiók Cloud Explorer 

1. A **Cloud Explorer**, jelölje be **Azure-fiók beállításai**.

    ![Cloud Explorer Azure fiók beállításainak ikonja](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Mellett a fiók el szeretné távolítani, válassza ki a **eltávolítása**.

    ![Cloud Explorer Azure fiók beállításainak ikonja](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

## <a name="view-resource-types-or-resource-groups"></a>Erőforrástípus vagy erőforráscsoportok megtekintésére
Az Azure-erőforrások megtekintéséhez, vagy választhat **erőforrástípusok** vagy **erőforráscsoportok** nézet.

1. A **Cloud Explorer**, válassza ki a erőforrás nézet legördülő listában.

    ![Cloud Explorer legördülő lista segítségével válassza ki a kívánt erőforrások megtekintése](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. A helyi menüből válassza ki a kívánt nézetet: 

    - **Erőforrástípusok** - az nézet közös használt a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040), az Azure-erőforrások kategóriába sorolt, például webes alkalmazásokat, a storage-fiókok és a virtuális gépek típus szerint jeleníti meg. 
    - **Erőforráscsoportok** - által az Azure erőforráscsoport, amellyel fontosságúak társított kategorizálja Azure-erőforrások megtekintése. Egy erőforráscsoportot az Azure-erőforrások, általában egy adott alkalmazás által használt csomag egyik gyermekszoftver. Azure erőforráscsoport-sablonok kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](./azure-resource-manager/resource-group-overview.md).

    Az alábbi ábrán a két erőforrás nézeteinek társítása összehasonlítása:

    ![Cloud Explorer erőforrás nézetek összehasonlítása](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Megtekintheti, és keresse meg a Cloud Explorer erőforrások
Egy Azure-erőforrás keresse meg, és a Cloud Explorer megtekintse az adataikat, bontsa ki az elem típusa vagy kapcsolódó erőforráscsoport, és válassza ki az erőforrás. Amikor kiválaszt egy erőforrást, információ szerepel a két lap - **műveletek** és **tulajdonságok** - Cloud Explorer alján. 

- **Műveletek** lap – felsorolja azokat a műveleteket hajthatja végre a Cloud Explorer a kiválasztott erőforrás. Kattintson a jobb gombbal az erőforrás megtekintéséhez a helyi menüből is megtekintheti ezeket a beállításokat.

- **Tulajdonságok** lap – az erőforrás, például a típusa, területi beállítás és az erőforrás csoport, amelyhez társítva a tulajdonságok láthatók.

Az alábbi ábrán láthatók az egyes lapokon az egy App Service egy példa összehasonlítása:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Minden erőforrás van a művelet **nyissa meg a portál**. Ha úgy dönt, hogy ez a művelet, a Cloud Explorer megjeleníti a kiválasztott erőforrás a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040). A **nyissa meg a portál** jellemzője, ami hasznos lehet, navigáljon a mélyen beágyazott erőforrások.

További műveletek és a tulajdonságértékek is szerepel az Azure erőforrás-alapú. Például webes alkalmazásokat és a logic apps is műveletet kell végrehajtani a **nyissa meg böngészőben** és **Hibakereső csatlakoztatása** kívül **nyissa meg a portál**. Szerkesztő megnyitásához műveletek jelennek meg, ha úgy dönt, hogy a tárolási fiók blob, üzenetsor vagy tábla. Az Azure-alkalmazások is rendelkeznek **URL-cím** és **állapot** tulajdonságok, míg a tárolási erőforrások kulcs és a kapcsolati karakterlánc tulajdonságait.

## <a name="find-resources-in-cloud-explorer"></a>A Cloud Explorer erőforrások keresése
Keresse meg az adott névvel rendelkező erőforrásokat az Azure-fiók-előfizetések, adja meg a nevét a **keresési** Cloud Explorer párbeszédpanel.

![A Cloud Explorer erőforrások keresése](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

A karakterek megadásakor a **keresési** mezőbe csak azok a karakterek megfelelő erőforrások jelennek meg a erőforrás fában.

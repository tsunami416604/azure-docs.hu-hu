---
title: Az Azure-erőforrások felügyelete a Cloud Explorer |} A Microsoft Docs
description: Ismerje meg, hogyan Cloud Explorer használatával megkeresheti és felügyelheti az Azure-erőforrások Visual Studión belül.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/25/2017
ms.author: ghogen
ms.openlocfilehash: 596db56a71d4cc73517b9a9cbfe9b97e536aa83a
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378846"
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>A Visual Studio Cloud Explorer az Azure-fiókokhoz kapcsolódó erőforrások kezelése
Cloud Explorer segítségével is megtekintheti az Azure-erőforrások és erőforráscsoportok, azok tulajdonságait megvizsgálhatja és műveleteket kulcs Fejlesztőeszközök diagnosztika a Visual Studión belül. 

Például a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), Cloud Explorer az Azure Resource Manager-verembe épül. Ezért a Cloud Explorer tisztában van azzal, erőforrások, például az Azure-erőforráscsoportot és az Azure-szolgáltatások, például a Logic apps és API-alkalmazások, és támogatja a [szerepköralapú hozzáférés-vezérlés](role-based-access-control/role-assignments-portal.md) (RBAC). 

## <a name="prerequisites"></a>Előfeltételek
- [A Visual Studio 2017](https://www.visualstudio.com/downloads/) az a **Azure-beli számítási** kiválasztva, vagy a Visual Studio egy korábbi verzióját a [Microsoft Azure SDK for .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- A Microsoft Azure-fiók – Ha nincs fiókja, akkor az [regisztráljon egy ingyenes próbaverzióra](http://go.microsoft.com/fwlink/?LinkId=623901) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Cloud Explorer megtekintéséhez jelölje ki **nézet** > **Cloud Explorer** a menüsávon.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Adjon hozzá egy Azure-fiókját a Cloud Explorer
Az Azure-fiókkal társított erőforrások megtekintéséhez először hozzá kell adnia a fiók a Cloud Explorer. 

1. A **Cloud Explorer**válassza **Azure-fiók beállításai**.

    ![Cloud Explorer Azure fiók beállítások ikon](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Válassza ki **fiókok kezelése**. 

    ![Cloud Explorer-fiók hozzáadása hivatkozás](media/vs-azure-tools-resources-managing-with-cloud-explorer/manage-accounts-link.png)

1. Jelentkezzen be az Azure-fiók amelynek Tallózás kívánt erőforrásokat. 

1. Miután bejelentkezett Azure-fiókba az előfizetések, a fiókhoz társított jelennek meg. Válassza ki a tallózással keresse meg és jelölje ki a kívánt fiók-előfizetések jelölőnégyzeteit **alkalmaz**. 
 
    ![Cloud Explorer: válassza ki az Azure-előfizetések megjelenítése](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. Miután kijelölte az előfizetéseket, amelyek átnézni kívánt erőforrások, ezek előfizetésekre és erőforrásokra megjelenítése a Cloud Explorer.

    ![A cloud Explorer erőforrás Azure-fiók listázása](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>A Cloud Explorer az Azure-fiók eltávolítása 

1. A **Cloud Explorer**válassza **fiókkezelés**.

    ![Cloud Explorer Azure fiók beállítások ikon](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. El kívánja távolítani a fiókot, mellett válassza ki a **fiókok kezelése**.

    ![Cloud Explorer Azure fiók beállítások ikon](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

1. Válasszon **eltávolítása** eltávolítani egy fiókot.

    ![Cloud Explorer kezelése fiókok párbeszédpanel](media/vs-azure-tools-resources-managing-with-cloud-explorer/accountmanage.PNG)

## <a name="view-resource-types-or-resource-groups"></a>Típusú erőforrások vagy erőforráscsoportok megtekintése
Az Azure-erőforrások megtekintéséhez, vagy választhat **erőforrástípusok** vagy **erőforráscsoportok** megtekintése.

1. A **Cloud Explorer**, válassza ki az erőforrás legördülő menüjére.

    ![Cloud Explorer legördülő listában válassza ki a kívánt erőforrások megtekintése](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. A helyi menüből válassza ki a kívánt nézetet: 

    - **Erőforrástípusok** megtekintése – a használt gyakori nézet a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), az Azure-erőforrások például a web apps, a storage-fiókok és a virtual machines típusuk szerint osztályozva megjeleníti. 
    - **Erőforráscsoportok** - a, amellyel még társított Azure-erőforráscsoport szerint kategorizálja a Azure-erőforrások megtekintése. Egy erőforráscsoport egy Azure-erőforrások, általában egy adott alkalmazás által használt csomagot. Azure erőforráscsoportokkal kapcsolatos további tudnivalókért lásd: [Azure Resource Manager áttekintése](./azure-resource-manager/resource-group-overview.md).

    Az alábbi képen látható a két erőforrás nézeteinek társítása összehasonlítása:

    ![A cloud Explorer erőforrás nézetek összehasonlítása](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Megtekintheti, és keresse meg a Cloud Explorer erőforrások
Keresse meg az Azure-erőforrás és az információk a Cloud Explorer megtekintéséhez bontsa ki az elem típusa vagy társított erőforráscsoportot, és válassza ki az erőforrást. Amikor kiválaszt egy erőforrást, az információk megjelennek-e a két lap – **műveletek** és **tulajdonságok** – a Cloud Explorer alján. 

- **Műveletek** lap – felsorolja azokat a műveleteket, elvégezhető a kiválasztott erőforrás a Cloud Explorerben. Kattintson a jobb gombbal az erőforrás megtekintéséhez a helyi menüből is megtekintheti ezeket a beállításokat.

- **Tulajdonságok** lap – például a típus, nyelv és az erőforrás csoportból, amelyhez társítva az erőforrás tulajdonságait mutatja.

Az alábbi képen egy példa összehasonlítása, amit lát az egyes lapokon az App Service-ben:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Minden erőforrás rendelkezik a művelet **Megnyitás a portálon**. Ha úgy dönt, hogy ez a művelet, a Cloud Explorer jeleníti meg a kiválasztott erőforrás a a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). A **Megnyitás a portálon** a funkció a mélyen beágyazott erőforrások navigáláshoz praktikus.

További műveleteket és tulajdonságértékeket is megjelenhetnek az Azure-erőforrások alapján. Például web apps és logic Apps-alkalmazások is rendelkeznek a műveletek **nyissa meg böngészőben** és **Hibakereső csatlakoztatása** mellett **Megnyitás a portálon**. Nyissa meg a szerkesztőknek a műveletek jelennek meg, ha úgy dönt, hogy egy storage-fiók blob, üzenetsor vagy tábla. Az Azure-alkalmazások **URL-cím** és **állapot** tulajdonságok, míg a tárolási erőforrások kulcs és a kapcsolati karakterlánc tulajdonságai.

## <a name="find-resources-in-cloud-explorer"></a>A Cloud Explorer erőforrások keresése
Az Azure-fiókba az előfizetések adott névvel rendelkező erőforrások megkereséséhez írja be a nevét a **keresési** Cloud Explorer párbeszédpanel.

![A Cloud Explorer erőforrások keresése](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

A karakterek beírásával a **keresési** mezőben csak azok a karakterek megfelelő erőforrások jelennek meg az erőforrás-fában.

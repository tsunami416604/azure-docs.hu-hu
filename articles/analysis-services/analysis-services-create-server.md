---
title: Egy Analysis Services-kiszolgáló létrehozása az Azure-ban |} Microsoft Docs
description: 'Útmutató: az Analysis Services server-példány létrehozása az Azure-ban.'
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c910416524f149c785aae299d576ca5c521abc6d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>Az Azure Analysis Services-kiszolgáló létrehozása az Azure-portálon
Ez a cikk végigvezeti egy Analysis Services-kiszolgáló erőforrás létrehozása az Azure-előfizetéshez.

## <a name="before-you-begin"></a>Előkészületek
A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* **Azure-előfizetés**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/).
* **Az Azure Active Directory**: az előfizetéshez kell tartoznia az Azure Active Directory-bérlő. És meg kell jelentkeznie az Azure-bA egy olyan fiókkal, az adott Azure Active Directoryban. Microsoft-fiókok nem támogatottak. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).
* **Erőforráscsoport**: már van erőforráscsoport használata vagy [hozzon létre egy újat](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> A kiszolgáló létrehozása új számlázható szolgáltatás létrejöttét eredményezheti. További tudnivalókért lásd: [Analysis Services-díjszabás](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="to-create-a-server-in-the-azure-portal"></a>A kiszolgáló létrehozása az Azure-portálon
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. Kattintson a **+ új** > **adatok + analitika** > **Analysis Services**.
3. Az a **Analysis Services** panelen adja meg a kötelező mezőket, és nyomja le az **létrehozása**.
   
    ![Kiszolgáló létrehozása](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Kiszolgálónév**: Adjon meg egy egyedi nevet mutató hivatkozás a kiszolgálón.
   * **Előfizetés**: Jelölje ki az ehhez a kiszolgálóhoz való váltók stb.
   * **Erőforráscsoport**: ezek a tárolók úgy tervezték, hogy az Azure erőforrások kezeléséhez nyújt segítséget. További tudnivalókért lásd: [erőforráscsoportok](../azure-resource-manager/resource-group-overview.md).
   * **Hely**: az Azure-adatközpont hely üzemelteti a kiszolgálót. Válasszon a legnagyobb felhasználói bázis legközelebbi helyet.
   * **IP-címek**: tarifacsomag kiválasztása. Táblázatos modellek legfeljebb 400 GB támogatottak. További tudnivalókért lásd: [Azure Analysis Services díjszabása](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Kattintson a **Create** (Létrehozás) gombra.

Hozzon létre egy percet; általában tesz gyakran csak néhány másodpercig. Ha a kiválasztott **portálon**, keresse meg a portálhoz, és az új kiszolgáló. Vagy keresse meg **minden szolgáltatás** > **Analysis Services** megjelenítéséhez, ha a kiszolgáló készen áll-e.

 ![Irányítópult](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>További lépések
Miután létrehozta a kiszolgáló, [a modell rendszerbe állítása](analysis-services-deploy.md) hozzá SSDT vagy ssms alkalmazásával.

A modell központi telepítése a kiszolgálóra a helyi adatforrások csatlakozik, ha telepíteni szeretné egy [helyszíni adatátjáró](analysis-services-gateway.md) a hálózat egyik számítógépén.


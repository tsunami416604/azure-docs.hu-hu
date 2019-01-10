---
title: Adjon hozzá egy egyszerű szolgáltatást az Azure Analysis Services kiszolgálói rendszergazdai szerepkörhöz |} A Microsoft Docs
description: Az automation szolgáltatás egyszerű felvétele a kiszolgálói rendszergazdai szerepkörhöz
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5089083862f243a6a6847a58ac0777c52f978e73
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189728"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Szolgáltatásnév hozzáadása kiszolgálói rendszergazdai szerepkörhöz 

 Felügyelet nélküli PowerShell-feladatok automatizálásához, rendelkeznie kell egy egyszerű szolgáltatást **kiszolgáló-rendszergazda** jogosultságokkal az Analysis Services-kiszolgáló felügyelete alatt áll. Ez a cikk ismerteti egy egyszerű szolgáltatás hozzáadása egy Azure-AS kiszolgálón a kiszolgáló-rendszergazdák szerepkörhöz.

## <a name="before-you-begin"></a>Előkészületek
Ez a feladat befejezése, előtt rendelkeznie kell egy egyszerű szolgáltatást az Azure Active Directoryban.

[Szolgáltatásnév létrehozása – Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Szolgáltatásnév létrehozása – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Szükséges engedélyek
Ez a feladat végrehajtásához rendelkeznie kell [kiszolgáló-rendszergazda](analysis-services-server-admins.md) engedélyeket az Azure-AS kiszolgálón. 

## <a name="add-service-principal-to-server-administrators-role"></a>Szolgáltatásnév hozzáadása kiszolgálói rendszergazdák szerepkörhöz

1. Az ssms-ben az Azure AS-kiszolgálóhoz csatlakozhat.
2. A **kiszolgálótulajdonságok** > **biztonsági**, kattintson a **Hozzáadás**.
3. A **egy felhasználó vagy csoport kiválasztása**, keresse meg a regisztrált alkalmazás nevét, válassza ki, és kattintson a **Hozzáadás**.

    ![Keresse meg a szolgáltatásnévhez tartozó fiók](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ellenőrizze az egyszerű szolgáltatásfiók azonosítója, és kattintson a **OK**.
    
    ![Keresse meg a szolgáltatásnévhez tartozó fiók](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Kiszolgálói műveletek AzureRm-parancsmagok használatával, a szolgáltatás egyszerű futó scheduler kell tartoznia a **tulajdonosa** erőforráshoz tartozó szerepkör [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Kapcsolódó információk

* [Az SQL Server PowerShell-modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Töltse le az ssms használatával](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   



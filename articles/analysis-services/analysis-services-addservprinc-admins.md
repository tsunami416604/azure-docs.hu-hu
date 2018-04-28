---
title: Adja hozzá egy egyszerű Azure Analysis Services kiszolgálói rendszergazda szerepkör |} Microsoft Docs
description: Ismerje meg az automation szolgáltatás egyszerű felvétele a rendszergazdai szerepkör
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f1cc563cc13a9102dbdac7bd505b4dd844ff8247
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Egy egyszerű szolgáltatás hozzáadása a kiszolgáló-rendszergazdai szerepkör 

 Felügyelet nélküli PowerShell feladatok automatizálásához, rendelkeznie kell egy egyszerű **rendszergazda** jogosultsággal az Analysis Services-kiszolgáló felügyelete alatt áll. Ez a cikk ismerteti a szolgáltatás egyszerű felvétele az Azure-AS-kiszolgáló a kiszolgálói Rendszergazdák szerepkör.

## <a name="before-you-begin"></a>Előkészületek
Ez a feladat befejezése előtt rendelkeznie kell egy egyszerű szolgáltatás regisztrálva az Azure Active Directoryban.

[Hozzon létre egyszerű szolgáltatásnév - Azure-portálon](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Szolgáltatásnév - PowerShell létrehozása](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Szükséges engedélyek
A feladat végrehajtásához rendelkeznie kell [rendszergazda](analysis-services-server-admins.md) engedélyeket az Azure-AS kiszolgálón. 

## <a name="add-service-principal-to-server-administrators-role"></a>Egyszerű szolgáltatás kiszolgáló-Rendszergazdák szerepkör hozzáadása

1. Az SSMS a Azure AS-kiszolgálóhoz való csatlakozás.
2. A **kiszolgálótulajdonságok** > **biztonsági**, kattintson a **Hozzáadás**.
3. A **egy felhasználó vagy csoport kiválasztása**, keresse meg a regisztrált alkalmazás neve, válassza ki, és kattintson a **Hozzáadás**.

    ![Keresse meg az egyszerű szolgáltatás fiókja](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ellenőrizze a szolgáltatás egyszerű fiók Azonosítóját, és kattintson **OK**.
    
    ![Keresse meg az egyszerű szolgáltatás fiókja](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> A kiszolgáló műveleteket AzureRm-parancsmagok használatával, egyszerű futó Feladatütemező szolgáltatás is tartoznia kell a **tulajdonos** az erőforrás-szerepkör [átruházásához hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Kapcsolódó információk

* [SQL-kiszolgáló PowerShell-modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Töltse le a szolgáltatáshoz az SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   



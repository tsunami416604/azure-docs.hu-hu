---
title: "Adja hozzá a szolgáltatás funkciókat az Azure Analysis Services kiszolgálói rendszergazda szerepkör |} Microsoft Docs"
description: "Ismerje meg az automation szolgáltatás elv felvétele a rendszergazdai szerepkör"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: owend
ms.openlocfilehash: 8e51b80e184b2b1ff24b1051b55088fbc54c271c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="add-a-service-principle-to-the-server-administrator-role"></a>A szolgáltatás funkciókat az a kiszolgáló-rendszergazdai szerepkör hozzáadása 

 Felügyelet nélküli PowerShell feladatok automatizálásához, rendelkeznie kell a szolgáltatás funkciókat **rendszergazda** jogosultsággal az Analysis Services-kiszolgáló felügyelete alatt áll. Ez a cikk ismerteti a service funkciókat felvétele az Azure-AS-kiszolgáló a kiszolgálói Rendszergazdák szerepkör.

## <a name="before-you-begin"></a>Előkészületek
Ez a feladat befejezése előtt rendelkeznie kell a szolgáltatás funkciókat az Azure Active Directoryban regisztrálva.

[Hozzon létre szolgáltatás elv - Azure-portálon](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Szolgáltatás elv - PowerShell létrehozása](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Szükséges engedélyek
A feladat végrehajtásához rendelkeznie kell [rendszergazda](analysis-services-server-admins.md) engedélyeket az Azure-AS kiszolgálón. 

## <a name="add-service-principle-to-server-administrators-role"></a>Kiszolgáló-Rendszergazdák szerepkör szolgáltatás elv hozzáadása

1. Az SSMS a Azure AS-kiszolgálóhoz való csatlakozás.
2. A **kiszolgálótulajdonságok** > **biztonsági**, kattintson a **Hozzáadás**.
3. A **egy felhasználó vagy csoport kiválasztása**, keresse meg a regisztrált alkalmazás neve, válassza ki, és kattintson a **Hozzáadás**.

    ![Szolgáltatásfiók elv keresése](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ellenőrizze a szolgáltatás elv fiók Azonosítóját, és kattintson **OK**.
    
    ![Szolgáltatásfiók elv keresése](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> A kiszolgáló műveleteket AzureRm-parancsmagok használatával, fut, a Feladatütemező szolgáltatás elv is tartoznia kell a **tulajdonos** az erőforrás-szerepkör [átruházásához hozzáférés-vezérlés (RBAC)](../active-directory/role-based-access-control-what-is.md). 

## <a name="related-information"></a>Kapcsolódó információk

* [SQL-kiszolgáló PowerShell-modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Töltse le a szolgáltatáshoz az SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   



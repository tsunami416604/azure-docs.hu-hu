---
title: Egyszerű szolgáltatásnév hozzáadása Azure Analysis Services kiszolgálói rendszergazdai szerepkörhöz | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá automatizálási szolgáltatásnevet a kiszolgálói rendszergazdai szerepkörhöz
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e4650d3be5ce21a49b419577eaf83225b1c0d4d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298714"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Egyszerű szolgáltatásnév hozzáadása a kiszolgáló-rendszergazdai szerepkörhöz 

 A felügyelet nélküli PowerShell-feladatok automatizálásához egy egyszerű szolgáltatásnak **rendszergazdai** jogosultságokkal kell rendelkeznie a felügyelt Analysis Services kiszolgálón. Ez a cikk azt ismerteti, hogyan adhat hozzá egy egyszerű szolgáltatást egy Azure-beli kiszolgáló-rendszergazdák szerepkörhöz.

## <a name="before-you-begin"></a>Előzetes teendők
A feladat elvégzése előtt rendelkeznie kell egy Azure Active Directoryban regisztrált egyszerű szolgáltatással.

[Egyszerű szolgáltatásnév létrehozása – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Szolgáltatásnév létrehozása – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Szükséges engedélyek
A feladat elvégzéséhez [kiszolgálói rendszergazdai](analysis-services-server-admins.md) engedélyekkel kell rendelkeznie az Azure-ban kiszolgálóként. 

## <a name="add-service-principal-to-server-administrators-role"></a>Egyszerű szolgáltatásnév hozzáadása a kiszolgálói rendszergazdák szerepkörhöz

1. A SSMS-ben kapcsolódjon az Azure-hoz kiszolgálóként.
2. A **kiszolgáló tulajdonságai** > **Biztonság**területen kattintson a **Hozzáadás**gombra.
3. A **felhasználó vagy csoport kiválasztása**területen keresse meg a regisztrált alkalmazást név szerint, válassza ki, majd kattintson a **Hozzáadás**gombra.

    ![Egyszerű szolgáltatásnév-fiók keresése](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ellenőrizze az egyszerű szolgáltatás fiókjának AZONOSÍTÓját, majd kattintson **az OK**gombra.
    
    ![Egyszerű szolgáltatásnév-fiók keresése](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Azure PowerShell parancsmagokat használó kiszolgálói műveletek esetében az ütemező szolgáltatást futtató szolgáltatásnak az [Azure szerepköralapú Access Control (RBAC)](../role-based-access-control/overview.md)erőforrás **tulajdonosi** szerepköréhez is tartoznia kell. 

## <a name="related-information"></a>Kapcsolódó információk

* [SQL Server PowerShell-modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   



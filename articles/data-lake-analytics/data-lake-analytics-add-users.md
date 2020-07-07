---
title: Felhasználók hozzáadása egy Azure Data Lake Analytics-fiókhoz
description: Megtudhatja, hogyan adhat hozzá felhasználókat a Data Lake Analytics-fiókhoz a felhasználó hozzáadása varázsló és a Azure PowerShell használatával.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "71672728"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Felhasználó hozzáadása az Azure Portalon

## <a name="start-the-add-user-wizard"></a>A felhasználó hozzáadása varázsló elindítása
1. Nyissa meg a Azure Data Lake Analyticst a használatával https://portal.azure.com .
2. Kattintson a **felhasználó hozzáadása varázsló**elemre.
3. A **felhasználó kiválasztása** lépésben keresse meg azt a felhasználót, akit hozzá kíván adni. Kattintson a **Kiválasztás** gombra.
4. a **szerepkör kiválasztása lépésben válassza ki** **Data Lake Analytics fejlesztőt**. Ez a szerepkör az U-SQL-feladatok elküldéséhez/figyeléséhez vagy kezeléséhez szükséges engedélyek minimális készletét határozza meg. Rendeljen hozzá ehhez a szerepkörhöz, ha a csoport nem az Azure-szolgáltatások kezelésére szolgál.
5. A **katalógus engedélyeinek kiválasztása** lépésben válassza ki azokat az adatbázisokat, amelyekre a felhasználónak hozzá kell férnie. A feladatok elküldéséhez olvasási és írási hozzáférés szükséges a Master adatbázishoz. Amikor elkészült, kattintson az **OK** gombra.
6. A **kiválasztott engedélyek hozzárendelésének** utolsó lépésében tekintse át a varázsló által végrehajtott módosításokat. Kattintson az **OK** gombra.


## <a name="configure-acls-for-data-folders"></a>Hozzáférés-vezérlési listák konfigurálása adatmappákhoz
Szükség szerint "R-X" vagy "RWX" megadása a bemeneti adatokat és a kimeneti adatokat tartalmazó mappákon.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Szükség esetén vegye fel a felhasználót a Azure Data Lake Storage Gen1 szerepkör- **olvasó** szerepkörbe.
1.  Keresse meg Azure Data Lake Storage Gen1-fiókját.
2.  Kattintson a **Felhasználók** elemre.
3. Kattintson a **Hozzáadás** parancsra.
4.  Válasszon ki egy Azure RBAC-szerepkört a csoport hozzárendeléséhez.
5.  Hozzárendelés az olvasó szerepkörhöz. Ez a szerepkör a ADLSGen1-ben tárolt adatkereséshez és kezeléshez szükséges minimális engedélyekkel rendelkezik. Rendeljen hozzá ehhez a szerepkörhöz, ha a csoport nem az Azure-szolgáltatások kezelésére szolgál.
6.  Írja be a csoport nevét.
7.  Kattintson az **OK** gombra.

## <a name="adding-a-user-using-powershell"></a>Felhasználó hozzáadása a PowerShell használatával

1. Kövesse a jelen útmutató utasításait: [Azure PowerShell telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Töltse le a [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell-szkriptet.
3. Futtassa a PowerShell-szkriptet. 

Az a minta parancs, amellyel felhasználói hozzáférést biztosíthat a feladatok elküldéséhez, megtekintheti az új feladatok metaadatait, és megtekintheti a régi metaadatokat:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [A Data Lake Analytics használatának első lépései a Azure Portal](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics kezelése Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)


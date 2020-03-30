---
title: Felhasználók hozzáadása Azure Data Lake Analytics-fiókhoz
description: Megtudhatja, hogyan adhat hozzá felhasználókat a Data Lake Analytics-fiókhoz a Felhasználó hozzáadása varázsló és az Azure PowerShell használatával.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672728"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Felhasználó hozzáadása az Azure Portalon

## <a name="start-the-add-user-wizard"></a>A Felhasználó hozzáadása varázsló indítása
1. Nyissa meg az Azure https://portal.azure.comData Lake Analytics-et a segítségével.
2. Kattintson **a Felhasználó hozzáadása varázsló gombra.**
3. A **Felhasználó kiválasztása** lépésben keresse meg a hozzáadni kívánt felhasználót. Kattintson a **Kiválasztás** gombra.
4. a **Szerepkör kiválasztása** lépés, válassza a Data Lake Analytics **Developer**lehetőséget. Ez a szerepkör rendelkezik az U-SQL-feladatok elküldéséhez/figyeléséhez/kezeléséhez szükséges minimális engedélykészletekkel. Rendeljen ehhez a szerepkörhöz, ha a csoport nem azure-szolgáltatások kezelésére szolgál.
5. A **Katalógusengedélyek kiválasztása** lépésben válassza ki azokat a további adatbázisokat, amelyekhez a felhasználónak hozzáférésre van szüksége. Olvasási és írási hozzáférés a fő adatbázishoz szükséges a feladatok elküldéséhez. Amikor elkészült, kattintson az **OK** gombra.
6. Az utolsó lépésben a **Kijelölt engedélyek hozzárendelése** tekintse át a varázsló által végrehajtott módosításokat. Kattintson az **OK** gombra.


## <a name="configure-acls-for-data-folders"></a>A cl-k konfigurálása adatmappákhoz
Szükség szerint adja meg az "R-X" vagy "RWX" parancsot a bemeneti adatokat és kimeneti adatokat tartalmazó mappákon.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Szükség esetén adja hozzá a felhasználót az Azure Data Lake Storage Gen1 szerepkör **olvasó szerepkörolvasó** szerepkörhöz.
1.  Keresse meg az Azure Data Lake Storage Gen1 fiókját.
2.  Kattintson a **Felhasználók** elemre.
3. Kattintson a **Hozzáadás** gombra.
4.  Válasszon ki egy Azure RBAC szerepkört a csoport hozzárendeléséhez.
5.  Hozzárendelés az Olvasó szerepkörhöz. Ez a szerepkör rendelkezik az ADLSGen1-ben tárolt adatok tallózásához/kezeléséhez szükséges minimális engedélykészletekkel. Rendeljen ehhez a szerepkörhöz, ha a csoport nem azure-szolgáltatások kezelésére szolgál.
6.  Írja be a csoport nevét.
7.  Kattintson az **OK** gombra.

## <a name="adding-a-user-using-powershell"></a>Felhasználó hozzáadása a PowerShell használatával

1. Kövesse az útmutató utasításait: [Az Azure PowerShell telepítése és konfigurálása.](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
2. Töltse le az [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell-parancsfájlt.
3. Futtassa a PowerShell-parancsfájlt. 

A mintaparancs, amely hozzáférést biztosít a felhasználóknak a feladatok elküldéséhez, az új feladat metaadatainak megtekintéséhez és a régi metaadatok megtekintéséhez:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Ismerkedés a Data Lake Analytics szolgáltatással az Azure Portal használatával](data-lake-analytics-get-started-portal.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)


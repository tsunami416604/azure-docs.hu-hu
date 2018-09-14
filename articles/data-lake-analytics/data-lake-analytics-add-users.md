---
title: Felhasználók hozzáadása az Azure Data Lake Analytics-fiókkal
description: Ismerje meg, hogyan megfelelően a Data Lake Analytics-fiók a felhasználók hozzáadása
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: f48dc07e27c6cb01a842f1f6d720ed6476028ef7
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542312"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Felhasználók hozzáadása az Azure Portalon

## <a name="start-the-add-user-wizard"></a>Indítsa el a felhasználó hozzáadása varázsló
1. Nyissa meg az Azure Data Lake Analytics-n keresztül https://portal.azure.com.
2. Kattintson a **felhasználó hozzáadása varázsló**.
3. Az a **felhasználó kiválasztása** lépést, a hozzáadni kívánt felhasználó található. Kattintson a **Kiválasztás** gombra.
4. a **szerepkör kiválasztása** lépést, válasszon **Data Lake Analytics-fejlesztő**. Ez a szerepkör rendelkezik a minimális U-SQL-feladatok elküldése és felügyelet/kezelés szükséges engedélyeket. Rendelje hozzá ehhez a szerepkörhöz, ha a csoport nem alkalmas az Azure-szolgáltatások kezeléséhez.
5. Az a **katalógusengedélyek kiválasztása** lépést, minden további adatbázisok válassza ki, hogy a felhasználó hozzá kell férniük. Olvasási és írási hozzáférés a master adatbázishoz szükséges feladatok elküldéséhez. Ha elkészült, kattintson az **OK** gombra.
6. Az utolsó lépésben nevű **kijelölt engedélyek hozzárendelése** tekintse át a varázsló elvégzi a módosításokat. Kattintson az **OK** gombra.


## <a name="configure-acls-for-data-folders"></a>Adatok mappákra vonatkozó hozzáférés-vezérlési listák konfigurálása
"Az R-X" vagy "RWX", adja meg a bemeneti adatokat tartalmazó mappák igény szerint, és a kimeneti adatokat.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Opcionálisan adja hozzá a felhasználót az Azure Data Lake Storage Gen1 szerepkör **olvasó** szerepkör.
1.  Az Azure Data Lake Storage Gen1 fiók található.
2.  Kattintson a **felhasználók**.
3. Kattintson a **Hozzáadás** parancsra.
4.  Válassza ki az Azure RBAC szerepkör hozzárendelése ehhez a csoporthoz.
5.  Olvasó szerepkört rendelni. Ez a szerepkör rendelkezik a minimális ADLSGen1 tárolt adatok tallózással keresse meg és kezeléséhez szükséges engedélyeket. Rendelje hozzá ehhez a szerepkörhöz, ha a csoport nem alkalmas az Azure-szolgáltatások kezeléséhez.
6.  Írja be a csoport nevét.
7.  Kattintson az **OK** gombra.

## <a name="adding-a-user-using-powershell"></a>Hozzáadja a PowerShell használatával

1. Kövesse a jelen útmutató: [telepítése és konfigurálása az Azure PowerShell-lel](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Töltse le a [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell-parancsfájlt.
3. PowerShell-szkript futtatásához. 

A mintául szolgáló parancs felhasználói hozzáférésének küldhetők be feladatok, új feladat-metaadatok és a régi metaadatai nézet megtekintéséhez:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Data Lake Analytics használatának első lépései az Azure portal használatával](data-lake-analytics-get-started-portal.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)


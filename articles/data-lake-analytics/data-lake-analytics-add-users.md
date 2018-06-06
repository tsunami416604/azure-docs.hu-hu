---
title: Felhasználók hozzáadása az Azure Data Lake Analytics-fiókkal
description: Megtudhatja, hogyan megfelelően adhat hozzá felhasználókat a Data Lake Analytics-fiók
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 58b78c7d9769069f36c9f01c2a7650878a6c5ec9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34717229"
---
# <a name="adding-a-user-in-the-azure-portal"></a>A felhasználó hozzáadása az Azure-portálon

## <a name="start-the-add-user-wizard"></a>Indítsa el a felhasználó hozzáadása varázsló
1. Nyissa meg az Azure Data Lake Analytics keresztül https://portal.azure.com.
2. Kattintson a **felhasználó hozzáadása varázsló**.
3. Az a **felhasználó kijelölése** . lépés:, keresse meg a hozzáadni kívánt felhasználó. Kattintson a **Kiválasztás** gombra.
4. a **válassza szerepkör** . lépés:, válasszon **Data Lake Analytics fejlesztői**. Ez a szerepkör be van állítva a minimális U-SQL feladatok küldje el figyelő/felügyeletéhez szükséges engedélyeket. Ha a csoport nem az Azure szolgáltatások kezelésére szolgáló hozzárendelése ehhez a szerepkörhöz.
5. Az a **katalógus engedélyként válassza** . lépés:, válassza ki a további adatbázisokat a felhasználónak kell hozzáférést. Olvasási és írási hozzáférést a fő adatbázishoz szükséges feladatok küldéséhez. Ha elkészült, kattintson az **OK** gombra.
6. Az utolsó lépésben nevű **rendelje hozzá a kijelölt engedélyeket** áttekintheti a változtatásokat, a varázsló elvégzi. Kattintson az **OK** gombra.


## <a name="configure-acls-for-data-folders"></a>Hozzáférés-vezérlési listák beállítása adatmappáinak
Adja meg "R-X" vagy "RWX" igény szerint a bemeneti adatokat tartalmazó mappák és a kimeneti adatai.


## <a name="optionally-add-the-user-to-the-azure-data-lake-store-role-reader-role"></a>Szükség esetén vegye fel a felhasználót az Azure Data Lake Store szerepkör **olvasó** szerepkör.
1.  Az Azure Data Lake Store-fiók található.
2.  Kattintson a **felhasználók**.
3. Kattintson a **Hozzáadás** parancsra.
4.  Válassza ki az Azure RBAC szerepkört ehhez a csoporthoz hozzárendelni.
5.  Olvasó szerepkört rendelni. Ez a szerepkör be van állítva a minimális ADLS tárolt adatok Tallózás/felügyeletéhez szükséges engedélyeket. Ha a csoport nem az Azure szolgáltatások kezelésére szolgáló hozzárendelése ehhez a szerepkörhöz.
6.  Írja be a csoport nevét.
7.  Kattintson az **OK** gombra.

## <a name="adding-a-user-using-powershell"></a>A PowerShell használatával felhasználó hozzáadása

1. Kövesse az utasításokat az útmutató: [telepítése és konfigurálása az Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Töltse le a [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell-parancsfájlt.
3. A PowerShell-parancsprogrammal. 

A felhasználói hozzáférésének elküldeni a feladatokat, a minta parancs új feladat metaadatok és a régi metaadat-nézet megjelenítése:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Ismerkedés a Data Lake Analytics az Azure portál használatával](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)


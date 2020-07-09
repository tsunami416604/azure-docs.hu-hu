---
title: Azure Migrate-projektek létrehozása és kezelése
description: Projektek keresése, létrehozása, kezelése és törlése Azure Migrateban.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: 644ac5ecab9f5384b446c3b1ebe762a5d06aaf12
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113428"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate-projektek létrehozása és kezelése

Ez a cikk bemutatja, hogyan hozhat létre, kezelhet és törölhet [Azure Migrate](migrate-services-overview.md) projekteket.


## <a name="create-a-project-for-the-first-time"></a>Projekt létrehozása első alkalommal

A Azure Migrate első beállításakor létre kell hoznia egy projektet, és hozzá kell adnia egy értékelési vagy áttelepítési eszközt. [Kövesse az alábbi utasításokat](how-to-add-tool-first-time.md) az első beállításához.

## <a name="create-additional-projects"></a>További projektek létrehozása

Ha már rendelkezik Azure Migrate-projekttel, és szeretne egy további projektet létrehozni, tegye a következőket:  

1. Az [Azure nyilvános portálon](https://portal.azure.com) vagy [Azure Government](https://portal.azure.us)keresse meg a **Azure Migrate**.
2. A Azure Migrate irányítópulton > **kiszolgálók**lapon válassza a **módosítás** lehetőséget a jobb felső sarokban.

   ![Azure Migrate projekt módosítása](./media/create-manage-projects/switch-project.png)

3. Új projekt létrehozásához válassza a **kattintson ide**.

   ![Második Azure Migrate projekt létrehozása](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Projekt keresése

A következőképpen kereshet egy projektet:

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Migrate**.
2. A Azure Migrate irányítópulton > **kiszolgálók**lapon válassza a **módosítás** lehetőséget a jobb felső sarokban.

    ![Váltás meglévő Azure Migrate projektre](./media/create-manage-projects/switch-project.png)

3. Válassza ki a megfelelő előfizetést és Azure Migrate projektet.


Ha a projektet a Azure Migrate [előző verziójában](migrate-services-overview.md#azure-migrate-versions) hozta létre, a következőképpen keresheti meg:

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Migrate**.
2. Ha a Azure Migrate-irányítópulton létrehozott egy projektet az előző verzióban, megjelenik egy régebbi projektekre hivatkozó szalagcím. Válassza ki a szalagcímet.

    ![Meglévő projektek elérése](./media/create-manage-projects/access-existing-projects.png)

3. Tekintse át a régi projektek listáját.


## <a name="delete-a-project"></a>Projekt törlése

A következőképpen törölheti:

1. Nyissa meg azt az Azure-erőforráscsoportot, amelyben a projekt létrejött.
2. Az erőforráscsoport lapon válassza a **rejtett típusok megjelenítése**elemet.
3. Válassza ki a törölni kívánt áttelepíthető projektet és a hozzá tartozó erőforrásokat.
    - Az erőforrástípus a **Microsoft. migrál/migrateprojects**.
    - Ha az erőforráscsoportot kizárólag az Azure Migrate-projekt használja, akkor törölheti a teljes erőforráscsoportot.


Vegye figyelembe:

- A törléskor a rendszer a projekt és a felderített gépek metaadatait is törli.
- Ha a Azure Migrate régebbi verzióját használja, nyissa meg azt az Azure-erőforráscsoportot, amelyben a projekt létrejött. Válassza ki a törölni kívánt áttelepítési projektet (az erőforrás típusa **áttelepítési projekt**).
- Ha függőségi elemzést használ egy Azure Log Analytics-munkaterülettel:
    - Ha Log Analytics munkaterületet csatolt a kiszolgáló-értékelési eszközhöz, a munkaterület nem törlődik automatikusan. Ugyanaz a Log Analytics munkaterület több forgatókönyv esetén is használható.
    - Ha törölni szeretné a Log Analytics munkaterületet, ezt manuálisan végezze el.

### <a name="delete-a-workspace-manually"></a>Munkaterület törlése manuálisan

1. Tallózással keresse meg a projekthez csatolt Log Analytics munkaterületet.

    - Ha nem törölte a Azure Migrate projektet, a munkaterületre mutató hivatkozást az **Essentials**-  >  **kiszolgáló értékelése**területen találja.
       ![LA munkaterület ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Ha már törölte a Azure Migrate projektet, válassza ki az **erőforráscsoportok** elemet a Azure Portal bal oldali ablaktábláján, és keresse meg a munkaterületet.
       
2. A munkaterület törléséhez [kövesse az utasításokat](../azure-monitor/platform/delete-workspace.md) .

## <a name="next-steps"></a>Következő lépések

[Felmérési](how-to-assess.md) vagy [áttelepítési](how-to-migrate.md) eszközök hozzáadása Azure Migrate projektekhez.

---
title: Azure Migrate-projektek létrehozása és kezelése
description: Projekteket kereshet, hozhat létre, kezelhet és törölhet az Azure Migrate szolgáltatásban.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: a49595f0580e71048239d5c5d8f4d1a66e24fe6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269639"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate-projektek létrehozása és kezelése

Ez a cikk ismerteti, hogyan hozhat létre, kezelhet és [törölheti az Azure Migrate](migrate-services-overview.md) projekteket.


## <a name="create-a-project-for-the-first-time"></a>Projekt létrehozása első alkalommal

Az Azure Migrate első beállításakor létrehoz egy projektet, és hozzáad egy értékelési vagy áttelepítési eszközt. [Kövesse ezeket](how-to-add-tool-first-time.md) az utasításokat az első beállításhoz.

## <a name="create-additional-projects"></a>További projektek létrehozása

Ha már rendelkezik egy Azure Migrate projekttel, és további projektet szeretne létrehozni, tegye a következőket:  

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Migrate**című kifejezést.
2. Az Azure Áttelepítési irányítópulton > **kiszolgálók,** válassza a **változás** a jobb felső sarokban.

   ![Az Azure Migrate projekt módosítása](./media/create-manage-projects/switch-project.png)

3. Új projekt létrehozásához **kattintson ide.**

   ![Második Azure Migrate projekt létrehozása](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Projekt keresése

Keressen egy projektet az alábbiak szerint:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Migrate**című kifejezést.
2. Az Azure Migrate irányítópulton > **kiszolgálók,** válassza a **változás** a jobb felső sarokban.

    ![Váltás meglévő Azure Migrate projektre](./media/create-manage-projects/switch-project.png)

3. Válassza ki a megfelelő előfizetést és az Azure Migrate projektet.


Ha a projektet az Azure Migrate [korábbi verziójában](migrate-services-overview.md#azure-migrate-versions) hozta létre, keresse meg a következőképpen:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Migrate**című kifejezést.
2. Ha az előző verzióban létrehozott egy projektet, megjelenik egy régebbi projektekre hivatkozó szalagcím. Válassza ki a szalagcímet.

    ![Meglévő projektek elérése](./media/create-manage-projects/access-existing-projects.png)

3. Tekintse át a régi projektek listáját.


## <a name="delete-a-project"></a>Projekt törlése

A következő rész törlendő:

1. Nyissa meg azt az Azure-erőforráscsoportot, amelyben a projektet létrehozták.
2. Az erőforráscsoport lapon válassza a **Rejtett típusok megjelenítése**lehetőséget.
3. Jelölje ki a törölni kívánt projekt et és a hozzá tartozó erőforrásokat.
    - Az erőforrás típusa: **Microsoft.Migrate/migrateprojects**.
    - Ha az erőforráscsoportot kizárólag az Azure Migrate projekt használja, törölheti a teljes erőforráscsoportot.


Vegye figyelembe:

- Ha törli a programot, a projekt és a felderített gépek metaadatai is törlődnek.
- Ha az Azure Migrate régebbi verzióját használja, nyissa meg azt az Azure-erőforráscsoportot, amelyben a projektet létrehozták. Jelölje ki a törölni kívánt áttelepítési projektet (az erőforrás típusa **Áttelepítési projekt**).
- Ha függőségi elemzést használ egy Azure Log Analytics-munkaterülettel:
    - Ha a Kiszolgálóértékelés eszközhöz csatolt egy Log Analytics-munkaterületet, a munkaterület nem törlődik automatikusan. Ugyanaz a Log Analytics-munkaterület több forgatókönyvhöz is használható.
    - Ha törölni szeretné a Log Analytics munkaterületet, ezt manuálisan kell elvégeznie.

### <a name="delete-a-workspace-manually"></a>Munkaterület manuális törlése

1. Tallózással keresse meg a projekthez csatolt Log Analytics munkaterületet.

    - Ha még nem törölte az Azure Áttelepítés projektet, megtalálhatja a munkaterületre mutató hivatkozást az **Essentials** > **Server Assessment című területen.**
       ![LA munkaterület](./media/create-manage-projects/loganalytics-workspace.png).
       
    - Ha már törölte az Azure Migrate projektet, válassza az **Erőforráscsoportok lehetőséget** az Azure Portal bal oldali ablaktáblájában, és keresse meg a munkaterületet.
       
2. A munkaterület törléséhez kövesse az [utasításokat.](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)

## <a name="next-steps"></a>További lépések

[Értékelési](how-to-assess.md) vagy [áttelepítési](how-to-migrate.md) eszközök hozzáadása az Azure Áttelepítési projektekhez.

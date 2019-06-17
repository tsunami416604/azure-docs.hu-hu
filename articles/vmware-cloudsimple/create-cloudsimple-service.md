---
title: Azure VMware megoldás által CloudSimple - szolgáltatás létrehozása
description: Ismerteti, hogyan lehet létrehozni a CloudSimple szolgáltatást az Azure Portalon
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fc08ce48b3b5a4b2cd28cb66e6dd4d9eae3f3885
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676953"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Azure VMware-megoldás által CloudSimple - szolgáltatás létrehozása

Első lépésként az Azure VMware megoldás CloudSimple, hozzon létre az Azure VMware megoldás CloudSimple szolgáltatás az Azure Portalon.

> [!NOTE]
> Mielőtt létrehozna a CloudSimple szolgáltatást, regisztrálnia kell Microsoft.VMwareCloudSimple erőforrás-szolgáltató az Azure-előfizetésében. Kövesse a [engedélyezése az Azure-előfizetésében Microsoft.VMwareCloudSimple erőforrás-szolgáltató](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg **CloudSimple szolgáltatások**.

    ![Search CloudSimple Service](media/create-cloudsimple-service-search.png)

3. Válassza ki **CloudSimple szolgáltatások**.

4. Kattintson a **Hozzáadás** új szolgáltatás létrehozása.

    ![Add CloudSimple Service](media/create-cloudsimple-service-add.png)

5. Válassza ki az előfizetést, ahol szeretné létrehozni a CloudSimple szolgáltatást.

6. Válassza ki az erőforráscsoportot, a szolgáltatás. Egy új erőforráscsoport hozzáadásához kattintson **hozzon létre új**.

7. Adja meg a szolgáltatás azonosítására szolgáló név.

8. Adja meg a CIDR a felhőszolgáltatási átjárót. Adjon meg egy/28-as alhálózatot, amelyet nem lehetnek átfedésben azokkal a meglévő alhálózat.  Ezek közé tartozik a helyszíni alhálózatokról, Azure alhálózatok, vagy bármilyen tervezett CloudSimple alhálózatokat. A CIDR a szolgáltatás létrehozása után nem módosítható.

    ![A CloudSimple szolgáltatás létrehozása](media/create-cloudsimple-service.png)

9. Kattintson az **OK** gombra.

A szolgáltatás létrehozása és hozzáadni a szolgáltatások listájában.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [magánfelhő létrehozása](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Ismerje meg, hogyan [egy magánfelhő-környezet konfigurálása](quickstart-create-private-cloud.md)

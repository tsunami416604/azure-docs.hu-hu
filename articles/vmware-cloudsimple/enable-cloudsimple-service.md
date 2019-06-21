---
title: Azure VMware megoldás engedélyezése CloudSimple szolgáltatás
description: Ismerteti, hogyan lehet engedélyezni a CloudSimple szolgáltatást az Azure-előfizetéssel, és regisztrálhatja a CLoudSimple erőforrás-szolgáltató
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154862"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Az Azure-előfizetésében Microsoft.VMwareCloudSimple erőforrás-szolgáltató regisztrálása

A CloudSimple szolgáltatás lehetővé teszi, hogy Azure VMware megoldás által CloudSimple felhasználásához. Az erőforrás-szolgáltató, a Microsoft.VMwareCloudSimple szolgáltatás regisztrálására.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza az **Összes szolgáltatás** elemet.

3. Keresse meg és válassza **előfizetések**.

    ![Keresés az előfizetések között](media/cloudsimple-service-select-subscriptions.png)

4. Válassza ki az előfizetést, amelyen szeretné engedélyezni, CloudSimple szolgáltatás.

5. Kattintson a **erőforrás-szolgáltatók** az előfizetéshez.

6. Használat **Microsoft.VMwareCloudSimple** az erőforrás-szolgáltató szűrése.

7. Válassza ki az erőforrás-szolgáltató, és kattintson a **regisztrálása**.

    ![Erőforrás-szolgáltató regisztrálása](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [CloudSimple szolgáltatás létrehozása](create-cloudsimple-service.md)
* Ismerje meg, hogyan [egy magánfelhő-környezet konfigurálása](quickstart-create-private-cloud.md)

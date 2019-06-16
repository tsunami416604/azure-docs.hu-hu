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
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676938"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Az Azure-előfizetésében Microsoft.VMwareCloudSimple erőforrás-szolgáltató regisztrálása

A CloudSimple szolgáltatás lehetővé teszi, hogy Azure VMware megoldás által CloudSimple felhasználásához. A CloudSimple szolgáltatás használatához először engedélyezni kell az Azure-előfizetésében. Az erőforrás-szolgáltató, majd a Microsoft.VMwareCloudSimple szolgáltatás is regisztrálhat.

## <a name="enable-the-cloudsimple-service"></a>A CloudSimple szolgáltatás engedélyezése

Az Azure-előfizetésében CloudSimple szolgáltatás engedélyezéséhez nyissa meg egy támogatási kérést az [a Microsoft támogatási](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Válassza ki a következő beállításokat, a kérelem elküldésekor.

* Probléma típusa: **Technikai**
* Előfizetés: **Az előfizetés-azonosító**
* Szolgáltatás típusa: **VMware megoldást CloudSimple szerint**
* Probléma típusa: **Dedikált csomópontok kvóta**
* A probléma altípus: **Növelje meg a dedikált csomópontok**
* Tulajdonos: **CloudSimple szolgáltatás engedélyezése**

Is kapcsolatba léphet a következő Microsoft-ügyfélmenedzserhez [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com). Adja meg az e-mailben az Azure-előfizetés azonosítója.  

Miután a CloudSimple szolgáltatás engedélyezve van az előfizetés, az erőforrás-szolgáltatót az előfizetésben engedélyezheti.

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
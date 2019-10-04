---
title: Azure Red Hat OpenShift-fürt rendszergazdai szerepköre | Microsoft Docs
description: Az Azure Red Hat OpenShift-fürt rendszergazdai szerepkörének hozzárendelése és használata
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936931"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift ügyfél-rendszergazdai szerepkör

Ön egy Azure Red Hat OpenShift-fürt Fürtfelügyelő. A fiókja nagyobb engedélyekkel rendelkezik, és minden felhasználó által létrehozott projekthez férhet hozzá.

Ha a fiókja rendelkezik az OSA-Customer-admin engedélyezési szerepkörrel, akkor automatikusan felügyelheti a projekteket.

> [!Note] 
> Az OSA-Customer-admin fürt szerepköre nem egyezik meg a fürt – rendszergazda fürt szerepkörével.


Például végrehajthat olyan műveleteket, amelyek egy adott művelethez (`create`) vannak társítva, hogy az erőforrás-nevek (`templates`) alapján működjenek. Az alábbi parancs futtatásával tekintheti meg a szerepkörök és a hozzájuk tartozó műveletek és erőforrások készletének részleteit:

`$ oc describe clusterrole/osa-customer-admin`

A műveletek nevei nem feltétlenül minden leképezést közvetlenül `oc` parancsokra mutatnak. Ezek általánosságban az elvégezhető CLI-műveletek típusaihoz hasonlítanak. 

Például ha a `list` művelet azt jelenti, hogy megjelenítheti egy erőforrás neve (`oc get`) összes objektumának listáját. A `get` művelet azt jelenti, hogy megjelenítheti egy adott objektum részleteit, ha ismeri a nevét (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Az ügyfél-rendszergazdai szerepkör konfigurálása

Az ügyfél-rendszergazdai szerepkört csak a fürt létrehozása során állíthatja be úgy, hogy megadja a `--customer-admin-group-id` jelzőt. A Azure Active Directory és a rendszergazdák csoport konfigurálásának megismeréséhez tekintse meg az [Azure Red Hat OpenShift való Azure Active Directory integrációját](howto-aad-app-configuration.md)ismertető témakört.

## <a name="next-steps"></a>További lépések

Az OSA-Customer-admin szerepkör konfigurálása:
> [!div class="nextstepaction"]
> [Azure Active Directory integráció az Azure Red Hat OpenShift](howto-aad-app-configuration.md)

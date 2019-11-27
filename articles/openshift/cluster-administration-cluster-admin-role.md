---
title: Azure Red Hat OpenShift-fürt rendszergazdai szerepköre | Microsoft Docs
description: Az Azure Red Hat OpenShift-fürt rendszergazdai szerepkörének hozzárendelése és használata
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 0cb875122c63be18f7c39cdfea7986d705ed434e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539277"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift ügyfél-rendszergazdai szerepkör

Ön egy Azure Red Hat OpenShift-fürt Fürtfelügyelő. A fiókja nagyobb engedélyekkel rendelkezik, és minden felhasználó által létrehozott projekthez férhet hozzá.

Ha a fiókja az ügyfél-rendszergazda-fürt engedélyezési szerepkörrel van kötve, akkor automatikusan felügyelheti a projekteket.

> [!Note] 
> Az ügyfél-rendszergazda-fürt szerepkör nem azonos a fürt – rendszergazda fürt szerepkörével.


Például végrehajthat műveleteket egy adott művelethez társított műveletek végrehajtásához (`create`) az erőforrásnevek (`templates`) egy készletén való működéshez. Az alábbi parancs futtatásával tekintheti meg a szerepkörök és a hozzájuk tartozó műveletek és erőforrások készletének részleteit:

`$ oc get clusterroles customer-admin-cluster -o yaml`

A műveletek nevei nem feltétlenül minden leképezést közvetlenül `oc` parancsokra mutatnak. Ezek általánosságban az elvégezhető CLI-műveletek típusaihoz hasonlítanak. 

A `list` művelettel például azt jelenti, hogy megjelenítheti egy erőforrás neve (`oc get`) összes objektumának listáját. A `get` művelet azt jelenti, hogy megjelenítheti egy adott objektum részleteit, ha ismeri a nevét (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Az ügyfél-rendszergazdai szerepkör konfigurálása

Az ügyfél-rendszergazda fürt szerepkört csak a fürt létrehozása során állíthatja be úgy, hogy megadja a jelző `--customer-admin-group-id`. A Azure Active Directory és a rendszergazdák csoport konfigurálásának megismeréséhez tekintse meg az [Azure Red Hat OpenShift való Azure Active Directory integrációját](howto-aad-app-configuration.md)ismertető témakört.

## <a name="next-steps"></a>Következő lépések

Az ügyfél-rendszergazda-fürt szerepkör konfigurálása:
> [!div class="nextstepaction"]
> [Azure Active Directory integráció az Azure Red Hat OpenShift](howto-aad-app-configuration.md)

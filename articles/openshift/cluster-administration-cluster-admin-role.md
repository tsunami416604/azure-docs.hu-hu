---
title: Azure Red Hat OpenShift-fürt rendszergazdai szerepköre | Microsoft Docs
description: Az Azure Red Hat OpenShift-fürt rendszergazdai szerepkörének hozzárendelése és használata
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79139096"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift ügyfél-rendszergazdai szerepkör

Ön egy Azure Red Hat OpenShift-fürt Fürtfelügyelő. A fiókja nagyobb engedélyekkel rendelkezik, és minden felhasználó által létrehozott projekthez férhet hozzá.

Ha a fiókja az ügyfél-rendszergazda-fürt engedélyezési szerepkörrel van kötve, akkor automatikusan felügyelheti a projekteket.

> [!Note] 
> Az ügyfél-rendszergazda-fürt szerepkör nem azonos a fürt – rendszergazda fürt szerepkörével.

Például végrehajthat műveleteket egy adott művelethez társított műveletek végrehajtásához (`create`), hogy az erőforrás-nevek (`templates`) készletén működjenek. Az alábbi parancs futtatásával tekintheti meg a szerepkörök és a hozzájuk tartozó műveletek és erőforrások készletének részleteit:

`$ oc get clusterroles customer-admin-cluster -o yaml`

A műveletek nevei nem feltétlenül minden leképezést közvetlenül `oc` a parancsokra mutatnak. Ezek általánosságban az elvégezhető CLI-műveletek típusaihoz hasonlítanak. 

Például a `list` művelet azt jelenti, hogy megjelenítheti egy erőforrás neve (`oc get`) összes objektumának listáját. A `get` művelet azt jelenti, hogy megjelenítheti egy adott objektum részleteit, ha ismeri a nevét (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Az ügyfél-rendszergazdai szerepkör konfigurálása

Az ügyfél-rendszergazda fürt szerepkört csak a fürt létrehozása során állíthatja be a jelző `--customer-admin-group-id`megadásával. Ez a mező jelenleg nem konfigurálható a Azure Portalban. A Azure Active Directory és a rendszergazdák csoport konfigurálásának megismeréséhez tekintse meg az [Azure Red Hat OpenShift való Azure Active Directory integrációját](howto-aad-app-configuration.md)ismertető témakört.

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Tagság megerősítése az ügyfél-rendszergazdai szerepkörben

Ha szeretné megerősíteni a tagságát a Customer admin csoportban, próbálja ki `oc get nodes` a `oc projects`OpenShift CLI-parancsokat vagy a következőt:. `oc get nodes`a csomópontok listáját jeleníti meg, ha az ügyfél-rendszergazda-fürt szerepkörrel rendelkezik, és ha csak az ügyfél-rendszergazda projekt szerepkörrel rendelkezik. `oc projects`Megjeleníti a fürtben lévő összes projektet, és nem csupán azokat a projekteket, amelyeken dolgozik.

A szerepkörök és engedélyek további megismeréséhez a fürtben használhatja az [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) parancsot.

## <a name="next-steps"></a>További lépések

Az ügyfél-rendszergazda-fürt szerepkör konfigurálása:
> [!div class="nextstepaction"]
> [Azure Active Directory integráció az Azure Red Hat OpenShift](howto-aad-app-configuration.md)

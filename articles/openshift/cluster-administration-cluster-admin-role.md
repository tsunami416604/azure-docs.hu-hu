---
title: Azure Red Hat OpenShift fürtrendszergazdai szerepkör | Microsoft dokumentumok
description: Az Azure Red Hat OpenShift fürtrendszergazdai szerepkör hozzárendelése és használata
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139096"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift ügyfélrendszergazdai szerepkör

Ön egy Azure Red Hat OpenShift-fürt fürtrendszergazdája. Fiókja megnövelte az engedélyeket és hozzáférést az összes felhasználó által létrehozott projekthez.

Ha a fiók hoz a customer-admin-cluster engedélyezési szerepkör takarja, akkor automatikusan kezelheti a projektet.

> [!Note] 
> Az ügyfél-rendszergazda-fürt fürt szerepkör nem ugyanaz, mint a fürt-rendszergazda fürt szerepkör.

Végrehajthatja például az igék (`create`) készletéhez társított műveleteket, hogy`templates`erőforrásnevek ( ) készletén működjön. A szerepkörök részleteinek, valamint az igék és erőforrások készletének megtekintéséhez futtassa a következő parancsot:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Az igenevek nem feltétlenül `oc` felelnek meg közvetlenül a parancsoknak. Általánosabban megegyeznek a CLI-műveletek végrehajtható típusával. 

Ha például `list` az ige megvan, az azt jelenti,`oc get`hogy megjelenítheti az erőforrásnév ( objektumainak listáját . ). Az `get` ige azt jelenti, hogy megjelenítheti egy adott`oc describe`objektum részleteit, ha ismeri a nevét ( ).

## <a name="configure-the-customer-administrator-role"></a>Az ügyfélrendszergazdai szerepkör konfigurálása

Az ügyfél-rendszergazda-fürt fürtszerepkört csak a fürt létrehozása `--customer-admin-group-id`során konfigurálhatja a jelző megadásával. Ez a mező jelenleg nem konfigurálható az Azure Portalon. Az Azure Active Directory és a Rendszergazdák csoport konfigurálásáról az [Azure Red Hat OpenShift Azure Active Directory-integráció című témakörben olvashat.](howto-aad-app-configuration.md)

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Tagság megerősítése az ügyfél-rendszergazdai szerepkörben

Az ügyfélfelügyeleti csoport tagságának megerősítéséhez próbálkozzon az `oc get nodes` `oc projects`OpenShift CLI parancsokkal, vagy a lehetőséggel. `oc get nodes`a csomópontok listáját jeleníti meg, ha rendelkezik az ügyfél-admin-fürt szerepkörrel, és egy engedélyhibát, ha csak az ügyfél-rendszergazda-projekt szerepkörrel rendelkezik. `oc projects`a fürt összes projektjét megmutatja, nem csak azokkal a projektekkel, amelyeken éppen dolgozik.

A fürt szerepköreinek és engedélyeinek további [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) feltárásához használja a parancsot.

## <a name="next-steps"></a>További lépések

Konfigurálja az ügyfél-rendszergazda-fürt fürtszerepkört:
> [!div class="nextstepaction"]
> [Azure Active Directory-integráció az Azure Red Hat OpenShift szolgáltatáshoz](howto-aad-app-configuration.md)

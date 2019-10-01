---
title: Azure Red Hat OpenShift-fürt rendszergazdai szerepköre | Microsoft Docs
description: Azure Red Hat OpenShift-fürt rendszergazdai szerepkörének hozzárendelése és használata
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709952"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift ügyfél-rendszergazdai szerepkör

Ha egy OpenShift-fürt ARO (Azure Red Hat OpenShift) fürtjének rendszergazdája, a fiókja megnövelte az engedélyeket, és hozzáfér minden felhasználó által létrehozott projekthez.

Ha a fiókja rendelkezik az OSA-Customer-admins engedélyezési szerepkörrel, akkor automatikusan felügyelheti a projekteket.

> [!Note] 
> OSA – az ügyfél és a rendszergazda clusterrole nem ugyanaz, mint a fürt – rendszergazda clusterrole


Például végrehajthat olyan műveleteket, amelyek egy adott művelethez (`create`) vannak társítva, hogy az erőforrás-nevek (`templates`) alapján működjenek. Az alábbi parancs futtatásával tekintheti meg a szerepkörök és a hozzájuk tartozó műveletek és erőforrások készletének részleteit:

`$ oc describe clusterrole/osa-customer-admin`

A műveletek nevei nem feltétlenül közvetlenül a oC-parancsokra mutatnak, hanem inkább az elvégezhető CLI-műveletek típusaihoz hasonlítanak. Ha például a `list` művelet azt jelenti, hogy egy adott erőforrás neve (`oc get`) összes objektumának listáját jeleníti meg, a `get` művelet pedig azt jelenti, hogy megjelenítheti egy adott objektum részleteit, ha ismeri a nevét (`oc describe`).

## <a name="how-to-configure-customer-administrator-role"></a>Az ügyfél-rendszergazdai szerepkör konfigurálása

Az ügyfél-rendszergazdai szerepkör csak a fürt létrehozása során konfigurálható úgy, hogy `--customer-admin-group-id` jelzőt biztosít. A Azure Active Directory és a rendszergazdák csoport konfigurálása az útmutató lépéseinek követéséhez: [Azure Active Directory integráció az Azure Red Hat OpenShift](howto-aad-app-configuration.md)

## <a name="next-steps"></a>További lépések

Az OSA-Customer-admin szerepkör konfigurálása:
> [!div class="nextstepaction"]
> [Azure Active Directory integráció az Azure Red Hat OpenShift](howto-aad-app-configuration.md)

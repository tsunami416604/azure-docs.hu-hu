---
title: Hozzáférés az Azure VMware Solutions (AVS)-portálhoz
description: Ismerteti, hogyan érheti el az Azure VMware Solutions (AVS) alkalmazást a Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015950"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Azure VMware-megoldások (AVS) elérése a Azure Portal

Az egyszeri bejelentkezés támogatott az AVS-portálhoz való hozzáféréshez. Miután bejelentkezett a Azure Portalba, a következő bejelentkezés nélkül érheti el az AVS-portált. Amikor először fér hozzá az AVS-portálhoz, a rendszer felszólítja az [AVS szolgáltatás engedélyezési](#consent-to-avs-service-authorization-application) alkalmazásának engedélyezésére. Az engedélyezés egyszeri művelet.

## <a name="before-you-begin"></a>Előzetes teendők

A beépített **tulajdonosi** és **közreműködői** szerepkörökkel rendelkező felhasználók hozzáférhetnek az AVS-portálhoz. A szerepköröket az AVS szolgáltatást telepítő erőforráscsoporthoz kell konfigurálni. A szerepkörök az AVS szolgáltatás objektumán is konfigurálhatók. A szerepkör ellenőrzésével kapcsolatos további információkért tekintse meg a [szerepkör-hozzárendelések megtekintése](https://docs.microsoft.com/azure/role-based-access-control/check-access) című cikket. Csak a beépített **tulajdonosi** és **közreműködői** szerepkörökkel rendelkező felhasználók férhetnek hozzá az AVS-portálhoz. A szerepköröket az előfizetésen kell konfigurálni. A szerepkör ellenőrzésével kapcsolatos további információkért tekintse meg a [szerepkör-hozzárendelések megtekintése](https://docs.microsoft.com/azure/role-based-access-control/check-access) című cikket.

Ha egyéni szerepköröket használ, a szerepkörnek a ```Actions```alatt a következő műveletek bármelyikével kell rendelkeznie.  Az egyéni szerepkörökkel kapcsolatos további információkért lásd: [Egyéni szerepkörök az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Ha a műveletek bármelyike ```NotActions```részét képezi, a felhasználó nem férhet hozzá az AVS-portálhoz. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-avs-portal"></a>Hozzáférés az AVS-portálhoz

1. Válassza az **Összes szolgáltatás** elemet.

2. Keressen rá az **AVS-szolgáltatások**kifejezésre.

3. Válassza ki azt az AVS-szolgáltatást, amelyen létre szeretné hozni a saját Felhőjét.

4. Az **Áttekintés** lapon kattintson az **Ugrás az AVS-portálra**elemre. Ha első alkalommal éri el az AVS-Azure Portal portált, a rendszer felszólítja az [AVS szolgáltatás engedélyezési](#consent-to-avs-service-authorization-application) alkalmazásának engedélyezésére. 

    ![Az AVS-portál elindítása](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Ha kijelöl egy privát Felhőbeli műveletet (például egy privát felhő létrehozását vagy kibővítését) közvetlenül a Azure Portal, megnyílik az AVS-portál a jelzett oldalon.

Az AVS-portálon válassza az oldal menü **Kezdőlap** elemét az AVS Private Cloud-ra vonatkozó összegző információk megjelenítéséhez. Megjelenik az AVS Private Cloud erőforrásai és kapacitása, valamint a riasztások és a beavatkozást igénylő feladatok. A gyakori feladatokhoz kattintson az oldal tetején található elnevezett ikonokra.

![Kezdőlap](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Hozzájárulás az AVS szolgáltatás engedélyezési alkalmazásához

Ha első alkalommal indítja el az AVS-portált a Azure Portal, az AVS szolgáltatás-engedélyezési alkalmazáshoz kell beleegyeznie. Válassza az **elfogadás** lehetőséget a kért engedélyek megadásához és az AVS-portál eléréséhez.

![Hozzájárulás az AVS szolgáltatás engedélyezéséhez – rendszergazdák](media/cloudsimple-azure-consent.png)

Ha globális rendszergazdai jogosultsággal rendelkezik, akkor Ön hozzájárulhat a szervezetéhez. Válassza **a jóváhagyva lehetőséget a szervezet nevében**.

![Hozzájárulás az AVS szolgáltatás engedélyezéséhez – globális rendszergazda](media/cloudsimple-azure-consent-global-admin.png)

Ha az engedélyei nem engedélyezik a hozzáférést az AVS-portálhoz, forduljon a bérlő globális rendszergazdájához, és adja meg a szükséges engedélyeket. A globális rendszergazda hozzájárulhat a szervezet nevében.

![Hozzájárulás az AVS szolgáltatás engedélyezéséhez – rendszergazdai jogosultságok szükségesek](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [hozhat létre saját felhőt](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Ismerje meg, hogyan [konfigurálhatja a saját felhőalapú környezetét](quickstart-create-private-cloud.md)

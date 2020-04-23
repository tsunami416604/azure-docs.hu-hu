---
title: Access Azure VMware-megoldás a CloudSimple által – Portál
description: Leírja, hogyan érhető el a VMware Solution by CloudSimple portálja az Azure Portalról
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869334"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>A VMware-megoldás elérése a CloudSimple portálon keresztül az Azure Portalról

Egyszeri bejelentkezés a CloudSimple portál eléréséhez támogatott. Miután bejelentkezett az Azure Portalra, a CloudSimple portált anélkül érheti el, hogy újra bejelentkezne. A CloudSimple portál első elérésekor a rendszer kéri, hogy engedélyezze a [CloudSimple szolgáltatásengedélyezési](#consent-to-cloudsimple-service-authorization-application) alkalmazást.  Az engedélyezés egyszeri művelet.

## <a name="before-you-begin"></a>Előkészületek

A beépített **tulajdonosi** és **közreműködői** szerepkörrel rendelkező felhasználók hozzáférhetnek a CloudSimple portálhoz.  A szerepköröket azon az erőforráscsoporton kell konfigurálni, ahol a CloudSimple szolgáltatás telepítve van.  A szerepkörök is konfigurálhatók a CloudSimple szolgáltatás objektum.  A szerepkör ellenőrzéséről a [Szerepkör-hozzárendelések megtekintése](https://docs.microsoft.com/azure/role-based-access-control/check-access) című cikkben olvashat bővebben. Csak a beépített **tulajdonosi** és **közreműködői** szerepkörrel rendelkező felhasználók férhetnek hozzá a CloudSimple portálhoz.  A szerepköröket konfigurálni kell az előfizetésen.  A szerepkör ellenőrzéséről a [Szerepkör-hozzárendelések megtekintése](https://docs.microsoft.com/azure/role-based-access-control/check-access) című cikkben olvashat bővebben.

Ha egyéni szerepköröket használ, a szerepkörnek az ```Actions```alábbi műveletek bármelyikével kell rendelkeznie a területen.  Az egyéni szerepkörökről az [Egyéni szerepkörök az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)című témakörben talál további információt.  Ha a műveletek bármelyike része ```NotActions```a, a felhasználó nem tud hozzáférni a CloudSimple portálhoz.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg a **CloudSimple szolgáltatásokat.**

3. Válassza ki azt a CloudSimple szolgáltatást, amelyen létre szeretné hozni a magánfelhőt.

4. Az **Áttekintés** lapon kattintson **az Ugrás a CloudSimple portálra**elemre.  Ha első alkalommal éri el a CloudSimple portált az Azure Portalról, a rendszer kéri, hogy engedélyezze a [CloudSimple szolgáltatásengedélyezési](#consent-to-cloudsimple-service-authorization-application) alkalmazást. 

    ![A CloudSimple portál elindítása](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Ha közvetlenül az Azure Portalról választ ki egy magánfelhő-műveletet (például egy magánfelhő létrehozását vagy bővítését), a CloudSimple portál megnyílik a megjelölt oldalon.

A CloudSimple portálon válassza az oldalsó menü **Kezdőlap** parancsát a privát felhők összesítő adatainak megjelenítéséhez. A privát felhők erőforrásai és kapacitása, valamint a figyelmet igénylő riasztások és feladatok láthatók. Gyakori feladatok esetén kattintson a lap tetején található elnevezett ikonokra.

![Kezdőlap](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Hozzájárulás a CloudSimple szolgáltatásengedélyezési alkalmazáshoz

A CloudSimple portál első indítása az Azure Portalról az Ön beleegyezését igényli a CloudSimple szolgáltatásengedélyezési alkalmazáshoz.  Válassza **az Elfogadás** lehetőséget a kért engedélyek megadásához és a CloudSimple portál eléréséhez.

![Hozzájárulás a CloudSimple szolgáltatás engedélyezéséhez - rendszergazdák](media/cloudsimple-azure-consent.png)

Ha globális rendszergazdai jogosultsággal rendelkezik, beleegyezhet a szervezetbe.  Válassza a **Jóváhagyás a szervezet nevében** lehetőséget.

![Hozzájárulás a CloudSimple szolgáltatás engedélyezéséhez – globális rendszergazda](media/cloudsimple-azure-consent-global-admin.png)

Ha az engedélyek nem engedélyezik a hozzáférést a CloudSimple portálhoz, lépjen kapcsolatba a bérlő globális rendszergazdájával a szükséges engedélyek megadásához.  A globális rendszergazda a szervezet nevében is beleegyezhet.

![Hozzájárulás a CloudSimple szolgáltatás engedélyezéséhez – rendszergazdáknak](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>További lépések

* További információ a [magánfelhő létrehozásáról](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/)
* További információ a [magánfelhő-környezet konfigurálásáról](quickstart-create-private-cloud.md)

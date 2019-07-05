---
title: Hozzáférés Azure VMware-megoldások CloudSimple – portál
description: Ismerteti, hogyan lehet VMware megoldás eléréséhez az Azure Portalról CloudSimple portál
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 61c3d37f365034984231c780199e181872c010c6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544133"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Az Azure Portalról CloudSimple portál által a VMware megoldás elérése

Egyszeri bejelentkezés támogatása a CloudSimple portáljához való hozzáférésre. Az Azure Portalra való bejelentkezés után hozzáférhet a CloudSimple portál anélkül, hogy jelentkezzen be újra. Az első alkalommal kéri, hogy engedélyezze CloudSimple portál eléréséhez a [CloudSimple szolgáltatás engedélyezésével](#consent-to-cloudsimple-service-authorization-application) alkalmazás.  Az engedélyezés egy egyszeri művelet történik.

## <a name="before-you-begin"></a>Előkészületek

A beépített rendelkező felhasználók **tulajdonosa** és **közreműködői** szerepkörök elérheti CloudSimple portált.  A szerepkörök kell konfigurálni az erőforráscsoport CloudSimple szolgáltatás telepítési helyét.  A szerepkörök is megadható a CloudSimple service objektum.  A szerepkör ellenőrzése a további információkért lásd: [szerepkör-hozzárendelések megtekintése](https://docs.microsoft.com/azure/role-based-access-control/check-access) cikk.

Ha egyéni szerepköröket használ, a szerepkör kell alatt a következő műveletek bármelyike ```Actions```.  Egyéni szerepkörökkel kapcsolatos további információkért lásd: [egyéni szerepkörök az Azure-erőforrások](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Ha műveletek egy része ```NotActions```, a felhasználó nem férhet hozzá a CloudSimple portálon. 

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

2. Keresse meg **CloudSimple szolgáltatások**.

3. Válassza ki a CloudSimple szolgáltatást, amelyen szeretné létrehozni a Magánfelhő.

4. Az a **áttekintése** kattintson **a CloudSimple portálon**.  Ha első alkalommal próbál hozzáférni a CloudSimple portálon az Azure Portalról, kérni fogja engedélyezni a [CloudSimple szolgáltatás engedélyezésével](#consent-to-cloudsimple-service-authorization-application) alkalmazás. 

    ![Indítsa el a CloudSimple portál](media/launch-cloudsimple-portal.png)

> [!TIP]
> Ha kiválaszt egy Magánfelhő-művelet (például létrehozása vagy bővítése Magánfelhő) közvetlenül az Azure Portalról, a CloudSimple portálon a jelzett oldal jelenik meg.

Válassza a CloudSimple portál **kezdőlap** a oldalsó menüben a Magánfelhők összegző információit jeleníti meg. Az erőforrások és a Magánfelhők kapacitását jelennek meg, valamint riasztások és a figyelmet igénylő feladatok. Gyakori feladatokat kattintson a lap tetején az elnevezett ikonok.

![Kezdőlap](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Hozzájárulás az alkalmazáshoz CloudSimple szolgáltatás engedélyezése

Ön hozzájárul az CloudSimple szolgáltatás engedélyezési alkalmazás első indítását a CloudSimple portálon az Azure Portalon kell rendelkeznie.  Válassza ki **elfogadás** kért engedélyeket és a CloudSimple portál eléréséhez. 

![Beleegyezik abba CloudSimple szolgáltatás engedélyezése - rendszergazdák](media/cloudsimple-azure-consent.png)

Ha globális rendszergazdai jogosultsággal rendelkezik, a hagyhatja jóvá a szervezet számára.  Válassza ki **hozzájárul a szervezet nevében**.

![Beleegyezik abba CloudSimple szolgáltatás engedélyezése - globális rendszergazda](media/cloudsimple-azure-consent-global-admin.png)

Ha az engedélyek nem teszi lehetővé a CloudSimple portáljához való hozzáférésre, lépjen kapcsolatba a szükséges engedélyek megadására a bérlő globális rendszergazdája.  A globális rendszergazdák adhatnak hozzájárulást a szervezet nevében.

![Beleegyezik abba CloudSimple szolgáltatás engedélyezése – a rendszergazdák igényel](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [magánfelhő létrehozása](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Ismerje meg, hogyan [egy magánfelhő-környezet konfigurálása](quickstart-create-private-cloud.md)

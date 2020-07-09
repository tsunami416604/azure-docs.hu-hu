---
title: Az Azure VMware-megoldás kibontása a CloudSimple Private Cloud használatával
description: Ismerteti, hogyan bővíthető egy meglévő CloudSimple-felhő a kapacitás meglévő vagy új fürthöz való hozzáadásához
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77025299"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>CloudSimple privát felhő kibontása

A CloudSimple rugalmasságot biztosít a privát felhők dinamikus kibővítéséhez. Ha nagyobb kapacitásra van szüksége, kezdjen egy kisebb konfigurációval, majd bontsa ki a csomópontot. Vagy létrehozhat egy saját felhőt a jelenlegi igények alapján, majd kiterjesztheti a felhasználás növekedését.

A privát felhő egy vagy több vSphere-fürtből áll. Minden fürthöz 3 – 16 csomópont tartozhat.  A privát felhő bővítésekor csomópontokat kell hozzáadnia a meglévő fürthöz, vagy létre kell hoznia egy új fürtöt. Meglévő fürt kibontásához a további csomópontoknak ugyanolyan típusúnak (SKU) kell lenniük, mint a meglévő csomópontoknak. Új fürt létrehozásához a csomópontok eltérő típusúak lehetnek. A saját Felhőbeli korlátokkal kapcsolatos további információkért lásd: a [CloudSimple privát felhő áttekintő](cloudsimple-private-cloud.md) cikk korlátozásai szakasza.

A privát felhőt a vCenter alapértelmezett **adatközpontja** hozza létre.  Minden adatközpont legfelső szintű felügyeleti entitásként működik.  Új fürt esetén a CloudSimple lehetőséget biztosít a meglévő adatközponthoz való hozzáadásra vagy egy új adatközpont létrehozására.

Az új fürtkonfiguráció részeként a CloudSimple konfigurálja a VMware-infrastruktúrát.  A beállítások közé tartozik a vSAN, a VMware magas rendelkezésre állása és az elosztott erőforrás-ütemező (DRS) tárolási beállításai.

A privát felhő többször is kiterjeszthető. A bővítés csak akkor végezhető el, ha a csomópontok általános korlátain belül marad. Minden alkalommal, amikor kibővít egy saját felhőt, amelyet hozzáad a meglévő fürthöz, vagy újat hoz létre.

## <a name="before-you-begin"></a>Előkészületek

A csomópontokat a saját felhő kibontása előtt kell kiépíteni.  A csomópontok kiépítésével kapcsolatos további információkért lásd: [csomópontok kiépítése a VMware-megoldáshoz a CloudSimple – Azure-](create-nodes.md) cikk.  Új fürt létrehozásához legalább három elérhető csomópontnak kell lennie ugyanahhoz az SKU-hoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="expand-a-private-cloud"></a>Magánfelhő bővítése

1. [Nyissa meg a CloudSimple portált](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot, és válassza ki azt a privát felhőt, amelynek ki szeretné bővíteni a szolgáltatását.

3. Az összefoglalás szakaszban kattintson a **Kibontás**elemre.

    ![Privát felhő kibontása](media/resources-expand-private-cloud.png)

4. Adja meg, hogy ki szeretné-e bontani a meglévő fürtöt, vagy új vSphere-fürtöt szeretne létrehozni. A módosítások elvégzése közben a lapon található összegző információk frissülnek.

    * A meglévő fürt kibontásához kattintson a **meglévő fürt kibontása**elemre. Válassza ki a kibontani kívánt fürtöt, és adja meg a hozzáadandó csomópontok számát. Minden fürt legfeljebb 16 csomópontot tartalmazhat.
    * Új fürt hozzáadásához kattintson az **új fürt létrehozása**lehetőségre. Adjon egy nevet a fürtnek. Válasszon egy meglévő adatközpontot, vagy adjon meg egy nevet egy új adatközpont létrehozásához. Válassza ki a csomópont típusát. Új vSphere-fürt létrehozásakor választhat másik csomópont-típust is, de a meglévő vSphere-fürt kibővítésekor nem. Válassza ki a csomópontok számát. Minden új fürtnek legalább három csomóponttal kell rendelkeznie.

    ![Privát felhő kibontása – csomópontok hozzáadása](media/resources-expand-private-cloud-add-nodes.png)

5. Kattintson a **Submit (Küldés** ) elemre a privát felhő kibontásához.

## <a name="next-steps"></a>További lépések

* [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)
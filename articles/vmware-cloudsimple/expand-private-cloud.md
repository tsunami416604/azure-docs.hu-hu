---
title: Az Azure VMware Solutions (AVS) privát felhő kibontása
description: Ismerteti, hogyan bővíthető egy meglévő AVS privát felhő a kapacitás meglévő vagy új fürtben való hozzáadásához
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025299"
---
# <a name="expand-an-avs-private-cloud"></a>Az AVS Private Cloud kibontása

Az AVS rugalmasságot biztosít az AVS privát felhő dinamikus kibővítéséhez. Ha nagyobb kapacitásra van szüksége, kezdjen egy kisebb konfigurációval, majd bontsa ki a csomópontot. Az is előfordulhat, hogy a jelenlegi igények alapján létrehoz egy AVS Private-felhőt, majd kibővíti a felhasználás növekedését.

Az AVS Private Cloud egy vagy több vSphere-fürtből áll. Minden fürthöz 3 – 16 csomópont tartozhat. Az AVS Private Cloud kibővítésekor csomópontokat adhat hozzá a meglévő fürthöz, vagy létrehozhat egy új fürtöt. Meglévő fürt kibontásához a további csomópontoknak ugyanolyan típusúnak (SKU) kell lenniük, mint a meglévő csomópontoknak. Új fürt létrehozásához a csomópontok eltérő típusúak lehetnek. Az AVS Private Cloud korlátaival kapcsolatos további információkért lásd: korlátozások szakasz az [AVS Private Cloud áttekintő](cloudsimple-private-cloud.md) cikkében.

Az AVS Private Cloud a vCenter alapértelmezett **adatközpontjában** jön létre. Minden adatközpont legfelső szintű felügyeleti entitásként működik. Új fürt esetén az AVS lehetőséget biztosít a meglévő adatközponthoz való hozzáadásra vagy egy új adatközpont létrehozására.

Az új fürtkonfiguráció részeként az AVS konfigurálja a VMware-infrastruktúrát. A beállítások közé tartozik a vSAN, a VMware magas rendelkezésre állása és az elosztott erőforrás-ütemező (DRS) tárolási beállításai.

Az AVS privát felhő többször is kiterjeszthető. A bővítés csak akkor végezhető el, ha a csomópontok általános korlátain belül marad. Minden alkalommal, amikor kibővít egy, a meglévő fürthöz hozzáadott AVS Private Cloud-felhőt, vagy újat hoz létre.

## <a name="before-you-begin"></a>Előzetes teendők

A csomópontokat az AVS Private Cloud kibontása előtt kell kiépíteni. A csomópontok kiépítésével kapcsolatos további információkért lásd: [csomópontok kiépítése a VMware-megoldáshoz az AVS-Azure-](create-nodes.md) cikkben. Új fürt létrehozásához legalább három elérhető csomópontnak kell lennie ugyanahhoz az SKU-hoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="expand-an-avs-private-cloud"></a>Az AVS Private Cloud kibontása

1. [Hozzáférés az AVS-portálhoz](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot, és válassza ki azt az AVS Private-felhőt, amelynek ki szeretné bővíteni a szolgáltatását.

3. Az összefoglalás szakaszban kattintson a **Kibontás**elemre.

    ![Az AVS Private Cloud kibontása](media/resources-expand-private-cloud.png)

4. Adja meg, hogy ki szeretné-e bontani a meglévő fürtöt, vagy új vSphere-fürtöt szeretne létrehozni. A módosítások elvégzése közben a lapon található összegző információk frissülnek.

    * A meglévő fürt kibontásához kattintson a **meglévő fürt kibontása**elemre. Válassza ki a kibontani kívánt fürtöt, és adja meg a hozzáadandó csomópontok számát. Minden fürt legfeljebb 16 csomópontot tartalmazhat.
    * Új fürt hozzáadásához kattintson az **új fürt létrehozása**lehetőségre. Adjon egy nevet a fürtnek. Válasszon egy meglévő adatközpontot, vagy adjon meg egy nevet egy új adatközpont létrehozásához. Válassza ki a csomópont típusát. Új vSphere-fürt létrehozásakor választhat másik csomópont-típust is, de a meglévő vSphere-fürt kibővítésekor nem. Válassza ki a csomópontok számát. Minden új fürtnek legalább három csomóponttal kell rendelkeznie.

    ![Az AVS Private Cloud kibontása – csomópontok hozzáadása](media/resources-expand-private-cloud-add-nodes.png)

5. Kattintson a **Submit (Küldés** ) elemre az AVS Private Cloud kibontásához.

## <a name="next-steps"></a>Következő lépések

* [VMware virtuális gépek használata az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ az [AVS Private felhőkről](cloudsimple-private-cloud.md)
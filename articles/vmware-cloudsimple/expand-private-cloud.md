---
title: Bővítse az Azure VMware-megoldást a CloudSimple private cloud szolgáltatásával
description: A meglévő CloudSimple private cloud kibontása meglévő vagy új fürt kapacitásának növeléséhez
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025299"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Felhőegyszerű magánfelhő kibontása

A CloudSimple rugalmasságot biztosít a magánfelhő dinamikus bővítéséhez. Kezdheti egy kisebb konfigurációval, majd bontsa ki, ahogy nagyobb kapacitásra van szüksége. Vagy létrehozhat egy privát felhőt az aktuális igények alapján, majd a felhasználás növekedésével bővülhet.

A magánfelhő egy vagy több vSphere-fürtből áll. Minden fürt rendelkezhet 3–16 csomópontos csomópontokkal.  A magánfelhő bővítésekor csomópontokat ad hozzá a meglévő fürthöz, vagy új fürtöt hoz létre. Meglévő fürt kibontásához további csomópontoknak ugyanolyan típusúnak (SKU) kell lenniük, mint a meglévő csomópontoknak. Új fürt létrehozásához a csomópontok más típusúak is lehetnek. A magánfelhő-korlátozásokról a [CloudSimple magánfelhő-áttekintésről](cloudsimple-private-cloud.md) szóló cikkének korlátok című szakaszában olvashat bővebben.

A magánfelhő jön létre egy alapértelmezett **datacenter** a vCenter.  Minden adatközpont egy legfelső szintű felügyeleti entitásként szolgál.  Új fürt esetén a CloudSimple lehetőséget ad a meglévő adatközponthoz való hozzáadásra vagy egy új adatközpont létrehozására.

Az új fürtkonfiguráció részeként a CloudSimple konfigurálja a VMware-infrastruktúrát.  A beállítások közé tartoznak a vSAN lemezcsoportok, a VMware magas rendelkezésre állású és az elosztott erőforrás-ütemező (DRS) tárolási beállításai.

A magánfelhő többször is kibontható. A bővítés csak akkor végezhető el, ha a teljes csomópont-korláton belül marad. Minden alkalommal, amikor kibont egy magánfelhőt, amelyet hozzáad a meglévő fürthöz, vagy újat hoz létre.

## <a name="before-you-begin"></a>Előkészületek

A csomópontokat ki kell építeni, mielőtt kibonthatja a privát felhőt.  A csomópontok kiépítéséről további információt a [Felhőegyszerű n- Azure-cikk A VMware-megoldás csomópontjainak kiépítése](create-nodes.md) című témakörben talál.  Új fürt létrehozásához ugyanannak a termékváltozatnak legalább három elérhető csomókat kell létrehoznia.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="expand-a-private-cloud"></a>Magánfelhő bővítése

1. [A CloudSimple portál elérése.](access-cloudsimple-portal.md)

2. Nyissa meg az **Erőforrások** lapot, és válassza ki azt a magánfelhőt, amelyhez ki szeretné bővíteni.

3. Az összefoglaló szakaszban kattintson a **Kibontás gombra.**

    ![Privát felhő bővítése](media/resources-expand-private-cloud.png)

4. Válassza ki, hogy kiszeretné-e bővíteni a meglévő fürtöt, vagy új vSphere-fürtöt hoz létre. A módosítások végrehajtásakor az oldalon található összesítő információk frissülnek.

    * A meglévő fürt kibontásához kattintson **a Meglévő fürt kibontása gombra.** Jelölje ki a kibontani kívánt fürtöt, és adja meg a hozzáadni kívánt csomópontok számát. Minden fürt legfeljebb 16 csomót rendelkezhet.
    * Új fürt hozzáadásához kattintson **az Új fürt létrehozása gombra.** Adjon egy nevet a fürtnek. Jelöljön ki egy meglévő adatközpontot, vagy adjon meg egy nevet egy új adatközpont létrehozásához. Válassza ki a csomópont típusát. Új vSphere-fürt létrehozásakor választhat egy másik csomóponttípust, de nem egy meglévő vSphere-fürt kibővítésekor. Adja meg a csomópontok számát. Minden új fürtnek legalább három csomón kell lennie.

    ![Privát felhő bővítése - csomópontok hozzáadása](media/resources-expand-private-cloud-add-nodes.png)

5. A Magánfelhő kibontásához kattintson a **Küldés** gombra.

## <a name="next-steps"></a>További lépések

* [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)
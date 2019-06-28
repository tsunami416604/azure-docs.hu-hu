---
title: Bontsa ki az Azure VMware-megoldás által CloudSimple Magánfelhő
description: Ismerteti, hogyan bővít ki egy meglévő CloudSimple Magánfelhő kapacitásának hozzáadása egy meglévő vagy új fürt
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332576"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Bontsa ki a CloudSimple Magánfelhő

CloudSimple rugalmasságot biztosít, és dinamikusan bontsa ki a Magánfelhő. Kisebb konfiguráció kezdődik, és bontsa ki a nagyobb kapacitást igény szerint. Vagy a jelenlegi igények alapján Magánfelhő létrehozása, és bontsa ki a fogyasztás növekedésével.

Magánfelhő egy vagy több vSphere-fürtöket tartalmaz. Ha mindegyik fürthöz is 3 – 16 csomóponttal rendelkezik.  Magánfelhő kibővítésekor, csomópontok hozzáadása a meglévő fürthöz, vagy hozzon létre egy új fürtöt. Bontsa ki a meglévő fürt, további csomópontokat a meglévő csomópontok azonos típusú (Termékváltozat) kell lennie. Egy új fürtöt hoz létre, a csomópont lehet egy másik típusú. A Magánfelhő korlátok további információkért lásd: korlátozza szakaszában [CloudSimple magánfelhő áttekintése](cloudsimple-private-cloud.md) cikk.

Magánfelhő létrehozása az alapértelmezett érték **adatközpont** vcenter.  Minden adatközpont szolgálja ki a legfelső szintű felügyeleti egységként.  Egy új fürtöt CloudSimple biztosítja a választás a meglévő adatközpontjának hozzáadását és a egy új adatközpont létrehozása.

Az új fürt konfiguráció részeként CloudSimple konfigurálja a VMware-infrastruktúrára.  A beállítások közé tartozik a tárolási beállítások vsan-hoz lemez csoportok, a VMware magas rendelkezésre állás és a Distributed erőforrás ütemező (DRS).

Magánfelhő többször bővíthetők. Bővítés teheti meg, csak akkor, ha a teljes csomópont korlátokon belül marad. Minden alkalommal, bontsa ki a Magánfelhő adja hozzá a meglévő fürthöz, vagy hozzon létre egy újat.

## <a name="before-you-begin"></a>Előkészületek

Csomópont kibontásával, a Magánfelhő előtt ki kell építeni.  A csomópont kiépítése további információkért lásd: [csomópontok kiépítésére CloudSimple – Azure által a VMware megoldás](create-nodes.md) cikk.  Egy új fürtöt hoz létre, az azonos termékváltozatának legalább három elérhető csomópont kell rendelkeznie.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="expand-a-private-cloud"></a>Bontsa ki a Magánfelhő

1. [A CloudSimple portál eléréséhez](access-cloudsimple-portal.md).

2. Nyissa meg a **erőforrások** lapon, és válassza a privát felhő, amelynek meg szeretné kiterjeszteni.

3. Összegzési területen kattintson **Kibontás**.

    ![Bontsa ki a magánfelhőben](media/resources-expand-private-cloud.png)

4. Döntse el, bontsa ki a meglévő fürtök vagy a vSphere-fürtöt létrehozni. A módosítások, az összefoglaló információkat a lapon frissül.

    * A meglévő fürt kibontásához kattintson **bontsa ki a meglévő fürt**. Válassza ki a fürtöt, bontsa ki, és írja be a hozzáadandó csomópontok számát. Minden egyes fürt egy legfeljebb 16 csomóponttal rendelkezhet.
    * Adjon hozzá egy új fürtöt, kattintson a **új fürt létrehozása**. Adjon egy nevet a fürtnek. Válassza ki egy meglévő adatközpont, vagy adjon meg egy nevet, hozzon létre egy új adatközpontra. Válassza ki a csomópontot. Amikor egy új vSphere-fürtöt hoz létre, de nem egy meglévő vSphere-fürt kibővítésekor választhat egy másik csomópont típusa. Válassza ki a csomópontok számát. Minden egyes új fürthöz legalább három csomóponttal kell rendelkeznie.

    ![Bontsa ki a magánfelhő - csomópontok hozzáadása](media/resources-expand-private-cloud-add-nodes.png)

5. Kattintson a **küldés** bontsa ki a magánfelhő.

## <a name="next-steps"></a>További lépések

* [Az Azure-ban a VMware virtuális gépeket felhasználására](quickstart-create-vmware-virtual-machine.md)
* Tudjon meg többet [Magánfelhők létrehozása](cloudsimple-private-cloud.md)
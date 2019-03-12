---
title: Azure IaaS virtuális gépek replikálása egy másodlagos Azure-régióba az Azure Site Recovery szolgáltatással vész-helyreállítási sikertelen.
description: Ismerje meg, hogyan végezhet az Azure virtuális gépek az Azure Site Recovery szolgáltatással.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: 90ebea81c37c40dc5396d2e6378c7b9af52ad23d
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733114"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Nem sikerült az Azure virtuális gépek Azure-régiók között

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul a vész-helyreállítási stratégiát kezelésével és irányításával replikálása, feladatátvétele és vissza a helyszíni gépek és Azure-beli virtuális gépek (VM) sikertelen.

Ez az oktatóanyag leírja, hogyan visszavételhez egy Azure virtuális Gépen. A feladatátvétel után visszaadja a feladatokat az elsődleges régióra, amint az elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * A másodlagos virtuális gép feladat-visszavétele
> * Ismételt védelem az elsődleges virtuális gép a másodlagos régióba

> [!NOTE]
>
> Ebben az oktatóanyagban az célja, hogy végigvezetik a felhasználókat a lépéseket a célrégió és minimális testreszabási; vissza a feladatátvétel További információ az üzemeltetés minőségének különböző szempontjait feladatátvételt, beleértve a hálózati megfontolások társított szeretne automation és a hibaelhárítás, tekintse meg a dokumentumokat a "Hogyan To" az Azure virtuális gépek.

## <a name="prerequisites"></a>Előfeltételek

> * Győződjön meg arról, hogy a virtuális Gépet a feladatátvétel véglegesítve állapotban van-e, és ellenőrizze, hogy az elsődleges régióban érhető el, és Ön hozhat létre, és azt az új erőforrások eléréséhez.
> * Győződjön meg arról, hogy ismételt védelem engedélyezve van.

## <a name="fail-back-to-the-primary-region"></a>Feladat-visszavétel az elsődleges régióba

Után újra védett virtuális gépek, mint az elsődleges régióba, és ha meg szeretné helyezésnél.

1. Nyissa meg a Recovery Services-tároló. Kattintson a replikált elemek, és válassza ki a virtuális gép védelme újra be lett.

2. Láthatja, hogy a következő. Fontos megjegyezni, hogy hasonlóan a panel a feladatátvételi teszt és a feladatátvételt az elsődleges régióból.
![Feladat-visszavétel az elsődleges](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Kattintson a feladatátvételi teszt végrehajtásához egy feladatátvételi tesztet az elsődleges régióba. Válassza ki a helyreállítási pont és a virtuális hálózat feladatátvételi teszthez, és kattintson az OK gombra. Láthatja, hogy a teszt virtuális gép létrehozása az elsődleges régióban, amely eléréséhez, és vizsgálja meg.

4. Ha feladatátvételi teszt kielégítő, kattintson a feladatátvételi teszt utáni karbantartás karbantartása a forrásrégióban a teszt feladatátvételhez létrehozott erőforrásokat.

5. A replikált elem, jelölje ki a virtuális gép feladatátvételi kívánt > feladatátvétel.

6. Feladatátvétel esetén válassza ki a helyreállítási pont a feladatátvételhez. Az alábbi lehetőségek egyikét használhatja:
    1. Legújabb (alapértelmezett): Ez a lehetőség feldolgozza a Site Recovery szolgáltatásban lévő összes adatot, és a legalacsonyabb helyreállítási időkorlátot (RPO) biztosít
    2. Legutóbb feldolgozott: Ez a beállítás visszaáll a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontot a virtuális gép
    3. Egyéni: Használja ezt a beállítást egy adott helyreállítási pontra történő feladatátvételt. Ez a beállítás akkor hasznos, ha a feladatátvételi teszt végrehajtása

7. Ha azt szeretné, hogy a Site Recovery megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt a feladatátvétel megkezdése előtt válassza ki a Leállítás gépet. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. Vegye figyelembe, hogy a Site Recovery nem távolítsa el a forrás a feladatátvételt követően.

8. Hajtsa végre a feladatátvétel előrehaladását a feladatok lapon

9. A feladatátvétel után a virtuális gépre való bejelentkezéssel ellenőrizze a virtuális gépet. Ha vissza szeretne lépni a virtuális gép egy másik helyreállítási pontot, majd használhatja módosítása helyreállítási pont.

10. Ha már elégedett a feladatátviteli virtuális géppel, akkor is véglegesíti a feladatátvételt. A véglegesítés törli a szolgáltatással elérhető összes helyreállítási pontot. A módosítás a helyreállítási pont beállítás már nem érhető el.

![Az elsődleges és másodlagos régióban található virtuális gép](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

Ha az előző képernyőképen látható, "ContosoWin2016" virtuális gép az USA középső RÉGIÓJA, USA keleti régiója feladatátvételt, és visszaküldi az USA keleti RÉGIÓJA, USA középső RÉGIÓJA nem sikerült.

Vegye figyelembe, hogy a DR-beli virtuális gépek felszabadítva leállítási állapotban marad. Ez szándékosan van, mivel az Azure Site Recovery az adatokat a virtuális gépet, amelynek hasznosak lehetnek a feladatátvétel az elsődleges később a másodlagos régióba. Nem kell fizetnie a felszabadítva virtuális gépek, így, mert azt kell tárolni.

> [!NOTE]
> Tekintse meg a ["hogyan" szakasz](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) bővebben az ismételt védelem munkafolyamat, és mi történik, ismételt védelem során.

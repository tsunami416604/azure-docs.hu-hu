---
title: "VMware virtuális gépek replikálása az Azure-bA az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "A lépések áttekintést nyújt a replikálása az Azure-bA VMware virtuális gépeken futó számítási feladatok"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: db6f5f95929503e82a529dba26b56af1edb0767f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-vmware-vms-to-azure-with-site-recovery"></a>VMware virtuális gépek replikálása az Azure Site Recovery szolgáltatással

Ez a cikk áttekintést lépéseit a helyszíni VMware virtuális gépek replikálása az Azure-ba, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.


![A központi telepítési folyamat](./media/vmware-walkthrough-overview/vmware-to-azure-process.png)

**1. ábra: Központi telepítési folyamat összegzése**

## <a name="step-1-review-architecture-and-prerequisites"></a>1. lépés: Tekintse át a architektúra és Előfeltételek

Központi telepítés megkezdése előtt tekintse át a kialakítandó architektúra, és győződjön meg arról is telepíteni kell minden összetevők megismerése

Ugrás a [1. lépés: az architektúra áttekintése](vmware-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>2. lépés: Felülvizsgálati Előfeltételek

Győződjön meg arról, hogy az előfeltételek teljesülnek az egyes telepítési összetevők:

- **Azure-Előfeltételek**: egy Microsoft Azure-fiók, az Azure-hálózatok és a storage-fiókok van szükség.
- **A helyszíni Site Recovery-összetevőkhöz**: egy a helyszíni Site Recovery összetevőt futtató gép van szüksége.
- **Helyszíni VMware Előfeltételek**: kell, hogy a Site Recovery férhetnek hozzá VMware-kiszolgálók és virtuális gépek fiókok beállítása.
- **Virtuális gépek replikálása**: replikálni kívánt virtuális gépeket kell ahhoz az Azure-követelményeknek, és rendelkeznie kell a mobilitási szolgáltatás összetevőt.

Ugrás a [2. lépés: tekintse át az Előfeltételek és korlátozások](vmware-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>3. lépés: Csomag kapacitás

Ha végzett teljes körű telepítésére, ki kell deríteni kell replikációs erőforrásokat. Nincsenek elérhető segítséget nyújthat eszközöket néhány. Folytassa a 2. Ha végzett a gyors másolatot a környezet teszteléséhez kihagyhatja ezt a lépést.

Folytassa a [3. lépés: Kapacitás megtervezése](vmware-walkthrough-capacity.md) szakasszal.

## <a name="step-4-plan-networking"></a>4. lépés: A hálózat megtervezése

Néhány tervezi, győződjön meg arról, hogy Azure virtuális gépek csatlakoznak feladatátvételt hajt végre, valamint az, hogy rendelkeznek-e a megfelelő IP-címek hálózati elvégzéséhez szükséges.

Ugrás a [4. lépés: hálózat tervezése](vmware-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>5. lépés: Felkészülés az Azure-erőforrások

Azure-hálózatok és a tárolás beállítása az megkezdése előtt. Ehhez üzembe helyezése során, de javasoljuk, hogy ehhez megkezdése előtt.

Ugrás a [5. lépés: Azure előkészítése](vmware-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-vmware"></a>6. lépés: A VMware előkészítése

Meg kell beállítania a fiókokat, amelyek a Site Recovery segítségével:

- VMware virtualizálási kiszolgálók automatikus észlelése a virtuális gépek.
- Hozzáférés virtuális gépeket telepíteni a mobilitási szolgáltatást. Minden replikálni kívánt virtuális gép rendelkeznie kell a mobilitási szolgáltatás ügynöke telepítve előtt is engedélyezze a replikációját.

Ugrás a [6. lépés: készítse elő a VMware](vmware-walkthrough-prepare-vmware.md)

## <a name="step-7-set-up-a-vault"></a>7. lépés: Állítson be egy tárolóban.

Meg kell állítania a Recovery Services-tároló összehangolására és-replikáció kezelésére. A tároló beállításakor szeretne replikálni, és amennyiben szeretne replikálni úgy, hogy adja meg.

Ugrás a [7. lépés: állítson be egy tárolóban.](vmware-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>8. lépés: A forrás- és beállítások konfigurálása

A forrás és cél-replikációhoz használt beállítása. Adatforrás-beállítások beállítása magában foglalja az egységes telepítő a helyszíni Site Recovery-összetevők telepítéséhez.

Ugrás a [8. lépés: a forrás és cél beállítása](vmware-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>9. lépés: A replikációs házirend beállítása

Egy házirend beállítása határozza meg a replikációs beállításokat VMware virtuális gépek esetén a tárolóban lévő állapottal.

Ugrás a [9. lépés: a replikációs házirend beállítása](vmware-walkthrough-replication.md)

## <a name="step-10-install-the-mobility-service"></a>10. lépés: A mobilitási szolgáltatás telepítése

A mobilitási szolgáltatás minden replikálni kívánt virtuális gép telepítve kell lennie. Néhány módon állítsa be a szolgáltatás a lekérés és küldés telepítést.

Ugrás a [10. lépés: a mobilitási szolgáltatás telepítése](vmware-walkthrough-install-mobility.md)

## <a name="step-11-enable-replication"></a>11. lépés: A replikáció engedélyezése

Miután a virtuális gép fut a mobilitási szolgáltatás engedélyezze a replikációját. Miután engedélyezte a, a virtuális gép kezdeti replikációs következik be.

Ugrás a [11. lépés: replikálás engedélyezése](vmware-walkthrough-enable-replication.md)

## <a name="step-12-run-a-test-failover"></a>12. lépés: Feladatátvételi teszt futtatása

Miután a kezdeti replikáció befejezését követően, és a változások replikálása fut, győződjön meg arról, hogy minden megfelelően működik-e a teszt feladatátvételt is futtathatja.

Ugrás a [12. lépés: feladatátvételi teszt futtatása](vmware-walkthrough-test-failover.md)

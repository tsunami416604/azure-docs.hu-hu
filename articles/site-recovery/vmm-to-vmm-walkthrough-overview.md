---
title: "Hyper-V virtuális gépek replikálása az Azure Site Recovery VMM másodlagos hely |} Microsoft Docs"
description: "Áttekintést nyújt a Hyper-V virtuális gépek replikálása egy másodlagos VMM-hely, az Azure portál használatával."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Hyper-V virtuális gépek replikálása másodlagos VMM-hely VMM-felhőkben

> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-vmm.md)
> * [Klasszikus portál](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Ez a cikk ismerteti a System Center Virtual Machine Manager (VMM) felügyelt helyszíni Hyper-V virtuális gépek (VM) replikálásához szükséges lépések áttekintését felhők, a másodlagos helyre VMM használatával [Azure Site Recovery](site-recovery-overview.md) az Azure portálon.

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.


## <a name="step-1-review-the-scenario-architecture"></a>1. lépés:, Tekintse át a kialakítandó architektúra

Központi telepítés megkezdése előtt tekintse át a kialakítandó architektúra, és győződjön meg arról, hogy tudomásul veszi a telepítendő összetevők.

Ugrás a [1. lépés: az architektúra áttekintése](vmm-to-vmm-walkthrough-architecture.md).

## <a name="step-2-review-prerequisites-and-limitations"></a>2. lépés: Tekintse át az Előfeltételek és korlátozások

Győződjön meg arról, hogy tudomásul veszi a telepítési előfeltételek és korlátozások vonatkoznak.

**Azure-Előfeltételek**: a Microsoft Azure-előfizetés és Azure Recovery Services-tároló, összehangolására és-replikáció kezelésére van szükség.
**A helyszíni VMM-kiszolgáló és a Hyper-V-gazdagépek**: Ellenőrizze, hogy a VMM-kiszolgálók és a Hyper-V-gazdagépek a szabályzatnak megfelelő és előkészített a Site Recovery üzembe helyezése.

Ugrás a [2. lépés: Ellenőrizze az Előfeltételek és korlátozások](vmm-to-vmm-walkthrough-prerequisites.md).

## <a name="step-3-plan-networking"></a>3. lépés: A hálózat megtervezése

Kell tennie az egyes hálózati biztosításához állíthatja be a hálózatra való leképezést a forgatókönyv központi telepítésekor a VMM Virtuálisgép-hálózatok között.

Ugrás a [3. lépés: hálózati terv](vmm-to-vmm-walkthrough-network.md).


## <a name="step-4-prepare-vmm-and-hyper-v"></a>4. lépés: A VMM és a Hyper-V előkészítése

A VMM-kiszolgáló és a Hyper-V gazdagépek előkészítése a Site Recovery üzembe helyezése.

Ugrás a [4. lépés: készítse elő a helyszíni kiszolgálók](vmm-to-vmm-walkthrough-vmm-hyper-v.md).

## <a name="step-5-set-up-a-vault"></a>5. lépés: Állítson be egy tárolóban.

Recovery Services-tároló beállítása. A tároló konfigurációs beállításokat tartalmaz, és koordinálja a replikációt.

[5. lépés: Állítson be egy tároló](vmm-to-vmm-walkthrough-create-vault.md).

## <a name="step-6-set-up-source-and-target-settings"></a>6. lépés: A forrás és cél beállítások megadása

A forrás és cél replikációs VMM helyek beállítása. A VMM-kiszolgáló hozzáadása a tárolóhoz, és töltse le a telepítőfájlokat, a Site Recovery-összetevők. Azure Site Recovery Provider telepítőjének futtatása a VMM-kiszolgálón. A telepítő telepíti a szolgáltatót a VMM-kiszolgálón, és regisztrálja a kiszolgálót a tárolóban lévő állapottal. A Microsoft Recovery Services Agent ügynök telepítése minden Hyper-V gazdagépen.

Ugrás a [6. lépés: a forrás és cél beállítások](vmm-to-vmm-walkthrough-source-target.md).

## <a name="step-7-configure-network-mapping"></a>7. lépés: Hálózatleképezés konfigurálása

Képezze le a forrás és cél helyek a VMM Virtuálisgép-hálózatok. A feladatátvétel után a célhálózat, amely leképezhető a forrás Virtuálisgép-hálózat, amelyben a forrás Hyper-V virtuális Gépen található virtuális gépek jönnek létre.

Ugrás a [7. lépés: hálózatleképezés konfigurálása](vmm-to-vmm-walkthrough-network-mapping.md).


## <a name="step-8-set-up-a-replication-policy"></a>8. lépés: A replikációs házirend beállítása

Adja meg, hogyan virtuális gépeket a rendszer replikálja a VMM-helyek között.

Ugrás a [8. lépés: állítson be egy replikációs házirendet](vmm-to-vmm-walkthrough-replication.md).


## <a name="step-9-enable-replication-for-vms"></a>9. lépés: Virtuális gépek replikációjának engedélyezése

Válassza ki a replikálni kívánt virtuális gépeket. A másodlagos hely, a folyamatban lévő változások replikálása után a kezdeti replikáció engedélyezése egy virtuális Gépet a replikálási váltja ki.

Ugrás a [9. lépés: engedélyezze a replikálást](vmm-to-vmm-walkthrough-enable-replication.md).


## <a name="step-10-run-a-test-failover"></a>10. lépés: Feladatátvételi teszt futtatása

Egy feladatátvételi teszt futtatásával ellenőrizze, hogy minden a vártnak megfelelően működik-e.

Ugrás a [10. lépés: feladatátvételi teszt futtatása](vmm-to-vmm-walkthrough-test-failover.md).

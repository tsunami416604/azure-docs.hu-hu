---
title: A VMware vész-helyreállítási forrás beállításainak beállítása az Azure-ba Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan állíthatja be a helyszíni környezetet a VMware virtuális gépek Azure-ba való replikálásához Azure Site Recovery használatával.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ff01aed92669acb193ff149ea9298550134f42a3
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84699549"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>A forrás-környezet beállítása a VMware – Azure replikáláshoz

Ez a cikk bemutatja, hogyan állíthatja be a helyszíni környezetet a VMware virtuális gépek Azure-ba történő replikálásához. A cikk tartalmazza a replikációs forgatókönyv kiválasztásának lépéseit, a helyszíni gép beállítását Site Recovery konfigurációs kiszolgálóként, valamint a helyszíni virtuális gépek automatikus felfedését.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már rendelkezik az alábbiakkal:

- Megtervezte az üzembe helyezést [Azure site Recovery Deployment Planner](site-recovery-deployment-planner.md)segítségével. Ez segít a napi adatváltozási arányon alapuló sávszélesség lefoglalásában, hogy megfeleljen a kívánt helyreállítási időkorlátnak (RPO).
- [Erőforrások beállítása](tutorial-prepare-azure.md) a [Azure Portalban](https://portal.azure.com).
- [A helyszíni VMware beállítása](vmware-azure-tutorial-prepare-on-premises.md), beleértve egy dedikált fiókot az automatikus felderítéshez.

## <a name="choose-your-protection-goals"></a>Válassza ki a védelmi célokat

1. A **Helyreállítási tárak** listából válassza ki a tár nevét. Ehhez a forgatókönyvhöz a **ContosoVMVault** nevet használjuk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **védelmi cél**  >  ,**ahol a gépek találhatók**, válassza **a**helyszíni lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.

## <a name="set-up-the-configuration-server"></a>A konfigurációs kiszolgáló beállítása

A konfigurációs kiszolgálót helyszíni VMware virtuális gépként is beállíthatja egy Open Virtualization Application (PETESEJT) sablonnal. [További](concepts-vmware-to-azure-architecture.md) információ a VMWare virtuális gépre telepítendő összetevőkről.

1. Ismerje meg a konfigurációs kiszolgáló telepítésének [előfeltételeit](vmware-azure-deploy-configuration-server.md#prerequisites) .
2. A központi telepítés [Kapacitási számának ellenőrzését](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) .
3. [Töltse le](vmware-azure-deploy-configuration-server.md#download-the-template) és [importálja](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) a petesejtek sablont egy olyan helyszíni VMWare virtuális gép beállításához, amely a konfigurációs kiszolgálót futtatja. A sablonhoz megadott licenc egy próbaverziós licenc, amely 180 napig érvényes. Ezen időszak közzététele után az ügyfélnek egy beszerzett licenccel kell aktiválnia a Windowst.
4. Kapcsolja be a VMware virtuális gépet, és [regisztrálja azt](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) a Recovery Services-tárolóban.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Azure Site Recovery mappák kizárása a víruskereső programból

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Ha a víruskereső szoftver aktív a forrásoldali gépen

Ha a forrásoldali gépen aktív víruskereső szoftver található, a telepítési mappát ki kell zárni. Tehát zárja ki a *C:\ProgramData\ASR\agent* mappát a zökkenőmentes replikáláshoz.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Ha a víruskereső szoftver aktív a konfigurációs kiszolgálón

A következő mappák kizárása a víruskereső szoftverből a zökkenőmentes replikáláshoz és a kapcsolódási problémák elkerüléséhez

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- C:\Program Files\Microsoft Azure Site Recovery-szolgáltató
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery hiba-összegyűjtési eszköz 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86) \MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Site Recovery kiszolgáló telepítési könyvtára. Például: E:\Program Files (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Ha a víruskereső szoftver aktív a kibővíthető folyamat-kiszolgáló vagy fő célhelyen

A következő mappák kizárása a víruskereső szoftverből

1. C:\Program Files\Microsoft Azure Recovery Services ügynök
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery terheléselosztási folyamat kiszolgálójának telepítési könyvtára, példa: C:\Program Files (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>További lépések
[A célként megadott környezet beállítása](./vmware-azure-set-up-target.md) 

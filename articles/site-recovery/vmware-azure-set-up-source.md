---
title: A VMware vész-helyreállítási forrásbeállításainak beállítása az Azure-ba az Azure Site Recovery segítségével
description: Ez a cikk ismerteti, hogyan állíthatja be a helyszíni környezetben a VMware virtuális gépek replikálása az Azure-ba az Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ff01aed92669acb193ff149ea9298550134f42a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257055"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>A VMware forráskörnyezetének beállítása az Azure replikációjához

Ez a cikk ismerteti, hogyan állíthatja be a helyszíni forráskörnyezetben, a VMware virtuális gépek replikálása az Azure-ba. A cikk a replikációs forgatókönyv kiválasztásának lépéseit, a helyszíni gép site recovery konfigurációs kiszolgálóként való beállítását, valamint a helyszíni virtuális gépek automatikus felderítését ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már:

- Az [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md)segítségével tervezte meg a telepítést. Ez segít a megfelelő sávszélesség lefoglalásához a napi adatváltozási arány alapján, hogy megfeleljen a kívánt helyreállítási pont célkitűzésnek (RPO).
- [Erőforrások beállítása](tutorial-prepare-azure.md) az [Azure Portalon.](https://portal.azure.com)
- [Állítsa be a helyszíni VMware,](vmware-azure-tutorial-prepare-on-premises.md)beleértve egy dedikált fiókot az automatikus felderítés.

## <a name="choose-your-protection-goals"></a>Válaszd ki a védelmi célokat

1. A **Helyreállítási tárak** listából válassza ki a tár nevét. Ehhez a forgatókönyvhöz a **ContosoVMVault** nevet használjuk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelem cél:** > **Hol találhatók a gépek,** válassza **a Helyszíni**lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.

## <a name="set-up-the-configuration-server"></a>A konfigurációs kiszolgáló beállítása

A konfigurációs kiszolgálót helyszíni VMware virtuális gépként állíthatja be egy nyitott virtualizálási alkalmazás (OVA) sablonon keresztül. [További információ](concepts-vmware-to-azure-architecture.md) a VMware virtuális gépre telepítendő összetevőkről.

1. Ismerje meg a konfigurációs kiszolgáló telepítésének [előfeltételeit.](vmware-azure-deploy-configuration-server.md#prerequisites)
2. Ellenőrizze a [kapacitásszámokat](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) az üzembe helyezéshez.
3. [Töltse le](vmware-azure-deploy-configuration-server.md#download-the-template) és [importálja](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) a OVA sablont egy helyszíni VMware virtuális gép, amely a konfigurációs kiszolgálót futtatja. A sablonhoz mellékelt licenc értékelési licenc, és 180 napig érvényes. Ha ezt az időszakot könyveli, az ügyfélnek beszerzett licenccel kell aktiválnia az ablakokat.
4. Kapcsolja be a VMware virtuális gépet, és [regisztrálja azt](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) a Recovery Services tárolóban.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Az Azure Site Recovery mappa kizárása a víruskereső programból

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Ha a víruskereső szoftver aktív a Forrásgépen

Ha a forrásgépen aktív víruskereső szoftver van, a telepítési mappát ki kell zárni. Tehát zárja ki a *C:\ProgramData\ASR\agent* mappát a zökkenőmentes replikáció érdekében.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Ha a víruskereső szoftver aktív a konfigurációs kiszolgálón

A következő mappák kizárása a víruskereső szoftverből a zökkenőmentes replikáció és a kapcsolódási problémák elkerülése érdekében

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- C:\Program Files\Microsoft Azure webhely-helyreállítási szolgáltató
- C:\Program Files\Microsoft Azure Site Recovery konfigurációkezelő 
- C:\Program Files\Microsoft Azure webhely-helyreállítási hibagyűjtemény eszköz 
  - C:\harmadik fél
  - C:\Ideiglenes
  - C:\eper
  - C:\ProgramData\MySQL
  - C:\Program fájlok (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure webhely helyreállítása
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Site Recovery kiszolgáló telepítési könyvtára. Például: E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Ha a víruskereső szoftver aktív a horizontális felskálázási folyamatkiszolgálón/főcélon

A következő mappák kizárása a víruskereső szoftverből

1. C:\Program Files\Microsoft Azure helyreállítási szolgáltatások ügynöke
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure webhely helyreállítása
7. Az Azure Site Recovery terheléselosztásos folyamatkiszolgálótelepítési könyvtára, példa: C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>További lépések
[A célkörnyezet beállítása](./vmware-azure-set-up-target.md) 

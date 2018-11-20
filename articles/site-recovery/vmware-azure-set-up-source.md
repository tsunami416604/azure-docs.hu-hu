---
title: A forráskörnyezet beállítása VMware-ről az Azure-bA az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet VMware virtuális gépek replikálásához az Azure-bA az Azure Site Recovery a helyszíni környezet beállítása.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 5f7d3bb51b2d1c05083645ccc5bca4f668d4a661
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52160520"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>A forráskörnyezet beállítása VMware-ről az Azure-bA

Ez a cikk ismerteti, hogyan állítható be a forráskörnyezethez a helyszíni VMware virtuális gépek replikálásához az Azure-bA. A replikációs forgatókönyv, egy a helyszíni gépen a Site Recovery konfigurációs kiszolgálóként beállítása kiválasztására szolgáló lépéseket tartalmazza, és automatikusan felderítése a helyszíni virtuális gépeket. 

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már:

- A központi telepítés segítségével tervezett [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). Ennek segítségével a kívánt helyreállítási időkorlátot (RPO) teljesítéséhez a napi szintű adatváltozások díj alapján elegendő sávszélességet kell kiosztania.
- [Erőforrások beállítása](tutorial-prepare-azure.md) a a [az Azure portal](http://portal.azure.com).
- [Állítsa be a helyszíni VMware](vmware-azure-tutorial-prepare-on-premises.md), beleértve az automatikus felderítéshez egy dedikált fiókot.

## <a name="choose-your-protection-goals"></a>Védelmi célok megválasztása

1. A **Helyreállítási tárak** listából válassza ki a tár nevét. Ehhez a forgatókönyvhöz a **ContosoVMVault** nevet használjuk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.

## <a name="set-up-the-configuration-server"></a>A konfigurációs kiszolgáló beállítása

Állítható be a konfigurációs kiszolgálót egy helyszíni VMware virtuális gép egy Open Virtualization alkalmazás (OVA) sablon segítségével. [További](concepts-vmware-to-azure-architecture.md) kapcsolatban az összetevőket, amelyek a VMware virtuális gép lesz telepítve.

1. További információ a [Előfeltételek](vmware-azure-deploy-configuration-server.md#prerequisites) server központi telepítésére vonatkozóan.
2. [Ellenőrizze a kapacitás számok](vmware-azure-deploy-configuration-server.md#capacity-planning) üzembe helyezéshez.
3. [Töltse le](vmware-azure-deploy-configuration-server.md#download-the-template) és [importálása](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) az OVA sablon egy helyszíni VMware virtuális gép, amely futtatja a konfigurációs kiszolgáló beállításához. Az engedélyt a sablonhoz megadott értékelési engedélyhez és érvényes 180 napig. POST ennek az időtartamnak kell határidődátumával engedéllyel rendelkező a windows aktiválása.
4. A VMware virtuális gép bekapcsolása és [regisztrálja](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) a Recovery Services-tároló.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Az Azure Site Recovery mappakivételek víruskereső program

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Ha a víruskereső szoftver aktív forrásgépen

Ha a forrásgépen egy víruskereső szoftver aktív, ki kell zárni a telepítési mappa. Így a mappa kizárása *C:\ProgramData\ASR\agent* zökkenőmentes replikálásra.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Ha a víruskereső szoftver aktív, a konfigurációs kiszolgálón

A következő mappák a víruskereső szoftver zökkenőmentes replikáció és a kapcsolódási problémák elkerülése érdekében kizárása

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Konfigurációkezelő 
- C:\Program Files\Microsoft Azure Site Recovery hiba gyűjtemény eszköz 
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
 - C:\Inetpub
 - ASR-kiszolgáló telepítési könyvtárában. Például: E:\Program fájlok (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Ha a víruskereső szoftver aktív, a horizontális felskálázás feldolgozása vagy a fő cél

A következő mappák a víruskereső szoftver kizárása

1. C:\Program Files\Microsoft Azure Recovery Services-ügynök
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Elosztott terhelésű ASR folyamat kiszolgáló telepítési mappájában, például: C:\Program Files (x86) \Microsoft Azure Site Recovery

## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>További lépések
[A célkörnyezet beállítása](./vmware-azure-set-up-target.md) 

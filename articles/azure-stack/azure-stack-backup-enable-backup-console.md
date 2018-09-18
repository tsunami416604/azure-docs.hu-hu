---
title: Biztonsági mentés engedélyezése az Azure Stack a felügyeleti portálról |} A Microsoft Docs
description: Engedélyezze az infrastruktúra Backup szolgáltatás a felügyeleti portálon keresztül, úgy, hogy az Azure Stack állíthatók, ha hiba történik.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.openlocfilehash: fdcc4f51f49e8eb133deeeecd070c55511cda464
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985796"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Biztonsági mentés engedélyezése az Azure Stack a felügyeleti portálról
Engedélyezze az infrastruktúra Backup szolgáltatás a felügyeleti portálon keresztül, úgy, hogy az Azure Stack biztonsági mentéseket hozhat létre. Ezeket a biztonsági másolatokat segítségével helyreállíthatja az-környezet a felhőbeli helyreállítási [végzetes hiba](.\azure-stack-backup-recover-data.md). A felhőbeli helyreállítási célja, hogy győződjön meg arról, hogy az operátorok és a felhasználók is jelentkezzen be újra a portál recovery befejeződése után. Felhasználók lesz visszaállítva, beleértve a szerepköralapú hozzáférési engedélyek és szerepkörök, eredeti csomagok, ajánlatok, és korábban meghatározott számítási, tárolási és hálózati kvóták megszűnni.

Azonban a biztonsági mentési infrastruktúra-szolgáltatás biztonsági mentés IaaS virtuális gépekhez, hálózati konfigurációk és tárolási erőforrások, például a storage-fiókok, blobok, táblák, és így tovább, így a felhasználók felhőalapú helyreállítás után a bejelentkezés befejezése nem jelenik meg bármelyik korábban meglévő az erőforrásokat. Platform (PaaS) szolgáltatás-erőforrások és adatok is nem készül biztonsági másolat a szolgáltatás által. 

A rendszergazdák és felhasználók felelőssége biztonsági mentése és visszaállítása IaaS és PaaS-erőforrásokat külön-külön, a biztonsági mentési infrastruktúra-folyamatok. IaaS és PaaS-erőforrások biztonsági mentésével kapcsolatos további információkért lásd az alábbi hivatkozásokat:

- [Virtuális gépek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [APP SERVICE](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Engedélyezi, vagy konfigurálja újra a biztonsági mentés

1. Nyissa meg a [Azure Stack felügyeleti portálon](azure-stack-manage-portals.md).
2. Válassza ki **minden szolgáltatás**, majd a **felügyeleti** kategória kiválasztása **infrastruktúra biztonsági mentését**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentését** panelen.
3. Írja be a elérési útját a **biztonsági mentési tárhelyet**. Egy univerzális elnevezési konvenció (UNC) karakterlánc használata egy különálló eszköz található fájlmegosztás elérési útját. Karakterláncnak UNC helyét adja meg az erőforrások, például megosztott fájlokhoz vagy eszközökön. A szolgáltatás IP-címet is használhatja. A biztonsági mentési adatok rendelkezésre állásának biztosításához egy vészhelyzetet követően, hogy az eszköz egy külön helyen kell lennie.

    > [!Note]  
    > Ha a környezet támogatja a vállalati környezetben az Azure Stack infrastruktúra-hálózaton a névfeloldást, használhatja a IP-cím helyett a teljes Tartománynevet.
    
4. Írja be a **felhasználónév** segítségével tartomány és felhasználónév megfelelő szintű hozzáféréssel rendelkező fájlok olvasását és írását. Például: `Contoso\backupshareuser`.
5. Írja be a **jelszó** a felhasználó számára.
6. Írja be újra a jelszót **jelszó megerősítése**.
7. A **gyakorisága (óra)** határozza meg, hogy milyen gyakran jönnek létre biztonsági mentéseket. Az alapértelmezett érték: 12. A Scheduler támogatja a legfeljebb 12 és a egy legalább 4. 
8. A **megőrzési időszak napban** határozza meg, hány napig a biztonsági mentések megmaradnak a külső helyen. Az alapértelmezett értéke a 7. A Scheduler támogatja a legfeljebb 14 és a egy legalább 2. Biztonsági másolatok régebbi, mint a megőrzési időszak a külső helyről automatikusan törlődnek.

    > [!Note]  
    > Ha a megőrzési időszak régebbi biztonsági másolatok archiválni kívánja, ügyeljen arra, hogy a fájlok biztonsági mentését, mielőtt az ütemező törli a biztonsági mentéseket. Ha csökkenti a biztonsági másolat megőrzési idejének (pl. az 5 napos 7 napig), az ütemező törli a régebbi, mint az új megőrzési időszaknál minden biztonsági mentés. Ellenőrizze, hogy ok a a biztonsági mentések törlődik, ez az érték frissítése előtt. 

9. Az előmegosztott kulcsot adja meg a **titkosítási kulcs** mezőbe. Biztonsági másolatok ezt a kulcsot az vannak titkosítva. Ellenőrizze, hogy ezt a kulcsot biztonságos helyen tárolja. Állítsa be ezt a kulcsot először, vagy a kulcs rotálása a jövőben, miután a kulcsát, ez az interfész nem tekintheti meg. A kulcs létrehozásához futtassa a következő Azure Stack PowerShell-parancsokat:
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. Válassza ki **OK** a biztonsági mentés vezérlő beállítások mentéséhez.

    ![Az Azure Stack - biztonsági mentést vezérlő beállítások](media\azure-stack-backup\backup-controller-settings.png)

## <a name="start-backup"></a>Biztonsági mentés indítása
A biztonsági mentés indításához kattintson a **biztonsági mentés** egy igény szerinti biztonsági mentés elindításához. Egy igény szerinti biztonsági mentés nem módosítják a következő ütemezett biztonsági mentés időpontját. A feladat befejezése után ellenőrizheti a beállítások **Essentials**:

![Az Azure Stack - igény szerinti biztonsági mentés](media\azure-stack-backup\scheduled-backup.png)

A PowerShell-parancsmagot is futtathatja **Start-AzsBackup** az Azure Stack felügyeleti számítógépen. További információkért lásd: [biztonsági mentése az Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Engedélyezi vagy letiltja az automatikus biztonsági mentés
Biztonsági mentés automatikusan van ütemezve, amikor a biztonsági mentés engedélyezése. A következő biztonsági mentési ütemezés alkalommal ellenőrizheti **Essentials**. 

![Az Azure Stack - igény szerinti biztonsági mentés](media\azure-stack-backup\on-demand-backup.png)

Ha a jövőbeli ütemezett biztonsági mentések tiltása van szüksége, kattintson a **automatikus biztonsági mentések tiltása**. Letiltását automatikus biztonsági mentést fogja megőrizni a biztonsági mentési beállítások konfigurálása, és megőrzi a biztonsági mentés ütemezése. Ez a művelet egyszerűen arra utasítja a jövőbeli biztonsági mentések kihagyja az ütemezőnek. 

![Az Azure Stack - letiltása az ütemezett biztonsági mentések](media\azure-stack-backup\disable-auto-backup.png)

Győződjön meg arról, hogy jövőbeli ütemezett biztonsági mentések le vannak tiltva a **Essentials**:

![Az Azure Stack - erősítse meg a biztonsági mentések le vannak tiltva.](media\azure-stack-backup\confirm-disable.png)

Kattintson a **engedélyezze az automatikus biztonsági mentés** tájékoztatja az ütemező jövőbeli biztonsági mentések elindítani az ütemezett időpontban. 

![Az Azure Stack - enable ütemezett biztonsági mentések](media\azure-stack-backup\enable-auto-backup.png)


> [!Note]  
> Ha konfigurálta az infrastruktúra biztonsági mentését 1807 frissítése előtt, az automatikus biztonsági mentés le lesz tiltva. Ezzel a módszerrel a lépések: Azure Stack biztonsági mentések nem ütköznek a biztonsági mentések indítja el külső Feladatütemező motor. Ha letiltja a bármely külső Feladatütemezőt, kattintson a **engedélyezze az automatikus biztonsági mentés**.


## <a name="next-steps"></a>További lépések

- Ismerje meg a biztonsági mentés futtatása. Lásd: [biztonsági mentése az Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Ismerje meg, annak ellenőrzéséhez, hogy futtatta-e a biztonsági mentés. Lásd: [megerősítése a biztonsági mentés a felügyeleti portál](azure-stack-backup-back-up-azure-stack.md).

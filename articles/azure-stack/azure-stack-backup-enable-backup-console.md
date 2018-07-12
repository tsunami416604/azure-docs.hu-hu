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
ms.date: 07/11/2018
ms.author: jeffgilb
ms.openlocfilehash: fba04490aca4c7123ca478ae07a5f0c865d9a826
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968697"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Biztonsági mentés engedélyezése az Azure Stack a felügyeleti portálról
Engedélyezze az infrastruktúra Backup szolgáltatás a felügyeleti portálon keresztül, úgy, hogy az Azure Stack biztonsági mentéseket hozhat létre. Ezeket a biztonsági másolatokat segítségével helyreállíthatja az-környezet a felhőbeli helyreállítási [végzetes hiba](.\azure-stack-backup-recover-data.md). A felhőbeli helyreállítási célja, hogy győződjön meg arról, hogy az operátorok és a felhasználók is jelentkezzen be újra a portál recovery befejeződése után. Felhasználók lesz visszaállítva, beleértve a szerepköralapú hozzáférési engedélyek és szerepkörök, eredeti csomagok, ajánlatok, és korábban meghatározott számítási, tárolási és hálózati kvóták megszűnni.

Azonban a biztonsági mentési infrastruktúra-szolgáltatás biztonsági mentés IaaS virtuális gépekhez, hálózati konfigurációk és tárolási erőforrások, például a storage-fiókok, blobok, táblák, és így tovább, így a felhasználók felhőalapú helyreállítás után a bejelentkezés befejezése nem jelenik meg bármelyik korábban meglévő az erőforrásokat. Platform (PaaS) szolgáltatás-erőforrások és adatok is nem készül biztonsági másolat a szolgáltatás által. 

A rendszergazdák és felhasználók felelőssége biztonsági mentése és visszaállítása IaaS és PaaS-erőforrásokat külön-külön, a biztonsági mentési infrastruktúra-folyamatok. IaaS és PaaS-erőforrások biztonsági mentésével kapcsolatos további információkért lásd az alábbi hivatkozásokat:

- [Virtuális gépek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [APP SERVICE](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Mielőtt engedélyezné a biztonsági mentés a konzolon keresztül, kell beállítani a biztonsági mentési szolgáltatást. Konfigurálhatja a PowerShell használatával a biztonsági mentési szolgáltatás. További információkért lásd: [biztonsági mentés engedélyezése az Azure Stack a PowerShell-lel](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

1. Nyissa meg az Azure Stack felügyeleti portálon található [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Válassza ki **további szolgáltatások** > **infrastruktúra biztonsági mentését**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentését** panelen.

    ![Az Azure Stack - biztonsági mentést vezérlő beállítások](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Írja be a elérési útját a **biztonsági mentési tárhelyet**. Egy univerzális elnevezési konvenció (UNC) karakterlánc használata egy különálló eszköz található fájlmegosztás elérési útját. Karakterláncnak UNC helyét adja meg az erőforrások, például megosztott fájlokhoz vagy eszközökön. A szolgáltatás IP-címet is használhatja. A biztonsági mentési adatok rendelkezésre állásának biztosításához egy vészhelyzetet követően, hogy az eszköz egy külön helyen kell lennie.
    > [!Note]  
    > Ha a környezet támogatja a vállalati környezetben az Azure Stack infrastruktúra-hálózaton a névfeloldást, használhatja a IP-cím helyett a teljes Tartománynevet.
4. Írja be a **felhasználónév** segítségével tartomány és felhasználónév megfelelő szintű hozzáféréssel rendelkező fájlok olvasását és írását. Például: `Contoso\backupshareuser`.
5. Írja be a **jelszó** a felhasználó számára.
5. Írja be újra a jelszót **jelszó megerősítése**.
6. Az előmegosztott kulcsot adja meg a **titkosítási kulcs** mezőbe. Biztonsági másolatok ezt a kulcsot az vannak titkosítva. Ellenőrizze, hogy ezt a kulcsot biztonságos helyen tárolja. Után állítsa be ezt a kulcsot az első alkalommal vagy a kulcs rotálása a jövőben, akkor ezt a kulcsot a felületén nem jelennek meg. Előre megosztott kulcs létrehozásához további utasításokért hajtsa végre a scripts [biztonsági mentés engedélyezése az Azure Stack a PowerShell-lel](azure-stack-backup-enable-backup-powershell.md).
7. Válassza ki **OK** a biztonsági mentés vezérlő beállítások mentéséhez.

A biztonsági mentés végrehajtásához töltse le az Azure Stack-eszközöket, és a PowerShell-parancsmag futtatásával szeretne **Start-AzSBackup** az Azure Stack felügyeleti csomóponton. További információkért lásd: [biztonsági mentése az Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>További lépések

- Ismerje meg a biztonsági mentés futtatása. Lásd: [biztonsági mentése az Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Ismerje meg, annak ellenőrzéséhez, hogy futtatta-e a biztonsági mentés. Lásd: [megerősítése a biztonsági mentés a felügyeleti portál](azure-stack-backup-back-up-azure-stack.md).

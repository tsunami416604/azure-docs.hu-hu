---
title: Biztonsági mentés engedélyezése Azure verem az adminisztrációs portálról |} Microsoft Docs
description: Engedélyezze az infrastruktúra biztonsági másolat szolgáltatás a felügyeleti portálon keresztül, hogy az Azure-verem állítható vissza, ha hiba történik.
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
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: ce5fd2feaa30948042cc0570a4b0ea7f0ab7ad77
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34302256"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Biztonsági mentés engedélyezése Azure verem a felügyeleti portálon
Az infrastruktúra biztonsági másolat szolgáltatás a felügyeleti portálon keresztül, hogy az Azure-verem hozhat létre a biztonsági mentés engedélyezése Ezek a biztonsági mentések használatával állítsa vissza a környezetben, a felhő helyreállítással [végzetes hiba](.\azure-stack-backup-recover-data.md). Felhőbeli helyreállítási célja annak érdekében, hogy az operátorok és a felhasználók is jelentkezzen be a portál helyreállítást követően. Az előfizetések, beleértve a szerepköralapú hozzáférési engedélyek és szerepkörök, eredeti tervek, ajánlatokat, és a korábban meghatározott számítási, tárolási és hálózati kvóták vissza a felhasználóknak kell.

Azonban az infrastruktúra a biztonsági mentési szolgáltatás biztonsági mentés IaaS virtuális gépeket, a hálózati konfigurációt és a tárolási erőforrások, például a storage-fiókok, a BLOB, a táblák, és így tovább, így a felhasználók felhő helyreállítás után a bejelentkezés befejezése nem láthatók a korábban meglévő erőforrások. Platform (PaaS) szolgáltatás erőforrásokhoz és adatokhoz is nem készül biztonsági másolat a szolgáltatás. 

A rendszergazdák és felhasználók felelőssége biztonsági mentése és visszaállítása IaaS és PaaS erőforrására külön-külön az infrastruktúra biztonsági mentési folyamatok. IaaS és PaaS erőforrására biztonsági mentésével kapcsolatos további információkért tekintse meg a következőket:

- [Virtuális gépek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [APP SERVICE](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> A konzol segítségével a biztonsági mentés engedélyezése előtt kell beállítani a biztonsági mentési szolgáltatást. A biztonsági mentési szolgáltatás PowerShell-lel konfigurálhatja. További információkért lásd: [Azure verem PowerShell és a biztonsági mentés engedélyezése](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

1. Nyissa meg a verem Azure felügyeleti portálon, a [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Válassza ki **további szolgáltatások** > **infrastruktúra biztonsági mentés**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentés** panelen.

    ![Az Azure verem - biztonsági mentés vezérlő beállítások](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Írja be a elérési útját a **biztonsági másolat tárolási helye**. Egy univerzális elnevezési konvenció (UNC) szerinti karakterlánc használata egy különálló eszköz üzemeltetett fájlmegosztás elérési útja. UNC-karakterláncnak erőforrások, például a megosztott fájlok vagy az eszközök helyét adja meg. A szolgáltatás IP-címet is használhatja. A biztonsági mentési adatok rendelkezésre állásának biztosításához után egy olyan vészhelyzet esetén, az eszköz külön kell lennie.
    > [!Note]  
    > Ha a környezet támogatja a névfeloldást a verem Azure infrastruktúra-hálózat számára a vállalati környezetben, az IP-cím helyett egy teljes Tartománynevet is használhatja.
4. Típus a **felhasználónév** segítségével a tartomány és felhasználónév megfelelő hozzáféréssel rendelkező fájlok olvasását és írását. Például: `Contoso\backupshareuser`.
5. Típus a **jelszó** a felhasználó számára.
5. Írja be újra a jelszót **jelszó megerősítése**.
6. Adja meg az előmegosztott kulcsot a **titkosítási kulcs** mezőbe. A biztonságimásolat-fájlok titkosítása a kulcs használatával. Győződjön meg arról, hogy ezt a kulcsot biztonságos helyen tárolja. Állítsa be ezt a kulcsot az első alkalommal vagy a jövőben forgassa el a kulcsot, nem lehet megtekinteni, ezt a kulcsot, az adott csatolón. Egy előre megosztott kulcs létrehozásához további útmutatást kövesse a scripts [Azure verem PowerShell és a biztonsági mentés engedélyezése](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Válassza ki **OK** a biztonsági mentés vezérlő beállítások mentéséhez.

A biztonsági mentés végrehajtásához, és töltse le az Azure-verem eszközöket, majd futtassa a PowerShell-parancsmag szeretne **Start-AzSBackup** a Azure verem felügyeleti csomóponton. További információkért lásd: [biztonsági mentése Azure verem](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>További lépések

- Ismerje meg, a biztonsági mentés futtatására. Lásd: [biztonsági mentése Azure verem](azure-stack-backup-back-up-azure-stack.md ).
- Ismerje meg, annak ellenőrzéséhez, hogy futtatta-e a biztonsági másolat. Lásd: [ellenőrizze a biztonsági mentés felügyeleti portál](azure-stack-backup-back-up-azure-stack.md).

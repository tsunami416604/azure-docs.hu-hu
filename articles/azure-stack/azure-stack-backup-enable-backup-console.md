---
title: "Biztonsági mentés engedélyezése Azure verem az adminisztrációs portálról |} Microsoft Docs"
description: "Az infrastruktúra vissza szolgáltatás engedélyezése a felügyeleti portálon keresztül, hogy az Azure-verem állítható vissza, ha hiba történik."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: a5a9757d871c343ba663862de7b6d75b9dd21c31
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Biztonsági mentés engedélyezése Azure verem a felügyeleti portálon

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az infrastruktúra vissza szolgáltatás engedélyezése a felügyeleti portálon keresztül, hogy az Azure-verem hozhat létre a biztonsági másolatok. Ennek segítségével készít biztonsági ups visszaállítása a környezetben, ha hiba történik.

## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

1. Nyissa meg a verem Azure felügyeleti portálon, a [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Válassza ki **további szolgáltatások** > **infrastruktúra biztonsági mentés**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentés** panelen.

    ![Az Azure verem - biztonsági mentés vezérlő beállítások](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Írja be a elérési útját a **biztonsági másolat tárolási helye**. Egy univerzális elnevezési konvenció (UNC) szerinti karakterlánc kell használnia egy különálló eszköz üzemeltetett fájlmegosztás elérési útja. UNC-karakterláncnak erőforrások, például a megosztott fájlok vagy az eszközök helyét adja meg. A szolgáltatás IP-címet is használhatja. Ahhoz, hogy a biztonsági mentési adatok katasztrófa esetén rendelkezésre állását, az eszköz külön kell lennie.
    > [!Note]  
    > Ha a környezet támogatja a névfeloldást a verem Azure infrastruktúra-hálózat számára a vállalati környezetben használható az IP-cím helyett egy teljes Tartománynevet.
4. Típus a **felhasználónév** tartomány és felhasználónév használatával. Például: `Contoso\administrator`.
5. Típus a **jelszó** a felhasználó számára.
5. Írja be újra a jelszót **jelszó megerősítése**.
6. Adja meg az előmegosztott kulcsot a **titkosítási kulcs** mezőbe. A biztonságimásolat-fájlok titkosítása a kulcs használatával. Győződjön meg arról, hogy ezt a kulcsot biztonságos helyen tárolja. Állítsa be ezt a kulcsot az első alkalommal vagy a jövőben forgassa el a kulcsot, nem lehet megtekinteni, ezt a kulcsot, az adott csatolón. Egy előre megosztott kulcs létrehozásához további útmutatást kövesse a scripts [Azure verem PowerShell és a biztonsági mentés engedélyezése](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Válassza ki **OK** a biztonsági mentés vezérlő beállítások mentéséhez.

A biztonsági mentés végrehajtásához, és töltse le az Azure-verem eszközöket, majd futtassa a PowerShell-parancsmag szeretne **Start-AzSBackup** a Azure verem felügyeleti csomóponton. További információkért lásd: [biztonsági mentése Azure verem](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>További lépések

 - Ismerje meg, a biztonsági másolat, lásd: futtatásához [biztonsági mentése Azure verem](azure-stack-backup-back-up-azure-stack.md ).
- Ismerje meg, annak ellenőrzéséhez, hogy futtatta-e a biztonsági mentés című [ellenőrizze a biztonsági mentés felügyeleti portál](azure-stack-backup-back-up-azure-stack.md ).

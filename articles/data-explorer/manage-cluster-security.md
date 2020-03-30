---
title: A fürt biztonságossá tétele az Azure Data Explorerben
description: Ez a cikk ismerteti, hogyan biztonságos a fürt az Azure Data Explorer az Azure Portalon belül.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720344"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Fürt védelme az Azure Data Explorerben – Azure portal

[Az Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) segít megvédeni és megvédeni az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalásoknak. Kötettitkosítást biztosít a fürt virtuális gépeinek operációs rendszeréhez és adatlemezeihez. Emellett integrálható [az Azure Key Vault,](/azure/key-vault/)amely lehetővé teszi számunkra, hogy ellenőrizzék és kezeljék a lemez titkosítási kulcsok és titkos kulcsok, és biztosítja a virtuális gép lemezeken lévő összes adat titkosítva van. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Titkosítás engedélyezése inaktív módon az Azure Portalon
  
A fürt biztonsági beállításai lehetővé teszik a lemeztitkosítás engedélyezését a fürtön. A [fürt inaktív titkosításának](/azure/security/fundamentals/encryption-atrest) engedélyezése adatvédelmet biztosít a tárolt adatok számára (nyugalmi állapotban). 

1. Az Azure Portalon nyissa meg az Azure Data Explorer fürterőforrás. A **Beállítások** cím alatt válassza a **Biztonság**lehetőséget. 

    ![A titkosítás bekapcsolása inaktív helyen](media/manage-cluster-security/security-encryption-at-rest.png)

1. A **Biztonság** ablakban válassza a **Be** lehetőséget a **Lemeztitkosítás** biztonsági beállításához. 

1. Kattintson a **Mentés** gombra.
 
> [!NOTE]
> Válassza a **Ki** lehetőséget a titkosítás letiltásához, miután engedélyezve lett.

## <a name="next-steps"></a>További lépések

[Fürt állapotának ellenőrzése](/azure/data-explorer/check-cluster-health)

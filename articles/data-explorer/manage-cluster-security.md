---
title: A fürt biztonságossá tétele az Azure-ban Adatkezelő
description: Ez a cikk azt ismerteti, hogyan védheti a fürtöt az Azure Adatkezelő a Azure Portalon belül.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720344"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>A fürt biztonságossá tétele az Azure Adatkezelőban – Azure Portal

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. Mennyiségi titkosítást biztosít a fürt virtuális gépei operációsrendszer-és adatlemezei számára. Emellett integrálva van [Azure Key Vaultokkal](/azure/key-vault/), ami lehetővé teszi a lemezes titkosítási kulcsok és titkos kódok vezérlését és kezelését, valamint gondoskodik arról, hogy a virtuálisgép-lemezeken lévő összes adatok titkosítva legyenek. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>A Azure Portalban lévő titkosítás engedélyezése
  
A fürt biztonsági beállításai lehetővé teszik a lemez titkosításának engedélyezését a fürtön. A [titkosítás](/azure/security/fundamentals/encryption-atrest) inaktív állapotban történő engedélyezése a fürtön a tárolt adatok (nyugalmi állapotban) adatvédelmet biztosít. 

1. A Azure Portal lépjen az Azure Adatkezelő fürterőforrás elemre. A **Beállítások** fejléc alatt válassza a **Biztonság**elemet. 

    ![A titkosítás bekapcsolása nyugalmi állapotban](media/manage-cluster-security/security-encryption-at-rest.png)

1. A **Biztonság** ablakban válassza a **be** lehetőséget a **lemez titkosítása** biztonsági beállításnál. 

1. Kattintson a **Mentés** gombra.
 
> [!NOTE]
> A **kikapcsolás** lehetőség kiválasztásával letilthatja a titkosítást az engedélyezése után.

## <a name="next-steps"></a>Következő lépések

[A fürt állapotának megtekintése](/azure/data-explorer/check-cluster-health)

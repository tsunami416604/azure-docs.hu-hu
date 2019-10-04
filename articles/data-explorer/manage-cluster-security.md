---
title: A fürt biztonságossá tétele az Azure-ban Adatkezelő
description: Ez a cikk azt ismerteti, hogyan védheti a fürtöt az Azure Adatkezelő a Azure Portalon belül.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: c6f7e921886a6acdaa31d0f69f57119c339c0b8b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172594"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>A fürt biztonságossá tétele az Azure-ban Adatkezelő

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. Mennyiségi titkosítást biztosít a fürt virtuális gépei operációsrendszer-és adatlemezei számára. Emellett integrálva van [Azure Key Vault](/azure/key-vault/) , amely lehetővé teszi a lemezes titkosítási kulcsok és titkos kódok vezérlését és kezelését, valamint arról, hogy a virtuálisgép-lemezeken lévő összes adatok titkosítva maradnak az Azure Storage-ban. 

A fürt biztonsági beállításai lehetővé teszik a lemez titkosításának engedélyezését a fürtön.
  
## <a name="enable-encryption-at-rest"></a>Titkosítás engedélyezése nyugalmi állapotban
  
A [titkosítás](/azure/security/fundamentals/encryption-atrest) inaktív állapotban történő engedélyezése a fürtön a tárolt adatok (nyugalmi állapotban) adatvédelmet biztosít. 

1. A Azure Portal lépjen az Azure Adatkezelő fürterőforrás elemre. A **Beállítások** fejléc alatt válassza a **Biztonság**elemet. 

    ![A titkosítás bekapcsolása nyugalmi állapotban](media/manage-cluster-security/security-encryption-at-rest.png)

1. A **Biztonság** ablakban válassza a **be** lehetőséget a **lemez titkosítása** biztonsági beállításnál. 

1. Kattintson a **Mentés** gombra.
 
> [!NOTE]
> A kikapcsolás lehetőség kiválasztásával letilthatja a titkosítást az engedélyezése után.

## <a name="next-steps"></a>További lépések

[A fürt állapotának megtekintése](/azure/data-explorer/check-cluster-health)

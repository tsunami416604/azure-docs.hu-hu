---
title: A fürt biztonságossá tétele az Azure-ban Adatkezelő
description: Ez a cikk azt ismerteti, hogyan védheti a fürtöt az Azure Adatkezelő a Azure Portalon belül.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 86350f21f5c530a00560c92cc0ae2fd58c9a2c57
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780054"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>A fürt biztonságossá tétele az Azure-ban Adatkezelő

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. Mennyiségi titkosítást biztosít a fürt virtuális gépei operációsrendszer-és adatlemezei számára. Emellett integrálható [Azure Key Vault](/azure/key-vault/) a lemez titkosítási kulcsainak és titkainak szabályozására és felügyeletére, valamint biztosítja, hogy a virtuálisgép-lemezeken lévő összes információ titkosítva maradjon az Azure Storage-ban. A fürt biztonsági beállításai lehetővé teszik a lemez titkosításának engedélyezését a fürtön.
  
## <a name="enable-encryption-at-rest"></a>Titkosítás engedélyezése nyugalmi állapotban
  
A [titkosítás](/azure/security/fundamentals/encryption-atrest) inaktív állapotban történő engedélyezése a fürtön a tárolt adatok (nyugalmi állapotban) adatvédelmet biztosít. 

1. A Azure Portal lépjen az Azure Adatkezelő fürterőforrás elemre. A **Beállítások** fejléc alatt válassza a **Biztonság**elemet. 

    ![A titkosítás bekapcsolása nyugalmi állapotban](media/manage-cluster-security/security-encryption-at-rest.png)

1. A **Biztonság** ablakban válassza a **be** lehetőséget a **lemez titkosítása** biztonsági beállításnál. 

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

[A fürt állapotának megtekintése](/azure/data-explorer/check-cluster-health)

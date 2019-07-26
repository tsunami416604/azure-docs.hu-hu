---
title: A fürt biztonságossá tétele az Azure-ban Adatkezelő
description: Ez a cikk azt ismerteti, hogyan védheti a fürtöt az Azure Adatkezelő a Azure Portalon belül.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: fbc5b18de093b2c91b17fa310c08a5b02b113a22
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406521"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>A fürt biztonságossá tétele az Azure-ban Adatkezelő

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. Mennyiségi titkosítást biztosít a fürt virtuális gépei operációsrendszer-és adatlemezei számára. Emellett integrálható [Azure Key Vault](/azure/key-vault/) a lemez titkosítási kulcsainak és titkainak szabályozására és felügyeletére, valamint biztosítja, hogy a virtuálisgép-lemezeken lévő összes információ titkosítva maradjon az Azure Storage-ban. A fürt biztonsági beállításai lehetővé teszik a lemez titkosításának engedélyezését a fürtön.
  
## <a name="enable-encryption-at-rest"></a>Titkosítás engedélyezése nyugalmi állapotban
  
A [titkosítás](/azure/security/azure-security-encryption-atrest) inaktív állapotban történő engedélyezése a fürtön a tárolt adatok (nyugalmi állapotban) adatvédelmet biztosít. 

1. A Azure Portal lépjen az Azure Adatkezelő fürterőforrás elemre. A **Beállítások** fejléc alatt válassza a **Biztonság**elemet. 

    ![A titkosítás bekapcsolása nyugalmi állapotban](media/manage-cluster-security/security-encryption-at-rest.png)

1. A **Biztonság** ablakban válassza a **be** lehetőséget a **lemez titkosítása** biztonsági beállításnál. 

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

[A fürt állapotának megtekintése](/azure/data-explorer/check-cluster-health)

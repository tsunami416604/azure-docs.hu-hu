---
title: Engedélyezze a titkosítást a storage-fiókot az Azure Security Centerben |} Microsoft Docs
description: Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center javaslatait **engedélyezheti a titkosítást az Azure Storage-fiók**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
ms.openlocfilehash: 82bb201c0b518d0b45e06a1eb25d54f60cb3e028
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30235019"
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Engedélyezze a titkosítást az Azure storage-fiókot az Azure Security Centerben
Az Azure Security Center javasolhatja Azure Storage szolgáltatás titkosítási engedélyeznie az inaktív adatok.

Storage Service Encryption (SSE) működik, az adatok titkosítása az Azure storage írásakor, és az adatok beolvasása előtt visszafejtéséhez.  SSE jelenleg csak az Azure Blob szolgáltatáshoz érhető el, és nem használható blokkblobokat, lapblobokat, és hozzáfűző blobokat.  További tudnivalókért lásd: [Storage szolgáltatás titkosítási az inaktív adatok](../storage/common/storage-service-encryption.md).


> [!Note]
> Miután engedélyezte a titkosítás, csak az új adatok titkosítva van. Minden létező blobot, amely a tárfiók nem titkosított marad. Meglévő blobok titkosítása, tekintse meg a [Storage szolgáltatás titkosítási GYIK](../storage/common/storage-service-encryption.md#faq-for-storage-service-encryption).
>
>

Storage szolgáltatás titkosítási csak erőforrás-kezelő tárfiókok esetén támogatott. Klasszikus tárfiókokba jelenleg nem támogatottak. A klasszikus és Resource Manager üzembe helyezési modell ismertetése: [Azure üzembe helyezési modellel](../azure-classic-rm.md).

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok** panelen válassza **engedélyezheti a titkosítást az Azure Storage-fiók**.
   ![Titkosítás engedélyezése tárfiókokon][1]
2. A **storage-titkosítás engedélyezéséhez** panel nyílik meg. Ezen a panelen sorolja fel, ahol a tárolás titkosítása le van tiltva az Azure storage-fiókok. Ebben a példában most válasszon **storageacct1**.
   ![Engedélyezze a tárolás titkosítása][2]
3. A **titkosítási** paneljén **storageacct1** nyílik meg. Válassza ki **engedélyezett**.
   ![Titkosítási panel][3]
4. Kattintson a **Mentés** gombra.

Most már engedélyezte a tárolás titkosítása **storageacct1**.


## <a name="see-also"></a>Lásd még
Ez a dokumentum bemutatta megvalósításához a Security Center ajánlás "Titkosítási Azure Storage-fiókhoz tartozó Engedélyezés." Azure Storage szolgáltatás titkosítási kapcsolatos további tudnivalókért olvassa el a következőket:

* [Az Azure Storage szolgáltatás titkosítási az inaktív adatok](../storage/common/storage-service-encryption.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) -Útmutató: az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) -Útmutató: az Azure-erőforrások állapotának figyelésére.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) -útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) -megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Azure Security Center: GYIK](security-center-faq.md) -gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png

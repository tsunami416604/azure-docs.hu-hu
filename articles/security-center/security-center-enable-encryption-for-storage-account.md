---
title: Engedélyezze a titkosítást a storage-fiókot az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatainak **Azure Storage-fiók titkosításának engedélyezése**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 9f8679e988513eecd778970ac796264b274a8088
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111662"
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Az Azure Security Centerben az Azure storage-fiók titkosításának engedélyezése
Az Azure Security Center javasolhatja az Azure Storage Service Encryption engedélyeznie az inaktív adatok.

Storage Service Encryption (SSE) működik, amikor az oktatóprogram az Azure storage-adatok titkosításához, és visszafejti az adatokat lekérés előtt.  SSE jelenleg csak az Azure Blob service érhető el, és a blokkblobok, lapblobok és használható, és hozzáfűző blobok.  További tudnivalókért lásd: [inaktív adatok a Storage Service Encryption](../storage/common/storage-service-encryption.md).


> [!Note]
> Titkosításának engedélyezése után csak az új adatok titkosítva van. A tárfiókban lévő bármely meglévő BLOB titkosítatlanok maradnak. Meglévő blobok titkosítása, tekintse meg a [Storage Service Encryption – gyakori kérdések](../storage/common/storage-service-encryption.md#faq-for-storage-service-encryption).
>
>

A Storage Service Encryption csak a Resource Manager-tárfiókokra támogatott. Klasszikus storage-fiókok jelenleg nem támogatottak. A klasszikus és Resource Manager üzembe helyezési modellek ismertetése: [Azure üzembehelyezési modell](../azure-classic-rm.md).

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok** panelen válassza ki **Azure Storage-fiók titkosításának engedélyezése**.
   ![Titkosítás engedélyezése tárfiókokon][1]
2. A **tártitkosítás engedélyezése** panel nyílik meg. A panel felsorolja az Azure storage-fiókok, ahol a storage-titkosítás le van tiltva. Ebben a példában válassza **storageacct1**.
   ![Tártitkosítás engedélyezése][2]
3. A **titkosítási** paneljén **storageacct1** nyílik meg. Válassza ki **engedélyezve**.
   ![Titkosítási panel][3]
4. Kattintson a **Mentés** gombra.

Most már engedélyezte a storage titkosítása **storageacct1**.


## <a name="see-also"></a>Lásd még
Ez a dokumentum láthatta, hogyan valósíthat meg a Security Center javaslatait "Titkosítást az Azure Storage-fiók." Az Azure Storage Service Encryption szolgáltatással kapcsolatos további információkért tekintse meg a következőket:

* [Az Azure Storage Service Encryption for Data at Rest](../storage/common/storage-service-encryption.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Az Azure Security Center – gyakori kérdések](security-center-faq.md) – gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png

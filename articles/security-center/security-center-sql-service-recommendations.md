---
title: Adat- & tárolási javaslatok – Azure Security Center
description: Ez a dokumentum az Azure Security Centerben található javaslatokkal foglalkozik, amelyek segítenek az adatok és az Azure SQL-szolgáltatás védelmében, és a biztonsági szabályzatok betartásában maradnak.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552865"
---
# <a name="protect-azure-data-and-storage-services"></a>Az Azure-adatok és tárolási szolgáltatások védelme
Amikor az Azure Security Center azonosítja a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán az erőforrások megerősítéséhez és védelméhez.

Ez a cikk a Security Center erőforrás-biztonsági szakaszának **Adatbiztonság lapját** ismerteti.

Az ezen az oldalon látható javaslatok teljes listáját az [Adat- és tárolási javaslatok című témakörben találja.](recommendations-reference.md#recs-datastorage)


## <a name="view-your-data-security-information"></a>Az adatbiztonsági adatok megtekintése

1. Az **Erőforrás-biztonsági higiénia csoportban** kattintson az **Adatok és tárolási erőforrások**elemre.

    ![Adat- & tárolási erőforrások](./media/security-center-monitoring/click-data.png)

    Az **Adatbiztonság** lap megnyílik az adat-erőforrásokra vonatkozó javaslatokkal.

    [![Adatforrások](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Ezen az oldalon a következőket teheti:

    * Kattintson az **Áttekintés** fülre, amely felsorolja a kiújítandó összes adaterőforrás-ajánlást. 
    * Kattintson az egyes lapokra, és tekintse meg a javaslatokat erőforrástípus szerint.

    > [!NOTE]
    > A tárolótitkosításról az [Azure Storage titkosítása az inaktív adatokért](../storage/common/storage-service-encryption.md)című témakörben talál további információt.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Adaterőforrásra vonatkozó javaslat kiújítása

1. Az erőforráslapok bármelyikén kattintson egy erőforrásra. Megnyílik az információs lap, amely felsorolja a helyreállítandó javaslatokat.

    ![Erőforrás-információk](./media/security-center-monitoring/sql-recommendations.png)

2. Kattintson egy javaslatra. A Javaslat lap megnyílik, és megjeleníti a **javítási lépéseket** a javaslat végrehajtásához.

   ![Javítási lépések](./media/security-center-monitoring/remediate1.png)

3. Kattintson **a Művelet művelet gombra.** Megjelenik az erőforrás-beállítások lap.

    ![Javaslat engedélyezése](./media/security-center-monitoring/remediate2.png)

4. Kövesse a **szervizelési lépéseket,** és kattintson a **Mentés gombra.**


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a más Azure-erőforrástípusokra vonatkozó javaslatokról, tekintse meg az alábbi témaköröket:

* [Az Azure Security Center biztonsági javaslatainak teljes hivatkozási listája](recommendations-reference.md)
* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
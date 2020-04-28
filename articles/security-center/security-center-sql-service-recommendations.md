---
title: Az adat& tárolási javaslatai – Azure Security Center
description: Ez a dokumentum olyan Azure Security Center javaslatokat tartalmaz, amelyek segítségével megvédheti adatait és az Azure SQL-szolgáltatást, és a biztonsági szabályzatoknak megfelelően maradhat.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552865"
---
# <a name="protect-azure-data-and-storage-services"></a>Az Azure-beli adatkezelési és-tárolási szolgáltatások védelme
Ha Azure Security Center észleli a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának lépésein az erőforrások megerősítéséhez és védelméhez.

Ez a cikk a Security Center erőforrás-biztonsági szakaszának **adatbiztonság lapját** ismerteti.

Az ezen a lapon megjelenő javaslatok teljes listáját az [adatkezelési és tárolási javaslatok](recommendations-reference.md#recs-datastorage)című cikkben tekintheti meg.


## <a name="view-your-data-security-information"></a>Az adatbiztonsági adatok megtekintése

1. Az **erőforrás-biztonsági higiénia** szakaszban kattintson az **adatforrások és tárolók erőforrásai**elemre.

    ![Adat& Storage-erőforrások](./media/security-center-monitoring/click-data.png)

    Megnyílik az **adatbiztonság** lap, amely az adaterőforrásokra vonatkozó ajánlásokat tartalmaz.

    [![Adatforrások](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Ezen az oldalon a következőket teheti:

    * Kattintson az **Áttekintés** lapra az összes szervizelni kívánt adatforrásra vonatkozó javaslat. 
    * Kattintson az egyes lapokra, és tekintse meg az adott erőforrás típusa szerinti javaslatokat.

    > [!NOTE]
    > A tárolás titkosításával kapcsolatos további információkért lásd: [Az Azure Storage titkosítása inaktív adatokhoz](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Javaslat szervizelése egy adaterőforráson

1. Az összes erőforrás lapon kattintson egy erőforrásra. Megnyílik az információs oldal, amely felsorolja a szervizelni kívánt ajánlásokat.

    ![Erőforrás-információk](./media/security-center-monitoring/sql-recommendations.png)

2. Kattintson egy javaslatra. Megnyílik a javaslat lap, és megjeleníti a **szervizelési lépéseket** a javaslat megvalósításához.

   ![Szervizelési lépések](./media/security-center-monitoring/remediate1.png)

3. Kattintson a **művelet elvégzése**gombra. Megjelenik az erőforrás-beállítások lap.

    ![Javaslat engedélyezése](./media/security-center-monitoring/remediate2.png)

4. Kövesse a **szervizelési lépéseket** , és kattintson a **Mentés**gombra.


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következő témaköröket:

* [Azure Security Center biztonsági javaslatainak teljes listája](recommendations-reference.md)
* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
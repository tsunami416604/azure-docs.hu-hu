---
title: Azure foglalások megtekintése |} A Microsoft Docs
description: Ismerje meg az Azure-foglalások megtekintése az Azure Portalon.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2018
ms.author: cwatson
ms.openlocfilehash: 2ae30ca55f3ca03a64438025960ddd807e288216
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48272640"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Az Azure-foglalások megtekintése az Azure Portalon

Az előfizetés típusa és az engedélyek, függően nincsenek többféle módon Azure foglalások megtekintéséhez.

## <a name="view-reservations-as-owner-or-reader"></a>A foglalások tulajdonosaként vagy olvasó megtekintése

Alapértelmezés szerint amikor vásárol egy foglalást, és a fiók rendszergazdája tekintheti meg a foglalást. Ön és fiókadminisztrátori automatikusan a tulajdonos szerepkör kapnak a foglalást. Ahhoz, hogy mások megtekintése a foglalást, hozzá kell adnia azokat egy **tulajdonosa** vagy **olvasó** a foglalást meg. További információkért lásd: [hozzáadása vagy módosítása egy foglalást kezelésére jogosult felhasználók](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
 
Tulajdonos vagy olvasó, foglalás megtekintése

1. Jelentkezzen be az [Azure Portalra]( http://portal.azure.com).
1. A Keresés **foglalások**.

    ![Az Azure portál keresési bemutató képernyőkép](./media/billing-view-reservation/portal-reservation-search.png)

1. A foglalások listáját láthatja, amelyekben a tulajdonos vagy olvasó szerepkört.

A Foglalás hatóköre módosítani szeretné, ha a Foglalás felosztása vagy módosítása, aki kezelheti a foglalást, lásd: [kezelése az Azure-foglalások](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>A vállalati belépéseket foglalás tranzakciók megtekintése

 Ha egy partner vezetett a nagyvállalati beléptetés, megtekintheti a foglalások **jelentések** az EA-portálon. Egyéb vállalati belépéseket megtekintheti a foglalásokat, a nagyvállalati szerződések portálján és az Azure Portalon. Foglalás tranzakciók megtekintése nagyvállalati szerződéssel rendelkező rendszergazdának kell lennie.

Foglalás tranzakciók megtekintése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra]( http://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.

    ![Az Azure portál keresési bemutató képernyőkép](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Válassza ki **foglalás tranzakciók**.
1. Az eredmények szűréséhez válassza **Timespan**, **típus**, vagy **leírás**.
1. Kattintson az **Alkalmaz** gombra.

    ![Foglalás tranzakciók eredményeket megjelenítő képernyőkép](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

API-k használatával az adatok lekéréséhez lásd: [nagyvállalati vásárlók számára fenntartott példány első szolgáltatásra vonatkozó forgalmi díjak](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Fizessen elő az, Cosmos DB szolgáltatás számára fenntartott kapacitás](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [CSP-előfizetésekben foglalás használati adatai](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

---
title: Azure-foglalások vásárlási és visszatérítési tranzakcióinak megtekintése
description: Megtudhatja, hogyan tekintheti meg az Azure-foglalások vásárlási és visszatérítési tranzakcióit.
author: yashesvi
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 988dd057834f6eb19dfd75a868c2893aefb61435
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290764"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Foglalások vásárlási és visszatérítési tranzakcióinak megtekintése

A foglalások vásárlási és visszatérítési tranzakcióit különféle módokon tekintheti meg. Használhatja hozzá az Azure Portalt, a Power BI-t vagy a REST API-kat.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Foglalási tranzakciók megtekintése az Azure Portalon

A Nagyvállalati Beléptetés vagy a Microsoft Ügyfélszerződés számlázási rendszergazdája megtekintheti a foglalási tranzakciókat a Költségkezelés + számlázás területen.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
1. Válassza a **Foglalási tranzakciók** lehetőséget.
1. Az eredmények szűréséhez válassza az **Időtartomány**, a **Típus** vagy a **Leírás** lehetőséget.
1. Kattintson az **Alkalmaz** gombra.

[![A képernyőképen foglalási tranzakciói láthatók az Azure Portalon](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Foglalási tranzakciók megtekintése a Power BI-ban

A Nagyvállalati Beléptetés vagy a Microsoft Ügyfélszerződés számlázási rendszergazdája megtekintheti a foglalási tranzakciókat a Cost Management Power BI-alkalmazásban.

1. Szerezze be a [Cost Management Power BI-alkalmazást](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Lépjen az RI-vásárlások jelentéshez.

[![Foglalási tranzakciókat bemutató példa](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

További információkért lásd: [Azure Cost Management Power BI-alkalmazás Nagyvállalati Szerződésekhez](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

## <a name="use-apis-to-get-reservation-transactions"></a>Foglalási tranzakciók lekérése API-k használatával

A Nagyvállalati Szerződéssel (EA) és a Microsoft Ügyfélszerződéssel rendelkező felhasználók lekérhetik a foglalási tranzakciók adatait a [Reservation Transactions – List API](https://docs.microsoft.com/rest/api/consumption/reservationtransactions/list)-val.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](manage-reserved-vm-instance.md) szakaszt.
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
  - [A Reservations kezelése az Azure-ban](manage-reserved-vm-instance.md)

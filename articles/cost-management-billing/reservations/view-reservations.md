---
title: Foglalások megtekintése az Azure-erőforrásoknál | Microsoft Docs
description: Ismerje meg, hogyan tekintheti meg az Azure Reservationst az Azure Portalon.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: fd5d4248e9b1e645628ac8b87df3b4b0a16e2049
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288961"
---
# <a name="view-azure-reservations"></a>Azure-foglalások megtekintése

A megvásárolt foglalásokat az Azure Portalon tekintheti meg és kezelheti.

## <a name="permissions-to-view-a-reservation"></a>Engedélyek foglalások megtekintésére

Egy foglalás megtekintéséhez vagy kezeléséhez olvasói vagy tulajdonosi engedéllyel kell rendelkeznie hozzá. Amikor foglalást vásárol, Ön és a fiókadminisztrátor automatikusan megkapja a Tulajdonos szerepkört a foglalási rendelés és a foglalás tekintetében. Amennyiben mások számára is engedélyezni kívánja a megtekintést, a **Tulajdonos** vagy az **Olvasó** szerepkör tagjaként kell hozzáadnia őket a foglalási rendeléshez vagy a foglaláshoz. Ha a foglalás számlázásához megadott előfizetéshez ad valakit, nem adja hozzá automatikusan a foglaláshoz. 

További információk [a foglalást kezelő felhasználók hozzáadásáról vagy módosításáról](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Foglalás és kihasználtság megtekintése az Azure Portalon

Foglalás megtekintése Tulajdonosként vagy Olvasóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen a [Foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) területre.
3. A listán szerepel az összes olyan foglalás, amelyhez Tulajdonos vagy Olvasó szerepkörben hozzáfér. Mindegyik foglalás mellett a kihasználtság legutóbb ismert százalékos értéke látható.
4. Kattintson a kihasználtság százalékára a kihasználtság előzményeinek és részleteinek megtekintéséhez. Az alábbi videóban talál részleteket.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Foglalások és kihasználtság lekérése API-k, a PowerShell vagy a parancssori felület használatával

Az összes foglalás listájának lekérése a következő erőforrásokkal
- [API: Foglalásrendelések listája](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Foglalásrendelések listája](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [Parancssori felület: Foglalásrendelések listája](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

A [foglalás kihasználtsága](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) ezenkívül a Reserved Instance használati API használatával is megállapítható. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Foglalások és kihasználtság megtekintése a Power BI-ban

Két lehetőség érhető el a Power BI-felhasználók számára
- Tartalomcsomag: A foglalásvásárlások és a kihasználtsági adatok a [Consumption Insights Power BI-tartalomcsomagban](/power-bi/desktop-connect-azure-cost-management) érhetők el. Ezzel a tartalomcsomaggal hozhatja létre a kívánt jelentéseket. 
- Cost Management alkalmazás: A [Cost Management alkalmazást](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) további testreszabásra kész, előre létrehozott jelentésekhez használhatja.

## <a name="next-steps"></a>További lépések

- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md).
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
- [A foglalási kihasználtság ismertetése CSP-előfizetésnél](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

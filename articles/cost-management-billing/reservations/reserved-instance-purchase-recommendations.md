---
title: Az Azure-beli foglalási javaslatok létrehozása
description: Megtudhatja, hogyan jönnek létre az Azure-beli foglalási javaslatok a virtuális gépekhez.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: 2f7b09c14553fdb5d642080d286ce123176b997f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991048"
---
# <a name="how-reservation-recommendations-are-created"></a>Foglalási javaslatok létrehozása
Az Azure-beli fenntartott példányok (RI) vásárlására vonatkozó javaslatokat az Azure Consumption [Reservation Recommendation API](/rest/api/consumption/reservationrecommendations), az [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) és az Azure Portal foglalásvásárlási folyamata hozza létre.

A javaslatkalkulációs módszer magadásának lépései: 
1. A javaslati motor az elmúlt hét, 30 és 60 napra vonatkozóan kiértékeli az erőforrások óránkénti használatát az adott hatókörben.
2. A használati adatok alapján a motor szimulálja a költségeket a foglalásokkal és azok nélkül is.
3. A költségek szimulálása különböző mennyiségek esetében történik, és a maximális megtakarítást eredményező mennyiség a javasolt mennyiség.
4. Ha az erőforrásai rendszeresen le vannak állítva, a szimuláció nem fog megtakarítási lehetőséget találni, és nem ad vásárlási javaslatot.

## <a name="recommendations-in-azure-advisor"></a>Javaslatok az Azure Advisorban
Foglalásvásárlási javaslatok érhetők el a virtuális gépekhez kapcsolódóan az Azure Advisorban. Tartsa szem előtt az alábbi szempontokat: 
- Az Advisorban csak egyelőfizetéses hatókörre érvényes javaslatok érhetők el.
- Az Advisorban 30 napos visszatekintési időszak alapján kalkulált javaslatok érhetők el.
- Ha megosztott hatókörrel rendelkező foglalást vásárol, az Advisor foglalásvásárlási javaslatai legfeljebb 30 napig láthatók.

## <a name="other-expected-api-behavior"></a>Egyéb várt API-viselkedés
- Amikor hétnapos visszatekintési időszakot alkalmaz, előfordulhat, hogy nem kap javaslatokat, ha a virtuális gépek egy napnál tovább vannak leállítva.

## <a name="next-steps"></a>További lépések
- Ismerkedjen meg [az Azure-foglalási kedvezmény virtuális gépekre történő alkalmazásával](../manage/understand-vm-reservation-charges.md).

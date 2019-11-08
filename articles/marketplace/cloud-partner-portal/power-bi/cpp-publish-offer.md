---
title: Power BI alkalmazás-ajánlat közzététele | Azure piactér
description: Power BI alkalmazás-ajánlat közzététele a Microsoft AppSource piactéren.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pabutler
ms.openlocfilehash: 324be960bd2d22623763ca3e24b99be92ff04174
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826037"
---
# <a name="publish-a-power-bi-app-offer"></a>Power BI alkalmazás-ajánlat közzététele

Az utolsó lépés, miután meghatározta az ajánlatot a Cloud Partner Portalban, és létrehozta a kapcsolódó technikai eszközöket, a közzétételhez küldje el az ajánlatot. A folyamat elindításához az **új ajánlat** ablak bal oldali ablaktábláján válassza a **Közzététel**lehetőséget. További információkért lásd: [Azure Marketplace-és AppSource-ajánlatok közzététele](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Közzétételi lépések

A közzétételi folyamat fő lépései:

![Power BI app-ajánlathoz tartozó folyamat lépéseinek közzététele](./media/publishing-process-steps.png)

Ez a táblázat az egyes lépéseket ismerteti, és a becsült befejezési időt biztosítja:

|   Közzétételi lépés            |   Time     |   Leírás                                                                  |
| --------------------         |------------| ----------------                                                               |
| Előfeltételek ellenőrzése       | 15 perc     | Az ajánlatra vonatkozó információk és az ajánlat beállításainak ellenőrzése.                            |
| Tanúsítvány                | 1-7 nap   | Az Power BI minősítési csapat elemzi az ajánlatot. A csapat kézi ellenőrzési teszten keresztül futtatja a Power BI alkalmazást, ha telepíti az alkalmazást a megadott telepítési URL-címen keresztül. Az elsődleges érvényesítések az alkalmazás-minősítési folyamat részeként történnek (a jelen dokumentum későbbi részében leírtak szerint).         |
| Csomagolás                    | \< 1 óra  | Az ajánlat technikai eszközei az ügyfelek általi használatra vannak csomagolva.                        |
| Ólom-létrehozási regisztráció | \< 1 óra  | Az érdeklődő rendszerek konfigurálva és telepítve vannak.                                      |
| Közzétevő PM            | \-         | Az ajánlat életbe lépéséhez végre kell hajtania egy végleges felülvizsgálatot és megerősítést. Most már rendelkezik egy hivatkozással is az ajánlat előzetes verziójának megtekintéséhez. Miután elégedett az előzetes verzió megjelenésével, válassza az **élő indítás** lehetőséget az **állapot** lapon. Ez egy kérelmet küld a bevezetési csapatnak az alkalmazás AppSource való listázásához.    |
| Élő                         | \< 3 óra | Ajánlata mostantól nyilvánosan szerepel a AppSource, és az ügyfelek megtekinthetik az alkalmazást, és üzembe helyezhetik azokat Power BI előfizetésében. Egy megerősítő e-mailt is kap. A **minden ajánlat** lapon a jobb oldali oszlopban láthatja az összes ajánlat állapotát. Az **állapot** lapon megtekintheti az ajánlat részletes közzétételi folyamatának állapotát. |
|   |   |

Akár nyolc napig is eltarthat, amíg a folyamat befejeződik. Miután elvégezte ezeket a közzétételi lépéseket, a Power BI alkalmazás ajánlata a [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power bi alkalmazások szakaszban jelenik meg.


### <a name="app-certification-process"></a>Alkalmazás-minősítési folyamat

A Microsoft bevezetési csapata ezt a folyamatot használja a Power BI alkalmazás-ajánlatok küldésének ellenőrzésére:

1. Tekintse át a jogi dokumentumokat és a Súgó hivatkozásait.
2. A támogatási kapcsolattartási adatok ellenőrzése.
3. A megfelelő telepítés ellenőrzéséhez használja a telepítő URL-címét.
4. Vizsgálja meg az alkalmazást kártevők és egyéb kártékony tartalmak számára.
5. Ellenőrizze, hogy a megjelenített tartalom megfelel-e az alkalmazás leírásának.
6. Ellenőrizze, hogy az alkalmazással kapcsolatos műveletek a várt módon működnek-e Power BIben. A csoport a mintaadatok használatával nyitja meg a jelentéseket és az irányítópultokat, összekapcsolja az egyéni adatforrásokat, frissíti az adatforrásokat stb.

A minősítési csapat visszajelzést küld, ha problémát talál.  Power BI alkalmazásra vonatkozó követelményekkel kapcsolatos további információkért tekintse meg a [Power bi alkalmazás dokumentációját](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>További lépések

Javasoljuk, hogy rendszeresen figyelje az alkalmazást a [AppSource piactéren](https://appsource.microsoft.com).  A [Cloud Partner Portal](https://cloudpartner.azure.com/#insights) [eladói](../../cloud-partner-portal-orig/si-getting-started.md) betekintő funkciójának használatával bepillantást nyerhet a piactér-ügyfelekre és az alkalmazások használatára. Végezetül pedig [frissítheti az ajánlatot](./cpp-update-existing-offer.md).

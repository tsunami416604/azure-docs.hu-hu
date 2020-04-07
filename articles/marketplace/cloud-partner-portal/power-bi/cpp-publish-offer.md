---
title: Power BI-alkalmazásajánlat közzététele | Azure Piactér
description: Power BI-alkalmazásajánlat közzététele a Microsoft AppSource piactéren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d52270e623a713d7188ac5ff907ebb82ada13283
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745608"
---
# <a name="publish-a-power-bi-app-offer"></a>Power BI Alkalmazásajánlat közzététele

>[!Important]
>2020. március 30-tól megkezdjük a Power BI-alkalmazásajánlatok felügyeletének áthelyezését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Az áttelepített ajánlatok kezeléséhez kövesse a [Power BI-alkalmazások létrehozásának áttekintése](https://aka.ms/AzureCreatePBIServiceApp) című témakör utasításait.

Az utolsó lépés, miután definiált egy ajánlatot a Cloud Partner Portalon, és létrehozta a kapcsolódó technikai eszközöket, az ajánlat közzététele. A folyamat elindításához az Új **ajánlat** ablak bal oldali ablaktáblájában válassza a **Közzététel**lehetőséget. További információ: [Publish Azure Marketplace and AppSource offers](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Közzétételi lépések

Ezek a közzétételi folyamat fő lépései:

![Közzétételi folyamat lépései a Power BI App-ajánlathoz](./media/publishing-process-steps.png)

Ez a táblázat ismerteti az egyes lépéseket, és tartalmazza a becsült befejezési időt:

|   Közzétételi lépés            |   Time     |   Leírás                                                                  |
| --------------------         |------------| ----------------                                                               |
| Előfeltételek érvényesítése       | 15 perc     | Az ajánlati információk és az ajánlatbeállítások érvényesítve vannak.                            |
| Tanúsítvány                | 1-7 nap   | A Power BI minősítő csapata elemzi az ajánlatot. A csapat manuális ellenőrzési teszten keresztül futtatja a Power BI-alkalmazást az alkalmazás nak a megadott telepítési URL-címen történő telepítésével. Az elsődleges ellenőrzések az alkalmazás minősítési folyamatának részeként történik (a jelen dokumentum későbbi részében ismertetve).         |
| Csomagolás                    | \<1 óra  | Az ajánlat technikai eszközeit az ügyfelek számára csomagolják.                        |
| Érdeklődőgenerálás regisztrációja | \<1 óra  | Az ólomrendszerek konfigurálva és üzembe helyezve vannak.                                      |
| Kiadói kijelentkezés            | \-         | A végső felülvizsgálatot és a megerősítést még az ajánlat élessé adása előtt elvégzi. Most már egy linket is kap az ajánlat előnézetének megtekintéséhez. Miután elégedett az előnézet megjelenésével, válassza az **Élő adás lehetőséget** az **Állapot** lapon. Ez egy kérést küld a bevezetési csapatnak, hogy sorolja fel az alkalmazást az AppSource-on.    |
| Élő                         | \<3 óra | Az ajánlat most már nyilvánosan szerepel ("élő") az AppSource-on, és az ügyfelek megtekinthetik az alkalmazást, és telepíthetik azt a Power BI-előfizetéseikben. Egy visszaigazoló e-mailt is kap. A **Minden ajánlat** lap jobb oldali oszlopában láthatja az összes ajánlat állapotát. Az **Állapot** lapon megtekintheti az ajánlat részletes közzétételi folyamatállapotát. |
|   |   |

A folyamat befejezéséhez legfeljebb nyolc napot kell hagyni. Miután végigment ezeken a közzétételi lépéseken, a Power BI-alkalmazásajánlat megjelenik az [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI-alkalmazások szakaszban.


### <a name="app-certification-process"></a>Az alkalmazás minősítési folyamata

A Microsoft bevezetési csapata ezt a folyamatot használja a Power BI-alkalmazásajánlat elküldésének érvényesítéséhez:

1. Tekintse át a jogi dokumentumokat és a súgóhivatkozásokat.
2. A támogatási kapcsolattartóadatok ellenőrzése.
3. A telepítő URL-címének használatával ellenőrizze a megfelelő telepítést.
4. Rosszindulatú programok és egyéb rosszindulatú tartalmak keresése az alkalmazásban.
5. Ellenőrizze, hogy a megjelenített tartalom megegyezik-e az alkalmazás leírásával.
6. Ellenőrizze, hogy az alkalmazásokkal kapcsolatos műveletek a várt módon működnek-e a Power BI-ban. A csapat megnyitegy jelentéseket és irányítópultokat mintaadatokkal, egyéni adatforrásokhoz csatlakozik, frissíti az adatokat és így tovább.

A minősítő csoport visszajelzést ad, ha bármilyen problémát találnak.  A Power BI alkalmazáskövetelményeiről a [Power BI alkalmazás dokumentációjában](https://go.microsoft.com/fwlink/?linkid=2028636)olvashat bővebben.


## <a name="next-steps"></a>További lépések

Javasoljuk, hogy rendszeresen figyelje az alkalmazást az [AppSource piactéren.](https://appsource.microsoft.com)  A [Cloud Partner Portal](https://cloudpartner.azure.com/#insights) Seller [Insights](../../cloud-partner-portal-orig/si-getting-started.md) funkcióját is érdemes a piactéri ügyfelekés az alkalmazások használatának elemzéseinek megismeréséhez használnia. Végül [frissítheti az ajánlatot.](./cpp-update-existing-offer.md)

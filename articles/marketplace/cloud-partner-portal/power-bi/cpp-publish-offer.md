---
title: Közzététel a Power BI alkalmazást offer – Azure Marketplace-en |} A Microsoft Docs
description: Tegye közzé a Power BI-alkalmazás az ajánlat a Microsoft AppSource Marketplace-en.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666715"
---
# <a name="publish-power-bi-app-offer"></a>A Power BI alkalmazás-ajánlat közzététele

Az utolsó lépés az ajánlat meghatározott a portálon és a kapcsolódó technikai eszközök létrehozása után, hogy küldje el az ajánlatot a közzétételre.  Ez a folyamat elindításához kattintson a **közzététel** a függőleges menü gombjára a **új ajánlat** ablak.  További információkért lásd: [ajánlatok közzététele az Azure Marketplace és AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Közzétételi lépéseket

A következő ábra szemlélteti a fő lépések "az élő esemény indításra" a közzétételi folyamat.

![A Power BI alkalmazás közzétételi folyamat lépései](./media/publishing-process-steps.png)

Az alábbi táblázat ismerteti ezeket a lépéseket, és biztosít megszakíthat egy becsült maximális időtartam:

|   Közzétételi lépés            |   Time     |   Leírás                                                                  |
| --------------------         |------------| ----------------                                                               |
| Előfeltételek ellenőrzése       | 15 perc     | Információt kínálnak, és kínálnak a rendszer érvényesíti a beállításokat.                            |
| Tanúsítvány                | 1 – 7 nap   | A Power BI hitelesítő csapata elemzi az ajánlatot. A Power BI-alkalmazás azt futtassa egy manuális ellenőrzést teszt keresztül megadott telepítési URL-CÍMEN keresztül az alkalmazás telepítésével. Fő ellenőrzés végrehajtása esetén az alkalmazástanúsítási folyamat; részeként Lásd alább.         |
| Csomagolás                    | \< 1 óra  | Ajánlat technikai eszközök ügyfél használatra vannak csomagolva.                        |
| Lead Generation Registration | \< 1 óra  | Elégtelen rendszerek konfigurált és telepített.                                      |
| Közzétevő jóváhagyás            | \-         | Végső közzétevő áttekintése és az ajánlat élesíti előtt megerősítést. Most is kell egy hivatkozást az előzetes verzióra az ajánlathoz. Ha elégedett az előzetes verzió megjelenése, kattintson a **élő** gombra a **állapot** fülre. Ez a művelet egy kérést küld a bevezetési csapattal a közzé alkalmazását az appsource-ban.    |
| Élő                         | \< 3 óra | Az ajánlat nyilvánosan megjelenik ("élő") az appsource-ban, és az ügyfelek megtekinthetik, és helyezze üzembe az alkalmazást a Power BI-előfizetésekben is. Kap egy megerősítő e-mailt. Bármikor, kattintson a a **minden ajánlat** fülre, és minden ajánlat állapota megjelenik a jobb oldali oszlopban láthatja. Kattintson a a **állapot** lapján megtekintheti az ajánlat részletei a közzétételi folyamat állapotát. |
|   |   |

Lehetővé teszi a folyamat befejezéséhez legfeljebb 8 nap. Nyissa meg a fenti közzétételi lépéseket, miután a Power BI alkalmazás ajánlat jelenik meg a [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI-alkalmazások szakaszban.


### <a name="app-certification-process"></a>Alkalmazástanúsítási folyamat

A Microsoft bevezetési csapata a Power BI-ajánlat beküldött érvényesítése a következő folyamat használja:

1. Nyilvánosan lektorálhatók jogi dokumentumok és a Súgó hivatkozásokat.
2. Támogatási kapcsolattartási adatok érvényességét.
3. Telepítő URL-cím segítségével megfelelő telepítésének ellenőrzése. 
4. Alkalmazás vizsgálja a kártevő szoftverek és más rosszindulatú tartalmat. 
5. Végzi megjelenített tartalom megfelel az alkalmazás leírása.
6. Alkalmazás kapcsolatos műveletek a Power BI-ban várt módon működik: Nyissa meg a jelentéseket és irányítópultokat mintaadatokkal, csatlakozás egyéni adatforrásokhoz, frissítés, stb.

A minősítési csapatának visszajelzést biztosítanak, ha bármilyen hibát észlel.  További információ a Power BI alkalmazás-követelmények: az [Power BI alkalmazás dokumentációja](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>További lépések

Javasoljuk, hogy az alkalmazás rendszeresen figyelje a [az AppSource piactérről](https://appsource.microsoft.com).  Ezenkívül, használjon a [értékesítői Insights](../../cloud-partner-portal-orig/si-getting-started.md) funkcióját a [Cloud Partner Portalon](https://cloudpartner.azure.com/#insights) meg insights piactéren ügyfeleinek és használati.  Továbbá elvégezhet bizonyos [frissítések az ajánlatra](./cpp-update-existing-offer.md).

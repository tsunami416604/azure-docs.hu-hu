---
title: Közzététel a Power BI alkalmazást offer – Azure Marketplace-en |} A Microsoft Docs
description: Tegye közzé a Power BI-alkalmazás az ajánlat a Microsoft AppSource marketplace-en.
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
ms.openlocfilehash: 0e82041dab1932ed7c129b598740b2441aa59315
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822752"
---
# <a name="publish-a-power-bi-app-offer"></a>A Power BI alkalmazás-ajánlat közzététele

Az utolsó lépés egy ajánlatot meghatározott a Cloud Partner portálra és a kapcsolódó technikai eszközök létrehozása után, hogy küldje el az ajánlat közzététele. Ez a folyamat elindításához a bal oldali panelen, a **új ajánlat** ablakban válassza **közzététel**. További információkért lásd: [ajánlatok közzététele az Azure Marketplace és AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Közzétételi lépéseket

A közzétételi folyamat során a fő lépések a következők:

![A Power BI-alkalmazáshoz a közzétételi folyamat lépéseinek ajánlat](./media/publishing-process-steps.png)

Ez a táblázat ismerteti az egyes lépések, és biztosítja a becsült befejezési idő:

|   Közzétételi lépés            |   Time     |   Leírás                                                                  |
| --------------------         |------------| ----------------                                                               |
| Előfeltételek ellenőrzése       | 15 perc     | Információt kínálnak, és kínálnak a rendszer érvényesíti a beállításokat.                            |
| Tanúsítvány                | 1 – 7 nap   | A Power BI hitelesítő csapata elemzi az ajánlatot. A csapat a Power BI alkalmazás manuális ellenőrzési tesztet keresztül telepíti az alkalmazást a megadott telepítési URL-CÍMEN keresztül futtatja. Elsődleges ellenőrzés (később a jelen dokumentumban ismertetett) az alkalmazástanúsítási folyamat részeként történik.         |
| Csomagolás                    | \< 1 óra  | Az ajánlat technikai eszközök ügyfél használatra vannak csomagolva.                        |
| Érdeklődők generálása regisztráció | \< 1 óra  | Elégtelen rendszerek konfigurált és telepített.                                      |
| Közzétevő jóváhagyás            | \-         | Mielőtt az ajánlat élesíti a végső áttekintésre és a megerősítő végzett. Most is rendelkezni fog egy hivatkozást az előzetes verzióra az ajánlathoz. Miután elégedett az előzetes verzió megjelenése, válassza ki **élő** a a **állapot** fülre. Ez egy kérést küld a bevezetési csapattal a közzé alkalmazását az appsource-ban.    |
| Élő                         | \< 3 óra | Az ajánlat nyilvánosan megjelenik ("élő") az appsource-on, és az ügyfelek az alkalmazás megtekintéséhez és üzembe helyezni a Power BI megszűnni. Is kap egy megerősítő e-mailt. A jobb oldali oszlopban lévő a **minden ajánlat** lapon láthatja minden ajánlat állapotát. Az a **állapot** lapon láthatja a részletes közzétételi folyamat állapota az ajánlatban. |
|   |   |

Ez a folyamat befejezéséhez legfeljebb 8 nap engedélyezése. Nyissa meg a fenti közzétételi lépéseket, miután a Power BI alkalmazás ajánlat jelenik meg a [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI alkalmazások szakasz.


### <a name="app-certification-process"></a>Alkalmazástanúsítási folyamat

A Microsoft bevezetési csapata használja ezt a folyamatot a Power BI alkalmazás ajánlat beküldött ellenőrzése:

1. Tekintse át a jogi dokumentumok és a Súgó hivatkozásokat.
2. Ellenőrizze a támogatási kapcsolattartási adatokat.
3. A telepítő URL-cím használatával megfelelő telepítésének ellenőrzése.
4. Az alkalmazás a kártevő szoftverek és más kártékony tartalom vizsgálata.
5. Győződjön meg arról, hogy a megjelenített tartalom egyezik-e az alkalmazás leírása.
6. Győződjön meg arról, hogy a műveletek alkalmazáshoz kapcsolódó Power BI-ban várt módon működik-e. A csapat nyitja meg a jelentéseket és irányítópultokat mintaadatokkal, egyéni adatforrásokhoz kapcsolódik, frissíti az adatokat, és így tovább.

A minősítési csapatának visszajelzést biztosítanak, ha bármilyen hibát észlel.  A Power BI alkalmazás követelményeivel kapcsolatos további információkért lásd: a [Power BI alkalmazás dokumentációja](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>További lépések

Javasoljuk, hogy az alkalmazás rendszeresen figyelje a [az AppSource piactérről](https://appsource.microsoft.com).  Használja a [értékesítői Insights](../../cloud-partner-portal-orig/si-getting-started.md) funkcióját a [Cloud Partner Portalon](https://cloudpartner.azure.com/#insights) kaphat elemzési információkat a marketplace ügyfelei és az alkalmazások használatát. Végül [frissíteni az ajánlatot](./cpp-update-existing-offer.md).

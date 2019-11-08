---
title: Piactéri ajánlatok létrehozása | Azure piactér
description: Ajánlatok létrehozása az Azure-ban és a AppSource piactéren a Cloud Partner Portal használatával
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pabutler
ms.openlocfilehash: 4aa4e154c99f20963485a98d1a21af4ae2b7a1b1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828094"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Azure Marketplace-és AppSource-ajánlatok létrehozása

A Cloud Partner Portal egyik alapvető célja, hogy lehetővé tegye a kiadók számára az ajánlatok létrehozását (és közzétételét) a Microsoft Azure és a AppSource-piactéren.  Ez a művelet mindig az [új ajánlat menüből](../portal-tour/cpp-new-offer-menu.md)a kívánt ajánlat típusának kiválasztásával kezdődik.  Válaszként a megfelelő **új ajánlat** oldal jelenik meg az adott ajánlat típusához.  Az alábbi képen például egy Azure-alkalmazás típusának alapértelmezett **új ajánlat** lapja látható.

![Új ajánlat alapértelmezett lapja](./media/new-offer-page.png)

A lap tetején megjelenő vízszintes menüsorban két Tab-kijelölés érhető el: 
- **Szerkesztő** lap – lehetővé teszi az adatok bevitelét és az új ajánlati példányhoz tartozó eszközök feltöltését.  Alapértelmezés szerint ez a lap jelenik meg.
- **Állapot** lap – megadja a közzétételi állapotot, és felsorolja az érvényesítési és a felülvizsgálati problémákat. 

Ajánlat létrehozásakor a **szerkesztő** lapon adhatja meg az ajánlatra vonatkozó adatokat. 

## <a name="editing-operations"></a>Szerkesztési műveletek

Az adatbeviteli terület fölött található vízszintes eszköztár a következő gombokat jeleníti meg:

|   Gombra    |   Cél                                                          |
|   ------    |  --------                                                          |
| **Mentés**    | A legutóbbi adatbejegyzés módosításainak mentése.  A módosításokat manuálisan kell menteni, mielőtt az oldalról navigál, vagy a módosítások elvesznek. | 
| **Elveti** | A legutóbbi adatbeviteli módosítások elvetése (a legutóbbi mentés óta)             |
| **Összehasonlítása** | Összehasonlítja az aktuális ajánlat állapotát a közzétett ajánlattal.  Csak az ajánlat sikeres közzététele után engedélyezett.  |
| **Közzététel** | A közzétételi folyamat megkezdése ehhez az ajánlathoz                       |
| **Törlés**  | Ezt az ajánlatot a létrehozása után, de a közzététel előtt törli. |
|   |   |


## <a name="editing-tabs"></a>Lapok szerkesztése

Ajánlat létrehozásakor az **új ajánlat** oldal bal oldali függőleges oszlopában található minden lapon meg kell adnia a szükséges és opcionális adatmennyiséget.  Szabványos felhasználói felületi vezérlők – például szövegmezők, legördülő menük és jelölőnégyzetek – az adatgyűjtés során megjelennek.  Bár a szerkesztési lapok adott gyűjteménye az ajánlat típusától függ, a következő táblázat néhány gyakori fület sorol fel.

|      Lap neve       |   Cél                                                            |
|      --------       |   -------                                                            |
| **Ajánlat beállításai**  | Az ajánlat és a közzétevő személyazonossági adatait gyűjti.                    |
| **Termékváltozatok**            | Meghatározza az ajánlat egyes készletezési egységei (SKU) technikai és üzleti jellemzőit |
| **Tesztelési meghajtó**      | Az ezt a választható funkciót támogató típusok esetében az ajánlat szemléltetését határozza meg.  További információ: [Mi az a test Drive?](../test-drive/what-is-test-drive.md)  |
| **Piactér** vagy **kirakat** | Az ajánlatnak a piactéren való listázásához használt szöveges karakterláncokat, dokumentumokat és képeket gyűjti. |
| **Támogatás**         | Az ügyfél, a mérnöki és az online támogatás elérhetőségi adatait gyűjti.  |
|  |  |

A hasonlóan elnevezett lapok tartalma eltérő lehet a különböző ajánlati típusok között.  A lapokra vonatkozó ajánlat-specifikus részleteket az egyes ajánlatokhoz tartozó "ajánlat létrehozása" című szakaszban találja.


## <a name="next-steps"></a>További lépések

Miután létrehozta és mentette az ajánlatot, és a közzététel előtt vagy után [megtekintheti az állapotát](./cpp-view-status-offer.md).

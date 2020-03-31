---
title: Marketplace-ajánlatok létrehozása | Azure Piactér
description: Ajánlatok létrehozása az Azure és az AppSource piacterein a Cloud Partner Portal használatával
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: dsindona
ms.openlocfilehash: d14940eff569a80759e9db0bfa2d6d1541a01377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286472"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Azure Marketplace és AppSource ajánlatok létrehozása

A Cloud Partner Portal egyik alapvető célja, hogy lehetővé tegye a kiadók számára, hogy ajánlatokat hozzanak létre (majd tegyenek közzé a Microsoft Azure és az AppSource piactereken.  Ez a művelet mindig a kívánt ajánlattípus kiválasztásával kezdődik az [Új ajánlat menüből.](../portal-tour/cpp-new-offer-menu.md)  Erre válaszul megjelenik a megfelelő **Új ajánlat** oldal az adott ajánlattípushoz.  Az alábbi képen például egy Azure-alkalmazástípus alapértelmezett **új ajánlat** lapja látható.

![Új ajánlat alapértelmezett lapja](./media/new-offer-page.png)

A vízszintes menüsorban két lapválasztás érhető el az oldal tetején: 
- **Szerkesztő** lap – Lehetővé teszi az új ajánlatpéldány hoz való információbevitelt és az eszközök feltöltését.  Ez a lap alapértelmezés szerint megjelenik.
- **Állapot** lap – Megadja a közzétételi állapotot, és felsorolja az érvényesítési és ellenőrzési problémákat. 

Amikor létrehoz egy ajánlatot, a **Szerkesztő** lapon adhatja meg az ajánlattal kapcsolatos információkat. 

## <a name="editing-operations"></a>Szerkesztési műveletek

Az adatbeviteli terület felett található vízszintes eszköztár a következő gombokat jeleníti meg:

|   Gomb    |   Cél                                                          |
|   ------    |  --------                                                          |
| **Mentés**    | Menti az adatbevitel legutóbbi módosításait.  A módosításokat manuálisan kell mentenie, mielőtt elnavigálna az oldalról, vagy a módosítások elvesznek. | 
| **Elvetés** | Elveti a legutóbbi adatbeviteli módosításokat (az utolsó mentés óta)             |
| **Összehasonlítás** | Összehasonlítja az aktuális ajánlat állapotát a közzétett ajánlattal.  Csak az ajánlat sikeres közzététele után engedélyezve van.  |
| **Közzététele** | Az ajánlat közzétételi folyamatának megkezdése                       |
| **Szabályzat**  | Az ajánlat törlése a létrehozás után, de a közzététel előtt. |
|   |   |


## <a name="editing-tabs"></a>Szerkesztőlapok

Az ajánlat létrehozásakor adja meg a szükséges és választható adatokat az **Új ajánlat** lap bal oldali függőleges oszlopában található lapokon.  Az adatgyűjtéshez az általános jogú felhasználói felület vezérlői – például a szövegdobozok, a legördülő menük és a jelölőnégyzetek – jelennek meg az adatgyűjtéshez.  Bár a szerkesztési lapok konkrét gyűjteménye az ajánlat típusától függ, az alábbi táblázat néhány gyakori lapot sorol fel.

|      Lap neve       |   Cél                                                            |
|      --------       |   -------                                                            |
| **Ajánlat beállításai**  | Összegyűjti az ajánlat- és közzétevői identitásadatait.                    |
| **Termékváltozatok**            | Meghatározza az ajánlat minden egyes raktározási egység (SKU) verziójának műszaki és üzleti jellemzőit |
| **Tesztverzió**      | Az opcionális funkciót támogató típusok esetében meghatározza az ajánlat bemutatóját.  További információ: [Mi az a Tesztvezetés?](../test-drive/what-is-test-drive.md)  |
| **Piactér** vagy **kirakat** | Az ajánlat marketplace-en való listázásához használt szöveges karakterláncokat, dokumentumokat és képeket gyűjti |
| **Támogatás**         | Kapcsolattartási adatok gyűjtése az ügyfelek, a mérnöki és az online támogatás számára  |
|  |  |

A hasonlóan elnevezett lapok tartalma különböző ajánlattípusokonként eltérő lehet.  Ezeknek a lapoknak az ajánlatspecifikus részletei az "Ajánlat létrehozása" részben találhatók az egyes ajánlattípusokhoz.


## <a name="next-steps"></a>További lépések

Az ajánlat létrehozása és mentése, valamint közzététele előtt vagy után [megtekintheti annak állapotát.](./cpp-view-status-offer.md)

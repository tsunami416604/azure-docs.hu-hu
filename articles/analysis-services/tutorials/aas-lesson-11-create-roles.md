---
title: "Az Azure Analysis Services oktatóanyaga – 11. lecke: Szerepkörök létrehozása | Microsoft Docs"
description: "A lecke azt ismerteti, hogyan hozhat létre szerepköröket az Azure Analysis Services oktatóprojektjében."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: 5fb0e2dd56e373ecf723a3672d9538bcc6dc68e3
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="create-roles"></a>Szerepkörök létrehozása

Ebben a leckében szerepköröket hozhat létre. A szerepkörök biztonságot nyújtanak a modelladatbázis objektumai és adatai számára, mert csak a szerepkör tagjai számára biztosítanak hozzáférést. Minden szerepkör egyetlen engedéllyel van definiálva: Nincs, Olvasás, Olvasás és feldolgozás, Feldolgozás vagy Rendszergazda. A szerepkörök a Szerepkörkezelő használatával definiálhatók a modell létrehozása során. A modell üzembe helyezése után az SQL Server Management Studio (SSMS) használatával kezelheti a szerepköröket. További információkért lásd: [Szerepkörök](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular).
  
> [!NOTE]  
> A szerepkörök létrehozása nem kötelező a jelen oktatóanyag elvégzéséhez. Alapértelmezés szerint a fiók, amellyel jelenleg be van jelentkezve, rendszergazdai jogosultságokkal rendelkezik a modellben. Ahhoz azonban, hogy a szervezet többi felhasználója is tallózhasson egy jelentéskészítő ügyféllel, létre kell hoznia legalább egy olvasási engedéllyel rendelkező szerepkört, és hozzá kell adnia a felhasználókat tagokként.  
  
Három szerepkört kell létrehoznia:  
  
-   **Értékesítési vezető** – Ez a szerepkör tartalmazhatja a szervezet összes olyan felhasználóját, akiknek minden modellobjektumhoz és -adathoz olvasási engedélyt szeretne biztosítani.  
  
-   **Értékesítési elemző – USA** – Ez a szerepkör tartalmazhatja a szervezet összes olyan felhasználóját, akiknek csak az Egyesült Államokban zajló értékesítéssel kapcsolatos adatok tallózását szeretné engedélyezni. Ennél a szerepkörnél egy DAX-képlettel definiálhat egy *Sorszűrőt*, amely korlátozza a tagok hozzáférését, és csak az Egyesült Államokra vonatkozó adatok tallózását teszi lehetővé.  
  
-   **Rendszergazda** – Ez a szerepkör azon felhasználókat tartalmazhatja, akiket rendszergazdai engedélyekkel kíván ellátni. Ezek korlátlan hozzáférést és engedélyeket biztosítanak, amelyekkel felügyeleti feladatok hajthatók végre a modelladatbázison.  
  
Mivel a szervezetben található Windows-felhasználói és csoportfiókok egyediek, a saját cégéből is hozzáadhat fiókokat tagokként. A jelen oktatóanyag esetében azonban üresen is hagyhatja a tagokat. Az egyes szerepkörök hatásait a 12., Elemzés az Excelben című lecke során tesztelheti.  
  
A lecke elvégzésének várható időtartama: **15 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a témakör a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. A jelen leckében foglalt feladatok végrehajtása előtt be kell fejeznie az előző leckét ([10. lecke: Partíciók létrehozása](../tutorials/aas-lesson-10-create-partitions.md)).  
  
## <a name="create-roles"></a>Szerepkörök létrehozása  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Értékesítési vezető felhasználói szerepkör létrehozása  
  
1.  A Táblázatosmodell-tallózóban kattintson a jobb gombbal a **Szerepkörök** > **Szerepkörök** elemre.  
  
2.  A Szerepkörkezelőben kattintson az **Új** elemre.  
  
3.  Kattintson az új szerepkörre, majd a **Név** oszlopban nevezze át a következőre: **Értékesítési vezető**.  
  
4.  Az **Engedélyek** oszlopban kattintson a legördülő listára, majd válassza az **Olvasás** engedélyt. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Opcionális: Kattintson a **Tagok** lapra, majd a **Hozzáadás** parancsra. A **Felhasználók vagy csoportok kiválasztása** párbeszédpanelen adja meg a szervezetébe tartozó azon Windows-felhasználókat vagy -csoportokat, amelyeket hozzá kíván adni a szerepkörhöz.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Értékesítési elemző – USA felhasználói szerepkör létrehozása  
  
1.  A Szerepkörkezelőben kattintson az **Új** elemre.    
  
2.  Nevezze át a szerepkört a következőre: **Értékesítési elemző – USA**.  
  
3.  Adjon **Olvasás** engedélyt a szerepkörnek.  
  
4.  Kattintson a Sorszűrők fülre, majd csak a **DimGeography** táblában írja be az alábbi képletet a DAX-szűrő oszlopba:  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    A Sorszűrő képlet eredményének logikai (IGAZ/HAMIS) értéknek kell lennie. Ezzel a képlettel azt adja meg, hogy csak azon sorok legyenek láthatók a felhasználó számára, amelyek Ország-/régiókód értéke „US” (Egyesült Államok).  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Opcionális: Kattintson a **Tagok** lapra, majd a **Hozzáadás** parancsra. A **Felhasználók vagy csoportok kiválasztása** párbeszédpanelen adja meg a szervezetébe tartozó azon Windows-felhasználókat vagy -csoportokat, amelyeket hozzá kíván adni a szerepkörhöz.  
  
#### <a name="to-create-an-administrator-user-role"></a>Rendszergazdai felhasználói szerepkör létrehozása  
  
1.  Kattintson az **Új** lehetőségre.  
  
2.  Nevezze át a szerepkört a következőre: **Rendszergazda**.  
  
3.  Adjon **Rendszergazda** engedélyt a szerepkörnek.  
  
4.  Opcionális: Kattintson a **Tagok** lapra, majd a **Hozzáadás** parancsra. A **Felhasználók vagy csoportok kiválasztása** párbeszédpanelen adja meg a szervezetébe tartozó azon Windows-felhasználókat vagy -csoportokat, amelyeket hozzá kíván adni a szerepkörhöz. 
  
  
## <a name="whats-next"></a>A következő lépések
[12. lecke: Elemzés az Excelben](../tutorials/aas-lesson-12-analyze-in-excel.md).

  
  

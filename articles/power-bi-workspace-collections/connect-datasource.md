---
title: A Power BI munkaterület gyűjtemények egy adatforráshoz való kapcsolódáshoz |} Microsoft Docs
description: Tudnivalók a Power BI munkaterület gyűjtemények egy adatforráshoz való kapcsolódáshoz.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 5a154c4899db974645bb7ade028d8bd8f267aad7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="connect-to-a-data-source"></a>Kapcsolódás adatforráshoz

A **Power BI munkaterület gyűjtemények**, jelentés beágyazása az alkalmazásba. A Power BI-jelentés beágyazása az alkalmazásba, a jelentés csatlakozik-e az alapul szolgáló adatokat által **importálása** egy másolatot, az adatok vagy a **közvetlenül csatlakozó** a forrás-történő **DirectQuery** .

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

Az alábbiakban a két módszer (**importálás** és **DirectQuery**) közti különbségeket láthatja.

| Importálás | DirectQuery |
| --- | --- |
| A táblákat, oszlopokat *és az adatok* importálhatja vagy másolhatja be a jelentésben adatkészlet. A mögöttes adatok módosulásainak megtekintéséhez frissítse, vagy importáljon egy teljes aktuális adatkészletet újra. |Csak *táblákat és oszlopokat* importálhatja vagy másolhatja be a jelentésben adatkészlet. Mindig a legfrissebb adatok megtekintéséhez. |

A Power BI munkaterület gyűjtemények akkor DirectQuery használható felhőalapú adatforrások, de nem a helyi adatforrások jelenleg.

> [!NOTE]
> Az On-Premises átjáró jelenleg nem támogatott a Power BI munkaterület gyűjtemények. Ez azt jelenti, hogy a helyszíni adatforrások DirectQuery nem használhat.

## <a name="supported-data-sources"></a>Támogatott adatforrások

**DirectQuery**
* Azure SQL Database
* Azure SQL Data Warehouse

**Importálás**

Az összes az elérhető adatforrásokhoz Power BI Desktop segítségével importálhatja. Lehetővé teszi a **nem** tudni, hogy a Power BI munkaterület gyűjtemények adatainak frissítése. Töltse fel a módosításokat a Power BI munkaterület gyűjteményeket a PBIX-fájl van. Rendelkezésre álló átjáró okozza. 

## <a name="benefits-of-using-directquery"></a>DirectQuery használatának előnyei

Nincsenek két elsődleges előnnyel jár, használatakor **DirectQuery**:

* **DirectQuery** is létrehozhat képi megjelenítések nagy adatkészletek, ahol egyébként lenne, amelyeknek első importálandó keresztül az összes adat.
* Az alapul szolgáló adatváltozásokat lehet szükség, adatok frissítését, és az egyes jelentések aktuális adatok megjelenítéséhez szükség lehet szükség nagy adatátvitelt, és amelyeknek újraimportáló adatok. Ezzel szemben **DirectQuery** jelentések mindig aktuális adatokat használják.

## <a name="limitations-of-directquery"></a>DirectQuery korlátozásai

Néhány korlátozást érdemes használatával **DirectQuery**:

* Minden táblának egyetlen adatbázisra kell származnia.
* Ha a lekérdezés túl összetett, hiba történik. A hiba elhárításához a lekérdezést, hogy kevésbé összetett kell refactor. Ha a lekérdezés összetett kell lennie, szeretné-e az adatimportálás használata helyett **DirectQuery**.
* Kapcsolat szűrés csak egy irányban ahelyett, hogy mindkét irányban korlátozva.
* Egy oszlop adattípusa nem módosítható.
* Alapértelmezés szerint engedélyezett a mértékek DAX-kifejezések korlátozások kerülnek. Lásd: [DirectQuery és intézkedések](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery és intézkedések
Az alapul szolgáló adatforrásban küldött lekérdezések rendelkezik a megfelelő teljesítmény érdekében az korlátozások intézkedések a küszöbértéket. Használata esetén **Power BI Desktop**, speciális felhasználók engedélyezze a Ez a korlátozás kiválasztásával **fájl > lehetőségek és beállítások > Beállítások**. Az a **beállítások** párbeszédpanelen válasszon **DirectQuery**, válassza ki, **nem korlátozott mértékek engedélyezése DirectQuery módban**. Ez a lehetőség van kiválasztva, ha bármely mérték érvényes DAX-kifejezés használható. Lehet, hogy a felhasználók tisztában; azonban, hogy egyes kifejezések is végre, amikor az adatok importálása azt eredményezheti, lassú lekérdezi a háttérrendszer forrás, amikor a **DirectQuery** mód. 

## <a name="see-also"></a>Lásd még:

* [Ismerkedés a Microsoft Power BI munkaterület gyűjtemények](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)


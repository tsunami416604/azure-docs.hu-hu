---
title: A Power BI-Munkaterületcsoportok egy adatforráshoz való kapcsolódáshoz |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a Power BI-Munkaterületcsoportok adatforrások.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: c67369edec2f3c84ada14e6c2a3a3ab8056b3e0c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870095"
---
# <a name="connect-to-a-data-source"></a>Kapcsolódás adatforráshoz

A **Power BI-Munkaterületcsoportok**, jelentéseket beágyazhatja az alkalmazásába. Amikor a Power BI-jelentés beágyazása az alkalmazásba, a jelentés csatlakozik-e az alapul szolgáló adatok **importálása** egy példányát, az adatok vagy a **közvetlenül csatlakozó** a forrás-történő **DirectQuery** .

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

Az alábbiakban a két módszer (**importálás** és **DirectQuery**) közti különbségeket láthatja.

| Importálás | DirectQuery |
| --- | --- |
| Táblákat, oszlopokat *és az adatok* importálhatja vagy másolhatja a jelentés-adatkészletbe. A mögöttes adatok bekövetkezett változások megtekintéséhez frissítse, vagy importálja, a teljes aktuális adatkészletet újra. |Csak *táblák és oszlopok* importálhatja vagy másolhatja a jelentés-adatkészletbe. Mindig megtekintheti a legfrissebb adatok. |

A Power BI-Munkaterületcsoportok akkor a DirectQuery is használható a felhőben található adatforrásokhoz, de nem a helyszíni adatforrások jelenleg.

> [!NOTE]
> Az On-Premises Data Gateway jelenleg nem támogatott a Power BI munkaterületi gyűjtemények. Ez azt jelenti, hogy a DirectQuery a helyszíni adatforrások esetében nem használható.

## <a name="supported-data-sources"></a>Támogatott adatforrások

**DirectQuery**
* Azure SQL-adatbázis
* Azure SQL Data Warehouse

**Importálás**

Importálhatja az összes elérhető adatforrások a Power BI desktopban. Lehetővé teszi a **nem** képesek az adatokat a Power BI-Munkaterületcsoportok belül. Töltse fel a PBIX-fájlt a Power BI-Munkaterületcsoportok módosításokat kell. Rendelkezésre álló átjáró okozza. 

## <a name="benefits-of-using-directquery"></a>A DirectQuery használatának előnyei

Előnyökkel két elsődleges használatakor **DirectQuery**:

* **A DirectQuery** hozhat létre olyan vizualizációkat Nagy adatkészleteken, ahol egyébként lenne megoldhatatlanná tenné importálása összes adatát.
* A mögöttes adatok változásai lehet szükség az adatok frissítését, és egyes jelentések esetében az aktuális adatok megjelenítéséhez szükség lehet szükség nagy adatátvitelek, így újraimportáló adatok megoldhatatlanná tenné. Ezzel szemben az **DirectQuery** jelentések mindig aktuális adatokat használja.

## <a name="limitations-of-directquery"></a>A DirectQuery korlátozásai

Néhány korlátozást használatával **DirectQuery**:

* Minden tábla egyetlen adatbázishoz kell származnia.
* Ha a lekérdezés túl összetett, hiba történik. A hiba javításához a lekérdezést kell bontani, így kevésbé összetett. A lekérdezés összetett kell lennie, ha az adatok használata helyett importálnia kell **DirectQuery**.
* Szűrés kapcsolat korlátozva mindkét irány helyett csak egy irányban.
* Egy oszlop adattípusa nem módosítható.
* Alapértelmezés szerint korlátozások kerülnek mértékekben engedélyezett DAX-kifejezésekben. Lásd: [DirectQuery és mértékek](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>A DirectQuery és az intézkedések
Annak érdekében, hogy az alapul szolgáló adatforrás felé küldött lekérdezések elfogadható teljesítménye lesz, korlátai intézkedéseket vannak elő. Használata esetén **Power BI Desktop**, speciális felhasználók megkerülhetik a korlátozás kiválasztásával eldönthetik **fájl > lehetőségek és beállítások > Beállítások**. Az a **beállítások** párbeszédpanelen válasszon **DirectQuery**, és jelölje be **nem korlátozott mértékek engedélyezése DirectQuery módban**. Ez a lehetőség van kiválasztva, ha van, a mértékekre érvényes DAX-kifejezések bármelyike használható. Felhasználók figyelembe kell venniük; azonban, hogy végrehajtani az mikor importált adatokkal jól működő némely kifejezés vonhat maga után a lassú lekérdezések, a háttérkiszolgáló az adatforrásba **DirectQuery** mód. 

## <a name="see-also"></a>Lásd még:

* [Ismerkedés a Microsoft Power BI munkaterületi gyűjteményekkel](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

További kérdései vannak? [Tegye próbára a Power BI közösségét](https://community.powerbi.com/)


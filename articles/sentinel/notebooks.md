---
title: Notebookok használatával az Azure-on Előzetesben Sentinel-vadászat képességek |} A Microsoft Docs
description: Ez a cikk ismerteti a notebookok használata az Azure-Sentinel vadászat képességeket.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 63ce2be847017ed7e80fe5e573d5553311f6af2f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107678"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>Az anomáliák hunt notebookok használata

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure-Sentinel teljesítményét kihasználó interaktív Jupyter-notebookok insight és a műveletek vizsgálata vagy a biztonsági anomáliák kezelésére, a környezetben lévő hunt biztosítása érdekében. Az Azure Sentinel előre betöltött biztonsági elemzők a Microsoft által fejlesztett böngészését származnak. Minden egyes notebook célja egy adott használati esetekhez önálló munkafolyamattal készült. Vizualizációk minden notebook gyorsabb adatáttekintés és fenyegetés vadászat szerepelnek. Testreszabhatja az igényeinek, előzmények nélküli új jegyzetfüzet létrehozása vagy notebookok importálhat az Azure-Sentinel a beépített notebookok "GitHub közösségi. Jupyter-notebookok importálódnak a projekt az Azure-jegyzetfüzetek oldalon – Ez lehetővé teszi a felhasználó számára elérhető minden egy helyen a Azure Sentinel-jegyzetfüzetekre. Notebookok is futtatható egyetlen kattintással, vagy saját környezetükben egyeztetendő felhasználó által konfigurálható.

A teljes körűen integrált használattal lehetővé teszi, hogy a jegyzetfüzetek futtatásához a Felhőbeli számítási és tárolási nélkül alapul szolgáló karbantartási állnak. Kihasználhatja a meglévő Jupyter notebookok ökoszisztéma lehetővé teszi lehetővé a versenytársak közül forrás modellek, ügyféladatok megosztása nélkül. 


Minden egyes notebook az Azure-jegyzetfüzetek (jelenleg előzetes verzióban érhető el), egy interaktív fejlesztési környezetben az Azure-felhőben üzemel. Notebookok olyan mindig elérhető, mindig elérhető, bármilyen böngészőből, a világ bármely pontján található.  Az Azure-Sentinel "beépített notebookok a vizsgálati és vadászat a rendszer klónozza a projektbe, amely Önhöz tartozik, és amely módosíthatja, és testre szabni a környezethez. A beépített legnépszerűbb jegyzetfüzet a következők:

- **Vizsgálat és vadászat**: Az interaktív jegyzetfüzetek lehetővé teszi, hogy gyors, riasztások különböző osztályú osztályozási kapcsolódó tevékenység beolvasása és a riasztás bővítését hozzárendelt tevékenység és a riasztás generálása, amelyről adatokat.

- **Végponti gazdagép interaktív vadászat**: Lehetővé teszi egy végpont gazdagéphez tartozó biztonsági vonatkozó tevékenységeinek be részletezésével vonatkozó jeleit biztonsági incidensek hunt.  

- **Office jelentkezzen be az interaktív hunting**: Lehetővé teszi, hogy a földrajzi helyeken gyanús naplók megjelenítése, valamint a szokatlan bejelentkezési mintákon az Office 365-adatok megjelenítése a gyanús bejelentkezési hunt.

## <a name="run-a-notebook"></a>-Jegyzetfüzet futtatása
A következő példában azt a jegyzetfüzet futtatásához használja beépített hely rendellenességek megtekintheti, ha bárki váratlan helyen van tevékenységet a hálózaton részletes ról kereséséhez.

1. Az Azure-Sentinel-portálon kattintson a **notebookok** , majd válassza ki a beépített notebookok bármelyikét.
  
   ![notebook kiválasztása](./media/notebooks/select-notebook.png)

3. Kattintson a **importálás** , klónozza a GitHub-adattárat a Azure notebookok projektbe.
   ![Importálás notebook](./media/notebooks/import1.png)
4. Minden egyes notebook végigvezeti egy hunt vagy egy vizsgálatot hajt végre a lépéseket. Modellek, könyvtárak, és más függőségek és a konfiguráció a csatlakozást az Azure-Sentinel automatikusan importált egykattintásos végrehajtásának engedélyezéséhez. Kód és a egy Jegyzetfüzet futtatásához szükséges kódtárakat a rendszer előre betölti. Azonnal megkezdheti a jegyzetfüzet futtat a Log Analytics-munkaterület konfiguráció nélkül.

   ![Tárház importálása](./media/notebooks/import2.png)

5. Ismerje meg, módosítása és a megadott minta jegyzetfüzetek futtatása. Ezek használhatók kielégítésének számos különböző forgatókönyvekhez.

   ![notebook kiválasztása](./media/notebooks/import3.png)



## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan futtathat egy vadászat vizsgálat notebookok használatával az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Proaktív módon hunt fenyegetések](hunting.md)
- [Könyvjelzőkkel mentése közben vadászat hasznos információkat nyerhet ki](bookmarks.md)
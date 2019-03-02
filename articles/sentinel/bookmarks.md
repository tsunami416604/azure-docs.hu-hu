---
title: Nyomon követheti, az adatok vadászat az Azure-Sentinel-Előzetesében vadászat könyvjelzők használata során |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan használható az Azure-Sentinel vadászat könyvjelzők adatok nyomon követéséhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 9a7ceeab6d2ad761752f778038692256bd87624b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242836"
---
# <a name="keep-track-of-data-during-hunting"></a>Nyomon követheti, adatok során vadászat

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Veszélyforrások elleni vadászat legtöbbször keres a rosszindulatú működést bizonyítékokat naplóadatok hegyekbe áttekintése. A folyamat során kutató ne feledje, nyissa meg újra, és lehetséges feltételezéseket érvényesítése és a egy biztonsági sérülése a teljes esettanulmányt ismertetése részeként elemzését kívánt események kereséséhez.
Könyvjelzők vadászat segítséget nyújthat, megőrizve a lekérdezés eredményeit, hogy a megfelelő szükségesnek vélt együtt futtatta a Log Analytics, a lekérdezések által. Jegyezze fel a környezetfüggő megfigyelések is, és hivatkozni az eredményeket megjegyzések és a címkék hozzáadását. Könyvjelzők adatok Ön és a csapattársai könnyen csoportmunkához látható-e.   

Nyissa meg újra a könyvjelzők adatok bármikor a a **könyvjelző** lapján a **vadászat** lapot. Szűrés használatához, és a keresési beállításokat adott adatok megtalálhatja az aktuális vizsgálat céljára. Másik lehetőségként megtekintheti a közvetlenül a könyvjelzők adatait a **HuntingBookmark** táblázat a Log Analyticsben. Ez lehetővé teszi, hogy a szűrni, Összegzés és csatlakozzon a könyvjelzők adatok más adatforrásokkal, így könnyen elemeiről bizonyítékok kereséséhez.

A könyvjelzők adatokat jelenítheti meg kattintva **vizsgálat**. Ezzel elindítja a vizsgálati élmény, amelyben megtekintése, vizsgálja meg és az eredményeket egy interaktív entitás-graph diagram és ütemterv használatával vizuálisan kommunikációhoz.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Log Analytics-lekérdezés futtatása az Azure-Sentinel

1. Az Azure-Sentinel-portálon kattintson a **vadászat** gyanús és rendellenes viselkedésének lekérdezések futtatásához.

1. Vadászat kampány futtatni, válassza ki azt a vadászat lekérdezések és a bal oldali, tekintse át az eredményeket. 

1. Kattintson a **lekérdezési eredmények megtekintése** vadászat lekérdezésben **részletek** lap használatával jeleníthetők meg a lekérdezés eredménye a Log Analyticsben. Íme egy példa megjelenő Ha egyéni SSH találgatásos támadás támadási lekérdezést futtatta.
  
   ![eredmények megjelenítése](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Könyvjelző hozzáadása

1. A Log Analytics lekérdezési eredmények listájában bontsa ki az Ön érdeklődésére információkat tartalmazó sor.

4. Válassza ki a sor végén található három pontra (...), és válassza ki **vadászat a Könyvjelzők hozzáadása**.
5. A jobb oldalon található a **részletei** lapon frissítse a nevét, és adja hozzá a címkéket, és segít azonosítani, hogy mi volt az elemmel kapcsolatos érdekes jelzi.
6. Kattintson a **mentése** a módosítások véglegesítéséhez. Az összes könyvjelzők adatok más kutató van megosztva, és egy együttműködésen alapuló hibavizsgálati felület első lépését.

   ![eredmények megjelenítése](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> A könyvjelzők tetszőleges elindítása az Azure Sentinel-Log Analytics naplók oldalán vagy a Log Analytics oldaláról behúzás és a vadászat oldaláról megnyitott létrehozott lekérdezések a Log Analytics-lekérdezéseket is használhatja. Nem adhat hozzá egy könyvjelzőt, ha indítsa el a Log Analytics Sentinel-Azure-on kívül. 

## <a name="view-and-update-bookmarks"></a>Tekintse meg és frissítse a könyvjelzők 

1. Az Azure-Sentinel-portálon kattintson a **vadászat**. 
2. Kattintson a **könyvjelzők** könyvjelzők listájának megtekintéséhez az oldal közepén fülre.
3. Egy adott könyvjelzőt kereséséhez használja a keresési mezőbe, vagy szűrő beállításokat.
4. Válassza ki az egyes könyvjelzők alább a rácsban könyvjelző részleteinek megtekintéséhez a jobb oldali ablaktáblában.
5. Címkék és megjegyzések frissítéséhez kattintson a szövegdobozai a, majd kattintson **mentése** megőrzése érdekében a módosításokat.

   ![eredmények megjelenítése](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>A Log Analytics könyvjelzők adatok megtekintése 

A könyvjelzők adatok megtekintéséhez a Log Analytics több lehetőség van. 

A könyvjelzők lekérdezéseket, eredményeket vagy előzmények megtekintése legkönnyebben a kívánt, a könyvjelző választásával a **könyvjelzők** táblát, és a részletek panelen található hivatkozásokra. A lehetőségek a következők: 
- Kattintson a **lekérdezés megtekintése** az adatforrás-lekérdezés megtekintése a Log Analyticsben.  
- Kattintson a **könyvjelző előzményeinek megtekintése** megtekintheti az összes megjelölése könyvjelzővel metaadatok, többek között: ki hajtotta végre a frissítést, a frissített értékeket és az időt a frissítés történt. 

- Minden könyvjelző nyers könyvjelző adatai kattintva is megtekintheti **lássa el könyvjelzővel naplók** a könyvjelző rács fölötti. Ez a nézet a kapcsolódó metaadatok vadászat könyvjelző táblában jelennek meg az összes, a könyvjelzőket. KQL lekérdezések segítségével szűrheti le az adott könyvjelző keres a legújabb verzióra.  


> [!NOTE]
> Nincs jelentős késleltetés (percekben) között lehet egy könyvjelző létrehozását és, amikor megjelenik a a **HuntingBookmark** tábla. Javasoljuk, hogy először hozza létre a könyvjelzők, majd az adatok betöltött után elemezheti. 

## <a name="delete-a-bookmark"></a>Könyvjelző törlése
Ha törölni szeretné egy könyvjelző tegye a következőket: 
1.  Nyissa meg a th **vadászat könyvjelző** fülre. 
2.  Válassza ki a cél könyvjelzőt.
3.  Válassza ki a végén a sort, majd válassza a három pontra (...) **Delete könyvjelző**.
    
A Könyvjelzők törlése eltávolítja a könyvjelző listájából a **könyvjelző** fülre.  A Log Analytics "HuntingBookmark" tábla továbbra is korábbi könyvjelző bejegyzéseket tartalmaz, de a legújabb bejegyzést változik a **SoftDelete** értéke TRUE, megkönnyítve a régi könyvjelzők szűréséhez.  Könyvjelzők törlése nem távolítja el a entitások az egyéb könyvjelzők vagy riasztások társított hibavizsgálati felület. 


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan futtathat egy vadászat vizsgálat könyvjelzők használata az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:


- [Proaktív módon hunt fenyegetések](hunting.md)
- [Automatizált vadászat kampányok notebookok használata](notebooks.md)
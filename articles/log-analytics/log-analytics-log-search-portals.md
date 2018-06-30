---
title: Portálok a létrehozása és módosítása az Azure Naplóelemzés napló lekérdezések |} Microsoft Docs
description: Ez a cikk ismerteti a portálok, melyekkel az Azure Naplóelemzés létrehozásához és szerkesztéséhez jelentkezzen kereséseket.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: e2ea0bf1fb3f1c63f4e6f037e465e8fdfd9a4374
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132930"
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portálok a létrehozása és módosítása az Azure Naplóelemzés napló lekérdezések

A különböző pontjain lehet elérni a Naplóelemzési napló keresések segítségével adatainak lekérése a munkaterületen.  Ténylegesen létrehozásának, és a lekérdezések használata interaktív visszaadott adatok mellett, ha szerkesztésének, két lehetősége, hogy az alábbiakban található.  

## <a name="log-search"></a>Naplókeresés 
Az Azure-portálon elérhető-e a napló lapon.  Alkalmas alapvető lekérdezések külön sorba hozható létre.  Naplófájl-keresési egy külső portál megnyitása nélkül használható, és számos feladatot végrehajtani a napló keresések többek között a riasztási szabályok létrehozásához, számítógép-csoportok létrehozásáról és a lekérdezés eredményeit az használhatja.  

Naplófájl-keresési több szolgáltatásokat nyújt a lekérdezés szerkesztése a lekérdezési nyelv teljes ismerete nélkül.  Ezeket a funkciókat az összegzését kaphat [létrehozás napló megkeresi a naplófájl-keresési Azure Naplóelemzés](log-analytics-log-search-log-search-portal.md).


![Naplófájl-keresési lap](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Speciális Analytics portál
A speciális elemzés portál egy dedikált portál, amely speciális funkció nem érhető el a napló keresési Azure-portálról.  Szolgáltatásai közé tartozik a lekérdezés több sorban való szerkesztése, a szelektív kódvégrehajtás, a kontextusfüggő Intellisense és az intelligens elemzés.  Az Advanced Analytics portál legalkalmasabb összetett lekérdezések, amelyek vagy mentett napló keresést vagy másolja és illeszthetők be más Naplóelemzési elemek.  A napló lapon egy hivatkozást a Advanced Analytics portálon indíthatja el.

![Speciális Analytics portál](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


A speciális funkciók miatt általában használni a speciális elemzés portal az elsődleges eszközként létrehozása és szerkesztése a lekérdezések.  Egyszer meghatározta, hogy a lekérdezés megfelelően működik-e, majd lesz másolja és illessze be máshol például napló keresése oldal vagy adatforrásnézet-tervezőből.  

### <a name="firewall-requirements"></a>A tűzfalra vonatkozó követelmények
A böngésző Advanced Analytics portál eléréséhez a következő címekre hozzáférésre van szüksége.  Ha a böngésző a tűzfalon keresztül fér hozzá az Azure-portálon, engedélyeznie kell ezeket a címeket a hozzáférést.

| URI | IP | Portok |
|:---|:---|:---|
| portal.loganalytics.io | Dinamikus | 80,443 |
| api.loganalytics.io    | Dinamikus | 80,443 |
| docs.loganalytics.io   | Dinamikus | 80,443 |


## <a name="next-steps"></a>További lépések

- Az oktatóanyag bízná [naplófájl-keresési](log-analytics-tutorial-viewdata.md) megtudhatja, hogyan létrehozhat olyan lekérdezéseket, a lekérdezési nyelv használatával
- Tekintse meg a [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587) kifinomult lekérdezések létrehozása és a napló keresések a fejlesztési környezet használata.


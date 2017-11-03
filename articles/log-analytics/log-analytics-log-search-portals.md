---
title: "Portálok a létrehozása és módosítása az Azure Naplóelemzés napló lekérdezések |} Microsoft Docs"
description: "Ez a cikk ismerteti a portálok, melyekkel az Azure Naplóelemzés létrehozásához és szerkesztéséhez jelentkezzen kereséseket."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte; bwren
ms.openlocfilehash: b205f226d95d94b938a70a834ac0147e76d459ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portálok a létrehozása és módosítása az Azure Naplóelemzés napló lekérdezések

A különböző pontjain lehet elérni a Naplóelemzési napló keresések segítségével adatainak lekérése a munkaterületen.  Ténylegesen létrehozásának, és a lekérdezések használata interaktív visszaadott adatok mellett, ha szerkesztésének, két lehetősége, hogy az alábbiakban található.  

## <a name="log-search"></a>Naplókeresés 
Az Azure-portálon elérhető-e a napló lapon.  Alkalmas alapvető lekérdezések külön sorba hozható létre.  Naplófájl-keresési egy külső portál megnyitása nélkül használható, és számos feladatot végrehajtani a napló keresések többek között a riasztási szabályok létrehozásához, számítógép-csoportok létrehozásáról és a lekérdezés eredményeit az használhatja.  

Naplófájl-keresési több szolgáltatásokat nyújt a lekérdezés szerkesztése a lekérdezési nyelv teljes ismerete nélkül.  Ezeket a funkciókat az összegzését kaphat [létrehozás napló megkeresi a naplófájl-keresési Azure Naplóelemzés](log-analytics-log-search-log-search-portal.md).


![Naplófájl-keresési lap](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Speciális Analytics portál
A speciális elemzés portál egy dedikált portál, amely speciális funkció nem érhető el a napló keresési Azure-portálról.  Szolgáltatások közé tartozik a lekérdezés több sorban szerkesztése, szelektív módon hajtható végre a kódot, környezetfüggő Intellisense és intelligens Analytics képes.  Az Advanced Analytics portál legalkalmasabb összetett lekérdezések, amelyek vagy mentett napló keresést vagy másolja és illeszthetők be más Naplóelemzési elemek.  A napló lapon egy hivatkozást a Advanced Analytics portálon indíthatja el.

![Speciális Analytics portál](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


A speciális funkciók miatt általában használni a speciális elemzés portal az elsődleges eszközként létrehozása és szerkesztése a lekérdezések.  Egyszer meghatározta, hogy a lekérdezés megfelelően működik-e, majd lesz másolja és illessze be máshol például napló keresése oldal vagy adatforrásnézet-tervezőből.  Mivel az Advanced Analytics portál már támogatja több sort tartalmazó lekérdezések ellenére kell venni a figyelembe a következőket, amikor a lekérdezés másol ezen a portálon.

- Megjegyzések a lekérdezés kell távolítania, mielőtt másolni és beilleszteni egy másik helyre.  Egy sor Megjegyzés két perjeleket az azt megelőző (/ /).  Ha több sor lekérdezés beillesztése egy egysoros, a sortörések törlődnek.  Megjegyzések érhetők el, ha az összes karakter után az első Megjegyzés a Megjegyzés részének számít.


## <a name="next-steps"></a>Következő lépések

- Az oktatóanyag bízná [naplófájl-keresési](log-analytics-tutorial-viewdata.md) megtudhatja, hogyan létrehozhat olyan lekérdezéseket, a lekérdezési nyelv használatával
- Tekintse meg a [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587) kifinomult lekérdezések létrehozása és a napló keresések a fejlesztési környezet használata.


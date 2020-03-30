---
title: 'Illesztési adatok: Modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a join join data modult az Azure Machine Learningben az adatkészletek egyesítéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 38606f424e38fc68519181f485b5b698d0705d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477578"
---
# <a name="join-data"></a>Adatok összekapcsolása

Ez a cikk bemutatja, hogyan használhatja a **Join Data** modult az Azure Machine Learning designerben (előzetes verzió) két adatkészlet egyesítéséhez egy adatbázis-stílusú illesztési művelet használatával.  

## <a name="how-to-configure-join-data"></a>Illesztési adatok konfigurálása

Ha két adatkészleten szeretne illesztést végrehajtani, azokat egy kulcsoszlopnak kell összefüggenie. A több oszlopot használó összetett kulcsok is támogatottak. 

1. Adja hozzá az egyesíteni kívánt adatkészleteket, majd húzza az **Illesztési modult** a folyamatba. 

    A modul az **Adatátalakítás** kategóriában, a **Manipuláció**csoportban található.

1. Csatlakoztassa az adatkészleteket az **Illesztési adatok** modulhoz. 
 
1. Válassza **az Indítás oszlopválasztó** lehetőséget a kulcsoszlop(ok) kiválasztásához. Ne felejtsen el oszlopokat választani mind a bal, mind a jobb oldali bemenethez.

    Egyetlen kulcs esetén:

    Jelöljön ki egy kulcsoszlopot mindkét bemenethez.
    
    Összetett kulcs esetén:

    Jelölje ki az összes kulcsoszlopot a bal és a jobb oldali bemenetből ugyanabban a sorrendben. Az **Illesztési adatok** modul akkor csatlakozik a táblákhoz, amikor az összes kulcsoszlop egyezik. Jelölje be az **Ismétlődések engedélyezése és az oszlopsorrend megőrzése jelölőnégyzetet,** ha az oszlopsorrend nem egyezik meg az eredeti táblával. 

    ![oszlopválasztó](media/module/join-data-column-selector.png)


1. Jelölje be a **Kis- és nagybetűk egyeztetése** lehetőséget, ha meg szeretné őrizni a kis- és nagybetűk érzékenységét egy szövegoszlopillesztésen. 
   
1. Az **Illesztés típusa** legördülő lista segítségével megadhatja, hogyan lehessen az adatkészleteket egyesíteni.  
  
    * **Belső illesztés:** A *belső illesztés* a leggyakoribb illesztési művelet. Csak akkor adja vissza az egyesített sorokat, ha a kulcsoszlopok értékei egyeznek.  
  
    * **Bal oldali külső illesztés**: A *bal oldali laza illesztés* a bal oldali táblázat összes sorához illesztett sorokat ad vissza. Ha a bal oldali tábla egyik sorában nincsenek megfelelő sorok a jobb oldali táblázatban, a visszaadott sor a jobb oldali táblából származó összes oszlop hiányzó értékeit tartalmazza. A hiányzó értékekhez helyettesítő értéket is megadhat.  
  
    * **Teljes külső illesztés**: A *teljes laza illesztés* a bal oldali tábla (**table1**) és a jobb oldali tábla (**table2**) összes sorát adja vissza.  
  
         Az eredmény a tábla minden olyan sorában, ahol nincsenek egyező sorok, az eredmény egy hiányzó értékeket tartalmazó sort tartalmaz.  
  
    * **Bal oldali félillesztés:** A *bal oldali félillesztés* csak a bal oldali tábla értékeit adja vissza, amikor a kulcsoszlopok értékei egyeznek.  

1. A **jobb oldali kulcsoszlopok megtartása az illesztett táblázatban beállításesetén:**

    * Ezzel a beállítással mindkét bemeneti táblából megtekintheti a kulcsokat.
    * Törölje a jelet, ha csak a bal oldali bemenet kulcsoszlopait szeretné visszaadni.

1. Küldje el a folyamatot.

1. Az eredmények megtekintéséhez kattintson a jobb gombbal az **Illesztési adatokra,** és válassza a **Megjelenítés parancsot.**

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
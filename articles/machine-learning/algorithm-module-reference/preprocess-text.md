---
title: 'Előfeldolgozási szöveg: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning-szolgáltatásban az előfeldolgozási szöveges modult a szöveg tisztításához és egyszerűsítéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 4ee5e90b36f7a8fb3bfb42cad425cbb272553b3a
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210831"
---
# <a name="preprocess-text"></a>Szöveg előfeldolgozása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

A szöveg megtisztításához és leegyszerűsítéséhez használja az előfeldolgozási **szöveg** modult. Támogatja ezeket a gyakori szöveg-feldolgozási műveleteket:

* Leállítás – szavak eltávolítása
* Adott cél karakterláncok keresése és cseréje reguláris kifejezések használatával
* Morfológiai elemzéshez, amely több kapcsolódó szót konvertál egyetlen kanonikus űrlapra
* Kis-és nagybetűk normalizálása
* Bizonyos osztályok, például számok, speciális karakterek és ismétlődő karakterek (például "AAAA") eltávolítása
* E-mailek és URL-címek azonosítása és eltávolítása

Az **előfeldolgozó szöveges** modul jelenleg csak az angol nyelvet támogatja.

## <a name="configure-text-preprocessing"></a>Szöveg előfeldolgozásának konfigurálása  

1.  Adja hozzá az előfeldolgozási **szöveg** modult a kísérlethez Azure Machine learning szolgáltatásban. Ez a modul a **text Analytics**alatt található.

1. Csatlakoztasson egy olyan adatkészletet, amelynek legalább egy oszlopa tartalmaz szöveget.

1. Válassza ki a nyelvet a **nyelv** legördülő listából.

1. **Törölni kívánt szöveges oszlop**: Válassza ki az elődolgozni kívánt oszlopot.

1. **Leállítási szavak eltávolítása**: Akkor válassza ezt a lehetőséget, ha előre definiált nem indexelendő szót-listát kíván alkalmazni a Text (szöveg) oszlopra. 

    A nem indexelendő szót listája nyelvtől függő és testreszabható.

1. **Morfológiai elemzéshez**: Válassza ezt a lehetőséget, ha azt szeretné, hogy a szavak megjelenjenek a kanonikus formájában. Ez a beállítás akkor hasznos, ha csökkenteni szeretné az egyéb hasonló szöveges tokenek egyedi előfordulásának számát.

    A morfológiai elemzéshez folyamat nagymértékben nyelvtől függ.

1. **Mondatok észlelése**: Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a modul az elemzés végrehajtásakor beillesszen egy mondathoz tartozó határt.

    Ez a modul három cső karakterből `|||` álló sorozatot használ a lezáró mondat megjelenítéséhez.

1. Az opcionális keresési és lecserélési műveleteket reguláris kifejezésekkel hajthatja végre.

    * **Egyéni reguláris kifejezés**: Adja meg a keresett szöveget.
    * **Egyéni helyettesítő sztring**: Egyetlen helyettesítési értéket adjon meg.

1. **Kis-és nagybetűk normalizálása**: Válassza ezt a lehetőséget, ha ASCII nagybetűs karaktereket szeretne átalakítani a kisbetűs űrlapokra.

    Ha a karakterek nincsenek normalizálva, a kis-és nagybetűket tartalmazó szó két különböző szónak tekintendő.

1. A következő típusokat is eltávolíthatja a feldolgozott kimeneti szövegből:

    * **Számok eltávolítása**: Ezzel a beállítással távolíthatja el a megadott nyelv összes numerikus karakterét. Az azonosító számok tartományra és nyelvtől függenek. Ha a numerikus karakterek egy ismert szó szerves részét képezik, előfordulhat, hogy a számot nem távolítja el a rendszer.
    
    * **Speciális karakterek eltávolítása**: Ezzel a beállítással távolíthatja el a nem alfanumerikus speciális karaktereket.
    
    * **Ismétlődő karakterek eltávolítása**: Ezzel a beállítással távolíthatja el a további karaktereket minden olyan sorozatban, amely kétszer ismétlődik. Például az "AAAAA" karakterláncot az "AA" értékre kell csökkenteni.
    
    * **E-mail címek eltávolítása**: Ezzel a beállítással távolíthatja el a formátum `<string>@<string>`tetszőleges sorszámát.  
    * **URL-címek eltávolítása**: Ezzel a beállítással távolíthatja el az összes olyan sorozatot, amely tartalmazza az `http`alábbi `https`URL `ftp`-előtagokat:,,,`www`
    
1. **Művelet**-összehúzódások kibontása: Ez a beállítás csak olyan nyelvekre vonatkozik, amelyek művelet-összevonásokat használnak; jelenleg csak angol nyelven érhető el. 

    A beállítás kiválasztásával például lecserélheti a *"nem maradok"* kifejezést a *"ne*maradjon ott" kifejezésre.

1. **Fordított perjelek normalizálása a perjelhez**: Válassza ezt a lehetőséget a összes példányának `\\` `/`leképezéséhez.

1. **A tokenek felosztása speciális karakterek**esetén: Akkor válassza ezt a lehetőséget, ha a szavakat a (z) `&`, `-`stb. karaktereken szeretné megszüntetni. Ez a beállítás a speciális karaktereket is csökkentheti, ha kétszer ismétlődik. 

    A karakterlánc `MS---WORD` például három tokenre `MS` `-`, a, és `WORD`elemre lesz elválasztva.

1. Futtassa a kísérletet.

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
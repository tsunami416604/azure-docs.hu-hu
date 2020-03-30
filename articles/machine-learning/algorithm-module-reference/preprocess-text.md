---
title: 'Előfolyamat szövege: modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Szöveg előfeldolgozása modult az Azure Machine Learningben a szöveg tisztításához és egyszerűsítéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 6e4d4c8f798418e090caeba091dec33c71f0458f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477493"
---
# <a name="preprocess-text"></a>Szöveg előfeldolgozása

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

A **Szöveg előfeldolgozása** modullal tisztítsa meg és egyszerűsítheti a szöveget. Támogatja ezeket a gyakori szövegfeldolgozási műveleteket:

* A stop-szavak eltávolítása
* Reguláris kifejezések használata adott célkarakterláncok kereséséhez és cseréjéhez
* Lemmatization, amely több kapcsolódó szót konvertál egyetlen kanonikus formává
* Eset normalizálása
* Bizonyos karakterosztályok, például számok, speciális karakterek és ismétlődő karakterek sorozatainak eltávolítása, mint például az "aaaa"
* Az e-mailek és URL-ek azonosítása és eltávolítása

A **Folyamat előtti szöveg** modul jelenleg csak az angol nyelvet támogatja.

## <a name="configure-text-preprocessing"></a>Szöveg előfeldolgozásának konfigurálása  

1.  Adja hozzá a **folyamatelőre szöveg** modult az Azure Machine Learningben. Ezt a modult a **Text Analytics**csoportban találja.

1. Olyan adatkészlet csatlakoztatása, amely legalább egy szöveget tartalmazó oszlopot tartalmaz.

1. Válassza ki a nyelvet a **Nyelv** legördülő listából.

1. **A tisztítandó szövegoszlop**: Jelölje ki az előfeldolgozni kívánt oszlopot.

1. **Stop szavak eltávolítása**: Akkor válassza ezt a beállítást, ha előre definiált stopword listát szeretne alkalmazni a szövegoszlopra. 

    A stopword listák nyelvfüggőek és testreszabhatók.

1. **Lemmatization**: Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a szavak a gyűjtőformában jelennek meg. Ez a beállítás az egyébként hasonló szöveges tokenek egyedi előfordulásainak számának csökkentéséhez hasznos.

    A lemmatization folyamat erősen nyelvfüggő..

1. **Mondatok észlelése**: Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a modul mondathatárjelet szúrjon be az elemzés végrehajtásakor.

    Ez a modul három csőkarakterből álló sorozatot `|||` használ a mondatleterminátor ábrázolására.

1. A választható keresés és csere műveleteket reguláris kifejezésekkel hajthatja végre.

    * **Egyéni reguláris kifejezés:** Adja meg a keresett szöveget.
    * **Egyéni cserekarakterlánc:** Adjon meg egyetlen csereértéket.

1. **Kisbetűs sé normalizálja a kisbetűs tokot:** Akkor válassza ezt a beállítást, ha az ASCII kiskaraktereket kisbetűs alakokká szeretné konvertálni.

    Ha a karakterek nem normalizálódnak, ugyanaz a szó nagy- és kisbetűkkel két különböző szónak számít.

1. A feldolgozott kimeneti szövegből a következő típusú karaktereket vagy karaktersorozatokat is eltávolíthatja:

    * **Számok eltávolítása**: Ezzel a beállítással távolíthatja el a megadott nyelv összes numerikus karakterét. Az azonosító számok tartományfüggőek és nyelvfüggőek. Ha a numerikus karakterek egy ismert szó szerves részét képezik, előfordulhat, hogy a szám nem lesz eltávolítva.
    
    * **Speciális karakterek eltávolítása**: Ezzel a beállítással távolíthatja el a nem alfanumerikus speciális karaktereket.
    
    * **Ismétlődő karakterek eltávolítása**: Ezzel a beállítással további karaktereket távolíthat el minden olyan sorozatból, amely több mint kétszer ismétlődik. Például egy olyan sorozat, mint az "aaaaa" az "aa" lesz.
    
    * **E-mail címek eltávolítása**: Ezzel a `<string>@<string>`beállítással a formátum tetszőleges sorrendjét távolíthatja el.  
    * **URL-ek eltávolítása:** Ezzel a beállítással eltávolíthatja a következő `http`URL-előtagokat tartalmazó szekvenciákat: , `https`, `ftp`,`www`
    
1. **Igeösszehúzódások kibontása:** Ez a beállítás csak azige-összehúzódásokat használó nyelvekre vonatkozik; jelenleg csak angol nyelven. 

    Ha például ezt a lehetőséget választja, a *"nem maradnál ott"* kifejezést a *"nem maradnál ott"* kifejezést.

1. **Fordított perjelek normalizálása perjelre**: Ezzel a `\\` `/`beállítással az összes példányt a hoz szeretné leképezni.

1. **Tokenek felosztása speciális karaktereken:** Akkor válassza ezt a `&`lehetőséget, ha meg szeretné törni a szavakat olyan karaktereken, mint a , `-`, és így tovább. Ez a beállítás akkor is csökkentheti a speciális karaktereket, ha az ismétlődő több mint kétszer ismétlődik. 

    A `MS---WORD` karakterlánc például három tokenre, `MS`, `-`, `WORD`és a .

1. Küldje el a folyamatot.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
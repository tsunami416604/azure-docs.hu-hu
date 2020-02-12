---
title: 'Szöveg előfeldolgozása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használható a szöveg megtisztítása és leegyszerűsítése Azure Machine Learning a szöveg előfeldolgozására szolgáló modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: a8938eba0f7af995086ab1e2baba41aee7dc6330
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153808"
---
# <a name="preprocess-text"></a>Szöveg előfeldolgozása

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

A szöveg megtisztításához és leegyszerűsítéséhez használja az **előfeldolgozási szöveg** modult. Támogatja ezeket a gyakori szöveg-feldolgozási műveleteket:

* Leállítás – szavak eltávolítása
* Adott cél karakterláncok keresése és cseréje reguláris kifejezések használatával
* Morfológiai elemzéshez, amely több kapcsolódó szót konvertál egyetlen kanonikus űrlapra
* Kis-és nagybetűk normalizálása
* Bizonyos osztályok, például számok, speciális karakterek és ismétlődő karakterek (például "AAAA") eltávolítása
* E-mailek és URL-címek azonosítása és eltávolítása

Az **előfeldolgozó szöveges** modul jelenleg csak az angol nyelvet támogatja.

## <a name="configure-text-preprocessing"></a>Szöveg előfeldolgozásának konfigurálása  

1.  Adja hozzá az **előfeldolgozó szöveg** modult a folyamathoz Azure Machine learning. Ez a modul a **text Analytics**alatt található.

1. Csatlakoztasson egy olyan adatkészletet, amelynek legalább egy oszlopa tartalmaz szöveget.

1. Válassza ki a nyelvet a **nyelv** legördülő listából.

1. **Tiszta szöveg oszlop**: válassza ki az elődolgozni kívánt oszlopot.

1. **Leállítási szavak eltávolítása**: akkor válassza ezt a lehetőséget, ha előre definiált nem indexelendő szót-listát kíván alkalmazni a Text (szöveg) oszlopra. 

    A nem indexelendő szót listája nyelvtől függő és testreszabható.

1. **Morfológiai elemzéshez**: akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a szavak a kanonikus űrlapon megjelenjenek. Ez a beállítás akkor hasznos, ha csökkenteni szeretné az egyéb hasonló szöveges tokenek egyedi előfordulásának számát.

    A morfológiai elemzéshez folyamat nagymértékben nyelvtől függ.

1. **Mondatok észlelése**: akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a modul az elemzés végrehajtásakor beillesszen egy mondathoz tartozó határt.

    Ez a modul három cső karakterből álló sorozatot használ, `|||` a lezáró mondatot jelöli.

1. Az opcionális keresési és lecserélési műveleteket reguláris kifejezésekkel hajthatja végre.

    * **Egyéni reguláris kifejezés**: határozza meg a keresett szöveget.
    * **Egyéni helyettesítő sztring**: egyetlen helyettesítő érték definiálása.

1. Kis **-és nagybetűk normalizálása**: válassza ezt a beállítást, ha ASCII nagybetűs karaktereket szeretne a kisbetűs űrlapjaira konvertálni.

    Ha a karakterek nincsenek normalizálva, a kis-és nagybetűket tartalmazó szó két különböző szónak tekintendő.

1. A következő típusokat is eltávolíthatja a feldolgozott kimeneti szövegből:

    * **Számok eltávolítása**: válassza ezt a lehetőséget, ha el szeretné távolítani a megadott nyelv összes numerikus karakterét. Az azonosító számok tartományra és nyelvtől függenek. Ha a numerikus karakterek egy ismert szó szerves részét képezik, előfordulhat, hogy a számot nem távolítja el a rendszer.
    
    * **Speciális karakterek eltávolítása**: ezzel a beállítással távolíthatja el a nem alfanumerikus speciális karaktereket.
    
    * **Duplikált karakterek eltávolítása**: Ha ezt a beállítást választja, akkor a több mint kétszeresére ismétlődő sorokban távolítsa el a felesleges karaktereket. Például az "AAAAA" karakterláncot az "AA" értékre kell csökkenteni.
    
    * **E-mail-címek eltávolítása**: válassza ezt a lehetőséget, ha el szeretné távolítani a formátum `<string>@<string>`ét.  
    * **URL-címek eltávolítása**: válassza ezt a lehetőséget, ha el szeretné távolítani a következő URL-előtagokat tartalmazó sorozatot: `http`, `https`, `ftp`, `www`
    
1. Művelet-összevonások **kibontása**: Ez a beállítás csak olyan nyelvekre vonatkozik, amelyek művelet-összevonásokat használnak; jelenleg csak angol nyelven érhető el. 

    A beállítás kiválasztásával például lecserélheti a *"nem maradok"* kifejezést a *"ne*maradjon ott" kifejezésre.

1. **Fordított perjelek normalizálása a perjelhez**: válassza ezt a lehetőséget, ha a `\\` összes példányát le szeretné képezni `/`.

1. **Tokenek felosztása speciális karaktereknél**: akkor válassza ezt a lehetőséget, ha a szavakat olyan karaktereken szeretné megszüntetni, mint például a `&`, `-`és így tovább. Ez a beállítás a speciális karaktereket is csökkentheti, ha kétszer ismétlődik. 

    A `MS---WORD` sztring például három tokenre, `MS`ra, `-`ra és `WORD`re lesz elválasztva.

1. A folyamat futtatása.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
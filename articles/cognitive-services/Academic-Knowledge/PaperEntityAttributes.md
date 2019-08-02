---
title: Papír entitás attribútumai – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogy mely attribútumok használhatók a Academic Knowledge API papír entitásával.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4b9431469a7925d26003ad9c34f6b401e5767f6d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704938"
---
# <a name="paper-entity"></a>Papír entitás

<sub>* Az alábbi attribútumok a papír entitásra jellemzőek. (Ty = "0")</sub>


Name (Név)    |Leírás                                        |Type       | Műveletek
------- | ------------------------------------------------- | --------- | ----------------------------
Id      |Entitásazonosító                                          |Int64      |Egyenlő
Ti      |Papír címe                                        |Karakterlánc     |Egyenlő<br/>StartsWith
K       |Papír nyelvi kódja a következővel\@elválasztva: "\@\@"          |Karakterlánc     |Egyenlő
I       |Papír éve                                         |Int32      |Egyenlő<br/>IsBetween
D       |Könyv dátuma                                         |Date       |Egyenlő<br/>IsBetween
Másolat      |Idézetek száma                                     |Int32      |nincs  
ECC     |Becsült idézetek száma                           |Int32      |nincs
AA. AuN  |Szerző neve                                        |Karakterlánc     |Egyenlő<br/>StartsWith
AA.AuId |Szerző azonosítója                                          |Int64      |Egyenlő
AA.AfN  |Szerzői kapcsolat neve                            |Sztring     |Egyenlő<br/>StartsWith
AA.AfId |Szerzői kapcsolat azonosítója                              |Int64      |Egyenlő
AA.S    |Szerzői sorrend a papírhoz                         |Int32      |Egyenlő
F. FN    |Tanulmány neve                                |Sztring     |Egyenlő<br/>StartsWith
F.FId   |Tanulmányi azonosító mező                                  |Int64      |Egyenlő
J. JN    |Napló neve                                       |Sztring     |Egyenlő<br/>StartsWith
J. JId   |Napló azonosítója                                         |Int64      |Egyenlő
C.CN    |Konferencia adatsorozatának neve                             |Sztring     |Egyenlő<br/>StartsWith
C.CId   |Konferencia-sorozat azonosítója                               |Int64      |Egyenlő
RId     |Hivatkozott dokumentumok azonosítója                              |Int64 []    |Egyenlő
W       |Szavak a papír címéről és absztrakt                |Karakterlánc []   |Egyenlő
E       |Kiterjesztett metaadatok (lásd az alábbi táblázatot)                |Sztring     |nincs  
        


## <a name="extended-metadata-attributes"></a>Bővített metaadatok attribútumai ##

Name (Név)    | Leírás               
--------|---------------------------    
DN      | A dokumentum megjelenítendő neve 
S       | Források – a papír webes forrásainak listája, a statikus rangsor szerint rendezve
S. Ty    | Source Type (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S. U     | Forrás URL-címe
VFN     | Helyszín teljes neve – a napló vagy a konferencia teljes neve
VSN     | Helyszín rövid neve – a napló vagy a konferencia rövid neve
V       | Kötet – napló kötete
BV      | Napló neve
BT      | 
PB      | Naplók rövidítései
I       | Probléma – a naplóval kapcsolatos probléma
FP      | FirstPage – a papír első lapja
. LP      | LastPage – a papír utolsó lapja
DOI     | Digitális objektum azonosítója
Másolat      | Idézetek kontextusai – a hivatkozott papír AZONOSÍTÓjának és a dokumentum megfelelő környezetének listája (például [{123: ["Brown rókák ismertek a 123-es papíron hivatkozott jumping-szövegekben", "a lusta kutyák a korábbi, a 123-es papíron látható"]})
IA      | Fordított absztrakt
IA. IndexLength| Az indexben lévő elemek száma (absztrakt szavak száma)
IA.InvertedIndex| Az absztrakt szavak és a hozzájuk tartozó pozíciók listája az eredeti absztraktban (például [{"": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}])

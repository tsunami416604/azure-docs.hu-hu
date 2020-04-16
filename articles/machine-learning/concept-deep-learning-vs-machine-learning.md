---
title: Mély tanulás vs. gépi tanulás
titleSuffix: Azure
description: Ismerje meg, hogyan kapcsolódik a mély tanulás a gépi tanuláshoz és a mesterséges intelligenciához. A mélytanulást olyan forgatókönyvekben használják, mint a csalások észlelése, a hangfelismerés & az arcfelismerés, a hangulatelemzés és az idősorozat-előrejelzés.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 03/05/2020
ms.openlocfilehash: b024010583ba1c6e0ffdf663f7335011ce212bf1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414584"
---
# <a name="deep-learning-vs-machine-learning"></a>Mély tanulás vs. gépi tanulás

Ez a cikk segít összehasonlítani a deep learning és a gépi tanulás. Megtudhatja, hogyan viszonyul a két fogalom, és hogyan illeszkednek a mesterséges intelligencia szélesebb kategóriájába. A cikk azt is ismerteti, hogyan lehet a mély tanulást valós helyzetekben, például a csalások észlelésére, a hang- és arcfelismerésre, a hangulatelemzésre és az idősorozat-előrejelzésre alkalmazni.

## <a name="deep-learning-machine-learning-and-ai"></a>Mély tanulás, gépi tanulás és a mi

![Kapcsolatdiagram: AI vs. gépi tanulás vs. mély tanulás](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

A deep learning és a gépi tanulás és a mi ai megértéséhez vegye figyelembe a következő definíciókat:

- **A mély tanulás** a gépi tanulás egy olyan részhalmaza, amely mesterséges neurális hálózatokon alapul. A _tanulási folyamat_ _mély,_ mert a mesterséges neurális hálózatok szerkezete több bemeneti, kimeneti és rejtett rétegek. Minden réteg olyan egységeket tartalmaz, amelyek a bemeneti adatokat olyan információkká alakítják, amelyeket a következő réteg egy bizonyos prediktív feladathoz használhat. Ennek a struktúrának köszönhetően a gép saját adatfeldolgozással is tanulhat.

- **A gépi tanulás** a mesterséges intelligencia olyan részét, amely technikákat (például mélytanulást) használ, amelyek lehetővé teszik a gépek számára a feladatok élményének kihasználását. A _tanulási folyamat_ a következő lépéseken alapul:

   1. Adatok becsatornázása egy algoritmusba. (Ebben a lépésben további információkat adhat meg a modellnek, például a szolgáltatások kinyerésének végrehajtásával.)
   1. Ezeket az adatokat a modell betanításához.
   1. Tesztelje és telepítse a modellt.
   1. Használja fel az üzembe helyezett modellt egy automatikus prediktív feladat elvégzéséhez. (Más szóval hívja meg és használja az üzembe helyezett modellt a modell által visszaadott előrejelzések fogadásához.)

- **A mesterséges intelligencia (AI)** egy olyan technika, amely lehetővé teszi a számítógépek számára, hogy utánozzák az emberi intelligenciát. Ez magában foglalja a gépi tanulás. 
 
Fontos megérteni a mi, a gépi tanulás és a mély tanulás közötti kapcsolatot. A gépi tanulás a mesterséges intelligencia elérésének egyik módja. Gépi tanulási és mélytanulási technikák használatával olyan számítógépes rendszereket és alkalmazásokat hozhat létre, amelyek gyakran az emberi intelligenciához kapcsolódó feladatokat végeznek. Ezek közé tartozik a képfelismerés, a beszédfelismerés és a nyelvi fordítás.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>A mélytanulás és a gépi tanulás technikái 

Most, hogy már rendelkezik a gépi tanulás és a mély tanulás áttekintésével, hasonlítsa össze a két technikát. A gépi tanulásban az algoritmust meg kell mondani, hogyan lehet pontos előrejelzést készíteni több információ (például a funkciók kinyerése) elvégzésével. A mély tanulás, az algoritmus megtanulhatja, hogyan lehet pontos előrejelzést a saját adatfeldolgozás, köszönhetően a mesterséges neurális hálózati struktúra.

Az alábbi táblázat részletesebben összehasonlítja a két technikát:

| |Minden gépi tanulás |Csak a mély tanulás|
|---|---|---|
|  **Adatpontok száma** | Kis mennyiségű adat használatával előrejelzéseket készíthet. | Nagy mennyiségű betanítási adatokat kell használnia az előrejelzések hez. |
|  **Hardverfüggőségek** | Alsó kategóriás gépeken is működik. Nem igényel nagy mennyiségű számítási teljesítményt. | A csúcskategóriás gépektől függ. Eleve nagyszámú mátrixszorzási műveletet végez. A GPU hatékonyan optimalizálhatja ezeket a műveleteket. |
|  **Featurization folyamat** | A felhasználók által immár pontosan azonosíthatók és létrehozandó funkciók szükségesek. | Magas szintű funkciókat tanul az adatokból, és önmagában új funkciókat hoz létre. |
|  **Tanulási megközelítés** | A tanulási folyamatot kisebb lépésekre osztja. Ezután egyesíti az egyes lépből származó eredményeket egy kimenetben. | Végighalad a tanulási folyamaton a probléma végponttól végpontig történő megoldásával. |
|  **Végrehajtási idő** | Viszonylag kevés időt vesz igénybe a vonat, kezdve néhány másodperc, hogy néhány óra. | Általában hosszú időt vesz igénybe, hogy a vonat, mert a mély tanulási algoritmus magában foglalja a sok réteget. |
|  **Kimenet** | A kimenet általában egy numerikus érték, például egy pontszám vagy egy besorolás. | A kimenet több formátumot is tartalmazhat, például szöveget, pontszámot vagy hangot. |

## <a name="deep-learning-use-cases"></a>Mélytanulási használati esetek

A mesterséges neurális hálózat szerkezete miatt a mélytanulás kiválóan azonosítja a szerkezetfel építés nélküli adatok mintáit, például a képeket, a hangot, a videót és a szöveget. Ezért a mély tanulás gyorsan átalakítja számos iparágat, beleértve az egészségügyet, az energiát, a pénzügyeket és a közlekedést. Ezek az iparágak most újragondolják a hagyományos üzleti folyamatokat. 

A mélytanulás leggyakoribb alkalmazásait a következő bekezdések ismertetik.

### <a name="named-entity-recognition"></a>Elnevezett entitás felismerése

Az elnevezett entitás felismerése egy deep learning módszer, amely egy szöveget bevitelként vesz fel, és egy előre megadott osztálysá alakítja. Ez az új információ lehet irányítószám, dátum, termékazonosító. Az információ ezután egy strukturált sémában tárolható a címek listájának létrehozásához, vagy egy identitás-érvényesítési motor viszonyítási alapként szolgálhat.

### <a name="object-detection"></a>Objektumészlelés

A deep learning számos objektumészlelési használati esetben alkalmazva lett alkalmazva. Az objektumészlelés két részből áll: a képbesorolásból, majd a kép lokalizációjból. A _képbesorolás_ azonosítja a kép objektumait, például az autókat vagy a személyeket. A kép _lokalizálása_ adja meg ezeknek az objektumoknak a helyét. 

Az objektumészlelést már használják olyan iparágakban, mint a szerencsejátékok, a kiskereskedelem, a turizmus és az önvezető autók.

### <a name="image-caption-generation"></a>Képfelirat létrehozása

A képfelismeréshez hasonlóan a képfeliratozásban a rendszernek is létre kell hoznia egy feliratot, amely leírja a kép tartalmát. Ha képes észlelni és címkézni az objektumokat a fényképeken, a következő lépés az, hogy ezeket a címkéket leíró mondatokká alakítsa. 

A képfeliratos alkalmazások általában konvolúciós neurális hálózatokat használnak a lemezkép objektumainak azonosítására, majd egy ismétlődő neurális hálózat használatával a címkéket konzisztens mondatokká alakítják.

### <a name="machine-translation"></a>Gépi fordítás

A gépi fordítás az egyik nyelvről szavakat vagy mondatokat vesz igénybe, és automatikusan lefordítja azokat egy másik nyelvre. A gépi fordítás már régóta jelen van, de a mélytanulás két konkrét területen ér el lenyűgöző eredményeket: a szöveg automatikus fordítása (és a beszéd szöveggé fordítása) és a képek automatikus fordítása.

A megfelelő adatátalakítással a neurális hálózat képes megérteni a szöveg-, hang- és vizuális jeleket. A gépi fordítás sal azonosíthatja a hangrészleteket a nagyobb hangfájlokban, és szövegként átírhatja a kimondott szót vagy képet.

### <a name="text-analytics"></a>Szövegelemzés

A mélytanulási módszereken alapuló szövegelemzés magában foglalja a nagy mennyiségű szöveges adat (például orvosi dokumentumok vagy költségbevételezések) elemzését, a minták felismerését, valamint a szervezett és tömör információk létrehozását.

A vállalatok a mélytanulás segítségével szövegelemzést végeznek a bennfentes kereskedelem és a kormányzati előírásoknak való megfelelés felderítésére. Egy másik gyakori példa a biztosítási csalás: a szöveges analitikát gyakran használják nagy mennyiségű dokumentum elemzésére, hogy felismerjék a biztosítási igény csalásának esélyét. 

## <a name="artificial-neural-networks"></a>Mesterséges neurális hálózatok

A mesterséges neurális hálózatokat összekapcsolt csomópontok rétegei alkotják. Deep learning modellek neurális hálózatok, amelyek nagy számú réteget használ. 

A következő szakaszok a legnépszerűbb mesterséges neurális hálózati tipológiákat tárják fel.

### <a name="feedforward-neural-network"></a>Feedforward neurális hálózat

A feedforward neurális hálózat a legalapvetőbb típusú mesterséges neurális hálózat. Feedforward-hálózatban az információ csak egy irányban mozog a bemeneti rétegről a kimeneti rétegre. Feedforward neurális hálózatok átalakítják a bemeneti azáltal, hogy egy sor rejtett rétegek. Minden réteg áll egy sor neuronok, és minden réteg teljes mértékben csatlakozik az összes neuronok a réteg előtt. Az utolsó teljesen csatlakoztatott réteg (a kimeneti réteg) a létrehozott előrejelzéseket jelöli.

### <a name="recurrent-neural-network"></a>Visszatérő neurális hálózat

Visszatérő neurális hálózatok egy széles körben használt mesterséges neurális hálózat. Ezek a hálózatok menti a kimenetet egy réteg, és táplálja vissza a bemeneti réteget, hogy segítsen megjósolni a réteg eredményét. A visszatérő neurális hálózatok nagyszerű tanulási képességekkel rendelkeznek. Széles körben használják olyan összetett feladatokhoz, mint az idősorozatok előrejelzése, a kézírás tanulása és a nyelv felismerése.

### <a name="convolutional-neural-networks"></a>Konvolúciós neurális hálózatok

A konvolúciós neurális hálózat egy különösen hatékony mesterséges neurális hálózat, és egy egyedülálló architektúrát mutat be. A rétegek három dimenzióba vannak rendezve: szélesség, magasság és mélység. A neuronok egy rétegben nem kapcsolódnak az összes neuronok a következő rétegben, de csak egy kis régióban a réteg neuronok. A végső kimenet a valószínűségi pontszámok egyetlen vektorára csökken, a mélységdimenzió mentén rendezve. 

Konvolúciós neurális hálózatok at használták olyan területeken, mint a videó felismerés, képfelismerő és ajánló rendszerek.

## <a name="next-steps"></a>További lépések

Az alábbi cikkek bemutatják, hogyan használhatja a deep learning technológiát az [Azure Machine Learningben:](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=docs-article-lazzeri)

- [Kézzel írt számjegyek osztályozása TensorFlow modell használatával](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)

- [Kézzel írt számjegyek osztályozása TensorFlow-becslés és Keras használatával](https://docs.microsoft.com/azure/machine-learning/how-to-train-keras?WT.mc_id=docs-article-lazzeri)

- [Képek osztályozása Pytorch modell használatával](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)

- [Kézzel írt számjegyek osztályozása Chainer modell használatával](https://docs.microsoft.com/azure/machine-learning/how-to-train-ml-models)

Is, használja a [Machine Learning algoritmus Cheat Sheet](algorithm-cheat-sheet.md) kiválasztásához algoritmusok a modellhez.

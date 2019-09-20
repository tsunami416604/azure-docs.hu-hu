---
title: Mélyreható tanulás és gépi tanulás
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Deep learning és a Machine learning szolgáltatással, valamint a mesterséges intelligenciával kapcsolatos fogalmakkal. A mélyreható tanulás olyan helyzetekben alkalmazható, mint például a csalások észlelése, a hang-és Arcfelismerés, a hangulat-elemzés és az idősorozat-előrejelzés.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 791566dc65b41bf3124e84544390d7d843e51273
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123139"
---
# <a name="deep-learning-vs-machine-learning"></a>Mélyreható tanulás és gépi tanulás

Ez a cikk segítséget nyújt a Deep learning és a Machine learning összehasonlításában. Megtudhatja, hogyan hasonlítja össze a két fogalmat, és hogyan illeszkednek a mesterséges intelligencia tágabb kategóriába. A cikk azt is leírja, hogyan alkalmazhatók a mély tanulás olyan valós forgatókönyvekre, mint például a csalások észlelése, a hang-és Arcfelismerés, a hangulat-elemzés és az idősorozat-előrejelzés.

## <a name="deep-learning-machine-learning-and-ai"></a>Mély tanulás, gépi tanulás és AI

![Kapcsolati diagram: AI vs. Machine learning és Deep learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Vegye figyelembe az alábbi definíciókat a Deep learning és a Machine learning megismerése érdekében. AI

- A **Deep learning** a gépi tanulás egy olyan részhalmaza, amely mesterséges neurális hálózatokon alapul. A _tanulási folyamat_ _mély_ , mert a mesterséges neurális hálózatok szerkezete több bemeneti, kimeneti és rejtett rétegből áll. Minden réteg olyan egységeket tartalmaz, amelyek átalakítja a bemeneti adatokat olyan adatokba, amelyeket a következő réteg használhat egy adott prediktív feladathoz. Ennek a struktúrának köszönhetően a gépek saját adatfeldolgozással is megismerhetik.

- A **gépi tanulás** a mesterséges intelligencia olyan részhalmaza, amely technikákat használ (például a mély tanulást), amely lehetővé teszi a gépek számára, hogy a feladatok fejlesztéséhez használják a felhasználói élményt. A _tanulási folyamat_ a következő lépéseken alapul:

   1. Adatcsatorna átalakítása egy algoritmusba. (Ebben a lépésben további információkat adhat meg a modellhez, például a szolgáltatások kinyerésének végrehajtásával.)
   1. Ezekkel az értékekkel betaníthatja a modelleket.
   1. Tesztelje és telepítse a modellt.
   1. Az üzembe helyezett modell használata automatizált prediktív feladathoz. (Más szóval hívja meg és használja az üzembe helyezett modellt a modell által visszaadott előrejelzések fogadásához.)

- A **mesterséges intelligencia (AI)** olyan technika, amely lehetővé teszi a számítógépek számára az emberi intelligenciát. A gépi tanulást is magában foglalja. 
 
Fontos megérteni az AI, a gépi tanulás és a mély tanulás közötti kapcsolatot. A gépi tanulás a mesterséges intelligencia elérésének módja. A gépi tanulás és a mélyreható tanulási technikák használatával olyan számítógéprendszer és alkalmazások hozhatók létre, amelyek az emberi intelligenciához gyakran társított feladatokat látnak el. Ezek a feladatok a képek felismerését, a beszédfelismerést és a nyelvi fordítást tartalmazzák.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>A Deep learning és a Machine learning módszerei 

Most, hogy áttekintette a Machine learning és a Deep learning áttekintését, vesse össze a két technikát. A gépi tanulásban az algoritmusnak azt kell megadnia, hogyan lehet pontos előrejelzést készíteni a további információk felhasználásával (például a szolgáltatások kinyerésének elvégzésével). A Deep learningben az algoritmus megtudhatja, hogyan készíthet pontos előrejelzést a saját adatfeldolgozásával, a mesterséges neurális hálózati struktúra köszönhetően.

A következő táblázat részletesebben összehasonlítja a két technikát:

| |Minden gépi tanulás |Csak mély tanulás|
|---|---|---|
|  **Adatpontok száma** | Az előrejelzéseket kis mennyiségű adat használatával végezheti el. | Nagy mennyiségű betanítási adattal kell használnia a jóslatok létrehozásához. |
|  **Hardver függőségei** | Az alacsony végű gépeken is működhet. Nincs szükség nagy mennyiségű számítási teljesítményre. | A magas végú gépektől függ. Természeténél fogva nagy számú mátrix-szorzási műveletet végez. A GPU képes hatékonyan optimalizálni ezeket a műveleteket. |
|  **Featurization folyamat** | A funkciók pontos azonosítását és létrehozását igénylik a felhasználók számára. | Megismerheti az adatok magas szintű funkcióit, és saját maga is létrehoz új szolgáltatásokat. |
|  **Tanulási megközelítés** | A tanulási folyamat kisebb lépésekre osztása. Ezután kombinálja az egyes lépések eredményeit egyetlen kimenetre. | Áthalad a tanulási folyamaton a teljes körű probléma megoldásával. |
|  **Végrehajtási idő** | Viszonylag kevés időt vesz igénybe a betanítás, néhány másodperctől néhány órára. | A tanítás általában hosszú időt vesz igénybe, mivel a Deep learning algoritmus számos réteget magában foglal. |
|  **Kimenet** | A kimenet általában numerikus érték, például pontszám vagy besorolás. | A kimenetnek több formátuma is lehet, például egy szöveg, egy pontszám vagy egy hang. |

## <a name="deep-learning-use-cases"></a>Mély tanulási használati esetek

A mesterséges neurális hálózati struktúra miatt a Deep learning kiemelkedik a strukturálatlan adat, például képek, hang, videó és szöveg mintázatának azonosítására. Emiatt a Deep learning gyorsan átalakítja számos iparágat, például az egészségügyet, az energiát, a pénzügyt és a szállítást. Ezek az iparágak mostantól a hagyományos üzleti folyamatokat gondolják át. 

A Deep learning leggyakoribb alkalmazásai a következő bekezdésekben olvashatók.

### <a name="named-entity-recognition"></a>Nevesített entitások felismerése

A nevesített entitások felismerése olyan mély tanulási módszer, amely a szöveg bemenetként való bevitelét és egy előre megadott osztályba alakítását végzi. Ez az új információ lehet postai irányítószám, dátum, termékazonosító. Az adatok egy strukturált sémában tárolhatók, és a címek listájának összeállíthatók, vagy egy Identity Validation Engine viszonyítási céljaként szolgálnak.

### <a name="object-detection"></a>Objektumészlelés

A Deep learning számos objektum-észlelési használati esettel lett alkalmazva. Az objektumok észlelése két részből áll: a képek besorolása, majd a képek honosítása. A rendszerkép _besorolása_ azonosítja a rendszerkép objektumait, például az autókat vagy a személyeket. A rendszerkép _honosítása_ megadja az objektumok adott helyét. 

Az objektumok észlelése már használatban van olyan iparágakban, mint például a játékok, a kiskereskedelem, a turizmus és az önkiszolgáló autók.

### <a name="image-caption-generation"></a>Képfelirat létrehozása

A rendszerkép-feliratozásban, az adott képhez hasonlóan a képfeliratok esetében a rendszernek egy olyan feliratot kell létrehoznia, amely leírja a rendszerkép tartalmát. Ha a fényképekben objektumokat talál és címkéz, a következő lépés a címkék leíró mondatokra való bekapcsolása. 

A képfeliratos alkalmazások általában a párhuzamos neurális hálózatokat használják a rendszerképben lévő objektumok azonosítására, majd egy visszatérő neurális hálózatot használnak a címkék konzisztens mondatokra való bekapcsolásához.

### <a name="machine-translation"></a>Gépi fordítás

A gépi fordítás egy adott nyelv szavait vagy mondatait veszi figyelembe, és automatikusan más nyelvre fordítja őket. A gépi fordítás már régóta, de a Deep learning két konkrét területen látványos eredményeket érhet el: a szöveg automatikus fordítását (és a beszéd szövegének fordítását), valamint a képek automatikus fordítását.

A megfelelő adatátalakítással a neurális hálózat képes értelmezni a szöveg-, hang-és vizuális jeleket. A gépi fordítás használatával azonosíthatók a hangtöredékek a nagyobb hangfájlokban, és szövegként is áthelyezhetők a kimondott szövegből vagy képekből.

### <a name="text-analytics"></a>Szövegelemzés

A mélyreható tanulási módszereken alapuló szöveges elemzések nagy mennyiségű szöveges adat (például orvosi dokumentumok vagy költségek visszaigazolása) elemzését, a minták felismerését, valamint a szervezett és tömör információk létrehozását ismertetik.

A vállalatok mély tanulással végeznek szöveges elemzést a bennfentes kereskedelem és a kormányzati szabályozásoknak való megfelelés észlelése érdekében. Egy másik gyakori példa a biztosítási csalás: a szöveges elemzés gyakran a nagy mennyiségű dokumentum elemzésére szolgál, hogy felismerje a biztosítási követelések csalásának esélyét. 

## <a name="artificial-neural-networks"></a>Mesterséges neurális hálózatok

A mesterséges neurális hálózatokat összekapcsolt csomópontok rétegei alkotják. A Deep learning-modellek nagy mennyiségű réteget használó neurális hálózatokat használnak. 

A következő fejezetek a legnépszerűbb mesterséges neurális hálózati tipológiák ismertetik.

### <a name="feedforward-neural-network"></a>Feedforward neurális hálózat

A feedforward neurális hálózat a mesterséges neurális hálózat legalapvetőbb típusa. Egy feedforward-hálózatban az adatok csak egy irányba mozdulnak el a bemeneti rétegből a kimeneti rétegbe. A Feedforward neurális hálózatokat a rendszer a rejtett rétegek sorozatán keresztül alakítja át. Minden réteg idegsejtekből tevődik össze, és minden réteg teljesen csatlakoztatva van a rétegben lévő összes neuronokhoz. Az utolsó teljesen csatlakoztatott réteg (a kimeneti réteg) a generált előrejelzéseket jelöli.

### <a name="recurrent-neural-network"></a>Visszatérő neurális hálózat

Az ismétlődő neurális hálózatok széles körben használják a mesterséges neurális hálózatot. Ezek a hálózatok elmentik egy réteg kimenetét, és visszaküldik a bemeneti rétegnek a réteg eredményének előrejelzéséhez. Az ismétlődő neurális hálózatok nagy tanulási képességekkel rendelkeznek. Ezek széles körben használatosak olyan összetett feladatokhoz, mint például az idősorozat-előrejelzés, a tanulás és a nyelv felismerése.

### <a name="convolutional-neural-networks"></a>Összetartozó neurális hálózatok

A többrendszeres neurális hálózat egy különösen hatékony mesterséges neurális hálózat, amely egyedi architektúrát mutat be. A rétegek három dimenzióban vannak rendezve: szélesség, magasság és mélység. Az egyik rétegben lévő neuronok nem a következő rétegben lévő összes neuronokhoz csatlakoznak, hanem csak a réteg neuronok kis régiójához. A végső kimenetet a rendszer a mélységi dimenzió mentén szervezett valószínűségi pontszámok egyetlen vektorára csökkenti. 

A többszintű neurális hálózatok olyan területeken lettek felhasználva, mint a videó-felismerés, a képfelismerés és az ajánlott rendszerek.

## <a name="next-steps"></a>További lépések

A következő cikkek bemutatják, hogyan használhatja a Deep learning-technológiát a [Azure Machine Learningban](https://docs.microsoft.com/azure/machine-learning/service/?WT.mc_id=docs-article-lazzeri):

- [Kézzel írt számjegyek osztályozása TensorFlow-modell használatával](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)
- [Kézzel írt számjegyek osztályozása TensorFlow-kalkulátor és kerasz használatával](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-keras?WT.mc_id=docs-article-lazzeri)
- [Rendszerképek osztályozása Pytorch-modell használatával](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)
- [Kézzel írt számjegyek besorolása egy Láncer-modell használatával](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)

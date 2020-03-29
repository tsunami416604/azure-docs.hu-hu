---
title: Jogcímképességek használata az Azure DevTest Labsben | Microsoft dokumentumok
description: Ismerje meg az Azure DevTest Labs jogcím-/kizárati képességeinek különböző forgatókönyveit
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67861429"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Jogcímképességek használata az Azure DevTest Labsben
Az Azure DevTest Labs szolgáltatás javítja a fejlesztők és a tesztelők hatékonyságát és eredményességét. Ez a cikk az Azure DevTest Labs virtuális gépei nek igénylésére vagy kivonására összpontosít. Azt is felsorolja, hogy ez a funkció javítja a felhasználói élményt. Mielőtt megnéznék a különböző forgatókönyveket, ahol ez a funkció használható, nézzük meg, mi az **állítás,** és hogyan működik.

## <a name="claimable-machines"></a>Követelhető gépek
A kiigényelhető gép egy virtuális gép (VM), amely egy tesztkörnyezetben, tulajdonos nélkül jön létre. Miután a gép azt állította, a felhasználó rendelkezik az adott virtuális gép teljes körű lehetőségeivel. Amikor a felhasználó igényt tart egy gépre, a rendszer néhány módosítást hajt végre. A virtuális gép átkerül a **követelésre képes virtuális gépek** listájáról az Azure Portalon a **Saját virtuális gépek** listájára. 

A felhasználó csatlakozhat a virtuális géphez, testreszabhatja az összetevőket, újraindíthatja, leállíthatja vagy kiállíthatja a gépet. A virtuális gép többféleképpen is igényelhető:

- Hozzon létre egy gépet, és állítsa vissza úgy, hogy a követelhető készletbe kerüljön. 
- Hozzon létre egy virtuális gép, és helyezze a megosztott készlet [speciális beállításokat.](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/)

Két esetben, amikor a jogcím/un-jogcím képességek hatékonyan használható. Az első esetben több előrelátást és tervezést igényel, amelyet megfelelően kell megtervezni és végrehajtani. És a második sokkal szituációsbb. Az alábbiakban néhány példa a különböző esetek.

## <a name="designed-use-of-claimable-machines"></a>Követelhető gépek tervezett használata

- **Szoftverfejlesztés / tesztelés:** Lehetővé teszi a fejlesztők vagy a tesztelők számára, hogy hatékonyabbak legyenek azáltal, hogy a konfigurált gépek készen állnak és nem igényelt állapotban vannak. A különböző konfigurációkkal, szükséges eszközökkel és a legújabb kóddal rendelkező virtuális gépek készletével a felhasználók virtuális gépet igényelhetnek, és anélkül kezdhetik meg a munkát, hogy időt kellene fordítaniuk egy gép beállítására. A virtuális gépek igénylése előtt a gépek ki vannak építve, de a leállítás ily gyakran használt gépek költségeinek minimalizálása. Ha a virtuális gépekre van szükség, a felhasználó egyszerűen azt állítja, a virtuális gép, amely elindítja a gépet. A kiigénylési lehetőség ebben az esetben nem olyan hasznos, mivel egy új virtuális gép létrehozása gyakran könnyebb és olcsóbb.
- **Tanterem/Labor:** A virtuális gépek et előre konfiguráltegy osztályhoz vagy egy tesztkörnyezethez, hogy a diákok azonnal csatlakozhathassanak egy géphez az Azure Portalhasználatával.  Ha egy diák igényel egy virtuális gépet, a labor biztosítja, hogy senki sem igényelheti ugyanazt a gépet. A folyamat automatizálása biztosítja, hogy a megadott környezettel rendelkező gépek száma elérhető legyen. Ha a diákok nem jelennek meg, vagy késik, a nem igényelt gépek is elérhető, amíg a munkamenet vége minimális költséggel. A kikövetelési lehetőség nem olyan hatékony ebben a forgatókönyvben, mivel a virtuális gép ismeretlen állapotban van, amikor az előző felhasználó végzett.
- **Bemutatók:** Használjon gépeket bemutatókhoz, ahol a laborban lévő gépek meghatározott környezetben vannak beállítva. Ez a képesség akkor hasznos, ha egyszerre vagy véletlenszerűen, például egy konferencián több ember is bemutatót tarthat. A kiigénylési lehetőség hasznos lehet ebben a helyzetben, mivel a demó nem módosíthatja a gép állapotát, lehetővé téve a felhasználók számára, hogy a virtuális gépet visszaadják a követelhető készletnek a következő bemutatóhoz. A nem igényelt gép kiépítésés és a felmerülő minimális költség, virtuális gépek hosszabb ideig maradhat a laborban.
- **Ideiglenes/szerződéses munkavállalók:** A felhasználók használhatják a gépet. Amikor elhagyják, adatvesztés nélkül visszaküldik a virtuális gép a követelhető készletbe. A virtuális gép nem igényelt, egy másik felhasználó igényelheti a virtuális gépet, és folytassa, vagy tekintse át a számítógépet további információkért.
- **Általánosságban:** Az a képesség, hogy egy egyetlen forrás automatikusan konfigurálja és telepítse a virtuális gépek, egy adott ütemben, számos különböző helyzetekben hasznos. Számos különböző helyzetekben, amikor a jogcím/jogelenek követelés funkció segít a felhasználók hatékonyabbá azáltal, hogy egy automatizált folyamat a virtuális gépek egy nem igényelt állapotban egy meghatározott konfigurációval. A konfiguráció(k) az igényeitől függően különböző operációs rendszereket, nyelveket, lemezeket vagy [egyéb szoftvereket (összetevőket)](devtest-lab-artifact-author.md) tartalmazhatnak. A virtuális gép igénylése a laborból lehetővé teszi, hogy a labor felhasználó egy megfelelően konfigurált rendszer nélkül a gép konfigurálása időt vagy energiát. A laborkezelő használhatja a virtuális gépek igényelt állapotát a létrehozott gépek számának növeléséhez, a gépek karbantartásához és a konfigurációk prioritásának meghatározásához. A [lemezkép-gyár](image-factory-create.md) egy jó példa egy automatizált folyamat virtuális gépek és képek több labs. A parancsfájlok módosíthatók az alábbi helyzetek bármelyikének a megfelelő módosításokkal való végrehajtása érdekében, vagy hivatkozásként használhatók egyéni rendszer létrehozásához.

## <a name="situational-use-of-claimable-machines"></a>Követelhető gépek helyzeti használata

- Használja a jogcím/un-jogképesség, amely lehetővé teszi a felhasználók számára, hogy adja át a gépek vezérlését az egyik a másikra, és nem kell tudni, hogy kifejezetten ki lesz felvette a gépet a következő.
- Fejlesztési, tesztelési és hibakeresés egy forgatókönyv, ahol egy adott gép konfigurációja reprodukálni egy hibát, akkor a gép nem igényelt, amely lehetővé teszi egy másik fejlesztő igényt tarthat a gép, és folytassa a munkát. Ez a funkció különösen hasznos, mivel egyre több ember dolgozik távolról a világ különböző területein. 
- A csapattagok egyetlen környezetben dolgozhatnak. Manuálisan is beállíthat egy összetett környezetet, amely nem automatizálható, vagy olyan erőforrásokat hozhat létre, amelyek csak egyetlen bemenet, például képek módosításait tudják kezelni. A múltban ezt a problémát úgy oldották meg, hogy egy dedikált gépet működtettek. A követelhető funkció a manuális folyamat hoz a beépített felhasználói hozzáférés-vezérlés sel és a vizuális azonosítással, ha rendelkezésre áll, javítja a manuális folyamatot. Ha nem igényel, a virtuális gép a költségek csökkentése érdekében a kiépített.
- A virtuális géphez csatlakoztatott adatlemez. Minden lemez ~ 1 TB-nyi adat lehetővé teszi, hogy nagy mennyiségű adatot kell átadni anélkül, hogy másolni vagy másolni az adatokat. A virtuális gép eredetileg egy olyan csatlakoztatott lemezzel jön létre, amely nagy mennyiségű adatmennyiséggel rendelkezik.  Ezután bármely felhasználó igényelheti a gépet, és hozzáférhet az adatokhoz. Ha kész, a virtuális gép kijogintik a virtuális gépet, hogy más felhasználók számára a gép.

Vannak kikötések, hogy a követelhető gépek, leggyakrabban körül egyre hozzáférést biztosít a géphez. Ha a gép tartományhoz csatlakozott, majd a felhasználó azt állítja, a gép kell már hozzáférést kapott, általában ez történik azáltal, hogy hozzáférést biztosít egy csoport, amely magában foglalja az összes felhasználó a laborban, amikor a virtuális gép jön létre. Ha a számítógép nem csatlakozik a tartományhoz, a virtuális **gép jelszóának alaphelyzetbe állítása** összetevőt a nyilvános tárházban kell futtatni a felhasználó rendszergazdaként való hozzáadásához.  A műtermékek a gép indítása vagy igénylése után is alkalmazhatók.

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikket: [Követelésre hivatkozható virtuális gépek létrehozása és kezelése az Azure DevTest Labsben](devtest-lab-add-claimable-vm.md)

---
title: Ajánlott eljárások az értékelés létrehozása az Azure Migrate Server Assessment |} A Microsoft Docs
description: Tippek az Azure Migrate Server Assessment értékelés létrehozásához.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: d417efd4abf14247af171ea77b479f590e14fe76
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812961"
---
# <a name="best-practices-for-creating-assessments"></a>Értékelések létrehozásának ajánlott eljárásai

[Az Azure Migrate](migrate-overview.md) hub eszközöket tartalmazza, amelyek segítséget nyújtanak a felderítése, értékelheti és migrálhatja az alkalmazásokat, az infrastruktúra és a számítási feladatok a Microsoft Azure biztosít. A hub tartalmaz, az Azure Migrate eszközökkel és külső független szoftverszállítók (ISV) szállító ajánlatok. 

Ez a cikk az Azure Migrate Server Assessment eszközzel értékelések létrehozásakor ajánlott eljárásokat foglalja össze. 

## <a name="about-assessments"></a>Értékelések kapcsolatban

Létrehozott egy Azure Migrate Server Assessment értékelések olyan adatok időponthoz pillanatképet. Az Azure Migrate értékelések két típusa van.

**Értékelés típusa** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Értékelések, amely ajánlásokat alapján összegyűjtött teljesítményadatok | Virtuális gép mérete javaslat Processzor és a memória kihasználtsági adatok alapján.<br/><br/> Lemez típusa javaslat (standard vagy prémium szintű felügyelt lemezek) az IOPS és átviteli sebessége, a helyi lemezek alapul.
**T-a helyszínen** | Amelyek nem használják a teljesítmény adatait ajánlásokat értékelések. | Virtuális gép mérete javaslat a helyszíni virtuális gép mérete alapján<br/><br> A javasolt lemez típusa alapján az értékelés beállítása a tárolótípus kiválasztása.

### <a name="example"></a>Példa
Például ha egy helyszíni virtuális gép 20 %-os kihasználtságot, és 8 GB memóriát, valamint 10 %-os kihasználtságot, négy maggal rendelkezik az értékelések a következők:

- **Értékelés teljesítményalapú**:
    - Javasolja a magok és a memória-mag (0,8 mag), és a memóriahasználat (0,8 GB) alapján.
    - Az értékelés egy alapértelmezett kompatibilitási faktor 30 %-os vonatkozik.
    - Virtuális gépekre vonatkozó javaslatot: ~1.4 mag (0,8 x1.3) és a ~1.4 GB memória.
- **T-a (helyszíni) értékelés**:
    -  Javasolja, négy magot; virtuális gép 8 GB memóriát.

## <a name="best-practices-for-creating-assessments"></a>Értékelések létrehozásának ajánlott eljárásai

Az Azure Migrate készülék folyamatosan profilt a helyszíni környezetben, és metaadat-és teljesítményadatokat küld az Azure-bA. Kövesse az ajánlott eljárások az értékelés létrehozása:

- **Hozzon létre-értékelések van**: Létrehozhatja úgy is – az értékelések közvetlenül a felderítés után.
- **Teljesítmény-alapú értékelés létrehozása**: Miután beállította a felderítés, azt javasoljuk, hogy legalább egy napot várni a teljesítményalapú értékelés futtatása előtt:
    - Teljesítményadatok gyűjtése időt vesz igénybe. Legalább egy napot vár biztosíthatja, hogy elegendő teljesítményt adatpont az értékelés futtatása előtt.
    - Teljesítményadatok a készülék valós idejű adatokat gyűjt minden teljesítménymetrikájának 20 másodpercenként, és legfeljebb egy öt perces adatpont összesíti azokat. A berendezés küld az öt perces adatpont az Azure-bA óránként értékelés kiszámítása.  
- **A legújabb adatok**: Értékelések automatikusan nem frissül a legújabb adatokkal. Az értékelés frissítése a legújabb adatokkal, kell ismételt futtatásához. 
- **Ellenőrizze, hogy a megfelelő időtartamok**: Amikor teljesítmény alapuló értékeléseket rendszert használ, ellenőrizze, hogy a profil a környezet értékelés idejére. Ha például hoz létre egy értékelést a teljesítmény időtartamát, egy hétre értékre, ha szeretne várjon legalább egy hétig felderítés az összes adatponttal gyűjtendő elindítása után. Ha nem, akkor az értékelés Ötcsillagos minősítést nem kaphat. 
- **Kerülje a hiányzó adatpontokat**: A következő problémák hiányzik az adatpontok teljesítményalapú értékeléssel eredményezhet:
    - Virtuális gépek is ki van kapcsolva az értékelés során, és a teljesítményadatok nem lesznek gyűjtve. 
    - Ha a virtuális gépek korábbi teljesítménye alapján, amelyen a hónap során hoz létre. a virtuális gépeken az adatokat kevesebb mint egy hónapig lesz. 
    - Az értékelés jön létre azonnal a felderítés után, vagy az értékelés időpontja nem felel meg a teljesítmény adatok gyűjtése idő.

## <a name="best-practices-for-confidence-ratings"></a>Ajánlott eljárások a megbízhatósági minősítés

Teljesítmény alapuló értékeléseket futtatásakor értékelése beszállító a megbízhatósági minősítés az 1 csillag (legalacsonyabb), 5 csillagos (legmagasabb). Megbízhatósági minősítés használandó hatékonyan:
- Az Azure Migrate Server Assessment VM CPU/memória és a lemez IOPS/átviteli sebességről kihasználtsági adataira van szüksége.
- Az összes virtuális Géphez csatolt hálózati adapter az Azure Migrate kell a hálózati bejövő és kimenő adatokat.
- Ha a kihasználtsági adatok nem érhető el a vCenter Serverben, Azure Migrate által adott méretjavaslat nem megbízható. 

Az elérhető adatpontok, függően meg az értékelés megbízhatósági minősítése az alábbi táblázat foglalja össze.

   **Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%-40% | 2 csillag
   41%-60% | 3 csillag
   61%-80% | 4 csillag
   81%-100% | 5 csillag

- A megbízhatósági minősítés, amely öt csillag alatt van egy értékelést kap, ha legalább egy napot várni, és ezután számíthatja újra az értékelést.
- Alacsony értékelésnek azt jelenti, hogy a méretezési javaslatok előfordulhat, hogy nem megbízható. Ebben az esetben javasoljuk, hogy az értékelésben adatokként módosítása – helyszíni assessment van.

## <a name="common-assessment-issues"></a>Gyakori értékelési problémák

Íme néhány gyakori hibákhoz környezetet érintő értékelések.

###  <a name="out-of-sync-assessments"></a>Szinkronizálás out értékelések

Adja hozzá, vagy a gép eltávolítása egy csoportból, az értékelés létrehozása után, ha az értékelés létrehozott lesznek megjelölve **szinkronizálás out**. Futtassa újra az értékelést (**újraszámítása**) a csoport változásainak.

### <a name="outdated-assessments"></a>Elavult értékelések

Ha a helyszíni virtuális gépek számára egy csoport lett adatokon, az értékelés van megjelölve **elavult**. Az elvégzett módosításoknak megfelelően, futtassa újra az értékelést.

### <a name="missing-data-points"></a>Hiányzó adatpontokat

Értékelés összes adatponttal a számos oka lehet, hogy nincs:

- Előfordulhat, hogy kikapcsolt virtuális gépek, az értékelés során, és a teljesítményadatok nem lesznek gyűjtve. 
- Virtuális gépek előfordulhat, hogy hozható létre, amelyek előzményei alapján a hónap során, a ily módon a teljesítményadatok kevesebb mint egy hónapig. 
- Az értékelés közvetlenül a felderítés után jött létre. Annak érdekében, hogy teljesítményadatokat gyűjtsön a megadott időn, meg kell várnia az értékelés futtatása előtt a megadott időn. Például ha szeretne egy hétig teljesítményadatok felmérése, meg kell felderítés után egy hét múlva. Ha nem, akkor az értékelés Ötcsillagos minősítést nem jelenik meg. 


## <a name="next-steps"></a>További lépések

- [Ismerje meg,](concepts-assessment-calculation.md) értékelések számításával.
- [Ismerje meg,](how-to-modify-assessment.md) értékelés testreszabása.

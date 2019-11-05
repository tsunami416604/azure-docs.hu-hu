---
title: Ajánlott eljárások az értékelés létrehozásához Azure Migrate Server Assessment alapján
description: Tippek az értékelések létrehozásához Azure Migrate Server Assessment segítségével.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: raynew
ms.openlocfilehash: e235116ab77159a0e2e9c66ad09cdb86ce6da1e9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466945"
---
# <a name="best-practices-for-creating-assessments"></a>Ajánlott eljárások az értékelések létrehozásához

[Azure Migrate](migrate-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz.

Ez a cikk Az értékelések Azure Migrate Server Assessment eszközzel történő létrehozásával kapcsolatos ajánlott eljárásokat foglalja össze.

## <a name="about-assessments"></a>Az értékelések ismertetése

Az Azure Migrate Server Assessmenttel létrehozott értékelések az adatok időpontra vonatkozó pillanatképei. A Azure Migrateban kétféle értékelés van.

**Értékelés típusa** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Az összegyűjtött teljesítményadatok alapján ajánlásokat tevő értékelések | A virtuálisgép-méretre vonatkozó javaslat a processzor-és memóriahasználat adatain alapul.<br/><br/> A lemez típusára vonatkozó javaslat (standard HDD/SSD vagy prémium szintű Managed Disks) a helyszíni lemezek IOPS és átviteli sebességén alapul.
**Helyszíni** | Az olyan értékelések, amelyek nem használnak teljesítményadatokat, ajánlásokat tesznek. | A VM-méretre vonatkozó javaslat a helyszíni virtuális gép méretétől függ.<br/><br> Az ajánlott lemez típusa az értékeléshez megadott tárolási típus beállításán alapul.

### <a name="example"></a>Példa
Ha például egy helyszíni virtuális gép négy maggal rendelkezik 20%-os kihasználtsággal, és 8 GB memóriát használ 10%-os kihasználtsággal, az értékelések a következők lesznek:

- **Teljesítmény-alapú értékelés**:
    - A hatékony magok és memória azonosítása a mag (4 x 0,20 = 0,8) és a memória (8 GB x 0,10 = 0,8) kihasználtsága alapján.
    - Az értékelés tulajdonságaiban megadott kényelmi tényezőt alkalmazza (tegyük fel, hogy 1,3 x) a méretezéshez használandó értékek lekéréséhez. 
    - Az Azure-ban a legközelebbi virtuálisgép-méretet javasolja, amely támogatja a ~ 1,04 mag (0,8 x 1,3) és a ~ 1,04 GB (0,8 x 1,3) memóriát.

- A **(helyszíni) felmérés**:
    -  Négy maggal rendelkező virtuális gépet javasol; 8 GB memória.

## <a name="best-practices-for-creating-assessments"></a>Ajánlott eljárások az értékelések létrehozásához

Az Azure Migrate készülék folyamatosan felméri a helyszíni környezetet, és metaadatokat és teljesítményadatokat küld az Azure-nak. Kövesse az alábbi ajánlott eljárásokat a berendezések használatával felderített kiszolgálók értékeléséhez:

- **Create as-is assessments**: a (z)-as értékeléseket azonnal létrehozhatja, amint a gépek megjelennek a Azure Migrate portálon.
- **Teljesítmény-alapú Értékelés létrehozása**: Miután beállította a felderítést, javasoljuk, hogy várjon legalább egy nappal a teljesítmény-alapú értékelés futtatása előtt:
    - A teljesítményadatok gyűjtése időt vesz igénybe. Legalább egy nap várakozásával gondoskodhat arról, hogy az értékelés futtatása előtt elegendő teljesítményadatok legyenek.
    - Ha teljesítmény-alapú értékeléseket futtat, győződjön meg arról, hogy a környezete az értékelés időtartamára van feldolgozva. Ha például egy hétre állítja össze az értékelést, akkor a felderítés megkezdése után legalább egy hétig meg kell várnia az összes összegyűjtött adatpontot. Ha nem, az értékelés nem kap öt csillagos minősítést.
- **Értékelések újraszámítása**: mivel az értékelések időponthoz kötődő Pillanatképek, nem frissülnek automatikusan a legújabb adattal. Ha frissíteni szeretne egy értékelést a legújabb adattal, újra kell számítania.

Kövesse az alábbi ajánlott eljárásokat a Azure Migrate-ba importált kiszolgálók értékeléséhez. CSV-fájl:

- **Create as-is assessments**: a (z)-as értékeléseket azonnal létrehozhatja, amint a gépek megjelennek a Azure Migrate portálon.
- **Teljesítmény-alapú Értékelés létrehozása**: ez segít a jobb költségbecslés megszerzésében, különösen akkor, ha a helyszíni kiszolgáló kapacitása túl van kiépítve. A teljesítmény-alapú értékelés pontossága azonban a kiszolgálók által megadott teljesítményadatokat függ. 
- **Értékelések újraszámítása**: mivel az értékelések időponthoz kötődő Pillanatképek, nem frissülnek automatikusan a legújabb adattal. Ha frissíteni szeretne egy értékelést a legújabb importált adattal, újra kell számítania.

## <a name="best-practices-for-confidence-ratings"></a>Ajánlott eljárások a megbízhatósági minősítéshez

A teljesítmény-alapú értékelések futtatásakor a rendszer az értékeléshez az 1 csillagos (legalacsonyabb) és az 5 csillag közötti megbízhatósági minősítést adja meg. Megbízhatósági minősítések hatékony használata:
- Azure Migrate kiszolgáló értékeléséhez a virtuális gép PROCESSZORának/memóriájának kihasználtsági adatai szükségesek.
- A helyszíni virtuális géphez csatlakoztatott minden lemezhez szükség van az írási/olvasási IOPS/adatátviteli adatokra.
- A virtuális géphez csatlakoztatott minden hálózati adapter esetében a hálózat be-és kimenő adatelérésére van szükség.

A kiválasztott időtartamhoz elérhető adatpontok százalékos arányának függvényében az értékelés megbízhatósági minősítése az alábbi táblázat szerint Összefoglalva lesz.

   **Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag


## <a name="common-assessment-issues"></a>Gyakori értékelési problémák

Az értékeléseket érintő gyakori környezeti problémák megoldásához.

###  <a name="out-of-sync-assessments"></a>Nem szinkronizált értékelések

Ha gépeket ad hozzá vagy távolít el egy csoportból egy Értékelés létrehozása után, a létrehozott értékelést a rendszer **szinkronként**jelöli meg. Futtassa újra az értékelést (**újraszámítva**), hogy tükrözze a csoport módosításait.

### <a name="outdated-assessments"></a>Elavult értékelések

Ha olyan helyszíni módosításokat végeznek a virtuális gépeken, amelyek egy értékelt csoportban vannak, az értékelés **elavultként**van megjelölve. A módosítások tükrözéséhez futtassa újra az értékelést.

### <a name="low-confidence-rating"></a>Alacsony megbízhatósági minősítés

Előfordulhat, hogy az értékelés számos okból nem rendelkezik az összes adatponttal:

- Nem végzett profilkészítést a környezeten abban az időtartamban, amelyre az értékelést létrehozta. Ha például egy hétre beállított teljesítmény *-alapú értékelést* hoz létre, akkor legalább egy hétig várnia kell, miután elindította az összes adatpont felderítését. Az **újraszámítások** lehetőségre kattintva megtekintheti a legújabb alkalmazható megbízhatósági minősítést. A megbízhatósági minősítés csak akkor alkalmazható, ha *teljesítmény-alapú* értékelést hoz létre.

- Néhány virtuális gép le lett állítva abban az időszakban, amelyhez az értékelést számította. Ha egyes virtuális gépek egy ideig ki lettek kapcsolva, a Server Assessment nem fog tudni teljesítményadatokat gyűjteni az adott időszakra vonatkozóan.

- Kevés virtuális gép lett létrehozva a felderítés indítását követően a Server Assessmentben. Ha például az utolsó egy hónap teljesítményelőzményeinek értékelését hozza létre, de néhány virtuális gép csak egy hete jött létre a környezetben. Ilyen esetekben az új virtuális gépek teljesítményadatai a teljes időtartamra vonatkozóan nem lesznek elérhetőek, és a megbízhatósági minősítés alacsony lesz.


## <a name="next-steps"></a>További lépések

- [További információ](concepts-assessment-calculation.md) az értékelések kiszámításáról.
- [Megtudhatja](how-to-modify-assessment.md) , hogyan szabhatja testre az értékeléseket.

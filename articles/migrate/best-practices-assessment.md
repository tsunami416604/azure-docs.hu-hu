---
title: Az Azure Áttelepítési kiszolgáló értékelésének gyakorlati tanácsai
description: Tippek az Azure Áttelepítési kiszolgáló felmérésével végzett értékelések létrehozásához.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185982"
---
# <a name="best-practices-for-creating-assessments"></a>Bevált gyakorlatok az értékelések elkészítéséhez

[Az Azure Migrate](migrate-overview.md) olyan eszközök központi eszköze, amelyek segítségével felderítheti, felmérheti és áttelepítheti az alkalmazásokat, az infrastruktúrát és a számítási feladatokat a Microsoft Azure-ba. A központ tartalmazza az Azure Migrate eszközöket és a külső független szoftverszállítói (ISV) ajánlatokat.

Ez a cikk az Azure Áttelepítési kiszolgáló értékelése eszközzel végzett értékelések létrehozása során ajánlott eljárásokat foglal össze.

## <a name="about-assessments"></a>Az értékelésekről

Az Azure Áttelepítési kiszolgáló értékelésével létrehozott értékelések az adatok időponthoz helyszíni pillanatképét képezik. Az Azure Migrate kétféle felmérést létezik.

**Értékelés típusa** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Az összegyűjtött teljesítményadatokon alapuló ajánlásokat fogalmazó értékelések | A virtuális gép méretre vonatkozó javaslata processzor- és memóriakihasználtsági adatokon alapul.<br/><br/> A lemeztípus-ajánlás (szabványos HDD/SSD vagy prémium szintű felügyelt lemezek) a helyszíni lemezek IOPS-on és átviteli keresztülértékén alapul.
**A hogy van a helyszíni** | Olyan értékelések, amelyek nem használnak teljesítményadatokat javaslatok megfogalmazásához. | A virtuális gép méretére vonatkozó javaslat a helyszíni virtuális gép méretén alapul<br/><br> Az ajánlott lemeztípus az értékelés tárolási típusának beállításában kiválasztott beállításokon alapul.

### <a name="example"></a>Példa
Például, ha egy helyszíni virtuális gép négy mag 20%-os kihasználtságú, és a memória 8 GB 10%-os kihasználtság, az értékelések a következők lesznek:

- **Teljesítményalapú értékelés:**
    - A hatékony magokat és memóriát azonosítja a mag (4 x 0,20 = 0,8) és a memória (8 GB x 0,10 = 0,8) kihasználtság alapján.
    - Az értékelési tulajdonságokban megadott komforttényező (tegyük fel, hogy 1,3x) alkalmazza a méretezéshez használt értékek et. 
    - A legközelebbi virtuális gép méretét ajánlja az Azure-ban, amely ~1,04 mag (0,8 x 1,3) és ~1,04 GB (0,8 x 1,3) memóriát támogat.

- **Ahelyszíni értékeléshez illet:**
    -  Négy maggal rendelkező virtuális gép használatát javasolja; 8 GB memória.

## <a name="best-practices-for-creating-assessments"></a>Bevált gyakorlatok az értékelések elkészítéséhez

Az Azure Migrate berendezés folyamatosan profilokat a helyszíni környezetben, és elküldi a metaadatok és a teljesítmény adatok az Azure-ba. Kövesse az alábbi gyakorlati tanácsokat a készülék használatával felfedezett kiszolgálók értékeléséhez:

- **Hozzon létre a hogy-értékelés:** Azonnal létrehozhat a hogy-is értékeléseket, amint a gépek megjelennek az Azure Migrate portálon.
- **Teljesítményalapú felmérés létrehozása:** A felderítés beállítása után azt javasoljuk, hogy várjon legalább egy napot a teljesítményalapú felmérés futtatása előtt:
    - A teljesítményadatok gyűjtése időbe telik. Várakozás legalább egy nap biztosítja, hogy elegendő teljesítmény adatpontok at az értékelés futtatása előtt.
    - Teljesítményalapú értékelések futtatásakor győződjön meg arról, hogy az értékelés időtartama alapján profilozhatja a környezetet. Ha például egy hétre beállított teljesítményidőtartamú értékelést hoz létre, a felderítés megkezdése után legalább egy hetet kell várnia az összes adatpont összegyűjtéséhez. Ha nem, az értékelés nem kap egy ötcsillagos minősítést.
- **Értékelések újraszámítása:** Mivel az értékelések időponthoz(pont-in-time pillanatképek), nem frissülnek automatikusan a legfrissebb adatokkal. Ha frissíteni szeretne egy értékelést a legfrissebb adatokkal, újra kell számítania azértékelést.

Kövesse az alábbi gyakorlati tanácsokat az Azure Áttelepítése szolgáltatásba importált kiszolgálók értékeléséhez a rendszeren keresztül. CSV-fájl:

- **Hozzon létre a hogy-értékelés:** Azonnal létrehozhat a hogy-is értékeléseket, amint a gépek megjelennek az Azure Migrate portálon.
- **Teljesítményalapú felmérés létrehozása:** Ez segít jobb költségbecslést kapni, különösen akkor, ha a kiszolgáló kapacitása a helyszínen túlterhelt. A teljesítményalapú értékelés pontossága azonban a kiszolgálókhoz megadott teljesítményadatoktól függ. 
- **Értékelések újraszámítása:** Mivel az értékelések időponthoz(pont-in-time pillanatképek), nem frissülnek automatikusan a legfrissebb adatokkal. Ha frissíteni szeretne egy értékelést a legújabb importált adatokkal, újra kell számítania azadatokat.

## <a name="best-practices-for-confidence-ratings"></a>A megbízhatósági minősítések bevált módszerei

A teljesítményalapú értékelések futtatásakor az értékelés hez 1 csillagos (legalacsonyabb) és 5 csillagos (legmagasabb) megbízhatósági besorolás jár. A megbízhatósági minősítések hatékony használata:
- Az Azure Áttelepítési kiszolgáló felmérése szükséges a virtuális gép CPU/memória kihasználtsági adatokat.
- A helyszíni virtuális géphez csatlakoztatott minden egyes lemezhez szüksége van az olvasási/írási IOPS/átviteli sebességű adatokra.
- A virtuális géphez csatlakoztatott minden egyes hálózati adapterhez szüksége van a hálózati be- és kimenő adatokra.

A kiválasztott időtartamhoz rendelkezésre álló adatpontok százalékos arányától függően az értékelés megbízhatósági besorolása az alábbi táblázatban összesítve történik.

   **Az adatpont elérhetősége** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag


## <a name="common-assessment-issues"></a>Közös értékelési kérdések

Az értékeléseket érintő gyakori környezeti problémák kezelése az alábbiakban elmondott.

###  <a name="out-of-sync-assessments"></a>Szinkronon kívüli értékelések

Ha egy értékelés létrehozása után gépeket ad hozzá vagy távolít el egy csoportból, a létrehozott értékelés **nem lesz szinkronban megjelölve.** Futtassa újra az értékelést (**Újraszámítás**) a csoport változásainak megfelelően.

### <a name="outdated-assessments"></a>Elavult értékelések

Ha vannak helyszíni változások a virtuális gépek, amelyek egy csoportban, amely értékelt, az értékelés **elavult.** A módosítások tükrözése érdekében futtassa újra az értékelést.

### <a name="low-confidence-rating"></a>Alacsony megbízhatósági besorolás

Előfordulhat, hogy egy értékelés nem rendelkezik az összes adatponttal, több okból is:

- Nem végzett profilkészítést a környezeten abban az időtartamban, amelyre az értékelést létrehozta. Ha például egy *teljesítményalapú felmérést* hoz létre egy hétre állítva, legalább egy hetet kell várnia az összes adatpont gyűjtésére vonatkozó felderítés megkezdése után. Mindig kattintson **az Újraszámítás gombra** a legfrissebb konfidenciaminősítés megtekintéséhez. A megbízhatósági minősítés csak akkor alkalmazható, ha *teljesítményalapú* értékelést hoz létre.

- Néhány virtuális gép le lett állítva abban az időszakban, amelyhez az értékelést számította. Ha egyes virtuális gépek egy ideig ki lettek kapcsolva, a Server Assessment nem fog tudni teljesítményadatokat gyűjteni az adott időszakra vonatkozóan.

- Kevés virtuális gép lett létrehozva a felderítés indítását követően a Server Assessmentben. Ha például az utolsó egy hónap teljesítményelőzményeinek értékelését hozza létre, de néhány virtuális gép csak egy hete jött létre a környezetben. Ilyen esetekben az új virtuális gépek teljesítményadatai a teljes időtartamra vonatkozóan nem lesznek elérhetőek, és a megbízhatósági minősítés alacsony lesz.


## <a name="next-steps"></a>További lépések

- [Ismerje meg,](concepts-assessment-calculation.md) hogyan számítják ki az értékeléseket.
- [További információ](how-to-modify-assessment.md) az értékelések testreszabásáról.

---
title: "A StorSimple sávszélesség sablonok kezelése |} Microsoft Docs"
description: "Ismerteti a StorSimple sávszélesség sablonokat, amelyek lehetővé teszik a sávszélesség-használat szabályozása."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0027b90e-91a5-437d-9bd0-06b05674aa5f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b426a0fc8e0a6c8615b348d3e832c134be4dfbaf
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>A StorSimple Manager szolgáltatással a StorSimple sávszélesség sablonok kezelése
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [StorSimple sávszélesség sablonok kezelése a StorSimple Manager szolgáltatás segítségével](storsimple-8000-manage-bandwidth-templates.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
Sávszélesség-sablonok lehetővé teszik a réteg a StorSimple eszköz a felhőre adatait több idő napi ütemezés konfigurálja a hálózati sávszélesség-használat.

A sávszélesség-szabályozási ütemezések segítségével:

* Adja meg a testreszabott sávszélesség ütemezést attól függően, hogy a munkaterhelés hálózati módjait.
* Kezelésének központosítása és az ütemezések több eszközön könnyen és zökkenőmentes módon használja fel.

> [!NOTE]
> Ez a funkció csak a StorSimple fizikai eszközök és a virtuális eszköz nem érhető el.
> 
> 

A szolgáltatás sávszélesség-sablonok táblázatos formában jelennek meg, és a következő információkat tartalmazza:

* **Név** – egy egyedi nevet hozzárendelni a sávszélesség-sablon létrehozásakor.
* **Ütemezés** – egy adott sávszélesség sablonban található ütemezések száma.
* **Által használt** – a sávszélesség-sablonokkal kötetek számát.

A StorSimple Manager szolgáltatás használ **konfigurálása** sávszélesség sablonok kezelése a klasszikus Azure portálra a lap.

További információt talál a sávszélesség-sablonok konfigurálása is található:

* Sávszélesség-sablonokkal kapcsolatos kérdések és válaszok
* Gyakorlati tanácsok a sávszélesség-sablonok

## <a name="add-a-bandwidth-template"></a>Sávszélességsablon hozzáadása
A következő lépésekkel hozzon létre egy új sávszélesség-sablont.

#### <a name="to-add-a-bandwidth-template"></a>Sávszélességsablon hozzáadása
1. A StorSimple Manager szolgáltatás **konfigurálása** kattintson **sávszélességsablon hozzáadása/szerkesztése**.
2. Az a **Sávszélességsablon hozzáadása/szerkesztése** párbeszédpanel:
   
   1. Az a **sablon** legördülő listában válassza **hozzon létre új** új sávszélességsablon hozzáadása.
   2. Adja meg a sávszélesség-sablon egyedi nevét.
3. Adja meg a **sávszélesség ütemezés**. Ütemezés létrehozása:
   
   1. A legördülő listából válassza ki a napjait a héten az ütemezés beállítása. Több nap előtt a listában a megfelelő nap található jelölőnégyzetek bejelölésével választhatja.
   2. Válassza ki a **minden nap** lehetőséget, ha az ütemezés jön létre, mint a teljes napot. Ha ez a beállítás be van jelölve, akkor már nem adhat meg egy **kezdete** vagy egy **befejezésének**. Az ütemezés a 12:00-kor fut 23:59 óra.
   3. A legördülő listából válassza ki a **kezdete**. Ez akkor, ha az ütemezés megkezdődik.
   4. A legördülő listából válassza ki az **befejezésének**. Ez akkor, ha az ütemezés leáll.
      
      > [!NOTE]
      > Átfedő ütemezések nem engedélyezettek. A kezdő és záró időpontjának az átfedő ütemezés eredményez, ha látni fogja a figyelmet egy hibaüzenetet.
      > 
      > 
   5. Adja meg a **sávszélesség mértékének**. Ez a megabit / másodperc (Mbps) használata esetén a felhőben (feltöltések és letöltések) műveletek során a StorSimple eszköz által használt sávszélességet. Adjon meg egy 1 és 1000 ennél a mezőnél közötti számot.
   6. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). A létrehozott sablon nem kerülnek be a szolgáltatás a sávszélesség-sablonok listájának **konfigurálása** lap.
      
      ![Új sávszélesség-sablon létrehozása](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)
4. Kattintson a **mentése** a lapot, és kattintson az alsó **Igen** során a rendszer megerősítést kér. Ez a konfigurációs módosításokat végzett menti.

## <a name="edit-a-bandwidth-template"></a>A sávszélesség-sablon szerkesztése
A következő lépésekkel sávszélességsablon szerkesztése.

### <a name="to-edit-a-bandwidth-template"></a>A sávszélesség-sablon szerkesztése
1. Kattintson a **sávszélességsablon hozzáadása/szerkesztése**.
2. Az a **Sávszélességsablon hozzáadása/szerkesztése** párbeszédpanel:
   
   1. Az a **sablon** legördülő menüben válassza ki a módosítani kívánt meglévő sávszélesség-sablon.
   2. Végezze el a módosításokat. (Ezt módosíthatja a meglévő beállításokat.)
   3. Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). A szolgáltatás konfigurálása lapon látni fogja a módosított sablon sávszélesség sablonok listáján.
3. A módosítások mentéséhez kattintson **mentése** az oldal alján. Kattintson a **Igen** során a rendszer megerősítést kér.

> [!NOTE]
> Ön nem kapnak a módosítások mentéséhez, ha a szerkesztett ütemezés átfedésben van a sávszélesség-sablont, amely módosítja a meglévő ütemezés.
> 
> 

## <a name="delete-a-bandwidth-template"></a>Sávszélesség sablon törlése
A következő lépésekkel sávszélesség sablon törlése.

#### <a name="to-delete-a-bandwidth-template"></a>Sávszélesség sablon törlése
1. A sávszélesség-sablonok, a szolgáltatás táblázatos listájának válassza ki a törölni kívánt sablont. A Törlés ikonra (**x**) jelenik meg a kiválasztott sablon szélsőséges jobb. Kattintson a **x** ikon a sablon törlésére.
2. Megerősítést kér bekéri. Kattintson a **OK** a folytatáshoz.

Ha a sablon bármely kötet(ek) használja, akkor nem kapnak törli-e. Egy hiba üzent arról, hogy a sablon használatban jelenik meg. Egy hibaüzenet párbeszédpanelén megjelenik, hogy tanácsadást meg, hogy a sablon minden hivatkozást el kell távolítani.

A sablont a hivatkozások törléséhez fér hozzá a **Kötettárolók** lap és, hogy használjon egy másik sablont, vagy használjon egy egyéni vagy korlátlan sávszélesség-beállítása ezt a sablont használó kötettárolók módosítása. Minden hivatkozást el lettek távolítva, törölheti a sablont.

## <a name="use-a-default-bandwidth-template"></a>Sávszélesség alapértelmezett sablon használata
Alapértelmezett sávszélességsablon valósul meg, és segítségével kötettárolók alapértelmezés szerint kényszerítése a sávszélesség szabályozását a felhőben való hozzáféréskor. Az alapértelmezett sablont is szolgál a felhasználók számára, akik a saját sablonok létrehozása készen áll a hivatkozást. Az alapértelmezett sablon részleteit a következők:

* **Név** – a korlátlan éjszakák, és a hétvégéket
* **Ütemezés** – egy ütemezéssel hétfőtől péntekig, amely egy 8 óra és délután 5 óra eszköz idő közötti 1 MB/s sávszélesség mértékét vonatkozik. A sávszélesség nincs korlátozás van megadva a fennmaradó része a hét.

Az alapértelmezett sablon szerkeszthető. Ez a sablon (beleértve a szerkesztett verzióit) használatát a rendszer nyomon követi.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Egy megadott időpontban elinduló napos sávszélesség sablon létrehozása
Az alábbi eljárás segítségével hozzon létre egy ütemezést, amely elindítja a megadott időpontban, és minden nap futtatja. A példában az ütemezés reggel 9 óra kezdődik, és csak 9 óra a következő reggel futtat. Fontos megjegyezni, hogy a megadott ütemezés kezdési és befejezési idejének egyaránt tartalmaznia kell a 24 órás azonos ütemezéssel, és nem terjedhetnek ki több nap. Ha kell, amelyek több nap több sávszélesség-sablonok, szüksége lesz ahhoz, hogy használható egyszerre több ütemezés (a példában látható módon).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Egy napos sávszélesség-sablon létrehozása
1. Hozzon létre egy ütemezést, reggel Reggel 9 és éjfél futtatja.
2. Adjon hozzá egy másik ütemezést. Konfigurálja a második ütemezik arra az éjfél csak reggel 9 óra.
3. A sávszélesség-sablon mentéséhez.

Az összetett ütemezés majd indítsa el a egyszerre, és futtassa napos.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Sávszélesség-sablonokkal kapcsolatos kérdések és válaszok
**A Q**. Mi történik a sávszélesség szabályozását, amikor Between az ütemezéseket? (Ütemezés befejeződött, és egy másikat még nem kezdődött meg.)

**A**. Ilyen esetekben nem sávszélesség-vezérlés alkalmazzák. Ez azt jelenti, hogy az eszköz használhatja a adatok összekapcsolása a felhővel rétegezéséhez korlátlan sávszélesség.

**A Q**. Módosíthatja a sávszélesség-sablonok offline-eszközön?

**A**. Csak akkor tudja módosítani a sávszélesség-sablonok a kötetek tárolók, ha a megfelelő eszköz offline állapotban.

**A Q**. Szerkesztheti társított kötettároló, ha a társított kötetek offline sávszélességsablon?

**A**. A kötettároló, amelynek a kötetek offline módban társított sávszélességsablon módosíthatja. Vegye figyelembe, hogy kötetek offline módban, amikor nincsenek adatok fog helyezhető el az eszközről a felhőbe.

**A Q**. Törölheti alapértelmezett sablont?

**A**. Bár az alapértelmezett sablon törléséhez nincs ehhez jó ötlet. Az alapértelmezett sablont, többek között a szerkesztett verziók használatát a rendszer nyomon követi. A nyomon követési adatok elemzése és az idő folyamán fejlesztésére szolgálnak az alapértelmezett sablont.

**A Q**. Hogyan meg, meg, hogy a sávszélesség-sablonokat kell módosítani?

**A**. A jelek, hogy módosítania kell a sávszélesség-sablonok egyik jelent meg, a hálózati lelassul, vagy egy napon belül több alkalommal fogyasztja indításakor. Ez akkor fordul elő, ha figyelni a tárolás és a használati hálózati i/o-teljesítmény- és hálózati átviteli diagramok megtekintésével.

A hálózati átviteli adatokból azonosítsa a nap és a kötet-tárolókban, a hálózati szűk következik be. Ha ez történik, ha az adatokat a felhőbe alatt többszintű (Ez az információ lekérése az összes kötet tárolókat felhőbe eszköz i/o műveleteinek teljesítménye), akkor a sávszélesség-sablonokat a kötettárolók társított módosítani kell.

Miután a módosított sablonok használatban van, szüksége lesz a hálózat újra az jelentős késések fordulnak elő. Ha ezek továbbra is létezik, majd szüksége lesz a sávszélesség-sablonok le újra.

**A Q**. Mi történik, ha az eszközön több kötettárolók ütemezi, hogy hozzon létre, de különböző korlátok alkalmazása az egyes?

**A**. Tegyük fel, hogy rendelkezik-e egy eszközön keresztül a 3 kötettárolók. Az ezekhez a tárolókhoz teljesen társított ütemezések átfedésben vannak. Az egyes tárolókban a használt sávszélesség korlátja esnek 15 MB/s, 5, 10 kulcsattribútumokkal. I/o előforduló összes ezekhez a tárolókhoz egyszerre, ha a 3 sávszélességkorlátok minimális alkalmazható: Ebben az esetben 5 MB/s, ezek i/o-kérelmek kimenő megosztás ugyanazon a várólistán.

## <a name="best-practices-for-bandwidth-templates"></a>Gyakorlati tanácsok a sávszélesség-sablonok
Kövesse az alábbi gyakorlati tanácsok a StorSimple eszközhöz:

* Sávszélesség-sablonok konfigurálása az eszközön a változó szabályozásának engedélyezése a hálózati átviteli az eszköz által a különböző napszakokban. A mentési ütemezések használata esetén a sávszélesség sablonok csúcsidőn hatékonyan használhatják fel a felhőműveletek további hálózati sávszélességet.
* A központi telepítés és a szükséges helyreállítási idő célkitűzése (RTO) alapján adott üzembe helyezéséhez szükséges tényleges sávszélességet is kiszámíthatja.

## <a name="next-steps"></a>Következő lépések
További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).


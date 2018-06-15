---
title: A StorSimple 8000 series sávszélesség sablonok kezelése |} Microsoft Docs
description: Ismerteti a StorSimple sávszélesség sablonokat, amelyek lehetővé teszik a sávszélesség-használat szabályozása.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 50d0a920bef097013feddc828d2c37133b9057b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875073"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>A StorSimple Device Manager szolgáltatással a StorSimple sávszélesség sablonok kezelése

## <a name="overview"></a>Áttekintés

Sávszélesség-sablonok lehetővé teszik a réteg a StorSimple eszköz a felhőre adatait több idő napi ütemezés konfigurálja a hálózati sávszélesség-használat.

A sávszélesség-szabályozási ütemezések segítségével:

* Adja meg a testreszabott sávszélesség ütemezést attól függően, hogy a munkaterhelés hálózati módjait.
* Kezelésének központosítása és az ütemezések több eszközön könnyen és zökkenőmentes módon használja fel.

> [!NOTE]
> Ez a funkció csak a StorSimple fizikai eszközök (modellek 8100 és 8600) és a StorSimple felhő készülékek (modellek 8010-es és a 8020-as modell) nem érhető el.


## <a name="the-bandwidth-templates-blade"></a>A sávszélesség-sablonok panel

A **sávszélesség sablonok** panelen táblázatos formában a szolgáltatás sávszélesség-sablonok rendelkezik, és a következő információkat tartalmazza:

* **Név** – egy egyedi nevet hozzárendelni a sávszélesség-sablon létrehozásakor.
* **Ütemezés** – egy adott sávszélesség sablonban található ütemezések száma.
* **Által használt** – a sávszélesség-sablonokkal kötetek számát.

További információt talál a sávszélesség-sablonok konfigurálása is található:

* [Sávszélesség-sablonokkal kapcsolatos kérdések és válaszok](#questions-and-answers-about-bandwidth-templates)
* [Gyakorlati tanácsok a sávszélesség-sablonok](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Sávszélességsablon hozzáadása

A következő lépésekkel hozzon létre egy új sávszélesség-sablont.

#### <a name="to-add-a-bandwidth-template"></a>Sávszélességsablon hozzáadása

1. Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, kattintson a **sávszélesség sablonok** majd **+ Hozzáadás sávszélességsablon**.

    ![Kattintson a + sávszélességsablon hozzáadása](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Az a **sávszélességsablon hozzáadása** panelen tegye a következőket:
   
    1. Adja meg a sávszélesség-sablon egyedi nevét.
    2. Sávszélesség ütemezés meghatározása. Ütemezés létrehozása:
   
        1. A legördülő listából válassza ki a **nap** a héten az ütemezés beállítása. Kiválaszthatja, hogy több nap.        
        
        2. Adjon meg egy **kezdete** a _óó: pp_ formátumban. Ez akkor, ha az ütemezés megkezdődik.

        3. Adjon meg egy **befejezésének** a _óó: pp_ formátumban. Ez akkor, ha az ütemezés leáll.
      
           > [!NOTE]
           > Átfedő ütemezések nem engedélyezettek. A kezdő és záró időpontjának az átfedő ütemezés eredményez, ha látni fogja a figyelmet egy hibaüzenetet.

        4. Adja meg a **sávszélesség mértékének**. Ez a megabit / másodperc (Mbps) használata esetén a felhőben (feltöltések és letöltések) műveletek során a StorSimple eszköz által használt sávszélességet. Adjon meg egy 1 és 1000 ennél a mezőnél közötti számot.

            ![Sávszélesség ütemezés megadása](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Ismételje meg a sablon egyszerre több ütemezés definiálásához, amíg végzett a fenti lépéseket.

        5. Kattintson a **Hozzáadás** sávszélességsablon létrehozásához. A létrehozott sablon sávszélesség-sablonok listájának kerül.
      

## <a name="edit-a-bandwidth-template"></a>A sávszélesség-sablon szerkesztése

A következő lépésekkel sávszélességsablon szerkesztése.

### <a name="to-edit-a-bandwidth-template"></a>A sávszélesség-sablon szerkesztése

1. Nyissa meg a StorSimple Device Manager szolgáltatást, és kattintson a **sávszélesség sablonok**.
2. A sávszélesség-sablonok listájának megtekintéséhez válassza ki a törölni kívánt sablont. Kattintson a jobb gombbal, és válassza ki a helyi menüből **törlése**.
3. Amikor felszólítja a megerősítésre, kattintson **OK**. Ez a sávszélességsablon törölje. 
4. A törlés sávszélesség sablonok frissítések listája.

> [!NOTE]
> A módosítások nem menthetők, ha a szerkesztett ütemezés átfedésben van a sávszélesség-sablont, amely módosítja a meglévő ütemezés.

## <a name="delete-a-bandwidth-template"></a>Sávszélesség sablon törlése

A következő lépésekkel sávszélesség sablon törlése.

#### <a name="to-delete-a-bandwidth-template"></a>Sávszélesség sablon törlése

1. Nyissa meg a StorSimple Device Manager szolgáltatást, és kattintson a **sávszélesség sablonok**.
2. A sávszélesség-sablonok listájának megtekintéséhez válassza ki a törölni kívánt sablont. Kattintson a jobb gombbal, és a helyi menüből válassza a törlés.
3. Amikor felszólítja a megerősítésre, kattintson **OK**. Ez a sávszélességsablon törölje.
4. A törlés sávszélesség sablonok frissítések listája.

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

További információ [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).


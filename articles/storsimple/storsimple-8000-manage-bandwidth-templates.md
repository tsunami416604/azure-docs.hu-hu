---
title: A StorSimple 8000 sorozat sávszélességsablonok kezelése |} A Microsoft Docs
description: A StorSimple sávszélesség sablonokat, amelyek segítségével szabályozhatja a sávszélesség-használat kezelését ismerteti.
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
ms.openlocfilehash: 13a3e57bb27c075fc045e87790dbe13369ed9f8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699471"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>A StorSimple-Eszközkezelő szolgáltatás segítségével a StorSimple sávszélességsablonok kezelése

## <a name="overview"></a>Áttekintés

Sávszélességsablonok lehetővé teszik több idő napi ütemezés az adatok a StorSimple-eszközről a felhőbe, a hálózati sávszélesség használatának konfigurálhatók.

A sávszélesség-szabályozási ütemezések segítségével:

* Adja meg a testre szabott sávszélesség ütemezések attól függően, a számítási feladatok hálózati használatokban.
* Felügyeleti központosítása, és újra felhasználhatja az ütemezések több eszközön egy egyszerű és zökkenőmentes módon.

> [!NOTE]
> Ez a funkció csak a StorSimple fizikai eszköz (8100-as és 8600-as modellek) és a StorSimple Cloud Appliance (8010 és 8020-as modellek) nem érhető el.


## <a name="the-bandwidth-templates-blade"></a>A sávszélesség-sablonok panel

A **sávszélességsablonok** panelen táblázatos formában a szolgáltatás sávszélességsablonok rendelkezik, és a következő információkat tartalmazza:

* **Név** – a sávszélesség-sablon létrehozásakor hozzárendelt egyedi nevet.
* **Ütemezés** – a megadott sávszélességsablon lévő ütemezések száma.
* **Által használt** – a sávszélesség-sablonok használatával kötetek számát.

További információt talál a sávszélesség-sablonok konfigurálására is talál:

* [Sávszélességsablonok kapcsolatos kérdések és válaszok](#questions-and-answers-about-bandwidth-templates)
* [Sávszélességsablonok ajánlott eljárásai](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Sávszélességsablon hozzáadása

A következő lépésekkel hozzon létre egy új sávszélesség-sablont.

#### <a name="to-add-a-bandwidth-template"></a>Sávszélességsablon hozzáadása

1. Keresse meg a StorSimple-Eszközkezelő szolgáltatást, kattintson a **sávszélességsablonok** majd **+ Hozzáadás sávszélességsablon**.

    ![Kattintson a + sávszélességsablon hozzáadása](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Az a **sávszélességsablon hozzáadása** panelen tegye a következőket:
   
    1. Adjon meg egy egyedi nevet a sávszélesség-sablont.
    2. A sávszélesség ütemezés meghatározása. Ütemezés létrehozása:
   
        1. A legördülő listából válassza ki a **nap** a héten az ütemezés van konfigurálva. Több napig is választhat.        
        
        2. Adjon meg egy **kezdő időpont** a _óó: pp_ formátumban. Ez akkor, ha az ütemezés megkezdődik.

        3. Adjon meg egy **befejezésének** a _óó: pp_ formátumban. Ez akkor, ha az ütemezés le fog állni.
      
           > [!NOTE]
           > Átfedésben lévő ütemezések nem engedélyezettek. Ha a kezdési és befejezési idejének az átfedő ütemezés azt eredményezi, látni fogja értelmű egy hibaüzenet.

        4. Adja meg a **sávszélesség**. Ez az a sávszélesség megadása megabit / másodperc (Mbps) a StorSimple eszköz (feltöltések és letöltések) a felhő érintő műveletek által használt. Ebben a mezőben 1–1000 közötti értéket adjon meg.

            ![A sávszélesség ütemezés definiálása](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Ismételje meg a sablon több ütemezés definiálásához, amíg végzett a fenti lépéseket.

        5. Kattintson a **Hozzáadás** sávszélességsablon létrehozásának megkezdéséhez. A létrehozott sablon hozzáadódik a sávszélesség-sablonok listáját.
      

## <a name="edit-a-bandwidth-template"></a>Sávszélességsablon szerkesztése

A következő lépésekkel sávszélességsablon szerkesztéséhez.

### <a name="to-edit-a-bandwidth-template"></a>Sávszélességsablon szerkesztése

1. Nyissa meg az a StorSimple-Eszközkezelő szolgáltatás, majd kattintson a **sávszélességsablonok**.
2. Sávszélességsablonok listája válassza ki a törölni kívánt sablont. Kattintson a jobb gombbal, és a helyi menüből válassza ki a **törlése**.
3. Amikor a rendszer megerősítést kér, kattintson a **OK**. Ezt érdemes törölni a sávszélességsablont. 
4. A törlés sávszélesség sablonok frissítések listája.

> [!NOTE]
> A módosítások nem menthetők, ha a szerkesztett ütemezés átfedésben van a sávszélességsablont, amely módosítja a meglévőket.

## <a name="delete-a-bandwidth-template"></a>Sávszélességsablon törlése

A következő lépésekkel sávszélességsablon törlése.

#### <a name="to-delete-a-bandwidth-template"></a>Sávszélességsablon törlése

1. Nyissa meg az a StorSimple-Eszközkezelő szolgáltatás, majd kattintson a **sávszélességsablonok**.
2. Sávszélességsablonok listája válassza ki a törölni kívánt sablont. Kattintson a jobb gombbal, és a helyi menüből válassza a törlés.
3. Amikor a rendszer megerősítést kér, kattintson a **OK**. Ezt érdemes törölni a sávszélességsablont.
4. A törlés sávszélesség sablonok frissítések listája.

Ha a sablon bármely (ek) használja, meg fog nem engedélyezi a törlést. Megjelenik egy üzenet, miszerint, hogy a sablon használatban van. Egy hibaüzenet párbeszédpanelén jelenik meg beküldte, hogy a sablon minden hivatkozást el kell távolítani.

A sablonhoz a hivatkozások elérésével törölheti a **Kötettárolók** oldal és, hogy azok egy másik sablonnal, vagy egy egyéni vagy korlátlan sávszélesség-beállítást használja ezt a sablont használó kötettároló módosítása. Ha minden hivatkozást el lettek távolítva, törölheti a sablont.

## <a name="use-a-default-bandwidth-template"></a>A sávszélesség alapértelmezett sablon használata

Alapértelmezett sávszélességsablon van megadva, és alapértelmezés szerint használt által kötettárolók, sávszélesség-vezérlés kikényszerítésére a felhő elérése. Az alapértelmezett sablont is, a felhasználók számára, akik a saját sablonok létrehozása kész referenciaként szolgál. Ez a sablon részletei a következők:

* **Név** – korlátlan számú éjszaka és Hétvége
* **Ütemezés** – egyetlen ütemezés hétfőtől péntekig, amely a sávszélesség 8-kor és 17: 00 eszköz idő közötti 1 MB/s vonatkozik. A sávszélesség a hét további részében korlátlan értékre van állítva.

Szerkesztheti az alapértelmezett sablont. Ez a sablon (beleértve a szerkesztett verzió) használatát a rendszer nyomon követi.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Hozzon létre egy egész napos sávszélességsablont, amely egy megadott időpontban kezdődik

Az alábbi eljárás segítségével hozzon létre egy ütemezést, amely egy megadott időpontban elindítja és futtatja a minden nap. A példában az ütemezés a reggel 9-kor kezdődik, és a következő reggel 9 Órakor-ig. Fontos megjegyezni, hogy adott ütemezés kezdő és befejező időpontok egyaránt tartalmaznia kell a ugyanolyan 24 órás ütemezés szerint, és nem esetleg több napon. Ha sávszélesség-sablonok, amelyek esetleg több napon van szüksége, szüksége lesz a több ütemezés használata (a példa szerint).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Egy egész napos sávszélesség-sablon létrehozása

1. Hozzon létre egy ütemezést, amely elindítja a reggel 9 órakor, és éjfél-ig.
2. Adjon hozzá egy másik ütemezést. Állítsa be a második ütemezés csak reggel 9 óra az éjfél futtatásához.
3. Mentse a sávszélesség-sablont.

Összetett ütemezés majd Ön egyszerre elindul és egész napos fut.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Sávszélességsablonok kapcsolatos kérdések és válaszok

**Q**. Mi történik a sávszélesség-vezérlés, amikor az ütemezések között áll? (Egy ütemezés véget ért, és egy másik még nem kezdődött meg.)

**A**. Ezekben az esetekben nem sávszélesség-vezérlés alkalmazzák. Ez azt jelenti, hogy az eszköz használhatja-e korlátlan sávszélesség, amikor az adatok felhőbeli rétegezés.

**Q**. Módosíthatja a sávszélességsablonok egy offline eszközön?

**A**. Nem tudja módosítani a kötetek tárolók a sávszélességsablonok, ha a megfelelő eszköz offline állapotban.

**Q**. Szerkeszthet egy sávszélességsablont társított kötettároló, ha a társított kötetek offline állapotban?

**A**. Módosíthatja egy sávszélességsablont, egy kötettárolót, amelynek a kötetek offline állapotban társítva. Vegye figyelembe, hogy kötetek offline állapotban, amikor nincsenek adatok rétegezettek lesznek az eszközről a felhőbe.

**Q**. Alapértelmezett sablon is törli?

**A**. Alapértelmezett sablon törölheti, bár nincs ehhez célszerű. Az alapértelmezett sablon, többek között a szerkesztett verziók,-használatát, a rendszer nyomon követi. A nyomon követési adatok elemzett és az idő folyamán, javításához használja fel az alapértelmezett sablont.

**Q**. Hogyan meg, hogy, hogy a sávszélességsablonok módosítani kell-e?

**A**. A jelentkezik, hogy módosítania a sávszélességsablonok egyik jelennek meg a hálózat lelassul, vagy egy nap alatt több alkalommal fogyasztja indításakor. Ha ez történik, figyelheti a storage és a használati hálózati megnézzük az i/o-teljesítmény- és hálózati átviteli sebesség diagramok.

A hálózati átviteli sebesség adatokból azonosítsa a nap és a hálózati szűk keresztmetszetet következik be kötettárolók idején. Ha ez történik, ha van folyamatban rétegzett adatok a felhőben (ezeket az információkat kérhet az eszköz – felhő minden kötettároló i/o-teljesítmény), majd a sávszélességsablonok a kötettárolók társított módosítani kell.

Miután a módosított sablonok használatban vannak, szüksége lesz a hálózat újra az jelentős késés érdekében. Ha ezek továbbra is létezik, majd kell gondolnia a sávszélességsablonok.

**Q**. Mi történik, ha az eszköz több kötettárolóba ütemezi, átfedés, de különböző korlátok vonatkoznak az egyes?

**A**. Tegyük fel, hogy a 3 olyan kötettárolókat eszközzel rendelkezik. Ezek a tárolók teljes mértékben társított ütemezéseket átfedik egymást. Az egyes tárolók a használt sávszélesség korlátja: 5, 10, és 15 MB/s jelölik. I/o az összes tárolók egy időben történnek, ha a minimális sávszélességi korlátozásait 3 lehet alkalmazni: Ebben az esetben az 5 MB/s, ezek kimenő i/o-kérelmek megosztása ugyanazt az üzenetsort.

## <a name="best-practices-for-bandwidth-templates"></a>Sávszélességsablonok ajánlott eljárásai

Kövesse az ajánlott eljárások a StorSimple eszközhöz:

* A sávszélesség-sablonok konfigurálása az eszközön a változó szabályozásának engedélyezése a hálózati átviteli által az eszközt különböző időpontokban, a nap. A sávszélességsablonok mentési ütemezések együtt használva csúcsidőn hatékonyan használhatják a felhőbeli műveletek további hálózati sávszélességet.
* Az üzembe helyezés és a szükséges helyreállítási időre vonatkozó célkitűzés (RTO) alapján adott telepítéshez szükséges tényleges sávszélesség kiszámításához.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [a StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).


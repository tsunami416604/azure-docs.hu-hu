---
title: Sávszélesség-sablonok kezelése a StorSimple 8000 sorozathoz | Microsoft dokumentumok
description: A StorSimple sávszélesség-sablonjainak kezelése, amelyek lehetővé teszik a sávszélesség-felhasználás szabályozását.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254897"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple sávszélesség-sablonok kezeléséhez

## <a name="overview"></a>Áttekintés

Sávszélesség-sablonok lehetővé teszik, hogy konfigurálja a hálózati sávszélesség-használat több idő-ból-nap ütemezések réteg az adatokat a StorSimple eszközről a felhőbe.

A sávszélesség-szabályozási ütemezések segítségével a következőket teheti:

* A számítási feladatok hálózati használatától függően adja meg a testreszabott sávszélesség-ütemezéseket.
* A felügyelet központosítása és az ütemezések egyszerű és zökkenőmentes módon történő újrafelhasználása több eszközön.

> [!NOTE]
> Ez a funkció csak a StorSimple fizikai eszközök (8100-as és 8600-as modellek) és nem a StorSimple felhőalapú készülékek (8010-es és 8020-as modellek) esetén érhető el.


## <a name="the-bandwidth-templates-blade"></a>A Sávszélesség sablonok panel

A **Sávszélesség-sablonok** panel táblázatos formátumban tartalmazza a szolgáltatás összes sávszélesség-sablonját, és a következő információkat tartalmazza:

* **Név** – A sávszélesség-sablonhoz a létrehozáskor hozzárendelt egyedi név.
* **Ütemezés** – Az adott sávszélesség-sablonban található ütemezések száma.
* **Által használt** – A kötetek száma a sávszélesség-sablonokat.

További információkat is találhat a sávszélesség-sablonok konfigurálásához:

* [Sávszélesség-sablonokkal kapcsolatos kérdések és válaszok](#questions-and-answers-about-bandwidth-templates)
* [Ajánlott eljárások a sávszélesség-sablonokhoz](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Sávszélesség-sablon hozzáadása

Új sávszélesség-sablon létrehozásához hajtsa végre az alábbi lépéseket.

#### <a name="to-add-a-bandwidth-template"></a>Sávszélesség-sablon hozzáadása

1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, kattintson **a Sávszélesség-sablonok** elemre, majd a **+ Sávszélesség hozzáadása sablon parancsra.**

    ![Kattintson a + Sávszélesség hozzáadása sablonra](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. A **Sávszélesség hozzáadása sablon** panelen tegye a következő lépéseket:
   
    1. Adja meg a sávszélesség-sablon egyedi nevét.
    2. Adja meg a sávszélesség ütemezését. Ütemezés létrehozása:
   
        1. A legördülő listából válassza ki azt a **hét napjait,** amelyekhez az ütemezés be van állítva. Több napot is kijelölhet.        
        
        2. Adjon meg **kezdési időpontot** _hh:mm_ formátumban. Ez az, amikor a menetrend kezdődik.

        3. Adjon meg záró **időt** _hh:mm_ formátumban. Ez az, amikor a menetrend leáll.
      
           > [!NOTE]
           > Az egymást átfedő ütemezések nem engedélyezettek. Ha a kezdési és befejezési időpontok egymást átfedő ütemezést eredményeznek, egy erre vonatkozó hibaüzenet jelenik meg.

        4. Adja meg a **sávszélesség-sebességet**. Ez a StorSimple-eszköz által a felhőt érintő műveletekben (feltöltések és letöltések) használt Megabit/másodperc (Mbps) sávszélessége. Ebben a mezőben 1–1000 közötti értéket adjon meg.

            ![Sávszélesség-ütemezés meghatározása](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Ismételje meg a fenti lépéseket a sablon több ütemezésének definiálásához, amíg el nem készül.

        5. A sávszélesség-sablon létrehozásának megkezdéséhez kattintson a **Hozzáadás** gombra. A létrehozott sablon hozzáadódik a sávszélesség-sablonok listájához.
      

## <a name="edit-a-bandwidth-template"></a>Sávszélesség-sablon szerkesztése

A sávszélesség-sablon szerkesztéséhez hajtsa végre az alábbi lépéseket.

### <a name="to-edit-a-bandwidth-template"></a>Sávszélesség-sablon szerkesztése

1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és kattintson **a Sávszélesség-sablonok**elemre.
2. A sávszélesség-sablonok listájában jelölje ki a törölni kívánt sablont. Kattintson a jobb gombbal, és a helyi menüben válassza a **Törlés parancsot.**
3. Amikor megerősítést kér, kattintson az **OK**gombra. Ezzel törölnie kell a sávszélesség-sablont. 
4. A sávszélesség-sablonok listája a törlésnek megfelelően frissül.

> [!NOTE]
> A módosítások nem menthetők, ha a szerkesztett ütemezés átfedésben van a módosítandó sávszélességsablon meglévő ütemezésével.

## <a name="delete-a-bandwidth-template"></a>Sávszélesség-sablon törlése

A sávszélesség-sablon törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-bandwidth-template"></a>Sávszélesség-sablon törlése

1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és kattintson **a Sávszélesség-sablonok**elemre.
2. A sávszélesség-sablonok listájában jelölje ki a törölni kívánt sablont. Kattintson a jobb gombbal, és a helyi menüben válassza a Törlés parancsot.
3. Amikor megerősítést kér, kattintson az **OK**gombra. Ezzel törölnie kell a sávszélesség-sablont.
4. A sávszélesség-sablonok listája a törlésnek megfelelően frissül.

Ha a sablont bármilyen kötet(ek) használják, akkor nem törölheti azt. Megjelenik egy hibaüzenet, amely jelzi, hogy a sablon használatban van. Megjelenik egy hibaüzenetes párbeszédpanel, amely azt tanácsolja, hogy a sablonra mutató összes hivatkozást el kell távolítani.

A sablonra mutató összes hivatkozást törölheti a **Kötettárolók** lap elérésével és a sablont használó kötettárolók módosításával, hogy azok egy másik sablont használjanak, vagy egyéni vagy korlátlan sávszélesség-beállítást használjanak. Ha az összes hivatkozást eltávolította, törölheti a sablont.

## <a name="use-a-default-bandwidth-template"></a>Alapértelmezett sávszélesség-sablon használata

Az alapértelmezett sávszélesség-sablon biztosított, és a kötettárolók alapértelmezés szerint a sávszélesség-szabályozás kényszerítésére szolgálnak a felhő elérésekor. Az alapértelmezett sablon kész referenciaként is szolgál a saját sablonokat létrehozó felhasználók számára. Ennek az alapértelmezett sablonnak a részletei a következők:

* **Név** - Korlátlan éjszakák és hétvégék
* **Ütemezés** – Hétfőtől péntekig egyetlen ütemezés, amely 1 Mb/s sávszélességet alkalmaz 8 és 17:00 óra között. A sávszélesség a hét hátralévő részében korlátlan.

Az alapértelmezett sablon szerkeszthető. A sablon használatát (beleértve a szerkesztett verziókat is) nyomon követjük.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Egész napos sávszélességsablon létrehozása, amely egy megadott időpontban kezdődik

Az alábbi eljárással olyan ütemezést hozhat létre, amely egy adott időpontban kezdődik és egész nap fut. A példában a menetrend reggel 9-kor kezdődik, és másnap reggel 9-ig tart. Fontos megjegyezni, hogy egy adott ütemezés kezdési és befejezési időpontjait ugyanazon a 24 órás ütemezésben kell tartani, és nem terjedhet ki több napra. Ha több napig terjedő sávszélesség-sablonokat kell beállítania, több ütemezést kell használnia (amint az a példában látható).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Egész napos sávszélesség-sablon létrehozása

1. Hozzon létre egy ütemtervet, amely reggel 9-kor kezdődik és éjfélig tart.
2. Adjon hozzá egy másik ütemezést. Állítsa be a második ütemezést éjféltől reggel 9 óráig.
3. Mentse a sávszélesség-sablont.

Az összetett ütemezés ezután az Ön által választott időpontban kezdődik, és egész nap fut.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Sávszélesség-sablonokkal kapcsolatos kérdések és válaszok

**Q**. Mi történik a sávszélesség-szabályozásokkal, ha az ütemezések között van? (Az ütemezés véget ért, és egy másik még nem kezdődött el.)

**A.** Ilyen esetekben nem alkalmaznak sávszélesség-szabályozást. Ez azt jelenti, hogy az eszköz korlátlan sávszélességet használhat az adatok felhőbe történő rétegezésekén.

**Q**. Módosíthatja a sávszélesség-sablonokat egy kapcsolat nélküli eszközön?

**A.** Nem módosíthatja a kötettárolók sávszélesség-sablonjait, ha a megfelelő eszköz offline állapotban van.

**Q**. Szerkesztheti a kötettárolóhoz társított sávszélesség-sablont, ha a társított kötetek offline állapotban vannak?

**A.** Módosíthatja a kötettárolóhoz társított sávszélesség-sablont, amelynek kötetei offline állapotban vannak. Vegye figyelembe, hogy ha a kötetek offline állapotban vannak, az eszközről a felhőbe nem lesznek rétegezve adatok.

**Q**. Törölhet egy alapértelmezett sablont?

**A.** Bár törölheti az alapértelmezett sablont, ez nem jó ötlet. Az alapértelmezett sablon használatát, beleértve a szerkesztett verziókat is, nyomon követi a rendszer. A nyomon követési adatok elemzése és az idő múlásával, az alapértelmezett sablon javítására szolgál.

**Q**. Hogyan állapítható meg, hogy a sávszélesség-sablonokat módosítani kell?

**A.** A sávszélesség-sablonok módosításának egyik jele, amikor elkezdi látni, hogy a hálózat egy nap folyamán többször lelassul vagy megfullad. Ha ez történik, figyelje a tárolási és használati hálózatot az I/O teljesítmény- és hálózati átviteli diagramok on-át.

A hálózati átviteli adatok, határozza meg a napszakot, és a kötet tárolók, amelyben a hálózati szűk keresztmetszet fordul elő. Ha ez akkor történik, amikor az adatok rétegzett a felhőbe (ezt az információt az i/o-teljesítmény az összes kötettárolók az eszköz felhőbe), majd módosítania kell a kötettárolók társított sávszélesség-sablonokat.

A módosított sablonok használata után újra figyelnie kell a hálózatot a jelentős késések miatt. Ha ezek még mindig léteznek, akkor újra meg kell látogatnia a sávszélesség-sablonokat.

**Q**. Mi történik, ha az eszközöm több kötettárolója átfedésben van az ütemezéssel, de mindegyikre különböző korlátok vonatkoznak?

**A.** Tegyük fel, hogy 3 kötettárolóval rendelkező eszközzel rendelkezik. A tárolókhoz társított ütemezések teljesen átfedik egymást. Az egyes tárolók, a sávszélesség-korlátok 5, 10 és 15 Mbps volt. Ha az i/o-érték az összes ilyen tárolón egyszerre történik, a 3 sávszélesség-korlát minimális értéke alkalmazható: ebben az esetben 5 Mbps, mivel ezek a kimenő I/O-kérések ugyanazt a várólistát osztják.

## <a name="best-practices-for-bandwidth-templates"></a>Ajánlott eljárások a sávszélesség-sablonokhoz

Kövesse az alábbi gyakorlati tanácsokat a StorSimple eszközhöz:

* Konfigurálja a sávszélesség-sablonokat az eszközön, hogy lehetővé tegye a hálózati átviteli hálózat változó szabályozását az eszköz által a nap különböző időpontjaiban. Ezek a sávszélesség-sablonok biztonsági mentési ütemezéssel való használat esetén hatékonyan kihasználhatják a felhőbeli műveletek hez a csúcsidőn kívüli műveletekhez a hálózati sávszélességet.
* Számítsa ki az adott központi telepítéshez szükséges tényleges sávszélességet a központi telepítés mérete és a szükséges helyreállítási idő célkitűzés (RTO) alapján.

## <a name="next-steps"></a>További lépések

További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)


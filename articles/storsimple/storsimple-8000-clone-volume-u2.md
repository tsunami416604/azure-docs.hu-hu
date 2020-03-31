---
title: Klónozzon egy kötetet a StorSimple 8000 sorozaton | Microsoft dokumentumok
description: Ismerteti a különböző klóntípusok és a használat, és bemutatja, hogyan lehet egy biztonsági másolat készlet klónegy adott kötet egy StorSimple 8000 sorozatú eszköz.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255001"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Kötet klónozása az Azure Portal StorSimple-eszközkezelő szolgáltatásával

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan használhatja a biztonsági másolat készletklónozására egy adott kötetet a **Biztonsági másolat katalógus** panelen keresztül. Ez megmagyarázza az *átmeneti* és *az állandó* klónok közötti különbséget is. Az oktatóanyagban szereplő útmutató a 3.

A StorSimple Eszközkezelő szolgáltatás **Biztonsági másolat katalógus** panel megjeleníti az összes biztonsági mentési készletek, amelyek a manuális vagy automatikus biztonsági mentések készítésekor jönnek létre. Ezután kiválaszthatja a klónozni kívánt biztonságimásolat-készletben a kötetet.

 ![Biztonságimásolat-készlet lista](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>A kötet klónozásának szempontjai

A kötet klónozásasorán vegye figyelembe az alábbi információkat.

- A klón ugyanúgy viselkedik, mint a normál kötet. Minden olyan művelet, amely egy köteten lehetséges, elérhető a klónszámára.

- A figyelés és az alapértelmezett biztonsági mentés automatikusan le van tiltva egy klónozott köteten. A biztonsági mentések esetében konfigurálnia kell egy klónozott kötetet.

- A helyileg rögzített kötetek rétegzett kötetként klónozva vannak. Ha a klónozott kötet helyi legrögzítettet szeretne, a klónozást helyileg rögzített kötetté alakíthatja a klónozássikeres befejezése után. A rétegzett kötetek helyileg rögzített kötetté alakításáról [a Kötettípus módosítása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)című részről tudhatja meg.

- Ha egy klónozott kötetet közvetlenül a klónozás után (ha még átmeneti klónként) helyileg rögzítettkötetté próbál konvertálni, az átalakítás a következő hibaüzenettel sikertelen:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Ez a hiba csak akkor jelenik meg, ha egy másik eszközre klónoz. Sikeresen konvertálhatja a kötetet helyileg rögzítettre, ha először konvertálja az átmeneti klónt állandó klónlá. Az átmeneti klón felhőbeli pillanatképe állandó klónlá alakításához.

## <a name="create-a-clone-of-a-volume"></a>Kötet klónjának létrehozása

Létrehozhat egy klónt ugyanazon az eszközön, egy másik eszközön, vagy akár egy felhőalapú készüléket egy helyi vagy felhőbeli pillanatkép használatával.

Az alábbi eljárás bemutatja, hogyan hozhat létre klónt a biztonsági másolat katalógusából.  A klónozás kezdeményezésének másik módja a **Kötetek**, válasszon ki egy kötetet, majd kattintson a jobb gombbal a helyi menü meghívásához, és válassza a **Klónozás parancsot.**

Hajtsa végre az alábbi lépéseket a kötet klónozásához a biztonsági másolat katalógusából.

#### <a name="to-clone-a-volume"></a>Kötet klónozása

1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és kattintson **a Katalógus biztonsági másolata**gombra.

2. Válasszon ki egy biztonságimentési készletet az alábbiak szerint:
   
   1. Válassza ki a megfelelő eszközt.
   2. A legördülő listában válassza ki a kijelölni kívánt biztonsági másolat kötet- vagy biztonsági mentési házirendet.
   3. Adja meg az időtartományt.
   4. A lekérdezés végrehajtásához kattintson az **Alkalmaz** gombra.

      A kijelölt kötethez vagy biztonsági mentési házirendhez társított biztonsági másolatnak meg kell jelennie a biztonságimásolat-készletek listájában.
   
      ![Biztonságimásolat-készlet lista](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Bontsa ki a biztonságimásolat-készletet a társított kötet megtekintéséhez és egy biztonságimásolat-készletben lévő kötet kiválasztásához. Kattintson a jobb gombbal, majd a helyi menüben válassza a **Klónozás parancsot.**

   ![Biztonságimásolat-készlet lista](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. A **Klónozás** panelen tegye a következő lépéseket:
   
   1. Azonosítsa a céleszközt. Ez az a hely, ahol a klón létrejön. Kiválaszthatja ugyanazt az eszközt, vagy megadhat egy másik eszközt.

      > [!NOTE]
      > Győződjön meg arról, hogy a klónhoz szükséges kapacitás alacsonyabb, mint a céleszközön rendelkezésre álló kapacitás.
       
   2. Adja meg a klón egyedi kötetnevét. A névnek 3 és 127 karakter közötti nek kell lennie.
      
       > [!NOTE]
       > A **Klónozási kötet mint** mező **rétegzett** lesz, még akkor is, ha helyileg rögzített kötetet klónoz. Ez a beállítás nem módosítható; ha azonban a klónozott kötetet is helyileg rögzíteni szeretné, a klónt helyileg rögzített kötetté alakíthatja, miután sikeresen létrehozta a klónt. A rétegzett kötetek helyileg rögzített kötetté alakításáról [a Kötettípus módosítása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)című részről tudhatja meg.
          
   3. A **Csatlakoztatott állomások csoportban**adja meg a klón hozzáférés-vezérlési rekordját (ACR). Hozzáadhat egy új ACR-t, vagy választhat a meglévő listából. Az ACR határozza meg, hogy mely állomások férhetnek hozzá ehhez a klónhoz.
      
       ![Biztonságimásolat-készlet lista](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. A művelet végrehajtásához kattintson a **Klónozás** gombra.

4. A klónozási feladat elindul, és a klón sikeres létrehozásáról értesítést kap. Kattintson a feladat értesítési vagy lépjen a **feladatok** panela klónfeladat figyeléséhez.

    ![Biztonságimásolat-készlet lista](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. A klónozási feladat befejezése után nyissa meg a készüléket, és kattintson **a Kötetek gombra.** A kötetek listájában látnia kell a klón, amely csak létre ugyanabban a kötettárolóban, amely rendelkezik a forráskötettel.

    ![Biztonságimásolat-készlet lista](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Egy klón, ami így jön létre, egy átmeneti klón. A klóntípusokról további információt az [Átmeneti és állandó klónok](#transient-vs-permanent-clones)című témakörben talál.


## <a name="transient-vs-permanent-clones"></a>Átmeneti és állandó klónok
Az átmeneti klónok csak akkor jönnek létre, ha egy másik eszközre klónoz. Egy adott kötet klónozható egy biztonsági mentési készlet egy másik eszköz a StorSimple Eszközkezelő által felügyelt. Az átmeneti klón az eredeti kötetben lévő adatokra hivatkozik, és ezeket az adatokat használja a céleszköz helyi olvasásához és írásához.

Miután egy átmeneti klón felhőbeli pillanatképét készített, az eredményül kapott klón *állandó* klón. A folyamat során az adatok egy példányát a felhőben jön létre, és az adatok másolásának idejét az adatok mérete és az Azure-késések (ez egy Azure-azure-beli másolat). Ez a folyamat napokig vagy hetekig is eltarthat. Az átmeneti klón állandó klónná válik, és nem hivatkozik az eredeti kötetadatokra, amelyekből klónozták.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Átmeneti és állandó klónok forgatókönyvei
A következő szakaszok olyan példahelyzeteket írnak le, amelyekben átmeneti és állandó klónok használhatók.

### <a name="item-level-recovery-with-a-transient-clone"></a>Elemszintű helyreállítás átmeneti klónnal
Vissza kell állítania egy egyéves Microsoft PowerPoint-bemutatófájlt. A rendszergazda azonosítja az adott biztonsági mentést attól az időponttól kezdve, majd szűri a kötetet. A rendszergazda ezután klónozza a kötetet, megkeresi a keresett fájlt, és megadja azt. Ebben a forgatókönyvben egy átmeneti klón t használ.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Tesztelés az éles környezetben állandó klónnal
Meg kell erősítenie a tesztelési hibát az éles környezetben. Hozzon létre egy klón a kötet az éles környezetben, majd egy felhőbeli pillanatképet a klón egy független klónozott kötet létrehozásához. Ebben a forgatókönyvben egy állandó klón használatos.

## <a name="next-steps"></a>További lépések
* További információ a [StorSimple kötet biztonsági másolatból történő visszaállításáról.](storsimple-8000-restore-from-backup-set-u2.md)
* Ismerje meg, hogyan [használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple-eszköz felügyeletére.](storsimple-8000-manager-service-administration.md)


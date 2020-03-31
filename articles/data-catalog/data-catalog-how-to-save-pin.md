---
title: Keresések és adatelemek rögzítése az Azure Data Catalogban
description: Útmutató cikk, amely kiemeli az Azure Data Catalog képességeit adatforrások és adatelemek későbbi használatra történő mentéséhez.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: a8545939fd027989519e0f7a161f750ec676749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976816"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Keresések és adatelemek rögzítése az Azure Data Catalogban
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog az adatforrás-felderítéshez nyújt lehetőségeket. Gyorsan kereshet és szűrheti a katalógust az adatforrások megkereséséhez és a tervezett cél megértéséhez, megkönnyítve a megfelelő adatok megtalálását az adott feladathoz.

De mi van, ha rendszeresen kell dolgoznia ugyanazzal az adattal? És mi van akkor, ha Ön és más felhasználók rendszeresen hozzájárulnak a tudás, hogy ugyanazokat az adatforrásokat a katalógusban? Ezekben a helyzetekben, kelljen ismételten kiadja ugyanazt a keresést nem hatékony. Itt segíthetnek a mentett keresés és a rögzített adatelemek.

## <a name="saved-searches"></a>Mentett keresések
A Data Catalog mentett keresése egy újrafelhasználható, felhasználónkénti keresési definíció. Megadhatja a keresést, beleértve a keresési kifejezéseket, címkéket és egyéb szűrőket, majd mentheti. A mentett keresési definíciót később újra futtathatja, hogy visszaadja a keresési feltételeknek megfelelő adateszközöket.

### <a name="create-a-saved-search"></a>Mentett keresés létrehozása
Mentett keresés létrehozásához tegye a következőket:
1. Az Azure Data Catalog portál **Aktuális keresés** ablakában kattintson a Mentés **gombra.** 

    ![Jelenlegi keresési beállítások Mentés hivatkozás](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Adja meg az újra felhasználni kívánt keresési feltételeket, majd kattintson a **Mentés gombra.**

    ![A keresés aktuális beállításainak mentett neve](./media/data-catalog-how-to-save-pin/02-name.png)

3. Amikor a rendszer kéri, adja meg a mentett keresés nevét. Válasszon egy értelmes nevet, amely leírja a keresés által visszaadott adateszközöket.

### <a name="manage-saved-searches"></a>Mentett keresések kezelése
Egy vagy több keresés mentése után megjelenik a **Mentett keresések** lehetőség az **Aktuális keresés mező** alatt. A lista kibontásakor az összes mentett keresés megjelenik.

 ![Adatkatalógus – A mentett keresések listája](./media/data-catalog-how-to-save-pin/03-list.png)

Végezze el az alábbi műveletek bármelyikét:

* Keresés végrehajtásához jelöljön ki egy mentett keresést a listában.

* A mentett keresés kezelési beállításainak listájának megtekintéséhez kattintson a keresési név melletti lefelé mutató nyílra.

    ![Mentett keresések kezelésének lehetőségei](./media/data-catalog-how-to-save-pin/04-managing.png)

* A mentett keresés új nevének megadásához válassza az **Átnevezés lehetőséget.** A keresési definíció nem változik.

* Ha el szeretné távolítani a mentett keresést a listából, válassza a **Törlés**lehetőséget, majd erősítse meg a törlést.

* Ha a mentett keresést szeretné alapértelmezett keresésként megjelölni, válassza a **Mentés alapértelmezettként**lehetőséget. Ha "üres" keresést hajt végre az Azure Data Catalog kezdőlapjáról, az alapértelmezett keresés végrehajtása történik. Ezenkívül az alapértelmezett keresésként megjelölt keresés a **Mentett keresések** lista tetején jelenik meg.

### <a name="organizational-saved-searches"></a>Szervezeti mentett keresések
A szervezet minden felhasználója mentheti a kereséseket saját használatra. A Data Catalog-rendszergazdák a szervezeten belüli összes felhasználókereséseit is menthetik. Amikor a rendszergazdák mentenek egy keresést, a **vállalati opción belül megjelenik nekik egy megosztás.** Ha ezt a beállítást választja, megosztja a szervezet összes felhasználójának mentett keresését.

 ![Adatkatalógus – Szervezeti mentett keresések](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Rögzített adateszközök
A mentett keresésekkel mentheti és újra felhasználhatja a keresési definíciókat. A keresések által visszaadott adateszközök idővel változhatnak a katalógus tartalmának változásával. Adatelemek rögzítésekénél kifejezetten azonosíthat bizonyos adatelemeket, hogy könnyebben elérhesd őket anélkül, hogy keresést kellene használnia.

Az adategység rögzítése egyszerű. Ha hozzá szeretné adni az adatelemet a rögzített listához, egyszerűen kattintson a **pin** ikonra. Az ikon jelenik meg az eszközcsempe sarkában a csempe nézetben, és a bal szélső oszlopban a lista nézetben az Azure Data Catalog portálon.

![Data Catalog - Az adateszköz pinikonja](./media/data-catalog-how-to-save-pin/05-pinning.png)

Az adateszköz rögzítésének leplezése egyaránt egyszerű. Egyszerűen kattintson a **rögzítés ionjának** a kijelölt eszköz beállításának váltásához.

![Adatkatalógus – Az adateszköz rögzítésének feloldása ikon](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A Saját eszközök szakasz
A Data Catalog portál kezdőlapja tartalmaz egy **Saját eszközök** szakaszt, amely az aktuális felhasználó számára érdekes eszközöket jelenít meg. Ez a szakasz a rögzített eszközöket és a mentett kereséseket is tartalmazza.

![A Saját eszközök szakasz a kezdőlapon](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Összefoglalás
Az Azure Data Catalog olyan funkciókat biztosít, amelyek megkönnyítik a szükséges adatforrások felderítését, így Ön és a szervezet többi tagja kevesebb időt tölthet az adatok felkelőzésével, és több időt tölthet velük. A mentett keresések és a rögzített adatelemek ezekre az alapvető képességekre épülnek, így a felhasználók könnyen azonosíthatják azokat az adatforrásokat, amelyekkel ismételten együttműködnek.

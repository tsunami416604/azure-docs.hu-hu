---
title: A keresések és a PIN-kód az adategységek mentése az Azure Data Catalog
description: Útmutató a cikk kiemelése képességek az Azure Data Catalog az adatforrásokat és a későbbi használat céljából adategységek mentéséhez.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 786d65eaf667ae8ae9dc2c91d3113f5057a98a27
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053751"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>A keresések és a PIN-kód az adategységek mentése az Azure Data Catalog
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog adatforrás-felderítés képességeket biztosít. Gyorsan kereshet, és keresse meg az adatforrásokat, és azok használatának célját megértése katalógus szűrése, így könnyebb megtalálni a megfelelő adatokat a feladat csak olyan mélységben.

De mi történik, ha ugyanazokat az adatokat rendszeresen együttműködve kell? És mi történik, ha más felhasználók rendszeresen közreműködés adatforrásokat a katalógusban található ismereteit? Ebben az esetben nem kell ismételten ki ugyanazokat a kereséseket lehet nem elég hatékony. Ez az, ahol mentett keresés és a rögzített adategységek révén.

## <a name="saved-searches"></a>Mentett keresések
Mentett keresés a Data Catalog egy újrafelhasználható, felhasználónkénti keresési definíciója. Adja meg a keresés, többek között a keresési kifejezéseket, a címkék és a többi szűrőt, és mentse. Újra futtathatja a mentett keresés definíciót később vissza bármely adategységeket a keresési feltételeknek.

### <a name="create-a-saved-search"></a>Mentett keresés létrehozása
Mentett keresés létrehozásához tegye a következőket:
1. Az Azure Data Catalog-portálon található a **aktuális keresés** ablakban kattintson a **mentése**. 

    ![Aktuális keresési beállítások mentése hivatkozás](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Adja meg a keresési feltételeknek, hogy újra felhasználhatja, és kattintson a kívánt **mentése**.

    ![Aktuális keresési beállítások mentett keresés neve](./media/data-catalog-how-to-save-pin/02-name.png)

3. Amikor a rendszer kéri, adja meg egy nevet a mentett keresésnek. Adjon meg egy beszédes nevet, és, amely leírja, hogy a keresés által visszaadott az adategységeket.

### <a name="manage-saved-searches"></a>Mentett keresések kezelése
Egy vagy több keresések mentése után egy **mentett keresések** beállítás alatt jelennek meg a **aktuális keresés** mezőbe. Ha a listában ki van bontva, az összes mentett keresések jelennek meg.

 ![Mentett keresések listája](./media/data-catalog-how-to-save-pin/03-list.png)

Tegye a következők egyikét:

* A keresés végrehajtásához válassza ki a listából egy mentett keresés.

* Mentett keresés felügyeleti beállítások listájának megtekintéséhez kattintson a keresés neve melletti lefelé mutató nyílra.

    ![Mentett keresések kezelésének lehetőségei](./media/data-catalog-how-to-save-pin/04-managing.png)

* Adjon meg egy új nevet a mentett keresésnek, jelölje be **átnevezése**. A search-definíció nem változott.

* Válassza ki a mentett keresés törlése a listáról, hogy **törlése**, és erősítse meg a törlést.

* A mentett keresésnek, mint az alapértelmezett keresési jel, jelölje be **mentése alapértelmezett**. Az Azure Data Catalog kezdőlapjáról "üres" keresést hajt végre, ha az alapértelmezett keresési hajtja végre. Emellett a keresés, amely az alapértelmezett keresési van megjelölve tetején megjelenik a **mentett keresések** listája.

### <a name="organizational-saved-searches"></a>Szervezeti mentett keresések
A szervezet minden felhasználójának mentheti a saját használatra keres. A Data Catalog rendszergazdái is mentheti a szervezet bármely felhasználója keres. Amikor a rendszergazdák a Keresés mentése, akkor áll rendelkezésre egy **megosztás a vállalaton belül** lehetőséget. Ez a beállítás megosztja a mentett keresés a szervezet minden felhasználója számára.

 ![Szervezeti mentett keresések](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Rögzített adategységek
Mentett keresések mentheti, és újra felhasználhatja a keresési definíciókat. Az adategységeket, a keresés által visszaadott, a katalógus módosítás tartalmát idővel változhatnak. Amikor adatobjektumok kitűzése explicit módon azonosíthatja az egyszerűbb elérését anélkül, hogy a keresés meghatározott adategységeket.

Adategység rögzítés rendkívül egyszerű. Adja hozzá az adategységhez a rögzített listájához, akkor egyszerűen kattintson a **PIN-kód** ikonra. Az ikonra az eszköz csempe a csempe nézetben, és a listanézet, az Azure Data Catalog-portálon a bal szélső oszlopban a sarkában jelenik meg.

![Az adategység Gombostű ikon](./media/data-catalog-how-to-save-pin/05-pinning.png)

Feloldja az adategység egyaránt könnyen érthető megjegyzésblokkok írására. Egyszerűen kattintson a **rögzítésének** ikonra kattintva váltsa át a beállításokat a kiválasztott eszköz.

![Az adategység rögzítés ikon](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A saját eszközök szakasz
A Data Catalog portál kezdőlapján tartalmaz egy **saját eszközök** szakaszt, amely megjeleníti a lényeges eszközök az aktuális felhasználónak. Ebben a szakaszban mindkét rögzített adategységek tartalmazza, és a mentett kereséseket.

![A saját eszközök szakaszban a kezdőlapon](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Összegzés
Az Azure Data Catalog képességeket biztosít, amelyek megkönnyítik a van szüksége, az adatforrások felderítését, így más szervezet tagja is hasznosítsa az adat- és több időt azt. Mentett keresések és a rögzített adatok eszközök az alábbi alapképességek hozhat létre, így a felhasználók könnyen megtalálják azokat az adatforrásokat, azok együttműködését ismételten.

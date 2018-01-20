---
title: "A keresések mentése, és adategységeket rögzítheti az Azure Data Catalog |} Microsoft Docs"
description: "Útmutató a cikk adatforrások és a későbbi használatra adategységeket mentéséhez az Azure Data Catalog képességei kiemelve."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: cced7b8253a45d990371d8f95fe36b580c2debbf
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Az Azure Data Catalog keres, és a PIN-kód adategységeket mentése
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog az adatforrás-felderítés képességeket biztosít. Gyorsan kereshet, és keresse meg az adatforrásokat, és megismerheti azok használatának célját katalógus szűrése megkönnyíti az elvégzendő a feladat a megfelelő adatok kereséséhez.

De mi történik, ha ugyanazokat az adatokat rendszeresen együttműködve kell? És mi történik, ha más felhasználók rendszeresen hozzájárulhatnak a Tudásbázis azonos adatforrások a katalógusban? Ezekben a helyzetekben nem kell azonos kereséseket ismételten ki lehet hatékony. Ez azért, ahol mentett keresés és a rögzített adatok eszközök segítségével.

## <a name="saved-searches"></a>Mentett keresések
A mentett kereséseket a Data Catalog az újrafelhasználható, felhasználókon keresési definícióját. Adja meg a keresés – többek között a keresési feltételek, a címkék és a többi szűrőt, és mentse. A mentett keresés definíciót a később vissza bármely az adategységekhez a keresési feltételeknek megfelelő újbóli futtatásával.

### <a name="create-a-saved-search"></a>A mentett keresés létrehozása
A mentett kereséseket létrehozásához tegye a következőket:
1. Az Azure Data Catalog-portálon a a **aktuális keresés** ablak, kattintson a **mentése**. 

    ![Aktuális keresési beállítások mentése hivatkozás](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Adja meg a keresési feltételek használja fel, és kattintson a kívánt **mentése**.

    ![Aktuális keresési beállítások mentett keresési neve](./media/data-catalog-how-to-save-pin/02-name.png)

3. Amikor a rendszer kéri, adjon meg egy nevet a mentett keresés. Adjon meg egy megfelelő, kifejező nevet és, amely leírja, hogy a keresés által visszaadott az adategységeket.

### <a name="manage-saved-searches"></a>Mentett keresések kezelése
Egy vagy több keresés mentése után a **mentett keresések** lehetőség alatt jelennek meg a **aktuális keresés** mezőbe. Amikor a listában ki van bontva, minden mentett keresések jelennek meg.

 ![Mentett keresések listája](./media/data-catalog-how-to-save-pin/03-list.png)

Tegye a következők egyikét:

* A keresés végrehajtásához a listában jelölje ki a mentett kereséseket.

* A mentett keresések beállítások listájának megtekintéséhez kattintson a keresés neve melletti lefelé mutató nyílra.

    ![Mentett keresések kezelésére vonatkozó beállítások](./media/data-catalog-how-to-save-pin/04-managing.png)

* Adjon meg egy új nevet a mentett keresésnek, jelölje be **átnevezése**. A keresési definíció nem változott.

* Távolítsa el a mentett kereséseket a listáról, jelölje be **törlése**, majd erősítse meg a törlést.

* A mentett keresés, mint az alapértelmezett keresési megjelölni, válassza ki a **mentése alapértelmezett**. Az Azure Data Catalog kezdőlapról "empty" keresést hajt végre, ha az alapértelmezett keresés végrehajtása. Ezenkívül, amely az alapértelmezett keresési van megjelölve, a keresés felső részén jelenik meg a **mentett keresések** listája.

### <a name="organizational-saved-searches"></a>Szervezeti mentett keresések
A szervezet minden felhasználója keresés saját használatra takaríthat meg. Katalógus-rendszergazdák is mentheti a szervezeten belüli összes felhasználók keres. Ha a rendszergazdák a Keresés mentése, hogy találkozik egy **megosztás a vállalaton belül** lehetőséget. Ezzel a beállítással osztja meg a szervezet összes felhasználója számára a mentett keresés.

 ![Szervezeti mentett keresések](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Rögzített adategységeket
Mentett keresések mentheti, és ismét felhasználni a keresési definíciókat. Az adategységeket a keresés által visszaadott a katalógus módosítás tartalmát idővel változhatnak. Adategységek rögzíti, amikor explicit módon azonosíthatja a könnyebb hozzáférés anélkül, hogy a keresés adott adategységeket.

Egy adategységet rögzítési egyszerű. Az adatok eszköz hozzáadása a rögzített listájához, akkor egyszerűen kattintson a **PIN-kód** ikonra. Az ikonra az eszköz csempe a mozaik elrendezés nézetben, és az Azure Data Catalog-portál a listanézet bal szélső oszlopában sarkában jelenik meg.

![Az adatok-eszköz PIN-kód ikon](./media/data-catalog-how-to-save-pin/05-pinning.png)

Egy adategységet feloldásával egyaránt egyszerű. Egyszerűen kattintson a **rögzítésének** ikonra kattintva átválthatja a beállítás a kijelölt objektumhoz.

![A adategységet rögzítésének ikon](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A saját eszközök szakasz
A Data Catalog portál kezdőlapján tartalmaz egy **saját eszközök** szakasz érdeklő eszközök megjeleníti az aktuális felhasználónak. Ez a szakasz mindkét rögzített eszközöket tartalmazza, és a mentett kereséseket.

![A saját eszközök szakaszban a kezdőlapon](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Összegzés
Az Azure Data Catalog biztosít képességeket kínál, amelyek révén könnyebben van szüksége, adatforrásokat fedezzenek fel, ezért más szervezet tagja is kevesebb időt töltenek az adatok és dolgozni vele több időt keres. Mentett keresések, és a rögzített adatok eszközök ezek alapképességek létrehozásához, így a felhasználók könnyen azonosíthassák, hogy az adatforrások, amelyek működnek a ismételten.

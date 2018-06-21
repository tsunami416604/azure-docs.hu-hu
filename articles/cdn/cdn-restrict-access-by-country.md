---
title: Azure CDN-tartalom ország korlátozása |} Microsoft Docs
description: Útmutató az Azure CDN-tartalom ország elérésének korlátozása a földrajzi-szűrés szolgáltatás használatával.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: v-deasim
ms.openlocfilehash: 661356aeb2369bc1bbddd6caee57b256dd9e1212
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285015"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Ország Azure CDN-tartalom korlátozása

## <a name="overview"></a>Áttekintés
Ha egy felhasználó alapértelmezés szerint a tartalmat igényel, a tartalom kiszolgált függetlenül a kérést a felhasználó helyét. Azonban bizonyos esetekben érdemes lehet a tartalmat az ország való hozzáférésének korlátozásához. Az a *földrajzi-szűrés* funkció, szabályokat hozhat létre a megadott elérési utak a CDN-végpont engedélyezi vagy letiltja a kijelölt országokban tartalmat.

> [!IMPORTANT]
> **Az Azure CDN Standard Microsoft** profilok nem támogatják az elérési út alapú földrajzi-szűrés.
> 

## <a name="standard-profiles"></a>Standard szintű profilok
A jelen szakaszban ismertetett eljárások vannak a **Azure CDN Standard Akamai** és **Azure CDN Standard verizon** csak profiljait. 

A **verizon Azure CDN Premium** -profilok kell használnia a **kezelése** portálhoz, és aktiválja a földrajzi-szűrés. További információkért lásd: [profilok Verizon Azure CDN Premium](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>A könyvtár elérési útjának megadása
A földrajzi-szűrés szolgáltatás eléréséhez, válassza ki a CDN-végpontot a portálon, majd válasszon **földrajzi-szűrés** beállítások a bal oldali menüből. 

![Standard Georedundáns szűrése](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Az a **ELÉRÉSI** adja meg a relatív elérési útját, amelyhez felhasználók engedélyez vagy megtagadta a hozzáférést. 

Alkalmazhat földrajzi-szűrés egy továbbítani az összes fájl perjel (/), vagy válasszon adott mappák ad meg a könyvtár elérési (például */pictures/*). Is alkalmazhat földrajzi-szűrés egyetlen fájlba (például */pictures/city.png*). Több szabály engedélyezett; Miután megadta a szabály, egy üres sort adhatja meg a következő szabály jelenik meg.

Például a következő könyvtár elérési útja szűrőket összes érvényes:   
*/*                                 
*/Photos/*     
*/Photos/Strasbourgban /*     
*/Photos/Strasbourg/City.png*

### <a name="define-the-type-of-action"></a>Adja meg a műveletet

Az a **művelet** listáról válassza ki **engedélyezése** vagy **blokk**: 

- **Engedélyezése**: csak az adott ország felhasználók jogosultak a kért rekurzív elérési úton található eszközök eléréséhez.

- **Blokk**: a megadott országokból felhasználó sem kap hozzáférést a kért rekurzív elérési úton található eszközökhöz. Ha nincs más országban szűrési beállítások vannak konfigurálva, hogy a helyen, majd a többi felhasználó hozzáférése engedélyezett lesz.

Például egy földrajzi-szűrési szabály letiltására, az elérési út */fényképek/Strasbourgban/* szűrése a következő fájlokat:     
*http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countries"></a>Adja meg a országok
Az a **ORSZÁGHÍVÓSZÁMOK** listára, válassza ki az elérési utat engedélyezése vagy blokkolása kívánt országok. 

Miután országok kijelölését, válassza ki **mentése** aktiválja az új földrajzi szűrési szabály. 

![Földrajzi szűrési szabályok](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A szabály törléséhez válassza ki azt a listából a a **földrajzi-szűrés** lapon, majd kattintson a **törlése**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Profilok Verizon Azure CDN Premium
A **Azure CDN Premium verizon** profilok, a felhasználói felület különböző földrajzi szűrési szabályt hoz létre, érték:

1. Az Azure CDN-profilt a felső menüben válassza ki a **kezelése**.

2. Válassza ki a Verizon portálról **HTTP nagy**, majd jelölje be **ország szerinti szűrés**.

    ![Standard Georedundáns szűrése](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Válassza ki **ország-szűrő hozzáadása**.

    A **lépés egy:** lap jelenik meg.

4. Adja meg a könyvtár elérési útja, válasszon **blokk** vagy **Hozzáadás**, majd jelölje be **következő**.

    A **lépés két:** lap jelenik meg. 

5. Jelöljön ki egy vagy több országban a listából, majd **Befejezés** a szabály aktiválásához. 
    
    Az új szabály megjelenik a táblázatban a a **ország szerinti szűrés** lap.

    ![Földrajzi szűrési szabályok](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ország szűrési szabályokat, kijelölése a szabály törli-e melletti Törlés ikonra vagy módosítható a Szerkesztés ikonra.

## <a name="considerations"></a>Megfontolandó szempontok
* Közvetlenül a földrajzi-szűrés konfigurációjának módosításai nem lépnek érvénybe:
   * A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
   * Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
   * A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
 
* Ez a funkció nem támogatja a helyettesítő karakterek (például *).

* A földrajzi-szűrés konfigurációja társított a következő relatív elérési út rekurzív módon alkalmazza.

* Csak egy szabály ugyanazt az relatív elérési utat is alkalmazható. Ez azt jelenti, hogy nem hozható létre több ország szűrőket, amelyek ugyanazt az relatív elérési utat. Azonban ország szűrő rekurzív, mert a mappa lehet több országban szűrőt. Más szóval a korábban konfigurált mappa almappája más országban szűrő rendelhetők.

* A földrajzi-szűrés szolgáltatás országhívó számokat használ, amelyből kérelmet engedélyezett vagy letiltott biztonságos könyvtár országok meghatározására. Bár Akamai és Verizon profilok támogatja az ugyanazon országhívó számokat a legtöbb, van néhány különbség. További információkért lásd: [Azure CDN országhívószámok](https://msdn.microsoft.com/library/mt761717.aspx). 


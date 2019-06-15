---
title: Az Azure CDN-tartalom korlátozása ország/régió szerint |} A Microsoft Docs
description: Ismerje meg, hogyan való hozzáférés korlátozása ország/régió szerint az Azure CDN-tartalom a geoszűrési szolgáltatás használatával.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 083d8f66a73471548c812e27325e1ec69ad5c45c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869580"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Az Azure CDN-tartalom korlátozása ország/régió szerint

## <a name="overview"></a>Áttekintés
Amikor egy felhasználó a tartalmat, alapértelmezés szerint, a tartalomkézbesítés a felhasználó a kérés a helytől függetlenül. Azonban bizonyos esetekben előfordulhat, hogy szeretné a tartalmakhoz való hozzáférés korlátozása ország/régió szerint. Az a *geoszűrési* funkció, hozhat létre szabályokat meghatározott elérési utakra a CDN-végponton engedélyezheti vagy tilthatja le a tartalmat a kiválasztott országban és régióban.

> [!IMPORTANT]
> **Az Azure CDN Standard a Microsoft** profilok nem támogatják a-alapú földrajzi szűrés.
> 

## <a name="standard-profiles"></a>Standard szintű profilok
Ebben a szakaszban bemutatott eljárások a rendszer **Azure CDN Akamai Standard** és **Azure CDN Standard verizon** csak profilokat. 

A **verizon Azure CDN Premium** profilok kell használnia a **kezelés** portál geoszűrési aktiválásához. További információkért lásd: [Azure CDN Premiumhoz a Verizontól profilok](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>A könyvtár elérési útjának megadása
A geoszűrési szolgáltatás eléréséhez, válassza ki a CDN-végpontra a portálon, majd jelölje ki **geoszűrési** beállításaiban a bal oldali menüben. 

![A földrajzi szűrés standard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Az a **elérési út** adja meg a relatív elérési útját, amelyhez felhasználók engedélyezni vagy megtagadja a hozzáférést. 

Alkalmazhatja a földrajzi szűrés egy előre az összes fájl perjel (/), vagy válassza ki a meghatározott mappákról elérési utak megadásával (például */képek/japán.png*). Is alkalmazhat a földrajzi szűrés egyetlen fájlba (pl. */pictures/city.png*). Több engedélyezett; Miután megadta a szabályt, megjelenik egy üres sor adhatja meg a következő szabályt.

Ha például a következő könyvtár elérési útja szűrők összes érvényes:   
*/*                                 
*/Photos/*      
*/Photos/Strasbourg/*      
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Adja meg a művelet típusát

Az a **művelet** listáról válassza ki **engedélyezése** vagy **blokk**: 

- **Lehetővé teszi**: Csak a megadott országokból/régiókból származó felhasználók férhetnek hozzá a kért rekurzív elérési úton található eszközöket.

- **Blokk**: A megadott országokból/régiókból származó hozzáférés nem engedélyezett a rekurzív elérési úton található a kért eszközökhöz. Ha nincs más ország/régió szűrési beállítások vannak konfigurálva, az adott hely, majd a többi felhasználó hozzáférése engedélyezett lesz.

Például a földrajzi szűrés szabály blokkolja-e az elérési út */fényképek/Strasbourgban/* szűri a következő fájlokat:     
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Adja meg az országokban vagy régiókban
Az a **ORSZÁGKÓDOK** listájához, válassza ki, hogy az elérési út engedélyezni vagy letiltani kívánt országok vagy régiók. 

Után az országok/régiók kiválasztása után, jelölje be az **mentése** az új geoszűrés szabály aktiválásához. 

![Geoszűrési szabályok](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A szabály törléséhez válassza ki azt a listából a a **geoszűrési** lapon, majd kattintson a **törlése**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Az Azure CDN Premiumhoz a Verizontól profilok
A **az Azure CDN Premiumhoz a Verizontól** profilokat, a felhasználói felület különböző geoszűrési szabályok létrehozásához van:

1. Az Azure CDN-profil, a felső menüben válassza ki a **kezelés**.

2. Válassza ki a Verizon portálról **HTTP nagy**, majd **ország szerinti szűrés**.

    ![A földrajzi szűrés standard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Válassza ki **szűrés ország**.

    A **lépés egy:** lap jelenik meg.

4. Adja meg a könyvtár elérési útja, és válassza ki **blokk** vagy **Hozzáadás**, majd **tovább**.

    A **két lépés:** lap jelenik meg. 

5. Egy vagy több országok/régiók kiválasztása a listából, majd válassza ki **Befejezés** a szabály aktiválásához. 
    
    A tábla megjelenik az új szabályt a **ország szerinti szűrés** lapot.

    ![Geoszűrési szabályok](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A táblázatban ország/régió szűrési szabályokat válassza ki a mellett egy szabályt, amely törli a Törlés ikonra, vagy módosítsa a Szerkesztés ikonra.

## <a name="considerations"></a>Megfontolandó szempontok
* Azonnal a geoszűrési konfigurációjának módosításai nem lépnek érvénybe:
   * A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
   * Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
   * A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
 
* Ez a funkció nem támogatja a helyettesítő karakterek (például *).

* A relatív elérési út társított geoszűrési konfigurációs az elérési út rekurzív módon alkalmazza.

* Csak egy szabályt a relatív elérési útját is alkalmazható. Több ország/régió szűrők, amelyek azonos relatív elérési útját, nem hozható létre. Azonban mivel ország/régió szűrők rekurzív, a mappa lehet több ország/régió szűrőt. Más szóval egy korábban konfigurált mappa egyik almappájára egy másik országból/régióból szűrőt is hozzárendelhető.

* A geoszűrési szolgáltatás országhívó számokat használ az országokban vagy régiókban, amelyről kérelem engedélyezett vagy letiltott egy védett könyvtárban meghatározására. Bár az Akamai és a Verizon profilok támogatása az azonos országhívó számokat a legtöbb, néhány különbségek vannak. További információkért lásd: [Azure CDN országkódok](/previous-versions/azure/mt761717(v=azure.100)). 


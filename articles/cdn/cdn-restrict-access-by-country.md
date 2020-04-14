---
title: Az Azure CDN-tartalom korlátozása ország/régió szerint | Microsoft dokumentumok
description: Ismerje meg, hogyan korlátozhatja a hozzáférést ország/régió az Azure CDN-tartalomhoz a geo-szűrési funkció használatával.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: 7ae7224efdaa281106dfbe2118ab0092c8284c6e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260156"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Az Azure CDN-tartalom korlátozása ország/régió szerint

## <a name="overview"></a>Áttekintés
Amikor egy felhasználó kéri a tartalmat, alapértelmezés szerint a rendszer a kérést küldő felhasználó helyétől függetlenül kézbesíti a tartalmat. Bizonyos esetekben azonban ország/régió szerint korlátozhatja a tartalomhoz való hozzáférést. A *geoszűrési* funkcióval szabályokat hozhat létre a CDN-végpont adott elérési útjaira, hogy engedélyezze vagy letiltsa a tartalmat a kiválasztott országokban/régiókban.

> [!IMPORTANT]
> **A Microsoft-profilokból származó Azure CDN Standard** nem támogatja az elérési út alapú geoszűrést.
> 

## <a name="standard-profiles"></a>Szabványos profilok
Ebben a szakaszban az **Azure CDN Standard az Akamai** és **az Azure CDN Standard** csak a Verizon-profilok. 

A **Verizon-profilokból származó Azure CDN Premium** esetén a Geoszűrés aktiválásához a **Kezelés** portált kell használnia. További információ: [Azure CDN Premium from Verizon profiles](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>A könyvtár elérési útjának meghatározása
A geoszűrési funkció eléréséhez jelölje ki a CDN-végpontot a portálon belül, majd válassza a bal oldali menü BEÁLLÍTÁSOK csoportbeállítások **parancsát.** 

![Geoszűrő szabvány](./media/cdn-filtering/cdn-geo-filtering-standard.png)

A **PATH** mezőben adja meg annak a helynek a relatív elérési útját, ahhoz a helyhez, ahhoz a helyhez, ahhoz a helyhez, ahhoz a helyhez, ahhoz a helyhez, ahhoz a helyhez, ahhoz a helyhez, ahhoz a helyhez, ahhoz a helyhez 

A címtár elérési útjainak megadásával (például */pictures/*) alkalmazhatja az összes fájlra vonatkozó geoszűrést, vagy kiválaszthatja az adott mappákat. Egyetlen fájlra (például */pictures/city.png)* is alkalmazhat geoszűrést. Több szabály is megengedett; a szabály megadása után egy üres sor jelenik meg a következő szabály beírásához.

Az alábbi könyvtárelérési útszűrők mindegyike például érvényes:   
*/*                                 
*/Fotók/*     
*/Fotók/Strasbourg/*     
*/Fotók/Strasbourg/város.png*

### <a name="define-the-type-of-action"></a>A művelet típusának meghatározása

A **MŰVELET** listában válassza az **Engedélyezés** vagy **a Letiltás**lehetőséget: 

- **Engedélyezés:** Csak a megadott országokból/régiókból származó felhasználók férhetnek hozzá a rekurzív útvonalról kért eszközökhöz.

- **Letiltás:** A megadott országokból/régiókból származó felhasználók nem férhetnek hozzá a rekurzív útvonalról kért eszközökhöz. Ha nincs más ország-/régiószűrési beállítás konfigurálva az adott helyhez, akkor az összes többi felhasználó hozzáférhet.

Például egy földrajzi szűrési szabály az elérési út *blokkolására /Fotók/Strasbourg/* a következő fájlokat szűri:     
*\//http:\<végpont>.azureedge.net/Photos/Strasbourg/1000.jpg*
*\//\<http: végpont>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Az országok/régiók meghatározása
Az **ORSZÁGKÓDOK** listában válassza ki azokat az országokat/régiókat, amelyeket le szeretne tiltani vagy engedélyezni szeretné az elérési útnak. 

Miután befejezte az országok/régiók kiválasztását, válassza a **Mentés** lehetőséget az új geoszűrési szabály aktiválásához. 

![Geoszűrési szabályok](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Szabály törléséhez jelölje ki a jelet a **Geoszűrés** lap listájából, majd válassza a **Törlés gombot.**

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium a Verizon-profilokból
**A Verizon-profilokból származó Azure CDN Premium esetében** a geoszűrési szabály létrehozásához szükséges felhasználói felület eltérő:

1. Az Azure CDN-profiljának felső menüjében válassza a **Kezelés lehetőséget.**

2. A Verizon portálon válassza a **HTTP Large**lehetőséget, majd az **Országszűrés**lehetőséget.

    ![Geoszűrő szabvány](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Válassza **az Országszűrő hozzáadása lehetőséget.**

    Az **első lépés:** oldal jelenik meg.

4. Írja be a könyvtár elérési útját, válassza a **Blokkolás** vagy **a Hozzáadás**lehetőséget, majd a **Tovább**gombot.

    A **második lépés:** oldal jelenik meg. 

5. Jelöljön ki egy vagy több országot/régiót a listából, majd a **Befejezés** gombra a szabály aktiválásához. 
    
    Az új szabály megjelenik az **Országszűrés** lap táblázatában.

    ![Geoszűrési szabályok](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az ország-/területszűrési szabályok táblázatban jelölje ki a szabály melletti törlés ikont a törléshez, vagy a szerkesztésikont a módosításhoz.

## <a name="considerations"></a>Megfontolandó szempontok
* A geoszűrési konfiguráció módosításai nem lépnek azonnal érvénybe:
   * A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
   * Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
   * A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
 
* Ez a szolgáltatás nem támogatja a helyettesítő karaktereket (például *).

* A relatív elérési úthoz társított geoszűrési konfigurációt rekurzív módon alkalmazza a rendszer az adott elérési útra.

* Ugyanarra a relatív elérési útra csak egy szabály alkalmazható. Ez azt, hogy nem hozhat létre több ország-/régiószűrőt, amelyek ugyanarra a relatív elérési útra mutatnak. Mivel azonban az ország-/régiószűrők rekurzívak, egy mappa több ország-/régiószűrővel is rendelkezhet. Más szóval egy korábban konfigurált mappa almappájához másik ország-/régiószűrő rendelhető.

* A geoszűrési szolgáltatás országkódok at használ, hogy meghatározza azokat az országokat/régiókat, amelyekről egy biztonságos címtárra vonatkozó kérelem engedélyezett vagy le van tiltva. Bár az Akamai és a Verizon profilok támogatják a legtöbb azonos országkódok, van néhány különbség. További információ: [Azure CDN országkódok](/previous-versions/azure/mt761717(v=azure.100)). 


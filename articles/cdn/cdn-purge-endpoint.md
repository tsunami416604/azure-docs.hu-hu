---
title: Azure CDN-végpont kiürítése | Microsoft dokumentumok
description: Ismerje meg, hogyan törölheti az összes gyorsítótárazott tartalmat egy Azure CDN-végpontról.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: magattus
ms.openlocfilehash: 1bfbc1b730811e1111a08a957db3a747f90fb587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74546202"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Azure CDN-végpont kiürítése
## <a name="overview"></a>Áttekintés
Az Azure CDN peremhálózati csomópontjai gyorsítótárazza az eszközöket, amíg az eszköz az élő (TTL) lejár.  Miután az eszköz TTL lejár, amikor egy ügyfél kéri az eszközt a peremhálózati csomópont, a peremhálózati csomópont lekéri az eszköz egy új frissített példányát az ügyfélkérelem kiszolgálására és tárolja a gyorsítótár frissítését.

Az ajánlott eljárás annak biztosítása érdekében, hogy a felhasználók mindig megkapják az eszközök legújabb példányát, hogy minden egyes frissítéshez verziószámba adják az eszközöket, és új URL-ként teszik közzé őket.  A CDN azonnal lekéri az új eszközöket a következő ügyfélkérelmekhez.  Előfordulhat, hogy szeretné kiüríteni a gyorsítótárazott tartalmat az összes peremhálózati csomópontról, és mindet kényszeríteni az új frissített eszközök lekéréséhez.  Ennek oka lehet a webalkalmazás frissítése, vagy a helytelen adatokat tartalmazó eszközök gyors frissítése.

> [!TIP]
> Vegye figyelembe, hogy a tisztítás csak törli a cdn peremhálózati kiszolgálók gyorsítótárazott tartalmát.  Az alsóbb rétegbeli gyorsítótárak, például a proxykiszolgálók és a helyi böngészőgyorsítótárak továbbra is tárolhatják a fájl gyorsítótárazott másolatát.  Ezt fontos megjegyezni, amikor beállítja egy fájl élethez való idejét.  Kényszerítheti az alsóbb rétegbeli ügyfelet, hogy kérje a fájl legújabb verzióját, ha minden frissítéskor egyedi nevet ad neki, vagy kihasználhatja a [lekérdezési karakterlánc-gyorsítótárazást.](cdn-query-string.md)  
> 
> 

Ez az oktatóanyag végigvezeti az eszközök tisztításán egy végpont összes peremcsomópontjáról.

## <a name="walkthrough"></a>Útmutatás
1. Az [Azure Portalon](https://portal.azure.com)keresse meg a cdn-profilt, amely tartalmazza a végpontot szeretné megtisztítani.
2. A CDN-profilpanelen kattintson a törlés gombra.
   
    ![CDN profilpanel](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Kinyílik a Megtisztulás penge.
   
    ![CDN tisztító panel](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. A Kiürítés panelen válassza ki azt a szolgáltatáscímet, amelyet meg szeretne tisztítani az URL-legördülő legördülő címből.
   
    ![Űrlap kiürítése](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > A Kiürítés panelhez a CDN végpont panel **Tisztítás** gombjára kattintva is megkaphatja a kiürítési gombot.  Ebben az esetben az **URL-mező** előre ki lesz töltve az adott végpont szolgáltatáscímével.
   > 
   > 
4. Válassza ki, hogy milyen eszközöket szeretne kiüríteni a peremhálózati csomópontokról.  Ha törölni szeretné az összes eszköz törlését, kattintson az **Összes törlése** jelölőnégyzetre.  Ellenkező esetben írja be a **kiüríteni** kívánt eszközök elérési útját a Görbe mezőbe. Az alábbi formátumok at támogatja az elérési út.
    1. **Egyetlen URL-kiürítés**: Az egyes eszközök kiürítése a teljes URL megadásával, a fájlkiterjesztéssel vagy anélkül, pl.;`/pictures/strasbourg.png``/pictures/strasbourg`
    2. **Helyettesítő karakter kiürítése**\*: Csillag ( ) használható helyettesítő karakterként. Ürítse ki az összes mappát, almappát és fájlt egy végpont `/*` alatt az elérési úton, vagy ürítse ki az összes almappát és fájlt egy adott mappa alatt a mappa , majd `/*`a , például .`/pictures/*`  Vegye figyelembe, hogy a helyettesítő karakteres kiürítése jelenleg nem támogatja az Akamai Azure CDN-jét. 
    3. **Gyökértartomány kiürítése**: Ürítse ki a végpont gyökerét az elérési úton lévő "/" kapcsolóval.
   
   > [!TIP]
   > A kiürítéshez meg kell adni az elérési utakat, és relatív URL-címnek kell lenniük, amely megfelel a következő [reguláris kifejezésnek.](/dotnet/standard/base-types/regular-expression-language-quick-reference) **Kiürítése az összes** és **a helyettesítő karakter kiürítése** nem támogatja **az Azure CDN-től Akamai** jelenleg.
   > > Egyetlen URL-cím kiürítése`@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Lekérdezési karakterlánc`@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Helyettesítő karakter `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`kiürítése . 
   > 
   > A szöveg bevitele után további **szövegmezők** jelennek meg, amelyek lehetővé teszik több eszköz listájának összeállítását.  A három pont (...) gombra kattintva törölheti az eszközöket a listából.
   > 
5. Kattintson a **Kiürítés** gombra.
   
    ![Kiürítés gomb](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> A kiürítési kérelmek feldolgozása körülbelül 10 percet vesz igénybe a **Microsoft Azure CDN-jével,** körülbelül 2 percig a Verizon (standard és prémium) **Azure CDN-jével,** és körülbelül 10 másodpercet az **Akamai Azure CDN-jével.**  Az Azure CDN legfeljebb 50 egyidejű kiürítési kérelmek egy adott időpontban a profil szintjén. 
> 
> 

## <a name="see-also"></a>Lásd még
* [Eszközök előzetes betöltése Azure CDN-végponton](cdn-preload-endpoint.md)
* [Azure CDN REST API-hivatkozás – Végpont kiürítése vagy előzetes betöltése](/rest/api/cdn/endpoints)


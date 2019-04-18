---
title: Az Azure CDN-végpont végleges törlése |} A Microsoft Docs
description: Ismerje meg, hogy minden gyorsítótárazott tartalom kiürítése egy Azure CDN-végpontról.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: magattus
ms.openlocfilehash: 76e7817be81a97c8d1a0b9ca2fea8378c3c733e1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916479"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Az Azure CDN-végpont végleges törlése
## <a name="overview"></a>Áttekintés
Az Azure CDN-határcsomópontra eszközök gyorsítótárazzák, amíg az eszköz ideje-élettartam (TTL) le nem jár.  Az eszköz TTL lejárata után Ha egy ügyfél az eszköz kér az élcsomóponthoz, az élcsomópont fogja lekérni frissített másolatát az eszköz az ügyfél kérelem kiszolgálására, és a tároló a gyorsítótár frissítése.

Az ajánlott eljárás, hogy a felhasználók mindig a legfrissebb verzióját a eszközöket szerezze be az eszközök frissítése a verzióra, és közzéteheti őket az új URL-címként.  CDN azonnal lekéri az új eszközök a következő az ügyféli kérelmek részére.  Néha érdemes minden élcsomópontokból gyorsítótárazott tartalom végleges, és kényszerítheti az összeset lekérni frissített új eszközök.  Ez a webes alkalmazások, illetve gyorsan helytelen adatokat tartalmazó frissítési eszközök frissítések oka lehet.

> [!TIP]
> Vegye figyelembe, hogy csak végleges törlése törli a gyorsítótárazott tartalom a CDN peremhálózati kiszolgálókon.  Alsóbb rétegbeli gyorsítótárakat, például a proxy-kiszolgálók és a helyi böngésző gyorsítótárát, továbbra is előfordulhat, hogy a fájl gyorsítótárazott másolatának tárolására.  Fontos megjegyezni, ez a fájl beállításakor time-to-live.  Kényszerítheti a kérelem a fájl legújabb verzióját, így azt egy egyedi nevet minden alkalommal, amikor a frissítést, vagy kihasználásával az alsóbb rétegbeli ügyfele [lekérdezési karakterláncok gyorsítótárazása](cdn-query-string.md).  
> 
> 

Ez az oktatóanyag végigvezeti a végpont összes élcsomópontokból eszközök törlése.

## <a name="walkthrough"></a>Útmutatás
1. Az a [az Azure Portal](https://portal.azure.com), tallózással keresse meg a CDN-profilt, amely tartalmazza a véglegesen törölni kívánt végpontot.
2. A CDN-profil panelje a végleges törlés gombra.
   
    ![CDN-profil panelje](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    A végleges törlés panel nyílik meg.
   
    ![A CDN végleges törlése panelen](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. A végleges törlés panelen válassza ki a címét az URL-cím legördülő listából véglegesen törölni kívánt.
   
    ![Képernyő törlése](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Letöltheti a végleges törlés panelre kattintva az **kiürítése** a CDN-végpont panelről gombjára.  Ebben az esetben a **URL-cím** mező ki van töltve az, hogy a végpont megadott szolgáltatás címe lesz.
   > 
   > 
4. Válassza ki a élcsomópontokból véglegesen törölni kívánt milyen eszközöket.  Ha az összes erőforrás törléséhez, kattintson a **összes kiürítése** jelölőnégyzetet.  Ellenkező esetben adja meg a véglegesen törölni kívánt minden egyes eszköz elérési útját a **elérési út** szövegmezőbe. Az elérési út által támogatott formátumok alatt.
    1. **Egyetlen URL-cím kiürítése**: A teljes URL-cím megadásával, vagy a fájl kiterjesztése például anélkül eszköz egyedi kiürítése`/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **A helyettesítő karakteres kiürítés**: Csillag (\*) helyettesítő karakter is használható. Összes mappa, almappák és fájlok alatt a végpont végleges törlése `/*` végleges törlés vagy az elérési utat összes almappák és fájlok egy adott mappában a mappa megadásával követ `/*`, például`/pictures/*`.  Vegye figyelembe, hogy a helyettesítő karakteres kiürítés jelenleg nem támogatott az Akamai Azure CDN által. 
    3. **Legfelső szintű tartomány kiürítése**: Törölje a végpontot az "/" az elérési út gyökerében.
   
   > [!TIP]
   > Elérési utak meg kell adni a végleges törlés és a egy relatív URL-cím a következő igazodó kell lennie [reguláris kifejezés](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Összes kiürítése** és **helyettesítő karakteres kiürítés** által nem támogatott **Akamai Azure CDN** jelenleg.
   > > Egyetlen URL-cím végleges törlése `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Lekérdezési karakterlánc `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > A helyettesítő karakteres kiürítés `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > További **elérési út** szövegmezőből adategységek listáját létrehozását teszik lehetővé a szöveg beírása után jelenik meg.  Eszközök a három pontra (...) gombra kattintva törölheti a listából.
   > 
5. Kattintson a **kiürítése** gombra.
   
    ![Végleges törlés gomb](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Végleges törlés kérelmek igénybe körülbelül 2-3 perc alatt dolgozza fel a **verizon Azure CDN** (standard és prémium szintű), és körülbelül 7 perc alatt a **Akamai Azure CDN**.  Az Azure CDN esetében 50 egyidejű kérések kiürítése egy adott időpontban a profil szintjén. 
> 
> 

## <a name="see-also"></a>Lásd még
* [Eszközök előzetes betöltése Azure CDN-végponton](cdn-preload-endpoint.md)
* [Az Azure CDN – REST API-referencia - végleges törlése, vagy a végpont előzetes betöltése](/rest/api/cdn/endpoints)


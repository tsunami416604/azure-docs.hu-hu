---
title: "Az Azure CDN-végpont törlése |} Microsoft Docs"
description: "Ismerje meg, hogy minden gyorsítótárazott tartalom kiürítése az Azure CDN-végponton."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b035c232bb58d653960190d4974cc3789d55a51d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="purge-an-azure-cdn-endpoint"></a>Az Azure CDN-végpont törlése
## <a name="overview"></a>Áttekintés
Azure CDN peremhálózati csomópontok gyorsítótárazni fog eszközök, amíg az eszköz ideje-élettartam (TTL) lejár.  Az eszköz TTL lejárata után Ha egy ügyfél az eszköz kér az élcsomóponthoz, élcsomópont be fogja olvasni az eszköz az ügyfélkérés kiszolgálására frissített új példányát, és a tároló a gyorsítótár frissítése.

Győződjön meg arról, hogy a felhasználók mindig a legfrissebb verzióját a eszközöket szerezze be az ajánlott eljárás az objektumok minden egyes frissítés verzióra, és új URL-címeket is közzé őket.  CDN azonnal be fogja olvasni az új eszközök a következő ügyfélkéréseket.  Egyes esetekben előfordulhat, hogy kívánja kiüríteni a gyorsítótárazott tartalom peremhálózati összes csomópontjának, és annak kikényszerítéséhez őket az összes új és frissített eszközök beolvasása.  Ennek oka a webes alkalmazás, illetve gyorsan helytelen adatokat tartalmazó frissítési eszközök frissítések lehet.

> [!TIP]
> Vegye figyelembe, hogy csak kiürítése törli a gyorsítótárazott tartalmat a CDN peremhálózati kiszolgálóinak.  Bármely alárendelt gyorsítótárak, például proxykiszolgáló, és helyi böngésző gyorsítótárát, előfordulhat, hogy továbbra is a gyorsítótárazott másolatának tárolására szolgál a fájl.  Fontos, ha úgy állítja be a fájl élettartamát idő figyelembe kell venni.  Beállíthatja, hogy adjon neki egy egyedi nevet minden alkalommal, amikor végezzen frissítést, vagy ha kihasználja a kérést a fájl legújabb verzióját az alsóbb rétegbeli ügyfele [lekérdezési karakterláncok gyorsítótárazása](cdn-query-string.md).  
> 
> 

Ez az oktatóanyag bemutatja, hogyan eszközök kiürítése a végpont összes peremhálózati csomópontjából.

## <a name="walkthrough"></a>Útmutatás
1. Az a [Azure Portal](https://portal.azure.com), keresse meg a CDN-profilt, amely tartalmazza a kiüríteni kívánt végpont.
2. A CDN-profil panelje a végleges törlése gombra.
   
    ![CDN-profil panelje](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Ekkor megnyílik a kiürítési panel.
   
    ![CDN-kiürítési panelje](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. A kiürítési panelen válassza ki a szolgáltatás címet kívánja kiüríteni a URL-cím legördülő listából.
   
    ![Űrlap kiürítése](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Letöltheti a kiürítési paneljére kattint a **kiürítése** a CDN-végpont panelje gombjára.  Ebben az esetben a **URL-cím** mező ki van töltve, hogy az adott végponti szolgáltatás címével lesz.
   > 
   > 
4. Válassza ki milyen eszközöket kívánja kiüríteni peremhálózati csomópontjából.  Ha törli az összes eszközt, kattintson a **összes** jelölőnégyzetet.  Ellenkező esetben adja meg az elérési útját minden egyes a kiüríteni kívánt eszközt a **elérési** szövegmező. Az elérési út által támogatott formátumok alatt.
    1. **Egyetlen URL-cím kiürítése**: a teljes URL-címet, vagy a fájl kiterjesztése például anélkül megadásával eszköz egyedi kiürítése`/pictures/strasbourg.png`;`/pictures/strasbourg`
    2. **Helyettesítő karakteres kiürítés**: csillag (\*) helyettesítő karakter is használható. Törli az összes mappa, almappák és fájlok a végpont `/*` kiürítése vagy az elérési utat minden almappák és fájlok egy adott mappában a mappa megadásával követ `/*`, például`/pictures/*`.  Vegye figyelembe, hogy helyettesítő karakteres kiürítés nem támogatott Akamai Azure CDN által jelenleg. 
    3. **Legfelső szintű tartomány kiürítése**: törli a végpont a "/" elérési gyökérmappájában.
   
   > [!TIP]
   > Elérési utak meg kell adni a kiürítési, és egy relatív URL-cím a következő illeszkedő kell [reguláris kifejezés](https://msdn.microsoft.com/library/az24scfc.aspx). **Összes** és **helyettesítő karakteres kiürítés** nem támogatja a **Akamai Azure CDN** jelenleg.
   > > Egyetlen URL-cím kiürítése`@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Lekérdezési karakterlánc`@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Helyettesítő karakteres kiürítés `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > További **elérési** szövegmezők lehetővé teszi több eszközök listájának összeállítása szöveg megadása után jelenik meg.  Eszközök a három ponttal (…) gombra kattintva törölheti a listából.
   > 
5. Kattintson a **kiürítése** gombra.
   
    ![Kiürítése gomb](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Kiürítési kérelmeket feldolgozni a körülbelül 2-3 percet vehet igénybe **Azure CDN Verizon** (Standard és prémium), és körülbelül 7 perc **Akamai Azure CDN**.  Az Azure CDN rendelkezik maximális száma 50 egyidejű kérelmek kiüríteni egy adott időpontban. 
> 
> 

## <a name="see-also"></a>Lásd még:
* [Eszközök előzetes betöltése Azure CDN-végponton](cdn-preload-endpoint.md)
* [Az Azure CDN REST API-referencia - kiürítése, vagy a végpont előzetes betöltése](https://msdn.microsoft.com/library/mt634451.aspx)


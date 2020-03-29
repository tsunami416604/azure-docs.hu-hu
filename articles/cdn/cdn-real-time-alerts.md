---
title: Az Azure CDN valós idejű riasztásai | Microsoft dokumentumok
description: Valós idejű riasztások a Microsoft Azure CDN-ben. A valós idejű riasztások értesítéseket nyújtanak a CDN-profil végpontjainak teljesítményéről.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 4b8cbc27757cf6c321ea4b3c27720a129aa27c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593480"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Valós idejű riasztások a Microsoft Azure CDN-ben
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a valós idejű riasztásokat a Microsoft Azure CDN-ben. Ez a funkció valós idejű értesítéseket biztosít a CDN-profil végpontjainak teljesítményéről.  A levelezési vagy HTTP-riasztásokat a következők alapján állíthatja be:

* Sávszélesség
* Állapotkódok
* Gyorsítótár állapotai
* Kapcsolatok

## <a name="creating-a-real-time-alert"></a>Valós idejű riasztás létrehozása
1. Az [Azure Portalon](https://portal.azure.com)keresse meg a CDN-profilját.
   
    ![CDN-profil](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. A CDN-profilpanelen kattintson a **Kezelés** gombra.
   
    ![CDN-profilkezelés gomb](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
3. Mutasson az **Analytics** fülre, majd mutasson a **Valós idejű statisztika** úszó panelre.  Kattintson a **Valós idejű riasztások gombra.**
   
    ![CDN felügyeleti portál](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Megjelenik a meglévő riasztási konfigurációk listája (ha van ilyen).
4. Kattintson a **Riasztás hozzáadása** gombra.
   
    ![Riasztás hozzáadása gomb](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Megjelenik egy új riasztás létrehozására szolgáló űrlap.
   
    ![Új riasztási űrlap](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Ha azt szeretné, hogy ez a riasztás aktív legyen, amikor a **Mentés gombra**kattint, jelölje be az **Engedélyezett riasztás** jelölőnégyzetet.
6. A **Név** mezőben adja meg a riasztás leíró nevét.
7. Az **Adathordozó típusa** legördülő menüben válassza a **HTTP Large Object (Nagy méretű objektum) lehetőséget.**
   
    ![Médiatípus a kijelölt HTTP Nagy objektummal](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > A **HTTP Large Object elemet** **adathordozó-típusként kell kijelölnie.**  A többi választási lehetőséget az Azure CDN nem használja **a Verizontól.**  Ha nem sikerül kiválasztani a **HTTP Large Object objektumot,** a riasztás soha nem aktiválódik.
   > 
   > 
8. Hozzon létre egy figyelendő **kifejezést** **egy Metrika**, **Operátor**és **Eseményindító érték kiválasztásával.**
   
   * A **Metrika**mezőben válassza ki a figyelni kívánt feltétel típusát.  **Sávszélesség Mbps** az összeg a sávszélesség-használat megabit másodpercenként.  **Az összes kapcsolat** a peremhálózati kiszolgálóinkkal létesített egyidejű HTTP-kapcsolatok száma.  A gyorsítótár különböző állapotainak és állapotkódjainak definícióit az [Azure CDN gyorsítótárállapot-kódjai](/previous-versions/azure/mt759237(v=azure.100)) és az [Azure CDN HTTP-állapotkódok című témakörben található.](/previous-versions/azure/mt759238(v=azure.100))
   * **Operátor** az a matematikai operátor, amely létrehozza a metrika és az eseményindító értéke közötti kapcsolatot.
   * **Az eseményindító értéke** az a küszöbérték, amelyet az értesítés elküldése előtt teljesíteni kell.
     
     A következő példában a létrehozott kifejezés azt jelzi, hogy értesítést küld a rendszer, ha a 404-es állapotkódok száma 25-nél nagyobb.
     
     ![Valós idejű riasztási mintakifejezés](./media/cdn-real-time-alerts/cdn-expression.png)
9. Az **Intervallum mezőbe**írja be, hogy milyen gyakran szeretné kiértékelni a kifejezést.
10. A **Beírás legördülő** menüben válassza ki, hogy mikor szeretne értesítést kapni, ha a kifejezés igaz.
    
    * **A Feltétel indítása** azt jelzi, hogy a rendszer értesítést küld a megadott feltétel első észlelésekor.
    * **A Feltétel vége** azt jelzi, hogy a rendszer értesítést küld, ha a megadott feltétel már nem észlelhető. Ez az értesítés csak akkor indítható el, ha a hálózatfigyelő rendszerünk észlelte a megadott feltétel előfordulását.
    * **A Folyamatos** azt jelzi, hogy a rendszer minden alkalommal értesítést küld, amikor a hálózatfigyelő rendszer észleli a megadott feltételt. Ne feledje, hogy a hálózatfigyelő rendszer csak egyszer ellenőrzi a megadott feltételt.
    * **A Feltétel kezdete és vége** azt jelzi, hogy a rendszer értesítést küld a megadott feltétel első észlelésekor, majd akkor is, ha a rendszer a feltételt már nem észleli.
1. Ha e-mailben szeretne értesítéseket kapni, jelölje be az **Értesítés e-mailben** jelölőnégyzetet.  
    
    ![Értesítés e-mailben űrlapon](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    A **Címzett** mezőbe írja be azt az e-mail címet, amelyet az értesítések elküldésére szeretne küldeni. A **Tárgy** és **a Törzs**mezőben az alapértelmezettet hagyhatja, vagy testreszabhatja az üzenetet az Elérhető **kulcsszavak** lista segítségével, hogy az üzenet küldésekor dinamikusan szúrjon be riasztási adatokat.
    
    > [!NOTE]
    > Az e-mail értesítést a **Test Notification** gombra kattintva tesztelheti, de csak a riasztáskonfiguráció mentése után.
    > 
    > 
12. Ha azt szeretné, hogy értesítések et küldjön egy webkiszolgálóra, jelölje be az **Értesítés HTTP-közzétételkor** jelölőnégyzetet.
    
    ![Értesítés HTTP-bejegyzéssel űrlap](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Az **URL-cím** mezőbe írja be azt az URL-címet, amelyben a HTTP-üzenetet közzé szeretné tenni. A **Fejlécek** mezőbe írja be a kérelemben elküldendő HTTP-fejléceket.  A **Törzs**csoport ban az üzenetet az **Elérhető kulcsszavak** lista segítségével testreszabhatja, hogy az üzenet küldésekor dinamikusan szúrjon be riasztási adatokat.  **A fejlécek** és a **törzs** alapértelmezett használata az alábbi példához hasonló XML-hasznos adathoz:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > A HTTP-bejegyzési értesítést a **Tesztértesítés** gombra kattintva, de csak a riasztáskonfiguráció mentése után tesztelheti.
    > 
    > 
13. A **mentés** gombra kattintva mentse a riasztási konfigurációt.  Ha **az** 5.

## <a name="next-steps"></a>Következő lépések
* Valós idejű statisztikák elemzése [az Azure CDN-ben](cdn-real-time-stats.md)
* Mélyebbre áshat a [speciális HTTP-jelentésekkel](cdn-advanced-http-reports.md)
* [Használati minták](cdn-analyze-usage-patterns.md) elemzése


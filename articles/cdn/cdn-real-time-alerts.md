---
title: "Az Azure CDN valós idejű riasztások |} Microsoft Docs"
description: "A Microsoft Azure CDN valós idejű riasztásokat. Valós idejű riasztások adja meg a CDN-profil végpontja teljesítményének kapcsolatos értesítéseket."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 51dce1680be5f5f4387c2ba02827195bcdbe9b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>A Microsoft Azure CDN valós idejű riasztások
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a Microsoft Azure CDN valós idejű riasztásokat. Ez a funkció a CDN-profil végpontja teljesítményének valós idejű értesítések biztosít.  E-mailek vagy a HTTP-riasztások alapján állíthat be:

* Sávszélesség
* Állapotkódok
* Gyorsítótár-állapotok
* Kapcsolatok

## <a name="creating-a-real-time-alert"></a>A valós idejű riasztások létrehozása
1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a CDN-profilt.
   
    ![CDN-profil](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil kezelésére gomb](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
3. Vigye a **Analytics** lapra, és vigye a **valós idejű statisztikák** menü.  Kattintson a **valós idejű riasztások**.
   
    ![CDN-felügyeleti portálon](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    A meglévő riasztás konfigurációk (ha van ilyen) listája jelenik meg.
4. Kattintson a **riasztási hozzáadása** gombra.
   
    ![Riasztási gomb felvétele](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Új riasztás létrehozásához űrlap jelenik meg.
   
    ![Új riasztás űrlap](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Ha azt szeretné, hogy ez a riasztás aktív elemre **mentése**, ellenőrizze a **riasztás engedélyezve** jelölőnégyzetet.
6. Adjon meg egy leíró nevet a riasztással a **neve** mező.
7. Az a **médiatípus** legördülő menüből válassza **HTTP nagy objektum**.
   
    ![A kijelölt HTTP nagy objektum adathordozó-típus](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Ki kell választania **HTTP nagy objektum** , a **médiatípus**.  Az egyéb lehetőségek nem használják **Azure CDN Verizon**.  Válassza ki a hibát **HTTP nagy objektum** hatására a riasztást soha nem aktiválódott.
   > 
   > 
8. Hozzon létre egy **kifejezés** kiválasztásával figyelése egy **metrika**, **operátor**, és **érték indítás**.
   
   * A **metrika**, válassza ki a figyelt kívánt feltétel típusát.  **Sávszélesség MB/s** a memóriakészletben felhasznált sávszélesség-használat megabit / másodperc.  **Kapcsolatok száma összesen** peremhálózati kiszolgálókról egyidejű HTTP-kapcsolatok száma.  A különböző gyorsítótár állapotok és állapotkódok-definíciók, lásd: [Azure CDN gyorsítótár állapotkódok](https://msdn.microsoft.com/library/mt759237.aspx) és [Azure CDN HTTP-állapotkódok](https://msdn.microsoft.com/library/mt759238.aspx)
   * **Operátor** a matematikai operátor, amely megteremti a kapcsolatot a metrika és az eseményindítási között.
   * **Indítás, érték** a küszöbérték, amelyeknek teljesülniük kell értesítés elküldése előtt.
     
     A következő példában a létrehozott kifejezés azt jelzi, hogy értesítést küld, ha a 404-es állapotkód száma nagyobb, mint 25.
     
     ![Valós idejű riasztási mintakifejezés](./media/cdn-real-time-alerts/cdn-expression.png)
9. A **időköz**, adja meg, hogy milyen gyakran szeretné a kiértékelendő kifejezés.
10. Az a **értesítés** legördülő menüből válassza, ha szeretne értesítést kapni a kifejezés igaz.
    
    * **A feltétel Start** azt jelzi, hogy értesítést küldeni, a megadott feltétel először észlelése esetén.
    * **A feltétel End** azt jelzi, hogy értesítést küldeni, ha a megadott feltétel már nem észlelhető. Ezt az értesítést csak akkor is kiváltódik, miután a hálózatfigyelési rendszer azt észlelte, hogy a megadott állapot fordult elő.
    * **Folyamatos** azt jelzi, hogy egy értesítést küld minden alkalommal, hogy a hálózatfigyelési rendszer észleli az adott feltételnek. Ne feledje, hogy a hálózatfigyelési rendszer csak egyszer ellenőrzi a megadott feltétel időszakonként.
    * **Az állapot kezdő és záró** azt jelzi, hogy értesítést küldenek először, hogy a megadott feltétel észlel, és ismét Ha a feltétel már nem észlelhető.
1. Ha azt szeretné, hogy e-mailben értesítést kapjon, ellenőrizze a **e-mailben értesítse** jelölőnégyzetet.  
    
    ![E-mailek űrlappal értesítése](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Az a **való** mezőbe írja be az e-mail cím, ha azt szeretné, hogy értesítést küld. A **tulajdonos** és **törzs**, előfordulhat, hogy hagyja meg az alapértelmezett vagy testreszabhatja az üzenet használatával a **elérhető kulcsszavak** lista dinamikusan szúrható be riasztási adatokat, az üzenet elküldésekor.
    
    > [!NOTE]
    > Az e-mail értesítés kattintva tesztelheti a **ellenőrző értesítés** gomb, de csak a riasztás konfigurálásában mentését követően.
    > 
    > 
12. Ha azt szeretné, hogy a webkiszolgáló program értesítések, ellenőrizze a **HTTP Post által értesítendő** jelölőnégyzetet.
    
    ![Közli a HTTP Post képernyő](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Az a **URL-cím** mezőbe írja be az URL-cím, ahol azt szeretné, hogy a HTTP-üzenet közzé. Az a **fejlécek** szövegmező, adja meg a HTTP-fejlécek küldését a kérelemben.  A **törzs**, előfordulhat, hogy testre szabta az üzenetet használatával a **elérhető kulcsszavak** lista dinamikusan szúrható be riasztási adatokat, az üzenet elküldésekor.  **Fejlécek** és **törzs** alapértelmezett egy XML-adattartalmat a következőhöz hasonló:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > A HTTP Post értesítési kattintva tesztelheti a **ellenőrző értesítés** gomb, de csak a riasztás konfigurálásában mentését követően.
    > 
    > 
13. Kattintson a **mentése** gombra kattintva mentse a riasztás konfigurálásában.  Ha bejelölte **riasztás engedélyezve** 5. lépésben, a riasztás mostantól aktív.

## <a name="next-steps"></a>Következő lépések
* Elemezze [Azure CDN valós idejű statisztikák](cdn-real-time-stats.md)
* Mélyebb a Dig [speciális HTTP-jelentések](cdn-advanced-http-reports.md)
* Elemezze [használati minták](cdn-analyze-usage-patterns.md)


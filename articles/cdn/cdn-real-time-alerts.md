---
title: Az Azure CDN valós idejű riasztások |} A Microsoft Docs
description: Valós idejű riasztások a Microsoft Azure CDN Szolgáltatásban. Valós idejű riasztások adja meg a CDN-profil végpont teljesítményével kapcsolatos értesítések.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e20161147aa16456e31aff2bd3cc6337c3690e89
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917855"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>A Microsoft Azure CDN Szolgáltatásban valós idejű riasztások
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a Microsoft Azure CDN Szolgáltatásban valós idejű riasztások. Ez a funkció biztosítja a végpontok a CDN-profilban teljesítményének valós idejű értesítések.  Beállíthatja a e-mailben vagy a HTTP-riasztások alapján:

* Bandwidth
* Állapotkódok
* Gyorsítótárak Allapota
* Kapcsolatok

## <a name="creating-a-real-time-alert"></a>A valós idejű riasztás létrehozása
1. Az a [az Azure portal](https://portal.azure.com), tallózással keresse meg a CDN-profilra.
   
    ![CDN-profil](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. A CDN-profil panelje, kattintson a **kezelés** gombra.
   
    ![CDN-profil kezelése gomb](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portálját.
3. A kurzort a **Analytics** lapfülre, majd mutasson a **valós idejű statisztikák** úszó menü.  Kattintson a **valós idejű riasztások**.
   
    ![CDN felügyeleti portálját](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Megjelenik a meglévő riasztási konfigurációk (ha vannak) listája.
4. Kattintson a **riasztás hozzáadása** gombra.
   
    ![Riasztási gomb hozzáadása](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Megjelenik egy űrlap új riasztás létrehozásához.
   
    ![Új riasztás űrlap](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Ha azt szeretné, hogy ez a riasztás aktív kattintva **mentése**, ellenőrizze a **engedélyezve van a riasztás** jelölőnégyzetet.
6. Adjon meg egy leíró nevet a riasztással a **neve** mező.
7. Az a **adathordozó-típus** legördülő menüben válassza **HTTP nagy objektum**.
   
    ![A kijelölt HTTP nagy objektum az adathordozó típusát](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Ki kell választania **HTTP nagy objektum** , a **adathordozó-típus**.  Nem használja az egyéb lehetőségek **verizon Azure CDN**.  Válassza ki a hibát **HTTP nagy objektum** soha nem aktiválódott a riasztást okoz.
   > 
   > 
8. Hozzon létre egy **kifejezés** kiválasztásával figyelése egy **metrika**, **operátor**, és **értéket Trigger**.
   
   * A **metrika**, válassza ki a kívánt figyelt feltétel típusát.  **A sávszélesség MB/s** , amennyit a sávszélesség megadása megabit / másodperc.  **Kapcsolatok teljes** a peremhálózati kiszolgáló egyidejű HTTP-kapcsolatok száma.  A különböző gyorsítótárak allapota és állapotkódok definíciói, lásd: [Azure CDN gyorsítótára állapotkódok](/previous-versions/azure/mt759237(v=azure.100)) és [Azure CDN HTTP-állapotkódok](/previous-versions/azure/mt759238(v=azure.100))
   * **Operátor** a matematikai operátor, amely a metrika és az eseményindító érték közötti kapcsolatot létesít.
   * **Indító érték** a küszöbérték, amelyeknek teljesülniük kell ahhoz egy értesítést küld.
     
     A következő példában a létrehozott kifejezés azt jelzi, hogy egy értesítést küld-e, ha a 404-es állapotkódok száma nagyobb, mint 25.
     
     ![Valós idejű riasztási minta kifejezés](./media/cdn-real-time-alerts/cdn-expression.png)
9. A **időköz**, adja meg, hogy milyen gyakran szeretné kiértékelendő kifejezés.
10. Az a **értesítést kérek az** legördülő menüben válassza, ha szeretne értesítést, ha a kifejezés igaz.
    
    * **Indítási feltétel** azt jelzi, hogy egy értesítést küld-e, amikor először észlelt a megadott feltételnek.
    * **Teljes feltétel** azt jelzi, hogy egy értesítést küld-e, ha a megadott feltétel már nem észlelhető. Ezt az értesítést csak indítható el, miután a hálózatfelügyeleti rendszer azt észlelte, hogy történt-e a megadott feltételnek.
    * **Folyamatos** azt jelzi, hogy egy értesítést küldi a program minden alkalommal, hogy a hálózatfigyelési rendszer észlel-e a megadott feltételnek. Ne feledje, hogy a hálózatfigyelési rendszer csak egyszer ellenőrzi a megadott feltétel időszakonként.
    * **Az állapot kezdő és záró** azt jelzi, hogy egy értesítést küld az első alkalommal, hogy a megadott feltétel észlelt, és ismét Ha a feltétel már nem észlelhető.
1. Ha azt szeretné, hogy értesítést kapjon e-mailben, ellenőrizze a **e-mailben értesítendő** jelölőnégyzetet.  
    
    ![Csupán értesítő e-mailek űrlap](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Az a **való** mezőbe írja be az e-mail címet, ahol szeretne értesítéseket küldeni. A **tulajdonos** és **törzs**, előfordulhat, hogy hagyja meg az alapértelmezett, vagy előfordulhat, hogy testre szabhatja, az üzenet használatával a **elérhető kulcsszavak** dinamikusan a riasztási adatok beszúrása listában Ha az üzenet a rendszer küld.
    
    > [!NOTE]
    > Az e-mail-értesítés kattintva tesztelheti a **ellenőrző értesítés** gomb, de csak a riasztási konfiguráció mentése után.
    > 
    > 
12. Ha azt szeretné, hogy egy webkiszolgáló a közzétenni kívánt értesítések, ellenőrizze a **értesítése szerint a HTTP Post** jelölőnégyzetet.
    
    ![Közli a HTTP Post-űrlap](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Az a **URL-cím** mezőben adja meg az URL-címet, ahol azt szeretné, hogy a HTTP-üzenetet tesznek közzé. Az a **fejlécek** szövegmezőbe írja be a HTTP-fejléceket, a kérést küldött.  A **törzs**, előfordulhat, hogy testre szabta az üzenetet használatával a **elérhető kulcsszavak** lista dinamikusan riasztási adatok beszúrása, az üzenet elküldésekor.  **A fejlécek** és **törzs** alapértelmezés szerint egy XML-tartalma a következő példához hasonló:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > A HTTP Post értesítési kattintva tesztelheti a **ellenőrző értesítés** gomb, de csak a riasztási konfiguráció mentése után.
    > 
    > 
13. Kattintson a **mentése** gombra kattintva mentse a riasztási konfigurációt.  Ha bejelölte **engedélyezve van a riasztás** 5. lépésben, a riasztás már aktív.

## <a name="next-steps"></a>További lépések
* Elemezheti [valós idejű statisztikák az Azure CDN-ben](cdn-real-time-stats.md)
* Ásson mélyebbre [speciális HTTP-jelentések](cdn-advanced-http-reports.md)
* Elemezheti [használati minták](cdn-analyze-usage-patterns.md)


---
title: "Az Azure CDN szabályok motor használata HTTP működés felülbírálásához |} Microsoft Docs"
description: "A szabályok motor teszi lehetővé testre szabhatja a HTTP-kérések kezelésének módja Azure CDN által például blokkolja-e a tartalom bizonyos típusú kézbesítését, gyorsítótárazási házirend határozza meg és módosíthatja a HTTP-fejlécek."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Az Azure CDN szabálymotor HTTP viselkedésének felülbírálása
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
A szabályok motor lehetővé teszi, hogy segítségével testre szabhatja a HTTP-kérések kezelésének módja, például bizonyos típusú tartalom kézbesítésével blokkolja, a gyorsítótárazási házirend meghatározása és a HTTP-fejlécek módosítása.  Ez az oktatóanyag mutatni szabályt hoz létre a gyorsítótár-viselkedést CDN eszközök változik.  Nincs is videotartalom érhető el a "[lásd még:](#see-also)" szakasz.

   > [!TIP] 
   > Részletes szintaxisa referenciáért lásd: [szabályok motor hivatkozás](cdn-rules-engine-reference.md).
   > 


## <a name="tutorial"></a>Oktatóanyag
1. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Kattintson a **HTTP nagy** fülre, majd **szabálymotor**.
   
    Új szabály beállításait jelennek meg.
   
    ![Új CDN-szabály beállítások](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A több szabály listán sorrendje befolyásolja kezelésének módját. A következő szabály felülbírálhatják az előző szabályok által meghatározott műveleteket.
   > 
   > 
3. Írjon be egy nevet a **neve / leírása** szövegmező.
4. Azonosítja a kéréseket a szabály vonatkozik.  Alapértelmezés szerint a **mindig** egyezés feltétel meg van jelölve.  Fogjuk **mindig** ehhez az oktatóanyaghoz, így hagyja kiválasztott.
   
   ![CDN-egyeztetés feltétel](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > Nincsenek számos különböző típusú egyezik meg a legördülő listában elérhető feltételek.  A bal oldalra, az egyeztetés feltétel kék információs ikonra kattint alapján meghatározható a jelenleg kijelölt feltételt részletesen.
   > 
   >  Részletes feltételes kifejezések teljes listáját lásd: [szabályok motor feltételes kifejezések](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Egyezés feltételek részletesen teljes listáját lásd: [szabályok motor megfelelő feltételek](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 
5. Kattintson a  **+**  gombra **szolgáltatások** hozzáadni egy új funkciót.  A bal oldali legördülő menüben válassza ki **kényszerített belső maximális életkora**.  A szövegmezőben, amely akkor jelenik meg, írja be a **300**.  Hagyja a további alapértelmezett értékei.
   
   ![CDN-szolgáltatás](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Mint feltételek egyeznek, a bal oldalán a új szolgáltatás a kék információs ikonra kattintva jelenik meg ez a szolgáltatás adatait.  A következőket **kényszerített belső maximális életkora**, azt mérvadóak az eszköz **Cache-Control** és **Expires** fejlécek szabályozására, ha a CDN élcsomópont frissíti az eszköz a forrás.  300 másodperc példában azt jelenti, hogy a CDN élcsomópont gyorsítótárazhatják az eszköz 5 perccel eredetéül az eszköz frissítése előtt.
   > 
   > A részletes szolgáltatások teljes listájáért lásd: [szabályok motor a szolgáltatás részletei](cdn-rules-engine-reference-features.md).
   > 
   > 
6. Kattintson a **Hozzáadás** gombra kattintva mentse az új szabályt.  Az új szabály most jóváhagyására vár. Miután engedélyezte, az állapot változik **függőben lévő XML** való **aktív XML**.
   
   > [!IMPORTANT]
   > Szabályok módosítások keresztül a CDN propagálása 90 percig is eltarthat.
   > 
   > 

## <a name="see-also"></a>Lásd még:
* [Az Azure CDN áttekintése](cdn-overview.md)
* [Szabályok motor referencia](cdn-rules-engine-reference.md)
* [Szabályok motor egyezés feltételek](cdn-rules-engine-reference-match-conditions.md)
* [Szabályok motor feltételes kifejezések](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabályok adatbázismotor-szolgáltatások](cdn-rules-engine-reference-features.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)
* [Az Azure péntekenként: Az Azure CDN új Premium szolgáltatással](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (videó)
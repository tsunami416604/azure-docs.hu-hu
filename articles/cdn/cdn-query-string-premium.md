---
title: Szabályozhatja az Azure CDN a lekérdezési karakterláncok - prémium csomagban gyorsítótárazásának |} Microsoft Docs
description: Az Azure CDN lekérdezési karakterláncok gyorsítótárazásának szabályozza, hogyan kerül a gyorsítótárba amikor webes kérelem lekérdezési karakterláncot tartalmaz. Ez a cikk ismerteti a lekérdezési karakterláncok gyorsítótárazásának a a termék Verizon Azure CDN Premium.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mazha
ms.openlocfilehash: 9d92602ef5071579e0c741dd24a4e3e9f7b2c747
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Vezérlő Azure CDN a lekérdezési karakterláncok - prémium csomagban gyorsítótárazásának
> [!div class="op_single_selector"]
> * [Standard csomag](cdn-query-string.md)
> * [Premium szintű csomag](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Áttekintés
Lekérdezési karakterláncok gyorsítótárazása, az Azure Content Delivery Network (CDN) szabályozza, hogyan kerül a gyorsítótárba egy webes kérelem lekérdezési karakterláncot tartalmazó. A webes kérelem lekérdezési karakterláncot a lekérdezési karakterlánc része a kérelmet, amely után a kérdőjel (?). A lekérdezési karakterlánc tartalmazhat egy vagy több kulcs-érték párok, amely a mező nevét és az értéke egyenlőségjelnek (=) elválasztva. Minden kulcs-érték párt elválasztott ampersand (&). For example, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Ha a kérelem lekérdezési karakterláncként egynél több kulcs-érték pár, a sorrendjük nincs jelentősége. 

> [!NOTE]
> Az Azure CDN standard és premium termékek adja meg ugyanazt a lekérdezési karakterláncot gyorsítótárazási funkció, de a felhasználói felület különböző.  Ez a cikk ismerteti a felület **verizon Azure CDN Premium**. A lekérdezési karakterláncok gyorsítótárazása a **Azure CDN Standard Akamai** és **Azure CDN Standard verizon**, lásd: [vezérlő Azure CDN a lekérdezési karakterláncok - standard csomagragyorsítótárazásának](cdn-query-string.md).
>


Lekérdezés-karakterlánc három módot érhetők el:

- **Standard-gyorsítótár**: alapértelmezett mód. Ebben a módban a CDN nyújtó jelenléti pontra (POP) csomópont megfelel a lekérdezési karakterláncok a kérelmezőnek az az eredeti kiszolgálóra az első kérésre, és az eszköz gyorsítótárazza. Az összes további kérelmet az eszköz a kiszolgáló POP szolgáltatott figyelmen kívül hagyja a lekérdezési karakterláncok eléréséig a gyorsítótárazott objektumhoz.

    >[!IMPORTANT] 
    > Token engedélyezési bármilyen útvonalat ehhez a fiókhoz engedélyezett, ha a standard-gyorsítótár üzemmódban az egyetlen mód használható. 

- **no-cache**: Ebben a módban a lekérdezési karakterláncot tartalmazó kérelmek nem gyorsítótárazzák a CDN POP-ra a csomóponton. A POP-csomópont veszi át az objektumot közvetlenül a forráskiszolgálóról, és minden egyes kérelemmel a kérelmező számára továbbítja azokat.

- **egyedi gyorsítótár**: Ebben a módban egy egyedi URL-cím, beleértve a lekérdezési karakterlánc minden kérelmet egy egyedi objektum saját gyorsítótár számít. Például a forráskiszolgálóról example.ashx?q=test1 kérelmet a válaszban gyorsítótárba helyezte a POP-csomópont, és későbbi gyorsítótárak az azonos lekérdezési karakterláncot adott vissza. Saját idő a működés közbeni beállítású külön eszközként example.ashx?q=test2 kérelmet gyorsítótárazza.
   
    >[!IMPORTANT] 
    > Ne használja ezt a módot, ha a lekérdezési karakterlánc minden kérelemnél, például egy munkamenet-Azonosítót vagy egy felhasználónevet, változik paramétereket tartalmaz, mert egy kis gyorsítótár találati arány okoz.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Lekérdezési karakterláncok gyorsítótárazásának prémium szintű CDN-profil beállításainak módosítása
1. Nyissa meg a CDN-profilt, majd kattintson a **kezelése**.
   
    ![CDN-profil Manage gomb](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **HTTP nagy** lapra, és vigye a **gyorsítótár beállításainak** úszó menüje. Kattintson a **lekérdezési karakterlánc-gyorsítótár**.
   
    Lekérdezési karakterlánc gyorsítótárazási beállítások jelennek meg.
   
    ![A CDN a lekérdezési karakterláncban a gyorsítótár](./media/cdn-query-string-premium/cdn-query-string.png)
3. Válassza ki az lekérdezési karakterláncot, majd kattintson az **frissítés**.

> [!IMPORTANT]
> A regisztráció CDN propagálásához időt vesz igénybe, mert a gyorsítótár karakterlánc módosításait nem feltétlenül jelennek meg azonnal. A **verizon Azure CDN Premium** -profilok propagálása általában befejezi 90 percen belül.
 


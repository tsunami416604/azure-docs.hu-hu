---
title: Szabályozhatja az Azure CDN a lekérdezési karakterláncok - prémium csomagban gyorsítótárazásának |} Microsoft Docs
description: Az Azure CDN lekérdezési karakterláncok gyorsítótárazásának szabályozza, hogyan kerül a gyorsítótárba amikor webes kérelem lekérdezési karakterláncot tartalmaz. Ez a cikk ismerteti a lekérdezési karakterláncok gyorsítótárazásának a a termék Verizon Azure CDN Premium.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: 3777689af72f580645826a1ca4e31ca84bd65dab
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260834"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Vezérlő Azure CDN a lekérdezési karakterláncok - prémium csomagban gyorsítótárazásának
> [!div class="op_single_selector"]
> * [Standard csomag](cdn-query-string.md)
> * [Premium szintű csomag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Áttekintés
Az Azure Content Delivery Network (CDN), megadhatja a hogyan kerül a gyorsítótárba egy lekérdezési karakterláncot tartalmazó webes kérelem számára. A webes kérelem lekérdezési karakterláncot a lekérdezési karakterlánc része a kérelmet, amely után a kérdőjel (?). A lekérdezési karakterlánc tartalmazhat egy vagy több kulcs-érték párok, amely a mező nevét és az értéke egyenlőségjelnek (=) elválasztva. Minden kulcs-érték párt elválasztott ampersand (&). Például a http:\//www.contoso.com/content.mov?field1=value1 & mező2 = érték2. Ha a kérelem lekérdezési karakterláncként egynél több kulcs-érték pár, a sorrendjük nincs jelentősége. 

> [!IMPORTANT]
> A standard és prémium szintű CDN-termékek, adja meg ugyanazt a lekérdezési karakterláncot gyorsítótárazási funkció, de a felhasználói felület különböző. Ez a cikk ismerteti a felület **verizon Azure CDN Premium**. Lekérdezési karakterlánc gyorsítótárazást, ha Azure CDN standard termékek, lásd: [vezérlő Azure CDN a lekérdezési karakterláncok - standard csomagra gyorsítótárazásának](cdn-query-string.md).
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
> A regisztráció CDN propagálásához időt vesz igénybe, mert a gyorsítótár karakterlánc módosításait nem feltétlenül jelennek meg azonnal. Propagálása általában befejezi 10 perc múlva.
 


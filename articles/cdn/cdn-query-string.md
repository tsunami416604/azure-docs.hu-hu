---
title: Szabályozhatja az Azure CDN a lekérdezési karakterláncok - standard csomagra gyorsítótárazásának |} Microsoft Docs
description: Az Azure CDN lekérdezési karakterláncok gyorsítótárazásának szabályozza, hogyan kerül a gyorsítótárba amikor webes kérelem lekérdezési karakterláncot tartalmaz. Ez a cikk ismerteti a lekérdezési karakterláncok gyorsítótárazásának Azure CDN standard termékekben.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mazha
ms.openlocfilehash: ed6f0b2c021fc4b31b85986c07df0502dba826f2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Vezérlő Azure CDN a lekérdezési karakterláncok - standard csomagra gyorsítótárazásának
> [!div class="op_single_selector"]
> * [Standard csomag](cdn-query-string.md)
> * [Premium szintű csomag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Áttekintés
Lekérdezési karakterláncok gyorsítótárazása, az Azure Content Delivery Network (CDN) szabályozza, hogyan kerül a gyorsítótárba egy webes kérelem lekérdezési karakterláncot tartalmazó. A webes kérelem lekérdezési karakterláncot a lekérdezési karakterlánc része a kérelmet, amely után a kérdőjel (?). A lekérdezési karakterlánc tartalmazhat egy vagy több kulcs-érték párok, amely a mező nevét és az értéke egyenlőségjelnek (=) elválasztva. Minden kulcs-érték párt elválasztott ampersand (&). For example, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Ha a kérelem lekérdezési karakterláncként egynél több kulcs-érték pár, a sorrendjük nincs jelentősége. 

> [!NOTE]
> Az Azure CDN standard és premium termékek adja meg ugyanazt a lekérdezési karakterláncot gyorsítótárazási funkció, de a felhasználói felület különböző.  Ez a cikk ismerteti a felület **Azure CDN Standard Akamai** és **Azure CDN Standard verizon**. A lekérdezési karakterláncok gyorsítótárazása a **verizon Azure CDN Premium**, lásd: [vezérlő Azure CDN a lekérdezési karakterláncok - prémium csomagban gyorsítótárazásának](cdn-query-string-premium.md).
>

Lekérdezés-karakterlánc három módot érhetők el:

- **Lekérdezési karakterláncok figyelmen kívül**: alapértelmezett mód. Ebben a módban a CDN nyújtó jelenléti pontra (POP) csomópont megfelel a lekérdezési karakterláncok a kérelmezőnek az az eredeti kiszolgálóra az első kérésre, és az eszköz gyorsítótárazza. Az összes további kérelmet az eszköz a POP-ra a szolgáltatott figyelmen kívül hagyja a lekérdezési karakterláncok eléréséig a gyorsítótárazott objektumhoz.

- **Lekérdezési sztringek gyorsítótárazásának mellőzése**: Ebben a módban a lekérdezési karakterláncot tartalmazó kérelmek nem gyorsítótárazzák a CDN POP-ra a csomóponton. A POP-csomópont veszi át az objektumot közvetlenül a forráskiszolgálóról, és minden egyes kérelemmel a kérelmező számára továbbítja azokat.

- **Minden egyedi URL-cím gyorsítótárazása**: Ebben a módban egy egyedi URL-cím, beleértve a lekérdezési karakterlánc minden kérelmet egy egyedi objektum saját gyorsítótár számít. Például a forráskiszolgálóról example.ashx?q=test1 kérelmet a válaszban gyorsítótárba helyezte a POP-csomópont, és későbbi gyorsítótárak az azonos lekérdezési karakterláncot adott vissza. Saját idő a működés közbeni beállítású külön eszközként example.ashx?q=test2 kérelmet gyorsítótárazza.
   
    >[!IMPORTANT] 
    > Ne használja ezt a módot, ha a lekérdezési karakterlánc minden kérelemnél, például egy munkamenet-Azonosítót vagy egy felhasználónevet, változik paramétereket tartalmaz, mert egy kis gyorsítótár találati arány okoz.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Lekérdezési karakterláncok gyorsítótárazásának standard szintű CDN-profil beállításainak módosítása
1. Nyissa meg a CDN-profilt, majd válassza ki a kezelni kívánt CDN-végpont.
   
   ![CDN-profil végpontok](./media/cdn-query-string/cdn-endpoints.png)
   
2. A beállítások a bal oldali ablaktáblában kattintson **szabályok gyorsítótárazás**.
   
    ![CDN-gyorsítótárazás szabályok gomb](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Az a **lekérdezési sztringek gyorsítótárazásának** listán, válassza ki az lekérdezési karakterláncot, majd kattintson **mentése**.
   
   ![A CDN a lekérdezési karakterláncban a gyorsítótár](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> A regisztráció CDN propagálásához időt vesz igénybe, mert a gyorsítótár karakterlánc módosításait nem feltétlenül azonnal látható: 
> - A **Azure CDN Standard Akamai** -profilok propagálása általában befejezi egy percen belül. 
> - A **Azure CDN Standard verizon** -profilok propagálása általában befejezi 90 percen belül.
>



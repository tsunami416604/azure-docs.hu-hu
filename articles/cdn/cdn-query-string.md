---
title: "Szabályozhatja az Azure CDN a lekérdezési karakterláncok gyorsítótárazásának |} Microsoft Docs"
description: "Az Azure CDN lekérdezési karakterláncok gyorsítótárazásának hogyan kerül a gyorsítótárba lekérdezési karakterláncokat tartalmazó vezérlő."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 28e724f34c32edb0d5641b24f9ffedb7dc5f9680
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>Vezérlő Azure Content Delivery Network a lekérdezési karakterláncok gyorsítótárazásának
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Verizon Azure CDN Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Áttekintés
Az Azure Content Delivery Network (CDN), megadhatja a hogyan kerül a gyorsítótárba egy lekérdezési karakterláncot tartalmazó webes kérelem számára. A webes kérelem lekérdezési karakterláncot, a lekérdezési karakterlánc része a kérelmet, amely után a `?` karakter. A lekérdezési karakterlánc tartalmazhat egy vagy több paramétert, az elemek elválasztására pedig a `&` karakter. Például: `http://www.domain.com/content.mov?data1=true&data2=false`. Ha a kérelem több lekérdezési karakterláncot, a paraméterek sorrendje nem számít. 

> [!IMPORTANT]
> A standard és prémium szintű CDN-termékek, adja meg ugyanazt a lekérdezési karakterláncot gyorsítótárazási funkció, de a felhasználói felület különböző.  Ez a cikk ismerteti a felület **Azure CDN Standard Akamai** és **Azure CDN Standard verizon**. A lekérdezési karakterláncok gyorsítótárazása a **verizon Azure CDN Premium**, lásd: [a lekérdezési karakterláncok - prémium szintű CDN gyorsítótárazási viselkedésének vezérlése kérelmek](cdn-query-string-premium.md).

Lekérdezés-karakterlánc három módot érhetők el:

- **Lekérdezési karakterláncok figyelmen kívül**: alapértelmezett mód. Ebben a módban CDN élcsomópont továbbítja a lekérdezési karakterláncok a kérelmezőnek az eredeti az első kérésre, és az eszköz gyorsítótárazza. Az összes további kérelmet az eszköz az élcsomópont szolgáltatott figyelmen kívül hagyja a lekérdezési karakterláncok eléréséig a gyorsítótárazott objektumhoz.
- **Lekérdezési sztringek gyorsítótárazásának mellőzése**: Ebben a módban a lekérdezési karakterláncot tartalmazó kérelmek nem gyorsítótárazzák a CDN a peremhálózati csomóponton. Élcsomópont olvassa be az objektumot közvetlenül a forrásból, és minden egyes kérelemmel a kérelmező számára továbbítja azokat.
- **Minden egyedi URL-cím gyorsítótárazása**: Ebben a módban egy egyedi URL-cím, beleértve a lekérdezési karakterlánc minden kérelmet egy egyedi objektum saját gyorsítótár számít. Például az eredeti adatforrást kérelmet kapott válasz `example.ashx?q=test1` élcsomópont gyorsítótárazott és későbbi gyorsítótárak az azonos lekérdezési karakterláncot adott vissza. A kérelem `example.ashx?q=test2` saját idő a működés közbeni beállítású külön eszközként gyorsítótárazza.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Lekérdezési karakterláncok gyorsítótárazásának standard szintű CDN-profil beállításainak módosítása
1. Nyissa meg a CDN-profilt, majd válassza ki a kezelni kívánt CDN-végpont.
   
   ![CDN-profil végpontok](./media/cdn-query-string/cdn-endpoints.png)
   
2. Kattintson a beállítások **gyorsítótár**.
   
    ![CDN-profil gyorsítótár gomb](./media/cdn-query-string/cdn-cache-btn.png)
   
3. Az a **lekérdezési sztringek gyorsítótárazásának** listán, válassza ki az lekérdezési karakterláncot, majd kattintson **mentése**.
   
  <!--- Replace screen shot after general caching goes live ![CDN query string caching options](./media/cdn-query-string/cdn-query-string.png) --->

> [!IMPORTANT]
> A regisztráció CDN propagálásához időt vesz igénybe, mert a gyorsítótár karakterlánc módosításait nem feltétlenül jelennek meg azonnal. Az **Akamai Azure CDN** típusú profilok propagálása általában egy percen belül befejeződik. A **Verizon Azure CDN** típusú profilok propagálása általában 90 percen belül befejeződik, ám egyes esetekben több időt is igénybe vehet.



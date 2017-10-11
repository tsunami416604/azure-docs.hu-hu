---
title: "Az Azure CDN-végpont eszközök előzetes betöltése |} Microsoft Docs"
description: "Megtudhatja, hogyan előzetes betöltése Azure CDN-végpont gyorsítótárazott tartalmat."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 1f2dcd9a91bb6e883cbef06373c1acd98bf8d45f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Eszközök előzetes betöltése Azure CDN-végponton
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Alapértelmezés szerint eszközök először gyorsítótárba kerüljenek-e, erre felkérést kapnak. Ez azt jelenti, hogy az első kérésre minden régióban hosszabb időt vehet igénybe, mivel a peremhálózati kiszolgálóinak nem kell a tartalom gyorsítótárazva, és továbbítja a kérelmet az eredeti kiszolgálóra kell. Előre a tartalom betöltése elkerülhető az első találati késés.

Mellett jobb felhasználói élményt biztosít, a gyorsítótárazott eszközök előzetes betöltése is csökkentheti a forráskiszolgáló hálózati forgalmat.

> [!NOTE]
> Eszközök előzetes betöltése esetén hasznos nagy események vagy a tartalmat, hogy a felhasználók számára, például egy új movie kiadás vagy szoftverfrissítés nagy számú egyidejűleg elérhetővé válnak.
> 
> 

Ez az oktatóanyag bemutatja, hogyan előzetes betöltését a gyorsítótárazott tartalom minden Azure CDN peremhálózati csomópontján.

## <a name="walkthrough"></a>Útmutatás
1. Az a [Azure Portal](https://portal.azure.com), keresse meg a CDN-profilt, amely tartalmazza a előzetes betöltése kívánt végpont.  A profil panelje megnyílik.
2. Kattintson a végpont a listában.  A végpont panel nyílik meg.
3. A CDN-végpont paneljéről a terhelés gombra.
   
    ![CDN-végpont panelje](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Ekkor megnyílik a terhelés panel.
   
    ![CDN-betöltési panelje](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Írja be a betölteni kívánt minden egyes eszköz elérési útját (például `/pictures/kitten.png`) található a **elérési** szövegmező.
   
   > [!TIP]
   > További **elérési** szövegmezők lehetővé teszi több eszközök listájának összeállítása szöveg megadása után jelenik meg.  Eszközök a három ponttal (…) gombra kattintva törölheti a listából.
   > 
   > Elérési út lehet egy relatív URL-címet, amely megfelel a következő [reguláris kifejezés](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > >Egy egyetlen fájl elérési útját betöltése `@"^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$"`;  
   > >A lekérdezési karakterlánc egyetlen fájl betöltése`@"^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$";`  
   > 
   > Minden eszköz a saját elérési utat kell rendelkeznie.  Nincs előre betöltése eszközök helyettesítő funkció sem.
   > 
   > 
   
    ![Betöltési gomb](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Kattintson a **terhelés** gombra.
   
    ![Betöltési gomb](./media/cdn-preload-endpoint/cdn-load-button.png)

> [!NOTE]
> CDN-profil percenként 10 vonatkozó korlátozása van. 50 elérési utat kérelmenként engedélyezettek. Mindegyik elérési útból útvonal-hossza legfeljebb 1024 karakterből állhat.
> 
> 

## <a name="see-also"></a>Lásd még:
* [Az Azure CDN-végpont törlése](cdn-purge-endpoint.md)
* [Az Azure CDN REST API-referencia - kiürítése, vagy a végpont előzetes betöltése](https://msdn.microsoft.com/library/mt634451.aspx)


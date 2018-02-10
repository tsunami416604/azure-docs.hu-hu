---
title: "Az Azure CDN-végpont eszközök előzetes betöltése |} Microsoft Docs"
description: "Megtudhatja, hogyan előzetes betöltése Azure CDN-végpont gyorsítótárazott tartalmat."
services: cdn
documentationcenter: 
author: dksimpson
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: mazha
ms.openlocfilehash: acd6eae12ff338c64cc8879aa8c27b226e3d2f84
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Eszközök előzetes betöltése Azure CDN-végponton
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Alapértelmezés szerint eszközök csak akkor, amikor erre felkérést kapnak lettek gyorsítótárazva. Ennek eredményeképpen az első kérésre minden régióban is tovább tart, mint későbbi kérelmeket. A az oka, mert a peremhálózati kiszolgáló még nem gyorsítótárba helyezték a tartalmat, és továbbítja a kérelmet az eredeti kiszolgálóra kell. Tartalom előzetes betöltése, elkerülheti a találati első késés.

Mellett jobb felhasználói élményt biztosít, a gyorsítótárazott eszközök előzetes betöltése is csökkentheti a forráskiszolgáló hálózati forgalmat.

> [!NOTE]
> Eszközök előzetes betöltése esetén hasznos nagy események vagy a tartalmat, hogy a felhasználók számára, például egy új movie kiadás vagy szoftverfrissítés nagy számú egyidejűleg elérhetővé válnak.
> 
> 

Ez az oktatóanyag bemutatja, hogyan előzetes betöltését a gyorsítótárazott tartalom minden Azure CDN peremhálózati csomópontján.

## <a name="to-pre-load-assets"></a>Az eszközök előzetes betöltése
1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a CDN-profilt, amely tartalmazza a előzetes betöltése kívánt végpont. A profil ablaktábla megnyitása.
    
2. Kattintson a végpont a listában. A végpont ablaktábla megnyitása.
3. A CDN-végpont ablaktáblán válassza ki a **terhelés**.
   
    ![CDN-végpont ablaktábla](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    A **terhelés** ablaktábla megnyitása.
   
    ![CDN-betöltési ablaktábla](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. A **Tartalomelérési utat**, írja be a betölteni kívánt minden egyes eszköz elérési útját (például `/pictures/kitten.png`).
   
   > [!TIP]
   > További **Tartalomelérési utat** szövegmezők jelenik meg, hogy több eszközök listájának összeállítása meg szöveg megadásával elindítása után. Eszközök törlése a listából, kattintson a három ponttal (…) gombra, majd jelölje ki **törlése**.
   > 
   > Minden tartalom elérési útnak kell lennie egy relatív URL-címet, amely megfelel a következő [reguláris kifejezések](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > - Betölteni egy egyetlen fájl elérési útja: `@"^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$"`;  
   > - A lekérdezési karakterlánc egyetlen fájl betöltése:`@"^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$";` 
   > 
   > Minden eszköz a saját elérési utat kell rendelkeznie. Nincs előre betöltése eszközök helyettesítő funkció sem.
   > 
   > 
   
    ![Betöltési gomb](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Ha elkészült, írja be a tartalom elérési útját, válassza ki a **terhelés**.
   

> [!NOTE]
> CDN-profil percenként 10 vonatkozó korlátozása van. 50 egyidejű elérési utat egy időben lehet feldolgozni. Mindegyik elérési útból útvonal-hossza legfeljebb 1024 karakterből állhat.
> 
> 

## <a name="see-also"></a>Lásd még
* [Az Azure CDN-végpont törlése](cdn-purge-endpoint.md)
* [Az Azure CDN REST API-referencia: a végpont tartalom előzetes betöltése](https://docs.microsoft.com/en-us/rest/api/cdn/endpoints/loadcontent)
* [Az Azure CDN REST API-referencia: a végpont a tartalom kiürítése](https://docs.microsoft.com/en-us/rest/api/cdn/endpoints/purgecontent)


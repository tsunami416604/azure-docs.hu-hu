---
title: Az Azure CDN-végpont eszközök előzetes betöltése |} Microsoft Docs
description: Megtudhatja, hogyan előzetes betöltése Azure CDN-végpont gyorsítótárazott tartalmat.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: mazha
ms.openlocfilehash: bf3161d756759e4b278e48ad7a49615e4a73d17f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Eszközök előzetes betöltése Azure CDN-végponton
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Alapértelmezés szerint csak akkor, ha a kért folyamatban eszközök gyorsítótárba kerüljenek. A peremhálózati kiszolgáló még nem gyorsítótárba helyezték a tartalmat, és továbbítja a kérelmet az eredeti kiszolgálóra kell, mert az első kérésre minden régióban is tovább tart, mint későbbi kérelmeket. Az első találati késleltetés elkerülése érdekében eszközök előzetes betöltése a. Mellett jobb felhasználói élményt biztosít, a gyorsítótárazott eszközök előzetes betöltése csökkentheti a forráskiszolgáló hálózati forgalmat.

> [!NOTE]
> Eszközök előzetes betöltése esetén hasznos nagy események vagy a tartalmat, amely egyszerre sok felhasználó, például egy új movie kiadás vagy szoftverfrissítés elérhetővé válik.
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
   > Írja be a szöveget, további elindítása után **Tartalomelérési utat** szövegmezőkben lehetővé teszi több eszközök listájának összeállítása fog megjelenni. Eszközök törlése a listából, kattintson a három ponttal (…) gombra, majd jelölje ki **törlése**.
   > 
   > Minden tartalom elérési útnak kell lennie egy relatív URL-címet, amely megfelel a következő [reguláris kifejezések](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > - Betölteni egy egyetlen fájl elérési útja: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - A lekérdezési karakterlánc egyetlen fájl betöltése: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Minden eszköz rendelkeznie kell a saját elérési utat, mert nincs előre betöltése eszközök helyettesítő funkció sem.
   > 
   > 
   
    ![Betöltési gomb](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Ha elkészült, írja be a tartalom elérési útját, válassza ki a **terhelés**.
   

> [!NOTE]
> Maximális száma 10 terhelésigényét percenként CDN-profilt, és 50 egyidejű elérési utat egy időben dolgozhatók fel. Mindegyik elérési útból útvonal-hossza legfeljebb 1024 karakterből állhat.
> 
> 

## <a name="see-also"></a>Lásd még
* [Az Azure CDN-végpont törlése](cdn-purge-endpoint.md)
* [Az Azure CDN REST API-referencia: a végpont tartalom előzetes betöltése](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Az Azure CDN REST API-referencia: a végpont a tartalom kiürítése](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)


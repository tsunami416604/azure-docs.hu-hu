---
title: Az Azure CDN-végponton eszközök előzetes betöltése |} A Microsoft Docs
description: Ismerje meg, hogyan előzetes betöltése Azure CDN-végponton gyorsítótárazott tartalmat.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: d2dc8ad1e4b7e429dc758a96e49aa4825ae108e5
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091317"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Eszközök előzetes betöltése Azure CDN-végponton
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Alapértelmezés szerint eszközök csak akkor, ha Ön a kért lettek gyorsítótárazva. A peremhálózati kiszolgálókon még nem gyorsítótárazza a tartalmat, és továbbítja a kérést a forráskiszolgáló kell, mert az első kérés az egyes régiókban is tovább tart, mint későbbi kérelmeket. Az első találati késés elkerülése érdekében eszközök előzetes betöltése a. Amellett, hogy a jobb felhasználói élmény, a gyorsítótárazott eszközök előzetes betöltése képesek csökkenteni a hálózati forgalmat a forrás-kiszolgálón.

> [!NOTE]
> Eszközök előzetes betöltése hasznos a nagy méretű események vagy a tartalom, amely egyszerre sok felhasználó, például egy új filmet kiadás vagy egy szoftverfrissítés elérhetővé válnak.
> 
> 

Ez az oktatóanyag végigvezeti az összes Azure CDN-határcsomópontra gyorsítótárazott tartalmat előre betöltése.

## <a name="to-pre-load-assets"></a>Az eszközök előzetes betöltése
1. Az a [az Azure portal](https://portal.azure.com), keresse meg a végpont előre betölteni kívánt tartalmazó CDN profilt. A profil ablaktábla megnyitása.
    
2. Kattintson a végpontra a listában. A végpont ablaktábla megnyitása.
3. Válassza ki a CDN-végpont panelről **terhelés**.
   
    ![A CDN-végpont panel](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    A **terhelés** panel nyílik meg.
   
    ![CDN terhelés panel](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. A **Tartalomelérési utat**, írja be a betölteni kívánt minden egyes eszköz elérési útját (például `/pictures/kitten.png`).
   
   > [!TIP]
   > Szövegbevitel, további elindítása után **Tartalomelérési utat** szövegmezők jelenik meg, hogy hozhat létre több eszközök listáját. Eszközök törlése a listából, kattintson a három pontra (...) gombra, majd jelölje ki **törlése**.
   > 
   > Minden tartalom elérési útnak kell lennie egy relatív URL-címet, amely megfelel a következő [reguláris kifejezések](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > - Betöltése egy egyetlen fájl elérési útja: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - A lekérdezési karakterlánc egyetlen fájl betöltése: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Minden eszköznek rendelkeznie kell a saját elérési útja, mert nincs helyettesítő funkció sem eszközök előzetes betöltése során.
   > 
   > 
   
    ![Betöltés gombra](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Amikor végzett, írja be a tartalmak elérési útjait, válassza ki a **terhelés**.
   

> [!NOTE]
> A CDN-profil percenként 10 vonatkozó korlát, és legfeljebb 50 egyidejű elérési utat egy időben feldolgozható. Mindegyik elérési út esetében a elérési út – hosszúsága 1024 karakter lehet.
> 
> 

## <a name="see-also"></a>Lásd még
* [Az Azure CDN-végpont végleges törlése](cdn-purge-endpoint.md)
* [Az Azure CDN – REST API-referencia: tartalom a végpont előzetes betöltése](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Az Azure CDN – REST API-referencia: tartalom végleges-végpont](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)


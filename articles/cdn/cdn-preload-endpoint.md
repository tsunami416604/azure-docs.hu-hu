---
title: Eszközök előzetes betöltése egy Azure CDN-végponton | Microsoft dokumentumok
description: Ismerje meg, hogyan lehet előre betölteni a gyorsítótárazott tartalmat egy Azure CDN-végponton.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: d91507ad2cb271b23b588ef7da88e6e6712915b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593581"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Eszközök előzetes betöltése Azure CDN-végponton
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Alapértelmezés szerint az eszközök csak akkor kerülnek a gyorsítótárba, ha a rendszer kéri őket. Mivel a peremhálózati kiszolgálók még nem gyorsítótárazták a tartalmat, és a kérelmet továbbítaniuk kell az eredeti kiszolgálóra, az egyes régiókból érkező első kérés hosszabb időt vehet igénybe, mint a későbbi kérelmek. Az első találatos késés elkerülése érdekében előzetesen töltse be az eszközöket. A jobb ügyfélélmény biztosítása mellett a gyorsítótárazott eszközök előzetes betöltése csökkentheti a hálózati forgalmat az eredeti kiszolgálón.

> [!NOTE]
> Az eszközök előzetes betöltése nagy események vagy tartalom esetén hasznos, amelyek egyszerre válnak elérhetővé sok felhasználó számára, például egy új filmkiadás vagy egy szoftverfrissítés számára.
> 
> 

Ez az oktatóanyag végigvezeti a gyorsítótárazott tartalom előzetes betöltésén az összes Azure CDN peremhálózati csomóponton.

## <a name="to-pre-load-assets"></a>Eszközök előzetes betöltése
1. Az [Azure Portalon](https://portal.azure.com)keresse meg a CDN-profilt, amely tartalmazza a végpontot előtölteni kívánt. Megnyílik a profilablaktábla.
    
2. Kattintson a végpontra a listában. Megnyílik a végpontablaktábla.
3. A CDN végpontablaktábláján válassza a **Betöltés**lehetőséget.
   
    ![CDN végpont ablaktábla](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Megnyílik **a Betöltés** ablaktábla.
   
    ![CDN betöltési ablaktábla](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. A **Tartalom elérési út esetén**adja meg a betölteni `/pictures/kitten.png`kívánt eszközök (például) teljes elérési útját.
   
   > [!TIP]
   > A szöveg beírásának megkezdése után további **Tartalomelérési út** szövegmezők jelennek meg, amelyek lehetővé teszik több eszköz listájának összeállítását. Ha törölni szeretné az eszközöket a listából, kattintson a három pont (...) gombra, majd a **Törlés gombra.**
   > 
   > Minden tartalomelérési útnak relatív URL-címnek kell lennie, amely megfelel a következő [reguláris kifejezéseknek:](/dotnet/standard/base-types/regular-expression-language-quick-reference)  
   > - Egyetlen fájlelérési út betöltése:`^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Egyetlen fájl betöltése lekérdezési karakterlánccal:`^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Mivel minden eszköznek saját elérési úttal kell rendelkeznie, nincs helyettesítő funkció az eszközök előzetes betöltéséhez.
   > 
   > 
   
    ![Betöltés gomb](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Amikor befejezte a tartalomelérési utak beírását, válassza a **Betöltés**lehetőséget.
   

> [!NOTE]
> CdN-profilonként percenként legfeljebb 10 terhelési kérelem érhető el, és egyszerre 50 egyidejű elérési út dolgozható fel. Minden elérési út hosszhatára 1024 karakter.
> 
> 

## <a name="see-also"></a>Lásd még
* [Azure CDN-végpont kiürítése](cdn-purge-endpoint.md)
* [Azure CDN REST API-hivatkozás: Tartalom előtöltése egy végponton](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure CDN REST API-hivatkozás: Tartalom kiürítése egy végpontról](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)


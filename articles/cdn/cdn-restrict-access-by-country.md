---
title: "Azure CDN-tartalom ország korlátozása |} Microsoft Docs"
description: "Útmutató az Azure CDN-tartalom a földrajzi-szűrés szolgáltatás használatával korlátozza a hozzáférést."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="restrict-azure-cdn-content-by-country"></a>Ország Azure CDN-tartalom korlátozása

## <a name="overview"></a>Áttekintés
Amikor egy felhasználó alapértelmezés szerint a tartalmat igényel, függetlenül attól, ahol a felhasználó kérelmet ezt a kiszolgált a tartalmat. Bizonyos esetekben érdemes lehet a tartalmat az ország való hozzáférésének korlátozásához. Ez a témakör ismerteti, hogyan a **földrajzi-szűrés** szolgáltatás a szolgáltatás engedélyezi vagy letiltja a hozzáférést, ország konfigurálásához.

> [!IMPORTANT]
> Verizon és Akamai termékek funkcionalitása azonos földrajzi-szűrés, de egy kis értékkülönbségeket te országhívó számokat támogatják-e. Tekintse meg a 3. lépés a különbségek mutató hivatkozást.


Az ilyen korlátozásokat konfigurálására vonatkozó megfontolások kapcsolatos információkért lásd: a [szempontok](cdn-restrict-access-by-country.md#considerations) szakasz a témakör végén.  

![Ország szerinti szűrés](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>1. lépés: A könyvtár elérési útjának megadása
Válassza ki a végponthoz, a portálon, és ez a szolgáltatás található a bal oldali navigációs a földrajzi-szűrés lapon található.

Ország szűrő konfigurálásakor meg kell adnia a relatív elérési útját, amelyhez felhasználók engedélyez vagy megtagadta a hozzáférést. Az összes fájl földrajzi-szűrés alkalmazhatja "/" vagy a kijelölt mappák elérési útjaiban "/ képek /" megadásával. Is alkalmazhat földrajzi-szűrés egyetlen fájl megadása a fájl, és a záró perjelet kihagyva "/ képek/város.png".

Példa könyvtár elérési útja szűrő:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>2. lépés: Adja meg a műveletet: letiltása vagy engedélyezése
**Blokkolás:** megadott országokból származó felhasználók hozzáférését a rendszer megtagadja lekéri a rekurzív út eszközökhöz. Ha nincs más országban szűrési beállítások vannak konfigurálva, hogy a helyen, majd a többi felhasználó hozzáférése engedélyezett lesz.

**Engedélyezése:** csak a megadott országokból származó felhasználók kapnak hozzáférést, lekéri a rekurzív út eszközökhöz.

## <a name="step-3-define-the-countries"></a>3. lépés: A országok meghatározása
Válassza ki az elérési utat engedélyezése vagy blokkolása kívánt országok. 

A szabály az blokkolás /Photos/Strasbourgban/például fájlok például szűrheti:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Országkód
A **földrajzi-szűrés** szolgáltatás országhívó számokat használ, amelyből a kérelem fogja engedélyezett vagy letiltott biztonságos könyvtár országok meghatározására. Megtalálja az ország kódok [Azure CDN országhívószámok](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a id="considerations"></a>Szempontok
* Verizon vagy néhány percig Akamai, a szűrés konfigurálásának életbelépéséhez országa 90 percig is eltarthat.
* Ez a funkció nem támogatja a helyettesítő karakterek (például "*").
* A földrajzi-szűrés konfigurációs társított a következő relatív elérési út rekurzív módon alkalmazza lesz.
* Csak egy szabály alkalmazhatja a azonos relatív elérési (nem hozható létre több ország szűrőket, amelyek ugyanazt az relatív elérési utat. Azonban a mappa több országban szűrő állhat. Ez az ország szűrők rekurzív jellemzői miatt. Más szóval a korábban konfigurált mappa almappája más országban szűrő rendelhetők.


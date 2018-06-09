---
title: Azure-készletben a felügyeleti portál használatával |} Microsoft Docs
description: Azure verem kezelőként útmutató a felügyeleti portálon.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: mabrigg
ms.openlocfilehash: 673b1144fe927e0619f5f8638d7e8ce9a181f48c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248520"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Azure-készletben a felügyeleti portál használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Nincsenek a két portál Azure verem; a rendszergazda és a felhasználói portálhoz (más néven a *bérlői* portal.) Azure verem kezelőként használhatja az adminisztrációs portálhoz a napi szintű felügyeletéért és Azure verem műveletekhez.

## <a name="access-the-administrator-portal"></a>A felügyeleti portál eléréséhez

A fejlesztőkörnyezet kit kell először győződjön meg arról, hogy [a development kit állomáshoz](azure-stack-connect-azure-stack.md) távoli asztali kapcsolat vagy virtuális magánhálózati (VPN) keresztül.

A felügyeleti portál eléréséhez keresse meg a portál URL-címet, és jelentkezzen be Azure verem operátor hitelesítő adataival. Egy integrált rendszer a portál URL-cím változó alapján a régió nevét és külső teljesen minősített tartományneve (FQDN) az Azure Alkalmazásveremben üzembe.

| Környezet | Felügyeleti portál URL-címe |   
| -- | -- | 
| Szoftverfejlesztői készlet| https://adminportal.local.azurestack.external  |
| Integrált rendszerek | https://adminportal.&lt; *régió*&gt;.&lt; *Teljesen minősített Tartományneve*&gt; | 
| | |

 ![A felügyeleti portálon](media/azure-stack-manage-portals/image1.png)

A felügyeleti portálon műveleteket végezheti el, mint:

* kezelheti az infrastruktúra-(beleértve a helyrendszer állapotát, a frissítések, a kapacitás, a stb.)
* A Marketplace feltöltése
* a felhasználók számára előfizetés létrehozása
* tervek és ajánlatok létrehozása

A **gyors üzembe helyezési útmutató** csempe hivatkozások a leggyakoribb feladatokat tartozó online dokumentációt.

Bár egy üzemeltetőt erőforrások hozhat létre például a virtuális gépek, virtuális hálózatok és a felügyeleti portál storage-fiók, akkor [a felhasználói portálra történő bejelentkezéshez](user/azure-stack-use-portal.md) és erőforrások.

>[!NOTE]
>A **hozzon létre egy virtuális gépet** hivatkozás a gyors üzembe helyezési oktatóanyag csempén rendelkezik, akkor hozzon létre egy virtuális gépet a felügyeleti portálon, de ez csak a Azure verem ellenőrzése után először telepítették szolgál.

## <a name="understand-subscription-behavior"></a>Előfizetés viselkedésének megértése

Nincs elérhető a felügyeleti portál csak egyetlen előfizetéssel. Ez az előfizetés a *alapértelmezett szolgáltató előfizetés*. Nem minden más előfizetéseket, és nem használja őket a felügyeleti portálon.

Egy Azure verem operátorként is hozzáadhat előfizetések a felhasználók számára (beleértve a saját kezűleg) felügyeleti portálján. (Önt is), a felhasználók elérhetik és használja ezeket az előfizetéseket a **felhasználói** portálon. A felhasználói portál azonban nem biztosít érheti el a felügyeleti vagy működési funkcióiról az adminisztrációs portálhoz.

A rendszergazda és a felhasználói portálon külön példányokban Azure Resource Manager által támogatott. Ez az erőforrás-kezelő elkülönítés miatt előfizetések nem kereszt-portálok. Például, egy Azure verem operátort, mint a bejelentkezést a felhasználói portálra, akkor nem tud hozzáférni a *alapértelmezett szolgáltató előfizetés*. Bár nem fér bármely felügyeleti funkciókkal, létrehozhat saját előfizetések elérhető nyilvános ajánlatokat. Mindaddig, amíg a felhasználói portálra van bejelentkezve a felhasználó egy bérlői felhasználói minősül.

  >[!NOTE]
  >A development kit környezetben egy felhasználó tagja a bérlő könyvtárába az Azure-verem operátor szükséges, ha azok nem blokkolva vannak a felügyeleti portálra való bejelentkezéskor. Azonban ezek egyikét sem tudja használni a felügyeleti funkciókat. Emellett a felügyeleti portálon nem adhat előfizetések vagy hozzáférést kínál, amelyek számára elérhető a felhasználói portálon.

## <a name="administrator-portal-tips"></a>Felügyeleti portál tippek

### <a name="customize-the-dashboard"></a>Testre szabhatja az irányítópultot

Az irányítópult tartalmaz egy alapértelmezett csempék. Kiválaszthatja **Szerkesztés irányítópult** módosíthatja az alapértelmezett irányítópultot, vagy válasszon **új irányítópult** egyéni irányítópult hozzáadása. Csempék könnyen irányítópult adhat hozzá. Kiválaszthatja például **új**, kattintson a jobb gombbal **kínál + tervek**, majd válassza ki **rögzítés az irányítópulton**.

### <a name="quick-access-to-online-documentation"></a>Online dokumentáció gyors eléréséhez

Az Azure-verem operátor dokumentáció eléréséhez a súgóban és ikon (kérdőjel) támogatja a felügyeleti portál jobb felső sarkában. A kurzor az ikonra, majd válassza ki **súgó + támogatás**.

### <a name="quick-access-to-help-and-support"></a>Súgó és támogatás gyorsan elérheti őket

Ha meg a Súgó és támogatás ikon (kérdőjel) a felügyeleti portál jobb felső sarkában, majd válassza ki és **új támogatja a kérelem**, egyet az alábbi eredményeket fordulhat elő:

- Ha egy integrált rendszer használata esetén ez a művelet megnyitja egy helyet, ahol közvetlenül megnyithatja a támogatási jegy a Microsoft ügyfél támogatja a szolgáltatások (CSS). Tekintse meg [Honnan szerezhetők be támogatási](azure-stack-manage-basics.md#where-to-get-support) megérteni, hogy mikor kell lépjen a Microsoft támogatási szolgálatához vagy az eredeti hardvergyártó (OEM) hardver gyártójával támogatási keresztül.
- A csomag használata, ezzel megnyílik a verem Azure fórumok hely közvetlenül. Ezek a fórumok rendszeresen figyeli. A csomag egy kiértékelési környezete, mert nincs Microsoft CSS kínált hivatalos támogatás.

## <a name="next-steps"></a>További lépések

- [Azure-készletben régió kezelése](azure-stack-region-management.md)

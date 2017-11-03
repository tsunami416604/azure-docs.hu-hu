---
title: "Azure-készletben a felügyeleti portál használatával |} Microsoft Docs"
description: "Azure verem kezelőként útmutató a felügyeleti portálon."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3a1be7a08fab8ad0253f26e6a0683617bff4b7c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Azure-készletben a felügyeleti portál használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Nincsenek a két portál Azure verem; a rendszergazda és a felhasználói portálhoz (más néven a *bérlői* portál). Azure verem kezelőként használhatja az adminisztrációs portálhoz a napi szintű felügyeletéért és Azure verem műveletekhez. 

## <a name="access-the-administrator-portal"></a>A felügyeleti portál eléréséhez

A fejlesztőkörnyezet kit kell először győződjön meg arról, hogy [a development kit állomáshoz](azure-stack-connect-azure-stack.md) távoli asztali kapcsolat vagy virtuális magánhálózati (VPN) keresztül.

A felügyeleti portál eléréséhez keresse meg a portál URL-címet, és jelentkezzen be Azure verem operátor hitelesítő adataival. Egy integrált rendszer a portál URL-cím változó alapján a régió nevét és külső teljesen minősített tartományneve (FQDN) az Azure Alkalmazásveremben üzembe.

| Környezet | Felügyeleti portál URL-címe |   
| -- | -- | 
| Szoftverfejlesztői készlet| https://adminportal.local.azurestack.external  |
| Integrált rendszerek | https://adminportal. &lt; *régió*&gt;.&lt; *Teljesen minősített Tartományneve*&gt; | 
| | |

 ![A felügyeleti portálon](media/azure-stack-manage-portals/image1.png)

A felügyeleti portálon műveleteket végezheti el, mint:

* kezelheti az infrastruktúra-(beleértve a helyrendszer állapotát, a frissítések, a kapacitás, a stb.)
* A piactér feltöltése
* tervek és ajánlatok létrehozása
* a felhasználók számára előfizetés létrehozása

Az a **gyors üzembe helyezési útmutató** csempéjén kattintson a jobb hivatkozást leggyakoribb feladatokat az online dokumentációt.
 
Bár az erőforrások, például a virtuális gépek, a virtuális hálózatok és a storage-fiókok létrehozásáról a felügyeleti portálon operátor képesek, akkor [a felhasználói portálra történő bejelentkezéshez](user/azure-stack-use-portal.md) és erőforrásokat. (A **hozzon létre egy virtuális gépet** hivatkozás a gyors üzembe helyezési oktatóanyag csempén hozhat létre egy virtuális gépet a felügyeleti portálon, de ez az eljárás csak az Azure-verem ellenőrzése a kezdeti telepítés után.)

## <a name="subscription-behavior"></a>Előfizetés viselkedése
 
Nincs elérhető a felügyeleti portálon csak egyetlen előfizetéssel. Ez az előfizetés a *alapértelmezett szolgáltató előfizetés*. A felügyeleti portálon nem adható hozzá bármilyen más előfizetések való használatra.

Egy Azure verem operátorként is hozzáadhat előfizetések a felhasználók számára (beleértve a saját kezűleg) felügyeleti portálján. (Önt is), a felhasználók elérhetik és használja ezeket az előfizetéseket a felhasználói portálon. A felhasználói portál nem biztosít hozzáférést, sem a felügyeleti portál felügyeleti vagy működési képességeit.

A rendszergazda és a felhasználói portálon külön példányokban Azure Resource Manager által támogatott. A Resource Manager való miatt előfizetések nem kereszt-portálok. Például ha Ön Azure verem kezelőként jelentkezik be, a felhasználói portálra, nem használhatja az alapértelmezett szolgáltató előfizetés. Ezért nem rendelkezik felügyeleti funkciók eléréséhez. Létrehozhat saját előfizetések nyilvános ajánlatokat, de egy bérlői felhasználói útmutatóul szolgálnak.

  >[!NOTE]
  A development kit környezetben egy felhasználó tagja a bérlő könyvtárába az Azure-verem operátor szükséges, ha azok nem blokkolva vannak a felügyeleti portálra való bejelentkezéskor. Azonban ezek egyikét sem tudja használni a felügyeleti funkciókat. Emellett a felügyeleti portálon nem adhat előfizetések vagy hozzáférést kínál, amely érhetik el a felhasználói portálon.

## <a name="administrator-portal-tips"></a>Felügyeleti portál tippek

### <a name="customize-the-dashboard"></a>Testre szabhatja az irányítópultot

Az irányítópult tartalmaz egy alapértelmezett csempék. Kattinthat **Szerkesztés irányítópult** segítségével módosíthatja az alapértelmezett irányítópultot, vagy kattintson **új irányítópult** egyéni irányítópultok felvételéről. Az irányítópult könnyen is vegyen fel csempéket. Kattintson például **új**, kattintson a jobb gombbal **kínál + tervek**, és kattintson a **rögzítés az irányítópulton**.

### <a name="quick-access-to-online-documentation"></a>Online dokumentáció gyors eléréséhez

Az Azure-verem operátor dokumentáció eléréséhez kattintson a Súgó és támogatás ikonra (kérdőjel) a felügyeleti portál jobb felső sarkában, és kattintson **súgó + támogatás**.

### <a name="quick-access-to-help-and-support"></a>Súgó és támogatás gyorsan elérheti őket

Ha a felügyeleti portál jobb felső sarkában kattintson a Súgó és támogatás ikonra (kérdőjel), és kattintson a **új támogatja a kérelem**, ez does az alábbiak valamelyikét:

- Ha egy integrált rendszer használata esetén ez a művelet megnyitja egy helyet, ahol közvetlenül megnyithatja a támogatási jegy a Microsoft ügyfél támogatja a szolgáltatások (CSS). Tekintse át a "Hol kaphat segítséget" részt a [Azure verem Adminisztráció alapjai](azure-stack-manage-basics.md) megérteni, hogy mikor kell lépjen a Microsoft támogatási szolgálatához vagy az eredeti hardvergyártó (OEM) hardver gyártójával támogatási keresztül.
- A csomag használata, ezzel megnyílik a verem Azure fórumok hely közvetlenül. Ezek a fórumok rendszeresen figyeli. A csomag egy kiértékelési környezete, mert nincs Microsoft CSS kínált hivatalos támogatás.

## <a name="next-steps"></a>Következő lépések

- [Azure-készletben régió kezelése](azure-stack-region-management.md)

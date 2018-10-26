---
title: A felügyeleti portál használatával az Azure Stackben |} A Microsoft Docs
description: Az Azure Stack operátorait szerint megtudhatja, hogyan használhatja a felügyeleti portált.
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
ms.date: 10/23/2018
ms.author: mabrigg
ms.openlocfilehash: 4453516626c39f73deea8fce0d744fcecbd5c3fc
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085817"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>A felügyeleti portál használatával az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Nincsenek két portált az Azure Stackben; a felügyeleti portál és a felhasználói portál (más néven a *bérlői* portálon.) A napi szintű felügyeletéért és műveletek az Azure Stack az Azure Stack operátorait szerint használhatja a felügyeleti portálon.

## <a name="access-the-administrator-portal"></a>A felügyeleti portál elérése

A fejlesztőkörnyezet kit kell először győződjön meg arról, hogy [development kit állomás csatlakozni](azure-stack-connect-azure-stack.md) távoli asztali kapcsolaton keresztül, vagy virtuális magánhálózati (VPN) keresztül.

A felügyeleti portál eléréséhez keresse meg a portál URL-címet, és jelentkezzen be az Azure Stack operátorait hitelesítő adataival. Az integrált rendszer a portál URL-cím változik alapul a régió neve és a külső teljesen minősített tartománynevét (FQDN) az Azure Stack üzembe helyezés.

| Környezet | Felügyeleti portál URL-címe |   
| -- | -- | 
| Fejlesztői készlete| https://adminportal.local.azurestack.external  |
| Integrált rendszerek | https://adminportal.&lt; *régió*&gt;.&lt; *Teljes Tartományneve*&gt; | 
| | |

 ![Az adminisztrátori portál](media/azure-stack-manage-portals/admin-portal.png)

Vegye figyelembe, hogy az összes Azure Stack üzemelő példányhoz az alapértelmezett időzóna értéke az egyezményes világidő (UTC). Választhat egy időzóna telepítésekor az Azure Stack, azonban automatikusan visszaáll az UTC Időzóna alapértelmezés szerint a telepítés során.

A felügyeleti portálon lehetőség van például:

* Kezelheti az infrastruktúra-(beleértve a rendszer állapotát, a frissítések, a kapacitás, stb.)
* A Marketplace feltöltése
* A felhasználók számára előfizetés létrehozása
* Csomag és ajánlat létrehozása

A **a rövid útmutató** csempe hivatkozások a leggyakoribb feladatokat az online dokumentációt.

Bár az operátornak rendelkezik erőforrásokat hozhat létre például a virtuális gépek, virtuális hálózatok és tárfiókok a felügyeleti portálon, akkor [jelentkezzen be a felhasználói portál](user/azure-stack-use-portal.md) hozhat létre, és tesztelje az erőforrásokat.

>[!NOTE]
>A **hozzon létre egy virtuális gépet** csatolása a rövid útmutató csempe rendelkezik, hozzon létre egy virtuális gépet a felügyeleti portálon, de ez csak a ellenőrzése az Azure Stack, először üzembe helyezését követően szolgál.

## <a name="understand-subscription-behavior"></a>Előfizetés a viselkedés értelmezése

Nincs elérhető legyen a rendszergazdai portálon csak egyetlen előfizetéssel. Ez az előfizetés a *szolgáltatói előfizetés alapértelmezett*. Nem lehet bármely más előfizetéseket, és használja azokat a felügyeleti portálon.

Az Azure Stack operátorait szerint adhat hozzá az előfizetések (beleértve a saját maga) a felhasználók számára a felügyeleti portálról. Felhasználókat (beleértve a saját maga) eléri és használja ezeket az előfizetéseket a **felhasználói** portálon. A felhasználói portál azonban nem biztosít hozzáférést a felügyeleti portál felügyeleti vagy működési képességeit.

A rendszergazda és a felhasználói portált különálló példány az Azure Resource Manager élvezik. Az erőforrás-kezelő elkülönítés miatt előfizetések nem lépi túl portálokat. Például, mint az Azure Stack operátorait jelentkezik be a felhasználói portálra, ha nem fér hozzá a *szolgáltatói előfizetés alapértelmezett*. Bár nem kell minden olyan felügyeleti funkciók elérését, létrehozhat saját előfizetések elérhető nyilvános ajánlatokat. Mindaddig, amíg a felhasználói portálra van bejelentkezve, egy bérlő felhasználói minősülnek.

  >[!NOTE]
  >Development kit a környezetben ha egy felhasználó tartozik, az Azure Stack-operátorokról bérlői könyvtárába, nincs letiltva a jelentkezik be a felügyeleti portálon. Azonban nem tudják elérni a felügyeleti funkciók bármelyikét. Emellett a felügyeleti portálról, azok nem vehető fel előfizetések vagy hozzáférést kínál, amelyek számára elérhető a felhasználói portálon.

## <a name="administrator-portal-tips"></a>Felügyeleti portál tippek

### <a name="customize-the-dashboard"></a>Az irányítópult testreszabásával

Az irányítópult tartalmaz az alapértelmezett csempék jelennek meg. Választhat **irányítópult szerkesztése** módosítsa az alapértelmezett irányítópult, vagy válasszon **új irányítópult** egyéni irányítópult hozzáadása. Egyszerűen hozzáadhatja csempét az irányítópulton. Kiválaszthatja például **+ erőforrás létrehozása**, kattintson a jobb gombbal **ajánlatok és csomagok**, majd válassza ki **rögzítés az irányítópulton**.

Egyes esetekben jelenhet meg egy üres irányítópult, a portálon. Az irányítópult helyreállítani, kattintson **irányítópult szerkesztése**, majd kattintson a jobb gombbal, és válassza ki **visszaállítja az alapértelmezett állapotba**.

### <a name="quick-access-to-online-documentation"></a>Online dokumentáció gyors elérése

Hozzáférhet az Azure Stack operátori dokumentációja, használja a Súgó és ikonra (kérdőjel) támogatja a felügyeleti portál jobb felső sarkában. Helyezze a kurzort a ikonra, és válassza ki **súgó + támogatás**.

### <a name="quick-access-to-help-and-support"></a>Súgó és támogatás gyors elérése

Ha a felügyeleti portál jobb felső sarkában válassza ki a Súgó és támogatás ikonra (kérdőjel), és válassza **új támogatási kérelem**, egyet az alábbi eredményeket fordulhat elő:

- Integrált rendszer használja, ha ez a művelet megnyitja a hely, ahol közvetlenül megnyithatja egy támogatási jegyet a Microsoft ügyfél támogatási szolgálat (CSS). Tekintse meg [kérhet támogatást Where](azure-stack-manage-basics.md#where-to-get-support) megértéséhez, hogy mikor kell lépjen a Microsoft támogatási vagy a számítógépgyártó (OEM) hardver szállítójával támogatási keresztül.
- Ha a csomag használata esetén ez a művelet közvetlenül megnyitja az Azure Stack-fórumok. Ezek a fórumok rendszeresen figyeli a program. Mivel a csomag egy kiértékelési környezete, rendszer nem hivatalos támogatja a Microsoft CSS keresztül érhető el.

## <a name="next-steps"></a>További lépések

- [Régiók kezelése az Azure Stackben](azure-stack-region-management.md)

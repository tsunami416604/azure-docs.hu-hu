---
title: Adatvisszajelzés küldése az Azure Maps nek | Microsoft Azure Maps
description: Adatvisszajelzést küldhet a Microsoft Azure Maps visszajelzési eszközével.
author: philmea
ms.author: philmea
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 92469370f1ea64f5ee1bc2a84e47cc8b294b5375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335349"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Adatvisszajelzés küldése az Azure Maps nek

Az Azure Maps 2018 májusa óta érhető el. Az Azure Maps friss térképadatokat, könnyen használható REST API-kat és hatékony SDK-kat biztosít, hogy különböző típusú üzleti használati esetekben támogassa a vállalati ügyfeleket. A való világ minden másodpercben változik, és rendkívül fontos számunkra, hogy tényszerű digitális képviseletet biztosítsunk ügyfeleink számára. A létesítmények megnyitását vagy bezárását tervező ügyfeleinknek azonnal frissíteniük kell térképeinket. Így hatékonyan tervezhetik meg a szállítást, a karbantartást vagy az ügyfélszolgálatot a megfelelő létesítményekben. Azért hoztuk létre az Azure Maps adatvisszajelzési webhelyét, hogy ügyfeleink közvetlen adatvisszajelzést kapjanak. Az ügyfelek adatvisszajelzései közvetlenül adatszolgáltatóinkhoz és térképszerkesztőikhez szólnak. Gyorsan kiértékelhetik és beépíthetik a visszajelzéseket térképészeti termékeinkbe.  

[Az Azure Maps Data visszajelzési webhelye](https://feedback.azuremaps.com) egyszerű módot kínál ügyfeleink számára, hogy térképadatokkal visszajelzést adjanak, különösen az üzleti érdeklődési pontokon és a lakócímeken. Ez a cikk bemutatja, hogyan adhat meg különböző típusú visszajelzéseket az Azure Maps visszajelzési webhelyhasználatával.

## <a name="add-a-business-place-or-a-residential-address"></a>Üzleti hely vagy lakcím hozzáadása 

Érdemes lehet visszajelzést adni egy hiányzó érdekes pontról vagy egy lakcímről. Ezt kétféleképpen lehet megtenni. Nyissa meg az Azure Map adatvisszajelzési webhelyét, keresse meg a hiányzó hely koordinátáit, majd kattintson a "Hely hozzáadása" gombra.

  ![keresés hiányzó helye](./media/how-to-use-feedback-tool/search-poi.png)

Vagy, akkor kölcsönhatásba léphet a térképen. Kattintson a helyre, hogy csepp egy pin a koordináta, és kattintson a "Add a hely".

  ![pin hozzáadása](./media/how-to-use-feedback-tool/add-poi.png)

A kattintáskor a lehetőség egy űrlapra irányítja, hogy megadja a hely megfelelő adatait.

  ![hely hozzáadása](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Munkahely vagy lakcím javítása 

A visszajelzési webhely lehetővé teszi egy üzleti hely vagy cím keresését és megkeresését is. Visszajelzést küldhetsz a cím vagy a pin helyének javításához, ha azok nem megfelelőek. Ha visszajelzést szeretne küldeni a cím javításához, a keresősávsegítségével keressen meg egy üzleti helyet vagy lakcímet. Kattintson az érdeklődési helyére az eredménylistában. Kattintson a "Fix ez a hely".

  ![javításra hely](./media/how-to-use-feedback-tool/fix-place.png)

Ha visszajelzést szeretne adni a cím javításához, töltse ki a "Hely javítása" űrlapot, majd kattintson a "küldés" gombra.

  ![űrlap javítása](./media/how-to-use-feedback-tool/fix-form.png)

Ha a hely pin helye hibás, jelölje be a "Hely javítása" űrlap "A pin helye helytelen" jelölőnégyzetet. Mozgassa a tűt a megfelelő helyre, majd kattintson a "küldés" gombra.

  ![pin helyének áthelyezése](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Megjegyzés hozzáadása 

A visszajelzési eszköz lehetővé teszi a hely keresését is, és lehetővé teszi, hogy a helyhez kapcsolódó részletekhez szabad formátumú szöveges megjegyzést fűzzön. Megjegyzés hozzáadásához keresse meg a helyet, vagy kattintson a helyre. Kattintson a "Megjegyzés hozzáadása" gombra, írjon megjegyzést, majd kattintson a "Küldés" gombra.

  ![hozzászólás hozzáadása](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Állapot nyomon követése 

A kérés állapotát a "Nyomon akarom követni az állapotot" négyzetbe bejelölve, és kérés közben megadva az e-mail címét. Az e-mailben egy nyomon követési linket fog kapni, amely a kérés naprakész állapotát tartalmazza. 

  ![visszajelzés állapota](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>További lépések

Az Azure Mapsszolgáltatással kapcsolatos technikai kérdések közzétételéhez látogasson el a következő webhelyre:

* [Az Azure Maps veremtúlcsordulása](https://stackoverflow.com/questions/tagged/azure-maps)
* [Az Azure Maps visszajelzési fóruma](https://feedback.azure.com/forums/909172-azure-maps)

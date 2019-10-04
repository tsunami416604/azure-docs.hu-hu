---
title: Adatvisszajelzés biztosítása a Azure Maps számára | Microsoft Docs
description: Adatvisszajelzés biztosítása Azure Maps visszajelzési eszköz használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 076f98cb240014bcc88a395902203413e31fe0f1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642122"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Adatvisszajelzés biztosítása Azure Maps

A Azure Maps a május 2018 óta általánosan elérhető, amely friss térképi adatokkal, könnyen használható REST API-kkal és nagy teljesítményű SDK-kkal segíti a nagyvállalati ügyfeleket a különböző üzleti használati esetekben. A valós világ minden másodpercen át változik, és fontos számunkra, hogy tényszerű digitális képviseletet nyújtson ügyfeleinknek. Azon ügyfeleinknek, akik nyitva vagy le szeretnék nyitni a létesítményeket, gondoskodni kell arról, hogy a Maps frissítése azonnal megtörténjen, hogy hatékonyan tervezzenek a szállításra, a karbantartásra vagy az ügyfélszolgálatra a megfelelő létesítményekben. Létrehoztuk a Azure Maps adatvisszajelzési eszközt, amely lehetővé teszi ügyfeleink számára, hogy közvetlen adatvisszajelzéseket nyújtsanak. Az ügyfelek adatvisszajelzése közvetlenül az adatszolgáltatókhoz és azok Térkép-szerkesztőjéhez kapcsolódik, akik gyorsan kiértékelik és beépíthetik a leképezési termékekre vonatkozó visszajelzéseket.  

[Azure Maps adatvisszajelzési eszköz](https://feedback.azuremaps.com) egyszerű módszert kínál ügyfeleinknek a térképes adatvisszajelzések megadására, különösen az üzleti pontokra és a lakossági címekre. Ez a cikk bemutatja, hogyan biztosíthat különböző visszajelzést a Azure Maps visszajelzési eszköz használatával.

## <a name="add-a-business-place-or-a-residential-address"></a>Üzleti hely vagy lakcím hozzáadása 

Előfordulhat, hogy visszajelzést szeretne küldeni a Térkép hiányzó hasznos pontjáról vagy lakcíméről. Ezt kétféleképpen teheti meg, nyissa meg az Azure Map adatvisszajelzési eszközét, és keresse meg a hiányzó hely koordinátáit, majd kattintson a "hely hozzáadása" gombra.

  ![hiányzó hely keresése](./media/how-to-use-feedback-tool/search-poi.png)

Vagy használhatja a térképet, és a helyre kattintva elhúzhatja a PIN-kódot a koordinátákon, és kattintson a "hely hozzáadása" gombra. 

  ![PIN-kód hozzáadása](./media/how-to-use-feedback-tool/add-poi.png)

Ha rákattint, a rendszer egy űrlapra irányítja, hogy megadja a helyhez tartozó adatokat.

  ![hely hozzáadása](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Üzleti vagy lakossági címek javítása 

A visszajelzési eszköz lehetővé teszi, hogy megkeresse és keresse meg a munkahelyi helyet vagy a címeket, és visszajelzést küldjön a címnek vagy a PIN-kód helyének kijavításához, ha azok nem megfelelőek. Ha visszajelzést szeretne küldeni a címnek a kijavításához, használja a keresősáv-t egy üzleti hely vagy egy lakcím megkereséséhez. Kattintson a kívánt helyre a találatok listájában, és kattintson a "megoldás kijavítása" gombra.

  ![keresési hely a javításhoz](./media/how-to-use-feedback-tool/fix-place.png)

Ha visszajelzést szeretne küldeni a címe kijavításához, töltse ki a "hely kijavítása" űrlapot, és kattintson a "submit" (küldés) gombra.

  ![űrlap javítása](./media/how-to-use-feedback-tool/fix-form.png)

Ha a hely PIN-kódjának helye nem megfelelő, jelölje be az "olyan hely kijavítása" űrlap jelölőnégyzetét, amely szerint "a PIN-kód helye helytelen", és helyezze át a PIN-kódot a megfelelő helyre, majd kattintson a Submit (küldés) gombra.

  ![PIN-kód helyének áthelyezése](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Megjegyzés hozzáadása 

A tartózkodási hely keresése mellett a visszajelzési eszköz is lehetővé teszi, hogy a helyhez kapcsolódó részletekhez adjon hozzá egy ingyenes szöveges megjegyzést. Ha megjegyzést szeretne adni a helyhez, vagy kattintson a "Megjegyzés hozzáadása" gombra, írja be a megjegyzést, és kattintson a "Küldés" gombra. 

  ![Megjegyzés hozzáadása](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Állapotkövetés 

A kérés állapotát nyomon követheti is, ha az "állapot nyomon követése" négyzet bejelölésével bejelöli az e-mail-címet a kérelem végrehajtása közben. A rendszer egy nyomkövetési hivatkozást fog kapni az e-mailben, amely naprakész állapotot biztosít a kérelme számára. 

  ![visszajelzés állapota](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>További lépések

Azure Maps kapcsolatos technikai kérdések közzétételéhez látogasson el ide:

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure Maps visszajelzési fórum](https://feedback.azure.com/forums/909172-azure-maps)
---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f860a0cebefaebe16c40abf008bd24e333eb80d5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

1. Keresse meg, és másolja a *.pfx* fájlt az ügyfélszámítógépre. Az ügyfélszámítógépen kattintson duplán a *.pfx* fájlra annak telepítéséhez. Hagyja meg a **Tárolás helye** esetében az **Aktuális felhasználó** értéket, és kattintson a **Tovább** gombra.
2. A **Fájl** importálása lapon nem kell semmit módosítania. Kattintson a **Tovább** gombra.
3. Az a **kulcsvédelem** lapon adjon meg a jelszót a tanúsítványhoz, vagy győződjön meg arról, hogy a rendszerbiztonsági tag megfelelő, majd kattintson **következő**.
4. A **Tanúsítványtároló** lapon ne módosítsa az alapértelmezett helyet, majd kattintson a **Tovább** gombra.
5. Kattintson a **Befejezés** gombra. A tanúsítványtelepítés **Biztonsági figyelmeztetés** párbeszédpanelén kattintson az **Igen** gombra. Nyugodtan rákattinthat az Igenre, mivel már létrehozta a tanúsítványt. A rendszer ezután sikeresen importálja a tanúsítványt.
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
ms.openlocfilehash: 0068bd151c3d7d243b05c326ec73a201f4131296
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146811"
---
Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

1. Keresse meg, és másolja a *.pfx* fájlt az ügyfélszámítógépre. Az ügyfélszámítógépen kattintson duplán a *.pfx* fájlra annak telepítéséhez. Hagyja meg a **Tárolás helye** esetében az **Aktuális felhasználó** értéket, és kattintson a **Tovább** gombra.
2. A **Fájl** importálása lapon nem kell semmit módosítania. Kattintson a **tovább**.
3. Az a **titkos kulcs védelme** lapon adja meg a tanúsítvány jelszavát, vagy győződjön meg arról, hogy a rendszerbiztonsági tag megfelelő, majd kattintson a **tovább**.
4. A **Tanúsítványtároló** lapon ne módosítsa az alapértelmezett helyet, majd kattintson a **Tovább** gombra.
5. Kattintson a **Befejezés**gombra. A tanúsítványtelepítés **Biztonsági figyelmeztetés** párbeszédpanelén kattintson az **Igen** gombra. Nyugodtan rákattinthat az Igenre, mivel már létrehozta a tanúsítványt. A rendszer ezután sikeresen importálja a tanúsítványt.
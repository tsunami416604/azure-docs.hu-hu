---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 05/09/2018
ms.date: 06/04/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 9c4910d9ec1fdf651b1bc29955a4e1de2a775f7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576153"
---
1. Kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságainak lapján. 
2. Az a **csatlakozhat a virtuális gép** lapon tartsa válassza ki a megfelelő beállításokat, és kattintson **töltse le az RDP-fájl**.
2. Nyissa meg a letöltött RDP-fájlt, és kattintson a **Csatlakozás** gombra, amikor a rendszer erre kéri. 
2. Egy figyelmeztetés fog megjelenni arról, hogy az `.rdp`-fájl közzétevője ismeretlen. Ez nem jelent problémát. A Távoli asztal ablakában kattintson a **Csatlakozás** gombra a folytatáshoz.

    ![Képernyőkép az ismeretlen közzétevőre vonatkozó figyelmeztetésről.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a virtuális gép egy fiókjának a hitelesítő adatait, majd kattintson az **OK** gombra.

     **Helyi fiók** – ez általában a helyi fiók felhasználójának neve és jelszava, amelyet a virtuális gép létrehozásakor adott meg. Ebben az esetben a tartomány a virtuális gép neve, és a következő módon van megadva: *vmname*&#92;*username*.  

    **Tartományhoz csatlakoztatott virtuális gép** – ha a virtuális gép egy tartományhoz tartozik, a felhasználó nevét a következő formátumban adja meg: *Domain*&#92;*Username*. A fióknak szerepelnie kell a Rendszergazdák csoportban, vagy távoli hozzáférési jogosultságokkal kell rendelkeznie a virtuális géphez.

    **Tartományvezérlő** – ha a virtuális gép egy tartományvezérlő, írja be egy, a tartományhoz tartozó tartományi rendszergazdai fiók felhasználónevét és jelszavát.
4. Kattintson az **Igen** gombra a virtuális gép identitásának ellenőrzéséhez és a bejelentkezés befejezéséhez.

   ![Képernyőkép a virtuális gép identitásának ellenőrzésére vonatkozó üzenetről.](./media/virtual-machines-log-on-win-server/cert-warning.png)

---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 842083f0f69552fb9076423386353dbb4dae73b5
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
1. Kattintson a **Connect** gombra a virtuális gép tulajdonságok lapján. 
2. Az a **kapcsolódás a virtuális gép** lapon hagyja válassza ki a megfelelő beállításokat, majd kattintson **letöltése RDP-fájl**.
2. Nyissa meg a letöltött RDP-fájlt, és kattintson a **Connect** megjelenésekor. 
2. Egy figyelmeztetés fog megjelenni arról, hogy az `.rdp`-fájl közzétevője ismeretlen. Ez nem jelent problémát. A Távoli asztal ablakában kattintson a **Csatlakozás** gombra a folytatáshoz.
   
    ![Képernyőkép az ismeretlen közzétevőre vonatkozó figyelmeztetésről.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a virtuális gép egy fiókjának a hitelesítő adatait, majd kattintson az **OK** gombra.
   
     **Helyi fiók** – ez általában a helyi fiók felhasználójának neve és jelszava, amelyet a virtuális gép létrehozásakor adott meg. Ebben az esetben a tartomány a virtuális gép neve, és a következő módon van megadva: *vmname*&#92;*username*.  
   
    **Tartományhoz csatlakoztatott virtuális gép** – ha a virtuális gép egy tartományhoz tartozik, a felhasználó nevét a következő formátumban adja meg: *Domain*&amp;#92;*Username*. A fióknak szerepelnie kell a Rendszergazdák csoportban, vagy távoli hozzáférési jogosultságokkal kell rendelkeznie a virtuális géphez.
   
    **Tartományvezérlő** – ha a virtuális gép egy tartományvezérlő, írja be egy, a tartományhoz tartozó tartományi rendszergazdai fiók felhasználónevét és jelszavát.
4. Kattintson az **Igen** gombra a virtuális gép identitásának ellenőrzéséhez és a bejelentkezés befejezéséhez.
   
   ![Képernyőkép a virtuális gép identitásának ellenőrzésére vonatkozó üzenetről.](./media/virtual-machines-log-on-win-server/cert-warning.png)


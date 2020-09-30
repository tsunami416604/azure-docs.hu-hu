---
title: Az Azure VMware-megoldás Jump Box létrehozása
description: Az Azure VMware-megoldás Jump Box létrehozásának lépései.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 5d5a5d82cf6e70e7ad55bcfa10c7f85372131c87
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578417"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. Az erőforráscsoport területen válassza a **+ Hozzáadás** lehetőséget, majd válassza a **Microsoft Windows 10**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Új Windows 10-es virtuális gép hozzáadása egy Jump Box-hoz." border="true":::

1. Adja meg a szükséges adatokat a mezőkben, majd válassza a **felülvizsgálat + létrehozás**elemet. 

   A mezőkkel kapcsolatos további információkért tekintse meg a következő táblázatot.

   | Mező | Érték |
   | --- | --- |
   | **Előfizetés** | Az érték előre fel van töltve az erőforráscsoporthoz tartozó előfizetéssel. |
   | **Erőforráscsoport** | Az érték előre ki van töltve az aktuális erőforráscsoporthoz, amelyet az előző oktatóanyagban hozott létre.  |
   | **Virtuális gép neve** | Adjon meg egy egyedi nevet a virtuális gép számára. |
   | **Régió** | Válassza ki a virtuális gép földrajzi helyét. |
   | **Rendelkezésre állási beállítások** | Hagyja bejelölve az alapértelmezett értéket. |
   | **Rendszerkép** | Válassza ki a virtuális gép rendszerképét. |
   | **Méret** | Hagyja meg az alapértelmezett méret értéket. |
   | **Hitelesítés típusa**  | Válassza a **jelszó**lehetőséget. |
   | **Felhasználónév** | Adja meg a virtuális gépre való bejelentkezéshez használt felhasználónevet. |
   | **Jelszó** | Adja meg a virtuális gépre való bejelentkezéshez használt jelszót. |
   | **Jelszó megerősítése** | Adja meg a virtuális gépre való bejelentkezéshez használt jelszót. |
   | **Nyilvános bejövő portok** | Válassza a **Nincs** lehetőséget. Ha a nincs lehetőséget választja, a [JIT-hozzáférés](../../security-center/security-center-just-in-time.md#jit-configure) használatával szabályozhatja a virtuális gép elérését, ha azt szeretné elérni.  |


1. Az ellenőrzés után válassza a **Létrehozás** lehetőséget a virtuális gép létrehozási folyamatának elindításához.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Új Windows 10-es virtuális gép hozzáadása egy Jump Box-hoz." border="true":::
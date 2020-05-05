---
title: Oktatóanyag – a privát felhő elérésének megismerése
description: Ismerje meg, hogyan érheti el az Azure VMware-megoldás (AVS) privát felhőjét
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fcee332818c89d9c8b00795dca9e74f68260eefd
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740191"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>Oktatóanyag: az Azure VMware-megoldás (AVS) privát felhő elérésének megismerése

Az előzetes verzióban az AVS nem teszi lehetővé, hogy a helyszíni vCenter kezelhesse a saját felhőjét. További telepítést és kapcsolódást kell végrehajtania egy helyi vCenter-példányhoz egy Jump Box használatával. 

Ebben az oktatóanyagban egy Windows rendszerű virtuális gépet hoz létre egy Jump Box számára az előző oktatóanyagban létrehozott erőforráscsoporthoz [: konfigurálja a hálózatkezelést a VMware Private Cloud-ban az Azure-ban](tutorial-configure-networking.md) , és jelentkezzen be a vCenter. Ez egy virtuális gép, amely az Ön által létrehozott virtuális hálózaton található, és hozzáférést biztosít a vCenter és a NSX Managerhez. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A vCenter való kapcsolódáshoz használandó Windows rendszerű virtuális gép létrehozása
> * Bejelentkezés a vCenter a virtuális gépről

## <a name="create-a-new-windows-virtual-machine"></a>Új Windows rendszerű virtuális gép létrehozása

Az erőforráscsoport területen válassza a **+ Hozzáadás** , majd a keresés lehetőséget, és válassza a **Microsoft Windows 10**elemet, majd kattintson a **Létrehozás**gombra.

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Új Windows 10-es virtuális gép hozzáadása Jumpbox" border="true":::

Adja meg a szükséges adatokat a mezőkben, majd válassza a **felülvizsgálat + létrehozás**elemet. A mezőkkel kapcsolatos további információkért tekintse meg a következő táblázatot.

| Mező | Érték |
| --- | --- |
| **Előfizetés** | Ez az érték már fel van töltve azzal az előfizetéssel, amelyhez az erőforráscsoport tartozik. |
| **Erőforráscsoport** | Ez az érték már fel van töltve az aktuális erőforráscsoporthoz. Ez lehet az előző oktatóanyagban létrehozott erőforráscsoport. |
| **Virtuális gép neve** | Adjon meg egy egyedi nevet a virtuális gép számára. |
| **Régió** | Válassza ki a virtuális gép földrajzi helyét. |
| **Rendelkezésre állási beállítások** | Hagyja bejelölve az alapértelmezett értéket. |
| **Kép** | Válassza ki a virtuális gép rendszerképét. |
| **Méret** | Hagyja meg az alapértelmezett méret értéket. |
| **Hitelesítés típusa**  | Válassza a **jelszó**lehetőséget. |
| **Username** | Adja meg a virtuális gépre való bejelentkezéshez használt felhasználónevet. |
| **Jelszó** | Adja meg a virtuális gépre való bejelentkezéshez használt jelszót. |
| **Jelszó megerősítése** | Adja meg a virtuális gépre való bejelentkezéshez használt jelszót. |
| **Nyilvános bejövő portok** | Válassza a **Nincs** lehetőséget. Ha a nincs lehetőséget választja, a [JIT-hozzáférés](../security-center/security-center-just-in-time.md#configure-jit-access-from-an-azure-vms-page-) használatával szabályozhatja a virtuális gép elérését, ha azt szeretné elérni.  |

A megfelelő információk megadása után kattintson a **felülvizsgálat + létrehozás**gombra. Az ellenőrzés után válassza a **Létrehozás** lehetőséget a virtuális gép létrehozási folyamatának elindításához.

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Új Windows 10 rendszerű virtuális gép létrehozása Jumpbox" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Kapcsolódás a saját felhő helyi vCenter

A Jump Box használatával jelentkezzen be a vSphere-ügyfélbe VMware vCenter SSO-val. Jelentkezzen be a vSphere-ügyfélbe egy Felhőbeli rendszergazda felhasználóneve használatával. fogadja el a biztonsági kockázatot, és folytassa, ha megjelenik egy figyelmeztetés a lehetséges biztonsági kockázatról; Jelentkezzen be a VMware vCenter egyszeri bejelentkezési hitelesítő adataival, és ellenőrizze, hogy a felhasználói felület sikeresen megjelenik-e.

A Azure Portal válassza ki saját felhőjét, majd az **Áttekintés** nézetben válassza az **identitás > alapértelmezett**lehetőséget. Megjelenik a Private Cloud vCenter és NSX-T Manager URL-címei és bejelentkezési hitelesítő adatai.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Privát Felhőbeli vCenter és NSX-kezelő URL-címek és hitelesítő adatok megjelenítése" border="true":::

Navigáljon az előző lépésben létrehozott virtuális gépre, és kapcsolódjon a virtuális géphez. A virtuális géphez való kapcsolódás részletes lépéseiért lásd: [Kapcsolódás virtuális géphez](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

A Windows rendszerű virtuális gépen nyisson meg egy böngészőt, és navigáljon a vCenter és a NSX-T jászol URL-címeihez két lapon. A vCenter lapon adja meg az `cloudadmin@vmcp.local` előző lépésben használt felhasználói hitelesítő adatokat.

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Bejelentkezés a Private Cloud vCenter" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter-portál" border="true":::

A böngésző második lapján jelentkezzen be a NSX-T Managerbe.

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Helyi Private Cloud NSX Manger kezdőlapja" border="true":::

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A vCenter való kapcsolódáshoz használandó Windows rendszerű virtuális gép létrehozása
> * Bejelentkezés a vCenter a virtuális gépről

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan méretezhető az AVS Private Cloud.

> [!div class="nextstepaction"]
> [Az AVS Private Cloud méretezése](tutorial-scale-private-cloud.md)
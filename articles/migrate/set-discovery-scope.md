---
title: A VMware virtuális gép felderítési hatókörének beállítása Azure Migrate
description: Ismerteti, hogyan állítható be a felderítési hatókör a VMware virtuális gépek felméréséhez és a Azure Migrate-re való áttelepítéshez.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337635"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Felderítési hatókör beállítása a VMware virtuális gépekhez

Ez a cikk azt ismerteti, hogyan lehet korlátozni a [Azure Migrate berendezés](migrate-appliance-architecture.md)által felderített VMWare virtuális gépek felderítésének hatókörét.

A Azure Migrate készülék felfedi a helyszíni VMware virtuális gépeket, ha: 

- A [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával értékelheti a VMWare virtuális gépeket az Azure-ba való áttelepítéshez.
- Használja a [Azure Migrate: Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszközt a VMWare virtuális gépek [ügynök nélküli áttelepítéséhez](server-migrate-overview.md) az Azure-ba.

## <a name="set-discovery-scope"></a>Felderítési hatókör beállítása


Miután beállította a Azure Migrate készüléket a VMware virtuális gépek felméréséhez vagy áttelepítéséhez, a készülék megkezdi a virtuális gépek felfedését és a virtuális gép metaadatainak küldését az Azure-ba. Mielőtt csatlakoztatná a berendezést a felderítéshez vCenter Serverhoz, beállíthatja a felderítési hatókört úgy, hogy korlátozza a felderítést a vCenter Server adatközpontok, fürtök, fürtök, gazdagépek, gazdagépek vagy egyéni virtuális gépek mappája számára.

A hatókör beállításához engedélyeket kell rendelnie az Azure Migrate által használt fiókhoz a vCenter Server eléréséhez.

## <a name="create-a-vcenter-user-account"></a>VCenter felhasználói fiók létrehozása

Ha még nem állított be olyan vCenter felhasználói fiókot, amelyet a Azure Migrate készülék használ a VMware virtuális gépek felderítésére, értékelésére és áttelepítésére, tegye meg az elsőt.

1.    Jelentkezzen be a vSphere webes ügyfélprogramba vCenter Server rendszergazdaként.
2.    Válassza az **Adminisztráció** > **SSO-felhasználók és csoportok**lehetőséget, majd kattintson a **felhasználók** fülre.
3.    Válassza az **új felhasználó** ikont.
4.    Adja meg az új felhasználói adatokat > **OK gombra**.

A fiók engedélyei a telepítéstől függenek.

**Szolgáltatás** | **Fiók engedélyei**
--- | ---
[Felmérése](tutorial-assess-vmware.md)| A fióknak csak olvasási hozzáférésre van szüksége.
[Alkalmazások/szerepkörök/szolgáltatások felderítése](how-to-discover-applications.md) | A fióknak csak olvasási hozzáférésre van szüksége, és a virtuális gépekhez engedélyezett jogosultságokkal > vendég műveleteket.
[Függőségek elemzése (ügynök nélküli)](how-to-create-group-machine-dependencies-agentless.md) | A fióknak csak olvasási hozzáférésre van szüksége, és a virtuális gépekhez engedélyezett jogosultságokkal > vendég műveleteket.
[Migrálás (ügynök nélkül)](tutorial-migrate-vmware.md) | Szüksége van egy olyan szerepkörre, amely a megfelelő engedélyekhez van rendelve.<br/><br/> Szerepkör létrehozásához jelentkezzen be a vSphere webes ügyfelére vCenter Server rendszergazdaként. Válassza ki a vCenter Server példányt > **hozzon létre szerepkört**. Adja meg a szerepkör nevét, például <em>Azure_Migrate</em>, és rendelje hozzá a [szükséges engedélyeket](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) a szerepkörhöz.


## <a name="assign-permissions-on-vcenter-objects"></a>Engedélyek kiosztása vCenter-objektumokhoz

Az engedélyeket a következő két módszer egyikének használatával rendelheti hozzá a leltári objektumokhoz:

- Rendeljen hozzá egy szerepkört a szükséges engedélyekkel az Ön által használt fiókhoz az összes olyan szülőobjektum esetében, amely a felderíteni vagy áttelepíteni kívánt virtuális gépeket üzemelteti.
- Azt is megteheti, hogy a szerepkört és a felhasználói fiókot az adatközpont szintjén rendeli hozzá, és propagálja azokat a gyermekobjektumok számára. Ezután adjon meg egy **nem hozzáférési** szerepkört a fiók számára minden olyan objektumhoz, amelyet nem szeretne felderíteni vagy áttelepíteni. Ezt a megközelítést nem javasoljuk, mert nehézkes, és elérhetővé teheti a hozzáférés-vezérlést, mivel minden új gyermekobjektum automatikusan megkapja a szülőtől örökölt hozzáférést.

Ha szerepkört szeretne hozzárendelni az összes releváns objektumhoz használt fiókhoz, tegye a következőket:

- **Az értékeléshez**: a virtuális gépek felméréséhez alkalmazza a **csak olvasási** szerepkört a vCenter felhasználói fiókra a felderíteni kívánt virtuális gépeket üzemeltető összes szülő objektumra vonatkozóan. Szülő objektumok: gazdagép, gazdagépek, fürt és a hierarchiában lévő fürtök mappája, akár az adatközpontig is. Ezeket az engedélyeket a hierarchia alárendelt objektumaira propagálja.

    ![Engedélyek kiosztása](./media/tutorial-assess-vmware/assign-perms.png)

- **Ügynök nélküli áttelepítés**esetén: az ügynök nélküli áttelepítés esetében a felderíteni kívánt virtuális gépeket üzemeltető összes szülő objektum esetében alkalmazza a felhasználói fiókhoz [szükséges engedélyekkel](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) rendelkező felhasználó által definiált szerepkört. A szerepkör <em>Azure_Migrate</em>nevezhető el.

### <a name="scope-support"></a>Hatókör-támogatás

A kiszolgáló-értékelési eszköz jelenleg nem tudja felderíteni a virtuális gépeket, ha a vCenter-fiókhoz hozzáférés van megadva a vCenter VM-mappa szintjén. A gazdagépek és fürtök mappái támogatottak.

Ha a felderítést virtuálisgép-mappák szerint szeretné használni, végezze el a következő eljárást annak biztosításához, hogy a vCenter-fiók csak olvasási hozzáféréssel legyen hozzárendelve virtuálisgép-szinten.

1. Rendeljen csak olvasási jogosultságokat a felderítéshez használni kívánt virtuálisgép-mappák összes virtuális gépén.
2. Csak olvasási hozzáférést biztosítson a virtuális gépeket üzemeltető összes szülő objektumhoz.
    - A hierarchiában lévő összes szülőobjektum (gazdagép, gazdagép, fürt, fürtök mappája) az adatközpontba kerül.
    - Nem kell propagálnia az engedélyeket az összes gyermekobjektum számára.
3. A felderítési hitelesítő adatok használatával válassza ki az adatközpontot **gyűjtemény hatókörként**. A szerepköralapú hozzáférés-vezérlés beállítása biztosítja, hogy a megfelelő vCenter-felhasználói fiók csak a bérlők által meghatározott virtuális gépekhez férhessen hozzá.


## <a name="next-steps"></a>További lépések

[Állítsa be a készüléket](how-to-set-up-appliance-vmware.md) , és [indítsa el a folyamatos felderítést](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).

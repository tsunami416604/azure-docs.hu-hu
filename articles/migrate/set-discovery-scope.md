---
title: A VMware VM-felderítés hatókörének beállítása az Azure Migrate szolgáltatással
description: Bemutatja, hogyan állíthatja be a VMware vm-felmérés és az Azure Migrate áttelepítésfelderítési hatókörét.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337635"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>A VMware virtuális gépek felderítési hatókörének beállítása

Ez a cikk ismerteti, hogyan korlátozhatja az [Azure Migrate készülék](migrate-appliance-architecture.md)által felderített VMware virtuális gépek felderítési hatókörét.

Az Azure Migrate készülék felderíti a helyszíni VMware virtuális gépeket, amikor: 

- Az [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel felmérheti a VMware virtuális gépeket az Azure-ba való migráláshoz.
- Használja az [Azure Migrate:Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszközt a VMware virtuális gépek [ügynök nélküli áttelepítéséhez](server-migrate-overview.md) az Azure-ba.

## <a name="set-discovery-scope"></a>Felderítési hatókör beállítása


Miután beállította az Azure Migrate készüléket a VMware virtuális gépek értékeléséhez vagy áttelepítéséhez, a készülék elkezdi a virtuális gépek felderítését, és virtuális gép metaadatokat küld az Azure-ba. Mielőtt a készüléket a vCenter Server hez csatlakoztatná felderítésre, beállíthatja a felderítési hatókört, hogy korlátozza a felderítést a vCenter Server adatközpontjaira, fürtjeire, fürtjeiből, állomásaiból, állomásaiból vagy egyedi virtuális gépekre.

A hatókör beállításához engedélyeket rendelhet hozzá ahhoz a fiókhoz, amelyet az Azure Migrate használ a vCenter-kiszolgáló eléréséhez.

## <a name="create-a-vcenter-user-account"></a>VCenter felhasználói fiók létrehozása

Ha még nem állított be egy vCenter-felhasználói fiókot, amelyet az Azure Migrate appliance a VMware virtuális gépek felderítésére, értékelésére és áttelepítésére használ, először ezt tegye meg.

1.    Jelentkezzen be a vSphere webügyfélbe a vCenter Server rendszergazdájaként.
2.    Válassza a Felügyeleti**sso-felhasználók** **Administration** > és csoportok lehetőséget, majd kattintson a **Felhasználók** fülre.
3.    Válassza az **Új felhasználó** ikont.
4.    Adja meg az új felhasználói adatokat > **OK**gombra.

A fiókengedélyek attól függnek, hogy mit telepít.

**Szolgáltatás** | **Fiókengedélyek**
--- | ---
[Értékelje](tutorial-assess-vmware.md)| A fióknak írásvédett hozzáférésre van szüksége.
[Alkalmazások/szerepkörök/szolgáltatások felderítése](how-to-discover-applications.md) | A fióknak írásvédett hozzáférésre van szüksége, és a virtuális gépek > a vendégműveletek jogosultságai engedélyezve vannak.
[Függőségek elemzése (ügynök nélküli)](how-to-create-group-machine-dependencies-agentless.md) | A fióknak írásvédett hozzáférésre van szüksége, és a virtuális gépek > a vendégműveletek jogosultságai engedélyezve vannak.
[Áttelepítés (ügynök nélküli)](tutorial-migrate-vmware.md) | Olyan szerepkörre van szüksége, amely a megfelelő engedélyekkel rendelkezik.<br/><br/> Szerepkör létrehozásához jelentkezzen be a vSphere webügyfélbe a vCenter Server rendszergazdájaként. Válassza ki a vCenter-kiszolgáló példányát > **Szerepkör létrehozása lehetőséget.** Adjon meg egy szerepkörnevet, például <em>Azure_Migrate,</em>és rendelje hozzá a [szükséges engedélyeket](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) a szerepkörhöz.


## <a name="assign-permissions-on-vcenter-objects"></a>Engedélyek hozzárendelése vCenter-objektumokhoz

A készletobjektumokhoz kétfélekon rendelhet hozzá engedélyeket:

- Rendeljen hozzá egy szerepkört a szükséges engedélyekkel, a használt fiókhoz, minden olyan szülőobjektumhoz, amely a felderíteni/áttelepíteni kívánt virtuális gépeket üzemelteti.
- Másik lehetőségként hozzárendelheti a szerepkört és a felhasználói fiókot az adatközpont szintjén, és propagálhatja őket a gyermekobjektumokhoz. Ezután adjon a fióknak egy **Nincs hozzáférési** szerepkört minden olyan objektumhoz, amelyet nem szeretne felfedezni/áttelepíteni. Nem javasoljuk ezt a módszert, mivel nehézkes, és előfordulhat, hogy a hozzáférés-vezérlők, mert minden új gyermek objektum automatikusan megkapja a hozzáférést örökölt a szülő.

Ha szerepkört szeretne hozzárendelni az összes releváns objektumhoz használt fiókhoz, tegye a következőket:

- **A felmérés:** A virtuális gép értékelése, alkalmazza az **írásvédett** szerepkört a vCenter felhasználói fiók minden szülő objektumok virtuális gépeket szeretne felderíteni. Szülőobjektumok is: állomás, mappa állomások, fürt, és mappa fürtök a hierarchiában, egészen az adatközpontba. Az engedélyek terjesztése a hierarchia gyermekobjektumaiba.

    ![Engedélyek kiosztása](./media/tutorial-assess-vmware/assign-perms.png)

- **Ügynök nélküli áttelepítés:** Ügynök nélküli áttelepítés esetén alkalmazzon egy felhasználó által definiált szerepkört a [felhasználói](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) fiókhoz, a felderíteni kívánt virtuális gépeket tároló összes szülőobjektumra. A szerepkört <em>Azure_Migrate</em>is elnevezheti.

### <a name="scope-support"></a>Hatókör támogatása

Jelenleg a Kiszolgálóértékelés eszköz nem tudja felderíteni a virtuális gépeket, ha a vCenter-fiók a vCenter virtuálisgép mappájának szintjén biztosított hozzáféréssel rendelkezik. Az állomások és fürtök mappái támogatottak.

Ha szeretné hatóköre a felderítés virtuálisgép-mappák, hajtsa végre a következő eljárást annak érdekében, hogy a vCenter-fiók csak olvasható hozzáféréssel rendelkezik a virtuális gép szintjén.

1. Csak olvasható engedélyek hozzárendelése a felderítéshez hatókörbe helyezni kívánt virtuális gépmappák összes virtuális gépéhez.
2. Csak olvasható hozzáférést biztosít a virtuális gépeket üzemeltető összes szülőobjektumhoz.
    - A hierarchiában az adatközpontig minden szülőobjektum (állomás, állomásmappa, fürt, fürt mappa).
    - Az engedélyeket nem kell minden gyermekobjektumra propagálnia.
3. Használja a hitelesítő adatokat a felderítéshez az adatközpont **gyűjteményhatókörként való kiválasztásával.** A szerepköralapú hozzáférés-vezérlés i beállítása biztosítja, hogy a megfelelő vCenter felhasználói fiók csak bérlő-specifikus virtuális gépekhez férhessen hozzá.


## <a name="next-steps"></a>További lépések

[Állítsa be a készüléket,](how-to-set-up-appliance-vmware.md) és [indítsa el a folyamatos felderítést](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).

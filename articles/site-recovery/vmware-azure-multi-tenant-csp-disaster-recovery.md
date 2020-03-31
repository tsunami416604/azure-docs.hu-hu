---
title: A VMware vész-helyreállítási szolgáltatásának beállítása az Azure-ba több-bérlős környezetben a Site Recovery és a Cloud Solution Provider (CSP) program használatával | Microsoft dokumentumok
description: Bemutatja, hogyan állíthatja be a VMware vész-helyreállítási egy több-bérlős környezetben az Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60460978"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>A VMware vész-helyreállítási szolgáltatásának beállítása többbérlős környezetben a Felhőszolgáltató (CSP) programmal

A [CSP-program](https://partner.microsoft.com/en-US/cloud-solution-provider) elősegíti a Microsoft felhőszolgáltatásainak – többek között az Office 365, az Enterprise Mobility Suite és a Microsoft Azure – számára készült történeteit. A csp-vel a partnerek teljes körű kapcsolatot birtokolnak az ügyfelekkel, és az elsődleges kapcsolati kapcsolattartó ponttá válnak. A partnerek üzembe helyezhetik az Azure-előfizetéseket az ügyfelek számára, és kombinálhatják az előfizetéseket a saját értéknövelt, személyre szabott ajánlataikkal.

Az [Azure Site Recovery](site-recovery-overview.md)segítségével partnerként közvetlenül a kripta-szolgáltatáson keresztül kezelheti az ügyfelek vészutáni helyreállítását. A kripta-szolgáltatás sal site recovery környezeteket állíthat be, és lehetővé teheti az ügyfelek számára, hogy önkiszolgáló módon kezeljék saját vész-helyreállítási igényeiket. Mindkét esetben a partnerek a Site Recovery és ügyfeleik közötti összekötői. A partnerek kiszolgálják az ügyfélkapcsolatot, és kiszámlázják az ügyfeleket a Site Recovery használatáért.

Ez a cikk ismerteti, hogyan hozhat létre és kezelhet bérlői előfizetéseket a kripta-szolgáltatáson keresztül, egy több-bérlős VMware replikációs forgatókönyv.

## <a name="prerequisites"></a>Előfeltételek

A VMware replikációjának beállításához a következőket kell tennie:

- [Felkészülés](tutorial-prepare-azure.md) Azure-erőforrások, beleértve az Azure-előfizetést, egy Azure virtuális hálózatot és egy tárfiókot.
- Helyszíni VMware-kiszolgálók és virtuális gépek [előkészítése.](vmware-azure-tutorial-prepare-on-premises.md)
- Minden bérlő höz hozzon létre egy külön felügyeleti kiszolgálót, amely képes kommunikálni a bérlői virtuális gépekkel és a vCenter-kiszolgálókkal. Ehhez a felügyeleti kiszolgálóhoz csak Ön, mint partner férhet hozzá hozzáférési jogokkal. További információ a [több-bérlős környezetekről.](vmware-azure-multi-tenant-overview.md)

## <a name="create-a-tenant-account"></a>Bérlői fiók létrehozása

1. A [Microsoft Partner Center en](https://partnercenter.microsoft.com/)keresztül jelentkezzen be a kriptába.
2. Az **Irányítópult menüben** válassza a **Vevők**lehetőséget.
3. A megnyíló lapon kattintson a **Vevő hozzáadása** gombra.
4. Az **Új ügyfél** lapon adja meg a bérlő fiókadatainak adatait.

    ![A Fiókadatai lap](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Ezután kattintson a **Tovább: Előfizetések gombra.**
6. Az előfizetések kiválasztása lapon jelölje be a **Microsoft Azure** jelölőnégyzetet. Hozzáadhat más előfizetéseket most vagy bármikor.
7. A **Véleményezés** lapon erősítse meg a bérlő adatait, majd kattintson a **Küldés gombra.**
8. Miután létrehozta a bérlői fiókot, megjelenik egy megerősítő lap, amely megjeleníti az alapértelmezett fiók részleteit és az adott előfizetés jelszavát. Mentse az adatokat, és szükség szerint módosítsa a jelszót az Azure Portal bejelentkezési oldalán keresztül.

Ezt az információt megoszthatja a bérlővel, vagy szükség esetén létrehozhat és megoszthat egy külön fiókot.

## <a name="access-the-tenant-account"></a>A bérlői fiók elérése

A bérlői előfizetést a Microsoft Partner Center irányítópultján keresztül érheti el.

1. Az **Ügyfelek** lapon kattintson a bérlői fiók nevére.
2. A **bérlői** fiók Előfizetések lapján figyelheti a meglévő fiók-előfizetéseket, és szükség szerint további előfizetéseket adhat hozzá.
3. A bérlő vész-helyreállítási műveleteinek kezeléséhez válassza az **Összes erőforrás (Azure Portal)** lehetőséget. Ez hozzáférést biztosít a bérlő Azure-előfizetéseihez.

    ![A Minden erőforrás hivatkozás](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. A hozzáférés ellenőrzéséhez kattintson az Azure Active Directory-hivatkozásra az Azure Portal jobb felső részén.

    ![Az Azure Active Directory hivatkozása](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Most már végrehajthatja és kezelheti az összes site recovery műveletek a bérlő az Azure Portalon. A bérlői előfizetés elérése a felügyelt vész-helyreállítási kripta keresztül, kövesse a korábban leírt folyamat.

## <a name="assign-tenant-access-to-the-subscription"></a>Bérlői hozzáférés hozzárendelése az előfizetéshez

1. Győződjön meg arról, hogy a vész-helyreállítási infrastruktúra be van állítva. A partnerek a kriptaportálon keresztül érik el a bérlői előfizetéseket, függetlenül attól, hogy a vész-helyreállítási szolgáltatás felügyelt vagy önkiszolgáló. Állítsa be a tárolót, és regisztrálja az infrastruktúrát a bérlői előfizetésekhez.
2. Adja meg a bérlőnek a [létrehozott fiókot.](#create-a-tenant-account)
3. A kriptaportálon keresztül új felhasználót adhat hozzá a bérlői előfizetéshez az alábbiak szerint:

    a) Lépjen a bérlő kripta-előfizetési oldalára, majd válassza a **Felhasználók és licencek** lehetőséget.

      ![A bérlő kripta-előfizetési lapja](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Most hozzon létre egy új felhasználót a megfelelő adatok megadásával és az engedélyek kiválasztásával, vagy a felhasználók listájának feltöltésével egy CSV fájlba.
    
    c) Miután létrehozott egy új felhasználót, lépjen vissza az Azure Portalra. Az **Előfizetés** lapon válassza ki a megfelelő előfizetést.

    d) Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd kattintson **a Szerepkör-hozzárendelések gombra.**

    e) Kattintson **a Szerepkör-hozzárendelés hozzáadása** gombra, ha a megfelelő hozzáférési szinttel rendelkező felhasználót szeretne hozzáadni. A kriptaportálon keresztül létrehozott felhasználók megjelennek a Szerepkör-hozzárendelések lapon.

      ![Felhasználó hozzáadása](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- A legtöbb felügyeleti művelet esetében a *közreműködői* szerepkör elegendő. Az ilyen hozzáférési szinttel rendelkező felhasználók mindent megtehetnek egy előfizetésen, kivéve a hozzáférési szintek módosítását (amelyekhez *tulajdonosi*szintű hozzáférés szükséges).
- A Site Recovery három [előre definiált felhasználói szerepkörrel](site-recovery-role-based-linked-access-control.md)is rendelkezik, amelyek szükség szerint tovább korlátozhatják a hozzáférési szinteket.

## <a name="multi-tenant-environments"></a>Több-bérlős környezetek

Három fő több-bérlős modell létezik:

* **Megosztott tárhelyszolgáltató (HSP)**: A partner birtokolja a fizikai infrastruktúrát, és megosztott erőforrásokat (vCenter, adatközpontok, fizikai tárolás és így tovább) használ több bérlővirtuális gép ugyanazon az infrastruktúrán. A partner vész-helyreállítási felügyeletet biztosíthat felügyelt szolgáltatásként, vagy a bérlő önkiszolgáló megoldásként rendelkezhet vész-helyreállítási szolgáltatással.

* **Dedikált tárhelyszolgáltató:** A partner birtokolja a fizikai infrastruktúrát, de dedikált erőforrásokat (több vCenter, fizikai adattárak és így tovább) használ az egyes bérlők virtuális gépei külön infrastruktúrán való üzemeltetéséhez. A partner vész-helyreállítási felügyeletet biztosíthat felügyelt szolgáltatásként, vagy a bérlő önkiszolgáló megoldásként birtokolhatja.

* **Felügyelt szolgáltatások szolgáltatója (MSP)**: Az ügyfél birtokolja a virtuális gépeket kiszolgáló fizikai infrastruktúrát, és a partner biztosítja a vész-helyreállítási engedélyezést és -kezelést.

Bérlői előfizetések beállítása a cikkben leírtak szerint, gyorsan elkezdheti az ügyfelek engedélyezése a megfelelő több-bérlős modellek. A különböző több-bérlős modellekről és a helyszíni hozzáférés-vezérlés engedélyezéséről [itt](vmware-azure-multi-tenant-overview.md)olvashat bővebben.

## <a name="next-steps"></a>További lépések
- További információ az Azure Site Recovery-telepítések kezeléséhez szükséges [szerepköralapú hozzáférés-vezérlésről.](site-recovery-role-based-linked-access-control.md)
- További információ a VMware és az Azure [replikációs architektúrájának megismeréséről.](vmware-azure-architecture.md)
- Tekintse át a VMware virtuális gépek Azure-ba replikálásának [oktatóanyagát.](vmware-azure-tutorial.md)
További információ a vmware virtuális gépek Azure-ba replikálására szolgáló [több-bérlős környezetekről.](vmware-azure-multi-tenant-overview.md)

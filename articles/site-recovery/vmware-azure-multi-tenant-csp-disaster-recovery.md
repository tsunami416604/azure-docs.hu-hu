---
title: VMware-vészhelyreállításhoz az Azure-bA a Site Recovery és a Cloud Solution Provider (CSP) program egy több-bérlős környezet beállítása |} A Microsoft Docs
description: Ismerteti, hogyan állíthatja be a VMware-vészhelyreállítás több-bérlős környezetben az Azure Site Recoveryvel.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: c26421f848cf25e391589fa791f801e30fd14797
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50208901"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>A Cloud Solution Provider (CSP) program, több-bérlős környezetben VMware-vészhelyreállítás beállítása

A [CSP program](https://partner.microsoft.com/en-US/cloud-solution-provider) elősegíti a Microsoft felhőszolgáltatásai, beleértve az Office 365, Enterprise Mobility Suite és a Microsoft Azure jobbak együtt történetek. CSP, a partnerek saját a végpontok közötti kapcsolatot az ügyfelekkel, és az elsődleges kapcsolathoz kapcsolódási pont lesz. Partnerek üzembe helyezése az Azure-előfizetések ügyfelek számára, és kombinálhatja a saját értéknövelő, személyre szabott ajánlatokat az előfizetéseket.

A [Azure Site Recovery](site-recovery-overview.md), partnerként kezelheti vész-helyreállítási CSP közvetlenül az ügyfelek számára. Azt is megteheti a Site Recovery-környezetek beállítása a CSP segítségével, és lehetővé teszik a felhasználók önkiszolgáló módon kezelni a saját vész-helyreállítási igényekre. Mindkét esetben a partnerek olyan Site Recovery és az ügyfeleik között. Partnerek az Ügyfélkapcsolat szolgáltatásra, és a Díjszámítás a Site Recovery használata az ügyfelek.

Ez a cikk bemutatja, hogyan Ön partnerként hozhat létre és kezelhet egy több-bérlős VMware replikáció forgatókönyv CSP, bérlői fizethet.

## <a name="prerequisites"></a>Előfeltételek

VMware-replikáció beállításához szüksége tegye a következőket:

- [Készítse elő](tutorial-prepare-azure.md) Azure-erőforrásokat, beleértve az Azure-előfizetéssel, az Azure-beli virtuális hálózathoz és a egy tárfiókot.
- [Készítse elő](vmware-azure-tutorial-prepare-on-premises.md) a helyszíni VMware-kiszolgálók és virtuális gépeket.
- Az egyes bérlők számára hozzon létre egy külön felügyeleti kiszolgáló, amely képes kommunikálni a bérlői virtuális gépeket, és a vCenter-kiszolgálók. Csak partnerként kell hozzáférési jogosultsága ahhoz, hogy ez a felügyeleti kiszolgáló. Tudjon meg többet [több-bérlős környezetekben](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Hozzon létre egy bérlői fiókot

1. Keresztül [Microsoft Partner Centeren](https://partnercenter.microsoft.com/), jelentkezzen be a CSP-fiók.
2. Az a **irányítópult** menüjében válassza **ügyfelek**.
3. A megnyíló lapon kattintson a **Hozzáadás ügyfél** gombra.
4. A **új ügyfél** lapon, a bérlő számára adja meg a fiók információk részleteket.

    ![A fiókadatok lap](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Kattintson a **tovább: előfizetések**.
6. Az előfizetések kiválasztása lapon jelölje be **Microsoft Azure** jelölőnégyzetet. Adhat hozzá más előfizetéseket, most vagy a tetszőleges időpontban.
7. Az a **felülvizsgálati** lapon erősítse meg a bérlő részleteit, és kattintson a **küldés**.
8. Miután létrehozta a bérlői fiók megjelenik egy megerősítő oldal az alapértelmezett fiók és a jelszót az előfizetés részleteinek megjelenítése. Mentse az adatokat, és módosítsa a jelszót később szükség szerint, az Azure portal bejelentkezési oldalán keresztül.

Megoszthat a bérlőhöz, mert ezt az információt, vagy hozhat létre és megosztani egy külön fiókot, ha szükséges.

## <a name="access-the-tenant-account"></a>A bérlői fiók eléréséhez

A bérlő előfizetés a Microsoft Partner Center irányítópultjának érheti el.

1. Az a **ügyfelek** lap, kattintson a bérlői fiók nevét.
2. Az a **előfizetések** lap a bérlői fiók figyelheti a meglévő fiók-előfizetésre, és szükség szerint további előfizetéseket, hozzá.
3. Válassza ki a bérlő vész-helyreállítási műveleteinek kezelésére, **erőforrások (Azure portal)**. Ez a bérlő Azure-előfizetés hozzáférést biztosít.

    ![Az összes erőforrás-hivatkozás](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Hozzáférés a felül található Azure Active Directory hivatkozásra kattintva ellenőrizheti az Azure Portalon, jobb.

    ![Az Azure Active Directory-hivatkozás](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Ezután hajtsa végre és az Azure Portalon a bérlő összes Site Recovery-műveletek kezelése. A bérlő előfizetés CSP keresztül hozzáfér a felügyelt vész-helyreállítási, kövesse a korábban ismertetett folyamatot.

## <a name="assign-tenant-access-to-the-subscription"></a>Bérlő hozzáférések hozzárendelése az előfizetéshez

1. Győződjön meg arról, hogy be van-e beállítva a vészhelyreállítási infrastruktúra kiépítéséhez. Partnerek a CSP portálon keresztül, függetlenül attól, hogy vész-helyreállítási felügyeli-e vagy önkiszolgáló bérlői előfizetések eléréséhez. Állítsa be a tárolót, és regisztrálja a bérlői előfizetések infrastruktúrát.
2. Adja meg a bérlőt a [létrehozott fiókot](#create-a-tenant-account).
3. Hozzáadhat egy új felhasználót a bérlői előfizetéshez a CSP-portálon keresztül a következő:

    a) nyissa meg a bérlő CSP előfizetési lapját, és válassza a **a felhasználók és licencek** lehetőséget.

      ![A bérlő CSP-előfizetés lapján](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

      (b) írja be a vonatkozó adatokat, majd válassza az engedélyeket, vagy a kiválasztott felhasználók a CSV-fájl feltöltésével most új felhasználó létrehozása.
    c) Miután létrehozott egy új felhasználó, lépjen vissza az Azure Portalon. Az a **előfizetés** lapon, válassza ki a megfelelő előfizetést.
    d) a select **hozzáférés-vezérlés (IAM)**, és kattintson a **Hozzáadás**, hozzáadni egy felhasználót a megfelelő hozzáférési szinttel. A CSP-portálon keresztül létrehozó felhasználók automatikusan megjelennek a egy hozzáférési szintet kattintás után a megnyíló lapon.

      ![Felhasználó hozzáadása](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- A legtöbb felügyeleti műveletek a *közreműködői* szerepkör is elegendő. A hozzáféréssel rendelkező felhasználók műveletek mindegyikét egy előfizetésben, de úgy módosítsa a hozzáférési szintek (amelyhez *tulajdonosa*-szintű hozzáférésre szükség).
- A Site Recovery három is rendelkezik [előre meghatározott felhasználói szerepkörök](site-recovery-role-based-linked-access-control.md), amely tovább korlátozhatja a hozzáférési szintek szükség szerint használható.

## <a name="multi-tenant-environments"></a>Több-bérlős környezetekben

Nincsenek három fő több-bérlős modell:

* **A megosztott üzemeltetési szolgáltatásokat szolgáltató (HSP)**: A partner tulajdonában lévő fizikai infrastruktúráját, és használ megosztott erőforrásokat (vCenter, adatközpontok, fizikai tároló, és így tovább) több bérlő virtuális gépek üzemeltetéséhez ugyanazon az infrastruktúrán. A partner is képes a globálisnév vész-helyreállítási felügyelt szolgáltatás, vagy a bérlő is saját vész-helyreállítási önkiszolgáló megoldást.

* **Dedikált üzemeltetési szolgáltató**: A partner a fizikai infrastruktúra tulajdonosa, de dedikált erőforrásokat (több vCenters, fizikai adattárolók, és így tovább) használja a külön infrastruktúra minden egyes bérlő-alapú virtuális gépek üzemeltetéséhez. A partner is képes a globálisnév vész-helyreállítási felügyelt szolgáltatás, vagy a bérlő is saját, önkiszolgáló megoldás.

* **Felügyelt szolgáltatások szolgáltatói (MSP)**: az ügyfél a tulajdonosa a virtuális gépeket üzemeltető fizikai infrastruktúráját, és a partner által biztosított a vész-helyreállítási engedélyezését és felügyelet.

Beállításával bérlői előfizetések ebben a cikkben leírtak szerint, gyorsan hozzáadhatja a így az ügyfelek a megfelelő több-bérlős modell valamelyikében. További információ a különböző több-bérlős modell és a engedélyezése helyszíni hozzáférés-vezérlés [Itt](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [szerepköralapú hozzáférés-vezérlés](site-recovery-role-based-linked-access-control.md) Azure Site Recovery központi telepítések felügyeletéhez szükséges.
- További tudnivalók a VMware – Azure [replikáció architektúrája](vmware-azure-architecture.md).
- [Tekintse át az oktatóanyag](vmware-azure-tutorial.md) VMware virtuális gépek replikálásához az Azure-bA.
Tudjon meg többet [több-bérlős környezetekben](vmware-azure-multi-tenant-overview.md) VMware virtuális gépek replikálásához az Azure-bA.

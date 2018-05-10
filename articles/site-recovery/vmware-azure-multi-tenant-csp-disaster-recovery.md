---
title: VMware-replikáció az Azure Site Recovery és a Cloud Solution Provider (CSP) program segítségével több-bérlős környezet beállítása |} Microsoft Docs
description: Ismerteti, hogyan lehet létrehozni és CSP keresztül a bérlői előfizetések kezelése és Azure Site Recovery üzembe egy több-bérlős beállítása
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/03/2018
ms.author: manayar
ms.openlocfilehash: a9c77179aa77b7920646d0415ce4d244892215e4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>A Cloud Solution Provider (CSP) programmal több-bérlős környezetben VMware-replikáció beállítása

A [CSP program](https://partner.microsoft.com/en-US/cloud-solution-provider) elősegíti a Microsoft felhőszolgáltatásai, beleértve az Office 365, a nagyvállalati mobilitási csomag és a Microsoft Azure jobb együtt szövegek. A CSP-hez partnerek saját a végpontok közötti kapcsolat az ügyfelek és lesz az elsődleges kapcsolathoz kapcsolódási pontot. Partnerek Azure-előfizetések az ügyfelek központi telepítése, és a saját hozzáadott, testreszabott ajánlatokat az előfizetések kombinálhatja.

A [Azure Site Recovery](site-recovery-overview.md), partnerként kezelheti vész-helyreállítási közvetlenül CSP ügyfelek esetén. Alternatív megoldásként kriptográfiai Szolgáltató használatával állítsa be a Site Recovery környezetekben, és tudassa az ügyfelekkel önkiszolgáló módon kezelhetik a saját vész-helyreállítási igényekre. Mindkét esetben a partnerek olyan a Site Recovery és az ügyfelek között. Partnerek az Ügyfélkapcsolat szolgáltatásra, és a Site Recovery használata az ügyfelek kiszámlázni.

Ez a cikk ismerteti, hogyan partnerként hozhat létre és bérlői előfizetések keresztül CSP, több-bérlős VMware replikációs forgatókönyv esetén kezelése.

## <a name="prerequisites"></a>Előfeltételek

VMware-replikáció beállításához meg kell tegye a következőket:

- [Készítse elő](tutorial-prepare-azure.md) Azure-erőforrások, például az Azure-előfizetéssel, Azure virtuális hálózat és a storage-fiók.
- [Készítse elő](vmware-azure-tutorial-prepare-on-premises.md) helyszíni VMware-kiszolgálók és virtuális gépek.
- Az egyes bérlők számára hozzon létre egy külön felügyeleti kiszolgáló, amely képes kommunikálni a bérlői virtuális gépek és a vCenter-kiszolgáló. Csak partnerként kell rendelkezik hozzáférési jogosultsággal ehhez a felügyeleti kiszolgálóhoz. További információ [több-bérlős környezetekben](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Bérlői fiók létrehozása

1. Keresztül [Microsoft Partner Center](https://partnercenter.microsoft.com/), jelentkezzen be a CSP-fiókjába.
2. Az a **irányítópult** menü **ügyfelek**.
3. A megnyíló lapon kattintson a **Hozzáadás ügyfél** gombra.
4. A **új ügyfél** lapján adja meg a fiók adatai a bérlő számára.

    ![A fiók adatai lap](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Kattintson a **tovább: előfizetések**.
6. Az előfizetések kiválasztása lapon válassza ki a **Microsoft Azure** jelölőnégyzetet. Most, vagy más bármikor másik előfizetés is hozzáadhat.
7. Az a **felülvizsgálati** lapon erősítse meg a bérlői adatokat, és kattintson a **Submit**.
8. Miután létrehozta a bérlői fiókot, a jóváhagyó lapon jelenik meg, az alapértelmezett fiókot és egy jelszót, hogy az előfizetés részleteinek megjelenítése. Mentse az adatokat, és módosítsa a jelszót később szükség szerint, az Azure portál bejelentkezési oldal keresztül.

Ezek az információk megosztása a bérlő, mert a, vagy hozzon létre, és egy külön fiókot megosztani, ha szükséges.

## <a name="access-the-tenant-account"></a>A bérlői fiók eléréséhez

A bérlő előfizetés a Microsoft Partner Center irányítópultjának keresztül érheti el.

1. Az a **ügyfelek** lapján kattintson a bérlői fiók nevét.
2. Az a **előfizetések** lap a bérlői fiók figyelheti a meglévő fiók-előfizetések, és adja hozzá legalább egy előfizetésre, szükség szerint.
3. Válassza ki a bérlő vész-helyreállítási műveleteinek a felügyeletét, **összes erőforrást (Azure-portál)**. Ez hozzáférést biztosít a bérlő Azure-előfizetések.

    ![Az összes erőforrás-hivatkozás](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Hozzáférés az Azure Active Directory felső hivatkozásra kattintva ellenőrizheti az Azure-portálon sarkában.

    ![Az Azure Active Directory-hivatkozás](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Ezután hajtsa végre és kezelése az Azure-portálon a bérlőhöz tartozó összes Site Recovery-műveleteket. A bérlői előfizetéshez eléréséhez CSP keresztül felügyelt vész-helyreállítási kövesse a korábban ismertetett folyamatot.

## <a name="assign-tenant-access-to-the-subscription"></a>Bérlői hozzáférés hozzárendelése az előfizetéshez

1. Győződjön meg arról, hogy a vész-helyreállítási infrastruktúra be van-e beállítva. Partnerek a CSP-portálon, függetlenül attól, hogy katasztrófa utáni helyreállítás felügyelt vagy az önkiszolgáló bérlői előfizetések érhető el. Állítsa be a tárolóba, és regisztrálja a bérlői előfizetések infrastruktúrát.
2. Adja meg a bérlői a [létrehozott fiókot](#create-a-tenant-account).
3. Hozzáadhat egy új felhasználót a CSP-portálon keresztül a bérlői előfizetéshez az alábbiak szerint:

    a) nyissa meg a bérlői CSP előfizetés oldalát, és válassza a **felhasználók és licencek** lehetőséget.

      ![A bérlő CSP-előfizetés lapján](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

      b) most hozzon létre egy új felhasználót, írja be a vonatkozó adatokat, majd válassza az engedélyeket, vagy fel kell tölteni egy CSV-fájlban szereplő felhasználók listáját.
    c) új felhasználó létrehozása, után térjen vissza az Azure-portálon. Az a **előfizetés** lapon, válassza ki a megfelelő előfizetést.
    d) válassza **hozzáférés-vezérlés (IAM)**, és kattintson a **Hozzáadás**, hozzáadni egy felhasználót a megfelelő hozzáférési szinttel. A felhasználók, a CSP-portálon keresztül hozta létre automatikusan megjelennek az olyan hozzáférési szint kattintás után megjelenő oldalon.

      ![Felhasználó hozzáadása](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- A legtöbb felügyeleti műveleteket a *közreműködő* szerepkör is elegendő. Ez a hozzáférési szint rendelkező felhasználók is mindent meg azzal a különbséggel hozzáférési szint módosítása előfizetés (amelynek *tulajdonos*-szintű hozzáféréssel kell).
- A Site Recovery is rendelkezik három [előre meghatározott felhasználói szerepkörök](site-recovery-role-based-linked-access-control.md), amely tovább korlátozhatja a hozzáférési szintek szükség szerint használható.

## <a name="multi-tenant-environments"></a>Több-bérlős környezetekben

Három fő több-bérlős modell van:

* **A megosztott üzemeltetési szolgáltatásokat szolgáltató (HSP)**: A partner birtokolja a fizikai infrastruktúra, és a által használt megosztott erőforrások (vCenter, adatközpontok, fizikai tárhelyet, és így tovább) több bérlői virtuális gépek üzemeltetéséhez ugyanazon az infrastruktúrán. A partner biztosíthat a vész-helyreállítási felügyeleti felügyelt szolgáltatásként, vagy a bérlői önkiszolgáló megoldás vész-helyreállítási rendelkezhet.

* **Üzemeltetési szolgáltató dedikált**: A partner birtokolja a fizikai infrastruktúra, de a dedikált erőforrások (több Vcenter, fizikai datastores, és így tovább) használ a külön infrastruktúra minden egyes bérlői virtuális gépekre. A partner biztosíthat a vész-helyreállítási felügyeleti felügyelt szolgáltatásként, vagy a bérlő rendelkezhet önkiszolgáló megoldás.

* **Felügyelt szolgáltatások szolgáltató (MSP)**: az ügyfél birtokolja a fizikai infrastruktúra, amely a virtuális gépet futtat, és a partner biztosít a vész-helyreállítási engedélyezése és kezelése.

Bérlői előfizetések beállítása, a cikkben leírtak szerint, gyorsan megkezdheti az összes releváns több-bérlős modell ügyfelek engedélyezése. További információ a különböző több-bérlős modell és engedélyezése a helyszíni hozzáférés-vezérlést [Itt](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>További lépések
- További információ [szerepköralapú hozzáférés-vezérlés](site-recovery-role-based-linked-access-control.md) Azure Site Recovery központi telepítések felügyeletéhez szükséges.
- További tudnivalók az Azure-bA VMware [replikációs architektúra](vmware-azure-architecture.md).
- [Tekintse át az oktatóanyag](vmware-azure-tutorial.md) a VMware virtuális gépek replikálása Azure-bA.
További információ [több-bérlős környezetekben](vmware-azure-multi-tenant-overview.md) a VMware virtuális gépek replikálása Azure-bA.

---
title: "Több vállalat kiszolgálása az Azure Site Recovery a Cloud Solution Provider (CSP) programmal kezelése |} Microsoft Docs"
description: "Ismerteti, hogyan lehet létrehozni és CSP keresztül a bérlői előfizetések kezelése és Azure Site Recovery üzembe egy több-bérlős beállítása"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 6dc8b573e66eaae1b5cb923ae72333fb959d0969
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>Több vállalat kiszolgálása a Cloud Solution Provider (CSP) programmal kezelése

A [CSP program](https://partner.microsoft.com/en-US/cloud-solution-provider) elősegíti a Microsoft felhőszolgáltatásai, beleértve az Office 365, a nagyvállalati mobilitási csomag és a Microsoft Azure jobb együtt szövegek. CSP partnerek saját a végpontok közötti kapcsolat az ügyfelekkel és lesz az elsődleges kapcsolathoz kapcsolódási pontot. Partnerek telepítheti az Azure-előfizetések az ügyfelek és kombinálhatja a saját hozzáadott, testreszabott ajánlatokat az előfizetések.

Az Azure Site Recovery partnerek a teljes vész-helyreállítási megoldás felügyelheti az ügyfelek közvetlenül a CSP-hez. Vagy állítsa be a Site Recovery környezetekben, és lehetővé teszik az ügyfelek kezelése a saját vész-helyreállítási igényekre önkiszolgáló módon CSP használhatnak. Mindkét esetben a partnerek olyan a Site Recovery és az ügyfelek között. Partnerek az Ügyfélkapcsolat szolgáltatást, és a Site Recovery használata az ügyfelek kiszámlázni.

Ez a cikk arról, hogy a partner hogyan hozhat létre és kezelhet egy több-bérlős VMware telepítő keresztül CSP, bérlői előfizetések ismerteti.

## <a name="prerequisites"></a>Előfeltételek

- [Készítse elő](tutorial-prepare-azure.md) Azure-erőforrások, például az Azure-előfizetéssel, Azure virtuális hálózat és a storage-fiók.
- [Készítse elő](tutorial-prepare-on-premises-vmware.md) VMware helyszíni VMware-kiszolgálók és virtuális gépek.
- Az egyes bérlők számára hozzon létre egy külön felügyeleti kiszolgáló, amely képes kommunikálni a bérlői virtuális gépek és a partner által létrehozott vCenter. Csak a partner rendelkezik hozzáférési jogosultsággal ehhez a kiszolgálóhoz. A különböző több-bérlős környezetekben a további részletekért tekintse meg a [több-bérlős VMware](site-recovery-multi-tenant-support-vmware-using-csp.md) útmutatást.

## <a name="create-a-tenant-account"></a>Bérlői fiók létrehozása

1. Keresztül [Microsoft Partner Center](https://partnercenter.microsoft.com/), jelentkezzen be a CSP-fiókjába.

2. Az a **irányítópult** menü **ügyfelek**.

    ![A Microsoft Partner Center felhasználóinak hivatkozás](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. A megnyíló lapon kattintson a **Hozzáadás ügyfél** gombra.

    ![Az ügyfél hozzáadása gomb](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. Az a **új ügyfél** lapon adja meg a fiók adatai a bérlő számára, és kattintson a **tovább: előfizetések**.

    ![A fiók adatai lap](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. Az előfizetések kiválasztása lapon válassza ki a **Microsoft Azure** jelölőnégyzetet. Most, vagy más bármikor másik előfizetés is hozzáadhat.

    ![A Microsoft Azure-előfizetés jelölőnégyzetet](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. Az a **felülvizsgálati** lapon erősítse meg a bérlői adatokat, és kattintson a **Submit**.

    ![A nyomtatási kép](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    Miután létrehozta a bérlői fiókot, a jóváhagyó lapon jelenik meg, az alapértelmezett fiókot és egy jelszót, hogy az előfizetés részleteinek megjelenítése.

7. Mentse az adatokat, és módosítsa a jelszót később keresztül az Azure portál bejelentkezési lapon szükség szerint.  

    Ezek az információk megosztása a bérlő, mert a, vagy hozzon létre, és egy külön fiókot megosztani, ha szükséges.

## <a name="access-the-tenant-account"></a>A bérlői fiók eléréséhez

Érheti el a bérlő előfizetés a Microsoft Partner Center irányítópultján, lásd: "1. lépés: bérlői fiók létrehozása."

1. Lépjen a **ügyfelek** lapon, majd a bérlői fiók nevét.

2. Az a **előfizetések** lap a bérlői fiók figyelheti a meglévő fiók-előfizetések, és adja hozzá legalább egy előfizetésre, szükség szerint. Válassza ki a bérlő vész-helyreállítási műveleteinek a felügyeletét, **összes erőforrást (Azure-portál)**.

    ![Az összes erőforrás-hivatkozás](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    Kattintson a **összes erőforrás** hozzáférést biztosít a bérlő Azure-előfizetések. Hozzáférés az Azure Active Directory tetején kattintva ellenőrizheti az Azure-portálon sarkában.

    ![Azure Active Directory link](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

Ezután hajtsa végre az összes webhely-helyreállítási művelet az Azure portálon keresztül a bérlői és vész-helyreállítási műveleteinek a felügyeletét. A bérlői előfizetéshez eléréséhez CSP keresztül felügyelt vész-helyreállítási kövesse a korábban ismertetett folyamatot.

## <a name="deploy-resources-to-the-tenant-subscription"></a>A bérlői előfizetéshez erőforrások telepítése
1. Az Azure portálon hozzon létre egy erőforráscsoportot, és a Recovery Services-tárolónak a szokásos folyamatonként telepíteni.

2. Töltse le a tároló regisztrációs kulcsát.

3. A Tanúsítványszolgáltatások regisztrálja a bérlő a tárolóbeli regisztrációs kulcs használatával.

4. Adja meg a hitelesítő adatokat a két hozzáférési fiókjaiként: vCenter hálózatelérési fiók és a virtuális gép fér hozzá a fiókjához.

    ![Kezelői konfigurációs kiszolgáló fiókok](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Regisztrálja a Recovery Services-tárolónak a Site Recovery-infrastruktúra
1. Az Azure portálon, a tároló, amelyet korábban hozott létre a regisztrálja a vCenter-kiszolgáló a CS regisztrált a "3. lépés: telepítse a bérlői előfizetéshez erőforrásokat." A vCenter hálózatelérési fiókot használja erre a célra.
2. Az "Infrastruktúra előkészítése" folyamat befejezéséhez a Site Recovery a szokásos folyamatonként.
3. A virtuális gépek replikálása most már készen áll. Győződjön meg arról, hogy csak a bérlői virtuális gépek jelennek meg a **válassza ki a virtuális gépek** részen a **replikálása** lehetőséget.

    ![A Select virtuális gépek panelje bérlői virtuális gépek listája](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>Bérlői hozzáférés hozzárendelése az előfizetéshez

Az adatok önkiszolgáló helyreállítását, adja meg a bérlő fiók adatait, a 6. lépésében az "1. lépés: bérlői fiók létrehozása" szakasz. A művelet végrehajtása után a fiókpartner állít be a vész-helyreállítási infrastruktúra. Hogy a vész-helyreállítási típus kezelt vagy az önkiszolgáló, partnerek bérlői előfizetések kell férnie a CSP-portálon keresztül. Azok a partner által birtokolt tároló beállítása, és regisztrálja az infrastruktúrát, hogy a bérlői előfizetések.

Partnerek is hozzáadhat egy új felhasználót a CSP-portálon keresztül a bérlői előfizetéshez a következő módon:

1. Nyissa meg a bérlői CSP előfizetés oldalát, és válassza a **felhasználók és licencek** lehetőséget.

    ![A bérlő CSP-előfizetés lapján](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    Most létrehozhat egy új felhasználót, írja be a vonatkozó adatokat, majd válassza az engedélyeket, vagy fel kell tölteni egy CSV-fájlban szereplő felhasználók listáját.

2. Miután létrehozott egy új, lépjen vissza az Azure-portálon, majd a felhasználó a **előfizetés** panelen válassza ki a megfelelő előfizetést.

3. A megjelenő panelen válassza ki a **hozzáférés-vezérlés (IAM)**, és kattintson a **Hozzáadás** hozzáadni egy felhasználót a megfelelő hozzáférési szinttel.      
    A felhasználók a CSP-portálon létrehozott automatikusan megjelennek a olyan hozzáférési szint kattintás után megjelenő panelen.

    ![Felhasználó hozzáadása](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    A legtöbb felügyeleti műveleteket a *közreműködő* szerepkör is elegendő. Ez a hozzáférési szint rendelkező felhasználók is mindent meg azzal a különbséggel hozzáférési szint módosítása előfizetés (amelynek *tulajdonos*-szintű hozzáféréssel kell).

  Az Azure Site Recovery is rendelkezik három [előre meghatározott felhasználói szerepkörök](site-recovery-role-based-linked-access-control.md) , amely tovább korlátozhatja a hozzáférési szintek szükség szerint használható.

## <a name="next-steps"></a>További lépések
  [További](site-recovery-role-based-linked-access-control.md) vonatkozó szerepköralapú hozzáférés-vezérlést az Azure Site Recovery központi telepítések felügyeletéhez szükséges.

  [Több-bérlős VMware-környezetekben kezelése](site-recovery-multi-tenant-support-vmware-using-csp.md)

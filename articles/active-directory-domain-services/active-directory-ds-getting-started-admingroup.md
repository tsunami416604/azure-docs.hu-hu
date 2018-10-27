---
title: 'Az Azure Active Directory tartományi szolgáltatások: Első lépések |} A Microsoft Docs'
description: Engedélyezze az Active Directory Domain Servicest az Azure portal használatával
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 0dc4019d2b3f33bfc92d73aeadadc0c64ecaab3b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158516"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Engedélyezze az Active Directory Domain Servicest az Azure portal használatával


## <a name="task-3-configure-administrative-group"></a>3. feladat: felügyeleti csoport konfigurálása
A konfigurációs feladat egy felügyeleti csoport létrehozhat az Azure AD-címtárban. A speciális felügyeleti csoport neve *AAD DC rendszergazdák*. A csoport tagjai, amely tartományhoz van csatlakoztatva a felügyelt tartományhoz gépeken rendszergazdai jogosultsággal rendelkező. A tartományhoz csatlakoztatott gépeket ehhez a csoporthoz hozzáadni a Rendszergazdák csoportnak. A csoport tagjai Ezenkívül használhatja a távoli asztal távolról csatlakozni a tartományhoz csatlakoztatott gépeket.

> [!NOTE]
> Tartományi rendszergazdaként vagy vállalati rendszergazdai engedélyek az Azure Active Directory Domain Services használatával létrehozott felügyelt tartományon nem rendelkezik. A felügyelt tartományba ezeket az engedélyeket a szolgáltatás számára van fenntartva, és nem érhetik el a bérlő felhasználóit. A konfigurációs feladat létrehozása a speciális felügyeleti csoport használatával azonban néhány kiemelt műveletek végrehajtására. Ezek a műveletek közé tartozik a számítógépek csatlakoztatása a tartományhoz, a tartományhoz csatlakoztatott gépeket a felügyeleti csoportba tartozó és a csoportházirend konfigurálásához.
>

A varázsló automatikusan létrehozza a felügyeleti csoport az Azure AD-címtárban. Ez a csoport "AAD DC rendszergazdák" nevezzük. Ha rendelkezik egy meglévő csoportot ezen a néven az Azure AD-címtárhoz, a varázsló ebbe a csoportba választja ki. Csoport tagsági használatával konfigurálhatja a **rendszergazdai csoport** varázsló lapja.

1. Kattintson a csoport tagságának konfigurálására, **AAD DC rendszergazdák**.

    ![Csoporttagság konfigurálása](./media/getting-started/domain-services-blade-admingroup.png)

2. Kattintson a **tagok hozzáadása** gomb az Azure AD-címtárban lévő felhasználók hozzáadásához a rendszergazda csoporthoz.

3. Amikor elkészült, kattintson a **OK** a áthelyezése a **összegzése** lapján.


## <a name="deploy-your-managed-domain"></a>A felügyelt tartomány üzembe helyezése

1. A a **összegzése** oldalon a varázsló, tekintse át a konfigurációs beállításokat a felügyelt tartományhoz. Léphet vissza egy lépést, a varázsló módosítások, ha szükséges. Amikor elkészült, kattintson a **OK** hozhat létre az új felügyelt tartományhoz.

    ![Összegzés](./media/getting-started/domain-services-blade-summary.png)

2. Megjelenik egy értesítés az Azure AD tartományi szolgáltatások központi telepítésének állapotát. Kattintson az értesítésre való központi telepítésére vonatkozó részletes állapot.

    ![Értesítés - telepítés folyamatban](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="check-the-deployment-status-of-your-managed-domain"></a>A felügyelt tartomány üzembe helyezés állapotának ellenőrzéséhez
A folyamat üzembe helyezésének a felügyelt tartomány órát is igénybe vehet egy.

1. Amíg az üzembe helyezés folyamatban van, Ön is megkeresheti a "tartományi szolgáltatások" az a **erőforrások keresése** keresőmezőbe. Válassza ki **Azure AD tartományi szolgáltatások** a keresési eredmény. A **Azure AD tartományi szolgáltatások** panel felsorolja a felügyelt tartományhoz, amely kiépítése folyamatban van.

    ![Keresse meg a felügyelt tartomány kiépítése folyamatban](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Kattintson a nevére, a felügyelt tartomány (például "contoso100.com") a felügyelt tartomány kapcsolatos további részletek megtekintéséhez.

    ![Tartományi szolgáltatások – üzembe helyezési állapota](./media/getting-started/domain-services-provisioning-state.png)

3. A **áttekintése** lapon látható, hogy a felügyelt tartomány kiépítése folyamatban van. A felügyelt tartomány nem konfigurálható, amíg a teljes mértékben ki van építve. Igénybe vehet a felügyelt tartomány kiépítése egy órát.

    ![Tartományi szolgáltatások – áttekintés lap a kiépítés során ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Ha a felügyelt tartomány teljes kiépítését követően a **áttekintése** lapon látható, a tartomány állapota **futó**.

    ![Tartományi szolgáltatások – Áttekintés lap a teljes kiépítés után](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >A kiépítési folyamat során az Azure AD Domain Services nagyvállalati alkalmazások "Tartományvezérlő szolgáltatásokhoz" és "AzureActiveDirectoryDomainControllerServices" nevű a címtáron belül hoz létre. Ezeket az alkalmazásokat a vállalati szolgáltatás a felügyelt tartományra van szükség. Rendkívül fontos, hogy ezek nem törlődnek bármikor.
    >

5. Az a **tulajdonságok** lapon látható két IP-címek, amelyeken a tartományi vezérlőket érhetők el a virtuális hálózathoz.

    ![Tartományi szolgáltatások – teljes kiépítés után tulajdonságai lap](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="need-help"></a>Segítség
Eltarthat egy óráig vagy két mindkét ki kell építeni a felügyelt tartományhoz tartozó tartományvezérlőket. Ha a központi telepítés sikertelen volt, vagy több, mint néhány óra múlva "Függő" állapotban elakadt, nyugodtan [kérjen segítséget a termékcsoport](active-directory-ds-contact-us.md).


## <a name="next-step"></a>Következő lépés
[4. feladat: Az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)

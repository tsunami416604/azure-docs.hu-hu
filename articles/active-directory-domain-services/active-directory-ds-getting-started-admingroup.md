---
title: 'Az Azure Active Directory tartományi szolgáltatások: Első lépések |} Microsoft Docs'
description: Engedélyezze az Azure Active Directory tartományi szolgáltatások az Azure portál használatával
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: d5b81a6d4bdda24208673e42757807aba60fea97
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263975"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Engedélyezze az Azure Active Directory tartományi szolgáltatások az Azure portál használatával


## <a name="task-3-configure-administrative-group"></a>3. feladat: a felügyeleti csoport konfigurálása
A konfigurációs feladat létrehoz egy felügyeleti csoport az Azure AD-címtárát. Ez a különleges felügyeleti csoport neve *AAD DC rendszergazdák*. A csoport tagjai, amelyek a felügyelt tartományra tartományhoz csatlakoztatott számítógépen rendszergazdai jogosultsággal rendelkező. A tartományhoz csatlakoztatott számítógépeken ehhez a csoporthoz hozzáadni a Rendszergazdák csoport. A csoport tagjai emellett használhatja a távoli asztal távolról csatlakozni a tartományhoz csatlakoztatott számítógépeken.

> [!NOTE]
> Nincs tartományi rendszergazda vagy a vállalati rendszergazda engedélyekkel kell rendelkeznie az Azure Active Directory tartományi szolgáltatások által létrehozott felügyelt tartományon. A felügyelt tartományok ezeket az engedélyeket a szolgáltatás által fenntartott, és nem érhetik el a bérlő belüli felhasználók. A speciális felügyeleti csoport létrehozása a konfigurációs feladat használatával azonban néhány kiemelt műveletek végrehajtására. Ezek a műveletek közé tartoznak a számítógépek csatlakoztatása a tartományhoz, tartományhoz csatlakozó számítógépeken a felügyeleti csoportba tartozó és a csoportházirend konfigurálásához.
>

A varázsló automatikusan létrehozza a felügyeleti csoport az Azure AD-címtárát. Ez a csoport neve "AAD DC rendszergazdák". Ha van ilyen nevű meglévő csoport az Azure AD-címtár, a varázsló kiválasztása ehhez a csoporthoz. Csoport tagsági használatával konfigurálhatja a **rendszergazdai csoport** varázsló lapja.

1. A csoporttagság konfigurálásához kattintson **AAD DC rendszergazdák**.

    ![Csoporttagság konfigurálása](./media/getting-started/domain-services-blade-admingroup.png)

2. Kattintson a **tagok hozzáadása** gomb az Azure AD-címtárban lévő felhasználók hozzáadásához a rendszergazda csoporthoz.

3. Amikor elkészült, kattintson a **OK** a áthelyezése a **összegzés** a varázsló.


## <a name="deploy-your-managed-domain"></a>A felügyelt tartományok telepítése

1. A a **összegzés** oldalon a varázsló, tekintse át a felügyelt tartományra konfigurációs beállításait. Később is visszatérhet módosításokat végezni, a varázsló minden lépésre szükség. Amikor elkészült, kattintson a **OK** az új felügyelt tartomány létrehozásához.

    ![Összegzés](./media/getting-started/domain-services-blade-summary.png)

2. Megjelenik egy értesítés, hogy az Azure AD tartományi szolgáltatásokhoz központi telepítés végrehajtási állapotát tartalmazza. Kattintson az értesítésre tekintse meg a központi telepítés részletes folyamatban van.

    ![Értesítés - telepítés folyamatban](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="check-the-deployment-status-of-your-managed-domain"></a>A felügyelt tartományok központi telepítési állapotának ellenőrzése
A felügyelt tartományok kiépítési folyamat egy óráig is eltarthat.

1. Amíg a telepítés folyamatban van, a a "tartományi szolgáltatások" kereshet a **keresési erőforrások** keresőmezőbe. Válassza ki **Azure AD tartományi szolgáltatások** a keresési eredmény alapján. A **Azure AD tartományi szolgáltatások** panel sorolja fel a felügyelt tartományra, amelyek telepítése folyamatban van.

    ![Található felügyelt tartomány telepítése folyamatban](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Kattintson a nevére, a felügyelt tartomány (például "contoso100.com"), a felügyelt tartományra kapcsolatos további részletek megtekintéséhez.

    ![Tartományi szolgáltatások - üzembe helyezési állapota](./media/getting-started/domain-services-provisioning-state.png)

3. A **áttekintése** lapon láthatja, hogy a felügyelt tartományra jelenleg telepítése folyamatban van. A felügyelt tartományra nem konfigurálható, amíg a teljes mértékben ki van építve. Azt a felügyelt tartomány teljesen kiépített egy órát is tarthat.

    ![Tartományi szolgáltatások - áttekintés lap során a kiépítési állapot ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Ha a felügyelt tartományra teljesen ki van építve, a **áttekintése** lapon láthatók a tartomány állapotának **futtató**.

    ![Tartományi szolgáltatások – Áttekintés lap a teljes kiépítés után](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >A telepítési folyamat során az Azure AD tartományi szolgáltatások vállalati alkalmazások a címtáron belül "Tartományvezérlő szolgáltatásokhoz" és "AzureActiveDirectoryDomainControllerServices" nevű hoz létre. A vállalati alkalmazások a felügyelt tartományok kiszolgálásához szükséges. Rendkívül fontos, hogy ezek nem törlődnek bármikor.
    >

5. Az a **tulajdonságok** lapon látni tartományi vezérlők érhetők el a virtuális hálózat két IP-címet.

    ![Tartományi szolgáltatások - teljesen kiépítése után tulajdonságai lap](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="need-help"></a>Segítség
Szükség lehet egy óráig vagy két úgy kell létrehozni a felügyelt tartományok mindkét tartományvezérlőn. Ha a telepítés sikertelen volt, vagy a "Függő" állapotba Beragadt több mint néhány óra múlva, nyugodtan [segítségért lépjen kapcsolatba a termékért felelős csoport](active-directory-ds-contact-us.md).


## <a name="next-step"></a>Következő lépés
[4. feladat: Az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)

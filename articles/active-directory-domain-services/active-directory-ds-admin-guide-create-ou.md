---
title: 'Az Azure Active Directory tartományi szolgáltatások: Felügyeleti útmutató |} Microsoft Docs'
description: Hozzon létre egy szervezeti egység (OU) az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 1facce770ca2b106d2e3113bb6d1a754ecb9376b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211713"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Hozzon létre egy szervezeti egység (OU) az az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
Az Azure AD tartományi szolgáltatások felügyelt tartományok közé tartoznak a "AADDC számítógépek" és "AADDC felhasználók" néven rendre két beépített tárolók. A AADDC Computers rendelkezik minden olyan számítógép, a felügyelt tartományra tartományhoz csatlakoztatott számítógép-objektumokat. A AADDC felhasználótároló felhasználókat és csoportokat tartalmazza az Azure AD-bérlő. Alkalmanként lehet a felügyelt tartomány munkaterhelések telepítése, a szolgáltatás fiókjainak létrehozásához szükséges. Erre a célra hozzon létre egy egyéni szervezeti egységet (OU) a felügyelt tartományra, és a szervezeti egységre belül szolgáltatásfiókok létrehozása. Ez a cikk bemutatja, hogyan hozzon létre egy szervezeti Egységet a felügyelt tartományok.

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy egy helyszíni címtár vagy egy csak felhőalapú directory szinkronizálva.
3. **Azure AD tartományi szolgáltatások** az Azure AD-címtár engedélyezni kell. Ha még nem tette meg, az összes ismertetett feladatok végrehajtásával a [első lépések útmutató](active-directory-ds-getting-started.md).
4. A tartományhoz csatlakoztatott virtuális gép, amelyből az Azure AD tartományi szolgáltatásokkal kezelt tartományi felügyeletéhez. Ha egy virtuális gép nem rendelkezik, a című cikkben ismertetett összes feladatok végrehajtásával [egy Windows rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).
5. A hitelesítő adatait kell egy **az "AAD DC rendszergazdák" csoportba tartozó felhasználói fiók** a könyvtárban, a felügyelt tartományok egyéni szervezeti létrehozásához.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>A Távfelügyelet tartományhoz csatlakoztatott virtuális gépen AD felügyeleti eszközök telepítése
Az Azure AD tartományi szolgáltatások felügyelt tartományok kezelheti távolról már ismerős eszközökkel Active Directory felügyeleti például az Active Directory felügyeleti központ (ADAC) vagy AD PowerShell segítségével. A bérlői rendszergazdák nem jogosult csatlakozni a távoli asztalon keresztül a felügyelt tartományra tartományvezérlők. A felügyelt tartományra felügyeletéhez, a telepítés AD felügyeleti eszközök a felügyelt tartományhoz csatlakoztatott virtuális gépen. Című cikkben [felügyelheti az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-administer-domain.md) utasításokat.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>A felügyelt tartományra a szervezeti egység létrehozása
Most, hogy az AD-felügyeleti eszközök telepítve vannak a tartományhoz csatlakoztatott virtuális gép, ezek az eszközök használatával is hozható létre egy szervezeti egységet a felügyelt tartományra. Hajtsa végre a következő lépéseket:

> [!NOTE]
> Csak a "AAD DC rendszergazdák" csoport tagjai rendelkeznek egy egyéni szervezeti létrehozásához szükséges jogosultságokkal. Győződjön meg arról, hogy ehhez a csoporthoz tartozó felhasználóként hajtsa végre a következő lépéseket.
>
>

1. A kezdőképernyőről kattintson **felügyeleti eszközök**. Meg kell jelennie a virtuális gépen telepített AD felügyeleti eszközök.

    ![A kiszolgálón telepített felügyeleti eszközök](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Kattintson a **Active Directory felügyeleti központ**.

    ![Active Directory felügyeleti központ](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. A tartomány megtekintéséhez kattintson a tartomány nevét, a bal oldali ablaktáblán (például "contoso100.com").

    ![Az ADAC - nézet tartomány](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. A jobb oldalon **feladatok** ablaktáblán kattintson a **új** a tartomány neve csomópontja alatt. Ebben a példában elemre kattint **új** jobb oldalán a "contoso100(local)" csomópontban **feladatok** ablaktáblán.

    ![Az ADAC - új szervezeti egység](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Létrehozhat egy szervezeti egységhez kell megjelennie. Kattintson a **szervezeti egység** elindíthatja a **szervezeti egység létrehozása** párbeszédpanel.
6. Az a **szervezeti egység létrehozása** párbeszédpanelen adja meg a **neve** az új szervezeti egység. Adjon meg egy rövid leírást a szervezeti egység. Is meg lehet, hogy a **kezelő** a szervezeti egység mezőt. Az egyéni szervezeti létrehozásához kattintson a **OK**.

    ![Az ADAC - létrehozás OU párbeszédpanel](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. Az újonnan létrehozott OU ekkor meg kell jelennie szerepel az AD felügyeleti központban (ADAC).

    ![Az ADAC - szervezeti egység létrehozása](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Engedélyek/biztonsági az újonnan létrehozott szervezeti egységek
Alapértelmezés szerint az egyéni szervezeti létrehozó felhasználó (a "AAD DC rendszergazdák" csoport tagja) rendszergazdai jogosultsággal (teljes hozzáférés) keresztül a szervezeti Egységhez. A felhasználó ezután használatától és jogosultságokat, más felhasználók számára, vagy a "AAD DC rendszergazdák" csoportba. Az alábbi képernyőfelvételen a felhasználó látható "bob@domainservicespreview.onmicrosoft.com" ki hozta létre az új "MyCustomOU" szervezeti egység teljes hozzáférésük kap.

 ![Az ADAC - új szervezeti biztonsági](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Tudnivalók a egyéni szervezeti egységek felügyelete
Most, hogy létrehozott egy egyéni szervezeti egység, lépjen tovább, és a szervezeti felhasználók, csoportok, számítógépek és szolgáltatásfiókok létrehozása. Nem helyezhető át felhasználókat és csoportokat a "AADDC felhasználók" szervezeti egység egyéni szervezeti egységekhez.

> [!WARNING]
> Felhasználói fiókok, csoportok, szolgáltatásfiókok és az Ön által létrehozott egyéni szervezeti egységek alapján számítógép-objektumok nem találhatók az Azure AD-bérlő. Más szóval ezek az objektumok ne jelenjen meg az Azure AD Graph API-val vagy az Azure AD felhasználói felületén. Ezek az objektumok találhatók csak az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz.
>
>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [A csoportházirend konfigurálja a felügyelt tartományhoz](active-directory-ds-admin-guide-administer-group-policy.md)
* [Az Active Directory felügyeleti központ: Első lépések](https://technet.microsoft.com/library/dd560651.aspx)
* [Szolgáltatásfiókok részletes útmutatója](https://technet.microsoft.com/library/dd548356.aspx)

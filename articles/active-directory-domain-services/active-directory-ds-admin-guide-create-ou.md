---
title: 'Az Azure Active Directory tartományi szolgáltatások: Felügyeleti útmutató |} A Microsoft Docs'
description: Hozzon létre egy szervezeti egység (OU) az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 49b915242ca0a4775236da3314eed816fde7ea6b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856264"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Hozzon létre egy szervezeti egység (OU) a az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
Az Azure AD Domain Services felügyelt tartomány "AADDC számítógépek" és "AADDC felhasználók" nevű, illetve két beépített tárolók közé tartozik. A "AADDC számítógépek" tároló rendelkezik minden olyan számítógép, a felügyelt tartományhoz csatlakoztatott számítógép-objektumai. A "AADDC felhasználók" tároló tartalmazza a felhasználók és csoportok az Azure AD-bérlőben. Alkalmanként lehet a felügyelt tartomány számítási feladatok üzembe helyezéséhez a szolgáltatásfiókok létrehozásához szükséges. Erre a célra hozzon létre egy egyéni szervezeti egység (OU) a felügyelt tartományon, és a szervezeti egységre belül szolgáltatásfiókok létrehozása. Ez a cikk bemutatja, hogyan hozhat létre egy szervezeti Egységet a felügyelt tartományra.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. Tartományhoz csatlakoztatott virtuális gép, amelyről az Azure AD Domain Services felügyelt tartomány felügyeletéhez. Ha egy virtuális gép nem rendelkezik, az összes című cikkben ismertetett feladatok végrehajtásával [Windows virtuális gépek csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).
5. A hitelesítő adatait kell egy **felhasználói fiók, az "AAD DC rendszergazdák" csoportba tartozó** a címtárban, egy egyéni szervezeti egység létrehozása a felügyelt tartományon.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>A távoli felügyeleti tartományhoz csatlakoztatott virtuális gép AD kiszolgálófelügyelet eszközeinek telepítése
Az Azure AD Domain Services felügyelt tartomány távolról a jól ismert az Active Directory felügyeleti eszközök például az Active Directory felügyeleti központ (ADAC) vagy AD PowerShell segítségével is felügyelhetők. A bérlői rendszergazdák nem rendelkezik jogosultságokkal a tartományvezérlők a távoli asztalon keresztül felügyelt tartományon való kapcsolódáshoz. A felügyelt tartomány felügyeletéhez, telepítse a AD felügyeleti eszközei szolgáltatás a felügyelt tartományhoz csatlakoztatott virtuális gépen. A cikkben nevű [egy Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md) útmutatást.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Hozzon létre egy szervezeti egységet a felügyelt tartományon
Most, hogy a rendszergazda AD-eszközök telepítve vannak a tartományhoz csatlakoztatott virtuális gépet, ezek az eszközök használatával hozzon létre egy szervezeti egységet a felügyelt tartományon. Hajtsa végre az alábbi lépéseket:

> [!NOTE]
> Csak az "AAD DC rendszergazdák" csoport tagjai rendelkeznek egy egyéni szervezeti egység létrehozása a szükséges jogosultságokkal. Győződjön meg arról, hogy ehhez a csoporthoz tartozó felhasználó, hajtsa végre az alábbi lépéseket.
>
>

1. A kezdőképernyőről kattintson **felügyeleti eszközök**. A felügyeleti eszközök AD, a virtuális gépen telepítve kell megjelennie.

    ![A kiszolgálón telepített felügyeleti eszközök](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Kattintson a **Active Directory felügyeleti központ**.

    ![Active Directory felügyeleti központ](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. A tartomány megtekintéséhez kattintson a bal oldali ablaktáblán (például "contoso100.com") a tartomány nevét.

    ![Az ADAC - nézet tartomány](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. A jobb oldalon **feladatok** ablaktáblán kattintson a **új** a tartomány nevét csomópontja alatt. Ebben a példában kattint **új** a jobb oldalon a "contoso100(local)" csomópont alatt **feladatok** ablaktáblán.

    ![Az ADAC - új szervezeti egység](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. A szervezeti egység létrehozásának lehetőségét kell megjelennie. Kattintson a **szervezeti egység** elindíthatja a **szervezeti egység létrehozása** párbeszédpanel.
6. Az a **szervezeti egység létrehozása** párbeszédpanelen adja meg egy **neve** az új szervezeti egység. Adjon meg egy rövid leírást a szervezeti egység. Akkor is beállíthatnak a **kezelő** mezőjét a szervezeti egység. Az egyéni szervezeti egység létrehozásához kattintson a **OK**.

    ![Az ADAC - szervezeti egység párbeszédpanel létrehozása](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. Az újonnan létrehozott OU most meg kell jelennie a AD felügyeleti központban (ADAC) található.

    ![Az ADAC - szervezeti egység létrehozása](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Engedélyek vagy biztonsági az újonnan létrehozott szervezeti egységek
Alapértelmezés szerint a felhasználó (az "AAD DC rendszergazdák" csoport tagja), aki létrehozta az egyéni szervezeti rendszergazdai jogosultsággal (teljes hozzáférés) keresztül a szervezeti Egységet. A felhasználó ezután lépjen tovább, és más felhasználók számára, vagy igény szerint az "AAD DC rendszergazdák" csoportba jogosultságok engedélyezése. Ahogy az alábbi képernyőképen a felhasználó "bob@domainservicespreview.onmicrosoft.com" ki hozta létre az új "MyCustomOU" szervezeti egység, teljes hozzáférést kap.

 ![Az ADAC - új szervezeti biztonsági](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Megjegyzések az egyéni szervezeti egységek felügyelete
Most, hogy létrehozott egy egyéni szervezeti egység, lépjen tovább, és a szervezeti egység felhasználók, csoportok, számítógépek és szolgáltatásfiókok létrehozása. Nem válthat felhasználók vagy csoportok "AADDC felhasználók" szervezeti egység egyéni szervezeti egységekhez.

> [!WARNING]
> Felhasználói fiókok, csoportok, szolgáltatásfiókok és egyéni szervezeti egységek alapján létrehozott számítógép-objektumok nem érhetők az Azure AD-bérlőben. Más szóval ezek az objektumok ne jelenjen meg az Azure AD Graph API-val vagy az Azure AD felhasználói felületén. Ezek az objektumok csak érhető el az Azure AD tartományi szolgáltatásokkal felügyelt tartományban.
>
>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [A csoportházirend konfigurálja egy felügyelt tartományon](active-directory-ds-admin-guide-administer-group-policy.md)
* [Active Directory felügyeleti központ: Első lépések](https://technet.microsoft.com/library/dd560651.aspx)
* [Szolgáltatásfiókok részletes útmutatója](https://technet.microsoft.com/library/dd548356.aspx)

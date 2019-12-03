---
title: Szervezeti egység (OU) létrehozása a Azure AD Domain Servicesban | Microsoft Docs "
description: Megtudhatja, hogyan hozhat létre és kezelhet egyéni szervezeti egységeket Azure AD Domain Services felügyelt tartományokban.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: e202eed8e3694245b5b4527578c02cfb518723f4
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705347"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Szervezeti egység (OU) létrehozása Azure AD Domain Services felügyelt tartományban

Active Directory tartományi szolgáltatások (AD DS) szervezeti egységek (OU-ket) lehetővé teszik az objektumok (például felhasználói fiókok, szolgáltatásfiókok vagy számítógépfiókok) logikai csoportosítását. Ezután hozzárendelheti a rendszergazdákat adott szervezeti egységekhez, és Csoportházirendet alkalmazhat a megcélzott konfigurációs beállítások betartatására.

Az Azure AD DS felügyelt tartományok két beépített szervezeti egységgel rendelkeznek – *AADDC számítógépek* és *AADDC-felhasználók*. A *AADDC számítógépek* szervezeti egysége a felügyelt tartományhoz csatlakozó összes számítógép számítógép-objektumait tartalmazza. A *AADDC-felhasználók* szervezeti egysége az Azure ad-bérlőből szinkronizált felhasználókat és csoportokat tartalmaz. Az Azure AD DSt használó munkaterhelések létrehozásakor és futtatásakor előfordulhat, hogy létre kell hoznia egy szolgáltatásfiókot az alkalmazásokhoz, hogy azok hitelesítése megtörténjen. A szolgáltatásfiókok rendszerezéséhez gyakran létre kell hoznia egy egyéni szervezeti egységet az Azure AD DS felügyelt tartományban, majd a szervezeti egységen belül létre kell hoznia a szolgáltatási fiókokat.

Hibrid környezetben a helyszíni AD DS környezetben létrehozott szervezeti egységek nem szinkronizálhatók az Azure AD DS. Az Azure AD DS felügyelt tartományok lapos szervezeti struktúrát használnak. Az összes felhasználói fiókot és csoportot a rendszer a *AADDC-felhasználók* tárolóban tárolja, annak ellenére, hogy a különböző helyszíni tartományokból vagy erdőkből szinkronizálva van, még akkor is, ha van konfigurálva egy hierarchikus szervezeti egység struktúrája.

Ez a cikk bemutatja, hogyan hozhat létre szervezeti egységet az Azure AD DS felügyelt tartományában.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előzetes teendők

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services-példány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
    * Ha szükséges, fejezze be az oktatóanyagot [egy felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="custom-ou-considerations-and-limitations"></a>Egyéni OU-megfontolások és korlátozások

Amikor egyéni szervezeti egységeket hoz létre egy Azure AD DS felügyelt tartományban, a felhasználók felügyeletéhez és a csoportházirend alkalmazásához további kezelési rugalmasságot szerezhet. A helyszíni AD DS környezethez képest bizonyos korlátozások és megfontolások az Azure-beli egyéni OU-struktúra létrehozásakor és kezelésekor AD DS:

* Egyéni szervezeti egységek létrehozásához a felhasználóknak a *HRE DC-rendszergazdák* csoport tagjának kell lenniük.
* Az egyéni szervezeti egységeket létrehozó felhasználók a szervezeti egység felett rendszergazdai jogosultságokkal (teljes hozzáférés) rendelkeznek, és az erőforrás tulajdonosa.
    * Alapértelmezés szerint az *HRE DC-rendszergazdák* csoport az egyéni szervezeti egység teljes hozzáférését is ellenőrzi.
* A rendszer létrehoz egy alapértelmezett szervezeti egységet a *AADDC felhasználók számára* , amely az Azure ad-bérlő összes szinkronizált felhasználói fiókját tartalmazza.
    * A felhasználók vagy csoportok nem helyezhetők át a *AADDC felhasználói* szervezeti egységből a létrehozott egyéni szervezeti egységekre. Csak az Azure AD DS felügyelt tartományában létrehozott felhasználói fiókok és erőforrások helyezhetők át egyéni szervezeti egységbe.
* Az egyéni szervezeti egységek alatt létrehozott felhasználói fiókok, csoportok, szolgáltatásfiókok és számítógép-objektumok nem érhetők el az Azure AD-bérlőben.
    * Ezek az objektumok nem jelennek meg az Azure AD Graph API vagy az Azure AD felhasználói felületén. Ezek csak az Azure AD DS felügyelt tartományában érhetők el.

## <a name="create-a-custom-ou"></a>Egyéni szervezeti egység létrehozása

Egyéni szervezeti egység létrehozásához a Active Directory felügyeleti eszközöket kell használnia egy tartományhoz csatlakoztatott virtuális gépről. A Active Directory felügyeleti központ segítségével megtekintheti, szerkesztheti és létrehozhatja az erőforrásokat egy Azure AD DS felügyelt tartományban, beleértve a szervezeti egységeket is.

> [!NOTE]
> Ha egyéni szervezeti egységet szeretne létrehozni egy Azure AD DS felügyelt tartományban, be kell jelentkeznie egy olyan felhasználói fiókba, amely tagja az *HRE DC-rendszergazdák* csoportnak.

1. Jelentkezzen be a felügyeleti virtuális gépre. A Azure Portal használatával történő kapcsolódás lépéseiért lásd: [Kapcsolódás Windows Server rendszerű virtuális géphez][connect-windows-server-vm].
1. A kezdőképernyőn válassza a **felügyeleti eszközök**elemet. Megjelenik a rendelkezésre álló felügyeleti eszközök listája, amely az oktatóanyagban a [felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm]lett telepítve.
1. A szervezeti egységek létrehozásához és kezeléséhez válassza ki a **Active Directory felügyeleti központ** elemet a felügyeleti eszközök listájából.
1. A bal oldali ablaktáblán válassza ki az Azure AD DS felügyelt tartományát, például *aadds.contoso.com*. Megjelenik a meglévő szervezeti egységek és erőforrások listája:

    ![Válassza ki az Azure AD DS felügyelt tartományát a Active Directory felügyeleti központ](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. A **feladatok** ablaktábla a Active Directory felügyeleti központ jobb oldalán jelenik meg. A tartomány alatt, például a *aadds.contoso.com*területen válassza az **új > szervezeti egység**lehetőséget.

    ![Válassza ki az új szervezeti egység létrehozásához szükséges lehetőséget a Active Directory felügyeleti központ](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. A **szervezeti egység létrehozása** párbeszédpanelen adja meg az új szervezeti egység (például *MyCustomOu*) **nevét** . Adja meg a szervezeti egység rövid leírását, például az *Egyéni szervezeti egységet a szolgáltatásfiókok számára*. Ha kívánja, megadhatja a szervezeti egység **felügyelt** mezőjét is. Az egyéni szervezeti egység létrehozásához kattintson **az OK gombra**.

    ![Egyéni szervezeti egység létrehozása a Active Directory felügyeleti központ](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. A Active Directory felügyeleti központban az egyéni szervezeti egység már fel van sorolva, és használható:

    ![A Active Directory felügyeleti központ használható egyéni szervezeti egység](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>Következő lépések

A felügyeleti eszközök használatával, illetve a szolgáltatásfiókok létrehozásával és használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Active Directory felügyeleti központ: Első lépések](https://technet.microsoft.com/library/dd560651.aspx)
* [Szolgáltatásfiókok részletes útmutatója](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

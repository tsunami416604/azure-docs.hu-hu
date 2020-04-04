---
title: Szervezeti egység létrehozása az Azure AD tartományi szolgáltatásokban | Microsoft Dokumentumok"
description: Ismerje meg, hogyan hozhat létre és kezelhet egyéni szervezeti egységet egy Azure AD tartományi szolgáltatások által felügyelt tartományban.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 61f951c0dd6561fc8d5a5de6b80e3759fd42eb78
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655558"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Szervezeti egység létrehozása egy Azure AD tartományi szolgáltatások által felügyelt tartományban

Az Active Directory tartományi szolgáltatások (AD DS) szervezeti egységei lehetővé teszik az objektumok, például a felhasználói fiókok, a szolgáltatásfiókok vagy a számítógépfiókok logikai csoportosítását. Ezután hozzárendelheti a rendszergazdákat az adott számítógépgyártókhoz, és csoportházirendet alkalmazhat a célzott konfigurációs beállítások kényszerítéséhez.

Az Azure AD DS által felügyelt tartományok a következő két beépített ous-t tartalmazzák:

* *AADDC Számítógépek* – a felügyelt tartományhoz csatlakozó összes számítógép számítógépobjektumait tartalmazza.
* *AADDC-felhasználók* – az Azure AD-bérlőből szinkronizált felhasználókat és csoportokat tartalmazza.

Az Azure AD DS-t használó számítási feladatok létrehozása és futtatása során előfordulhat, hogy szolgáltatási fiókokat kell létrehoznia az alkalmazások számára a hitelesítéshez. Ezek a szolgáltatásfiókok rendszerezéséhez gyakran hozzon létre egy egyéni szervezeti egységet az Azure AD DS felügyelt tartományban, majd hozzon létre szolgáltatásfiókokat az adott szervezeti egységen belül.

Hibrid környezetben a helyszíni Activeád-ds-környezetben létrehozott független szoftverei nincsenek szinkronizálva az Azure AD DS-sel. Az Azure AD DS által felügyelt tartományok egy sima szervezeti egység struktúrát használnak. Minden felhasználói fiók és csoport az *AADDC-felhasználók* tárolóban tárolódik, annak ellenére, hogy különböző helyszíni tartományokból vagy erdőkből vannak szinkronizálva, még akkor is, ha ott hierarchikus szervezeti egységstruktúrát konfigurált.

Ez a cikk bemutatja, hogyan hozhat létre egy szervezeti egységet az Azure AD DS felügyelt tartományban.

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén töltse ki az oktatóanyagot [az Azure Active Directory tartományi szolgáltatások példányának létrehozásához és konfigurálásához.][create-azure-ad-ds-instance]
* Az Azure AD DS felügyelt tartományához csatlakozott Windows Server felügyeleti virtuális gép.
    * Szükség esetén fejezze be az [oktatóanyagot egy felügyeleti virtuális gép létrehozásához.][tutorial-create-management-vm]
* Egy felhasználói fiók, amely az *Azure AD DC rendszergazdák* csoportjának tagja az Azure AD-bérlőben.

## <a name="custom-ou-considerations-and-limitations"></a>Egyéni szervezeti egységekkel kapcsolatos szempontok és korlátozások

Amikor egyéni független védelmi államokat hoz létre egy Azure AD DS felügyelt tartományban, további felügyeleti rugalmasságot kap a felhasználókezeléshez és a csoportházirend alkalmazásához. A helyszíni AD DS-környezethez képest az Azure AD DS-ben egy egyéni szervezeti egységstruktúra létrehozása és kezelése során bizonyos korlátozások és szempontok vannak:

* Egyéni számítógépgyártók létrehozásához a felhasználóknak az *AAD tartományvezérlő-rendszergazdák* csoport tagjának kell lenniük.
* Az egyéni szervezeti egységet létrehozó felhasználó rendszergazdai jogosultságokat (teljes körű vezérlést) kap a szervezeti egység felett, és ő az erőforrás tulajdonosa.
    * Alapértelmezés szerint az *AAD tartományvezérlő-rendszergazdák* csoport is teljes hozzáféréssel rendelkezik az egyéni szervezeti egységhez.
* Létrejön az *AADDC-felhasználók* alapértelmezett szervezeti egysége, amely az Azure AD-bérlő összes szinkronizált felhasználói fiókját tartalmazza.
    * Az *AADDC-felhasználók szervezeti* egységből nem helyezhet át felhasználókat vagy csoportokat a létrehozott egyéni szervezeti egységekbe. Csak az Azure AD DS felügyelt tartományban létrehozott felhasználói fiókok vagy erőforrások helyezhetők át az egyéni ous-okba.
* Az egyéni számítógép-környezetben létrehozott felhasználói fiókok, csoportok, szolgáltatásfiókok és számítógép-objektumok nem érhetők el az Azure AD-bérlőben.
    * Ezek az objektumok nem jelennek meg a Microsoft Graph API-val vagy az Azure AD felhasználói felületén; csak az Azure AD DS felügyelt tartományában érhetők el.

## <a name="create-a-custom-ou"></a>Egyéni szervezeti egység létrehozása

Egyéni szervezeti egység létrehozásához használja az Active Directory felügyeleti eszközöket egy tartományhoz csatlakozó virtuális gépről. Az Active Directory felügyeleti központ lehetővé teszi, hogy egy Azure AD DS által felügyelt tartományban tekintsen, szerkesszen és hozzon létre erőforrásokat, beleértve a saját erőforrásokat is.

> [!NOTE]
> Ha egyéni szervezeti egységet szeretne létrehozni egy Azure AD DS felügyelt tartományban, be kell jelentkeznie egy olyan felhasználói fiókba, amely az *AAD DC rendszergazdák* csoport tagja.

1. Jelentkezzen be a felügyeleti virtuális gép. Az Azure Portal használatával való csatlakozáslépéseiről a [Csatlakozás Windows Server virtuális géphez][connect-windows-server-vm]című témakörben található.
1. A kezdőképernyőn válassza a **Felügyeleti eszközök lehetőséget.** Megjelenik az oktatóanyagban telepített elérhető felügyeleti eszközök listája a [felügyeleti virtuális gép létrehozásához.][tutorial-create-management-vm]
1. A felelős személyek létrehozásához és kezeléséhez válassza az **Active Directory felügyeleti központ** elemet a felügyeleti eszközök listájából.
1. A bal oldali ablaktáblában válassza ki az Azure AD DS felügyelt tartományát, például *a aaddscontoso.com.* A meglévő ous-ok és erőforrások listája a következő:

    ![Az Azure AD DS által felügyelt tartomány kiválasztása az Active Directory felügyeleti központban](./media/create-ou/create-ou-adac-overview.png)

1. A **Feladatok** ablaktábla az Active Directory felügyeleti központ jobb oldalán látható. A tartomány ban, például *aaddscontoso.com,* válassza **az Új > szervezeti egység**lehetőséget.

    ![Válassza ki az új szervezeti egység létrehozásának lehetőségét az Active Directory felügyeleti központban](./media/create-ou/create-ou-adac-new-ou.png)

1. A **Szervezeti egység létrehozása** párbeszédpanelen adja meg az új szervezeti egység **nevét,** például *A MyCustomOu*. Adjon meg egy rövid leírást a szervezeti egységhez, például *egyéni szervezeti egységet a szolgáltatásfiókokhoz.* Ha szükséges, beállíthatja a szervezeti egység **felügyelt mezőjét** is. Az egyéni szervezeti egység létrehozásához válassza az **OK gombot.**

    ![Egyéni szervezeti egység létrehozása az Active Directory felügyeleti központból](./media/create-ou/create-ou-dialog.png)

1. Az Active Directory felügyeleti központban az egyéni szervezeti egység már szerepel a listában, és használható:

    ![Az Active Directory felügyeleti központban használható egyéni szervezeti egység](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>További lépések

A felügyeleti eszközök használatáról, illetve a szolgáltatásfiókok létrehozásáról és használatáról az alábbi cikkekben talál további információt:

* [Active Directory felügyeleti központ: Első lépések](https://technet.microsoft.com/library/dd560651.aspx)
* [Szolgáltatásfiókok részletes útmutatója](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

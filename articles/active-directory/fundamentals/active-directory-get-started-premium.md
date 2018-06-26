---
title: Azure AD Premium-fiók regisztrálása | Microsoft Docs
description: Azt ismerteti, hogyan regisztrálhat a Prémium szintű Azure Active Directory-kiadásra
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2017
ms.author: lizross
ms.reviewer: piotrci
ms.custom: it-pro;
ms.openlocfilehash: c15cbb632410eb0b6867677d7802960033dfdd44
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268382"
---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>Gyors útmutató: Prémium szintű Azure Active Directory – Regisztráció
Az Azure Active Directory (Azure AD) Prémium szintű kiadásával folytatott munka megkezdéséhez megvásárolhatja a licenceket, és hozzárendelheti őket Azure-előfizetéséhez. Ha új Azure-előfizetést hoz létre, aktiválnia kell a licenccsomagját és az Azure AD-szolgáltatás hozzáférését is a következő szakaszokban leírtak szerint. 

## <a name="sign-up-for-active-directory-premium"></a>Regisztrálás Prémium szintű Active Directory-kiadásra
A Prémium szintű Active Directory-kiadásra történő regisztrálásra több lehetősége van: 
* Az Azure- vagy Office 365-előfizetés használata
* Enterprise Mobility + Security licenccsomag használata
* Microsoft mennyiségi licenccsomag használata

### <a name="azure-or-office-365"></a>Azure vagy Office 365 
Azure- vagy Office 365-előfizetőként az interneten vásárolhatja meg a Prémium szintű Azure Active Directory-kiadást. 

A részletes lépésekért lásd: [A Prémium szintű Active Directory megvásárlása – meglévő ügyfelek](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) vagy [A Prémium szintű Active Directory megvásárlása – új ügyfelek](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

### <a name="enterprise-mobility--security"></a>Enterprise Mobility + Security
Az Enterprise Mobility + Security (EMS) csomaggal a szervezetek költséghatékony módon, együttesen, egy licenccsomagban használhatják a következő szolgáltatásokat: Prémium szintű Active Directory, Azure Information Protection és Microsoft Intune. Az EMS-ről az [Enterprise Mobility + Security weboldalán](https://www.microsoft.com/cloud-platform/enterprise-mobility-security) tájékozódhat, a megvásárolható EMS licenctípusokról pedig az [Enterprise Mobility + Security díjszabási lehetőségeit](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing) ismertető oldal nyújt további információkat.  

Az Azure AD használatát EMS-licenceken keresztül a következő licenclehetőségek egyikével kezdheti meg:

- Próbálja ki az EMS-t az [Enterprise Mobility + Security E5 ingyenes próba-előfizetésével](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).
- Vásároljon [Enterprise Mobility + Security E5 licenceket](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1)
- Vásároljon [Enterprise Mobility + Security E3 licenceket](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1)

### <a name="microsoft-volume-licensing"></a>Microsoft nagybani licencbeadás
A Prémium szintű Active Directory a [Microsoft Nagyvállalati szerződés](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 vagy több licenc esetén) vagy a [Open mennyiségi licencelés](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5–250 licenc esetén) keretén belül érhető el.

A nagybani licencbeadási vásárlási lehetőségekről a [nagybani licencbeadáson keresztüli vásárlást](https://www.microsoft.com/en-us/Licensing/how-to-buy/how-to-buy.aspx) ismertető oldalon tájékozódhat.

> [!NOTE]
> Az Azure Active Directory Prémium és Alapszintű kiadásai az Azure Active Directory világszerte elérhető példányával érhetők el a kínai ügyfelek számára. Az Azure Active Directory Prémium és Alapszintű kiadásai jelenleg nem támogatottak Kínában a 21Vianet által működtetett Microsoft Azure szolgáltatásban. További információkért lépjen velünk kapcsolatba az [Azure Active Directory fórumán](https://feedback.azure.com/forums/169401-azure-active-directory/).

Ha már vásárolt és aktivált Azure AD-licenceket az előző lépésekben használt Azure-előfizetéshez, a licenceket a rendszer automatikusan aktiválja ugyanabban a címtárban. Ha még nem, akkor folytassa az ebben a cikkben alább leírt lépésekkel.

## <a name="activate-your-license-plan"></a>A licenccsomag aktiválása
Ez az első Azure AD-licenccsomag, amelyet a Microsofttól vásárolt? Ha igen, visszaigazoló e-mailt fog kapni a vásárlás végeztével. Az első licenccsomag aktiválásához szüksége lesz erre az e-mailre.

**A licenccsomag aktiválásához hajtsa végre az alábbi lépések egyikét:**

1. Az aktiválás elindításához kattintson a **Bejelentkezés** vagy a **Regisztráció** gombra.
   
    ![Bejelentkezés](media/active-directory-get-started-premium/MOLSEmail.png)

    - Ha rendelkezik meglévő bérlővel, kattintson a **Bejelentkezés** gombra, hogy bejelentkezzen a meglévő rendszergazdai fiókjával. Jelentkezzen be az azon bérlőből származó globális rendszergazdai hitelesítő adatokkal, ahol a licenceket aktiválni szeretné.

    - Ha új Azure AD-bérlőt szeretne aktiválni a licenccsomaghoz, kattintson a **Regisztráció** gombra a **Fiókprofil létrehozása** párbeszédpanel megnyitásához.

        ![Fiókprofil létrehozása](media/active-directory-get-started-premium/MOLSAccountProfile.png)

Amikor végzett, megjelenik a következő párbeszédpanel a bérlő licenccsomagja aktiválásának megerősítéseként:

![Megerősítés](media/active-directory-get-started-premium/MOLSThankYou.png)

## <a name="activate-your-azure-active-directory-access"></a>Az Azure Active Directory-hozzáférés aktiválása
Ha új Prémium szintű Azure AD-licenceket egy létező előfizetéshez rendel hozzá, az Azure AD-hozzáférés már valószínűleg aktív. Más esetekben aktiválnia kell az **üdvözlő e-mail** fogadása után.  

Amikor az Ön által megvásárolt licencek érvénybe lépnek a címtárban, egy **üdvözlő e-mailt** kap. Az e-mail megerősíti, hogy elkezdheti a Prémium szintű Active Directory vagy az Enterprise Mobility + Security licenceinek és funkcióinak kezelését. 

> [!TIP]
> Az Azure AD-t nem érheti el az új bérlőjéhez, amíg nem aktiválja az Azure AD címtárhozzáférését a licenckiosztási folyamat végén automatikusan megkapott üdvözlő e-maillel. 

**Azure AD-hozzáférésének aktiválásához végezze el a következő lépéseket:**

1. Az **üdvözlő e-mailben** kattintson a **Bejelentkezés** hivatkozásra. 
   
    ![Üdvözlő e-mail](media/active-directory-get-started-premium/AADEmail.png)
2. Miután sikeresen bejelentkezett, el kell végeznie egy kéttényezős hitelesítést is egy mobileszköz használatával:
   
    ![Mobileszközös ellenőrzés](media/active-directory-get-started-premium/SignUppage.png)

Az aktiválás mindössze néhány percet vesz igénybe. Ezután hozzáférhet Azure AD-jéhez, és kezelni is tudja. 

## <a name="next-steps"></a>További lépések
Ebben a gyors útmutatóban megismerheti, hogyan regisztrálhat a Prémium szintű Azure AD-kiadásra, és hogyan aktiválhatja az Azure Active Directory hozzáférését. 

Ha már van Azure-előfizetése, a következő hivatkozással megkezdhet egy próbaidőszakot, vagy vásárolhat Prémium szintű licenceket az Azure Portalon.

> [!div class="nextstepaction"]
> [Azure AD Prémium szintű licencek aktiválása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade)
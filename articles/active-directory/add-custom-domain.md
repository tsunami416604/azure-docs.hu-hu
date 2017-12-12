---
title: "Az Azure AD hozzá egyéni tartományt |} Microsoft Docs"
description: "Ismerteti, hogyan adhatnak hozzá egyéni tartományt az Azure Active Directoryban."
services: active-directory
author: curtand
manager: mtillman
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: e7b85d5f4cd19c94fe904f16090e174d87ea120b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Gyors üzembe helyezés: Egyéni tartománynév hozzáadása az Azure Active Directoryhoz

Minden Azure AD-címtárral rendelkezik egy kezdeti tartománynevet formájában *tartománynév*. onmicrosoft.com. A kezdeti tartománynév nem módosítható vagy törölhető, de az Azure AD is adhat hozzá a vállalata tartománynevét. A szervezet például valószínűleg más üzleti és a felhasználók, akik jelentkezzen be vállalati tartománynév használt tartománynevek rendelkezik. Egyéni tartománynevek hozzáadása az Azure AD lehetővé teszi a felhasználóneveket rendeljen a könyvtárat, amelyek a felhasználók számára, mint "alice@contoso.com." Ahelyett, hogy "alice @*tartománynév*. onmicrosoft.com". A folyamat egyszerűen végrehajtható:

1. Az egyéni tartománynév hozzáadása a címtárhoz
2. Vegye fel a DNS-bejegyzést a tartománynévhez a tartománynév-regisztrálónál
3. Az egyéni tartománynév ellenőrzése az Azure AD-ben

## <a name="add-the-custom-domain-name-to-your-directory"></a>Az egyéni tartománynév hozzáadása a címtárhoz
1. Jelentkezzen be a [Azure-portálon](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki a bal oldali **egyéni tartománynevek**.
3. Válassza ki **egyéni tartomány hozzáadása**.
   
   ![Válassza ki a hozzáadása parancs](./media/add-custom-domain/add-custom-domain.png)
5. A **egyéni tartománynevek**, írja be a mezőbe, például "contoso.com", az egyéni tartomány nevét, és válassza ki **tartomány hozzáadása**. A névben mindenképpen szerepeljen a .com, a .net vagy egyéb legfelső szintű kiterjesztés.
6. A ***tartománynév*** (Ez azt jelenti, az új tartománynév a cím), a DNS-bejegyzés adatait az Azure ad-ben az egyéni tartománynév ellenőrzésére használt később összegyűjtése.
   
   ![DNS-bejegyzés adatainak lekérése](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Ha tervezi összevonni a helyszíni Windows Server AD az Azure ad-vel, akkor ki kell választania a **I tervezi, hogy ebben a tartományban az egyszeri bejelentkezés konfigurálása a helyi Active Directoryval** jelölőnégyzetet az Azure AD Connect eszköz futtatásakor a címtárak szinkronizálása. Ugyanazon a néven a helyszíni címtárral a összevonását választva regisztrálnia kell a **Azure AD tartományi** lépés a varázslóban. Megtekintheti, hogyan néz ki ez a lépés a varázslóban [ezekben az útmutatásokban](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Ha még nem rendelkezik Azure AD Connect eszközzel, [innen letöltheti](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>Vegye fel a DNS-bejegyzést a tartománynévhez a tartománynév-regisztrálónál
A következő lépés az egyéni tartománynév Azure AD-vel történő használatához a tartomány DNS-zónafájljának frissítése. Az Azure AD majd ellenőrizheti, hogy a szervezete rendelkezik-e az egyéni tartománynév. Használhat [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) az Azure vagy Office 365/külső DNS-rekordok az Azure, a, vagy adja hozzá a következő DNS-bejegyzés [egy másik DNS-regisztráló webhelyén](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Jelentkezzen be a tartomány tartománynév-regisztrálójába. Ha nem rendelkezik hozzáféréssel a DNS-bejegyzés frissítéséhez, kérjen meg egy olyan személyt vagy csapatot, amely rendelkezik a 2. lépés végrehajtásához szükséges hozzáféréssel, és értesíti, amikor a frissítés befejeződött.
2. Frissítse a tartomány DNS-zónafájlját az Azure AD által rendelkezésre bocsátott DNS-bejegyzés hozzáadásával. A DNS-bejegyzés semmilyen értelemben nem módosítja a rendszer viselkedését, például a levélkezelési útválasztást vagy a webes üzemeltetést sem.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Az egyéni tartománynév ellenőrzése az Azure AD-ben
A DNS-bejegyzés hozzáadását követően készen áll a tartománynév Azure AD-vel történő ellenőrzésére. A tartománynév csak azt követően a DNS-rekordok propagálása ellenőrizhetők. Ez a propagálás általában csak másodperceket vesz igénybe, de néha egy vagy több óráig is eltarthat. Ha az ellenőrzés nem sikerül első alkalommal, próbálkozzon újra később.

1. Jelentkezzen be [az Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely a bérlő globális rendszergazdája.
2. Válassza ki **egyéni tartománynevek**.
3. Válassza ki az ellenőrizni kívánt nem ellenőrzött tartomány nevét.
4. Ellenőrizze a bejegyzéseket, és válassza ki **ellenőrizze** az ellenőrzés végrehajtásához.

Mostantól [hozzá tud rendelni egyéni tartománynevet tartalmazó felhasználóneveket](active-directory-users-create-azure-portal.md). Felhő alapú felhasználói fiókokat hozhat létre, vagy a frissítés korábban szinkronizált a helyi felhasználói fiók adatait, az egyéni tartománynév használatával. Módosíthatja is szinkronizált felhasználói fióknak tartományi utótag adatokat a [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) vagy a [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

> [!TIP]
> 900 felügyelt tartománynév legfeljebb is egyezzen. Beállításakor minden helyszíni összevonási esetén a tartománynak az Active Directoryban, adhat hozzá minden könyvtárban 450 tartománynevek legfeljebb. További információkért lásd: [külső és a felügyelt tartománynév](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="troubleshooting"></a>Hibaelhárítás
Ha egy egyéni tartománynevet nem tudja ellenőrizni, próbálja meg a következő hibaelhárítási lépéseket:

1. **Várjon egy órát**. DNS-rekordokat kell propagálása előtt ellenőrizheti, hogy az Azure AD a tartományhoz. Ez a folyamat egy óráig vagy tovább is eltarthat.
2. **Győződjön meg róla, hogy a DNS-rekord helyesen lett megadva**. Hajtsa végre ezt a lépést a tartomány tartománynév-regisztrációs webhelyén. Az Azure AD ellenőrizze a tartománynevet, ha 
  * A DNS-bejegyzés nem szerepel a DNS-zónafájlját
  * Terméknévnek pontosan egyeznie kell a DNS-bejegyzést, amely az Azure AD meg a megadott nincs. 
  
  Ha nincs hozzáférése a tartomány DNS-rekordjainak frissítéséhez a tartománynév-regisztrálónál, ossza meg a DNS-bejegyzést egy ilyen engedélyekkel rendelkező személlyel vagy csapattal, majd kérje a DNS-bejegyzés hozzáadását.
3. **Tartománynév törlése egy Azure AD-ben található másik címtárból**. A tartománynév csak egyetlen címtárban ellenőrizhető. Ha egy tartomány nevét jelenleg egy másik könyvtár ellenőrzése után azt nem lehet ellenőrizni az új könyvtárban amíg nem törli a az egyik. Információ a tartománynevek törléséről: [Egyéni tartománynevek kezelése](active-directory-domains-manage-azure-portal.md).    

Ismételje meg a cikk adja hozzá mindegyik tartományneve.

## <a name="learn-more"></a>Részletek
[Az Azure ad-ben egyéni tartománynevek elméleti áttekintése](active-directory-domains-manage-azure-portal.md)

[Egyéni tartománynevek kezelése](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>Következő lépések
A gyors üzembe helyezés az egyéni tartománynév felvétele az Azure AD hogy megismerte. 

A következő hivatkozás segítségével új egyéni tartomány hozzáadása az Azure ad-ben az Azure portálról.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 
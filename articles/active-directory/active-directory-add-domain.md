---
title: "Egyéni tartománynév hozzáadása az Azure Active Directoryhoz | Microsoft Docs"
description: "A vállalati tartománynevek hozzáadása az Azure Active Directoryhoz és a nevek ellenőrzése."
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: 35a6e20a-9907-432b-9d36-16b916a5c249
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/30/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d24af6385c24860f40f6aab633e6212431809aea


---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>Egyéni tartománynév hozzáadása az Azure Active Directoryhoz
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-domains-add-azure-portal.md)
> * [klasszikus Azure portál](active-directory-add-domain.md)
> 
> 

Üzleti tevékenysége során a vállalatok egy vagy több tartománynevet használnak, a felhasználók pedig vállalati tartománynév használatával jelentkeznek be a vállalati hálózatra. Mivel most már Azure Active Directoryt (Azure AD-t) használ, vállalata tartománynevét is felveheti az Azure AD-ba. Ezzel lehetővé válik, hogy olyan felhasználóneveket rendeljen a címtárhoz, amelyek a felhasználók számára is felismerhetők, például ‘alice@contoso.com.’ A művelet egyszerűen elvégezhető:

1. Az egyéni tartománynév hozzáadása a címtárhoz
2. Vegye fel a DNS-bejegyzést a tartománynévhez a tartománynév-regisztrálónál
3. Az egyéni tartománynév ellenőrzése az Azure AD-ben

> [!NOTE]
> Ha tervezi az egyéni tartománynév beállítását, hogy azt használhassa Active Directory összevonási szolgáltatásokkal (AD FS) vagy egy másik biztonsági jogkivonatokkal kapcsolatos szolgáltatással (STS) a vállalati hálózaton, kövesse az [Add and configure a domain for federation with Azure Active Directory](active-directory-add-domain-federated.md) (Tartomány hozzáadása és beállítása Azure Active Directory-összevonáshoz) című részben leírt utasításokat. Ez akkor hasznos, ha szinkronizálni szeretné a felhasználókat a vállalati címtárból az Azure AD-be, és a [jelszókivonat szinkronizálása](active-directory-aadconnectsync-implement-password-synchronization.md) nem felel meg a követelményeinek.
> 
> 

## <a name="add-a-custom-domain-name-to-your-directory"></a>Egyéni tartománynév hozzáadása a címtárhoz
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com/) az Azure AD-címtár globális rendszergazdájának felhasználói fiókjával.
2. Az **Active Directory** területen nyissa meg a címtárat, majd válassza a **Tartományok** lapot.
3. A parancssávon válassza a **Hozzáadás** elemet. Adja meg az egyéni tartomány nevét (például: „contoso.com”). A névben mindenképpen szerepeljen a .com, a .net vagy egyéb legfelső szintű kiterjesztés, és hagyja az „egyszeri bejelentkezés” (összevonás) jelölőnégyzetet üresen.
4. Válassza a **Hozzáadás** lehetőséget.
5. A Tartomány hozzáadása varázsló második oldalán keresse meg azt a DNS-bejegyzést, amelyet az Azure AD fog használni annak ellenőrzésére, hogy az adott tartománynév valóban a szervezete tulajdonában van-e.

A tartománynév hozzáadását követően az Azure AD-nek ellenőriznie kell, hogy az adott tartománynév valóban a szervezete tulajdonában van-e. Mielőtt az Azure AD végre tudná hajtani ezt az ellenőrzést, DNS-bejegyzést kell hozzáadnia a tartománynév DNS zónafájljában. Ezt a feladatot a tartománynév-regisztrációs webhelyén kell végrehajtani.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Vegye fel a DNS-bejegyzést a tartomány tartománynév-regisztrálójánál.
A következő lépés az egyéni tartománynév Azure AD-vel történő használatához a tartomány DNS-zónafájljának frissítése. Ez lehetővé teszi, hogy az Azure AD ellenőrizze, hogy az adott tartománynév valóban a szervezete tulajdonában van-e.

1. Jelentkezzen be a tartomány tartománynév-regisztrálójába. Ha nem rendelkezik hozzáféréssel a DNS-bejegyzés frissítéséhez, kérjen meg egy olyan személyt vagy csapatot, amely rendelkezik a 2. lépés végrehajtásához szükséges hozzáféréssel, és értesíti, amikor a frissítés befejeződött.
2. Frissítse a tartomány DNS-zónafájlját az Azure AD által rendelkezésre bocsátott DNS-bejegyzés hozzáadásával. Ez a DNS-bejegyzés lehetővé teszi az Azure AD számára az adott tartomány tulajdonosának ellenőrzését. A DNS-bejegyzés semmilyen értelemben nem módosítja a rendszer viselkedését, például a levélkezelési útválasztást vagy a webes üzemeltetést sem.

DNS-bejegyzések hozzáadásával kapcsolatos segítségért olvassa el az [Útmutató DNS-bejegyzések hozzáadásához népszerű DNS-regisztrálókon](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/) című részt.

## <a name="verify-the-domain-name-with-azure-ad"></a>A tartománynév ellenőrzése az Azure AD-vel
A DNS-bejegyzés hozzáadását követően készen áll a tartománynév Azure AD-vel történő ellenőrzésére.

Ha még meg van nyitva a **Tartomány hozzáadása** varázsló, annak harmadik oldalán válassza az **Ellenőrzés** lehetőséget. Az **Ellenőrzés** kiválasztásakor az Azure AD megkeresi a DNS-bejegyzést a tartomány DNS-zónafájljában. Az Azure AD csak azután tudja ellenőrizni a tartománynevet, hogy a DNS-rekordok propagálása megtörtént. Ez a propagálás általában csak másodperceket vesz igénybe, de néha egy vagy több óráig is eltarthat. Ha az ellenőrzés nem sikerül első alkalommal, próbálkozzon újra később.

Ha a **Tartomány hozzáadása** varázsló már nincs megnyitva, a következőképpen ellenőrizheti a tartományt a [klasszikus Azure portálon](https://manage.windowsazure.com/):

1. Jelentkezzen be az Azure AD-címtár globális rendszergazdájának felhasználói fiókjával.
2. Nyissa meg a címtárat, majd válassza a **Tartományok** lapot.
3. Jelölje ki az ellenőrizni kívánt tartománynevet, és válassza az **Ellenőrzés** lehetőséget a parancssávon.
4. A párbeszédpanelen válassza az **Ellenőrzés** elemet az ellenőrzés befejezéséhez.

Mostantól [hozzá tud rendelni egyéni tartománynevet tartalmazó felhasználóneveket](active-directory-add-domain-add-users.md).

## <a name="troubleshooting"></a>Hibaelhárítás
Ha nem képes egyéni tartománynév ellenőrzésére, próbálja meg a következőket. A leggyakoribb okok ismertetésével kezdjük, és a végén megemlítünk néhány ritkábban jelentkező okot is.

1. **Várjon egy órát**. A DNS-rekordokat propagálni kell, mielőtt az Azure AD ellenőrizni tudja a tartományt. Ez egy vagy több órát is igénybe vehet.
2. **Győződjön meg róla, hogy a DNS-rekord helyesen lett megadva**. Hajtsa végre ezt a lépést a tartomány tartománynév-regisztrációs webhelyén. Az Azure AD nem tudja ellenőrizni a tartománynevet, ha a DNS-bejegyzés nincs a DNS-zónafájlban, vagy ha az nem pontosan egyezik azzal a DNS-bejegyzéssel, amelyet az Azure AD adott meg. Ha nincs hozzáférése a tartomány DNS-rekordjainak frissítéséhez a tartománynév-regisztrálónál, ossza meg a DNS-bejegyzést egy ilyen engedélyekkel rendelkező személlyel vagy csapattal, majd kérje a DNS-bejegyzés hozzáadását.
3. **Tartománynév törlése egy Azure AD-ben található másik címtárból**. A tartománynév csak egyetlen címtárban ellenőrizhető. Ha a tartománynév ellenőrzése egy másik címtárban már megtörtént, onnan előbb törölni kell, mielőtt ellenőrizni lehetne az új címtárban. Információ a tartománynevek törléséről: [Egyéni tartománynevek kezelése](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>További egyéni tartománynevek hozzáadása
Ha a szervezete több tartománynevet használ (például: „contoso.com” és „contosobank.com”), legfeljebb 900 tartománynév felvételére van lehetőség. Minden egyes tartománynév hozzáadásához hajtsa végre a jelen cikkben ismertetett lépéseket.

## <a name="next-steps"></a>Következő lépések
* [Egyéni tartománynevet tartalmazó felhasználónevek hozzárendelése](active-directory-add-domain-add-users.md)
* [Egyéni tartománynevek kezelése](active-directory-add-manage-domain-names.md)
* [Ismerkedés az Azure AD tartománykezelési fogalmaival](active-directory-add-domain-concepts.md)
* [Vállalati arculat megjelenítése a felhasználói bejelentkezés során](active-directory-add-company-branding.md)
* [Tartománynevek kezelése a PowerShell segítségével az Azure AD-ban](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)




<!--HONumber=Nov16_HO2-->



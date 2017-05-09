---
title: "Egyéni tartománynév hozzáadása és összevont bejelentkezés beállítása az Azure Active Directoryhoz | Microsoft Docs"
description: "A vállalati tartománynevek hozzáadása az Azure Active Directoryhoz és a összevont bejelentkezés beállítása az Azure Active Directory és a saját helyszíni összevont megoldás között."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 27126c7e-e6d6-4ef3-a4fb-f5f0706e749d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2016
ms.author: curtand;jeffsta
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 77d34a6ebce12465a15de158b9160a03db58c48e
ms.contentlocale: hu-hu
ms.lasthandoff: 12/07/2016


---
# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Egyéni tartománynév hozzáadása az Azure Active Directoryhoz
Konfigurálhat egyéni tartománynevet (például: „contoso.com”), így a contoso.com felhasználóinak összevont egyszeri bejelentkezési élményben lehet részük a vállalati hálózatról. Ha már rendelkezik Active Directory összevonási szolgáltatásokkal (AD FS), vagy egy másik összevonási kiszolgáló fut a vállalati hálózatán, beállíthatja az Azure AD-t az Azure AD Connect eszköz segítségével úgy, hogy az egyéni tartománynevét használja. Azure AD Connectet is használhat egy új AD FS-környezet üzembe helyezéséhez, és beállíthatja azt összevont egyszeri bejelentkezéshez az Azure AD-be.

Ha nem rendelkezik AD FS-sel, vagy nem tervezi AD FS vagy más összevonási kiszolgáló telepítését, kövesse az alábbi utasításokat: [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Egyéni tartománynév hozzáadása a címtárhoz
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com/) az Azure AD-címtár globális rendszergazdájának felhasználói fiókjával.
2. Az **Active Directory** területen nyissa meg a címtárat, majd válassza a **Tartományok** lapot.
3. A parancssávon válassza a **Hozzáadás** lehetőséget, majd adja meg az egyéni tartomány nevét (például „contoso.com”). A névben mindenképpen szerepeljen a .com, a .net vagy egyéb legfelső szintű kiterjesztés.
4. Válassza a **Szeretném konfigurálni ezt a tartományt egyszeri bejelentkezéshez a helyi Active Directoryhoz** jelölőnégyzetet.
5. Válassza a **Hozzáadás** lehetőséget.

Futtassa az Azure AD Connect eszközt azon DNS-bejegyzés beszerzéséhez, amelyet az Azure AD fog használni a tartomány ellenőrzéséhez. Egy DNS-bejegyzés lesz látható az **Azure AD-tartomány** lépésnél a varázslóban. Megtekintheti, hogyan néz ki ez a lépés a varázslóban [ezekben az útmutatásokban](connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Ha még nem rendelkezik Azure AD Connect eszközzel, [innen letöltheti](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Vegye fel a DNS-bejegyzést a tartomány tartománynév-regisztrálójánál.
A következő lépés az egyéni tartománynév Azure AD-vel történő használatához a tartomány DNS-zónafájljának frissítése. Ez lehetővé teszi, hogy az Azure AD ellenőrizze, hogy az adott tartománynév valóban a szervezete tulajdonában van-e.

1. Jelentkezzen be a tartománynevének tartománynév-regisztrálójához tartozó webhelyre. Ha nem rendelkezik hozzáféréssel ennek elvégzéséhez, kérjen meg a szervezetéből egy olyan személyt vagy csapatot, amely rendelkezik a 2. lépés végrehajtásához szükséges hozzáféréssel, és értesíti, amikor a frissítés befejeződött.
2. Frissítse a tartomány DNS-zónafájlját az Azure AD által rendelkezésre bocsátott DNS-bejegyzés hozzáadásával. Ez a DNS-bejegyzés lehetővé teszi az Azure AD számára az adott tartomány tulajdonosának ellenőrzését. A DNS-bejegyzés semmilyen értelemben nem módosítja a rendszer viselkedését, például a levélkezelési útválasztást vagy a webes üzemeltetést sem.

Az ezzel a lépéssel kapcsolatos segítségért olvassa el az [Útmutató DNS-bejegyzések hozzáadásához népszerű DNS-regisztrálókon](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/) című részt.

## <a name="verify-the-domain-name-with-azure-ad"></a>A tartománynév ellenőrzése az Azure AD-vel
A DNS-bejegyzés hozzáadását követően készen áll a tartománynév Azure AD-vel történő ellenőrzésére.

A tartomány ellenőrzéséhez válassza ki a **Tovább** lehetőséget az **Azure AD-tartomány** lépésnél az Azure AD Connect varázslójában. Az Azure AD megkeresi a DNS-bejegyzést a tartománynév DNS-zónafájljában. Az Azure AD csak azután ellenőrzi a tartománynevet, miután a DNS-rekordok propagálása megtörtént. A propagálás általában csak másodperceket vesz igénybe, de néha egy vagy több óráig is eltarthat. Ha az ellenőrzés nem sikerül első alkalommal, próbálkozzon újra később.

Majd folytassa a fennmaradó lépésekkel az Azure AD Connect varázslójában. Ez szinkronizálja a felhasználókat a Windows Server AD-ből az Azure AD-be. Az összevonáshoz beállított tartomány szinkronizált felhasználóinak összevont egyszeri bejelentkezési élményben lehet részük a vállalati hálózatról Azure AD-re bejelentkezéskor.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha nem képes egyéni tartománynév ellenőrzésére, próbálja meg a következőket. A leggyakoribb okok ismertetésével kezdjük, és a végén megemlítünk néhány ritkábban jelentkező okot is.

1. **Várjon egy órát**. A DNS-rekordokat propagálni kell, mielőtt az Azure AD ellenőrizni tudja a tartományt. Ez egy vagy több órát is igénybe vehet.
2. **Győződjön meg róla, hogy a DNS-rekord helyesen lett megadva**. Hajtsa végre ezt a lépést a tartomány tartománynév-regisztrációs webhelyén. Az Azure AD nem tudja ellenőrizni a tartománynevet, ha a DNS-bejegyzés nincs a DNS-zónafájlban, vagy ha az nem pontosan egyezik azzal a DNS-bejegyzéssel, amelyet az Azure AD adott meg. Ha nincs hozzáférése a tartomány DNS-rekordjainak frissítéséhez a tartománynév-regisztrálónál, ossza meg a DNS-bejegyzést egy ilyen engedélyekkel rendelkező személlyel vagy csapattal, majd kérje a DNS-bejegyzés hozzáadását.
3. **Tartománynév törlése egy Azure AD-ben található másik címtárból**. A tartománynév csak egyetlen címtárban ellenőrizhető. Ha a tartománynév ellenőrzése egy másik címtárban már megtörtént, onnan előbb törölni kell, mielőtt ellenőrizni lehetne az új címtárban. Információ a tartománynevek törléséről: [Egyéni tartománynevek kezelése](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>További egyéni tartománynevek hozzáadása
Ha a szervezete több tartománynevet használ (például: „contoso.com” és „contosobank.com”), legfeljebb 900 tartománynév felvételére van lehetőség. Minden egyes tartománynév hozzáadásához hajtsa végre a jelen cikkben ismertetett lépéseket.

## <a name="next-steps"></a>Következő lépések
* [Egyéni tartománynevek kezelése](active-directory-add-manage-domain-names.md)
* [Ismerkedés az Azure AD tartománykezelési fogalmaival](active-directory-add-domain-concepts.md)
* [Vállalati arculat megjelenítése a felhasználói bejelentkezés során](active-directory-add-company-branding.md)
* [Tartománynevek kezelése a PowerShell segítségével az Azure AD-ban](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)



<properties
    pageTitle="Egyéni tartománynév hozzáadása az Azure Active Directoryhoz | Microsoft Azure"
    description="A vállalati tartománynevek hozzáadása az Azure Active Directoryhoz és a nevek ellenőrzése."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/20/2016"
    ms.author="curtand;jeffsta"/>

# Egyéni tartománynév hozzáadása az Azure Active Directoryhoz

Üzleti tevékenysége során a vállalatok egy vagy több tartománynevet használnak, a felhasználók pedig vállalati tartománynév használatával jelentkeznek be a vállalati hálózatra. Mivel most már Azure Active Directoryt (Azure AD-t) használ, vállalata tartománynevét is felveheti az Azure AD-ba. Ezzel lehetővé válik, hogy olyan felhasználóneveket rendeljen a címtárhoz, amelyek a felhasználók számára is felismerhetők (például „alice@contoso.com”). A folyamat egyszerűen végrehajtható:

- A klasszikus Azure portálon vegye fel saját tartománynevét a **Tartomány hozzáadása** varázslóval.

- A klasszikus Azure AD portálon vagy az Azure AD Connect eszköz használatával szerezze be a DNS-bejegyzést.

- Vegye fel a tartománynévhez tartozó DNS-bejegyzést a DNS zónafájljába a DNS-regisztráló webhelyén.

- A klasszikus Azure AD portálon vagy az Azure AD Connect eszköz használatával ellenőrizze a tartománynevet.


Amíg nem ellenőrzi az egyéni tartománynevet, a felhasználóknak olyan felhasználónévvel kell bejelentkezniük, mint például az „alice@contoso.onmicrosoft.com”, amely a címtár eredeti tartománynevét használja. Ha több tartománynévre van szüksége (például „contoso.com” és „contosobank.com”), legfeljebb 900 tartománynév felvételére van lehetőség. Minden egyes tartománynév hozzáadásához hajtsa végre a jelen cikkben ismertetett lépéseket.

## Egyéni tartománynév hozzáadása a címtárhoz

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com/) az Azure AD-címtár globális rendszergazdájának felhasználói fiókjával.

2. Az **Active Directory** területen nyissa meg a címtárat, majd válassza a **Tartományok** lapot.

3. A parancssávon válassza a **Hozzáadás** lehetőséget, majd adja meg az egyéni tartomány nevét (például „contoso.com”). A névben mindenképpen szerepeljen a .com, a .net vagy egyéb legfelső szintű kiterjesztés.

4. Ha a tartománynál [összevont bejelentkezést](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) tervez konfigurálni a helyszíni Active Directoryval, jelölje be a jelölőnégyzetet.

5. Válassza a **Hozzáadás** lehetőséget.

A tartománynév hozzáadását követően az Azure AD-nek ellenőriznie kell, hogy az adott tartománynév valóban a szervezete tulajdonában van-e. Mielőtt az Azure AD végre tudná hajtani ezt az ellenőrzést, DNS-bejegyzést kell hozzáadnia a tartománynév DNS zónafájljában. Ezt a feladatot a tartománynév-regisztrációs webhelyén kell végrehajtani.

## A tartománynévhez tartozó DNS-bejegyzés beszerzése

A DNS-bejegyzések a **Tartomány hozzáadása** varázsló második oldalán találhatók, ha nem végez összevonást a helyszíni Windows Server Active Directoryval.

Ha a tartományt összevonásra konfigurálja, a rendszer az Azure AD Connect eszköz letöltéséhez irányítja át. [A tartománynév-regisztrálóban hozzáadandó DNS-bejegyzések beszerzéséhez](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation) futtassa az Azure AD Connect eszközt. Az Azure AD Connect eszköz az Azure AD tartománynevét is ellenőrzi.

## DNS-bejegyzés hozzáadása a DNS-zónafájlhoz

1.  Jelentkezzen be a tartomány tartománynév-regisztrálójába. Ha nem rendelkezik megfelelő engedélyekkel a DNS-bejegyzés frissítéséhez, kérjen meg egy ilyen jogosultsággal rendelkező személyt vagy csapatot a DNS-bejegyzés hozzáadására.

2.  Frissítse a tartomány DNS-zónafájlját az Azure AD által rendelkezésre bocsátott DNS-bejegyzés hozzáadásával. Ez a DNS-bejegyzés lehetővé teszi az Azure AD számára az adott tartomány tulajdonosának ellenőrzését. A DNS-bejegyzés semmilyen értelemben nem módosítja a rendszer viselkedését, például a levélkezelési útválasztást vagy a webes üzemeltetést sem. A DNS-rekordok propagálása akár egy órát is igénybe vehet.

[Útmutató DNS-bejegyzések hozzáadásához népszerű DNS-regisztrálókon](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## A tartománynév ellenőrzése az Azure AD-vel

A DNS-bejegyzés hozzáadását követően meg kell győződnie arról, hogy az Azure AD ellenőrizte-e a tartománynevet. Ez a végső lépés a sikerhez.

Ha még meg van nyitva a **Tartomány hozzáadása** varázsló, annak harmadik oldalán válassza az **Ellenőrzés** lehetőséget. Mielőtt megkezdené az ellenőrzést, várjon legalább egy órát a DNS-bejegyzés propagálására.

Ha a **Tartomány hozzáadása** varázsló már nincs megnyitva, a következőképpen ellenőrizheti a tartományt a [klasszikus Azure portálon](https://manage.windowsazure.com/):

1.  Jelentkezzen be az Azure AD-címtár globális rendszergazdájának felhasználói fiókjával.

2.  Nyissa meg a címtárat, majd válassza a **Tartományok** lapot.

3.  Jelölje ki az ellenőrizni kívánt tartományt.

4.  A parancssávon válassza az **Ellenőrzés** lehetőséget, majd a párbeszédpanelen válassza szintén az **Ellenőrzés** lehetőséget.

Gratulálunk a sikeres végrehajtáshoz! Mostantól [hozzá tud rendelni egyéni tartománynevet tartalmazó felhasználóneveket](active-directory-add-domain-add-users.md). Ha a tartománynév ellenőrzése során bármilyen problémát tapasztal, tekintse meg a [Hibaelhárítás](#troubleshooting) című szakaszt.

## Hibaelhárítás
Ha nem képes egyéni tartománynév ellenőrzésére, ennek néhány potenciális oka lehet. A leggyakoribb okok ismertetésével kezdjük, és a végén megemlítünk néhány ritkábban jelentkező okot is.

- A DNS-bejegyzés propagálása előtt próbálkozott a tartománynév ellenőrzésével. Várjon egy ideig, majd próbálkozzon újra.

- A DNS-rekord egyáltalán nem lett megadva. Ellenőrizze a DNS-bejegyzést, várjon annak propagálására, majd próbálkozzon újra.

- A tartománynév ellenőrzése egy másik címtárban már megtörtént. Keresse meg a tartománynevet, törölje azt a címtárból, majd próbálkozzon újra.

- A DNS-rekord hibát tartalmaz. Javítsa ki a hibát, majd próbálkozzon újra.

- Nem rendelkezik megfelelő engedélyekkel a DNS-rekordok frissítéséhez. Ossza meg a DNS-bejegyzéseket egy ilyen engedélyekkel rendelkező személlyel vagy csapattal, majd kérje fel őt/őket a DNS-bejegyzés hozzáadására.


## További lépések

-   [Az egyéni tartománynevet tartalmazó felhasználónevek hozzárendelése](active-directory-add-domain-add-users.md)
-   [Egyéni tartománynevek kezelése](active-directory-add-manage-domain-names.md)
-   [Ismerkedés az Azure AD tartománykezelési fogalmaival](active-directory-add-domain-concepts.md)
-   [Vállalati arculat megjelenítése a felhasználói bejelentkezés során](active-directory-add-company-branding.md)
-   [Tartománynevek kezelése a PowerShell segítségével az Azure AD-ban](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)



<!--HONumber=Jun16_HO2-->



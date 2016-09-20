<properties
    pageTitle="Azure AD tartományi szolgáltatások: Jelszavak szinkronizálásának engedélyezése | Microsoft Azure"
    description="Első lépések az Azure Active Directory tartományi szolgáltatások használatával"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/06/2016"
    ms.author="maheshu"/>

# Azure AD tartományi szolgáltatások *(előzetes kiadás)* – Jelszavak szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatásokra

## 5. feladat – Jelszavak szinkronizálásának engedélyezése az AAD tartományi szolgáltatásokra kizárólag felhőalapú Azure AD-címtáron
Miután engedélyezte az Azure AD tartományi szolgáltatásokat az Azure AD bérlőn, a következő konfigurációs feladat a hitelesítő adatok szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatásokra. Így a felhasználók vállalati hitelesítői adataikkal jelentkezhetnek be a felügyelt tartományra.

Ennek a lépései eltérőek attól függően, hogy a vállalat egy kizárólag felhőalapú Azure AD-címtárral rendelkezik, vagy a szinkronizálás a helyszíni címtárra történik az Azure AD Connect használatával.

<br>

> [AZURE.SELECTOR]
- [Kizárólag felhőalapú Azure AD-címtár](active-directory-ds-getting-started-password-sync.md)
- [Szinkronizált Azure AD-címtár](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Kerberos és NTLM hitelesítő adatok kivonatai létrehozásának engedélyezése kizárólag felhőalapú Azure AD-címtáron
Amennyiben a szervezet egy kizárólag felhőalapú Azure AD-címtárral rendelkezik, az Azure AD tartományi szolgáltatásokat használó felhasználóknak módosítaniuk kell jelszavukat. A jelszómódosítási folyamat eredményeképp az Azure AD tartományi szolgáltatások által a Kerberos- és NTLM-hitelesítéshez igényelt hitelesítő adatok kivonatait az Azure AD szolgáltatásban kell létrehozni. Hagyhatja az Azure AD tartományi szolgáltatásokat használó összes felhasználó jelszavát elévülni, vagy utasíthatja a felhasználókat, hogy módosítsák jelszavukat.

Az alábbi utasításokat küldje el a felhasználóknak a jelszavuk módosításához:

1. Lépjen a szervezet Azure AD hozzáférési panel oldalára. Ez általában a [http://myapps.microsoft.com](http://myapps.microsoft.com) oldalon érhető el.

2. Válassza a **profil** lapot ezen az oldalon.

3. Kattintson a **Jelszó módosítása** csempére ezen az oldalon a jelszómódosítás kezdeményezéséhez.

    ![Hozzon létre egy virtuális hálózatot az Azure AD tartományi szolgáltatásokhoz.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Ekkor megjelenik a **jelszómódosítás** oldal. A felhasználók létező (régi) jelszavuk beírásával léphetnek tovább a jelszó módosítására.

    ![Hozzon létre egy virtuális hálózatot az Azure AD tartományi szolgáltatásokhoz.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Miután a felhasználók módosították jelszavukat, az új jelszó rövidesen használható az Azure AD tartományi szolgáltatásokban. Néhány perc múltán a felhasználók újonnan módosított jelszavukkal bejelentkezhetnek a felügyelt tartományra kapcsolódó számítógépekre.


<br>

## Kapcsolódó tartalom

- [Jelszavak szinkronizálásának engedélyezése az AAD tartományi szolgáltatásokra szinkronizált Azure AD-címtáron](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Az Azure AD tartományi szolgáltatások által felügyelt tartományok adminisztrációja](active-directory-ds-admin-guide-administer-domain.md)

- [Windows virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)

- [Red Hat Enterprise Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-rhel-linux-vm.md)



<!--HONumber=sep16_HO1-->



---
title: "Active Directory Domain Services: Jelszavak szinkronizálásának engedélyezése | Microsoft Docs"
description: "Első lépések az Azure Active Directory tartományi szolgáltatások használatával"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b7b5f92c0093faa96a367fc95d459b1babd99789
ms.lasthandoff: 04/28/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>Jelszavak szinkronizálásának engedélyezése az Active Directory Domain Servicesben
Az előző feladatokban engedélyezte az Active Directory Domain Servicest (AD DS) az Azure Active Directory (Azure AD) bérlő számára. A következő feladat az NT LAN Manager (NTLM) és a Kerberos hitelesítéshez szükséges hitelesítésiadat-kivonatok szinkronizálásának engedélyezése az Active Directory Domain Services használatával. A bejelentkezési adatok szinkronizálásának beállítását követően a felhasználók a vállalati hitelesítői adataikkal jelentkezhetnek be a felügyelt tartományba.

Az eljárások eltérőek lehetnek aszerint, hogy a szervezet csak felhőalapú Azure AD-bérlővel rendelkezik-e, vagy be van állítva az Azure AD Connect használatával a helyszíni címtárral történő szinkronizálás.

> [!div class="op_single_selector"]
> * [Kizárólag felhőalapú Azure AD-bérlő](active-directory-ds-getting-started-password-sync.md)
> * [Szinkronizált Azure AD-bérlő](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>5. feladat – Jelszavak szinkronizálásának engedélyezése az Active Directory Domain Servicesre kizárólag felhőalapú Azure AD-címtáron
A felhasználók a felügyelt tartományon való hitelesítéséhez az Active Directory Domain Servicesnek az NTLM- és Kerberos-hitelesítéshez megfelelő formátumú hitelesítőadat-kivonatokra van szükségük. Ha nem engedélyezi az Active Directory Domain Servicet a bérlő számára, akkor az Azure AD nem az NTLM- vagy Kerberos-hitelesítéshez szükséges formátumban hozza létre vagy tárolja a hitelesítési adatok kivonatait. Nyilvánvaló biztonsági okokból az Azure AD nem tiszta szöveges formátumban tárolja a hitelesítő adatokat. Az Azure AD ezért nem tudja létrehozni az NTLM vagy Kerberos hitelesítésiadat-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

> [!NOTE]
> Ha a szervezet kizárólag felhőalapú Azure AD-bérlővel rendelkezik, az Active Directory Domain Servicest használó felhasználóknak módosítaniuk kell jelszavukat.
>
>

A jelszómódosítási folyamat eredményeképp az Active Directory Domain Services által a Kerberos- és NTLM-hitelesítéshez igényelt hitelesítő adatok kivonatait az Azure AD szolgáltatásban kell létrehozni. Hagyhatja az Active Directory Domain Servicest használó összes felhasználó jelszavát elévülni, vagy utasíthatja őket, hogy módosítsák jelszavukat.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Kerberos és NTLM hitelesítőadat-kivonatok létrehozásának engedélyezése egy kizárólag felhőalapú Azure AD-bérlőn
Az alábbi utasításokat küldje el a végfelhasználóknak a jelszavuk módosításához:

1. Lépjen a szervezet [Azure AD hozzáférési panel](http://myapps.microsoft.com) oldalára.
2. A hozzáférési panel ablakában válassza a **profil** lapot.
3. Kattintson a **Jelszó módosítása** csempére.

    ![Az Azure AD Hozzáférési panel „Jelszó módosítása” csempéje](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Ha a hozzáférési panel ablakában nem jelenik meg a **Jelszó módosítása** lehetőség, győződjön meg róla, hogy a szervezethez be van állítva a [jelszókezelés az Azure AD-ben](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. A **Jelszó módosítása** oldalon adja meg a meglévő (régi) jelszavát, adja meg az új jelszót, majd erősítse meg.

    ![Hozzon létre virtuális hálózatot az Azure AD tartományi szolgáltatásokhoz.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Kattintson a **Submit** (Küldés) gombra.

Néhány perccel azután, hogy módosította a jelszavát, az új jelszó használható lesz az Active Directory Domain Servicesben. Néhány további perc (átlagosan körülbelül 20 perc) múlva az új jelszavával bejelentkezhet a felügyelt tartományhoz kapcsolódó számítógépekre.

## <a name="next-steps"></a>Következő lépések
* [Saját jelszó frissítése](../active-directory/active-directory-passwords-update-your-own-password.md)
* [A jelszókezelés első lépései az Azure AD-ben](../active-directory/active-directory-passwords-getting-started.md)
* [Jelszó-szinkronizálás engedélyezése Active Directory Domain Servicesre szinkronizált Azure AD bérlő esetén](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Az Azure Active Directory tartományi szolgáltatások által felügyelt tartományok adminisztrációja](active-directory-ds-admin-guide-administer-domain.md)
* [Windows virtuális gép csatlakoztatása az Active Directory Domain Services által felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Red Hat Enterprise Linux virtuális gép csatlakoztatása az Active Directory Domain Services által felügyelt tartományhoz](active-directory-ds-admin-guide-join-rhel-linux-vm.md)


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
ms.date: 06/30/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4b6da997f44860dccb2aa2571ce099ab2d0231f3
ms.contentlocale: hu-hu
ms.lasthandoff: 07/08/2017


---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Jelszavak szinkronizálásának engedélyezése az Azure Active Directory Domain Services tartományi szolgáltatásokra
Az előző feladatokban engedélyezte az Active Directory Domain Servicest az Azure Active Directory (Azure AD) bérlő számára. A következő feladat az NT LAN Manager (NTLM) és Kerberos hitelesítésiadat-kivonatok Azure AD tartományi szolgáltatásokkal való szinkronizálásának engedélyezése. A bejelentkezési adatok szinkronizálásának beállítását követően a felhasználók a vállalati hitelesítői adataikkal jelentkezhetnek be a felügyelt tartományba.

A folyamat lépései eltérőek a csak felhőalapú felhasználói fiókok és a helyszíni könyvtárból az Azure AD Connect használatával szinkronizált felhasználói fiókok esetében.  Ha az Azure AD-bérlő csak felhőalapú és a helyszíni AD-ből származó felhasználókkal is rendelkezik, mindkét lépést végre kell hajtania.

<br>

> [!div class="op_single_selector"]
> * **Csak felhőalapú felhasználói fiókok**: [Jelszavak szinkronizálása a felügyelt tartományra csak felhőalapú felhasználói fiókok esetében](active-directory-ds-getting-started-password-sync.md)
> * **Helyszíni felhasználói fiókok**: [Jelszavak szinkronizálása a felügyelt tartományra a helyszíni AD-ből szinkronizált felhasználói fiókok esetében](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>5. feladat: jelszavak szinkronizálásának engedélyezése a felügyelt tartományra a csak felhőalapú felhasználói fiókok számára
A felhasználók a felügyelt tartományon való hitelesítéséhez az Active Directory Domain Servicesnek az NTLM- és Kerberos-hitelesítéshez megfelelő formátumú hitelesítőadat-kivonatokra van szükségük. Az Azure AD nem az NTLM- vagy Kerberos-hitelesítéshez szükséges formátumban hozza létre vagy tárolja a hitelesítési adatok kivonatait, amíg nem engedélyezi az Active Directory Domain Servicest a bérlő számára. Nyilvánvaló biztonsági okokból az Azure AD nem tiszta szöveges formátumban tárolja a jelszóalapú hitelesítő adatokat. Az Azure AD ezért nem tudja automatikusan létrehozni az NTLM vagy Kerberos hitelesítésiadat-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

> [!NOTE]
> Ha a szervezet rendelkezik csak felhőalapú felhasználói fiókokkal, az Active Directory Domain Servicest használó felhasználóknak módosítaniuk kell jelszavukat. A csak felhőalapú felhasználói fiókok olyan fiókok, amelyek az Azure AD-címtárban lettek létrehozva az Azure Portal vagy Azure AD PowerShell-parancsmagok használatával. Az ilyen felhasználói fiókok nem a helyszíni címtárból szinkronizálódnak.
>
>

A jelszómódosítási folyamat eredményeképp az Active Directory Domain Services által a Kerberos- és NTLM-hitelesítéshez igényelt hitelesítő adatok kivonatait az Azure AD szolgáltatásban kell létrehozni. Hagyhatja az Active Directory Domain Servicest használó összes felhasználó jelszavát elévülni, vagy utasíthatja őket, hogy módosítsák jelszavukat.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-user-account"></a>Kerberos és NTLM hitelesítőadat-kivonatok létrehozásának engedélyezése egy kizárólag felhőalapú felhasználói fiókon
Az alábbi utasításokat küldje el a végfelhasználóknak a jelszavuk módosításához:

1. Lépjen a szervezet [Azure AD hozzáférési panel](http://myapps.microsoft.com) oldalára.

    ![Az Azure AD Hozzáférési panel elindítása](./media/active-directory-domain-services-getting-started/access-panel.png)

2. A jobb felső sarokban kattintson a nevére, és válassza a **Profil** lehetőséget a menüből.

    ![Profil kiválasztása](./media/active-directory-domain-services-getting-started/select-profile.png)

3. A **Profil** lapon kattintson a **Jelszó módosítása** lehetőségre.

    ![Kattintson a „Jelszó módosítása” lehetőségre](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Ha a hozzáférési panel ablakában nem jelenik meg a **Jelszó módosítása** lehetőség, győződjön meg róla, hogy a szervezethez be van állítva a [jelszókezelés az Azure AD-ben](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. A **Jelszó módosítása** oldalon adja meg a meglévő (régi) jelszavát, adja meg az új jelszót, majd erősítse meg.

    ![Hozzon létre virtuális hálózatot az Azure AD tartományi szolgáltatásokhoz.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Kattintson a **Submit** (Küldés) gombra.

Néhány perccel azután, hogy módosította a jelszavát, az új jelszó használható lesz az Active Directory Domain Servicesben. Néhány további perc (átlagosan körülbelül 20 perc) múlva az új jelszavával bejelentkezhet a felügyelt tartományhoz kapcsolódó számítógépekre.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Saját jelszó frissítése](../active-directory/active-directory-passwords-update-your-own-password.md)
* [A jelszókezelés első lépései az Azure AD-ben](../active-directory/active-directory-passwords-getting-started.md)
* [Jelszó-szinkronizálás engedélyezése Active Directory Domain Servicesre szinkronizált Azure AD bérlő esetén](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Az Azure Active Directory tartományi szolgáltatások által felügyelt tartományok adminisztrációja](active-directory-ds-admin-guide-administer-domain.md)
* [Windows virtuális gép csatlakoztatása az Active Directory Domain Services által felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Red Hat Enterprise Linux virtuális gép csatlakoztatása az Active Directory Domain Services által felügyelt tartományhoz](active-directory-ds-admin-guide-join-rhel-linux-vm.md)


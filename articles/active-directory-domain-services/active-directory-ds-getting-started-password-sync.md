---
title: 'Azure Active Directory Domain Services: Jelszókivonat szinkronizálásának engedélyezése | Microsoft Docs'
description: Első lépések az Azure Active Directory tartományi szolgáltatások használatával
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: maheshu
ms.openlocfilehash: 0f7023b60ef3678c284fe05bff7be73674595512
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504151"
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Az Azure Active Directory Domain Servicesre történő jelszókivonat-szinkronizálás engedélyezése
Az előző feladatokban engedélyezte az Active Directory Domain Servicest az Azure Active Directory (Azure AD) bérlő számára. A következő feladat a jelszókivonatok szinkronizálásának engedélyezése, amely az Azure AD Domain Servicesszel való NT LAN Manager- (NTLM) és Kerberos-hitelesítéshez szükséges. A jelszókivonat-adatok szinkronizálásának beállítása után a felhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be a felügyelt tartományba.

A folyamat lépései eltérőek a csak felhőalapú felhasználói fiókok és a helyszíni könyvtárból az Azure AD Connect használatával szinkronizált felhasználói fiókok esetében. 

<br>
| **A felhasználói fiók típusa** | **A végrehajtandó lépések** |
| --- |---|
| **Az Azure AD-ben létrehozott felhőalapú felhasználói fiókok** |**&#x2713;** [Kövesse a cikkben megadott utasításokat](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Helyszíni címtárból szinkronizált felhasználói fiókok** |**&#x2713;** [Jelszókivonatok szinkronizálása a felügyelt tartományra a helyszíni AD-ből szinkronizált felhasználói fiókok esetében](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Lehetséges, hogy mindkét lépéssorozatot végre kell hajtania.**
> Ha az Azure AD-bérlő csak felhőalapú és a helyszíni AD-ből származó felhasználókkal is rendelkezik, mindkét lépéssorozatot végre kell hajtania.
>

## <a name="task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>5. feladat: a felügyelt tartományra történő jelszókivonat-szinkronizálás engedélyezése a csak felhőalapú felhasználói fiókok számára
A felhasználók felügyelt tartományon való hitelesítéséhez az Azure Active Directory Domain Servicesnek az NTLM- és Kerberos-hitelesítéshez megfelelő formátumú jelszókivonatokra van szüksége. Az Azure AD csak akkor hozza létre és tárolja a jelszókivonatokat az NTLM- vagy Kerberos-hitelesítéshez szükséges formátumban, ha engedélyezi az Azure Active Directory Domain Servicest a bérlő számára. Nyilvánvaló biztonsági okokból az Azure AD nem tiszta szöveges formátumban tárolja a jelszóalapú hitelesítő adatokat. Az Azure AD ezért nem tudja automatikusan létrehozni ezeket az NTLM- vagy Kerberos-jelszókivonatokat a felhasználók meglévő hitelesítő adatai alapján.

> [!NOTE]
> **Ha a szervezet rendelkezik csak felhőalapú felhasználói fiókokkal, az Active Directory Domain Servicest használó összes felhasználónak módosítania kell a jelszavát.** A csak felhőalapú felhasználói fiókok olyan fiókok, amelyek az Azure AD-címtárban lettek létrehozva az Azure Portal vagy Azure AD PowerShell-parancsmagok használatával. Az ilyen felhasználói fiókok nem a helyszíni címtárból szinkronizálódnak.
>
>

A jelszómódosítási folyamat eredményeképpen az Azure Active Directory Domain Services által a Kerberos- és NTLM-hitelesítéshez igényelt jelszókivonatokat az Azure AD-ben kell létrehozni. Hagyhatja az Active Directory Domain Servicest használó összes felhasználó jelszavát elévülni, vagy utasíthatja őket, hogy módosítsák jelszavukat.

### <a name="enable-ntlm-and-kerberos-password-hash-generation-for-a-cloud-only-user-account"></a>Kerberos- és NTLM-jelszókivonatok létrehozásának engedélyezése egy kizárólag felhőalapú felhasználói fiókon
Az alábbi utasításokat küldje el a végfelhasználóknak a jelszavuk módosításához:

1. Lépjen a szervezet [Azure AD hozzáférési panel](http://myapps.microsoft.com) oldalára.

    ![Az Azure AD Hozzáférési panel elindítása](./media/active-directory-domain-services-getting-started/access-panel.png)

2. A jobb felső sarokban kattintson a nevére, és válassza a **Profil** lehetőséget a menüből.

    ![Profil kiválasztása](./media/active-directory-domain-services-getting-started/select-profile.png)

3. A **Profil** lapon kattintson a **Jelszó módosítása** lehetőségre.

    ![Kattintson a „Jelszó módosítása” lehetőségre](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Ha a hozzáférési panel ablakában nem jelenik meg a **Jelszó módosítása** lehetőség, győződjön meg róla, hogy a szervezethez be van állítva a [jelszókezelés az Azure AD-ben](../active-directory/authentication/quickstart-sspr.md).
   >
   >
4. A **Jelszó módosítása** oldalon adja meg a meglévő (régi) jelszavát, adja meg az új jelszót, majd erősítse meg.

    ![Felhasználói jelszómódosítás](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Kattintson a **Submit** (Küldés) gombra.

Néhány perccel azután, hogy módosította a jelszavát, az új jelszó használható lesz az Active Directory Domain Servicesben. Körülbelül 20 perc múlva az új jelszavával bejelentkezhet a felügyelt tartományhoz kapcsolódó számítógépekre.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Saját jelszó frissítése](../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
* [A jelszókezelés első lépései az Azure AD-ben](../active-directory/authentication/quickstart-sspr.md)
* [Azure Active Directory Domain Servicesre történő jelszókivonat-szinkronizálás engedélyezése szinkronizált Azure AD-bérlő esetén](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Az Azure Active Directory tartományi szolgáltatások által felügyelt tartományok adminisztrációja](active-directory-ds-admin-guide-administer-domain.md)
* [Windows virtuális gép csatlakoztatása az Active Directory Domain Services által felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Red Hat Enterprise Linux virtuális gép csatlakoztatása az Active Directory Domain Services által felügyelt tartományhoz](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

---
title: "Azure AD tartományi szolgáltatások: Jelszavak szinkronizálásának engedélyezése | Microsoft Docs"
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
ms.date: 09/20/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c625fdf679c282bb62fdcbc864cd5b65d1014231


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Jelszavak szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatásokra
Az előző feladatokban engedélyezte az Azure AD tartományi szolgáltatásokat az Azure AD-bérlő számára. A következő feladat az NTLM és Kerberos hitelesítésiadat-kivonatok Azure AD tartományi szolgáltatásokkal való szinkronizálásának engedélyezése. Ha a bejelentkezési adatok szinkronizálása be van állítva, a felhasználók a vállalati hitelesítői adataikkal jelentkezhetnek be a felügyelt tartományra.

Az ehhez szükséges lépések eltérőek aszerint, hogy a szervezet csak felhőalapú Azure AD-címtárral rendelkezik-e, vagy be van állítva az Azure AD Connect használatával a helyszíni címtárral történő szinkronizálás.

<br>

> [!div class="op_single_selector"]
> * [Kizárólag felhőalapú Azure AD-bérlő](active-directory-ds-getting-started-password-sync.md)
> * [Szinkronizált Azure AD-bérlő](active-directory-ds-getting-started-password-sync-synced-tenant.md)
> 
> 

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloudonly-azure-ad-tenant"></a>5. feladat – Jelszavak szinkronizálásának engedélyezése az AAD tartományi szolgáltatásokra kizárólag felhőalapú Azure AD-címtáron
Az Azure AD tartományi szolgáltatásoknak az NTLM- és Kerberos-hitelesítéshez megfelelő formátumú hitelesítőadat-kivonatokra van szükségük a felhasználók felügyelt tartományon való hitelesítéséhez. Ha nem engedélyezi az AAD tartományi szolgáltatásokat a bérlő számára, akkor az Azure AD nem az NTLM- vagy Kerberos-hitelesítéshez szükséges formátumban hozza létre a hitelesítési adatok kivonatait. Nyilvánvaló biztonsági okokból az Azure AD nem tiszta szöveges formátumban tárolja a hitelesítő adatokat. Az Azure AD ezért nem tudja létrehozni az NTLM vagy Kerberos hitelesítésiadat-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

> [!NOTE]
> Ha a szervezet kizárólag felhőalapú Azure AD-bérlővel rendelkezik, az Azure AD tartományi szolgáltatásokat használó felhasználóknak módosítaniuk kell jelszavukat.
> 
> 

A jelszómódosítási folyamat eredményeképp az Azure AD tartományi szolgáltatások által a Kerberos- és NTLM-hitelesítéshez igényelt hitelesítő adatok kivonatait az Azure AD szolgáltatásban kell létrehozni. Hagyhatja az Azure AD tartományi szolgáltatásokat használó összes felhasználó jelszavát elévülni, vagy utasíthatja a felhasználókat, hogy módosítsák jelszavukat.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloudonly-azure-ad-tenant"></a>Kerberos és NTLM hitelesítőadat-kivonatok létrehozásának engedélyezése egy kizárólag felhőalapú Azure AD-bérlőn
Az alábbi utasításokat küldje el a felhasználóknak a jelszavuk módosításához:

1. Lépjen a szervezet Azure AD hozzáférési panel oldalára a következő címen: [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Válassza a **profil** lapot ezen az oldalon.
3. Kattintson a **Jelszó módosítása** csempére ezen az oldalon.
   
    ![Hozzon létre virtuális hálózatot az Azure AD tartományi szolgáltatásokhoz.](./media/active-directory-domain-services-getting-started/user-change-password.png)
   
   > [!NOTE]
   > Ha a hozzáférési panel oldalon nem jelenik meg a **Jelszó módosítása** lehetőség, akkor győződjön meg róla, hogy a szervezethez be van állítva a [jelszókezelés az Azure AD-ben](../active-directory/active-directory-passwords-getting-started.md).
   > 
   > 
4. A **Jelszó módosítása** oldalon adja meg a meglévő (régi) jelszavát, majd adja meg az új jelszót, és erősítse meg. Kattintson a **Submit** (Küldés) gombra.
   
    ![Hozzon létre virtuális hálózatot az Azure AD tartományi szolgáltatásokhoz.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Miután módosította a jelszavát, az új jelszó rövidesen használható lesz az Azure AD tartományi szolgáltatásokban. Néhány perc (átlagosan körülbelül 20 perc) múlva az új jelszavával bejelentkezhet a felügyelt tartományhoz kapcsolódó számítógépekre.

<br>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Saját jelszó frissítése](../active-directory/active-directory-passwords-update-your-own-password.md)
* [A jelszókezelés első lépései az Azure AD-ben](../active-directory/active-directory-passwords-getting-started.md).
* [Jelszó-szinkronizálás engedélyezése AAD tartományi szolgáltatásokra szinkronizált Azure AD bérlő esetén](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [Windows virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Red Hat Enterprise Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-rhel-linux-vm.md)




<!--HONumber=Nov16_HO2-->



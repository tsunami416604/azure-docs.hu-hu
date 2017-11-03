---
title: "Az Office 365-bérlő használata Azure-előfizetés |} Microsoft Docs"
description: "Ismerje meg az Office 365-címtár (bérlői) felvétele az Azure-előfizetésre."
services: 
documentationcenter: 
author: JiangChen79
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: cjiang
ms.openlocfilehash: e6300932d044ec9a4f88eb5bd5977220ed11d513
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="link-an-office-365-tenant-to-an-azure-subscription"></a>Az Office 365-bérlő összekapcsolása egy Azure-előfizetés
Csatolja az önálló Azure és az Office 365-előfizetések, hogy az Azure-előfizetéssel az Office 365-bérlő érhető el. Az előfizetések csatolásához jelentkezzen be az Azure-bA az Azure szolgáltatás-rendszergazdai fiók, adja hozzá a könyvtárát, és adjon hozzá az Office 365 munkahelyi vagy iskolai fiókja az Azure Active Directory-bérlő.

**Helyezze át a meglévő Azure-előfizetése az Office 365 munkahelyi vagy iskolai fiókkal szeretné?** Ha regisztrált az Azure személyes Microsoft-fiókkal, és azt, vagy jelentkezzen be Office 365-fiókjával szeretne használni, erősen ajánlott az előfizetés átvitele. Lásd: [átvitele Azure előfizetés tulajdonjogának másik fiókra](billing-subscription-transfer.md). 

**Regisztráció az Azure, az Office 365-tel szeretné?** Lásd: [regisztráció Office 365-fiókkal az Azure](billing-use-existing-office-365-account-azure-subscription.md). 

## <a name="before-you-begin"></a>Előkészületek
* Az Azure-előfizetés szolgáltatás-rendszergazda hitelesítő adatait kell rendelkeznie. Közös rendszergazdai fiókok nem hajtható végre néhány cikkben leírt lépéseket. A szolgáltatás-rendszergazda módosításához lásd [hozzáadása vagy módosítása az Azure-rendszergazdai szerepkörök](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription).
* Az Office 365-bérlő globális rendszergazdájának hitelesítő adatait kell rendelkeznie.
* A szolgáltatás-rendszergazda e-mail címe nem lehet az Office 365-bérlő.
* A szolgáltatás-rendszergazda e-mail címe nem egyezik, az Office 365-bérlő globális rendszergazdái.
* Ha egy e-mail címet, amely a Microsoft-fiók és a szervezeti fiók használatához, átmenetileg váltson egy másik Microsoft-fiók használata az Azure-előfizetéshez a szolgáltatás rendszergazdájával. A Microsoft-fiókjával a hozhat létre a [Microsoft-fiók bejelentkezési oldalának](https://signup.live.com/).

## <a name="link-office-365-tenant-to-azure-subscription"></a>Office 365-bérlő hivatkozás Azure-előfizetéshez
Rendelje hozzá a az Office 365-bérlő az Azure-előfizetéshez, kövesse az alábbi lépéseket:

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>1. lépés: Az Office 365-bérlő hozzáadása az Azure-előfizetéshez

1. Jelentkezzen be a [a klasszikus Azure portálon](https://manage.windowsazure.com/) a szolgáltatás rendszergazdai hitelesítő adataival.

    ![Képernyőfelvétel az Azure-bejelentkezés](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
2. A bal oldali panelen válassza ki a **ACTIVE DIRECTORY**. Ne tekintse meg az Office 365-bérlő. Ha azt látja, folytassa a [2. lépés: módosítsa a könyvtárat, az Azure-előfizetéshez társított](#Step2).
   
   ![Képernyőkép az Active Directory-bejegyzés](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
3. Válassza ki **új** > **DIRECTORY** > **egyéni létrehozás**.
   
    ![Képernyőfelvétel az Azure Active Directory egyéni létrehozása](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
4. Az a **Hozzáadás directory** lap **DIRECTORY**, jelölje be **meglévő címtár használata**. Válassza ki **készen állok a kijelentkezésre**, és válassza ki **Complete** ![befejezéséhez ikon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Képernyőkép a "Meglévő könyvtár használata"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
5. Aláírása után jelentkezzen be az Office 365-bérlő globális rendszergazdájának hitelesítő adatait.
   
    ![Képernyőkép az Office 365 globális rendszergazdai bejelentkezés](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
6. Válassza ki **továbbra is**.
   
    ![Képernyőkép a ellenőrzése](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
7. Válassza ki **azonnali Kijelentkezés**.
   
    ![Képernyőkép a kijelentkezési](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
8. Jelentkezzen be a [a klasszikus Azure portálon](https://manage.windowsazure.com/) a szolgáltatás rendszergazdai hitelesítő adataival.
   
    ![Képernyőfelvétel az Azure-bejelentkezés](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
9. Meg kell jelennie az Office 365-bérlő az irányítópulton.
   
    ![Irányítópultjának képernyőképe](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>2. lépés: Módosítsa a könyvtárat, az Azure-előfizetéshez társított
   
1. Válassza ki **beállítások**.
   
    ![Képernyőfelvétel az Azure klasszikus portál beállítások ikon](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
2. Az Azure-előfizetéshez, majd válassza ki és **könyvtár szerkesztése**.

    ![Képernyőfelvétel az Azure-előfizetés Szerkesztés könyvtára](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
3. Válassza ki **következő** ![tovább ikon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Képernyőkép a "Módosítása a társított könyvtár"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
4. Tekintse át az érintett fiókokat. Minden társrendszergazdák és [szerepköralapú hozzáférés-vezérlést (RBAC)](../active-directory/role-based-access-control-configure.md) a meglévő erőforrás-csoportok a hozzárendelt hozzáféréssel rendelkező felhasználók törlődnek. A figyelmeztetést kap csak akkor említi, társrendszergazdák eltávolítása.
      
    ![Képernyőkép a a közös rendszergazdai fiókok, el kell távolítani.](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![Képernyőkép a egy példa-felhasználói fiókot el kell távolítani.](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
5. Válassza ki **Complete** ![befejezéséhez ikon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

### <a name="step-3-add-your-office-365-organizational-accounts-as-admins-to-the-azure-subscription"></a>3. lépés: Adja hozzá az Office 365 munkahelyi és iskolai fiókok rendszergazdák az Azure-előfizetéshez
   
Egy rendszergazda hozzáadása az Azure-előfizetéssel, lásd: [hozzáadása vagy módosítása, hogy az előfizetés vagy a szolgáltatások kezelése az Azure rendszergazdai szerepkörök](billing-add-change-azure-subscription-administrator.md)

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.


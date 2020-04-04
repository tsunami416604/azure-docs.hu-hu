---
title: SharePoint felhasználói profil szolgáltatás engedélyezése az Azure AD DS szolgáltatással | Microsoft dokumentumok
description: Megtudhatja, hogy miként konfigurálhat azure Active Directory tartományi szolgáltatásokat felügyelt tartomány a SharePoint Server profilszinkronizálásának támogatására
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: a684a669c491e35b5c6b62dd318b4fe61edeb52b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655381"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Az Azure Active Directory tartományi szolgáltatások konfigurálása a SharePoint Server felhasználói profilszinkronizálásának támogatására

A SharePoint Server tartalmaz egy szolgáltatást a felhasználói profilok szinkronizálásához. Ez a funkció lehetővé teszi a felhasználói profilok központi helyen való tárolását, és több SharePoint-webhelyen és -farmon is elérhető. A SharePoint Server felhasználói profilszolgáltatás konfigurálásához a megfelelő engedélyeket meg kell adni egy Azure Active Directory tartományi szolgáltatások (Azure AD DS) felügyelt tartományban. További információt a [Felhasználói profil szinkronizálása a SharePoint Server ben című témakörben talál.](https://technet.microsoft.com/library/hh296982.aspx)

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure AD DS-t a SharePoint Server felhasználói profil szinkronizálási szolgáltatásának engedélyezéséhez.

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén töltse ki az oktatóanyagot [az Azure Active Directory tartományi szolgáltatások példányának létrehozásához és konfigurálásához.][create-azure-ad-ds-instance]
* Az Azure AD DS felügyelt tartományához csatlakozott Windows Server felügyeleti virtuális gép.
    * Szükség esetén fejezze be az [oktatóanyagot egy felügyeleti virtuális gép létrehozásához.][tutorial-create-management-vm]
* Egy felhasználói fiók, amely az *Azure AD DC rendszergazdák* csoportjának tagja az Azure AD-bérlőben.
* SharePoint-szolgáltatásfiók a felhasználói profil szinkronizálási szolgáltatásához.
    * Szükség esetén olvassa el [a SharePoint Server felügyeleti és szolgáltatásfiókjainak megtervezése témakört.][sharepoint-service-account]

## <a name="service-accounts-overview"></a>Szolgáltatásfiókok – áttekintés

Egy Azure AD DS felügyelt tartományban egy **AAD DC szolgáltatásfiókok** nevű biztonsági csoport létezik a *felhasználók* szervezeti egység (OU) részeként. A biztonsági csoport tagjai a következő jogosultságokat delegálják:

- **Címtármódosítási jogosultság replikálása** a gyökér DSE-n.
- **Címtármódosítási jogosultság replikálása** a *konfigurációs* névhasználati környezetben (tároló).`cn=configuration`

Az **AAD DC szolgáltatásfiókok** biztonsági csoport a Windows **2000 előtti windows 2000-kompatibilis hozzáférés**beépített csoportjának is tagja.

Ha hozzáadja ezt a biztonsági csoportot, a SharePoint Server felhasználói profil szinkronizálási szolgáltatásának szolgáltatásfiókja megkapja a megfelelő munkához szükséges jogosultságokat.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>A SharePoint Server felhasználói profil szinkronizálásának támogatásának engedélyezése

A SharePoint Server szolgáltatásfiókjának megfelelő jogosultságokkal kell rendelkeznie a módosítások könyvtárba történő replikálásához és a SharePoint Server felhasználói profil szinkronizálásának megfelelő működéséhez. Ezen jogosultságok biztosításához adja hozzá a SharePoint felhasználói profil szinkronizálásához használt szolgáltatásfiókot az **AAD DC szolgáltatásfiókok** csoporthoz.

Az Azure AD DS felügyeleti virtuális gép, hajtsa végre az alábbi lépéseket:

> [!NOTE]
> Az Azure AD DS felügyelt tartományban lévő csoporttagság szerkesztéséhez be kell jelentkeznie egy olyan felhasználói fiókba, amely az *AAD DC rendszergazdák* csoport tagja.

1. A kezdőképernyőn válassza a **Felügyeleti eszközök lehetőséget.** Megjelenik az oktatóanyagban telepített elérhető felügyeleti eszközök listája a [felügyeleti virtuális gép létrehozásához.][tutorial-create-management-vm]
1. A csoporttagság kezeléséhez válassza az **Active Directory felügyeleti központ** elemet a felügyeleti eszközök listájából.
1. A bal oldali ablaktáblában válassza ki az Azure AD DS felügyelt tartományát, például *a aaddscontoso.com.* Megjelenik a meglévő o-k és erőforrások listája.
1. Válassza a **Felhasználók** szervezeti egységet, majd válassza az *AAD DC szolgáltatásfiókok* biztonsági csoportot.
1. Válassza a **Tagok**lehetőséget, majd válassza **a Hozzáadás...** lehetőséget.
1. Írja be a SharePoint szolgáltatásfiók nevét, majd kattintson az **OK gombra.** A következő példában a SharePoint szolgáltatásfiók neve *spadmin*:

    ![A SharePoint szolgáltatásfiók hozzáadása az AAD tartományvezérlő szolgáltatásfiókok biztonsági csoporthoz](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>További lépések

További információ: [Grant Active Directory Tartományi szolgáltatások engedélyei a SharePoint Server profilszinkronizálásához](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts

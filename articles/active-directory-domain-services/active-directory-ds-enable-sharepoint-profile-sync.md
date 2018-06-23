---
title: 'Az Azure Active Directory tartományi szolgáltatások: A SharePoint felhasználói profil szolgáltatás támogatásának engedélyezése |} Microsoft Docs'
description: Azure Active Directory tartományi szolgáltatások felügyelt tartományok profilszinkronizálás támogatja a SharePoint-kiszolgáló konfigurálása
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 9e21547f6e1088677bb5699d17d81d170b4cab3d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332133"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Egy felügyelt tartomány profilszinkronizálás támogatja a SharePoint-kiszolgáló konfigurálása
A SharePoint Server tartalmaz egy felhasználói profil szolgáltatást, a felhasználói profil szinkronizáláshoz használt. Állítsa be a felhasználói profil szolgáltatás, megfelelő engedélyekkel kell egy Active Directory-tartományhoz kell biztosítani. További információkért lásd: [Active Directory tartományi szolgáltatások engedélyeket kap a SharePoint Server 2013 profilszinkronizálás](https://technet.microsoft.com/library/hh296982.aspx).

Ez a cikk azt ismerteti, hogyan konfigurálhatja a felügyelt tartományok Azure AD tartományi szolgáltatásokat a SharePoint Server felhasználóiprofil-szinkronizálás szolgáltatást.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>A "AAD DC-szolgáltatásfiókok" csoport
A biztonsági csoport neve "**AAD DC szolgáltatásfiókok**" érhető el a "Felhasználók" szervezeti egységet a felügyelt tartományon belül. Ez a csoport megjelenik a **Active Directory – felhasználók és számítógépek** MMC beépülő modul felügyelt tartományra.

![Az AAD DC szolgáltatásfiókok biztonsági csoport](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

A biztonsági csoport tagjai delegált a következő engedélyekkel:
- A legfelső szintű a felügyelt tartomány DSE "Directory változások replikálása" jogosultság.
- A konfigurációs névhasználati környezetében a "Directory változások replikálása" jogosultság (cn = konfigurációtároló) a felügyelt tartomány.

Ez a biztonsági csoport tagja is a beépített csoport **Windows 2000 előtti rendszerrel kompatibilis hozzáférés**.

![Az AAD DC szolgáltatásfiókok biztonsági csoport](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>A felügyelt tartományok támogatásához a SharePoint Server felhasználóiprofil-szinkronizálás engedélyezése
A SharePoint felhasználói profil szinkronizálás használt fiókkal is hozzáadhat a **AAD DC szolgáltatásfiókok** csoport. Ennek eredményeképpen a szinkronizálási fiók lekérdezi a megfelelő jogosultságokkal replikálni a módosításokat a könyvtárba. Ez a konfigurációs lépés lehetővé teszi, hogy a SharePoint Server felhasználóiprofil-szinkronizálás megfelelő működéséhez.

![AAD DC szolgáltatásfiókokat - tagok hozzáadása](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![AAD DC szolgáltatásfiókokat - tagok hozzáadása](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Kapcsolódó tartalom
* [Technikai útmutató - támogatás Active Directory tartományi szolgáltatások engedélyeit a SharePoint Server 2013 profilszinkronizálás](https://technet.microsoft.com/library/hh296982.aspx)

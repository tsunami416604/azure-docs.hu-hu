---
title: 'Azure Active Directory Domain Services: A SharePoint felhasználói profil szolgáltatás engedélyezése | Microsoft Docs'
description: Azure Active Directory Domain Services felügyelt tartományok konfigurálása a SharePoint Server-profilok szinkronizálásának támogatásához
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4a9ee05b37a69927d70269dccef2b74a2c251722
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234106"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Felügyelt tartomány konfigurálása a SharePoint Server-profilok szinkronizálásának támogatásához
A SharePoint-kiszolgáló tartalmazza a felhasználói profil szinkronizálásához használt felhasználói profil szolgáltatást. A felhasználói profil szolgáltatás beállításához meg kell adni a megfelelő engedélyeket egy Active Directory tartományban. További információ: [Active Directory tartományi szolgáltatások engedélyek megadása a SharePoint Server 2013-beli profilok szinkronizálásához](https://technet.microsoft.com/library/hh296982.aspx).

Ez a cikk azt ismerteti, hogyan konfigurálhatja Azure AD Domain Services felügyelt tartományokat a SharePoint Server felhasználói profil szinkronizálási szolgáltatásának telepítéséhez.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>Az "HRE DC Service accounts" csoport
Az "**HRE DC Service accounts**" nevű biztonsági csoport a felügyelt tartomány felhasználó szervezeti egységén belül érhető el. Ezt a csoportot a felügyelt tartomány **Active Directory felhasználók és számítógépek** MMC beépülő moduljában tekintheti meg.

![HRE DC szolgáltatásfiókok biztonsági csoport](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

A biztonsági csoport tagjai a következő jogosultságokat delegálják:
- A "címtárbeli módosítások replikálása" jogosultság a felügyelt tartomány gyökérszintű DSE.
- A "címtárbeli módosítások replikálása" jogosultság a felügyelt tartomány konfigurációs névhasználati környezetében (CN = Configuration tároló).

Ez a biztonsági csoport szintén tagja a beépített **Windows 2000-kompatibilis hozzáférésnek**.

![HRE DC szolgáltatásfiókok biztonsági csoport](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>A felügyelt tartomány engedélyezése a SharePoint Server felhasználói profilok szinkronizálásának támogatásához
A SharePoint felhasználói profilok szinkronizálásához használt szolgáltatásfiókot a **HRE DC szolgáltatásfiók** csoportba veheti fel. Ennek eredményeképpen a szinkronizációs fiók megfelelő jogosultságokkal rendelkezik a módosítások replikálásához a címtárban. Ez a konfigurációs lépés lehetővé teszi, hogy a SharePoint Server felhasználói profiljának szinkronizálása megfelelően működjön.

![HRE DC szolgáltatásfiókok – Tagok hozzáadása](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![HRE DC szolgáltatásfiókok – Tagok hozzáadása](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Kapcsolódó tartalom
* [Technikai útmutató – Active Directory tartományi szolgáltatások engedélyek megadása a SharePoint Server 2013-beli profilok szinkronizálásához](https://technet.microsoft.com/library/hh296982.aspx)

---
title: 'Az Azure Active Directory Domain Services: A SharePoint felhasználói profil szolgáltatás támogatásának engedélyezése |} A Microsoft Docs'
description: Profilszinkronizálás támogatásának a SharePoint Server Azure Active Directory Domain Services a felügyelt tartományok konfigurálása
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
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: c25fca2f3645a0397a999cec7552de15f20fb6be
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502075"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Profilszinkronizálás támogatásának a SharePoint Server felügyelt tartomány konfigurálása
A SharePoint Server tartalmaz egy felhasználói profil szolgáltatás, amely a felhasználói profil szinkronizáláshoz szolgál. Állítsa be a felhasználói profil szolgáltatás, a megfelelő engedélyekkel kell rendelkeznie az Active Directory-tartományban. További információkért lásd: [profilszinkronizáláshoz a SharePoint Server 2013-ban az Active Directory Domain Services engedélyeket](https://technet.microsoft.com/library/hh296982.aspx).

Ez a cikk bemutatja, hogyan konfigurálhatja a felügyelt tartományok az Azure AD tartományi szolgáltatásokat a SharePoint Server felhasználói profil Sync szolgáltatás üzembe helyezéséhez.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>Az "AAD DC-szolgáltatásfiókok" csoport
A biztonsági csoport neve "**AAD DC szolgáltatásfiókok**" érhető el a "Felhasználók" szervezeti egységet a felügyelt tartományon belül. Ez a csoport megjelenik a **Active Directory – felhasználók és számítógépek** MMC beépülő modul a felügyelt tartományon.

![AAD DC szolgáltatásfiókok biztonsági csoport](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

A biztonsági csoport tagjai delegált a következő jogosultságokat:
- A gyökér-DSE a felügyelt tartomány "Címtárváltozások replikálása" jogosultsággal.
- A konfigurációs névhasználati környezet "Címtárváltozások replikálása" jogosultsággal (cn = configuration tároló) a felügyelt tartomány.

Ez a biztonsági csoport tagja is a beépített csoport **Windows 2000 előtti rendszerrel kompatibilis hozzáférés**.

![AAD DC szolgáltatásfiókok biztonsági csoport](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>A felügyelt tartomány támogatása a SharePoint-kiszolgáló a felhasználóiprofil-szinkronizálás engedélyezése
A SharePoint felhasználói profilhoz történő szinkronizáláshoz használt szolgáltatási fiók is hozzáadhat a **AAD DC szolgáltatásfiókok** csoport. Ennek eredményeképpen a szinkronizálási fióknak beolvassa a módosításokat replikálja arra a könyvtárra, megfelelő jogosultságokkal. Ez a konfigurációs lépés lehetővé teszi, hogy a SharePoint-kiszolgáló a felhasználóiprofil-szinkronizálás megfelelő működéséhez.

![Szolgáltatásfiókok AAD DC - tagok hozzáadása](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Szolgáltatásfiókok AAD DC - tagok hozzáadása](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Kapcsolódó tartalom
* [Technikai referencia - támogatás az Active Directory Domain Services engedélyek a SharePoint Server 2013-ban](https://technet.microsoft.com/library/hh296982.aspx)

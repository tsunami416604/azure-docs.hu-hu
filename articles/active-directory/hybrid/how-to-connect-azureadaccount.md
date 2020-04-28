---
title: Az Azure AD Connector-fiók jelszavának módosítása | Microsoft Docs
description: Ez a témakör az Azure AD Connector-fiók visszaállítására szolgáló dokumentumot ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67204530"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Az Azure AD Connector-fiók jelszavának módosítása
Az Azure AD Connector-fióknak ingyenes szolgáltatásnak kellene lennie. Ha alaphelyzetbe kell állítania a hitelesítő adatait, akkor ez a témakör Önnek szól. Ha például egy globális rendszergazda véletlenül alaphelyzetbe állítja a fiók jelszavát a PowerShell használatával.

## <a name="reset-the-credentials"></a>Hitelesítő adatok alaphelyzetbe állítása
Ha az Azure AD Connector-fiók hitelesítési problémák miatt nem tud kapcsolatba lépni az Azure AD-vel, akkor a jelszó alaphelyzetbe állítható.

1. Jelentkezzen be a Azure AD Connect Sync-kiszolgálóra, és indítsa el a PowerShellt.
2. Futtassa az `Add-ADSyncAADServiceAccount` parancsot.  
   ![PowerShell-parancsmag addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Adja meg az Azure AD globális rendszergazdai hitelesítő adatait.

Ez a parancsmag alaphelyzetbe állítja a szolgáltatásfiók jelszavát, és frissíti azt az Azure AD-ben és a Szinkronizáló motorban is.

## <a name="known-issues-these-steps-can-solve"></a>A lépések által ismert problémák elhárítása
Ez a szakasz azoknak a hibáknak a listáját tartalmazza, amelyeket az Azure AD Connector-fiókban a hitelesítő adatok alaphelyzetbe állításával rögzítettek.

---
6900-es esemény  
A kiszolgáló váratlan hibát észlelt a jelszó-módosítási értesítés feldolgozása során:  
AADSTS70002: hiba történt a hitelesítő adatok érvényesítése során. AADSTS50054: a rendszer a régi jelszót használja a hitelesítéshez.

---
659-es esemény  
Hiba történt a jelszóházirend-szinkronizálás konfigurációjának beolvasása közben. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: hiba történt a hitelesítő adatok érvényesítése során. AADSTS50054: a rendszer a régi jelszót használja a hitelesítéshez.

## <a name="next-steps"></a>További lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)


---
title: Az Azure AD Connector-fiók jelszavának módosítása | Microsoft dokumentumok
description: Ez a témakör az Azure AD Connector-fiók visszaállítását ismerteti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204530"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Az Azure AD Connector-fiók jelszavának módosítása
Az Azure AD-összekötő fiók állítólag szolgáltatásmentes. Ha alaphelyzetbe kell állítania a hitelesítő adatait, akkor ez a témakör az Ön számára. Ha például egy globális rendszergazda véletlenül alaphelyzetbe állította a jelszót a fiókpowershell használatával.

## <a name="reset-the-credentials"></a>A hitelesítő adatok alaphelyzetbe állítása
Ha az Azure AD-összekötő fiók hitelesítési problémák miatt nem tud kapcsolatba lépni az Azure AD-vel, a jelszó alaphelyzetbe állítható.

1. Jelentkezzen be az Azure AD Connect szinkronizálási kiszolgálójára, és indítsa el a PowerShellt.
2. Futtassa az `Add-ADSyncAADServiceAccount` parancsot.  
   ![PowerShell-parancsmag addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Adja meg az Azure AD globális rendszergazdai hitelesítő adatait.

Ez a parancsmag alaphelyzetbe állítja a szolgáltatásfiók jelszavát, és frissíti mind az Azure AD-ben, mind a szinkronizálási motorban.

## <a name="known-issues-these-steps-can-solve"></a>Az ezek a lépések által megoldható ismert problémák
Ez a szakasz az ügyfelek által jelentett hibák listáját tartalmazza, amelyeket az Azure AD Connector-fiók alaphelyzetbe állított hitelesítő adatokkal javítottak.

---
6900- es esemény  
A kiszolgáló váratlan hibát észlelt a jelszómódosítási értesítés feldolgozása közben:  
AADSTS70002: Hiba a hitelesítő adatok érvényesítése. AADSTS50054: A hitelesítéshez régi jelszót használ.

---
Esemény 659  
Hiba történt a jelszóházirend szinkronizálási konfigurációjának beolvasása közben. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Hiba a hitelesítő adatok érvényesítése. AADSTS50054: A hitelesítéshez régi jelszót használ.

## <a name="next-steps"></a>További lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)


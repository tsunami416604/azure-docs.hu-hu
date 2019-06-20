---
title: Az Azure AD-összekötő fiókhoz tartozó jelszó módosítása |} A Microsoft Docs
description: Ez a témakör az Azure AD-összekötő fiók visszaállítása dokumentumok.
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
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204530"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Az Azure AD Connector-fiók jelszavának módosítása
Az Azure AD-összekötő fiók kellene lennie ingyenes szolgáltatás. Ha a hitelesítő adatok alaphelyzetbe kell, majd ez a témakör Önnek szól. Például egy globális rendszergazdai szerint hibásan van visszaállíthatja a jelszót a fiók PowerShell-lel.

## <a name="reset-the-credentials"></a>A hitelesítő adatok alaphelyzetbe állítása
Az Azure AD-összekötő fiók nem tud kapcsolatba lépni az Azure AD hitelesítési problémák miatt merülnek fel, ha a jelszó állítható vissza.

1. Jelentkezzen be az Azure AD Connect szinkronizálási kiszolgáló, és indítsa el a Powershellt.
2. Futtassa az `Add-ADSyncAADServiceAccount` parancsot.  
   ![PowerShell-parancsmag addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Adja meg az Azure AD globális rendszergazdai hitelesítő adatait.

Ez a parancsmag alaphelyzetbe állítja a jelszót a fiókhoz, és frissítse az Azure ad-ben és a szinkronizálási motor is.

## <a name="known-issues-these-steps-can-solve"></a>Ismert problémákat oldhat meg ezeket a lépéseket
Ez a szakasz az ügyfelek által a hitelesítő adatok alaphelyzetbe állítása, az Azure AD-összekötő fiók javított által jelentett hibák listája.

---
Esemény 6900  
A kiszolgáló váratlan hibába ütközött a jelszóváltoztatási értesítés feldolgozása közben:  
AADSTS70002: Hiba történt a hitelesítő érvényesítésekor. AADSTS50054: Régi jelszót használt a hitelesítéshez.

---
Esemény 659  
Hiba történt a jelszó szinkronizálása szabályzatkonfiguráció beolvasása. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Hiba történt a hitelesítő érvényesítésekor. AADSTS50054: Régi jelszót használt a hitelesítéshez.

## <a name="next-steps"></a>További lépések
**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)


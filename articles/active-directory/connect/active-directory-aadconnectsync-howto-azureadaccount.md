---
title: "Azure AD Connect szinkronizálása: az Azure AD-szolgáltatásfiók kezelése |} Microsoft Docs"
description: "Ez a témakör az Azure AD-szolgáltatásfiók visszaállítása dokumentumokat."
services: active-directory
keywords: "AADSTS70002, AADSTS50054, az Azure AD Connect szinkronizálási szolgáltatás Connector-szolgáltatásfióknak a jelszó alaphelyzetbe állítása"
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 8e9e8192ee4fcb636b5be91d2616acbc9120c8c0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Azure AD Connect szinkronizálása: kezelése az Azure AD-szolgáltatásfiók
Az Azure AD-összekötő által használt szolgáltatásfiók kellene lennie ingyenes szolgáltatás. Ha a hitelesítő adatok alaphelyzetbe kell, akkor ez a témakör értéke meg. Például ha egy globális rendszergazda által hibát állítsa vissza a PowerShell használatával a szolgáltatásfiók jelszavát.

## <a name="reset-the-credentials"></a>A hitelesítő adatok alaphelyzetbe állítása
Ha a fiók az Azure AD Connectoron definiálva az Azure AD hitelesítési problémák miatt nem tud kapcsolatba lépni, a jelszó állítható vissza.

1. Jelentkezzen be az Azure AD Connect szinkronizálási kiszolgálót, majd indítsa el a Powershellt.
2. Futtassa az `Add-ADSyncAADServiceAccount` parancsot.  
   ![PowerShell-parancsmag addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Adja meg Azure AD globális rendszergazda hitelesítő adatait.

Ez a parancsmag állítja vissza a szolgáltatásfiók jelszavát, és frissítse az Azure ad-ben és a szinkronizálási motor.

## <a name="known-issues-these-steps-can-solve"></a>A lépések segítségével megoldható ismert problémák
Ez a szakasz a hitelesítő adatok alaphelyzetbe állítása a következőn az Azure AD-szolgáltatásfiók által kijavított ügyfelei által jelentett hiba.

- - -
Esemény 6900  
A kiszolgáló váratlan hibába ütközött a jelszó-módosítási értesítés feldolgozása során:  
AADSTS70002: Hiba a érvényesítéséhez hitelesítő adatokat. AADSTS50054: Régi jelszó-hitelesítéshez használt.

- - -
Esemény 659  
Hiba történt a jelszó szinkronizálása konfigurálása beolvasása. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Hiba a érvényesítéséhez hitelesítő adatokat. AADSTS50054: Régi jelszó-hitelesítéshez használt.

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)


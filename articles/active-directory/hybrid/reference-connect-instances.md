---
title: 'Azure AD Connect: Szolgáltatáspéldány szinkronizálása |} A Microsoft Docs'
description: Ez az oldal az Azure AD-példányban vonatkozó különleges szempontok dokumentumok.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298819"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Példányokra vonatkozó különleges szempontok
Az Azure ad-ben világszerte példányát a leggyakrabban használt a az Azure AD Connect és az Office 365. De is vannak további példányok, és ezeknek eltérő követelmények URL-címek és más speciális szempontokat kell.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
A [Microsoft Cloud németországi adatközpontjában](https://www.microsoft.de/cloud-deutschland) egy német megbízott adatkezelő által működtetett szuverén felhő.

| Nyissa meg a proxykiszolgáló URL-címmel |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Tanúsítvány-visszavonási listákat |

Amikor bejelentkezik az Azure AD-bérlővel, bejelentkezéskor az onmicrosoft.de tartományba olyan fiókot kell használnia.

Jelenleg nem szerepel a Microsoft Cloud németországi adatközpontjában a funkciók:

* **A jelszóvisszaíró** előzetes verziójának, az Azure AD Connect verziója 1.1.570.0 és után.
* Más Azure AD Premium szolgáltatásokhoz nem érhető el.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
A [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/) Egyesült Államokbeli kormányzati felhő.

Ehhez a felhőhöz a DirSync korábbi verzióiban támogatott. A build 1.1.180 az Azure AD Connect a felhőt következő generációja használata támogatott. Ezt a létrehozást csak Egyesült Államokbeli alapján végpontok használja, és van egy másik, nyissa meg a proxykiszolgáló URL-címek listáját.

| Nyissa meg a proxykiszolgáló URL-címmel |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (az Azure Government-bérlő automatikus észleléséhez szükséges) |
| \*.gov.us.microsoftonline.com |
| + Tanúsítvány-visszavonási listákat |

> [!NOTE]
> Az Azure AD Connect verziója 1.1.647.0, kezdődően AzureInstance értékre állítja a beállításjegyzékben már nem szükséges, feltéve, hogy *. windows.net meg nyitva a proxy-kiszolgálón. Azon ügyfeleink esetében, amely engedélyezi az Azure AD Connect kiszolgálók internetkapcsolattal, azonban a következő manuális konfiguráció használható.

### <a name="manual-configuration"></a>Kézi konfigurálás

A következő manuális konfigurációs lépés végrehajtásával segítségével biztosítható, hogy az Azure AD Connect szinkronizálási végpontok az Azure Government használja.

1. Az Azure AD Connect telepítés elindításához.
2. Amikor megjelenik az első oldal, ahol meg kell menteniük a végfelhasználói licencszerződés elfogadásához, nem folytatható, de hagyja meg a telepítési varázsló futtatása.
3. Indítsa el a regedit, és módosítsa a beállításkulcsot `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` értékre `4`.
4. Lépjen vissza az Azure AD Connect telepítővarázsló, fogadja el a végfelhasználói LICENCSZERZŐDÉST, és továbbra is. A telepítés során ügyeljen arra, hogy használja a **egyéni konfigurációs** elérési útja (és nem Expressz telepítés), majd folytassa a telepítést a szokásos módon.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

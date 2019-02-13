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
ms.date: 06/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca481d50efb99d6e36c66388192e9f27cd66bf45
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217190"
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

## <a name="microsoft-azure-government-cloud"></a>A Microsoft Azure Government-felhőben
A [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/) Egyesült Államokbeli kormányzati felhő.

Ehhez a felhőhöz a DirSync korábbi verzióiban támogatott. A build 1.1.180 az Azure AD Connect a felhőt következő generációja használata támogatott. Ezt a létrehozást csak Egyesült Államokbeli alapján végpontok használja, és van egy másik, nyissa meg a proxykiszolgáló URL-címek listáját.

| Nyissa meg a proxykiszolgáló URL-címmel |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (automatikus szükséges Azure ad-ben kormányzati bérlő észlelés) |
| \*.gov.us.microsoftonline.com |
| + Tanúsítvány-visszavonási listákat |

> [!NOTE]
> Az AAD Connect verzió 1.1.647.0, kezdődően AzureInstance értékre állítja a beállításjegyzékben már nem szükséges, feltéve, hogy *. windows.net meg nyitva a proxy-kiszolgálón.

Jelenleg nem szerepel a Microsoft Azure Government cloud szolgáltatások:

* **A jelszóvisszaíró** előzetes verziójának, az Azure AD Connect verziója 1.1.570.0 és után.
* Más Azure AD Premium szolgáltatásokhoz nem érhető el.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

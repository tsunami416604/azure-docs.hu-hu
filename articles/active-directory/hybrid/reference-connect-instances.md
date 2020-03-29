---
title: 'Azure AD Connect: Szolgáltatáspéldányok szinkronizálása | Microsoft dokumentumok'
description: Ez a lap az Azure AD-példányokkal kapcsolatos speciális szempontokat dokumentálja.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66298819"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: A példányok speciális szempontjai
Az Azure AD Connect leggyakrabban az Azure AD és az Office 365 világméretű példányával használatos. De vannak más esetek is, és ezek különböző követelményeket támasztanak az URL-ek és más speciális megfontolások esetében.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
A [Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) egy német megbízott által működtetett szuverén felhő.

| A proxykiszolgálón megnyitandó URL-címek |
| --- |
| \*microsoftonline.de |
| \*.windows.net |
| +Visszavont tanúsítványok listája |

Amikor bejelentkezik az Azure AD-bérlő, egy fiókot kell használnia a onmicrosoft.de tartományban.

A Microsoft Cloud Germany jelenleg nem található szolgáltatásai:

* **A jelszó-visszaírás** előzetes verziójú az Azure AD Connect 1.1.570.0-s verziójával és azt követően érhető el.
* Más Azure AD Premium-szolgáltatások nem érhetők el.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
A [Microsoft Azure Government felhő](https://azure.microsoft.com/features/gov/) az Egyesült Államok kormányának felhője.

Ezt a felhőt a DirSync korábbi kiadásai is támogatták. Az Azure AD Connect 1.1.180-as buildjéből a felhő következő generációja támogatott. Ez a generáció csak az USA-ban alapú végpontokat használ, és a proxykiszolgálón megnyitható URL-címek egy másik listájával rendelkezik.

| A proxykiszolgálón megnyitandó URL-címek |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (az Azure Government-bérlők automatikus észleléséhez szükséges) |
| \*gov.us.microsoftonline.com |
| +Visszavont tanúsítványok listája |

> [!NOTE]
> Az Azure AD Connect 1.1.647.0-s verziójától az AzureInstance értékbeállítás beállítása a beállításjegyzékben már nem szükséges, feltéve, hogy a *.windows.net meg van nyitva a proxykiszolgáló(k)on. Azonban az ügyfelek számára, amelyek nem teszik lehetővé az internet-kapcsolat az Azure AD Connect-kiszolgáló(k) a következő manuális konfiguráció használható.

### <a name="manual-configuration"></a>Kézi konfiguráció

A következő manuális konfigurációs lépések biztosítják, hogy az Azure AD Connect az Azure Government szinkronizálási végpontokat használja.

1. Indítsa el az Azure AD Connect telepítését.
2. Amikor megjelenik az első olyan oldal, ahol el kell fogadnia a eula-t, ne folytassa, hanem hagyja futni a telepítővarázslót.
3. Indítsa el a regedit `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` et, `4`és módosítsa a rendszerleíró kulcsot az értékre.
4. Lépjen vissza az Azure AD Connect telepítővarázslóhoz, fogadja el a eula-t, és folytassa. A telepítés során győződjön meg arról, hogy az **egyéni konfigurációs** telepítési útvonalat használja (és nem az Express telepítést), majd folytassa a telepítést a szokásos módon.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

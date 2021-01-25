---
title: Azure Active Directory-adatkapcsolat összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan gyűjthet adatokat Azure Active Directoryről, és hogyan továbbíthatja az Azure AD bejelentkezési naplóit és naplóit az Azure Sentinelbe.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 409a316bd9c4222dd9b8ff30e42e37d23805c38b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757762"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Adatok összekötése Azure Active Directoryról (Azure AD)

Az Azure Sentinel beépített összekötője segítségével adatokat gyűjthet a [Azure Active Directoryről](../active-directory/fundamentals/active-directory-whatis.md) , és továbbíthatja azt az Azure sentinelbe. Az összekötő segítségével továbbíthatja a [bejelentkezési naplókat](../active-directory/reports-monitoring/concept-sign-ins.md) [és a naplókat](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Előfeltételek

- Minden Azure AD-licenc (ingyenes/O365/P1/P2) elegendő a bejelentkezési naplók Azure Sentinelbe való betöltéséhez. A Azure Monitor (Log Analytics) és az Azure Sentinel további GB-os díjat is igényelhet.

- A felhasználónak hozzá kell rendelnie az Azure Sentinel közreműködő szerepkört a munkaterületen.

- A felhasználónak hozzá kell rendelnie a globális rendszergazdai vagy biztonsági rendszergazdai szerepköröket azon a bérlőn, amelyről a naplókat továbbítani kívánja.

- A felhasználónak olvasási és írási engedélyekkel kell rendelkeznie az Azure AD diagnosztikai beállításaihoz, hogy láthassa a kapcsolatok állapotát. 

## <a name="connect-to-azure-active-directory"></a>Kapcsolódás Azure Active Directoryhoz

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget a navigációs menüből.

1. Az adatösszekötők katalógusában válassza a **Azure Active Directory** lehetőséget, majd válassza az **összekötő lap megnyitása** lehetőséget.

1. Jelölje be azon naplók melletti jelölőnégyzeteket, amelyeket az Azure Sentinelbe szeretne továbbítani, majd kattintson a **Kapcsolódás** elemre. A következő típusú naplók közül választhat:

    - **Bejelentkezési naplók**: a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról szóló információk.
    - **Naplók**: rendszertevékenység-információk a felhasználók és csoportok kezelésével, a felügyelt alkalmazásokkal és a címtárral kapcsolatos tevékenységekkel kapcsolatban.
    - **Nem interaktív felhasználói bejelentkezési naplók**: az ügyfél által a felhasználó nevében végrehajtott bejelentkezésekkel kapcsolatos információk, amelyek nem igényelnek semmilyen interakciót vagy hitelesítési tényezőt a felhasználótól.
    - **Egyszerű szolgáltatás bejelentkezési naplói**: az alkalmazások és az olyan egyszerű szolgáltatások bejelentkezési adatai, amelyek nem tartalmaznak felhasználót. Ezekben a bejelentkezésekben az alkalmazás vagy szolgáltatás hitelesítő adatokat szolgáltat a saját nevében az erőforrások hitelesítéséhez vagy eléréséhez.
    - **Felügyelt identitású bejelentkezési naplók**: az Azure által felügyelt titkos kulcsokkal rendelkező Azure-erőforrások bejelentkezése.
    - **Naplók** kiosztása: rendszertevékenység-információk az Azure ad-kiépítési szolgáltatás által kiépített felhasználókról, csoportokról és szerepkörökről.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **naplók** területen, a **LogManagement** szakaszban jelenik meg, az alábbi táblázatokban:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Az Azure AD-naplók lekérdezéséhez írja be a megfelelő táblanév nevet a lekérdezési ablak tetején.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure Active Directory az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

---
title: Az Azure Active Directory Identity Protection által észlelt biztonsági rések |} A Microsoft Docs
description: Az Azure Active Directory Identity Protection által észlelt biztonsági rések áttekintése.
services: active-directory
keywords: az Azure active directory identity protection, a cloud discovery, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fec9693641ff5918f622ecceee3fb94828b508e
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517895"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Az Azure Active Directory Identity Protection által észlelt biztonsági rések
Biztonsági rések olyan gyengeségek az adott környezetben, a támadók kihasználhatnák. Azt javasoljuk, hogy oldja meg a biztonsági rések javítása a szervezete biztonsági állapotát, és megakadályozza, hogy a támadók kihasználja őket.


![vulnerabilities](./media/vulnerabilities/101.png "vulnerabilities")



A következő szakaszok a biztonsági rések jelentette az Identity Protection áttekintése.

## <a name="multi-factor-authentication-registration-not-configured"></a>Nem konfigurált Multi-Factor Authentication-regisztráció
A biztonsági rés segít a szervezet az Azure multi-factor Authentication üzembe helyezését. 

Azure multi-factor authentication szolgáltatás egy második biztonsági szintként, felhasználói hitelesítés biztosít. Ez segít iránt támasztott felhasználói igényeknek egy egyszerű bejelentkezési folyamat adatokhoz és alkalmazásokhoz való hozzáférés védelme érdekében. Több egyszerű ellenőrzési lehetőség erős hitelesítést biztosít – telefonhívás, szöveges üzenet vagy mobilalkalmazás értesítési vagy ellenőrző kódot és a harmadik féltől származó OATH-tokenek.

Azt javasoljuk, hogy a felhasználói bejelentkezéseket az Azure multi-factor Authentication szükséges. A multi-factor authentication kulcsfontosságú szerepet játszik a kockázatalapú feltételes hozzáférési szabályzatok Identity Protection keresztül érhető el.

További információt [az Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md) ismertetőjében talál.

## <a name="unmanaged-cloud-apps"></a>Nem felügyelt felhőalkalmazások
A biztonsági rés segítségével azonosíthatja a nem kezelt felhőalkalmazások a szervezetben.

Modern vállalati IT-részlegek számára deduplikálta gyakran az összes a felhőalapú alkalmazások, amelyek a felhasználók a szervezetben való használatával végzik a munkájukat. Legyen könnyen látható, hogy miért a rendszergazdák lenne vállalati adatokat, lehetséges, az adatok kiszivárgásának és egyéb biztonsági kockázatokat való jogosulatlan hozzáféréssel kapcsolatos elvárásainak. 

Javasoljuk, hogy a nem kezelt felhőalkalmazások felderítése, és ezek az alkalmazások Azure Active Directory használatával kezelheti a Cloud Discovery üzembe helyezése.

További információkért lásd: [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>A Privileged Identity Management szolgáltatástól származó biztonsági riasztások
A biztonsági rés segít felderíteni, és oldja meg a szervezet emelt szintű identitások kapcsolatos riasztásokat.  

Ahhoz, hogy a felhasználók számára a privilegizált műveleteket végezni, szervezetek kell biztosítania a felhasználók ideiglenes vagy állandó emelt szintű hozzáférés az Azure AD-ben erőforrásokhoz, Azure vagy Office 365 vagy más SaaS-alkalmazások. Kiemelt jogosultságú felhasználók növeli a támadási felületet a szervezet. A biztonsági rés segítséget nyújt a szükségtelen emelt szintű hozzáféréssel rendelkező felhasználók azonosítása, és megteheti a szükséges lépéseket, csökkentheti vagy elkerülheti a kockázatot jelentenek. 

Azt javasoljuk, hogy a szervezet Azure AD Privileged Identity Management segítségével kezelheti, irányíthatja, és a figyelő az emelt szintű identitások és azok az Azure AD-erőforrásokhoz való hozzáférésének, valamint más Microsoft online szolgáltatásaihoz, például az Office 365 vagy a Microsoft Intune.

További információkért lásd: [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="see-also"></a>Lásd még

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)


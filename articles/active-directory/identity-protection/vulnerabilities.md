---
title: Az Azure Active Directory Identity Protection által észlelt biztonsági rések
description: Az Azure Active Directory Identity Protection által észlelt biztonsági rések áttekintése.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80faf1899da0f16d25b9b7c68cb0edcbc1276632
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59488233"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Az Azure Active Directory Identity Protection által észlelt biztonsági rések

Biztonsági rések olyan gyengeségek olyan környezetben, a támadók kihasználhatnák. Azt javasoljuk, hogy a rendszergazdák oldja meg a biztonsági rések javítása a szervezete biztonsági állapotát.

![Biztonsági rések jelentette az Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

A következő szakaszok a biztonsági rések jelentette az Identity Protection áttekintése.

## <a name="multi-factor-authentication-registration-not-configured"></a>Nincs konfigurálva a többtényezős hitelesítési regisztráció

A biztonsági rés segítségével felmérheti a szervezet Azure multi-factor Authentication szolgáltatás telepítését.

Megtekintéséhez a száma, a felhasználók, amelyek nem a multi-factor Authentication, kattintson a, és a biztonsági rés irányítja át statisztika identitás biztonságos pontszám belül.

Az Azure multi-factor Authentication a második biztonsági szintként, felhasználói hitelesítés biztosít. Ez segít iránt támasztott felhasználói igényeknek egy egyszerű bejelentkezési folyamat adatokhoz és alkalmazásokhoz való hozzáférés védelme érdekében. Az Azure multi-factor Authentication nyújt egyszerű ellenőrzési lehetőség, például:

* Telefonhívás
* Szöveges üzenet
* Mobilalkalmazás-értesítés
* Egyszeri Jelszavas ellenőrző kód

Azt javasoljuk, hogy a felhasználói bejelentkezéseket az Azure multi-factor Authentication szükséges. A multi-factor authentication kulcsfontosságú szerepet játszik a kockázatalapú feltételes hozzáférési szabályzatok Identity Protection keresztül érhető el.

További információt [az Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md) ismertetőjében talál.

## <a name="unmanaged-cloud-apps"></a>Nem felügyelt felhőalkalmazások

A biztonsági rés segítségével azonosíthatja a nem kezelt felhőalkalmazások a szervezetben.

Informatikai szakemberek gyakran deduplikálta a szervezet a felhőalapú alkalmazások. Legyen könnyen látható, hogy miért a rendszergazdák lenne vállalati adatokat, lehetséges, az adatok kiszivárgásának és egyéb biztonsági kockázatokat való jogosulatlan hozzáféréssel kapcsolatos elvárásainak.

Javasoljuk, hogy a Cloud Discovery nem kezelt felhőalkalmazások felderítése, és ezek az alkalmazások Azure Active Directory használatával kezelheti.

További információkért lásd: [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>A Privileged Identity Management szolgáltatástól származó biztonsági riasztások

A biztonsági rés segít felderíteni, és oldja meg a szervezet emelt szintű identitások kapcsolatos riasztásokat.  

Ahhoz, hogy a felhasználók számára a privilegizált műveleteket végezni, szervezetek kell biztosítania a felhasználók ideiglenes vagy állandó emelt szintű hozzáférés az Azure AD-ben erőforrásokhoz, Azure vagy Office 365 vagy más SaaS-alkalmazások. Kiemelt jogosultságú felhasználók növeli a támadási felületet a szervezet. A biztonsági rés segítséget nyújt a szükségtelen emelt szintű hozzáféréssel rendelkező felhasználók azonosítása, és megteheti a szükséges lépéseket, csökkentheti vagy elkerülheti a kockázatot jelentenek.

Azt javasoljuk a szervezeteknek használata kezelheti, az Azure AD Privileged Identity Management vezérlőelemet, és figyelheti az Azure ad-ben emelt jogosultsági szintű identitásait, valamint más Microsoft online szolgáltatásaihoz, például az Office 365 vagy a Microsoft Intune.

További információkért lásd: [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Lásd még

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

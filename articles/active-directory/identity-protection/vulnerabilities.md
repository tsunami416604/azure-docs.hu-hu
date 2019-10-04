---
title: A Azure Active Directory Identity Protection által észlelt biztonsági rések
description: A Azure Active Directory Identity Protection által észlelt biztonsági rések áttekintése.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446e2ef33b714afe06f24f3dbfc46bc6a42790b8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335149"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>A Azure Active Directory Identity Protection által észlelt biztonsági rések

A biztonsági rések a támadók által kihasználható környezetek gyengeségei. Javasoljuk, hogy a rendszergazdák ezeket a biztonsági réseket a szervezete biztonsági helyzetének javítására használják.

![Az Identity Protection által jelentett biztonsági rések](./media/vulnerabilities/identity-protection-vulnerabilities.png)

A következő szakaszokban áttekintheti az Identity Protection által jelentett biztonsági réseket.

## <a name="multi-factor-authentication-registration-not-configured"></a>Nincs konfigurálva a multi-Factor Authentication-regisztráció

Ez a biztonsági rés segít felmérni az Azure multi-Factor Authentication üzembe helyezését a szervezetben.

Az Azure multi-Factor Authentication egy második biztonsági réteget biztosít a felhasználói hitelesítéshez. Segít az adatvédelemhez és az alkalmazásokhoz való hozzáférésben, miközben a felhasználói igényeket egy egyszerű bejelentkezési folyamatra is megtarthatja. Az Azure multi-Factor Authentication egyszerűen használható ellenőrzési lehetőségeket kínál, például:

* Telefonhívás
* Szöveges üzenet
* Mobilalkalmazásbeli értesítés
* OTP ellenőrző kód

Javasoljuk, hogy az Azure multi-Factor Authentication használatát igényli a felhasználói bejelentkezésekhez. A többtényezős hitelesítés kulcsfontosságú szerepet játszik az Identity Protection által elérhető kockázatalapú feltételes hozzáférési szabályzatokban.

További információt [az Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md) ismertetőjében talál.

## <a name="unmanaged-cloud-apps"></a>Nem kezelt felhőalkalmazások

Ez a biztonsági rés segítséget nyújt a nem felügyelt felhőalapú alkalmazások azonosításában a szervezetben.

Az informatikai munkatársak gyakran nem ismerik a szervezeten belüli összes felhőalapú alkalmazást. Könnyen megtekintheti, hogy a rendszergazdák miért ütköznek a vállalati adatvédelemmel, a lehetséges adatszivárgással és más biztonsági kockázatokkal kapcsolatos nem megfelelő jogosultságokkal.

Javasoljuk, hogy Cloud Discovery üzembe helyezését a nem felügyelt felhőalapú alkalmazások felderítéséhez, valamint az alkalmazások Azure Active Directory használatával történő kezeléséhez.

További információ: [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>A Privileged Identity Management biztonsági riasztásai

Ez a biztonsági rés segít felderíteni és elhárítani a szervezetében található emelt szintű identitásokkal kapcsolatos riasztásokat.  

Ahhoz, hogy a felhasználók elvégezzék a Kiemelt műveleteket, a szervezeteknek ideiglenes vagy állandó jogosultságú hozzáférést kell biztosítaniuk a felhasználóknak az Azure AD-ben, az Azure-ban, az Office 365-erőforrásokban vagy más SaaS-alkalmazásokban. A Kiemelt jogosultságú felhasználók mindegyike növeli a szervezet támadási felületét. Ez a biztonsági rés lehetővé teszi a szükségtelen jogosultsági szintű hozzáféréssel rendelkező felhasználók azonosítását, és megfelelő lépéseket tehet az általuk jelentett kockázat csökkentése vagy kiküszöbölése érdekében.

Azt javasoljuk, hogy a Azure AD Privileged Identity Management szervezetek az Azure AD-ben és más Microsoft-online szolgáltatások, például az Office 365-as vagy a Microsoft Intune-ban felügyelni, ellenőrizni és figyelni tudják a Kiemelt identitásokat.

További információkért lásd: [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Lásd még

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

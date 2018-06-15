---
title: Azure Active Directory Identity Protection által észlelt biztonsági rések |} Microsoft Docs
description: Az Azure Active Directory Identity Protection által észlelt biztonsági rések áttekintése.
services: active-directory
keywords: az Azure active directory azonosító adatok védelmét, a cloud app discovery, alkalmazások, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 9ee926ff7cf44041c1969d541ca225395ba31a2f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713694"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection által észlelt biztonsági rések
Biztonsági rések a környezetben, amely is kihasználható egy támadó gyengeségei miatt. Azt javasoljuk, hogy a szervezet meghatározott biztonsági előírások javítása érdekében a biztonsági rések cím, és hogy a támadók kihasználva őket.


![biztonsági rések](./media/active-directory-identityprotection-vulnerabilities/101.png "biztonsági réseket")



A következő szakaszokban Identity Protection által jelentett biztonsági rések áttekintést.

## <a name="multi-factor-authentication-registration-not-configured"></a>Nincs konfigurálva a többtényezős hitelesítésre való regisztráció
A biztonsági rés segít a szervezet Azure multi-factor Authentication központi telepítésére. 

Az Azure multi-factor Authentication hitelesítést biztosít egy második biztonsági szintként, felhasználói hitelesítés. Az segítségével megakadályozhatja a adatokhoz és alkalmazásokhoz való hozzáférés mellett egyszerű bejelentkezési folyamatot a felhasználó igény szerint. Erős hitelesítés, könnyen ellenőrzési lehetőségek széles keresztül biztosítja – a telefonhívás, szöveges üzenet vagy mobilalkalmazás értesítés vagy ellenőrző kód és a harmadik féltől származó OATH-tokeneket.

Azt javasoljuk, hogy a felhasználói bejelentkezések Azure multi-factor Authentication hitelesítés szükséges. A multi-factor authentication kulcsfontosságú szerepet játszik a kockázat-alapú feltételes hozzáférési házirendek Identity Protection keresztül érhető el.

További információkért lásd: [Mi az Azure multi-factor Authentication?](authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Nem kezelt felhőalkalmazások
A biztonsági rés segít azonosítani a szervezet nem felügyelt felhőalkalmazások.

A modern vállalatok számára az informatikai részleg gyakran nem tudnak a a szervezethez tartozó felhasználók használnak végezhesse a munkáját a felhőalapú alkalmazásokhoz. Legyen könnyen látható, hogy miért a rendszergazdák jogosulatlan hozzáférés a vállalati adatokat, lehetséges adatszivárgás és egyéb biztonsági kockázatok kétségei vannak. 

Azt javasoljuk, központi telepítéséhez a Cloud App Discovery nem felügyelt felhőalapú alkalmazások felderítésére, valamint hogyan kezelhetők ezek az alkalmazások az Azure Active Directoryval.

További információkért lásd: [keresése a nem felügyelt felhőalapú alkalmazásokhoz, a Cloud App Discovery](manage-apps/cloud-app-discovery.md).

## <a name="security-alerts-from-privileged-identity-management"></a>A Privileged Identity Management biztonsági riasztásai
A biztonsági rés segít felderíteni, és oldja meg a szervezet a kiemelt jogosultságú identitások kapcsolatos riasztások.  

Ahhoz, hogy a felhasználók a jogosultságokhoz kötött műveletek végrehajtására, a szervezetek engedélyt kell felhasználók ideiglenes és állandó kiemelt az Azure AD-erőforrások Azure vagy az Office 365 vagy más Szolgáltatottszoftver-alkalmazásoknál. A megfelelő jogosultsággal rendelkező felhasználók mindegyikének növeli a támadási felületet a szervezet. A biztonsági rés segítséget nyújt a szükségtelen jogosultsági szintű hozzáféréssel rendelkező felhasználók azonosítása, és hajtsa végre a megfelelő műveletet, csökkentheti vagy elkerülheti a kockázatot jelentenek. 

Azt javasoljuk, hogy a szervezet Azure AD Privileged Identity Management használ kezelése, szabályozása és figyelése emelt szintű identitások és az Azure AD-erőforrások elérését, valamint más Microsoft online szolgáltatások, például az Office 365-öt vagy a Microsoft Intune.

További információkért lásd: [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="see-also"></a>Lásd még
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


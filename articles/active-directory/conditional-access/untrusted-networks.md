---
title: Többszintű szolgáltatás megkövetelése nem megbízható hálózatokról – Azure Active Directory
description: Megtudhatja, hogyan konfigurálhat feltételes hozzáférési szabályzatot az Azure Active Directoryban (Azure AD) a nem megbízható hálózatokból származó hozzáférési kísérletekhez.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379992"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Útmutató: Többszintű forrás megkövetelése a feltételes hozzáféréssel rendelkező nem megbízható hálózatokból való hozzáféréshez   

Az Azure Active Directory (Azure AD) bárhonnan egyszeri bejelentkezést tesz lehetővé az eszközökre, alkalmazásokra és szolgáltatásokra. A felhasználók nem csak a szervezet hálózatáról, hanem bármely nem megbízható internetes helyről is elérhetik a felhőalkalmazásokat. A nem megbízható hálózatokból való hozzáférés általános ajánlott gyakorlata a többtényezős hitelesítés (MFA) megkövetelése.

Ez a cikk a nem megbízható hálózatokból való hozzáféréshez szükséges feltételes hozzáférési házirend konfigurálásához szükséges információkat tartalmazza. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a következőket: 

- Az Azure AD feltételes hozzáférés [alapvető fogalmai](overview.md) 
- A feltételes hozzáférési [szabályzatok](best-practices.md) azure portalon történő konfigurálásának ajánlott eljárásai

## <a name="scenario-description"></a>Forgatókönyv leírása

A biztonság és a hatékonyság közötti egyensúly elsajátításához elegendő lehet, ha csak a szervezet hálózatáról érkező bejelentkezések hez szükséges jelszót kér. Azonban a nem megbízható hálózati helyről való hozzáférés esetén fokozott a kockázata annak, hogy a bejelentkezéseket nem jogszerű felhasználók hajtják végre. Az aggodalomra okot adó probléma megoldásához letilthatja a nem megbízható hálózatokból való hozzáférést. Azt is megteheti, hogy többtényezős hitelesítést (MFA) is megkövetelhet, hogy további biztosítékot kapjon arra vonatkozóan, hogy a fiók jogos tulajdonosa kísérletet tett. 

Az Azure AD feltételes hozzáférés, akkor ezt a követelményt egyetlen szabályzat, amely hozzáférést biztosít: 

- A kijelölt felhőalapú alkalmazásokra
- Kijelölt felhasználók és csoportok számára  
- Többtényezős hitelesítés megkövetelése 
- Ha a hozzáférés a következőkből származik: 
   - Nem megbízható hely

## <a name="implementation"></a>Megvalósítás

Ebben a forgatókönyvben az a feladata, hogy *a nem megbízható hálózati helyről származó hozzáférést* feltételes hozzáférési feltételsé alakítsa le. Feltételes hozzáférési házirendben a [helyek feltételét](location-condition.md) a hálózati helyekkel kapcsolatos forgatókönyvek kezelésére konfigurálhatja. A helyek feltétel lehetővé teszi, hogy kiválassza az elnevezett helyeket, amelyek az IP-címtartományok, országok és régiók logikai csoportjai.  

A szervezet általában egy vagy több címtartománytulajdonosa, például 199.30.16.0 - 199.30.16.15.
Az elnevezett helyet a következő konkretinációk szerint állíthatja be:

- A tartomány megadása (199.30.16.0/28) 
- Leíró név, például **vállalati hálózat hozzárendelése** 

Ahelyett, hogy megpróbálna meghatározni, hogy melyek az összes nem megbízható hely, a következőket teheti:

- Bármilyen hely belefoglalása 

   ![Feltételes hozzáférés](./media/untrusted-networks/02.png)

- Az összes megbízható hely kizárása 

   ![Feltételes hozzáférés](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Szabályzat érvénybe léptetése

A jelen cikkben ismertetett megközelítéssel most konfigurálhatja a nem megbízható helyek feltételes hozzáférési házirendet. Annak érdekében, hogy a szabályzat a várt módon működjön, ajánlott eljárás, hogy tesztelje, mielőtt éles környezetben. Ideális esetben egy tesztbérlő segítségével ellenőrizze, hogy az új szabályzat megfelelően működik-e. További információ: [Új házirend telepítése.](best-practices.md#how-should-you-deploy-a-new-policy) 

## <a name="next-steps"></a>További lépések

Ha szeretne többet megtudni a feltételes hozzáférés, olvassa el [a Mi a feltételes hozzáférés az Azure Active Directoryban?](../active-directory-conditional-access-azure-portal.md)

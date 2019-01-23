---
title: Többtényezős hitelesítés (MFA) megkövetelése a hozzáféréshez a nem megbízható hálózatok az Azure Active Directory (Azure AD) feltételes hozzáférés hogyan |} A Microsoft Docs
description: Ismerje meg, hogyan egy feltételes hozzáférési szabályzat konfigurálása az Azure Active Directoryban (Azure AD) való hozzáférési kísérleteket a nem megbízható hálózatokon.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 7ffb3bfb4985f56e7e2e81a2a6d08a6ff7469fdb
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445405"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Útmutató: Többtényezős hitelesítés megkövetelése a hozzáféréshez a nem megbízható hálózatokhoz, a feltételes hozzáférés   

Az Azure Active Directory (Azure AD) lehetővé teszi az egyszeri bejelentkezést az eszközök, alkalmazások és szolgáltatások bárhonnan. Felhasználói bármikor hozzáférhetnek a felhőbeli alkalmazásokat, nem csak a vállalati hálózaton, hanem nem megbízható internetes bárhonnan. Hozzáférést a nem megbízható hálózatokon általános ajánlott eljárás, akkor a multi-factor authentication (MFA) kötelező.

Ez a cikk a feltételes hozzáférési szabályzatot, amely többtényezős Hitelesítést követel meg a hozzáférést a nem megbízható hálózatok konfigurálásához szükséges információkat biztosít. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy Ön ismeri a: 

- A [alapvető fogalmait](overview.md) az Azure AD feltételes hozzáférés 
- A [ajánlott eljárások](best-practices.md) a feltételes hozzáférési szabályzatok konfigurálása az Azure Portalon



## <a name="scenario-description"></a>Forgatókönyv leírása

Szakértőjévé válhat biztonság és hatékonyság közötti egyensúly, azt is elegendő lehet, hogy csak a vállalati hálózaton történő bejelentkezések a jelszó szükséges. Hozzáférés egy nem megbízható hálózati helyről, van azonban egy megnövekedett kockázata, hogy a bejelentkezések, amelyek nem megbízható a felhasználók által végrehajtott. Ezen probléma megoldásának, hogy akkor képes blokkolni a hozzáférést a nem megbízható hálózatokon. Azt is megteheti többtényezős hitelesítés (MFA) történt kísérlet, a fiók jogos tulajdonosa vissza további frissítési garanciát biztosító próbál a jeggyel is megkövetelheti. 

Az Azure AD feltételes hozzáférés meg lehet oldani ezt a követelményt, egy egyetlen szabályzattal, amely engedélyezi a hozzáférést: 

- A kiválasztott felhőalapú alkalmazások

- A kijelölt felhasználók és csoportok  

- Többtényezős hitelesítés megkövetelése 

- Ha hozzáférést naplózásból származnak: 

    - Egy helyet, amely nem megbízható


## <a name="implementation"></a>Megvalósítás

Az ebben a forgatókönyvben a kihívás abban áll lefordítani *hozzáférést egy nem megbízható hálózati helyről* , a feltételes hozzáférés egy feltétele. A feltételes hozzáférési szabályzat, konfigurálhatja a [helyek feltétel](location-condition.md) helyzetekre kapcsolódó hálózati helyeket. A helyek feltétel lehetővé teszi, hogy válassza ki a nevesített helyek, amely az IP-címtartományok, országok és régiók logikai csoportosításán alapulnak.  

Általában a szervezete tulajdonában van egy vagy több-címtartományokat, például 199.30.16.0 - 199.30.16.24.
Konfigurálhat egy elnevezett helye szerint:

- Ez a tartomány (199.30.16.0/24) megadása 

- Egy leíró nevet például hozzárendelése **vállalati hálózat** 


Helyett adja meg, mi minden hely, amelyek nem megbízható, a következőket teheti:

- Minden hely felvétele 

    ![Feltételes hozzáférés](./media/untrusted-networks/02.png)

- Minden megbízható hely kizárása 

    ![Feltételes hozzáférés](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>Házirend központi telepítése

A következő cikkben ismertetett módszert használja nem megbízható helyekre vonatkozó feltételes hozzáférési szabályzat most konfigurálhatja. Győződjön meg arról, hogy a szabályzat a várt módon működik-e, hogy az ajánlott eljárás tesztelése, mielőtt éles környezetben megvalósítaná. Ideális esetben egy tesztelési bérlőn használatával győződjön meg arról, hogy az új szabályzat helyesen működik-e. További információkért lásd: [hogyan helyezhet üzembe egy új szabályzat](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>További lépések

Ha szeretne további információ a feltételes hozzáférésről, lásd: [Mi az az Azure Active Directory feltételes hozzáférés?](../active-directory-conditional-access-azure-portal.md)
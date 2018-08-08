---
title: Hogyan – feltételes hozzáférési szabályzatainak konfigurálása az Azure Active Directory hozzáférési a nem megbízható hálózatokhoz |} A Microsoft Docs
description: Ismerje meg, hogyan egy feltételes hozzáférési szabályzat konfigurálása az Azure Active Directoryban (Azure AD) való hozzáférési kísérleteket a nem megbízható hálózatokon.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, az Azure AD feltételes hozzáférés, biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: protection
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d170cdb42f7e3a368a50279f8a311a4ae94689df
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601807"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Útmutató: a nem megbízható hálózatokon kísérlete feltételes hozzáférési szabályzatok konfigurálása   

Mobileszközök és a felhő-és felhőközpontú világában Azure Active Directory (Azure AD) lehetővé teszi, hogy egyszeri bejelentkezéshez az eszközök, alkalmazások és szolgáltatások bárhonnan. Ennek eredményeképpen felhasználói bármikor hozzáférhetnek a felhőbeli alkalmazásokat, nem csak a vállalati hálózaton, hanem nem megbízható internetes bárhonnan. A [Azure Active Directory (Azure AD) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy jogosult felhasználók hozzáférhetnek a felhőbeli alkalmazásokat. Ebben a környezetben egy általános követelmény, hogy szabályozza a nem megbízható hálózatokon kezdeményezett hozzáférési kísérleteket. Ez a cikk, amely kezeli ezt a követelményt a feltételes hozzáférési szabályzat konfigurálásához szükséges információkat biztosít. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy Ön ismeri a: 

- Az alapvető fogalmait, az Azure AD feltételes hozzáférés 
- Feltételes hozzáférési szabályzatok konfigurálása az Azure Portalon

Lásd:

- [Mi a feltételes hozzáférés az Azure Active Directory](../active-directory-conditional-access-azure-portal.md) – feltételes hozzáférés áttekintése 

- [Gyors útmutató: Többtényezős hitelesítés az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md) – az első, némi tapasztalattal a feltételes hozzáférési szabályzatok konfigurálása. 


## <a name="scenario-description"></a>Forgatókönyv leírása

Szakértőjévé válhat biztonság és hatékonyság közötti egyensúly, azt is elegendő lehet, hogy a felhasználó hitelesíthető a jelszó csak akkor van szükség. Ha történt hozzáférési kísérlet egy nem megbízható hálózati helyről, van azonban egy megnövekedett kockázata, hogy a bejelentkezések, amelyek nem megbízható a felhasználók által végrehajtott. Ezen probléma megoldásának, tiltsa le a hozzáférési kísérleteket a nem megbízható hálózatokon. Azt is megteheti többtényezős hitelesítés (MFA) történt kísérlet, a fiók jogos tulajdonosa vissza további frissítési garanciát biztosító próbál a jeggyel is megkövetelheti. 

Az Azure AD feltételes hozzáférés meg lehet oldani ezt a követelményt, egy egyetlen szabályzattal, amely engedélyezi a hozzáférést: 

- A kiválasztott felhőalapú alkalmazások

- A kijelölt felhasználók és csoportok  

- Többtényezős hitelesítés megkövetelése 

- Ha egy hozzáférési kísérlet kérés érkezett: 

    - Egy helyet, amely nem megbízható


## <a name="considerations"></a>Megfontolandó szempontok

Az ebben a forgatókönyvben a kihívás abban áll lefordítani *mikor történt hozzáférési kísérlet, amely nem megbízható helyről* , a feltételes hozzáférés egy feltétele. A feltételes hozzáférési szabályzat, konfigurálhatja a [helyek feltétel](location-condition.md) helyzetekre kapcsolódó hálózati helyeket. A helyek feltétel nevesített helyek, amely logikai csoportosításait IP-címtartományok, országok és régiók kiválasztása lehetővé teszi.  

Általában a szervezete tulajdonában van egy vagy több-címtartományokat, például 199.30.16.0 - 199.30.16.24.
Konfigurálhat egy elnevezett helye szerint:

- Ez a tartomány (199.30.16.0/24) megadása 

- Egy leíró nevet például hozzárendelése **vállalati hálózat** 


Helyett adja meg, mi minden hely, amelyek nem megbízható, a következőket teheti:

- Belefoglalás 

    ![Feltételes hozzáférés](./media/untrusted-networks/02.png)

- Minden megbízható hely kizárása 

    ![Feltételes hozzáférés](./media/untrusted-networks/01.png)



## <a name="implementation"></a>Megvalósítás

A következő cikkben ismertetett módszert használja nem megbízható helyekre vonatkozó feltételes hozzáférési szabályzat most konfigurálhatja. Mielőtt éles környezetben, győződjön meg arról, hogy az elvárt módon működik, mindig tesztelje a szabályzatot. Ideális esetben hajtsa végre a tesztelési célú bérlői kezdeti teszteket, ha lehetséges. További információkért lásd: [hogyan kell egy új házirendet telepít](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>További lépések

Ha szeretne további információ a feltételes hozzáférésről, lásd: [Mi az az Azure Active Directory feltételes hozzáférés?](../active-directory-conditional-access-azure-portal.md)
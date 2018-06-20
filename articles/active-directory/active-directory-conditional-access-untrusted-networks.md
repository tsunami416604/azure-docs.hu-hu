---
title: Hogyan – Azure Active Directory konfigurálása a feltételes hozzáférési házirendeket az hozzáférési kísérletről a nem megbízható hálózatokon |} Microsoft Docs
description: 'Útmutató: a feltételes hozzáférési szabályzat konfigurálása az Azure Active Directory (Azure AD) való hozzáférés megkísérelnek-e a nem megbízható hálózatokon.'
services: active-directory
keywords: alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 952a3a3952a96c7125e0b0dbe770b72c17a57101
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232517"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Útmutató: a feltételes hozzáférési házirendek konfigurálása kísérlete nem megbízható hálózatokon   

Mobileszköz-first, a felhő-első világában Azure Active Directory (Azure AD) lehetővé teszi, hogy az egyszeri bejelentkezés eszközök, alkalmazások és szolgáltatások bárhonnan. Ennek eredményeként a felhasználók férhetnek hozzá a felhőalapú alkalmazások nem csak a vállalati hálózathoz, de is bárhonnan, nem megbízható internetes. A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy hogyan jogosult felhasználók úgy férhetnek hozzá a felhőalapú alkalmazásokhoz. Ebben a környezetben közös követelményt hoz kezdeményezése a nem megbízható hálózatokon hozzáférések szabályozása. Ez a cikk azt ismerteti a konfigurálja a feltételes hozzáférési szabályzatot, amely kezeli ezt a követelményt. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy Ön ismeri a: 

- A feltételes hozzáférés az Azure AD alapvető fogalmait 
- Feltételes hozzáférési szabályzatok konfigurálása az Azure-portálon

Lásd:

- [Mi az az Azure Active Directoryban feltételes hozzáférés](active-directory-conditional-access-azure-portal.md) - áttekintését a feltételes hozzáférés 

- [Gyors üzembe helyezés: Azure Active Directory feltételes hozzáféréssel rendelkező adott alkalmazásokhoz megkövetelő](active-directory-conditional-access-app-based-mfa.md) – némi tapasztalattal a feltételes hozzáférési szabályzatok konfigurálása eléréséhez. 


## <a name="scenario-description"></a>Forgatókönyv leírása

Biztonsági és a termelékenység közötti egyensúly főkiszolgálóvá, azt is elegendő lehet, hogy csak a felhasználó hitelesíthető a jelszót. Azonban ha hozzáférési kísérlet történt egy nem megbízható hálózati helyről, van egy nagyobb kockázatot jelent, amelyek bejelentkezések nem jogosult felhasználók által végzett. Ezen probléma megoldásának tilthatja le a nem megbízható hálózatokon hozzáférési kísérlet. Azt is megteheti többtényezős hitelesítés (MFA) ahhoz, hogy vissza további olyan garantálja, hogy a fiók jogos tulajdonosa történt kísérlet is megkövetelheti. 

A feltételes hozzáférés az Azure AD meg lehet oldani ezt a követelményt, amely hozzáférést biztosít egy házirendnek: 

- Kijelölt felhőalapú alkalmazásokhoz

- A kijelölt felhasználók és csoportok  

- Többtényezős hitelesítés megkövetelése 

- Ha egy hozzáférési kísérlet érkezett: 

    - Egy olyan helyre, nem megbízható


## <a name="considerations"></a>Megfontolandó szempontok

A dokumentációkat az ebben a forgatókönyvben az lefordítani *mikor történt hozzáférési kísérlet, amely nem megbízható helyről* azokat a feltételes hozzáférés egy feltétele. A feltételes hozzáférési szabályzatot, konfigurálhatja a [helyek feltétel](active-directory-conditional-access-locations.md) forgatókönyvek kezeléséhez kapcsolódó hálózati helyeket. A helyek feltétel lehetővé teszi [helyek nevű](active-directory-conditional-access-locations.md#named-locations), amely az IP-címtartományok, országokban és régiókban logikai csoportosításait jelképező.  

Általában a szervezete rendelkezik egy vagy több-címtartományokat, például 199.30.16.0 - 199.30.16.24.
Konfigurálhatja egy nevesített, hely:

- Ez a tartomány (199.30.16.0/24) megadása 

- Például hozzárendelése egy leíró nevet **vállalati hálózat** 


Nem próbálja meg mi összes helyét, amelyek nem megbízható, a következő műveletek végezhetők el:

- Belefoglalás 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-untrusted-networks/02.png)

- Az összes megbízható helyek kizárása 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-untrusted-networks/01.png)



## <a name="implementation"></a>Megvalósítás

A cikkben ismertetett módszert konfigurálhat egy feltételes hozzáférési házirend nem megbízható helyeken. A házirend mindig kell tesztelni, mielőtt éles környezetben győződjön meg arról, hogy az elvárt módon működik. Ideális esetben a kezdeti teszteket egy tesztelési bérlőn lehetőleg kell tenni. További információkért lásd: [hogyan kell új házirendet telepít](active-directory-conditional-access-best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>További lépések

Ha azt szeretné, további információ a feltételes hozzáférési, lásd: [Mi az, hogy a feltételes hozzáférés az Azure Active Directoryban?](active-directory-conditional-access-azure-portal.md)
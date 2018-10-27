---
title: Az Azure AD Join és az Azure Active Directory Domain Services összehasonlítása |} A Microsoft Docs
description: Érdemesebb Azure AD-csatlakozás, Azure AD tartományi szolgáltatások
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: ergreenl
ms.openlocfilehash: 275f537511f721ea3f01c22a962462bab458129c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156579"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Válasszon az Azure Active Directory join és az Azure Active Directory Domain Services
Ez a cikk ismerteti az Azure Active Directory (AD) való csatlakozás és az Azure AD tartományi szolgáltatások és a választja, a használati esetek alapján segít közötti különbségeket.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Az Azure ad-ben regisztrált és az Azure AD-hez csatlakoztatott eszközök
Az Azure AD lehetővé teszi az eszközök a vállalati erőforrásokhoz a szervezet és a hozzáférés által használt eszközök identitásának felügyeletét. Felhasználók a személyes eszközüket (bring-your-own) regisztrálhatnak az Azure ad-vel, amely az eszköz egy identitást biztosít. Ezt követően az Azure AD képes hitelesíteni az eszközt, ha egy felhasználó bejelentkezik az Azure AD és az eszközt használja a védett erőforrások eléréséhez. További az eszköz, például a Microsoft Intune mobileszköz-felügyeleti (MDM) szoftver használata kezelheti. Ez a funkció lehetővé teszi, hogy a házirend-szabályzatoknak megfelelő felügyelt eszközök bizalmas erőforrásokhoz való hozzáférés korlátozása.

A szervezet tulajdonában lévő eszközök Azure ad-hez is csatlakozhat. Ezt a mechanizmust, egy saját eszköz regisztrálása az Azure AD ugyanazokat az előnyöket kínál. Ezenkívül felhasználók is bejelentkeznek az eszköz vállalati hitelesítői adataikkal. Az Azure AD-csatlakoztatott eszközök a következő előnyöket biztosítanak:
* Egyszeri bejelentkezéses (SSO) az Azure AD által védett alkalmazások
* Vállalati megfelelnek központi felhasználói beállítások az eszközök között.
* Hozzáférés a Windows Store vállalatoknak a vállalati hitelesítő adatok használatával.
* Vállalati Windows Hello
* Korlátozott alkalmazásokhoz és erőforrásokhoz való hozzáférés a vállalati házirenddel való megfelelő eszközökről.

| **Eszköz típusa** | **Eszközplatformok** | **Mechanizmus** |
|:---| --- | --- |
| Személyes eszközök | A Windows 10, iOS, Android, Mac OS | Az Azure AD-ban regisztrálva |
| A szervezet tulajdonában lévő eszköz nem csatlakozik a helyszíni AD | Windows 10 | Azure AD-hez |
| A szervezet tulajdonában lévő eszköz csatlakozik egy helyszíni AD | Windows 10 | Hibrid Azure AD-hez |

Az Azure AD-tartományhoz kell csatlakoznia, vagy a regisztrált eszköz, felhasználói hitelesítés történik, a modern, OAuth vagy OpenID Connect-alapú protokollok használatával. Ezeket a protokollokat tervezték, hogy az interneten keresztül működik, és elérhetik a vállalati erőforrásokhoz bárhonnan, a mobil forgatókönyveknél kitűnően alkalmasak.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Az Azure AD Domain Services felügyelt tartományokhoz való csatlakozás tartományhoz
Az Azure AD tartományi szolgáltatásokat biztosít egy felügyelt AD-tartomány az Azure-beli virtuális hálózathoz. Gépek csatlakozhat a felügyelt tartomány, hagyományos tartományhoz való csatlakozás mechanizmusok használatával. Windows ügyfél (Windows 7, Windows 10-es) és a Windows Server rendszerű gépek a felügyelt tartományhoz is csatlakoztatható. A felügyelt tartományhoz ezenkívül Mac OS és Linuxos gépek is csatlakozhat. Ha egy gép egy AD-tartományhoz csatlakozik, felhasználók jelentkezhetnek be a vállalati hitelesítői adataikkal géphez. Ezek a gépek kezelhetők, Csoportházirend használata, így betartása a szervezet biztonsági házirendjében.

A tartományhoz csatlakozó a felhasználói hitelesítés NTLM vagy Kerberos-hitelesítési protokollok használatával történik. A tartományhoz csatlakoztatott gép üzemel, a felügyelt tartomány felhasználói hitelesítés működéséhez a tartományvezérlőn kell. Ezért a tartományhoz csatlakoztatott gépen kell lennie a felügyelt tartomány ugyanazon virtuális hálózaton. Azt is megteheti a tartományhoz csatlakoztatott gép igényeinek megfelelően a felügyelt tartományhoz kell csatlakoznia, vagy site-to-site VPN/ExpressRoute kapcsolaton keresztül egy társított virtuális hálózaton keresztül. Ezért ezt a mechanizmust nem egy kiválóan alkalmas a mobileszközökkel dolgozik, vagy a vállalati hálózaton kívülről származó erőforrások eléréséhez.

> [!NOTE]
> Technikailag lehetőség a helyszíni ügyfél munkaállomásról csatlakoztatása a felügyelt tartomány egy helyek közötti VPN vagy ExpressRoute-kapcsolaton keresztül. Azonban Határozottan javasoljuk, használjon a végfelhasználói eszközökön vagy az eszköz regisztrálása az Azure ad-vel (személyes eszközök esetén) vagy az eszköz csatlakoztatása az Azure AD (céges eszközök esetén). Ez a mechanizmus az interneten keresztül jobban működik, és a felhasználók bárhonnan dolgozhatnak. Az Azure AD Domain Services nagyszerű, Windows vagy Linux-kiszolgáló virtuális gépek az Azure virtuális hálózatok, az alkalmazások telepítését, amelyre telepítve.


## <a name="summary---key-differences"></a>Összegzés – különbségeket
| **Aspektus** | **Azure AD-csatlakozás** | **Az Azure AD tartományi szolgáltatások** |
|:---| --- | --- |
| Az eszköz által vezérelt | Azure AD | Az Azure AD tartományi szolgáltatásokkal felügyelt tartományban |
| A címtárban ábrázolás | Eszközobjektumok az Azure AD-címtárban. | Számítógép-objektumok az AAD-DS-ben a felügyelt tartományban. |
| Hitelesítés | OAuth és OpenID Connect-alapú protokollok | A Kerberos, NTLM-protokollok |
| Kezelés | Az Intune mobileszköz-felügyeleti (MDM) szoftver | Csoportházirend |
| Hálózat | Az interneten keresztül működik | Szükséges a gépet a felügyelt tartomány ugyanazon virtuális hálózaton.|
| Ideális választás... | Végfelhasználói mobil- vagy asztali eszközök | Server virtual machines gépek az Azure-ban |


## <a name="next-steps"></a>További lépések
### <a name="learn-more-about-azure-ad-domain-services"></a>További információ az Azure AD tartományi szolgáltatások
* [Az Azure AD Domain Services áttekintése](active-directory-ds-overview.md)
* [Szolgáltatások](active-directory-ds-features.md)
* [Központi telepítési forgatókönyvei](active-directory-ds-scenarios.md)
* [Ismerje meg, ha az Azure AD Domain Services megfelelő-e a használati esetek](active-directory-ds-comparison.md)
* [Megismerheti, hogyan szinkronizálja az Azure AD tartományi szolgáltatásokat az Azure AD-címtár](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Tudjon meg többet az Azure AD-csatlakozás
* [Az Azure Active Directory eszközkezelésének alapjai](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Ismerkedés az Azure AD tartományi szolgáltatásokkal
* [Az Azure Portalon az Azure AD tartományi szolgáltatások engedélyezése](active-directory-ds-getting-started.md)

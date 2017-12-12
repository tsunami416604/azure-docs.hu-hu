---
title: "Az Azure AD Join és az Azure Active Directory tartományi szolgáltatások összehasonlítása |} Microsoft Docs"
description: "Az Azure AD Join és az Azure AD tartományi szolgáltatások érdemesebb"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: fb28c7a66d26119444dd720c2e134f2818d46cfc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Válasszon az Azure Active Directory join és az Azure Active Directory tartományi szolgáltatások
Ez a cikk ismerteti az Azure Active Directory (AD) join és az Azure AD tartományi szolgáltatások és a választja, a használati esetek alapján segítségével közötti különbséget.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Regisztrálva az Azure AD és az Azure AD csatlakoztatott eszközök
Az Azure AD lehetővé teszi ezeknek az eszközöknek a vállalati erőforrások a szervezet és a vezérlés hozzáférési által használt eszközök identitásának felügyeletét. A felhasználók regisztrálhatják személyes (bring-a-saját) eszközüket az Azure ad szolgáltatással, amely egy identitással látja el az eszközt. Ezt követően az Azure AD képes hitelesíteni az eszközt, ha a felhasználó bejelentkezik az Azure AD és az eszköz használ a védett erőforrások eléréséhez. További az eszköz, például a Microsoft Intune mobileszköz-felügyeleti (MDM) szoftver használatával kezelheti. Ez a funkció lehetővé teszi a bizalmas erőforrások elérésének korlátozása a házirend-szabályzatoknak megfelelő kezelt eszközök.

A vállalat tulajdonában lévő eszközök, az Azure AD is csatlakozhat. A mechanizmus előnyökkel, egy saját eszköz regisztrálása az Azure ad-val. Emellett felhasználók is jelentkeznek be az eszközre a vállalati hitelesítő adataival. Az Azure AD csatlakoztatott eszközök a következő előnyöket biztosítják:
* Egyszeri bejelentkezéses (SSO) az Azure AD által védett alkalmazások
* Vállalati szabályzat előírásainak megfelelő központi felhasználói beállítások az eszközön.
* A Windows áruház hozzáférését a vállalati vállalati hitelesítő adataival.
* A vállalati Windows Hello
* Korlátozott hozzáférés az alkalmazásokhoz és erőforrásokhoz a vállalati házirendnek megfelelő eszközökről.

| **Eszköz típusa** | **Eszközök** | **Mechanizmus** |
|:---| --- | --- |
| Személyes eszközök | Windows 10-, iOS, Android-, Mac OS | Regisztrálva az Azure AD |
| Vállalat tulajdonban lévő eszköz nincs tartományhoz csatlakoztatva a helyszíni AD | Windows 10 | Az Azure AD-tartományhoz |
| Vállalat tulajdonban lévő eszköz csatlakozik egy helyszíni AD | Windows 10 | Az Azure AD hibrid csatlakoztatva |

Az Azure AD tartományhoz, illetve a regisztrált eszközhöz, a felhasználó hitelesítése történik, modern OAuth/OpenID Connect-alapú protokollok használatával. Ezeket a protokollokat tervezték, hogy az interneten keresztül működik, és érhetik el a vállalati erőforrásokhoz bárhonnan mobil helyzetekben ideális.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Tartományhoz való csatlakozást az Azure AD tartományi szolgáltatások felügyelt tartományokhoz
Azure AD tartományi szolgáltatások biztosít a felügyelt AD-tartomány egy Azure virtuális hálózatban. A felügyelt tartományra hagyományos tartományhoz való csatlakozást mechanizmusok használatával csatlakozhat a gépek. A Windows ügyfél (Windows 7, Windows 10) és a Windows Server-gépek társíthatók a felügyelt tartományra. A felügyelt tartományra, Linux és Mac OS gépek is csatlakozhat. Amikor olyan gép csatlakoztatása az AD-tartomány, felhasználók is jelentkezzen be a gépet, a vállalati hitelesítő adatok használatával. Ezek a gépek csoportházirend segítségével, így a szervezete biztonsági házirendek megfelelőség kényszerítése is felügyelhetők.

A tartományhoz csatlakoztatott számítógépeken a felhasználói hitelesítés NTLM vagy Kerberos hitelesítési protokoll használatával történik. A tartományhoz gép sor a láthatáron a tartományvezérlőn, a felügyelt tartomány felhasználói hitelesítés működéséhez szükséges. Ezért a tartományhoz gép kell lennie a felügyelt tartományra azonos virtuális hálózaton. Alternatív megoldásként a tartományhoz gép peered virtuális hálózaton keresztül, vagy egy pont-pont VPN/ExpressRoute-kapcsolaton keresztül a felügyelt tartományra kapcsolódnia kell. Ebből kifolyólag a mechanizmus nem egy nagyszerű beválik, ha az eszközök, amelyek a mobil, vagy az erőforrásokhoz a vállalati hálózaton kívülről.

> [!NOTE]
> Technikai szempontból is lehet csatlakozni a helyszíni ügyfél-munkaállomás a felügyelt tartományra pont-pont származó VPN- vagy ExpressRoute-kapcsolaton keresztül. Azonban erősen ajánlott végfelhasználói eszközökhöz az eszközt regisztrálni kell az Azure ad-val (személyes eszközök) vagy az eszköz csatlakoztatása az Azure AD (vállalati eszközök). A mechanizmus az interneten keresztül jobban működik, és lehetővé teszi, hogy a végfelhasználók számára, hogy bárhol dolgozhassanak. Azure AD tartományi szolgáltatások kiváló Windows vagy Linux-kiszolgáló virtuális gépek telepítése az Azure virtuális hálózataihoz, amelyen az alkalmazások vannak telepítve.


## <a name="summary---key-differences"></a>Összefoglalás – főbb változásai
| **Aspektusa** | **Azure AD-csatlakozás** | **Azure AD tartományi szolgáltatások** |
|:---| --- | --- |
| Az eszköz által vezérelt | Azure AD | Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz |
| A címtárban megjelenítésre | Az Azure AD-címtár eszköz objektumok. | Az AAD-felügyelt tartományi szolgáltatások számítógép-objektumához. |
| Hitelesítés | OAuth/OpenID Connect-alapú protokollok | A Kerberos, az NTLM-protokollok |
| Kezelés | Például az Intune mobileszköz-felügyeleti (MDM) szoftver | Csoportházirend |
| Hálózat | Az interneten keresztül működik | Igényli, hogy a felügyelt tartományra azonos virtuális hálózaton kell gépek.|
| A nagy... | Végfelhasználói hordozható vagy asztali eszközök | Az Azure-ban telepített kiszolgáló virtuális gépek |


## <a name="next-steps"></a>Következő lépések
### <a name="learn-more-about-azure-ad-domain-services"></a>További tudnivalók az Azure AD tartományi szolgáltatások
* [Az Azure AD tartományi szolgáltatások áttekintése](active-directory-ds-overview.md)
* [Szolgáltatások](active-directory-ds-features.md)
* [Központi telepítési forgatókönyvek](active-directory-ds-scenarios.md)
* [Annak megállapítása, ha Azure AD tartományi szolgáltatások megfelel-e a használati esetek](active-directory-ds-comparison.md)
* [Megérteni, hogyan szinkronizálja az Azure AD tartományi szolgáltatásokat az Azure AD-címtár](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>További tudnivalók az Azure AD Join
* [Kezelés az Azure Active Directoryban bemutatása](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Ismerkedés az Azure AD tartományi szolgáltatásokkal
* [Az Azure portál használatával Azure AD tartományi szolgáltatások engedélyezése](active-directory-ds-getting-started.md)

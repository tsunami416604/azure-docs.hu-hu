---
title: Kezelés az Azure Active Directoryban bemutatása |} Microsoft Docs
description: Ismerje meg, hogyan Eszközkezelés segítségével tehet szert az erőforrásoknak a környezetben elérő eszközök vezérlését.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 96de05eb8ebae85b73eaa012efdfb38ac074abf8
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110920"
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Kezelés az Azure Active Directoryban bemutatása

Mobileszköz-first, a felhő-első világában Azure Active Directory (Azure AD) lehetővé teszi, hogy az egyszeri bejelentkezés eszközök, alkalmazások és szolgáltatások bárhonnan. Az eszközök - kapcsolja a saját eszközök használata (BYOD), beleértve a elterjedése informatikai szakemberek számára két másik célt szemben:

- A végfelhasználók számára, hogy termelékenyebben legyenek a tetszőleges helyről és időben ellenőrizniük
- A vállalati eszközök védelme bármikor

Eszközön a felhasználók kihozhatják hozzáférés a vállalati eszközöket. A vállalati eszközök védelme informatikai rendszergazdaként, szeretné befolyásolni ezeket az eszközöket. Ez lehetővé teszi, hogy győződjön meg arról, hogy a felhasználók a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről érnek el az erőforrásokat. 

Eszközkezelés egyben a alapját [eszközalapú feltételes hozzáférési](active-directory-conditional-access-policy-connected-applications.md). Az eszközalapú feltételes hozzáférés biztosítható, hogy az erőforrásoknak a környezetben való hozzáférés csak felügyelt eszközökkel való lehetőség.   

Ez a cikk ismerteti, hogyan működik a kezelés az Azure Active Directoryban.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Az Azure AD felügyelete alatt eszközök

Ahhoz, hogy egy eszköz, az Azure AD felügyelete alatt, két lehetősége van:

- Regisztrálása 
- Csatlakozás

**Regisztrálás** egy eszközt az Azure AD lehetővé teszi egy eszköz identitás kezelése. Amikor regisztrál egy eszközt, az Azure AD eszközregisztrációval látja el az eszközt, amellyel hitelesíteni az eszközt, ha egy felhasználó bejelentkezik az Azure AD-identitással. Az identitás segítségével engedélyezheti vagy tilthatja le egy eszközt.

Például a Microsoft Intune mobileszköz-lévő eszközattribútumok megoldás együtt, az eszközattribútumokon az Azure AD frissítődik az eszközzel kapcsolatos további információk. Ez lehetővé teszi további feltételes hozzáférési szabályok létrehozását, amelyek arra kényszerítik az eszközhozzáféréseket, hogy megfeleljenek a biztonsági és megfelelőségi szabványoknak. A Microsoft Intune-beli regisztrálásának eszközökön további információkért lásd: eszközök regisztrálása felügyeletre a Intune-ban.

**Csatlakozás** egy eszköz bővítménye, egy eszköz regisztrálása. Ez azt jelenti, az eszköz regisztrálása az összes előnyt biztosít, és továbbá a is helyi állapotát módosítja az eszköz. A helyi állapotváltás lehetővé teszi, hogy a felhasználókat, hogy jelentkezzen be egy eszközt egy szervezeti munkahelyi vagy iskolai fiókot egy személyes fiók helyett.

## <a name="azure-ad-registered-devices"></a>Az Azure AD regisztrált eszközök   

Az Azure AD regisztrált eszközök célja támogatásával biztosítja, hogy a **kapcsolja a saját eszközök használata (BYOD)** forgatókönyv. Ebben a forgatókönyvben egy felhasználó hozzáférhessen a szervezet Azure Active Directory szabályozott erőforrások egy személyes eszközt használ.  

![Az Azure AD regisztrált eszközök](./media/device-management-introduction/03.png)

A hozzáférés a munkahelyi vagy iskolai fiókkal az eszközön beírt alapul.  
Windows 10 például lehetővé teszi a felhasználók munkahelyi vagy iskolai fiók hozzáadása a személyi számítógép, táblagép vagy telefon.  
Amikor a felhasználó hozzáadta a munkahelyi vagy iskolai fiókkal, az eszköz regisztrálva az Azure ad-vel és opcionálisan a szervezet van-e beállítva mobileszköz-felügyelet (MDM) rendszerben regisztrált. A szervezet felhasználók adja hozzá a munkahelyi vagy iskolai kényelmesen a személyes eszközökre fiókot:

- A munkahelyi alkalmazás első alkalommal való hozzáféréskor
- Kézzel a **beállítások** menü Windows 10 esetén 

Az Azure AD regisztrált eszközöket a Windows 10, iOS, Android és macOS konfigurálhatja.

## <a name="azure-ad-joined-devices"></a>Az Azure AD csatlakoztatott eszközök

Az Azure AD csatlakoztatott eszközök célja leegyszerűsítése érdekében:

- A munkahelyi eszközök a Windows központi telepítése 
- A szervezeti alkalmazások és erőforrások Windows-eszköz való hozzáférést
- Felhőalapú felügyelet a munkahelyi eszközök

![Az Azure AD regisztrált eszközök](./media/device-management-introduction/02.png)

Az Azure AD Join is telepíthető a következő módszerek egyikével sem: 
 - [Windows automata](https://docs.microsoft.com/en-us/windows/deployment/windows-autopilot/windows-10-autopilot)
 - [Tömeges központi telepítés](https://docs.microsoft.com/en-us/intune/windows-bulk-enroll)
 - [Az önkiszolgáló felhasználói élmény](device-management-azuread-joined-devices-frx.md) 

**Az Azure AD Join** célja a szervezeteknek, amelyek szeretnék felhő-első (Ez azt jelenti, hogy elsősorban felhőalapú szolgáltatásokat használ, a célja, hogy egy helyszíni infrastruktúra-felhasználás csökkentése) vagy csak felhőalapú (a helyszíni infrastruktúra nélkül). Nincsenek a méretét vagy a szervezeteknek, amelyek telepítheti az Azure AD Join típusú korlátozások. Az Azure AD Join works is jól hibrid környezetben, a felhőalapú és a helyszíni alkalmazásokhoz és erőforrásokhoz való hozzáférés engedélyezése.

Az Azure AD csatlakoztatott eszközök végrehajtási tesz lehetővé a következő előnyöket biztosítja:

- **Egyszeri bejelentkezéses (SSO)** az Azure által kezelt SaaS-alkalmazásokhoz és szolgáltatásokhoz. Nem jelennek meg a felhasználók további hitelesítési kérések munkahelyi erőforrásokhoz való hozzáféréskor. Az egyszeri bejelentkezés funkcióra van, akkor is, ha azok nem érhető el a tartományi hálózathoz csatlakoznak.

- **Vállalati kompatibilis központi** a felhasználói beállítások csatlakoztatott eszközön. Csatlakozás a Microsoft-fiókkal (például Hotmail) eszközön beállítások megtekintéséhez nem kell.

- **A vállalati Windows áruház elérésének** használata az Azure AD-fiókot. A felhasználók a szervezet által előre kiválasztott alkalmazások leltárt közül választhatnak.

- **A Windows Hello** erőforrásokhoz való biztonságosabb és kényelmesebb hozzáférés támogatása.

- **Hozzáférés korlátozása** alkalmazások csak olyan eszközökön, amelyek megfelelnek a megfelelőségi szabályzatnak.

- **A helyszíni erőforrásokhoz való zavartalan hozzáférés** Ha az eszköz rendelkezik-e sor a láthatáron a helyi tartományvezérlőre. 


Az Azure AD join elsődlegesen a helyi Windows Server Active Directory infrastruktúrával nem rendelkező szervezeteknek, amíg biztosan használata forgatókönyvekben ahol:

- Az Azure AD felhőalapú infrastruktúra átmenet kívánt és MDM például Intune-ra.

- Nem használhat egy helyszíni tartományhoz való csatlakozást, például, ha le kell töltenie a mobil eszközök, például táblagépekről és mobiltelefonokról vezérlése alatt.

- A felhasználók elsősorban Office 365- vagy más SaaS-alkalmazásokhoz az Azure ad-vel integrált eléréséhez szükséges.

- Az Azure AD ahelyett, hogy az Active Directory a felhasználók egy csoportjánál kezelni kívánt. Ez is vonatkozik, például határozza munkavállalók, alvállalkozói és a diákok.

- Szeretne távoli fiókirodák munkavállalók csatlakozó képességek biztosítása a korlátozott a helyszíni infrastruktúrával.

Beállíthatja, hogy csatlakozott az Azure AD-eszközök Windows 10 rendszerű eszközökhöz.


## <a name="hybrid-azure-ad-joined-devices"></a>Az Azure AD hibrid csatlakoztatott eszközök

Több mint egy évtizedben számos szervezet rendelkezik használni a tartományhoz való csatlakozást, a helyszíni Active Directoryban való engedélyezéséhez:

- INFORMATIKAI részlegek munkahelyi tulajdonban lévő eszközök kezelésére egy központi helyről.

- Jelentkezzen be az eszközét a következővel a Active Directory a felhasználók munkahelyi vagy iskolai fiókok. 

Általában egy helyszíni tárhely szervezetek támaszkodnak imaging módszerek ellátja az eszközöket, és gyakran használják **System Center Configuration Manager (SCCM)** vagy **csoportházirend (GP)** kezelése őket.

Ha a környezetben egy helyszíni AD kockázatokat, és azt is szeretné az Azure Active Directory által biztosított képességek hasznos, hibrid csatlakozott az Azure AD-eszközöket is létrehozható. Ezek a nyomtatók is, a helyszíni Active Directory és az Azure Active Directory tartományhoz.

![Az Azure AD regisztrált eszközök](./media/device-management-introduction/01.png)


Az Azure AD hibrid csatlakoztatott eszközöket kell használnia, ha:

- Win32 alkalmazásokat értünk ezekre az eszközökre, a számítógép Active Directory-hitelesítés használó telepítve van.

- Eszközkezelés a csoportházirend van szüksége.

- Biztosan folytatja a lemezkép-készítési megoldások segítségével konfigurálhatja az eszközöket az alkalmazottak számára.

Konfigurálhatja a Hybrid Azure AD csatlakoztatott eszközök a Windows 10-es és régebbi eszközök, például a Windows 8 és Windows 7.

## <a name="summary"></a>Összegzés

Az eszköz kezelése az Azure ad-ben a következőket teheti: 

- Az Azure AD felügyelete alatt eszközükkel egyszerűsíthető

- A vállalat felhőalapú erőforrások egy könnyen használható hozzáférést biztosítania a felhasználók

A görgetőgomb szabályként kell használnia:

- Az Azure AD regisztrált eszközökön:

    - Személyes eszközök 

    - A manuális regisztrálják az eszközeiket az Azure ad szolgáltatással

- Az Azure AD csatlakoztatott eszközök: 

    - A szervezete tulajdonában lévő eszközök

    - Az eszközök, amelyek **nem** csatlakozik egy helyszíni AD

    - A manuális regisztrálják az eszközeiket az Azure ad szolgáltatással

    - Az eszköz a helyi állapotának módosítása

- Az Azure AD hibrid csatlakoztatott eszközökhöz, egy helyszíni tartományhoz csatlakoztatott eszközök AD     

    - A szervezete tulajdonában lévő eszközök

    - Egy helyszíni tartományhoz csatlakoztatott eszközök AD

    - Az automatikusan regisztrálják az eszközeiket az Azure ad szolgáltatással

    - Az eszköz a helyi állapotának módosítása



## <a name="next-steps"></a>További lépések

- Áttekintést az Azure portálon eszközök felügyelete, lásd: [az Azure portál használatával eszközök kezelése](device-management-azure-portal.md)

- Eszközalapú feltételes hozzáférési kapcsolatos további információkért lásd: [Azure Active Directory eszközalapú feltételes hozzáférési házirendek konfigurálása](active-directory-conditional-access-policy-connected-applications.md).

- Telepítés:
    - Az Azure Active Directory regisztrált Windows 10-eszközök című [konfigurálása az Azure Active Directory regisztrált Windows 10-eszközök](device-management-azuread-registered-devices-windows10-setup.md)
    - Az Azure Active Directoryhoz csatlakoztatott eszközök című [konfigurálása az Azure Active Directoryhoz csatlakoztatott eszközök](device-management-azuread-joined-devices-setup.md)
    - Hibrid csatlakozott az Azure AD-eszközök esetén lásd: [hogyan hibrid konfigurálása az Azure Active Directoryhoz csatlakoztatott eszközök](device-management-hybrid-azuread-joined-devices-setup.md).



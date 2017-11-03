---
title: "Kezelés az Azure Active Directoryban bemutatása |} Microsoft Docs"
description: "Ismerje meg, hogyan Eszközkezelés segítségével tehet szert az erőforrásoknak a környezetben elérő eszközök vezérlését."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: fdc597c9e88e932eace5962c79af4dc3805685a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Kezelés az Azure Active Directoryban bemutatása

Mobileszköz-first, a felhő-első világában Azure Active Directory (Azure AD) lehetővé teszi, hogy az egyszeri bejelentkezés eszközök, alkalmazások és szolgáltatások bárhonnan. Az eszközök - kapcsolja a saját eszközök használata (BYOD), beleértve a elterjedése informatikai szakemberek számára két másik célt szemben:

- A végfelhasználók számára, hogy termelékenyebben legyenek a tetszőleges helyről és időben ellenőrizniük
- A vállalati eszközök védelme bármikor

Eszközön a felhasználók kihozhatják hozzáférés a vállalati eszközöket. A vállalati eszközök védelme informatikai rendszergazdaként, szeretné befolyásolni ezeket az eszközöket. Ez lehetővé teszi, hogy győződjön meg arról, hogy a felhasználók a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről érnek el az erőforrásokat. 

Eszközkezelés egyben a alapját [eszközalapú feltételes hozzáférési](active-directory-conditional-access-policy-connected-applications.md). Eszközalapú feltételes hozzáféréssel biztosíthatja, hogy az erőforrásoknak a környezetben való hozzáférés csak megbízható eszközökkel való lehetőség.   

Ez a témakör ismerteti, hogyan működik a kezelés az Azure Active Directoryban.

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

![Az Azure AD regisztrált eszközök](./media/device-management-introduction/02.png)


Ezen célok lehet elvégezni a felhasználók egy önkiszolgáló felhasználói élmény biztosítása számára a munkahelyi által birtokolt eszközök az Azure AD felügyelete alatt.  
**Az Azure AD Join** , amelyek a felhő-első / csak felhőalapú szervezetek számára készült. Ezek rendszerint azok a helyszíni Windows Server Active Directory infrastruktúrával nem rendelkező kis - és közepes méretű vállalkozások. 

Az Azure AD csatlakoztatott eszközök végrehajtási tesz lehetővé a következő előnyöket biztosítja:

- **Egyszeri bejelentkezéses (SSO)** az Azure által kezelt SaaS-alkalmazásokhoz és szolgáltatásokhoz. Nem jelennek meg a felhasználók további hitelesítési kérések munkahelyi erőforrásokhoz való hozzáféréskor. Az egyszeri bejelentkezés funkcióra van, akkor is, ha azok nem érhető el a tartományi hálózathoz csatlakoznak.

- **Vállalati kompatibilis központi** a felhasználói beállítások csatlakoztatott eszközön. Csatlakozás a Microsoft-fiókkal (például Hotmail) eszközön beállítások megtekintéséhez nem kell.

- **A vállalati Windows áruház elérésének** AD fiók használatával. A felhasználók a szervezet által előre kiválasztott alkalmazások leltárt közül választhatnak.

- **A Windows Hello** erőforrásokhoz való biztonságosabb és kényelmesebb hozzáférés támogatása.

- **Hozzáférés korlátozása** alkalmazások csak olyan eszközökön, amelyek megfelelnek a megfelelőségi szabályzatnak.

Az Azure AD join elsődlegesen a helyi Windows Server Active Directory infrastruktúrával nem rendelkező szervezeteknek, amíg is alapértékekkel is helyzetekben használhatja, ahol:

- Nem használhat egy helyszíni tartományhoz való csatlakozást, például, ha le kell töltenie a mobil eszközök, például táblagépekről és mobiltelefonokról vezérlése alatt.

- A felhasználók elsősorban Office 365- vagy más SaaS-alkalmazásokhoz az Azure ad-vel integrált eléréséhez szükséges.

- Az Azure AD ahelyett, hogy az Active Directory a felhasználók egy csoportjánál kezelni kívánt. Ez is vonatkozik, például határozza munkavállalók, alvállalkozói és a diákok.

- Szeretne távoli fiókirodák munkavállalók csatlakozó képességek biztosítása a korlátozott a helyszíni infrastruktúrával.

Beállíthatja, hogy csatlakozott az Azure AD-eszközök Windows 10 rendszerű eszközökhöz.


## <a name="hybrid-azure-ad-joined-devices"></a>Az Azure AD hibrid csatlakoztatott eszközök

Több mint egy évtizedben számos szervezet rendelkezik használni a tartományhoz való csatlakozást, a helyszíni Active Directoryban való engedélyezéséhez:

- INFORMATIKAI részlegek munkahelyi tulajdonban lévő eszközök kezelésére egy központi helyről.

- Jelentkezzen be az eszközét a következővel a Active Directory a felhasználók munkahelyi vagy iskolai fiókok. 

Általában egy helyszíni tárhely szervezetek támaszkodnak imaging módszerek ellátja az eszközöket, és gyakran használják **System Center Configuration Manager (SCCM)** vagy **csoportházirend (GP)** kezelhetők.

Ha a környezetben egy helyszíni AD kockázatokat, és azt is szeretné az Azure Active Directory által biztosított képességek hasznos, hibrid csatlakozott az Azure AD-eszközöket is létrehozható. Ezek a nyomtatók is, a helyszíni Active Directory és az Azure Active Directory tartományhoz.

![Az Azure AD regisztrált eszközök](./media/device-management-introduction/01.png)


Az Azure AD hibrid csatlakoztatott eszközöket kell használnia, ha:

- Rendelkezik Win32-alkalmazások telepítését ezekre az eszközökre, amelyek NTLM / Kerberos.

- Szüksége van a csoportházirend vagy SCCM / DCM eszközök kezelésére.

- Biztosan folytatja a lemezkép-készítési megoldások segítségével konfigurálhatja az eszközöket az alkalmazottak számára.

Konfigurálhatja a Hybrid Azure AD csatlakoztatott eszközök a Windows 10-es és régebbi eszközök, például a Windows 8 és Windows 7.

## <a name="summary"></a>Összefoglalás

Az eszköz kezelése az Azure ad-ben a következőket teheti: 

- Az Azure AD felügyelete alatt eszközükkel egyszerűsíthető

- A vállalat felhőalapú erőforrások egy könnyen használható hozzáférést biztosítania a felhasználók

A görgetőgomb szabályként kell használnia:

- Az Azure AD regisztrált eszközök személyes eszközök

- Az Azure AD csatlakoztatott eszközök olyan eszközök esetében, amelyek nincsenek csatlakoztatva egy helyszíni AD 

- Az Azure AD hibrid csatlakoztatott eszközökhöz, egy helyszíni tartományhoz csatlakoztatott eszközök AD     




## <a name="next-steps"></a>Következő lépések

- Áttekintést az Azure portálon eszközök felügyelete, lásd: [az Azure portál használatával eszközök kezelése](device-management-azure-portal.md)

- Eszközalapú feltételes hozzáférési kapcsolatos további információkért lásd: [Azure Active Directory eszközalapú feltételes hozzáférési házirendek konfigurálása](active-directory-conditional-access-policy-connected-applications.md).

- Telepítés:
    - Az Azure Active Directory regisztrált Windows 10-eszközök című [konfigurálása az Azure Active Directory regisztrált Windows 10-eszközök](device-management-azuread-registered-devices-windows10-setup.md)
    - Az Azure Active Directoryhoz csatlakoztatott eszközök című [konfigurálása az Azure Active Directoryhoz csatlakoztatott eszközök](device-management-azuread-joined-devices-setup.md)
    - Hibrid csatlakozott az Azure AD-eszközök esetén lásd: [hogyan hibrid konfigurálása az Azure Active Directoryhoz csatlakoztatott eszközök](device-management-hybrid-azuread-joined-devices-setup.md).



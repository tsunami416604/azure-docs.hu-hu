---
title: Az Eszközfelügyelet az Azure Active Directory – bevezetés |} A Microsoft Docs
description: Ismerje meg, hogyan Eszközfelügyelet segítségével is szabályozhatja az eszközöket, amelyek a környezetében erőforrásokat érnek el.
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
ms.openlocfilehash: 7e71ff5f5da17165d5127f3547d8eb0756411b8c
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597693"
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Az Eszközfelügyelet az Azure Active Directory bemutatása

Mobileszközök és a felhő-és felhőközpontú világában Azure Active Directory (Azure AD) lehetővé teszi, hogy egyszeri bejelentkezéshez az eszközök, alkalmazások és szolgáltatások bárhonnan. Eszközök – beleértve a Bring Your Own Device (BYOD), elterjedése informatikai szakemberek számára két ellentétes céljait néz szembe:

- Biztosítson hatékony eszközöket hatékonyan dolgozzanak, bárhol is legyenek, és minden alkalommal, amikor a végfelhasználók számára
- A vállalati eszközök védelme bármikor

Eszközön a felhasználók egyre hozzáférés a vállalati eszközöket. A vállalati eszközök védelme informatikai rendszergazdaként, érdemes szabályozhatják, ezeket az eszközöket. Ez lehetővé teszi, hogy győződjön meg arról, hogy a felhasználók az erőforrásokhoz hozzáférő eszközei megfeleljenek a biztonsági és megfelelőségi szabványoknak. 

Eszközkezelés egyben alapját [eszközalapú feltételes hozzáférési](../conditional-access/require-managed-devices.md). Eszközalapú feltételes hozzáféréssel biztosíthatja, hogy a környezetben lévő erőforrások elérése csak akkor lehetséges, a felügyelt eszközökkel.   

Ez a cikk ismerteti, hogyan Eszközfelügyelet az Azure Active Directoryban.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Az Azure AD vezérlése alá eszközeinek

Egy eszköz Azure AD vezérlése alá lekéréséhez két lehetősége van:

- Regisztrálása 
- Csatlakozás

**Regisztrálás** egy eszközt az Azure AD lehetővé teszi egy eszköz identitásának kezelése. Amikor regisztrál egy eszközt, az Azure AD eszközregisztrációval biztosít az eszközt, amellyel hitelesíteni az eszközt, ha egy felhasználó bejelentkezik az Azure AD-identitást az. Az identitás engedélyezése vagy letiltása az eszköz használható.

Ha például a Microsoft Intune mobileszköz-eszközattribútumok megoldás össze, az eszköz attribútumait az Azure ad-ben az eszköz további információival frissülnek. Ez lehetővé teszi további feltételes hozzáférési szabályok létrehozását, amelyek arra kényszerítik az eszközhozzáféréseket, hogy megfeleljenek a biztonsági és megfelelőségi szabványoknak. További információ az eszközök regisztrációját a Microsoft Intune-ban tekintse meg az eszközök regisztrálása felügyeletre az Intune-ban.

**Csatlakozás** eszköz az eszköz regisztrálásához ajánlatos a kiterjesztése. Ez azt jelenti, hogy azt az adott eszköz regisztrálásához ajánlatos előnyeit nyújtja, és emellett is megváltozik a helyi állapota. A helyi állapotának módosítása lehetővé teszi a felhasználóknak, hogy jelentkezzen be egy eszközt egy szervezeti munkahelyi vagy iskolai fiókot egy személyes fiók helyett.

## <a name="azure-ad-registered-devices"></a>Az Azure ad-ben regisztrált eszközök   

Az Azure ad-ben regisztrált eszközök célja, hogy a támogatást nyújtani a **Bring Your Own Device (BYOD)** forgatókönyv. Ebben a forgatókönyvben egy felhasználó hozzáférhessen a szervezet Azure Active Directory szabályozott erőforrások egy személyes eszköz használatával.  

![Az Azure ad-ben regisztrált eszközök](./media/overview/03.png)

A hozzáférést a munkahelyi vagy iskolai fiókkal az eszközön megadott alapul.  
Windows 10-es például lehetővé teszi a felhasználók munkahelyi vagy iskolai fiók hozzáadása egy személyi számítógép, táblagépen vagy telefonon.  
Amikor egy felhasználó hozzá van adva egy munkahelyi vagy iskolai fiókkal, az eszköz regisztrálva az Azure ad-ben, és igény szerint, amely a cége konfigurálta a mobileszköz-felügyelet (MDM) rendszerben regisztrált. A szervezet felhasználói adja hozzá a munkahelyi, akár kényelmesen iskolai fiókot egy személyes eszköz:

- Amikor első alkalommal a munkahelyi alkalmazásokhoz hozzáféréssel rendelkező
- Manuálisan keresztül a **beállítások** menüből a Windows 10 esetén 

Azure ad-ben regisztrált eszközök konfigurálhatók a Windows 10, iOS, Android és MacOS rendszeren.

## <a name="azure-ad-joined-devices"></a>Az Azure AD-csatlakoztatott eszközök

Az Azure AD-csatlakoztatott eszközök célja, hogy egyszerűsítse:

- Munkahelyi eszközök Windows központi telepítései 
- Hozzáférés a vállalati alkalmazásokhoz és erőforrásokhoz bármely Windows-eszközről
- Felhőalapú felügyelet munkahelyi által birtokolt eszközök

![Az Azure ad-ben regisztrált eszközök](./media/overview/02.png)

Az Azure AD Join is üzembe helyezhetők az alábbi módszerek egyikének használatával: 
 - [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
 - [Tömeges üzembe helyezés](https://docs.microsoft.com/intune/windows-bulk-enroll)
 - [Önkiszolgáló élményt](azuread-joined-devices-frx.md) 

**Az Azure AD Join** célja a szervezeteknek, amelyek szeretne felhőközpontú (azaz elsősorban az a cloud services, az a célja, hogy az a helyszíni infrastruktúra-felhasználás csökkentése) vagy csak felhőalapú (nem a helyszíni infrastruktúra). Nem vonatkoznak korlátozások a mérete vagy a szervezeteknek, amelyek üzembe helyezhet az Azure AD-csatlakozás típusa. Az Azure AD-csatlakozás működését is jól hibrid környezetben, felhőbeli és a helyszíni alkalmazásokhoz és erőforrásokhoz való hozzáférés engedélyezése.

Az Azure AD-csatlakoztatott eszközök végrehajtási nyújt a következő előnyökkel jár:

- **Egyszeri bejelentkezéses (SSO)** az Azure által felügyelt SaaS-alkalmazások és szolgáltatások. A felhasználók számára nem jelenik meg a további hitelesítési kérések munkahelyi erőforrásokhoz való hozzáféréskor. Az egyszeri bejelentkezési funkciója akkor is, ha nem érhető el a tartományi hálózathoz csatlakoznak.

- **A megfelelő központi vállalati** felhasználói beállítások között csatlakoztatott eszközökre. Felhasználóknak nem kell egy Microsoft-fiók csatlakoztatásához (például a Hotmail) beállítások megtekintéséhez a különböző eszközökön.

- **Windows Store vállalatoknak hozzáférést** egy Azure AD-fiók használatával. A felhasználók a szervezet által előre kiválasztott alkalmazások leltárát közül választhat.

- **Windows Hello** támogatja a biztonságos és kényelmes hozzáférést a munkahelyi erőforrásokhoz.

- **Hozzáférés korlátozása** az alkalmazások csak olyan eszközök, amelyek megfelelnek a megfelelőségi szabályzat.

- **A helyszíni erőforrásokhoz való zavartalan hozzáférés** mikor van-e az eszközön a helyi tartományvezérlőre üzemel. 


Bár az Azure AD-csatlakozás elsősorban egy helyszíni Windows Server Active Directory infrastruktúrával nem rendelkező szervezeteknek, természetesen használhat, a forgatókönyvek ahol:

- Szeretné való áttérés az Azure AD felhőalapú infrastruktúra és a mobileszköz-kezelési, mint például az Intune-ban.

- Nem használhat egy helyszíni tartományhoz való csatlakozás, például, ha le szeretné kérni a mobileszközökön, például táblagépekről és mobiltelefonokról irányítása alatt.

- A felhasználók elsősorban kell Office 365 vagy más Azure AD-vel integrált SaaS-alkalmazások eléréséhez.

- Az Active Directory helyett az Azure AD a felhasználók egy csoportjánál kezelni szeretne. Ez lehet alkalmazni, például idénymunkásoknak, alvállalkozók és diákok.

- Azt szeretné, csatlakozó képességeket biztosít a távoli fiókirodákban munkavállalók csak a helyszíni infrastruktúrával.

Az Azure AD-csatlakoztatott eszközök a Windows 10 rendszerű eszközökhöz konfigurálhat.


## <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-hez csatlakoztatott eszközök

Több mint egy évtizede nyújt védelmet, a számos szervezet használta a tartományhoz való csatlakozást, a helyszíni Active Directory engedélyezése:

- IT-részlegek számára a munkahelyi eszközök kezelését egy központi helyről.

- Jelentkezzen be az Active Directory-eszközeiket, hogy a felhasználók munkahelyi vagy iskolai fiókokat. 

Általában egy helyszíni erőforrás-igényű rendelkező szervezetek támaszkodjon imaging módszerek eszközök kiépítése, és gyakran **System Center Configuration Manager (SCCM)** vagy **csoportházirend (GP)** kezelése őket.

Ha a környezetben egy helyszíni AD erőforrás-igényű, és azt is szeretné profitál az Azure Active Directory által biztosított képességek, a hibrid Azure AD-csatlakoztatott eszközök valósíthat meg. Ezek a is, amelyek a helyszíni Active Directory és az Azure Active Directory tartományhoz.

![Az Azure ad-ben regisztrált eszközök](./media/overview/01.png)


Az Azure AD hibrid csatlakoztatott eszközöket kell használnia, ha:

- Ezek a gép Active Directory-hitelesítést használó eszközökre telepített Win32-alkalmazások rendelkezik.

- A csoportházirend-eszközök kezeléséhez szükséges.

- Továbbra is konfigurálhatja az eszközöket az alkalmazottak lemezkép-készítési megoldások használni szeretne.

Konfigurálhatja a hibrid Azure AD-hez csatlakoztatott eszközök a Windows 10-es és régebbi verziójú eszközök, például a Windows 8 és Windows 7.

## <a name="summary"></a>Összegzés

Az Eszközfelügyelet az Azure ad-ben a következőket teheti: 

- Egyszerűsítheti, hogy az eszközök felügyelete alatt, az Azure ad-ben

- Adja meg a felhasználók egy könnyen használható hozzáférést a szervezet felhőalapú erőforrások

Egy USB-szabály használjon:

- Az Azure ad-ben regisztrált eszközökön:

    - Személyes eszközök esetén 

    - Manuális eszközöket szeretne regisztrálni Azure ad-ben

- Az Azure AD-csatlakoztatott eszközök: 

    - A szervezet tulajdonában lévő eszközök esetében

    - Eszközök **nem** csatlakozik egy helyszíni AD

    - Manuális eszközöket szeretne regisztrálni Azure ad-ben

    - Az eszköz a helyi állapotának módosítása

- Hibrid Azure AD-hez csatlakoztatott eszközök helyszíni tartományhoz csatlakoztatott eszközök AD     

    - A szervezet tulajdonában lévő eszközök esetében

    - Egy helyszíni tartományhoz csatlakoztatott eszközök AD

    - Automatikusan eszközöket szeretne regisztrálni Azure ad-ben

    - Az eszköz a helyi állapotának módosítása



## <a name="next-steps"></a>További lépések

- Áttekintheti az Azure Portalon eszközök felügyelete, lásd: [az Eszközfelügyelet az Azure portal használatával](device-management-azure-portal.md)

- Eszközalapú feltételes hozzáférési kapcsolatos további információkért lásd: [Azure Active Directory eszközalapú feltételes hozzáférési szabályzatok konfigurálhatók](../conditional-access/require-managed-devices.md).

- A telepítő:
    - Az Azure Active Directory regisztrált Windows 10-es eszközök, lásd: [konfigurálása Azure Active Directoryban regisztrált Windows 10-eszközök](../user-help/device-management-azuread-registered-devices-windows10-setup.md)
    - Az Azure Active Directoryhoz csatlakoztatott eszközök, lásd: [konfigurálása Azure Active Directory-hez csatlakoztatott eszközök](../user-help/device-management-azuread-joined-devices-setup.md)
    - Hibrid Azure AD-hez csatlakoztatott eszközök esetén lásd: [a hibrid Azure Active Directory join megvalósítás tervezése](hybrid-azuread-join-plan.md).



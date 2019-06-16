---
title: Hibrid Azure Active Directory join megvalósítása az Azure Active Directoryban (Azure AD) tervezése |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directory-csatlakoztatott eszközöket.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a1c349ae7cdd66c09db412331c344d6512ecd3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110594"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>kézikönyv: A hibrid Azure Active Directory join megvalósítás megtervezése

Hasonló módon a felhasználó számára egy eszköz egy másik core identitás szeretné védeni, és az erőforrások védelmét, bármikor és bárhonnan. A cél oszloptárat és eszközidentitások kezelése az Azure ad-ben a következő módszerek egyikének használatával végezheti el:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az Azure AD-be való bevonással maximalizálható a felhasználók munkahatékonysága, köszönhetően az egyszeri bejelentkezésnek (SSO), amely a felhőbeli és a helyszíni erőforrásokhoz is hozzáférést nyújt. Egy időben, hozzáférés gondoskodhat a felhőbeli és helyszíni erőforrások [feltételes hozzáférési](../active-directory-conditional-access-azure-portal.md).

Ha a helyszíni Active Directory (AD) környezet és az AD-tartományhoz csatlakoztatott számítógépeket az Azure ad-hez csatlakozni szeretne, ez elvégezhető a hibrid Azure AD-csatlakozás végrehajtásával. Ez a cikk ismerteti, és a kapcsolódó lépéseket kell végrehajtani egy hibrid Azure AD join a környezetben. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a [eszköz Identitáskezelés az Azure Active Directoryban bemutatása](../device-management-introduction.md).

> [!NOTE]
> A szükséges minimális működési tartomány és erdő működési szintjét a Windows 10-es hibrid Azure AD-csatlakozás Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>A megvalósítás tervezése

A hibrid Azure AD-megvalósítás megtervezése, meg kell ismerkednie az:

|   |   |
| --- | --- |
| ![Jelölőnégyzet][1] | Tekintse át a támogatott eszközök |
| ![Jelölőnégyzet][1] | Felülvizsgálat tudnivalók |
| ![Jelölőnégyzet][1] | Tekintse át a hibrid Azure AD-csatlakozás vezérelt érvényesítése |
| ![Jelölőnégyzet][1] | Az Ön identitáskezelési infrastruktúráját alapuló forgatókönyv kiválasztása |
| ![Jelölőnégyzet][1] | Tekintse át a helyszíni AD UPN támogatják a hibrid Azure AD-csatlakozás |

## <a name="review-supported-devices"></a>Tekintse át a támogatott eszközök

Hibrid Azure AD-csatlakozás egy széles körű tartomány a Windows-eszközöket támogatja. A konfiguráció a Windows korábbi verzióit futtató eszközök esetében további vagy különböző lépéseket igényel, mert a támogatott eszközök két kategóriákba vannak csoportosítva:

### <a name="windows-current-devices"></a>Aktuális Windows-eszközök

- Windows 10
- Windows Server 2016
- A Windows Server 2019

Ebben a cikkben felsorolt támogatott verziója a Windows asztali operációs rendszert futtató eszközök esetében [Windows 10 kibocsátási információi](https://docs.microsoft.com/windows/release-information/). Ajánlott eljárásként a Microsoft azt javasolja, a Windows 10 legújabb verziójára frissít.

### <a name="windows-down-level-devices"></a>Windows régebbi verziójú eszközök

- Windows 8.1
- Windows 7. Támogatási információkat a Windows 7, olvassa el az ebben a cikkben [támogatja a Windows 7 lezárta](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Az első tervezési lépés tekintse át a környezet és alapján eldöntheti, hogy kell-e Windows régebbi verziójú eszközök támogatásához.

## <a name="review-things-you-should-know"></a>Felülvizsgálat tudnivalók

Hibrid Azure AD-csatlakozás jelenleg nem támogatott, ha a környezet áll-e egynél több Azure AD-bérlő azonosító adatok szinkronizálása a egyetlen AD-erdőben.

Hibrid Azure AD-csatlakozás jelenleg nem támogatott virtuális asztali infrastruktúra (VDI) használata esetén.

Hibrid Azure ad-ben nem támogatott a FIPS előírásainak megfelelő TPM. Ha az eszközök rendelkezik a FIPS-kompatibilis TPM-mel, akkor le kell tiltania őket a hibrid Azure AD-csatlakozás folytatása előtt. A Microsoft nem biztosít minden olyan eszközt, a TPM letiltása FIPS-módban, a TPM gyártója függ. Lépjen kapcsolatba a hardver OEM támogatása.

Hibrid Azure AD-csatlakozás a tartományvezérlőn (DC) szerepkört futtató Windows Server nem támogatott.

Hibrid Azure AD join nem támogatott Windows régebbi verziójú eszközök, hitelesítő adatok a központi vagy a felhasználói profil központi használatakor.

Ha a rendszer-előkészítő eszköz (Sysprep) hagyatkoznia, és használja a **Windows 10-es 1809** lemezkép telepítésének, győződjön meg arról, hogy a rendszerkép nem egy eszközről, amely már regisztrálva van az Azure AD-t hibrid Azure AD-csatlakozás.

Ha további virtuális gépek létrehozása egy virtuális gép (VM) pillanatképre, ellenőrizze, a pillanatkép nem, amely már regisztrálva van az Azure ad-hez mint hibrid Azure AD-csatlakozás a virtuális gép.

Ha a Windows 10-tartományhoz csatlakozó eszközök még [Azure ad-ben regisztrált](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) a bérlőjéhez, erősen ajánlott, hogy az állapot eltávolítása a hibrid Azure AD-csatlakozás engedélyezése előtt. A Windows 10-es 1809 a kiadásban a következő módosításokat végzett változtatások elkerülése érdekében ebben a kettős állapota:

- Minden meglévő Azure ad-ben regisztrált állapot automatikusan megszűnik, miután az eszköz a hibrid Azure AD-hez.
- Folyamatban van az Azure AD-ban regisztrálva a beállításkulcs - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" hozzáadásával megakadályozhatja a tartományba léptetett eszköz = dword: 00000001.
- Ez a változás már elérhető a Windows 10 1803 kiadásban a alkalmazni KB4489894. Azonban ha Windows Hello for Business konfigurálva, a felhasználóra szükség, re-telepítő Windows Hello for Business kettős állapota után távolítsa el.


## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Tekintse át a hibrid Azure AD-csatlakozás vezérelt érvényesítése

Előfeltételek az összes érvényesítve van, amikor Windows-eszközök automatikusan regisztrálnak az Azure AD-bérlő eszközökként. Az Azure ad-ben ezek eszközidentitások állapotát a hibrid Azure AD-csatlakozás nevezik. Ebben a cikkben ismertetett fogalmakat további információt a cikkekben található [eszköz Identitáskezelés az Azure Active Directoryban bemutatása](overview.md) és [a hibrid Azure Active Directory join megtervezése megvalósítás](hybrid-azuread-join-plan.md).

Szervezetek előfordulhat, hogy szeretné a hibrid Azure AD-csatlakozás szabályozott ellenőrzés előtt teszi lehetővé, és egyszerre a teljes szervezet. Tekintse át a [vezérelt érvényesítése hibrid Azure AD-csatlakozás](hybrid-azuread-join-control.md) tudni, hogyan végezheti el azt.


## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Az Ön identitáskezelési infrastruktúráját alapuló forgatókönyv kiválasztása

Hibrid Azure AD-csatlakozás, felügyelt és összevont környezetben is működik.  

### <a name="managed-environment"></a>Felügyelt környezetben

Lehet felügyelt környezetben keresztül telepített [jelszó Jelszókivonat szinkronizálása (nál)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) vagy [haladnak keresztül hitelesítést (ESP)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) a [zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Ezekben az esetekben nincs szükség, hogy konfiguráljon egy összevonási kiszolgálót a hitelesítéshez.

### <a name="federated-environment"></a>Összevont környezetben

Egy összevont környezetben rendelkeznie kell egy identitásszolgáltatóval, amely támogatja a következő követelményeknek:

- **WS-Trust protokollal:** Ez a protokoll megadása kötelező hitelesítéséhez a Windows jelenlegi hibrid Azure AD-hez csatlakoztatott eszközök Azure AD-val.
- **WIAORMULTIAUTHN jogcím:** Ez a jogcím szükség, ehhez a hibrid Azure AD-csatlakozás régebbi verziójú Windows-eszközökhöz.

Ha az Active Directory összevonási szolgáltatásokat (AD FS) összevont környezettel rendelkezik, majd a fenti követelmények már támogatottak.

> [!NOTE]
> Az Azure AD nem támogatja intelligens kártyákkal vagy tanúsítványokkal felügyelt tartományokban.

Az 1.1.819.0-s verziótól kezdve az Azure AD Connectben egy varázsló segíti a hibrid Azure AD-csatlakozások konfigurálását. Ez a varázsló jelentősen leegyszerűsíti a konfigurálási folyamatot. Ha a szükséges verzió az Azure AD Connect telepítésével lehetőség nem az Ön számára, lásd: [manuális konfigurálása az eszközregisztrációs](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

A forgatókönyv, amely megfelel az Ön identitáskezelési infrastruktúráját alapján, lásd:

- [Összevont környezetben hibrid Azure Active Directory-csatlakozás konfigurálása](hybrid-azuread-join-federated-domains.md)
- [Hibrid Azure Active Directory-csatlakozás felügyelt környezet konfigurálása](hybrid-azuread-join-managed-domains.md)



## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Tekintse át a helyszíni hibrid Azure AD-csatlakozás AD UPN támogatása

Előfordulhat, hogy a helyszíni AD UPN-EK az Azure AD UPN-EK eltérő lehet. Ezekben az esetekben a Windows 10-es hibrid Azure AD-csatlakozás korlátozott támogatást biztosít a helyszíni AD UPN-EK alapján a [hitelesítési módszer](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), típusa és a Windows 10-es verzió. Két típusa van a helyszíni AD UPN-EK, amely a környezetében is létezik:

- Irányítható egyszerű felhasználónév: Egy útválasztós UPN rendelkezik egy érvényes ellenőrzött tartományt, a tartományregisztráló regisztrált. Például akkor, ha a contoso.com az elsődleges tartomány Azure AD-ben, contoso.org lesz az elsődleges tartomány a helyszíni AD Contoso tulajdonában és [ellenőrzése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- Nem átirányítható egyszerű felhasználónév: Olyan nem átirányítható egyszerű felhasználónév nem rendelkezik egy ellenőrzött tartomány. Akkor csak a szervezet magánhálózaton belül. Például akkor, ha a contoso.com az elsődleges tartomány Azure AD-ben, contoso.local lesz az elsődleges tartomány a helyszíni AD, de nem egy ellenőrizhető az internetes tartományához, és csak a Contoso belül a hálózati.

Az alábbi táblázat részletesen támogatja ezeket a helyszíni AD UPN-EK a Windows 10-es hibrid Azure AD-csatlakozás

| Írja be a helyszíni AD UPN-jét | Alkalmazási tartomány típusa | Windows 10-es verzió | Leírás |
| ----- | ----- | ----- | ----- |
| Irányítható | Összevont | A 1703-as kiadás | Általánosan elérhető |
| A nem irányítható | Összevont | 1803 kiadásáról | Általánosan elérhető |
| Irányítható | Kezelt | Nem támogatott | |
| A nem irányítható | Kezelt | Nem támogatott | |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Konfigurálás hibrid Azure Active Directory-csatlakozás összevont környezeti](hybrid-azuread-join-federated-domains.md)
> [konfigurálása hibrid Azure Active Directory-csatlakozás felügyelt környezetben](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png

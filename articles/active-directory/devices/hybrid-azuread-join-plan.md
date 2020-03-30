---
title: Hibrid Azure Active Directory-csatlakozás tervezése – Azure Active Directory
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directory-csatlakoztatott eszközöket.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129258"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Útmutató: A hibrid Azure Active Directory-csatlakozás megvalósításának megtervezése

A felhasználóhoz hasonlóan az eszköz egy másik alapvető identitás, amelyet védeni szeretne, és amely et az erőforrások védelmére szeretne használni bármikor és bármely helyről. Ezt a célt úgy érheti el, hogy az alábbi módszerek egyikével hozza el és kezeli az eszközidentitásokat az Azure AD-ben:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az Azure AD-be való bevonással maximalizálható a felhasználók munkahatékonysága, köszönhetően az egyszeri bejelentkezésnek (SSO), amely a felhőbeli és a helyszíni erőforrásokhoz is hozzáférést nyújt. Ugyanakkor a feltételes hozzáféréssel biztonságos hozzáférést biztosíthat a felhőbeli és a helyszíni [erőforrásokhoz.](../active-directory-conditional-access-azure-portal.md)

Ha rendelkezik egy helyszíni Active Directory (AD) környezettel, és csatlakozni szeretne az AD tartományhoz csatlakozó számítógépekhez az Azure AD-hez, ezt hibrid Azure AD-csatlakozással valósíthatja meg. Ez a cikk a kapcsolódó lépéseket a hibrid Azure AD-csatlakozás megvalósításához a környezetben. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri az [Azure Active Directory eszközidentitás-kezelés – bevezetés](../device-management-introduction.md)t.

> [!NOTE]
> A Windows 10 hibrid Azure AD-csatlakozáshoz a minimálisan szükséges tartományvezérlő-verzió a Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Tervezze meg a megvalósítást

A hibrid Azure AD-implementáció megtervezéséhez ismerkedjen meg a következőkkel:

|   |   |
| --- | --- |
| ![Jelölőnégyzet][1] | A támogatott eszközök áttekintése |
| ![Jelölőnégyzet][1] | Az tudniad kell |
| ![Jelölőnégyzet][1] | A hibrid Azure AD-csatlakozás ellenőrzött érvényesítésének áttekintése |
| ![Jelölőnégyzet][1] | Válassza ki a forgatókönyvet az identitás-infrastruktúra alapján |
| ![Jelölőnégyzet][1] | Helyszíni AD UPN-támogatás áttekintése a hibrid Azure AD-csatlakozáshoz |

## <a name="review-supported-devices"></a>A támogatott eszközök áttekintése

A hibrid Azure AD-csatlakozás a Windows-eszközök széles körét támogatja. Mivel a Windows régebbi verzióit futtató eszközök konfigurációja további vagy különböző lépéseket igényel, a támogatott eszközök két kategóriába sorolhatók:

### <a name="windows-current-devices"></a>Windows aktuális eszközök

- Windows 10
- Windows Server 2016
  - **Megjegyzés:** Az Azure National felhőügyfeleinek 1809-es verzióra van szükségük
- Windows Server 2019

Az asztali Windows operációs rendszert futtató eszközök esetében a támogatott verzióebben a cikkben található a [Windows 10 kiadási információi.](/windows/release-information/) Ajánlott eljárásként a Microsoft azt javasolja, hogy frissítsen a Windows 10 legújabb verziójára.

### <a name="windows-down-level-devices"></a>Windows-szintalapú eszközök

- Windows 8.1
- A Windows 7 támogatása 2020. További információt a [Windows 7 támogatása című témakörben talál.](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. A Windows Server 2008 és 2008 R2 rendszerről a [Felkészülés a Windows Server 2008 támogatási szolgálatának végén](https://www.microsoft.com/cloud-platform/windows-server-2008)című témakörben talál.

Első tervezési lépésként tekintse át a környezetet, és állapítsa meg, hogy támogatnia kell-e a Windows lefelé irányuló eszközeit.

## <a name="review-things-you-should-know"></a>Az tudniad kell

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek
- Hibrid Azure AD-csatlakozás jelenleg nem támogatott, ha a környezet egyetlen AD-erdő ből áll, amely szinkronizálja az identitásadatokat egynél több Azure AD-bérlő.

- A tartományvezérlőt (DC) futtató Windows Server nem támogatja a hibrid Azure AD-csatlakozást.

- Hibrid Azure AD-csatlakozás nem támogatott a Windows down-level eszközök használata esetén hitelesítő adatok roaming vagy felhasználói profil roaming vagy kötelező profil használatakor.

- A Server Core operációs rendszer nem támogat semmilyen típusú eszközregisztrációt.

### <a name="os-imaging-considerations"></a>Az operációs rendszer képalkotási szempontjai
- Ha a Rendszerelőkészítő eszközre (Sysprep) támaszkodik, és **a Windows 10 1809 előtti** lemezképet használja a telepítéshez, győződjön meg arról, hogy a lemezkép nem olyan eszközről származik, amely hibrid Azure AD-csatlakozásként már regisztrálva van az Azure AD-vel.

- Ha egy virtuális gép (VM) pillanatkép további virtuális gépek létrehozásához, győződjön meg arról, hogy a pillanatkép nem egy virtuális gép, amely már regisztrálva van az Azure AD hibrid Azure AD-csatlakozásként.

- Ha az [egyesített írási szűrő](/windows-hardware/customize/enterprise/unified-write-filter) és hasonló technológiák, amelyek egyértelmű módosításokat a lemez újraindításkor, azokat alkalmazni kell, miután az eszköz hibrid Azure AD csatlakozott. Ha engedélyezi az ilyen technológiákat a hibrid Azure AD-csatlakozás befejezése előtt, az eszköz minden újraindításkor leválik a csatlakozásról.

### <a name="handling-devices-with-azure-ad-registered-state"></a>Eszközök kezelése az Azure AD regisztrált állapotával
Ha a Windows 10-es tartományhoz csatlakozó eszközök [Azure AD regisztrálva](overview.md#getting-devices-in-azure-ad) vannak a bérlő, ez vezethet a hibrid Azure AD-hez csatlakozott és az Azure AD regisztrált eszköz kettős állapotát. Javasoljuk, hogy frissítsen a Windows 10 1803 rendszerre (a KB4489894 alkalmazással) vagy újabb verzióra, hogy automatikusan megoldható a forgatókönyv. Az 1803 előtti kiadásokban manuálisan el kell távolítania az Azure AD regisztrált állapotát, mielőtt engedélyezne hibrid Azure AD-csatlakozást. 1803-ban és a fenti kiadásokban a következő módosításoktörténtek ennek a kettős állapotnak az elkerülése érdekében:

- A felhasználó meglévő Azure AD-regisztrált állapota automatikusan eltávolításra <i>kerül, miután az eszköz hibrid Azure AD-hez csatlakozik, és ugyanaz a felhasználó bejelentkezik.</i> Ha például "A" felhasználó nak volt egy Azure AD regisztrált állapota az eszközön, az "A" felhasználó kettős állapota csak akkor törlődik, ha "A" felhasználó bejelentkezik az eszközre. ha több felhasználó van ugyanazon az eszközön, a kettős állapot egyenként törlődik, amikor ezek a felhasználók bejelentkeznek.
- A rendszerleíró kulcs hozzáadásával megakadályozhatja, hogy a tartományhoz csatlakozó eszköz az Azure AD-t regisztrálta, ha hozzáadja ezt a beállításkulcsot - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- Ha a Windows 10 1803 rendszerben a Windows Hello for Business konfigurálva van, a felhasználónak újra kell állítania a Windows Hello for Business rendszert a kettős állapot megtisztítása után. A kb4512509

> [!NOTE]
> Az Azure AD regisztrált eszköz nem lesz automatikusan eltávolítva, ha az Intune kezeli.

### <a name="additional-considerations"></a>Néhány fontos megjegyzés
- Ha a környezet virtuális asztali infrastruktúrát (VDI) használ, olvassa el az [Eszközidentitás és az asztali virtualizálás című témakört.](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure)

- A hibrid Azure AD-csatlakozás a FIPS-kompatibilis TPM 2.0-s, a TPM 1.2-es szabvány ban nem támogatott. Ha az eszközök FIPS-kompatibilis TPM 1.2-es, le kell tiltania őket, mielőtt a hibrid Azure AD-csatlakozás. A Microsoft nem biztosít eszközöket a TPM-ek FIPS-módjának letiltására, mivel az a TPM gyártójától függ. Támogatásért forduljon a hardverogyártóhoz. 

- A Windows 10 1903-as kiadásától kezdve a TPMs 1.2 nem használható hibrid Azure AD-csatlakozással, és az ilyen TPM-ekkel rendelkező eszközöket úgy tekintünk, mintha nem rendelkeznének TPM-mel.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás ellenőrzött érvényesítésének áttekintése

Ha az összes előfeltétel a helyén van, a Windows-eszközök automatikusan regisztrálják magukat eszközként az Azure AD-bérlőben. Ezek az eszközidentitások az Azure AD-ben a továbbiakban hibrid Azure AD-csatlakozás. A cikkben tárgyalt fogalmakról az [Azure Active Directory eszközidentitás-kezelés](overview.md)– bevezetés című cikkben olvashat bővebben.

Előfordulhat, hogy a szervezetek a hibrid Azure AD-csatlakozás ellenőrzött érvényesítését szeretnék végezni, mielőtt egyszerre engedélyeznék a teljes szervezetben. Tekintse át a hibrid [Azure AD-csatlakozás szabályos érvényesítését](hybrid-azuread-join-control.md) a cikk et, hogy ismerje meg, hogyan valósítható meg.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Válassza ki a forgatókönyvet az identitás-infrastruktúra alapján

A hibrid Azure AD-illesztés attól függően működik, hogy az upn irányítható vagy nem irányítható, felügyelt és összevont környezetekkel is működik. A támogatott forgatókönyvek táblázatának alján a lap alján található.  

### <a name="managed-environment"></a>Felügyelt környezet

A felügyelt környezet a [jelszókivonat-szinkronizálás (PHS)](/azure/active-directory/hybrid/whatis-phs) vagy [az áthaladási hitelesítés (PTA)](/azure/active-directory/hybrid/how-to-connect-pta) segítségével telepíthető [zökkenőmentes egyszeri bejelentkezéssel.](/azure/active-directory/hybrid/how-to-connect-sso)

Ezek a forgatókönyvek nem igénylik az összevonási kiszolgáló hitelesítésre való konfigurálását.

### <a name="federated-environment"></a>Összevont környezet

Az összevont környezetben rendelkeznie kell egy identitásszolgáltató, amely támogatja a következő követelményeket. Ha az Active Directory összevonási szolgáltatások (AD FS) használatával összevont környezetet használ, akkor az alábbi követelmények már támogatottak.

- **WIAORMULTIAUTHN állítás:** Ez a jogcím a hibrid Azure AD-csatlakozás hoz a Windows down-level eszközök.
- **WS-Trust protokoll:** Ez a protokoll a Windows aktuális hibrid Azure AD-hez csatlakozott eszközeinek az Azure AD-vel való hitelesítéséhez szükséges. AD FS használata esetén a következő WS-Trust végpontokat kell engedélyeznie:`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Mind **az adfs/services/trust/2005/windowstransport,** mind **az adfs/services/trust/13/windowstransport** csak intranetes végpontként engedélyezhető, és NEM lehet extranetes végpontként kitenni a webalkalmazás-proxyn keresztül. A WS-Trust Windows végpontok letiltásáról a [WS-Trust Windows végpontok letiltása a proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)című témakörben olvashat bővebben. A > **Szolgáltatásvégpontok**csoportban láthatja, hogy az **Service**AD FS felügyeleti konzolon keresztül milyen végpontok vannak engedélyezve.

> [!NOTE]
> Az Azure AD nem támogatja az intelligens kártyákat vagy tanúsítványokat a felügyelt tartományokban.

Az 1.1.819.0-s verziótól kezdve az Azure AD Connectben egy varázsló segíti a hibrid Azure AD-csatlakozások konfigurálását. Ez a varázsló jelentősen leegyszerűsíti a konfigurálási folyamatot. Ha az Azure AD Connect szükséges verziójának telepítése nem lehetséges, olvassa el, [hogyan konfigurálhatja manuálisan az eszközregisztrációt.](hybrid-azuread-join-manual.md) 

Az identitás-infrastruktúrának megfelelő forgatókönyv alapján lásd:

- [Hibrid Azure Active Directory-csatlakozás konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
- [Hibrid Azure Active Directory-csatlakozás konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Helyszíni AD-felhasználók UPN-támogatásának áttekintése a hibrid Azure AD-csatlakozáshoz

Előfordulhat, hogy a helyszíni AD-felhasználók UPN-ek eltérhetnek az Azure AD upn-ek. Ilyen esetekben a Windows 10 hibrid Azure AD-csatlakozás korlátozott támogatást nyújt a helyszíni AD UPN-ek a [hitelesítési módszer](/azure/security/fundamentals/choose-ad-authn), a tartomány típusa és a Windows 10-es verzió alapján. A környezetben kétféle helyszíni ad upn létezhet:

- Irányítható felhasználók felhasználóinak felhasználói: Az irányítható upn érvényes ellenőrzött tartománnyal rendelkezik, amely egy tartományregisztrálónál van regisztrálva. Ha például contoso.com az elsődleges tartomány az Azure AD-ben, contoso.org a Contoso tulajdonában lévő és [az Azure AD-ben ellenőrzött](/azure/active-directory/fundamentals/add-custom-domain) helyszíni AD elsődleges tartománya.
- Nem irányítható felhasználók: Egy nem irányítható upn nem rendelkezik ellenőrzött tartománnyal. Csak a szervezet magánhálózatán belül alkalmazható. Ha például contoso.com az elsődleges tartomány az Azure AD-ben, contoso.local az elsődleges tartomány a helyszíni AD, de nem egy ellenőrizhető tartomány az interneten, és csak a Contoso hálózatán.

> [!NOTE]
> Az ebben a szakaszban szereplő információk csak a helyszíni felhasználók felhasználói nak felhasználói csoportn. Nem alkalmazható helyszíni számítógép-tartományutótagra (például: computer1.contoso.local).

Az alábbi táblázat részletesen ismerteti a Windows 10 hibrid Azure AD-csatlakozásban a helyszíni AD UPN-ek támogatását.

| A helyszíni AD UPN típusa | Alkalmazási tartomány típusa | Windows 10-es verzió | Leírás |
| ----- | ----- | ----- | ----- |
| Irányítható | Összevont | 1703-tól kiadás | Általánosan elérhető |
| Nem irányítható | Összevont | 1803-tól | Általánosan elérhető |
| Irányítható | Kezelt | 1803-tól | Általánosan elérhető, az Azure AD SSPR Windows zárolási képernyőn nem támogatott |
| Nem irányítható | Kezelt | Nem támogatott | |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hibrid Azure Active Directory-csatlakozás konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
> [A hibrid Azure Active Directory-csatlakozás konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png

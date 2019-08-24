---
title: Hibrid Azure Active Directory csatlakoztatásának tervezése Azure Active Directory (Azure AD) alkalmazásban | Microsoft Docs
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
ms.openlocfilehash: 62496aceb1454283449e952c0ed86623597e9e66
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011667"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>kézikönyv: A hibrid Azure Active Directory-csatlakozás megvalósításának megtervezése

A felhasználóhoz hasonló módon az eszköz egy másik alapvető identitás, amelyet védetté szeretne tenni, és így bármikor és bárhonnan védetté teheti az erőforrásokat. Ezt a célt az eszközök identitásának az Azure AD-ben való üzembe helyezésével és kezelésével végezheti el az alábbi módszerek egyikével:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az Azure AD-be való bevonással maximalizálható a felhasználók munkahatékonysága, köszönhetően az egyszeri bejelentkezésnek (SSO), amely a felhőbeli és a helyszíni erőforrásokhoz is hozzáférést nyújt. Ugyanakkor a felhőben és a helyszíni erőforrásokhoz is biztonságossá teheti a hozzáférést a [feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md).

Ha helyszíni Active Directory (AD) környezettel rendelkezik, és az AD-tartományhoz csatlakoztatott számítógépeket az Azure AD-hez szeretné csatlakoztatni, ezt a hibrid Azure AD JOIN használatával végezheti el. Ez a cikk a hibrid Azure AD-csatlakozás környezetbe való bevezetéséhez kapcsolódó lépéseket ismerteti. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy ismeri a [Azure Active Directory eszköz-identitások kezelésének](../device-management-introduction.md)bevezetését.

> [!NOTE]
> A Windows 10 hibrid Azure AD JOIN minimálisan szükséges tartományi működési és erdő működési szintjei a Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>A megvalósítás tervezése

A hibrid Azure AD-megvalósítás megtervezéséhez Ismerkedjen meg a következővel:

|   |   |
| --- | --- |
| ![Ellenőrzés][1] | Támogatott eszközök áttekintése |
| ![Ellenőrzés][1] | Tekintse át a tudni kívánt dolgokat |
| ![Ellenőrzés][1] | Hibrid Azure AD-csatlakozás ellenőrzött ellenőrzésének áttekintése |
| ![Ellenőrzés][1] | Válassza ki a forgatókönyvet az identitás-infrastruktúra alapján |
| ![Ellenőrzés][1] | A hibrid Azure AD-csatlakozás helyszíni AD UPN-támogatásának áttekintése |

## <a name="review-supported-devices"></a>Támogatott eszközök áttekintése

A hibrid Azure AD JOIN a Windows-eszközök széles körét támogatja. Mivel a Windows régebbi verzióit futtató eszközök konfigurációja további vagy eltérő lépéseket igényel, a támogatott eszközök két kategóriába vannak csoportosítva:

### <a name="windows-current-devices"></a>Windows aktuális eszközök

- Windows 10
- Windows Server 2016
- A Windows Server 2019

A Windows asztali operációs rendszert futtató eszközök esetében a támogatott verzió a [Windows 10 kiadási információi](https://docs.microsoft.com/windows/release-information/)című cikkben szerepel. Ajánlott eljárásként a Microsoft azt javasolja, hogy frissítsen a Windows 10-es legújabb verziójára.

### <a name="windows-down-level-devices"></a>Windows Down szintű eszközök

- Windows 8.1
- Windows 7. A Windows 7 rendszerrel kapcsolatos támogatási információkért tekintse át ezt a cikket a [Windows 7 támogatásának vége](https://www.microsoft.com/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Első tervezési lépésként tekintse át a környezetet, és állapítsa meg, hogy szükséges-e a Windows Down-szintű eszközeinek támogatása.

## <a name="review-things-you-should-know"></a>Tekintse át a tudni kívánt dolgokat

A hibrid Azure AD-csatlakozás jelenleg nem támogatott, ha a környezet egyetlen AD-erdőből áll, és több Azure AD-bérlőhöz szinkronizálja az azonosító adatait.

A hibrid Azure AD-csatlakozás jelenleg nem támogatott a virtuális asztali infrastruktúra (VDI) használata esetén.

A hibrid Azure AD JOIN nem támogatott az FIPS-kompatibilis TPM. Ha az eszközök FIPS-kompatibilis TPM rendelkeznek, le kell tiltania azokat, mielőtt folytatná a hibrid Azure AD Joint. A Microsoft nem biztosít olyan eszközöket, amelyekkel letiltható a TPM FIPS üzemmódja, mert a TPM-gyártótól függ. Támogatásért forduljon a hardver OEM-hez.

A hibrid Azure AD JOIN nem támogatott a tartományvezérlő (DC) szerepkört futtató Windows Serveren.

A hibrid Azure AD JOIN nem támogatott a Windows rendszerű, a hitelesítő adatok központi vagy felhasználói profiljának barangolásakor.

Ha a rendszer-előkészítő eszköz (Sysprep) szolgáltatásra támaszkodik, és ha a telepítés **előtt Windows 10 1809** rendszerképet használ, győződjön meg arról, hogy a rendszerkép nem olyan eszközről származik, amely már regisztrálva van az Azure ad-ben hibrid Azure ad-csatlakozásként.

Ha virtuális gép (VM) pillanatképét szeretné létrehozni további virtuális gépek létrehozásához, győződjön meg arról, hogy a pillanatkép nem olyan virtuális gépről származik, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-csatlakozásként.

Ha a Windows 10 tartományhoz csatlakoztatott eszközök az [Azure ad](overview.md#getting-devices-in-azure-ad) -t a bérlőhöz regisztrálják, akkor a hibrid Azure ad-hez csatlakoztatott és az Azure ad-beli regisztrált eszközök kettős állapotát eredményezheti. Javasoljuk, hogy a forgatókönyv automatikus megoldásához frissítsen a Windows 10 1803 (KB4489894 alkalmazott) vagy újabb verzióra. Az 1803-os kiadásokban a hibrid Azure AD-csatlakozás engedélyezése előtt manuálisan el kell távolítania az Azure AD-beli regisztrált állapotot. A 1803-es és újabb verzióiban a következő módosítások történtek a kettős állapot elkerüléséhez:

- A meglévő Azure AD-beli regisztrált állapotok automatikusan el lesznek távolítva <i>az eszköz hibrid Azure ad-hez való csatlakoztatása után</i>.
- Megakadályozhatja, hogy a tartományhoz csatlakoztatott eszköz regisztrálva legyen az Azure AD-ben a következő beállításkulcs hozzáadásával: HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = DWORD: 00000001.
- Windows 10 1803 rendszerben, ha a vállalati Windows Hello konfigurálva van, a felhasználónak újra kell telepítenie a Windows Hello for businesst a kettős állapot törlése után. Ez a probléma a KB4512509-mel foglalkozott



## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás ellenőrzött ellenőrzésének áttekintése

Ha az összes előfeltétel teljesül, a Windows rendszerű eszközök automatikusan regisztrálják az eszközöket az Azure AD-bérlőben. Az Azure AD-beli eszköz-identitások állapotát hibrid Azure AD-csatlakozásnak nevezzük. Az ebben a cikkben ismertetett fogalmakkal kapcsolatos további információkért tekintse meg az [eszközök Identitáskezelés](overview.md) a Azure Active Directoryban című cikket, és [tervezze meg a hibrid Azure Active Directory illesztés megvalósítását](hybrid-azuread-join-plan.md).

Előfordulhat, hogy a szervezetek a hibrid Azure AD-csatlakozás ellenőrzött érvényesítését szeretnék elvégezni, mielőtt a teljes szervezetben egyszerre engedélyezzék az egész szervezetet. Tekintse át a [hibrid Azure ad](hybrid-azuread-join-control.md) -hez való csatlakozás ellenőrzött ellenőrzésének lépéseit, és Ismerje meg, hogyan valósítható meg.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Válassza ki a forgatókönyvet az identitás-infrastruktúra alapján

A hibrid Azure AD JOIN mindkét, felügyelt és összevont környezettel működik, attól függően, hogy az egyszerű felhasználónév irányítható vagy nem irányítható. Tekintse meg a lap alját a táblázathoz a támogatott forgatókönyvek esetében.  

### <a name="managed-environment"></a>Felügyelt környezet

A felügyelt környezetek a [jelszó kivonatos szinkronizálásával (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) vagy a [hitelesítés (PTA ESP)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) használatával is üzembe helyezhetők, [zökkenőmentes egyszeri bejelentkezéssel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Ezekhez a forgatókönyvekhez nem szükséges összevonási kiszolgálót konfigurálni a hitelesítéshez.

### <a name="federated-environment"></a>Összevont környezet

Az összevont környezetnek rendelkeznie kell egy olyan identitás-szolgáltatóval, amely a következő követelményeket támogatja. Ha Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával összevont környezettel rendelkezik, az alábbi követelmények már támogatottak.

- **WIAORMULTIAUTHN jogcím:** Ez a jogcím szükséges a hibrid Azure AD-csatlakozáshoz a Windows rendszerű eszközökön.
- **WS-Trust protokoll:** Ez a protokoll szükséges a Windows jelenlegi hibrid Azure AD-hez csatlakoztatott eszközök Azure AD-vel való hitelesítéséhez. AD FS használatakor engedélyeznie kell a következő WS-Trust végpontokat:`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Az **ADFS/Services/Trust/2005/windowstransport** , vagy az **ADFS/Services/Trust/13/windowstransport** beállítást csak intranetes végpontként kell engedélyezni, és a webalkalmazás-proxyn keresztül nem szabad az extranet felé irányuló végpontok számára elérhetővé tenni. Ha többet szeretne megtudni a WS-Trust Windows-végpontok letiltásáról, tekintse meg a következőt: [ws-Trust Windows-végpontok letiltása a proxyn](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Láthatja, hogy mely végpontok vannak engedélyezve a AD FS felügyeleti konzolon a **szolgáltatási** > **végpontok**alatt.

> [!NOTE]
> Az Azure AD nem támogatja a felügyelt tartományokban található intelligens kártyákat vagy tanúsítványokat.

Az 1.1.819.0-s verziótól kezdve az Azure AD Connectben egy varázsló segíti a hibrid Azure AD-csatlakozások konfigurálását. Ez a varázsló jelentősen leegyszerűsíti a konfigurálási folyamatot. Ha a Azure AD Connect szükséges verziójának telepítése nem lehetséges, tekintse meg az [eszközök regisztrációjának manuális konfigurálását](hybrid-azuread-join-manual.md)ismertető témakört. 

Az identitás-infrastruktúrának megfelelő forgatókönyv alapján lásd:

- [Hibrid Azure Active Directory csatlakozás konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
- [Hibrid Azure Active Directory csatlakozás konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás helyszíni AD UPN-támogatásának áttekintése

Időnként előfordulhat, hogy a helyszíni AD UPN-felhasználónevek nem azonosak az Azure AD UPN-vel. Ilyen esetekben a Windows 10 Hybrid Azure AD JOIN korlátozott támogatást biztosít a helyszíni AD UPN-hez a [hitelesítési módszer](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), a tartomány típusa és a Windows 10 verziója alapján. A környezetben két típusú helyszíni AD UPN létezik:

- Irányítható UPN: Az átirányítható UPN-nek van egy érvényes ellenőrzött tartománya, amely regisztrálva van egy tartományregisztrálónál. Ha például az contoso.com az elsődleges tartomány az Azure AD-ben, a contoso.org az elsődleges tartomány a contoso által birtokolt és az [Azure ad-ben ellenőrzött](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) helyszíni ad-ben.
- Nem irányítható UPN: Egy nem irányítható UPN-nek nincs ellenőrzött tartománya. Csak a szervezet magánhálózaton belül alkalmazható. Ha például az contoso.com az elsődleges tartomány az Azure AD-ben, a contoso. local az elsődleges tartomány a helyszíni AD-ben, de nem ellenőrizhető tartomány az interneten, és csak a contoso hálózatán belül használatos.

Az alábbi táblázat részletesen ismerteti ezen helyszíni AD UPN-ket a Windows 10 hibrid Azure AD JOIN szolgáltatásban

| A helyszíni AD UPN típusa | Alkalmazási tartomány típusa | Windows 10 verzió | Leírás |
| ----- | ----- | ----- | ----- |
| Irányítható | Összevont | 1703-es kiadásból | Általánosan elérhető |
| Nem irányítható | Összevont | 1803-es kiadásból | Általánosan elérhető |
| Irányítható | Kezelt | Nem támogatott | |
| Nem irányítható | Kezelt | Nem támogatott | |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hibrid Azure Active Directory illesztés konfigurálása összevont környezetet](hybrid-azuread-join-federated-domains.md)
> –[hibrid Azure Active Directory csatlakozás konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png

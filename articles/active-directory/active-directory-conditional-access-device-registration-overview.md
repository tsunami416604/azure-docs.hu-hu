---
title: "Az Azure Active Directory eszközregisztrációjának áttekintése | Microsoft Docs"
description: "az eszközalapú feltételes hozzáférési forgatókönyvek alapja. Amikor regisztrál egy eszközt, az Azure Active Directory eszközregisztráció egy identitással látja el az eszközt, amely az eszköz hitelesítésére használható a felhasználó bejelentkezésekor."
services: active-directory
keywords: "eszközregisztráció, eszközregisztráció engedélyezése, eszközregisztráció és MDM"
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: 1e92c1a2-01b8-4225-950b-373cd601b035
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2017
ms.author: Markvi
translationtype: Human Translation
ms.sourcegitcommit: 8df5889bfe2757ef68d0895d140dd44a765a89d0
ms.openlocfilehash: 06fb59f627ea6aff3916787e321069c6b58c3e10
ms.lasthandoff: 02/23/2017


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Ismerkedés az Azure Active Directory eszközregisztrációjával
Az Azure Active Directory eszközregisztráció az eszközalapú feltételes hozzáférési forgatókönyvek alapja. Amikor regisztrál egy eszközt, az Azure Active Directory eszközregisztráció egy identitással látja el az eszközt, amely az eszköz hitelesítésére használható a felhasználó bejelentkezésekor. A hitelesített eszköz és az eszköz attribútumai ezután a feltételes hozzáférési házirendek betartatásához használhatók a felhőben és a helyszínen tárolt alkalmazások esetében.

Amikor mobileszköz-kezelési (MDM) megoldással, például a Microsoft Intune-nal ötvözi, frissülnek az Azure Active Directoryban lévő eszközattribútumok az eszköz további információival. Ez lehetővé teszi további feltételes hozzáférési szabályok létrehozását, amelyek arra kényszerítik az eszközhozzáféréseket, hogy megfeleljenek a biztonsági és megfelelőségi szabványoknak. További információk az eszközöknek a Microsoft Intune-ban történő regisztrálásával kapcsolatban: [Eszközök regisztrálása felügyeletre a Microsoft Intune-ban](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Az Azure Active Directory eszközregisztráció által engedélyezett forgatókönyvek
Az Azure Active Directory eszközregisztráció részét képezi az iOS-, Android- és Windows Phone-eszközök támogatása. Az Azure AD eszközregisztrációt használó egyes forgatókönyvek konkrétabb követelményekkel és platformtámogatással rendelkezhetnek. Ezek a forgatókönyvek a következők:

* **Helyben tárolt alkalmazások feltételes hozzáférése:** Hozzáférési házirendekkel használhat regisztrált eszközöket olyan alkalmazásokhoz, amelyek a Windows Server 2012 R2 AD FS szolgáltatásának használatához vannak konfigurálva. A helyszíni feltételes hozzáférés beállításáról további információért lásd: [Helyszíni feltételes hozzáférés beállítása az Azure Active Directory eszközregisztrációjával](active-directory-conditional-access-on-premises-setup.md).
* **Feltételes hozzáférés az Office 365-alkalmazásokhoz a Microsoft Intune-nal:** Az informatikai rendszergazdák feltételes hozzáférési eszközházirendeket adhatnak meg a vállalati erőforrások biztonságossá tétele érdekében, miközben lehetővé teheteik, hogy az infomunkások elérhessék a szolgáltatásokat a feltételeknek megfelelő eszközökön. További információ: [Feltételes hozzáférés eszközházirendjei Office 365-szolgáltatásokhoz](active-directory-conditional-access-device-policies.md).

## <a name="setting-up-azure-active-directory-device-registration"></a>Az Azure Active Directory eszközregisztráció beállítása
Engedélyeznie kell az Azure AD eszközregisztrációját az Azure Portalon, hogy a mobileszközök felderíthessék a szolgáltatást a jól ismert DNS-rekordok megkeresésével. Úgy kell konfigurálnia a vállalat DNS-ét, hogy a Windows 10, Windows 8.1, Windows 7, Android és iOS rendszerű eszközök felderíthessék és használhassák a szolgáltatást.
Az Azure Active Directory felügyeleti portálján tekintheti meg és engedélyezheti/tilthatja le a regisztrált eszközöket.

> [!NOTE]
> Az automatikus eszközregisztráció beállításával kapcsolatos legújabb utasításokat a [How to setup automatic registration of Windows domain joined devices with Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md) (Windows-tartományhoz csatlakoztatott eszközök automatikus regisztrációjának beállítása az Azure Active Directoryval) című cikkben tekintheti meg.
> 
> 

### <a name="enable-azure-active-directory-device-registration-service"></a>Az Azure Active Directory eszközregisztrációs szolgáltatás engedélyezése
1. Jelentkezzen be a Microsoft Azure Portalra rendszergazdaként.
2. A bal oldali panelen válassza az **Active Directory** elemet.
3. A **Címtár** lapon válassza ki a címtárat.
4. Válassza a **Konfigurálás** lapot.
5. Görgessen az **Eszközök** nevű szakaszhoz.
6. Válassza a **MIND** lehetőséget **A FELHASZNÁLÓK CSATLAKOZTATHATJÁK A MUNKAHELYEN AZ ESZKÖZEIKET** alatt.
7. Válassza ki a felhasználónként regisztrálni kívánt eszközök maximális számát.

> [!NOTE]
> A Microsoft Intune vagy az Office 365 mobileszköz-felügyelet használatával történő beléptetéshez munkahelyi csatlakoztatásra van szükség. Ha a szolgáltatások valamelyikét konfigurálta, a MIND lesz kiválasztva. és az EGYIK SEM gomb le lesz tiltva.
> 
> 

Alapértelmezés szerint a kéttényezős hitelesítés nem engedélyezett a szolgáltatáshoz. De a kéttényezős hitelesítés kiválasztása javasolt az eszközök regisztrálásakor.

* Mielőtt kéttényezős hitelesítést követelne meg ehhez a szolgáltatáshoz, konfigurálnia kell egy kéttényezős hitelesítésszolgáltatót az Azure Active Directoryban és konfigurálnia kell a felhasználói fiókjait a Multi-Factor Authentication lehetőséghez. Lásd: [A Multi-Factor Authentication felvétele az Azure Active Directoryba](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* Ha az AD FS-t a Windows Server 2012 R2 rendszerrel használja, konfigurálnia kell egy kéttényezős hitelesítési modult az AD FS-ben. Lásd: [Multi-Factor Authentication használata az Active Directory összevonási szolgáltatásokkal](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Az Azure Active Directory eszközregisztráció felderítésének konfigurálása
A Windows 7- és a Windows 8.1-eszközök az Eszközregisztrációs szolgáltatás felderítéséhez ötvözik a felhasználói fiók nevét egy jól ismert eszközregisztrációs kiszolgáló nevével.

Létre kell hoznia egy DNS CNAME-rekordot, amely az Azure Active Directory eszközregisztrációs szolgáltatással társított A-rekordra mutat. A CNAME-rekordnak a jól ismert enterpriseregistration előtagot kell használnia, amelyet a szervezetben a felhasználói fiókok által használt UPN-utótag követ. Ha a szervezet több UPN-utótagot használ, több CNAME-rekordot kell létrehozni a DNS-ben.

Ha például két UPN-utótagot használ a szervezetben (például a @contoso.com és a @region.contoso.com utótagot), a következő DNS-rekordokat kell létrehoznia.

| Bejegyzés | Típus | Cím |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Eszközobjektumok megtekintése és kezelése az Azure Active Directoryban
1. Az Azure felügyeleti portálon megtekintheti, blokkolhatja és feloldhatja az eszközök blokkolását. A blokkolt eszközök már nem érhetik el azon alkalmazásokat, amelyek csak regisztrált eszközök engedélyezésére vannak konfigurálva.
2. Jelentkezzen be a Microsoft Azure Portalra rendszergazdaként.
3. A bal oldali panelen válassza az **Active Directory** elemet.
4. Válassza ki a címtárát.
5. Válassza a **Felhasználók** lapot. Ezután válasszon ki egy felhasználót az eszközei megtekintéséhez.
6. Válassza az **Eszközök** lapot.
7. Válassza a legördülő menüből a **Regisztrált eszközök** elemet.
8. Itt megtekintheti, blokkolhatja, vagy feloldhatja a felhasználók regisztrált eszközeinek blokkolását.

## <a name="additional-topics"></a>További témakörök
Regisztrálhatja a Windows 7- és a Windows 8.1-tartományhoz csatlakozott eszközöket az Azure AD eszközregisztrációval. A következő témakörök további információt nyújtanak az előfeltételekről és a Windows 7- és Windows 8.1-eszközökkel végzett eszközregisztráció konfigurálásához szükséges lépésekről.

* [Automatikus eszközregisztráció az Azure Active Directoryval Windows-tartományhoz csatlakozott eszközökkel](active-directory-conditional-access-automatic-device-registration.md)
* [Automatikus eszközregisztráció konfigurálása Windows 7-tartományhoz csatlakozott eszközökkel](active-directory-conditional-access-automatic-device-registration-windows7.md)
* [Automatikus eszközregisztráció konfigurálása Windows 8.1-tartományhoz csatlakozott eszközökkel](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
* [Automatikus eszközregisztráció az Azure Active Directoryval Windows 10-tartományhoz csatlakozott eszközökkel](active-directory-azureadjoin-devices-group-policy.md)



---
title: 'Azure AD Connect: Hibrid Azure AD csatlakozás utáni konfigurációs feladatok | Microsoft dokumentumok'
description: Ez a dokumentum részletezi a hibrid Azure AD-csatlakozás végrehajtásához szükséges konfigurációs feladatokat
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffb8243041bb93ba8be6a65bb83df6f84affaee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049666"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás konfigurálása utáni feladatok

Miután futtatta az Azure AD Connectet a szervezet hibrid Azure AD-csatlakozáshoz való konfigurálásához, néhány további lépést kell végrehajtania a beállítás véglegesítéséhez.  Csak az eszközökre vonatkozó lépéseket hajtsa végre.

## <a name="1-configure-controlled-rollout-optional"></a>1. Az ellenőrzött bevezetés konfigurálása (nem kötelező)
A Windows 10 és a Windows Server 2016 rendszert futtató összes tartományhoz csatlakozó eszköz automatikusan regisztrál az Azure AD-vel, amint az összes konfigurációs lépés befejeződött. Ha az automatikus regisztráció helyett egy ellenőrzött bevezetést szeretne, a csoportházirend segítségével szelektíven engedélyezheti vagy letilthatja az automatikus bevezetést.  Ezt a csoportházirendet a többi konfigurációs lépés megkezdése előtt be kell állítani:
* Hozzon létre egy csoportházirend-objektumot az Active Directoryban.
* Nevezze el (ex- hibrid Azure AD-csatlakozás).
* Szerkesztés és ugrás: Számítógép-konfigurációs > házirendek > felügyeleti sablonok > windows-összetevők > eszközregisztráció.

>[!NOTE]
>A 2012R2 esetében a házirend-beállítások a **Számítógép konfigurációja > házirendjei > felügyeleti sablonok > a Windows-összetevők > a munkahelyi csatlakozás > Az ügyfélszámítógépek automatikus csatlakoztatása > automatikus munkahely**

* Engedélyezze ezt a beállítást: Regisztrálja a tartományhoz csatlakozó számítógépeket eszközként.
* Alkalmazza, és kattintson az OK gombra.
* Kapcsolja össze a csoportházirend-kiszolgálót az Ön által választott helyhez (szervezeti egység, biztonsági csoport vagy az összes eszköz tartománya).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Hálózat konfigurálása eszközregisztrációs végpontokkal
Győződjön meg arról, hogy a következő URL-címek érhetők el a szervezeti hálózaton belüli számítógépekről az Azure AD-re való regisztrációhoz:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. A WPAD implementálja A Windows 10-es eszközökhöz
Ha a szervezet egy kimenő proxyn keresztül fér hozzá az internethez, valósítsa meg a Web Proxy Auto-Discovery (WPAD) alkalmazást, hogy a Windows 10-es számítógépek regisztrálhassanak az Azure AD-re.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurálja az SCP-t minden olyan erdőben, amelyet nem az Azure AD Connect konfigurált 

A szolgáltatás csatlakozási pont (SCP) tartalmazza az Azure AD-bérlői adatokat, amelyeket az eszközök az automatikus regisztrációhoz fog használni.  Futtassa az Azure AD Connectről letöltött PowerShell-parancsfájlt, a ConfigureSCP.ps1 kódot.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Konfiguráljon minden olyan összevonási szolgáltatást, amelyet nem az Azure AD Connect konfigurált

Ha a szervezet egy összevonási szolgáltatást használ az Azure AD-be való bejelentkezéshez, az Azure AD függő entitás megbízhatóságának jogcímszabályainak engedélyezniük kell az eszköz hitelesítését. Ha az AD FS-sel összevonást használ, a jogcímszabályok létrehozásához lépjen az [AD FS súgójára.](https://aka.ms/aadrptclaimrules) Ha nem a Microsoft tól deszkaalapú összevonási megoldást használ, kérjen útmutatást a szolgáltatótól.  

>[!NOTE]
>Ha windowsos down-level eszközökkel rendelkezik, a szolgáltatásnak támogatnia kell a hitelesítési módszer és a wiaormultiauthn jogcímek kiadását az Azure AD megbízhatósági kapcsolatoknak érkező kérelmek fogadásakor. Az AD FS-ben olyan kiállításátalakítási szabályt kell hozzáadnia, amely átmegy a hitelesítési módszeren.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Az Azure AD zökkenőmentes egyszeri bejelentkezésének engedélyezése windowsos down-level eszközökhöz

Ha a szervezet jelszókivonat-szinkronizálást vagy átadó hitelesítést használ az Azure AD-be való bejelentkezéshez, engedélyezze az Azure AD seamless sso-t ezzel a bejelentkezési módszerrel a Windows lefelé irányuló eszközök hitelesítéséhez: https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Azure AD-házirend beállítása a Windows down-level eszközökhöz

A Windows-alapú lefelé simuló eszközök regisztrálásához győződjön meg arról, hogy az Azure AD-szabályzat lehetővé teszi a felhasználók számára az eszközök regisztrálását. 

* Jelentkezzen be a fiókjába az Azure Portalon.
* Ugrás: Az Azure Active Directory > eszközök > eszközbeállításai
* Állítsa a "Felhasználók regisztrálhatják eszközeiket az Azure AD"-val az ALL.
* Kattintson a Save (Mentés) gombra.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Azure AD-végpont hozzáadása a Windows down-szintű eszközeihez

Adja hozzá az Azure AD-eszköz hitelesítési végpontját a windowsos alsószintű eszközök helyi intranetzónáihoz, hogy elkerülje a tanúsítványkéréseket az eszközök hitelesítésekénte:`https://device.login.microsoftonline.com` 

Ha zökkenőmentes [egyszeri bejelentkezést](how-to-connect-sso.md)használ, engedélyezze az "Állapotsor-frissítések engedélyezése parancsfájlon keresztül" lehetőséget az adott zónában, és adja hozzá a következő végpontot:`https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. A Microsoft Workplace Join telepítése Windows-alapú lefelé néző eszközökre

Ez a telepítő egy ütemezett feladatot hoz létre az eszközrendszeren, amely a felhasználó környezetében fut. A feladat akkor indul el, amikor a felhasználó bejelentkezik a Windows rendszerbe. A feladat az integrált Windows-hitelesítés használatával történő hitelesítés után az Azure AD-vel csendesen csatlakozik az eszközhöz a felhasználói hitelesítő adatokkal. A letöltőközpont https://www.microsoft.com/download/details.aspx?id=53554a . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. A csoportházirend konfigurálása az eszközregisztráció engedélyezéséhez

* Hozzon létre egy csoportházirend-objektumot az Active Directoryban – ha még nem jött létre.
* Nevezze el (ex- hibrid Azure AD-csatlakozás).
* Szerkesztés & a következő: Számítógép konfigurációs > házirendek > felügyeleti sablonok > Windows-összetevők > eszközregisztráció
* Engedélyezés: Tartományhoz csatlakozó számítógépek regisztrálása eszközként
* Alkalmazza, és kattintson az OK gombra.
* Kapcsolja össze a csoportházirend-kiszolgálót az Ön által választott helyhez (szervezeti egység, biztonsági csoport vagy az összes eszköz tartománya).

>[!NOTE]
>A 2012R2 esetében a házirend-beállítások a **Számítógép konfigurációja > házirendjei > felügyeleti sablonok > a Windows-összetevők > a munkahelyi csatlakozás > Az ügyfélszámítógépek automatikus csatlakoztatása > automatikus munkahely**

## <a name="next-steps"></a>További lépések
[Eszközvisszaírás konfigurálása](how-to-connect-device-writeback.md)

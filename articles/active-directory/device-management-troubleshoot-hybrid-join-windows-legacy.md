---
title: Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi eszközök |} Microsoft Docs
description: Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi eszközök.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: d41e83c11f33b0bcbe4ea632332f2cd8bb12313f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714112"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi eszközök 

Ez a cikk csak olyan esetében alkalmazható a következő eszközöket: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 és Windows Server 2016: [hibaelhárítás hibrid Azure Active Directoryhoz csatlakoztatott Windows 10 és Windows Server 2016-os eszközök](device-management-troubleshoot-hybrid-join-windows-current.md).

Ez a cikk feltételezi, hogy rendelkezik [konfigurált hibrid Azure Active Directoryhoz csatlakoztatott eszközök](device-management-hybrid-azuread-joined-devices-setup.md) a következő forgatókönyvek támogatása céljából:

- Eszközalapú feltételes hozzáférés

- [Vállalati központi beállítások](active-directory-windows-enterprise-state-roaming-overview.md)

- [Vállalati Windows Hello](active-directory-azureadjoin-passport-deployment.md) 





Ez a cikk nyújt hibaelhárítási útmutatót a lehetséges problémák megoldásához.  

**Tudnivalók:** 

- A maximális száma felhasználónként: eszközközpontú. Például ha *jdoe* és *jharnett* jelentkezzen be egy eszközt, a különböző regisztrációs (DeviceID) hoz létre minden egyes azokat a **felhasználói** információ lapon.  

- A kezdeti regisztráció / eszközök illesztés van konfigurálva, hajtsa végre a bejelentkezéskor vagy a zárolás kísérlet / zárolásának feloldásához. A Feladatütemező által indított 5 perces késleltetés lehet. 

- Operációs rendszer vagy manuális Újraregisztrálás újratelepítését előfordulhat, hogy hozzon létre egy új regisztrációs az Azure AD, amely a felhasználó adatai lap az Azure portálon több bejegyzést eredményez. 

## <a name="step-1-retrieve-the-registration-status"></a>1. lépés: A regisztráció állapotának lekérése 

**A regisztráció állapotának ellenőrzése:**  

1. Bejelentkezés a felhasználói fiókkal, amely egy hibrid az Azure AD join hajtott végre.

2. Nyissa meg a parancssort rendszergazdaként 

3. Típusa `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

A parancs egy párbeszédpanelt, amely lehetővé teszi az illesztési állapotával kapcsolatos további adatokat jeleníti meg.

![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>2. lépés: Az Azure AD join állapot hibrid kiértékelése 

Ha a hybrid Azure AD join nem volt sikeres, a párbeszédpanel biztosít információkhoz juthat a problémáról történt.

**A leggyakoribb problémák vannak:**

- Egy helytelenül konfigurált AD FS vagy az Azure AD

    ![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Nincs bejelentkezve tartományi felhasználóként

    ![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Számos különböző okokból miért Ez akkor történhet.
    
    - A bejelentkezett felhasználó nevében nem tartományi felhasználó (például egy helyi felhasználót). Hibrid az Azure AD-kezelés régebbi eszközök csatlakoztatása csak tartományi felhasználók esetén támogatott.
    
    - Autoworkplace.exe nem tudja az Azure AD vagy AD FS csendes hitelesíteni. Ezt okozhatja az Azure AD URL-címek egy kimenő kötött hálózati kapcsolódási problémák. Azt is lehet, hogy többtényezős hitelesítés (MFA) a felhasználó számára engedélyezett/konfigurálva és WIAORMUTLIAUTHN nincs konfigurálva az összevonási kiszolgálón. Egy másik lehetőség, a hitelesítőtartomány felderítése (HRD) lap arra vár, hogy felhasználói beavatkozást, amely megakadályozza a **autoworkplace.exe** jogkivonat csendes beszerezni.
    
    - A szervezet használja az Azure AD zökkenőmentes egyszeri bejelentkezést, `https://autologon.microsoftazuread-sso.com` vagy `https://aadg.windows.net.nsatc.net` nem találhatók meg az eszköz Internet Explorer intranetes beállításai, és **beállítható a frissítések állapotsor keresztül parancsfájl** nincs engedélyezve az Intranet zóna.

- A kvóta elérve

    ![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- A szolgáltatás nem válaszol. 

    ![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Az állapot információt az eseménynaplóban a is talál: **alkalmazások és szolgáltatások Log\Microsoft-munkahelyi csatlakoztatás**
  
**Sikertelen a hibrid az Azure AD-csatlakozás leggyakoribb okai a következők:** 

- A számítógép nincs csatlakoztatva a szervezet belső hálózaton vagy a helyszíni kapcsolattal rendelkező VPN AD-tartományvezérlő.

- A számítógép helyi fiókkal jelentkezett be. 

- Szolgáltatás konfigurációs problémák: 

  - Az összevonási kiszolgáló konfigurációja támogatja **WIAORMULTIAUTHN**. 

  - A számítógép erdő rendelkezik nem szolgáltatáskapcsolódási pont objektum mutat az ellenőrzött és érvényes tartománynevet, az Azure ad-ben 

  - A felhasználó elérte a határértéket, az eszközök. 

## <a name="next-steps"></a>További lépések

Kérdéseit, tekintse meg a [eszköz felügyeleti kapcsolatos gyakori kérdések](device-management-faq.md)  

---
title: Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi verziójú eszközök |} A Microsoft Docs
description: Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi verziójú eszközök.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e79b0fc0ef23287c8871b7a7634d28d129b900e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101142"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi verziójú eszközök 

Ez a cikk az értékelem csak a következő eszközöket: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10-es vagy Windows Server 2016, lásd: [hibaelhárítás hibrid Azure Active Directoryhoz csatlakoztatott Windows 10 és Windows Server 2016 eszközök](troubleshoot-hybrid-join-windows-current.md).

Ez a cikk feltételezi, hogy [konfigurált hibrid Azure Active Directory-hez csatlakoztatott eszközök](hybrid-azuread-join-plan.md) támogatásához a következő esetekben:

- Eszközalapú feltételes hozzáférés


Ez a cikk nyújt hibaelhárítási útmutatót a potenciális problémák megoldásához.  

**Tudnivalók:** 

- Hibrid Azure AD joinnal korábbi verziójú Windows eszközök működése némileg eltérően, mint a Windows 10-es nem. Számos ügyfél nem tud szükségük van az Active Directory összevonási szolgáltatások (az összevont tartományok) vagy a közvetlen egyszeri bejelentkezés konfigurálva (a felügyelt tartományok).

- Összevont tartományok élvező vásárlók esetében ha a szolgáltatási csatlakozási pontot (SCP) lett konfigurálva, hogy a felügyelt tartomány nevét (például contoso.onmicrosoft.com, helyett contoso.com), mutat majd hibrid Azure AD Join korábbi verziójú Windows-eszközök esetén a rendszer nem működik.

- Jelenleg felhasználónként legfeljebb alacsonyabb szintű hibrid Azure AD-csatlakoztatott eszközök is vonatkozik. 

- Az ugyanazon fizikai eszköz több alkalommal Ha több tartományi felhasználó jelentkezzen be a korábbi verziójú hibrid Azure AD-csatlakoztatott eszközök Azure AD-ben jelenik meg.  Például ha *jdoe* és *jharnett* jelentkezzen be egy eszközt, egy különálló regisztrációs (DeviceID) jön létre minden egyes őket a **felhasználói** info lapon. 

- Az operációs rendszer vagy egy manuális Újraregisztrálás újratelepítésre miatt egy eszközhöz, a felhasználó-információ lapon is kaphat több bejegyzést.

- A kezdeti regisztráció / join eszközök megkísérli a bejelentkezést vagy a zárolás végrehajtása / feloldásához van konfigurálva. A Feladatütemező által aktivált 5 perces késleltetés lehet. 

- Győződjön meg arról, hogy [KB4284842](https://support.microsoft.com/help/4284842) van telepítve, Windows 7 SP1 vagy Windows Server 2008 R2 SP1 esetén. A frissítés megakadályozza, hogy az ügyfél-hozzáférési adatvesztés miatt a későbbi hitelesítési hibák védett kulcsokhoz jelszó módosítása után.

## <a name="step-1-retrieve-the-registration-status"></a>1. lépés: A regisztrációs állapot lekérése 

**A regisztrációs állapotának ellenőrzése:**  

1. Jelentkezzen be a felhasználói fiókkal, amely a hibrid Azure AD-csatlakozás hajtott végre.

2. Nyissa meg a parancssort 

3. Típusa `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Ez a parancs megjelenít egy párbeszédpanelt, amely biztosítja a join állapot részletes adatai.

![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>2. lépés: A hibrid Azure AD join állapot értékelése 

Ha az eszköz nem volt a hibrid Azure AD-hez, megpróbálhatja hibrid Azure AD-csatlakozás ehhez a "Csatlakozás" gombra kattintva. Hibrid Azure AD-csatlakozás ehhez a kísérlet meghiúsul, ha a hiba részleteit jelenik meg.


**A leggyakoribb kérdések a következők:**

- A helytelenül van konfigurálva, az AD FS vagy az Azure AD vagy hálózati problémák

    ![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
  - Autoworkplace.exe nem tudja az Azure AD vagy az AD FS-csendes hitelesítést. Ez okozhatja hiányzik vagy helytelenül van konfigurálva az Active Directory összevonási szolgáltatások (az összevont tartományok), vagy hiányzik vagy helytelenül konfigurált Azure AD közvetlen egyszeri bejelentkezés (a felügyelt tartományok), vagy hálózati problémák. 
    
    - Annak oka az lehet, hogy a multi-factor authentication (MFA) a kompatibilis vagy nincs konfigurálva a felhasználó számára, és WIAORMUTLIAUTHN nem az AD FS-kiszolgálón kell konfigurálni. 
     
    - Egy másik lehetőség, felhasználói beavatkozás, amely megakadályozza, hogy vár a kezdőtartomány felderítése (HRD) az oldal **autoworkplace.exe** csendes kérni egy token.
     
    - Annak oka az lehet, hogy az AD FS és az Azure ad-ben URL-címek hiányoznak az IE intranetzónához az ügyfélen.
     
    - Hálózati kapcsolódási problémák miatt **autoworkplace.exe** elérni az AD FS vagy az Azure AD URL-címeket. 
     
    - **Autoworkplace.exe** megköveteli, hogy közvetlen üzemel az ügyfél a szervezet a helyszínen rendelkeznek az ügyfél AD tartományvezérlőt, ami azt jelenti, hogy a hibrid Azure AD-csatlakozás sikeres, csak ha az ügyfél csatlakozik a szervezeti intraneten.
     
    - A szervezet használja az Azure AD közvetlen egyszeri bejelentkezés, `https://autologon.microsoftazuread-sso.com` vagy `https://aadg.windows.net.nsatc.net` nem találhatók az eszközök Internet Explorer intranetes beállításait, és **lehetővé teszik a frissítések állapotsor parancsfájl keresztül** nincs engedélyezve az Intranet zóna.

- Nincs bejelentkezve tartományi felhasználóként

    ![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    Van néhány különféle oka lehet, hogy miért Ez akkor fordulhat:
    
    - A bejelentkezett felhasználó nem egy tartományi felhasználó (például egy helyi felhasználó). Hibrid Azure AD join a régebbi verziójú eszközök csak tartományi felhasználók esetében támogatott.
    
    - Az ügyfél nem sikerül csatlakozni a tartományvezérlőhöz.    

- Elérte a kvótáját

    ![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- A szolgáltatás nem válaszol 

    ![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Az állapotinformációk is található eseménynaplóban: **Alkalmazások és szolgáltatások Log\Microsoft – a munkahelyi csatlakoztatás**
  
**A sikertelen a hibrid Azure AD join leggyakoribb okai a következők:** 

- A számítógép nincs csatlakoztatva a szervezet belső hálózati vagy VPN-kapcsolattal a helyszíni AD-tartományvezérlő.

- A számítógépre helyi fiókkal van bejelentkezve. 

- Szolgáltatás konfigurációs problémák: 

  - Az AD FS-kiszolgáló nincs beállítva támogatásához **WIAORMULTIAUTHN**. 

  - A számítógép erdő rendelkezik nincs Szolgáltatáskapcsolati pont objektumot, amely az ellenőrzött tartomány nevét az Azure ad-ben 
  
  - A tartomány felügyelt, akkor a közvetlen egyszeri bejelentkezés nem volt konfigurálva, vagy vagy nem működik.

  - A felhasználó elérte az eszközök felső határát. 

## <a name="next-steps"></a>További lépések

Ha kérdése van, tekintse meg a [Eszközfelügyelet – gyakori kérdések](faq.md)  

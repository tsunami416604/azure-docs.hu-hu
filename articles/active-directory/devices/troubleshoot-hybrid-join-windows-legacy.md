---
title: Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi verziójú eszközök |} A Microsoft Docs
description: Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi verziójú eszközök.
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
ms.openlocfilehash: 1121bc9e34f0e544ba5d7acb9179944ee2382efd
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414958"
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

- [Vállalati barangolás beállításai](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) 





Ez a cikk nyújt hibaelhárítási útmutatót a potenciális problémák megoldásához.  

**Tudnivalók:** 

- Eszközök felhasználónkénti maximális száma, eszközközpontú. Például ha *jdoe* és *jharnett* jelentkezzen be egy eszközt, egy különálló regisztrációs (DeviceID) jön létre minden egyes őket a **felhasználói** info lapon.  

- A kezdeti regisztráció / join eszközök megkísérli a bejelentkezést vagy a zárolás végrehajtása / feloldásához van konfigurálva. A Feladatütemező által aktivált 5 perces késleltetés lehet. 

- A felhasználó adatai lap egy eszközhöz több bejegyzés miatt az operációs rendszer vagy egy manuális Újraregisztrálás újratelepítésre kérheti le. 

- Győződjön meg arról, hogy [KB4284842](https://support.microsoft.com/en-us/help/4284842) van telepítve, Windows 7 SP1 vagy Windows Server 2008 R2 SP1 esetén. A frissítés megakadályozza, hogy az ügyfél-hozzáférési adatvesztés miatt a későbbi hitelesítési hibák védett kulcsokhoz jelszó módosítása után.

## <a name="step-1-retrieve-the-registration-status"></a>1. lépés: A regisztrációs állapot lekérése 

**A regisztrációs állapotának ellenőrzése:**  

1. Jelentkezzen be a felhasználói fiókkal, amely a hibrid Azure AD-csatlakozás hajtott végre.

2. Nyissa meg a parancssort rendszergazdaként 

3. Típusa `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Ez a parancs egy párbeszédpanel, amely biztosítja a join állapotával kapcsolatos további részleteket jeleníti meg.

![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>2. lépés: A hibrid Azure AD join állapot értékelése 

Ha a hibrid Azure AD join nem volt sikeres, a párbeszédpanel biztosít információkhoz juthat a problémáról történt.

**A leggyakoribb kérdések a következők:**

- Egy helytelenül konfigurált AD FS vagy az Azure ad-ben

    ![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/02.png)

- Nincs bejelentkezve tartományi felhasználóként

    ![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    Van néhány különféle oka lehet, hogy miért Ez akkor fordulhat:
    
    - A bejelentkezett felhasználó nem egy tartományi felhasználó (például egy helyi felhasználó). Hibrid Azure AD join a régebbi verziójú eszközök csak tartományi felhasználók esetében támogatott.
    
    - Autoworkplace.exe nem tudja az Azure AD vagy az AD FS-csendes hitelesítést. Ennek oka lehet egy kimenő irányú kötött hálózati problémák léptek fel az Azure AD URL-címekhez. Az is előfordulhat, hogy a multi-factor authentication (MFA) a kompatibilis vagy nincs konfigurálva a felhasználó számára, és WIAORMUTLIAUTHN nincs konfigurálva az összevonási kiszolgálón. Egy másik lehetőség, felhasználói beavatkozás, amely megakadályozza, hogy vár a kezdőtartomány felderítése (HRD) az oldal **autoworkplace.exe** csendes kérni egy token.
    
    - A szervezet használja az Azure AD közvetlen egyszeri bejelentkezés, `https://autologon.microsoftazuread-sso.com` vagy `https://aadg.windows.net.nsatc.net` nem találhatók az eszközök Internet Explorer intranetes beállításait, és **lehetővé teszik a frissítések állapotsor parancsfájl keresztül** nincs engedélyezve az Intranet zóna.

- Elérte a kvótáját

    ![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- A szolgáltatás nem válaszol 

    ![A munkahelyi csatlakoztatás Windows esetében](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Az állapotinformációk keresse meg az eseménynaplóban: **alkalmazások és szolgáltatások Log\Microsoft-alapú munkahelyi csatlakoztatás**
  
**A sikertelen a hibrid Azure AD join leggyakoribb okai a következők:** 

- A számítógép nincs csatlakoztatva a szervezet belső hálózati vagy VPN-kapcsolattal a helyszíni AD-tartományvezérlő.

- A számítógépre helyi fiókkal van bejelentkezve. 

- Szolgáltatás konfigurációs problémák: 

  - Az összevonási kiszolgáló konfigurációja támogatásához **WIAORMULTIAUTHN**. 

  - A számítógép erdő rendelkezik nincs Szolgáltatáskapcsolati pont objektumot, amely az ellenőrzött tartomány nevét az Azure ad-ben 

  - A felhasználó elérte az eszközök felső határát. 

## <a name="next-steps"></a>További lépések

Ha kérdése van, tekintse meg a [Eszközfelügyelet – gyakori kérdések](faq.md)  

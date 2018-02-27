---
title: "Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi eszközök |} Microsoft Docs"
description: "Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi eszközök."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 5657df412b1f2b7d4d43d7551289620ae4d77de2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi eszközök 

Ez a témakör a tulajdonság csak a következő eszközöket vonatkozik: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 és Windows Server 2016: [hibaelhárítás hibrid Azure Active Directoryhoz csatlakoztatott Windows 10 és Windows Server 2016-os eszközök](device-management-troubleshoot-hybrid-join-windows-current.md).

Ez a témakör azt feltételezi, hogy [konfigurált hibrid Azure Active Directoryhoz csatlakoztatott eszközök](device-management-hybrid-azuread-joined-devices-setup.md) a következő forgatókönyvek támogatása céljából:

- Eszközalapú feltételes hozzáférés

- [Vállalati központi beállítások](active-directory-windows-enterprise-state-roaming-overview.md)

- [Vállalati Windows Hello](active-directory-azureadjoin-passport-deployment.md) 





Ez a témakör nyújt hibaelhárítási útmutatót a lehetséges problémák megoldásához.  

**Tudnivalók:** 

- A maximális száma felhasználónként: eszközközpontú. Például ha *jdoe* és *jharnett* bejelentkezés az eszközön, a különböző regisztrációs (DeviceID) hoz létre minden egyes azokat a **felhasználói** információ lapon.  

- A kezdeti regisztráció / eszközök illesztés van konfigurálva, hajtsa végre a bejelentkezéskor vagy a zárolás kísérlet / zárolásának feloldásához. A Feladatütemező által indított 5 perces késleltetés lehet. 

- Egy telepítse újra az operációs rendszer vagy egy manuális unregister és regisztrálja újra az lehet, hogy hozzon létre egy új regisztrációs Azure ad-val, és a felhasználó adatai lap az Azure portálon több bejegyzést eredményez. 


## <a name="step-1-retrieve-the-registration-status"></a>1. lépés: A regisztráció állapotának lekérése 

**A regisztráció állapotának ellenőrzése:**  

1. Nyissa meg a parancssort rendszergazdaként 

2. Típusa `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

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
    
    1. Ha a bejelentkezett felhasználó nem tartományi felhasználó (például egy helyi felhasználót). Hibrid az Azure AD-kezelés régebbi eszközök csatlakoztatása csak tartományi felhasználók esetén támogatott.
    
    2. Ha bármilyen okból Autoworkplace.exe történő Azure AD vagy AD FS csendes hitelesítés sikertelen lesz. Néhány lehetséges ok lehet kimenő kötött hálózati csatlakozási problémák (az Előfeltételek ellenőrzése) URL az Azure AD-, vagy hogy a többtényezős hitelesítés a felhasználó számára engedélyezett/konfigurálva, de WIAORMUTLIAUTHN nincs konfigurálva az összevonási kiszolgálón (ellenőrzés konfigurációs lépések) is. Egy másik lehetőség, a hitelesítőtartomány felderítése (HRD) lap arra vár, hogy a felhasználói beavatkozás meggátolja, hogy a Autoworkplace.exe csendes megszerezni egy 
    
    3. Ha a szervezet használja az Azure AD zökkenőmentes egyszeri bejelentkezést, a következő URL-címeket nem találhatók meg az eszköz Internet Explorer intranetes beállításai:
    - https://autologon.microsoftazuread-sso.com
    - https://aadg.windows.net.nsatc.net
    
    és a "Állapotsor parancsfájl segítségével a frissítések engedélyezése" beállítást engedélyezni kell az Intranet zóna.

- A kvóta elérve

    ![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- A szolgáltatás nem válaszol. 

    ![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Az állapot információt az eseménynaplóban a is talál **alkalmazások és szolgáltatások Log\Microsoft-munkahelyi csatlakoztatás**.
  
**Sikertelen a hibrid az Azure AD-csatlakozás leggyakoribb okai a következők:** 

- A számítógép nincs a szervezet belső hálózaton vagy egy helyszíni kapcsolat nélkül VPN AD-tartományvezérlő.

- A számítógép helyi fiókkal jelentkezett be. 

- Szolgáltatás konfigurációs problémák: 

  - Az összevonási kiszolgáló konfigurációja támogatja **WIAORMULTIAUTHN**. 

  - Nincs mutat, a ellenőrzött tartomány nevét az Azure ad-ben az Active Directory-erdőben, ahol a számítógép tartozik szolgáltatáskapcsolódási pont objektum.

  - A felhasználó elérte a határértéket, az eszközök. 

## <a name="next-steps"></a>További lépések

Kérdéseit, tekintse meg a [eszköz felügyeleti kapcsolatos gyakori kérdések](device-management-faq.md)  

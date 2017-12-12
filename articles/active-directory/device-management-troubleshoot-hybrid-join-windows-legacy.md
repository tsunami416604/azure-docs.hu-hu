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
ms.openlocfilehash: f1b92c604e20198714e9697bf4d08b3f71f23ae3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
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

2. Típusa`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

A parancs egy párbeszédpanelt, amely lehetővé teszi az illesztési állapotával kapcsolatos további adatokat jeleníti meg.

![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>2. lépés: Az Azure AD join állapot hibrid kiértékelése 

Ha a hybrid Azure AD join nem volt sikeres, a párbeszédpanel biztosít információkhoz juthat a problémáról történt.

**A leggyakoribb problémák vannak:**

- Egy helytelenül konfigurált AD FS vagy az Azure AD

    ![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Nincs bejelentkezve tartományi felhasználóként

    ![A munkahelyi csatlakoztatás Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)

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

## <a name="next-steps"></a>Következő lépések

Kérdéseit, tekintse meg a [eszköz felügyeleti kapcsolatos gyakori kérdések](device-management-faq.md)  

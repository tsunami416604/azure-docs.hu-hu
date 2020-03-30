---
title: Az örökölt hibrid Azure Active Directoryhoz csatlakozó eszközök – problémamegoldás
description: A hibrid Azure Active Directory-hoz csatlakozott lefelé simító eszközök hibaelhárítása.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e168deea1ba442d48f483264c1e97ce618040f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379122"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Hibrid Azure Active Directory-csatlakozás oka az alulról nem vett eszközökre 

Ez a cikk csak a következő eszközökre vonatkozik: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Windows 10 vagy Windows Server 2016 esetén olvassa el A [hibrid Azure Active Directoryhoz csatlakozott Windows 10 és Windows Server 2016 eszközök hibaelhárítása című témakört.](troubleshoot-hybrid-join-windows-current.md)

Ez a cikk feltételezi, hogy [a hibrid Azure Active Directory-csatlakozással csatlakozott eszközöket a](hybrid-azuread-join-plan.md) következő forgatókönyvek támogatására konfigurálta:

- Eszközalapú feltételes hozzáférés

Ez a cikk hibaelhárítási útmutatást nyújt a lehetséges problémák megoldásához.  

**Mit kell tudni:** 

- Az emelt szintű Windows-eszközök hibrid Azure AD-csatlakozása némileg eltérően működik, mint a Windows 10-ben. Sok ügyfél nem veszi észre, hogy szükség van AD FS (az összevont tartományok) vagy zökkenőmentes egyszeri bejelentkezés konfigurálva (felügyelt tartományok).
- Az összevont tartományokkal rendelkező ügyfelek számára, ha a Szolgáltatás csatlakozási pont (SCP) úgy lett konfigurálva, hogy az a felügyelt tartománynévre mutat (például contoso.onmicrosoft.com, contoso.com helyett), akkor a hibrid Azure AD-csatlakozás az alsóbb szintű Windows-eszközökhöz nem működik.
- A felhasználónkénti eszközök maximális száma jelenleg az emelt szintű hibrid Azure AD-hez csatlakozó eszközökre is vonatkozik. 
- Ugyanaz a fizikai eszköz többször jelenik meg az Azure AD-ben, ha több tartományi felhasználó jelentkezik be az emelt szintű hibrid Azure AD-hez csatlakozott eszközök.  Ha például *jdoe* és *jharnett* bejelentkezik egy eszközre, a **FELHASZNÁLÓ** adatai lapon mindegyikhez külön regisztráció (DeviceID) jön létre. 
- Az operációs rendszer újratelepítése vagy manuális újbóli regisztráció miatt a felhasználói adatok lapon több bejegyzést is beszerezhet egy eszközhöz.
- Az eszközök kezdeti regisztrációja / illesztése úgy van beállítva, hogy kísérletet hajtson végre a bejelentkezésre vagy a zárolásra / feloldásra. Lehet, hogy egy feladatütemező feladat 5 perces késleltetést vált ki. 
- Windows 7 SP1 vagy Windows Server 2008 R2 SP1 esetén győződjön meg arról, hogy a [KB4284842](https://support.microsoft.com/help/4284842) frissítés telepítve van. Ez a frissítés megakadályozza a jövőbeli hitelesítési hibákat, mivel az ügyfél a jelszó módosítása után nem fér hozzá a védett kulcsokhoz.

## <a name="step-1-retrieve-the-registration-status"></a>1. lépés: A regisztrációs állapot lekérése 

**A regisztrációs állapot ellenőrzése:**  

1. Jelentkezzen be azzal a felhasználói fiókkal, amely hibrid Azure AD-csatlakozást hajtott végre.
1. A parancssor megnyitása 
1. Írja be a következőt: `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Ez a parancs egy párbeszédpanelt jelenít meg, amely az illesztés imát.

![Munkahelyi csatlakozás a Windows rendszerhez](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>2. lépés: Az Azure AD-illesztés hibrid állapotának kiértékelése 

Ha az eszköz nem hibrid Azure AD-hez csatlakozott, megpróbálhatja a hibrid Azure AD-csatlakozást a "Csatlakozás" gombra kattintva. Ha a kísérlet a hibrid Azure AD-csatlakozás sikertelen, a hiba részletei jelennek meg.

**A leggyakoribb problémák a következők:**

- Helytelenül konfigurált AD FS- vagy Azure AD- vagy hálózati problémák

    ![Munkahelyi csatlakozás a Windows rendszerhez](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - Az Autoworkplace.exe nem tud csendben hitelesíteni magát az Azure AD vagy az AD FS szolgáltatással. Ennek oka lehet a hiányzó vagy helytelenül konfigurált AD FS (összevont tartományok) vagy hiányzó vagy helytelenül konfigurált Azure AD seamless single sign-on (felügyelt tartományok) vagy hálózati problémák. 
   - Előfordulhat, hogy a többtényezős hitelesítés (MFA) engedélyezve van/konfigurálva van a felhasználó számára, és a WIAORMULTIAUTHN nincs konfigurálva az AD FS-kiszolgálón. 
   - Egy másik lehetőség, hogy az otthoni birodalom felderítése (HRD) lap felhasználói beavatkozásra vár, ami megakadályozza, hogy **az autoworkplace.exe** csendben tokenkérést kérjen.
   - Lehet, hogy az AD FS és az Azure AD URL-címek hiányoznak az IE intranetes zónájában az ügyfélen.
   - A hálózati kapcsolati problémák megakadályozhatják, hogy **az autoworkplace.exe** elérje az AD FS-t vagy az Azure AD URL-eket. 
   - **Az Autoworkplace.exe** program megköveteli, hogy az ügyfél közvetlen látótávolságon belül a szervezet helyszíni AD-tartományvezérlőjére irányítsa, ami azt jelenti, hogy a hibrid Azure AD-csatlakozás csak akkor sikeres, ha az ügyfél csatlakozik a szervezet intranetjéhez.
   - A szervezet az Azure AD seamless `https://autologon.microsoftazuread-sso.com` `https://aadg.windows.net.nsatc.net` single sign-on, vagy nem jelennek meg az eszköz IE intranet-beállításokat, és **a frissítések engedélyezése állapotsor parancsfájlon keresztül** nincs engedélyezve az intranetes zónában.
- Nincs bejelentkezve tartományi felhasználóként

   ![Munkahelyi csatlakozás a Windows rendszerhez](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   Ennek több oka is lehet:

   - A bejelentkezett felhasználó nem tartományi felhasználó (például helyi felhasználó). A hibrid Azure AD-csatlakozás az down-level eszközökön csak a tartományi felhasználók számára támogatott.
   - Az ügyfél nem tud tartományvezérlőhöz csatlakozni.    
- Elérte a kvótát

    ![Munkahelyi csatlakozás a Windows rendszerhez](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- A szolgáltatás nem válaszol 

    ![Munkahelyi csatlakozás a Windows rendszerhez](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Az állapotadatokat az eseménynaplóban is megtalálhatja: **Alkalmazások és szolgáltatások naplója\Microsoft-Workplace Join**
  
**A sikertelen hibrid Azure AD-csatlakozás leggyakoribb okai a következők:** 

- A számítógép nem csatlakozik a szervezet belső hálózatához vagy a vpn-hez, amely kapcsolatban áll a helyszíni AD-tartományvezérlővel.
- Helyi számítógépfiókkal van bejelentkezve a számítógépre. 
- Szolgáltatáskonfigurációs problémák: 
   - Az AD FS-kiszolgáló nincs beállítva a **WIAORMULTIAUTHN támogatásához.** 
   - A számítógép erdője nem rendelkezik olyan Service Connection Point-objektummal, amely az ellenőrzött tartománynévre mutat na az Azure AD-ben 
   - Vagy ha a tartomány felügyelt, akkor a zökkenőmentes egyszeri bejelentkezés nem volt konfigurálva vagy működik.
   - A felhasználó elérte az eszközök felső határát. 

## <a name="next-steps"></a>További lépések

További kérdések az [eszközkezelésről szóló gyakori kérdések ben](faq.md)  

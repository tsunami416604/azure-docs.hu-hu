---
title: 'Az Azure AD Connect: Objektum-szinkronizálás hibaelhárítása |} Microsoft Docs'
description: Ez a témakör ismerteti a problémák elhárítása hibaelhárítási feladat szinkronizációs lépéseit.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: billmath
ms.openlocfilehash: 54ae18b9a802fe078d307f4d36400adf806b233f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Objektum-szinkronizálás és az Azure AD Connect-szinkronizálás hibaelhárítása
Ez a dokumentum ismerteti a problémák elhárítása hibaelhárítási feladat szinkronizációs lépéseit.

## <a name="troubleshooting-task"></a>A feladat hibaelhárítása
Az Azure Active Directory (AAD) telepítési csatlakozás verziója 1.1.749.0 vagy újabb rendszerre, használja a hibaelhárítási feladat a varázsló objektum szinkronizálási kapcsolatos problémák elhárítása. Korábbi verzióihoz adjon hibaelhárítása leírtak manuálisan [Itt](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>A hibaelhárítási feladat futtatása a varázsló
A varázsló a hibaelhárítási feladat futtatásához a következő lépésekkel:

1.  Nyisson meg egy új Windows PowerShell-munkamenetben az Azure AD Connect-kiszolgáló a Futtatás rendszergazdaként lehetőséggel.
2.  Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.
3.  Az Azure AD Connect varázsló elindításához.
4.  Nyissa meg a további feladatok lapot, válassza ki a hibaelhárítás, és kattintson a Tovább gombra.
5.  A hibaelhárítás lapon kattintson az Indítás gombra a PowerShellben indítsa el a hibaelhárítási menü.
6.  A főmenühöz tartozik válassza ki a szinkronizációs hibaelhárítása.

### <a name="troubleshooting-input-parameters"></a>Hibaelhárítási bemeneti paraméterek
A következő bemeneti paraméterek a hibaelhárítási feladata van szükség:
1.  **Az objektum megkülönböztető név** – hibaelhárítás igénylő objektum megkülönböztető név
2.  **AD-összekötő neve** – a neve az AD-erdőben, ahol a fenti objektum található.
3.  Az Azure AD bérlő globális rendszergazda hitelesítő adatait ![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményének megismeréséhez
A hibaelhárítási feladat a következő műveleteket hajtja végre:

1.  Egyszerű felhasználónév eltérés észleli, ha az objektum van-e szinkronizálva az Azure Active Directoryhoz
2.  Ellenőrizze, hogy ha objektum szűrt miatt tartomány szűrése
3.  Ellenőrizze, hogy ha objektum szűrt miatt szervezeti egységek szűrése

Ez a szakasz a többi adott a tevékenység által visszaadott eredmények ismerteti. Minden esetben a feladat elemzi a probléma megoldása érdekében javasolt művelet követ.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Egyszerű felhasználónév eltérés észleli, ha objektum van-e szinkronizálva az Azure Active Directoryhoz
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Az Azure AD-bérlő nem ellenőrzi a UPN-utótag
Ha a UserPrincipalName (UPN) / másodlagos bejelentkezési azonosító utótag nem ellenőrzi a rendszer az Azure AD bérlővel, majd az alapértelmezett tartomány neve "onmicrosoft.com" Azure Active Directory lecseréli az UPN-utótagot.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Egyszerű Felhasználónévi utótagot módosítása az összevont tartományt egy másik összevont tartományt
Az Azure Active Directory nem teszi lehetővé a szinkronizálás a UserPrincipalName (UPN) / másodlagos bejelentkezési azonosító utótagot az változásait egy összevont tartományt egy másik összevont tartományt. Ez a tartomány, az Azure AD bérlővel ellenőrzése és a hitelesítési típus lehet külső vonatkozik.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Az Azure AD bérlő DirSync-szolgáltatás "SynchronizeUpnForManagedUsers" le van tiltva
Ha az Azure AD-bérlő DirSync "SynchronizeUpnForManagedUsers" funkció le van tiltva, Azure Active Directory nem engedélyezi a szinkronizálási frissítéseket UserPrincipalName/másodlagos bejelentkezési azonosító licenccel rendelkező felhasználói fiókok a felügyelt hitelesítéssel.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objektum szűrt miatt tartomány szűrése
### <a name="domain-is-not-configured-to-sync"></a>Tartomány beállítása nem szinkronizálni kívánt
Objektum nem része nincs konfigurálva tartomány miatt. Az alábbi példában az objektum szinkronban hatókör szűrt a tartományban, amelyhez tartozik, mint a szinkronizálás.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Tartomány úgy van konfigurálva, hogy szinkronizálni, de hiányzik a futtatási profilok/Futtatás lépései
Objektum hatókörén kívül hiányzik a tartomány fut profilok/Futtatás lépéseket. Az alábbi példában célja szinkronban hatókör, a tartományban, amelyhez tartozik, a teljes importálás futtatási profiljának futtatási lépéseket hiányzik.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch6.png)

### <a name="object-is-filtered-due-to-ou-filtering"></a>Objektum szűrt miatt szervezeti egységek szűrése
Az objektum szinkronban hatókör OU szűrési konfigurációja miatt. Az alábbi példában az objektum tartozik OU NoSync, DC = = bvtadwbackdc, DC = com.  A szervezeti egység nem szerepel a szinkronizálás hatókörében.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch7.png)

## <a name="html-report"></a>HTML-jelentés
Az objektum elemzése, mellett a hibaelhárítási feladat is, ami egy HTML-jelentést, amely minden ismert arról az objektumról. A HTML-jelentést megosztható támogatási csoport ehhez a további hibaelhárítási, ha szükséges.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch8.png)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

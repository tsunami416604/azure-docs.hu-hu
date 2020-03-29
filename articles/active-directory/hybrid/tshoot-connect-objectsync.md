---
title: 'Azure AD Connect: Az objektumok szinkronizálásának hibaelhárítása | Microsoft dokumentumok'
description: Ez a témakör az objektum-szinkronizálással kapcsolatos problémák hibaelhárítási lépéseit ismerteti a hibaelhárítási feladattal.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e56d4d94e38e5095ef2223d0cc2875cbf1dcd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64919120"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Objektumszinkronizálás hibaelhárítása Azure AD Connect-szinkronizálással
Ez a cikk az objektum-szinkronizálással kapcsolatos problémák hibaelhárítási lépéseit ismerteti a hibaelhárítási feladat használatával. Ha meg szeretné tudni, hogyan működik a hibaelhárítás az Azure Active Directory (Azure AD) Connect szolgáltatásban, tekintse meg [ezt a rövid videót.](https://aka.ms/AADCTSVideo)

## <a name="troubleshooting-task"></a>Feladatelhárítási feladat
Az 1.1.749.0-s vagy újabb verziójú Azure AD Connect üzemelő példánya esetében használja a varázsló hibaelhárítási feladatát az objektumszinkronizálási problémák elhárításához. A korábbi verziók esetében kérjük, hogy manuálisan hárítsa el az [itt](tshoot-connect-object-not-syncing.md)leírtak szerint a hibákat.

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>A hibaelhárítási feladat futtatása a varázslóban
A hibaelhárítási feladat varázslóban való futtatásához hajtsa végre az alábbi lépéseket:

1.  Nyisson meg egy új Windows PowerShell-munkamenetet az Azure AD Connect kiszolgálón a Futtatás rendszergazdaként beállítással.
2.  Futtassa `Set-ExecutionPolicy RemoteSigned` a vagy `Set-ExecutionPolicy Unrestricted`a ot.
3.  Indítsa el az Azure AD Connect varázslót.
4.  Nyissa meg a További feladatok lapot, válassza a Hibaelhárítás lehetőséget, és kattintson a Tovább gombra.
5.  A Hibaelhárítás lapon kattintson az Indítás gombra a PowerShell hibaelhárítási menüjének elindításához.
6.  A főmenüben válassza az Objektum-szinkronizálás hibaelhárítása lehetőséget.
![Objektumszinkronizálás – problémamegoldás](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Bemeneti paraméterek hibáinak elhárítása
A hibaelhárítási feladatnak a következő bemeneti paraméterekre van szüksége:
1.  **Objektum megkülönböztető neve** – Ez annak az objektumnak a megkülönböztető neve, amelyet hibaelhárításra kell
2.  **AD összekötő neve** – Ez annak az AD erdőnek a neve, ahol a fenti objektum található.
3.  Az Azure AD globális ![rendszergazdai hitelesítő adatai globális rendszergazdai hitelesítő adatok](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményeinek megismerése
A hibaelhárítási feladat a következő ellenőrzéseket hajtja végre:

1.  Az upn-eltérés észlelése, ha az objektum szinkronizálva van az Azure Active Directoryval
2.  Annak ellenőrzése, hogy az objektum szűrése tartományszűrés miatt történik-e
3.  Annak ellenőrzése, hogy az objektum szűrve van-e a szervezeti egység szűrése miatt
4.  Annak ellenőrzése, hogy csatolt postaláda miatt blokkolva van-e az objektumszinkronizálás
5. Annak ellenőrzése, hogy az objektum dinamikus terjesztési csoport-e, amelyet nem kellene szinkronizálni

A szakasz többi része a feladat által visszaadott konkrét eredményeket ismerteti. A feladat minden esetben elemzést ad, amelyet a probléma megoldásához javasolt műveletek követnek.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Az upn-eltérés észlelése, ha az objektum szinkronizálva van az Azure Active Directoryval
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Az UPN-utótag nem ellenőrzött az Azure AD-bérlővel
Ha a UserPrincipalName (UPN)/Alternate Login Iutófix nincs ellenőrizve az Azure AD-bérlővel, akkor az Azure Active Directory lecseréli az UPN-utótagokat az alapértelmezett "onmicrosoft.com" tartománynévre.

![Az Azure AD felváltja az UPN-t](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>UPN-utótag módosítása egyik összevont tartományról egy másik összevont tartományra
Az Azure Active Directory nem teszi lehetővé a UserPrincipalName (UPN)/Alternate Login ID utótag szinkronizálását egyik összevont tartományról egy másik összevont tartományra. Ez az Azure AD-bérlővel ellenőrzött és a hitelesítési típust összevontként rendelkező tartományokra vonatkozik.

![Nincs upn-szinkronizálás egyik összevont tartományból a másikba](media/tshoot-connect-objectsync/objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Az Azure AD-bérlődirSync szolgáltatása "SyncUpnForManagedUsers" le van tiltva
Ha az Azure AD-bérlődirSync szolgáltatás "SyncUpnForManagedUsers" le van tiltva, az Azure Active Directory nem engedélyezi a szinkronizálási frissítéseket UserPrincipalName/alternatív bejelentkezési azonosító felügyelt hitelesítéssel rendelkező licenccel rendelkező felhasználói fiókok.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Az objektum szűrése tartományszűrés miatt történik
### <a name="domain-is-not-configured-to-sync"></a>A tartomány nincs szinkronban
Az objektum hatóköre nem tartozik a tartományható tartományhatóhatóhoz, mivel nincs konfigurálva. Az alábbi példában az objektum nincs szinkronhatókörben, mivel a tartomány, amelyhez tartozik, szűrést végez a szinkronizálásból.

![A tartomány nincs szinkronban](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>A tartomány szinkronizálásra van konfigurálva, de hiányoznak a futtatási profilok/futtatási lépések
Az objektum hatóköre kívül esik, mivel a tartományból hiányoznak a futtatási profilok/futtatási lépések. Az alábbi példában az objektum nincs szinkronhatókörben, mivel a tartomány, amelyhez tartozik, hiányzik a Teljes importálás futtatási profil futtatási lépéseiből.
![hiányzó futtatási profilok](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Az objektum szűrése szervezeti egység szűrése miatt történik
Az objektum a szervezetiegység-szűrési konfiguráció miatt nem jelenik meg a szinkronizáláshatókörén. Az alábbi példában az objektum az OU=NoSync,DC=bvtadwbackdc,DC=com objektumhoz tartozik.  Ez a szervezeti egység nem szerepel a szinkronizálási hatókörben.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Csatolt postaláda-probléma
A csatolt postaládát egy másik megbízhatófiók-erdőben található külső főfiókhoz kell társítani. Ha nincs ilyen külső főfiók, majd az Azure AD Connect nem szinkronizálja a felhasználói fiók megfelel az Exchange-erdőben az Azure AD bérlőhöz csatolt postaládának.</br>
![Csatolt postaláda](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Dinamikus terjesztési csoport kiadása
A helyszíni Active Directory és az Azure Active Directory közötti különböző különbségek miatt az Azure AD Connect nem szinkronizálja a dinamikus terjesztési csoportokat az Azure AD-bérlővel.

![Dinamikus terjesztési csoport](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML-jelentés
Az objektum elemzése mellett a hibaelhárítási feladat egy HTML-jelentést is létrehoz, amely mindent tartalmaz, ami az objektumról ismert. Ez a HTML-jelentés megosztható a támogatási csapattal, hogy szükség esetén további hibaelhárítást végezhessenek.

![HTML-jelentés](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

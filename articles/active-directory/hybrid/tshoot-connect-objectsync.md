---
title: 'Az Azure AD Connect: Objektum-szinkronizálás hibaelhárítása |} A Microsoft Docs'
description: Ez a témakör ismerteti a problémák elhárítása a hibaelhárítási feladat használatával felügyeltobjektum-szinkronizációs.
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
ms.date: 05/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c810e121b751d098bd0fbda09db51c031f003460
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46314999"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálási objektum-szinkronizálás hibaelhárítása
Ez a cikk lépéseit kapcsolatos hibák elhárításának felügyeltobjektum-szinkronizációs a hibaelhárítási feladat használatával. Az az Azure Active Directory (Azure AD) Connect hibaelhárítási kipróbálásához, tekintse meg a [Ez a rövid videó](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>A feladat hibaelhárítása
Az Azure ad központi telepítési csatlakozzon 1.1.749.0 verzióját, vagy újabb verziója szükséges, használja a hibaelhárítási feladat a varázsló objektumok szinkronizálási problémáinak hibaelhárítása. Régebbi verziók esetében adjon hibaelhárítása leírtak szerint manuálisan [Itt](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>A hibaelhárítási feladat futtatása a varázsló
A hibaelhárítási feladat futtatása a varázsló, hajtsa végre az alábbi lépéseket:

1.  Nyisson meg egy új Windows PowerShell-munkamenetet a az Azure AD Connect-kiszolgáló a Futtatás rendszergazdaként lehetőséggel.
2.  Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.
3.  Az Azure AD Connect varázsló elindításához.
4.  Nyissa meg a további feladatok lapot, válassza ki a hibaelhárítás, és kattintson a Tovább gombra.
5.  A hibaelhárítás lapon kattintson az indítás, indítsa el a hibaelhárítási menü a PowerShellben.
6.  A fő menüjéből válassza az objektum-szinkronizálás hibáinak elhárítása.
![](media\tshoot-connect-objectsync\objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Hibaelhárítás a bemeneti paraméterek
A következő bemeneti paraméterek a hibaelhárítási feladat által van szükség:
1.  **Az objektum megkülönböztető nevét** – Ez a hibaelhárítási igénylő az objektum megkülönböztető nevét
2.  **AD-összekötő neve** – Ez a neve az AD-erdőhöz, amelyben a fenti objektum található.
3.  Az Azure AD bérlő globális rendszergazdai hitelesítő adatait ![](media\tshoot-connect-objectsync\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményeivel
A hibaelhárítási feladat az alábbi ellenőrzéseket hajtja végre:

1.  Egyszerű felhasználónév eltérés észleli, ha az Azure Active Directory szinkronizálva van az objektum
2.  Ellenőrizze az objektum tartomány szűrés miatt van szűrve
3.  Ellenőrizze az objektum van szűrve miatt szervezeti egységek szűrése
4.  Ellenőrizze, hogy ha felügyeltobjektum-szinkronizációs hivatkozott postafiókkal miatt le van tiltva
5. Ellenőrizze, hogy az objektum dinamikus terjesztési csoport, amely nem lehet szinkronizálni

Ez a szakasz a többi adott a tevékenység által visszaadott eredmények ismerteti. Minden esetben a feladat elemzi a probléma megoldása érdekében ajánlott műveletek követ.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>UPN eltérés észleli, ha az Azure Active Directory szinkronizálva van a objektum
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Az Azure AD-bérlő nem ellenőrzött UPN-utótagja
Ha a UserPrincipalName (UPN) / az Azure AD-bérlőben nem ellenőrzi a másodlagos bejelentkezési azonosító utótagot, majd az Azure Active Directory UPN-utótagot cseréli az alapértelmezett tartomány neve "onmicrosoft.com".

![](media\tshoot-connect-objectsync\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Egyszerű Felhasználónévi utótagot összevont tartományok egy másik, összevont tartomány módosítása
Az Azure Active Directory nem teszi lehetővé a szinkronizálás a UserPrincipalName (UPN) / másik bejelentkezési azonosító utótag módosítása egy összevont tartományból egy másikba összevont tartományban. Ez vonatkozik, amelyek ellenőrzése az Azure AD-bérlőben, és amelyek a hitelesítési típus szerint az összevont tartományokban.

![](media\tshoot-connect-objectsync\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Az Azure AD bérlő a DirSync szolgáltatás "SynchronizeUpnForManagedUsers" le van tiltva
Ha az Azure AD-bérlő a DirSync szolgáltatás "SynchronizeUpnForManagedUsers" le van tiltva, az Azure Active Directory nem engedélyezi a szinkronizálási frissítéseket UserPrincipalName/másodlagos bejelentkezési azonosító felügyelt hitelesítéssel licenccel rendelkező felhasználói fiókok esetében.

![](media\tshoot-connect-objectsync\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objektum tartomány szűrés miatt van szűrve.
### <a name="domain-is-not-configured-to-sync"></a>Nincs konfigurálva a tartományhoz való szinkronizálás
Objektum nem miatt a tartomány nincs konfigurálva. Az alábbi példában az objektum szinkronban hatókör vannak leszűrve a tartományban, amelyhez tartozik, mint a szinkronizálás.

![](media\tshoot-connect-objectsync\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Tartomány úgy van konfigurálva, hogy szinkronizálhatók, de hiányzik a futtatási profilok és futtató lépések
Objektum hatókörén kívül hiányzik a tartomány futtatja a profilok és futtató lépéseket. Az alábbi példában objektum szinkronban hatókör, a tartományban, amelyhez tartozik, futtatási lépéseket hiányzik a futtatási profil teljes importálásra.
![](media\tshoot-connect-objectsync\objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Objektum szűrt miatt szervezeti egységek szűrése
Az objektum nincs szinkronban hatókör OU-szűrés konfiguráció miatt. Az alábbi példában az objektum tartozik OU NoSync, DC = = bvtadwbackdc, DC = com.  Adatszinkronizálás hatóköre nem szerepel az ebbe a szervezeti Egységbe.</br>

![SZERVEZETI EGYSÉG](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>A csatolt postaláda-probléma
Hivatkozott postafiókkal kellene lennie a társított megbízható fiók egy másik erdőben található külső fő fiókkal. Ha nincs ilyen külső fő fiók, akkor az Azure AD Connect nem szinkronizálja a felhasználói fiók megfelel a hivatkozott postafiókkal az Exchange-erdő, az Azure AD-bérlőhöz.</br>
![Hivatkozott postafiókkal](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Dinamikus terjesztési csoport probléma
Helyszíni különböző eltérései miatt az Active Directory és az Azure Active Directory, az Azure AD Connect nem szinkronizálja a dinamikus terjesztésipont-csoportok az Azure AD-bérlőhöz.

![Dinamikus terjesztési csoport](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML-jelentés
Az objektum elemzése, mellett a hibaelhárítási feladat is egy HTML-jelentést, amely mindent az objektumra vonatkozó ismert állít elő. Ez a jelentés HTML megoszthatók a támogatási csapat számára a do további hibaelhárítási, ha szükséges.

![](media\tshoot-connect-objectsync\objsynch8.png)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

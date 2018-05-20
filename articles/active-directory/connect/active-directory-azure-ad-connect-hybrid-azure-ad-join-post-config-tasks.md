---
title: 'Az Azure AD Connect: Hibrid az Azure AD join utáni konfigurációs feladatok |} Microsoft Docs'
description: Ez a dokumentum részletek utáni konfigurációs feladatok szükséges, végezze el a hibrid az Azure AD join
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: samueld
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: anandyadavmsft
ms.openlocfilehash: 02f1cbd1f2b8f7b0bec2f8016a300ede1d6f0439
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Az Azure AD hibrid illesztési utáni konfigurációs feladatok

A szervezet az Azure AD hibrid illesztési konfigurálása az Azure AD Connect futtatása után van néhány további lépéseket, hogy el kell végeznie a telepítő által véglegesítéséhez.  Csak azokat a lépéseket, amelyek érvényesek a eszközök végez.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurálni az ellenőrzött bevezetés (nem kötelező)
Automatikusan futó Windows 10 és Windows Server 2016 minden tartományhoz csatlakozó eszközök regisztrálása az Azure AD, ha az összes beállítási lépéséhez befejeződött. Az automatikus regisztráció helyett ellenőrzött bevezetés tetszés szerint használhatja a csoportházirend szelektív letiltása és engedélyezése automatikus bevezetése.  A csoportházirend-kell állítani a további konfigurációs lépések: Azure AD elindítása előtt
* Hozzon létre egy csoportházirend-objektumot az Active Directoryban.
* Nevezze el (például-hibrid az Azure AD join).
* Szerkesztés & Ugrás: számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows-összetevők > Eszközregisztrációhoz.

>[!NOTE]
>A 2012R2 a házirend-beállítások erővel **számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows-összetevők > munkahelyi csatlakoztatás > automatikusan munkahelyi csatlakoztatás ügyfélszámítógépek**

* Ez a beállítás letiltása: eszközként regisztrálja a tartományhoz csatlakozó számítógépek.
* Vonatkoznak, és kattintson az OK gombra.
* Kapcsolja a csoportházirend-Objektumot a megfelelő helyre (szervezeti egység, biztonsági csoport, vagy az összes eszköz tartományhoz).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Hálózati eszköz regisztrációs végpontok konfigurálása
Győződjön meg arról, hogy a következő URL-címekkel érhető el az Azure AD, a regisztrálási a szervezeti hálózaton belüli számítógépek:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Megvalósítása WPAD a Windows 10-eszközök
Ha a szervezet az interneten egy kimenő proxyn keresztül fér hozzá, valósítja meg a webes Proxy automatikus felderítését a lekérdezés (WPA) lehetővé teszik a Windows 10 számítógépek regisztrálása az Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. A szolgáltatáskapcsolódási pont konfigurálása az Azure AD Connect nem konfigurált bármely erdőben 

A szolgáltatáskapcsolódási pont (SCP) az automatikus regisztráció az eszközök által használt Azure AD bérlő adatait tartalmazza.  ConfigureSCP.ps1, az Azure AD Connect webhelyről letöltött PowerShell-parancsprogram futtatásához.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Bármely összevonási szolgáltatást, amely nem lett konfigurálva az Azure AD Connect konfigurálása

Ha a szervezet jelentkezzen be az Azure AD összevonási szolgáltatást használ, a jogcímszabályok az Azure AD függőentitás-megbízhatóságot az eszközhitelesítés engedélyeznie kell. Ha az AD FS összevonási használ, folytassa a [AD FS-súgó](https://aka.ms/aadrptclaimrules) jogcímszabályok létrehozásához. Ha egy nem Microsoft-összevonási megoldás használata esetén ezt a szolgáltatót tud felvilágosítást nyújtani.  

>[!NOTE]
>Ha Windows-kezelés régebbi eszközöket, a szolgáltatás támogatnia kell a authenticationmethod és wiaormultiauthn jogcímek kiállítása az Azure AD-megbízhatóság kérelmek fogadására. Az AD FS-ben adja hozzá egy kiadási átalakítási szabálykészlet, amely folyamaton keresztül a hitelesítési módszert.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Az Azure AD zökkenőmentes egyszeri bejelentkezés a Windows-kezelés régebbi eszközök engedélyezése

A szervezet Jelszókivonat-szinkronizálást vagy átmenő hitelesítést használ az Azure AD bejelentkezés, ha adott bejelentkezési mód Windows régebbi eszközök hitelesítését, hogy engedélyezze az Azure AD zökkenőmentes SSO: https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Szabály beállítása az Azure AD-kezelés régebbi Windows-eszközök

Régebbi Windows-eszközök regisztrálásához szükséges győződjön meg arról, hogy az Azure AD-házirend lehetővé teszi a felhasználók regisztrálják eszközeiket. 

* Jelentkezzen be a fiók az Azure portálon.
* Keresse fel: az Azure Active Directory > eszközök > eszközbeállításaihoz
* Állítsa be a "Felhasználók előfordulhat, hogy regisztrálják az eszközeiket az Azure ad-val" az összes.
* Kattintson a Save (Mentés) gombra.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Az Azure AD-végpont hozzáadása a régebbi Windows-eszközök

Az Azure AD eszköz hitelesítési végpont hozzáadása a Helyi Intranet zóna tanúsítványokra elkerülésére, az eszközök hitelesítésekor Windows régebbi verziójú eszközökön: https://device.login.microsoftonline.com 

Ha használ [zökkenőmentes SSO](https://aka.ms/hybrid/sso), is engedélyezze a "Állapot parancsfájllal történő frissítésének engedélyezése", hogy zónára, és adja hozzá a következő végpontot: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Telepítse a Microsoft munkahelyi csatlakoztatás régebbi Windows-eszközökön

Ezt a telepítőt egy ütemezett feladatot az eszköz rendszere, a felhasználó környezetében futó hoz létre. A feladat lesz kiváltva, ha a felhasználó bejelentkezik a Windowsba. A feladat csendes csatlakozik az eszköz hitelesítése az integrált Windows-hitelesítés használata után a felhasználói hitelesítő adatokat az Azure AD-val. A letöltőközpontból jelenleg https://www.microsoft.com/en-us/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. A csoportházirend lehetővé teszi az eszközök regisztrációja

* Hozzon létre egy csoportházirend-objektumot az Active Directory –, ha nem hozott létre.
* Nevezze el (például-hibrid az Azure AD join).
* Ugrás a & szerkesztése: számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows-összetevők > eszközök regisztrációja
* A következők engedélyezése: Eszközként regisztrálja a tartományhoz csatlakozó számítógépek
* Vonatkoznak, és kattintson az OK gombra.
* Kapcsolja a csoportházirend-Objektumot a megfelelő helyre (szervezeti egység, biztonsági csoport, vagy az összes eszköz tartományhoz).

>[!NOTE]
>A 2012R2 a házirend-beállítások erővel **számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows-összetevők > munkahelyi csatlakoztatás > automatikusan munkahelyi csatlakoztatás ügyfélszámítógépek**

## <a name="next-steps"></a>További lépések
[Konfigurálja az eszközök visszaírásához.](./active-directory-aadconnect-feature-device-writeback.md)

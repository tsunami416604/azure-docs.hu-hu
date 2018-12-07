---
title: 'Az Azure AD Connect: Hibrid Azure AD join utáni konfigurációs feladatok |} A Microsoft Docs'
description: Ez a dokumentum részletesen szükséges utáni konfigurációs feladatok befejezéséhez a hibrid Azure AD-csatlakozás
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 16fc7f1bb69efe94ce87f213627b78a4afa0fcc2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999230"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás konfigurálása utáni feladatok

Miután futtatta a szervezetben a hibrid Azure AD-csatlakozás konfigurálása az Azure AD Connect, nincsenek néhány további lépéseket kell végrehajtani, hogy a telepítő véglegesítéséhez.  Csak azokat a lépéseket, amelyek az eszközök a alkalmazni végez.

## <a name="1-configure-controlled-rollout-optional"></a>1. Ellenőrzött bevezetését (nem kötelező) konfigurálása
Minden tartományhoz csatlakoztatott eszközök a Windows 10 és Windows Server 2016 fut, automatikusan regisztrálja az Azure AD, ha az összes konfigurációs lépés befejeződött. Ha inkább ellenőrzött bevezetését, mint az automatikus regisztráció, a csoportházirend segítségével szelektív engedélyezése vagy letiltása az automatikus bevezetési.  A csoportházirendet kell állítani a további konfigurációs lépések: az Azure AD elindítása előtt
* Hozzon létre egy csoportházirend-objektumot az Active Directoryban.
* Nevezze el azt (például-hibrid Azure AD-csatlakozás).
* Szerkeszthet, és nyissa meg: számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows Components > regisztrálni az eszközt.

>[!NOTE]
>A 2012 R2 a házirend-beállítások vannak **számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows Components > a munkahelyi csatlakoztatás > Automatikus munkahelyi csatlakozás ügyfélszámítógépek**

* Ez a beállítás letiltása: eszközként regisztrálja a tartományhoz csatlakoztatott számítógépeket.
* Érvényes, és kattintson az OK gombra.
* Kapcsolja a csoportházirend-Objektumot a megfelelő helyre (szervezeti egység, biztonsági csoportot, vagy az összes eszköz tartományhoz).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Hálózati eszköz regisztrációs végpontok konfigurálása
Győződjön meg arról, hogy az Azure AD regisztrációs a szervezeti hálózaton belüli számítógépek gazdagépcsoportról elérhetőek-e a következő URL-címek:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. WPAD Windows 10-es eszközök megvalósítása
Ha a szervezet az interneten keresztül egy kimenő proxy fér hozzá, megvalósítása a webes Proxy automatikus felderítési (WPAD) lehetővé teszik a Windows 10-es számítógépek regisztrálása az Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Minden olyan erdőben, az Azure AD Connect nem konfigurált a szolgáltatáskapcsolódási pont konfigurálása 

A szolgáltatáskapcsolódási pont (SCP) az automatikus regisztráció az eszközök által használt Azure AD bérlő adatait tartalmazza.  ConfigureSCP.ps1 letöltött az Azure AD Connect PowerShell-szkript futtatásához.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Minden olyan összevonási szolgáltatás, amely nem lett konfigurálva az Azure AD Connect konfigurálása

Ha a szervezet jelentkezzen be az Azure AD összevonási szolgáltatást használ, a függő entitás megbízhatóságához az Azure AD-ben a eszközhitelesítés lehetővé kell tennie. Ha az AD FS összevonási használ, lépjen a [AD FS-súgó](https://aka.ms/aadrptclaimrules) jogcímszabályok létrehozásához. Egy nem Microsoft-összevonási megoldás használatakor forduljon a szolgáltatóhoz útmutatást.  

>[!NOTE]
>Ha Windows régebbi verziójú eszközök, a szolgáltatás támogatnia kell az authenticationmethod és wiaormultiauthn jogcímet kiállító, ha az Azure AD-megbízhatóság kéréseket fogad. Az AD FS-ben hozzá kell adnia egy kiállítási átalakítási szabályt, amely pass révén a hitelesítési módszert.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Az Azure AD közvetlen egyszeri bejelentkezés a Windows régebbi verziójú eszközök engedélyezése

Ha a szervezete a Jelszókivonat-szinkronizálás és átmenő hitelesítés az Azure AD-bejelentkezés, az Azure AD közvetlen egyszeri bejelentkezés engedélyezése az adott bejelentkezési mód Windows régebbi verziójú eszközök hitelesítésére: https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Régebbi verziójú Windows-eszközökhöz az Azure AD-szabályzat beállítása

Windows régebbi verziójú eszközök regisztrálása, győződjön meg arról, hogy az Azure AD-szabályzat lehetővé teszi a felhasználóknak eszközöket regisztrálni kell. 

* Jelentkezzen be a fiók az Azure Portalon.
* Nyissa meg: az Azure Active Directory > eszközök > eszközbeállítások
* Állítsa be az "A felhasználók regisztrálhatják eszközeiket az Azure ad-vel" minden.
* Kattintson a Save (Mentés) gombra.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Az Azure AD-végpont hozzáadása Windows régebbi verziójú eszközök

Az Azure AD-eszközök hitelesítési végpont hozzáadása a helyi intranetes zóna elkerülése érdekében utasításokat a tanúsítvány hitelesíti az eszközöket a Windows régebbi verziójú eszközökön: https://device.login.microsoftonline.com 

Ha használ [közvetlen egyszeri bejelentkezés](how-to-connect-sso.md), is engedélyezze a zóna a "Állapot parancsfájllal történő frissítésének engedélyezése", és adja hozzá a következő végpontot: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Telepítse a Microsoft munkahelyi csatlakoztatás Windows régebbi verziójú eszközökön

Ez a telepítő az eszköz a rendszer a felhasználó környezetében futó ütemezett feladatot hoz létre. A feladat akkor aktiválódik, ha a felhasználó bejelentkezik a Windows. A feladat csendes csatlakoztatja az eszközt az Azure AD-felhasználói hitelesítő adatok hitelesítése az integrált Windows-hitelesítés használata után. Jelenleg a letöltőközpontból https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Az eszköz regisztrációjának engedélyezése a csoportházirend konfigurálásához

* Hozzon létre egy csoportházirend-objektumot az Active Directory – Ha még nem hozott létre.
* Nevezze el azt (például-hibrid Azure AD-csatlakozás).
* Szerkeszthet, és nyissa meg: számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows Components > az eszköz regisztrálása
* Engedélyezése: Tartományhoz csatlakoztatott számítógépeket eszközként regisztrálja.
* Érvényes, és kattintson az OK gombra.
* Kapcsolja a csoportházirend-Objektumot a megfelelő helyre (szervezeti egység, biztonsági csoportot, vagy az összes eszköz tartományhoz).

>[!NOTE]
>A 2012 R2 a házirend-beállítások vannak **számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows Components > a munkahelyi csatlakoztatás > Automatikus munkahelyi csatlakozás ügyfélszámítógépek**

## <a name="next-steps"></a>További lépések
[Eszközvisszaírás konfigurálása](how-to-connect-device-writeback.md)

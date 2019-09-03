---
title: 'Azure AD Connect: Hibrid Azure AD JOIN utáni konfigurációs feladatok | Microsoft Docs'
description: Ez a dokumentum részletesen ismerteti a hibrid Azure AD-csatlakozás befejezéséhez szükséges konfigurációs feladatokat.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e3267792f27a170efa26cc4267d1b25045a099
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231238"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás konfigurálása utáni feladatok

Miután futtatta a Azure AD Connectt a hibrid Azure AD-csatlakozáshoz való konfigurálásához, néhány további lépést is végre kell hajtania a beállítás véglegesítéséhez.  Csak azokat a lépéseket hajtsa végre, amelyek alkalmazhatók az eszközökre.

## <a name="1-configure-controlled-rollout-optional"></a>1. Vezérelt bevezetés konfigurálása (nem kötelező)
A Windows 10 és a Windows Server 2016 rendszerű összes tartományhoz csatlakoztatott eszköz automatikusan regisztrálja az Azure AD-t az összes konfigurációs lépés befejezése után. Ha az automatikus regisztráció helyett inkább az ellenőrzött bevezetést részesíti előnyben, a csoportházirend segítségével szelektív módon engedélyezheti vagy letilthatja az automatikus bevezetést.  Ezt a csoportházirendet a további konfigurációs lépések megkezdése előtt be kell állítani:
* Hozzon létre egy csoportházirend-objektumot a Active Directory.
* Nevezze el (a hibrid Azure AD-csatlakozás).
* Szerkesztés és ugrás:  Számítógép-konfiguráció > házirendek > Felügyeleti sablonok > Windows-összetevők > eszköz regisztrálása.

>[!NOTE]
>A 2012R2 a **Számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows-összetevők > Workplace JOIN > automatikusan munkahelyi csatlakozás ügyfélszámítógépeken**

* A beállítás engedélyezése:  Tartományhoz csatlakoztatott számítógépek regisztrálása eszközként.
* Alkalmazza, és kattintson az OK gombra.
* Csatolja a GPO-t az Ön által választott helyhez (szervezeti egység, biztonsági csoport vagy a tartományhoz az összes eszköz esetében).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. A hálózat konfigurálása eszköz-regisztrációs végpontokkal
Győződjön meg arról, hogy az alábbi URL-címek elérhetők a szervezeti hálózaton belüli számítógépekről az Azure AD-ba való regisztráláshoz:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. WPAD implementálása Windows 10-es eszközökhöz
Ha a szervezet kimenő proxyn keresztül éri el az internetet, implementálja a webproxy automatikus felderítését (WPAD) a Windows 10-es számítógépek Azure AD-ba való regisztrálásának engedélyezéséhez.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurálja az SCP-t bármely olyan erdőben, amelyet nem Azure AD Connect konfiguráltak 

A szolgáltatáskapcsolódási pont (SCP) tartalmazza az Azure AD-bérlői adatait, amelyeket az eszközök az automatikus regisztrációhoz fognak használni.  Futtassa a ConfigureSCP. ps1 PowerShell-parancsfájlt, amelyet a Azure AD Connectról töltött le.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Az Azure AD Connect által nem konfigurált összevonási szolgáltatások konfigurálása

Ha a szervezete összevonási szolgáltatást használ az Azure AD-be való bejelentkezéshez, az Azure AD függő entitás megbízhatóságának jogcím-szabályainak engedélyeznie kell az eszköz hitelesítését. Ha AD FS-vel való összevonást használ, lépjen a [AD FS súgóba](https://aka.ms/aadrptclaimrules) a jogcím-szabályok létrehozásához. Ha nem a Microsofttól származó összevonási megoldást használ, útmutatásért forduljon a szolgáltatóhoz.  

>[!NOTE]
>Ha a Windows leállási szintű eszközei vannak, a szolgáltatásnak támogatnia kell a AuthenticationMethod és a wiaormultiauthn jogcímek kiállítását az Azure AD-megbízhatóságra irányuló kérések fogadásakor. AD FS egy olyan kiállítási átalakítási szabályt kell hozzáadnia, amely áthalad a hitelesítési módszeren.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Az Azure AD zökkenőmentes egyszeri bejelentkezésének engedélyezése Windows rendszerű eszközökön

Ha a szervezete jelszó-kivonatoló szinkronizálást vagy átmenő hitelesítést használ az Azure AD-be való bejelentkezéshez, engedélyezze az Azure AD zökkenőmentes SSO-t a bejelentkezési módszerrel a Windows Down-szintű eszközök https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso hitelesítéséhez:. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Az Azure AD-házirend beállítása a Windows rendszerbeli régebbi eszközökhöz

A Windows Down szintű eszközök regisztrálásához meg kell győződnie arról, hogy az Azure AD-házirend lehetővé teszi a felhasználóknak az eszközök regisztrálását. 

* Jelentkezzen be a fiókjába a Azure Portal.
* Ugrás:  Azure Active Directory > eszközök > eszközbeállítások
* A "felhasználók regisztrálhatják eszközeiket az Azure AD-ben" értékre.
* Kattintson a Save (Mentés) gombra.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Azure AD-végpont hozzáadása Windows Down-szintű eszközökhöz

Adja hozzá az Azure AD-alapú hitelesítési végpontot a Windows Down-szintű eszközök helyi intranetes zónájához, hogy elkerülje a tanúsítvány kérését az eszközök hitelesítése során: https://device.login.microsoftonline.com 

Ha [zökkenőmentes SSO](how-to-connect-sso.md)-t használ, a zónában engedélyezze az "állapotjelző sáv frissítéseinek engedélyezése" lehetőséget is, és adja hozzá a következő végpontot: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. A Microsoft Workplace Join telepítése Windows rendszerű régebbi eszközökön

Ez a telepítő egy ütemezett feladatot hoz létre a felhasználó környezetében futó eszköz rendszeren. A feladat akkor aktiválódik, amikor a felhasználó bejelentkezik a Windowsba. A feladat csendesen csatlakoztatja az eszközt az Azure AD-vel a felhasználói hitelesítő adatokkal az integrált Windows-hitelesítés használatával végzett hitelesítés után. A letöltőközpontban a következő címen https://www.microsoft.com/download/details.aspx?id=53554 érhető el:. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Csoportházirend konfigurálása az eszközök regisztrálásának engedélyezéséhez

* Hozzon létre egy csoportházirend-objektumot a Active Directoryban – ha még nincs létrehozva.
* Nevezze el (a hibrid Azure AD-csatlakozás).
* Szerkesztés & ugrás:  Számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows-összetevők > eszköz regisztrációja
* Engedélyezése  Tartományhoz csatlakoztatott számítógépek regisztrálása eszközként
* Alkalmazza, és kattintson az OK gombra.
* Csatolja a GPO-t az Ön által választott helyhez (szervezeti egység, biztonsági csoport vagy a tartományhoz az összes eszköz esetében).

>[!NOTE]
>A 2012R2 a **Számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows-összetevők > Workplace JOIN > automatikusan munkahelyi csatlakozás ügyfélszámítógépeken**

## <a name="next-steps"></a>További lépések
[Az eszköz visszaírási konfigurálása](how-to-connect-device-writeback.md)

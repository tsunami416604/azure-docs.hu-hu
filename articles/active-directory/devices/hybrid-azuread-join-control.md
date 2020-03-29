---
title: A hibrid Azure AD-csatlakozás ellenőrzött érvényesítése – Azure AD
description: Ismerje meg, hogyan ellenőrizhet idotarthatja a hibrid Azure AD-csatlakozás ellenőrzött érvényesítését, mielőtt egyszerre engedélyezne a teljes szervezetben.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049979"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás szabályozott ellenőrzése

Ha az összes előfeltétel a helyén van, a Windows-eszközök automatikusan regisztrálják magukat eszközként az Azure AD-bérlőben. Ezek az eszközidentitások az Azure AD-ben a továbbiakban hibrid Azure AD-csatlakozás. A cikkben tárgyalt fogalmakról további információt az [Azure Active Directory eszközkezelés –](overview.md) bevezetés és a hibrid Azure Active [Directory-csatlakozási megvalósítás megtervezése című](hybrid-azuread-join-plan.md)cikktartalmaz.

Előfordulhat, hogy a szervezetek a hibrid Azure AD-csatlakozás ellenőrzött érvényesítését szeretnék végezni, mielőtt egyszerre engedélyeznék a teljes szervezetben. Ez a cikk ismerteti, hogyan valósíthatja meg a hibrid Azure AD-csatlakozás ellenőrzött érvényesítése.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>A hibrid Azure AD-illesztés ellenőrzött érvényesítése windowsos aktuális eszközökön

Az asztali Windows operációs rendszert futtató eszközök esetében a támogatott verzió a Windows 10 évfordulós frissítése (1607-es verzió) vagy újabb verzió. Ajánlott eljárásként frissítsen a Windows 10 legújabb verziójára.

A hibrid Azure AD-csatlakozás ellenőrzött érvényesítéséhez a Windows aktuális eszközökön a következőket kell tennie:

1. Törölje a Szolgáltatáscsatlakozási pont (SCP) bejegyzést az Active Directoryból (AD), ha létezik
1. Ügyféloldali beállításjegyzék-beállításbeállítás konfigurálása az SCP számára a tartományhoz csatlakozó számítógépeken csoportházirend-objektum (GPO) használatával
1. Ha AD FS-t használ, az AD FS-kiszolgálón az SCP ügyféloldali beállításjegyzék-beállítását is konfigurálnia kell egy csoportházirend-azonosító használatával.  
1. Előfordulhat, hogy az eszközszinkronizálás engedélyezéséhez testre is kell [szabnia a szinkronizálási beállításokat](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) az Azure AD Connectben. 


### <a name="clear-the-scp-from-ad"></a>Az SCP törlése az AD-ből

Az Active Directory szolgáltatások felhasználóifelület-szerkesztője (ADSI-szerkesztés) segítségével módosítsa az SCP-objektumokat az AD-ben.

1. Indítsa el az **ADSI Edit** asztali alkalmazást és a felügyeleti munkaállomásról, illetve egy tartományvezérlőt vállalati rendszergazdaként.
1. Csatlakozzon a tartomány **konfigurációs elnevezési környezetéhez.**
1. Tallózással keresse meg a **CN=Configuration,DC=contoso,DC=com** > CN=Services > **CN=Device Registration Configuration** (Tallózs= Konfiguráció,DC=contoso,DC=com**CN=Services CN=Device**Registration Configuration
1. Kattintson a jobb gombbal a levél objektum **CN = 62a0ff2e-97b9-4513-943f-0d221bd30080** és válassza **tulajdonságok**
   1. **Kulcsszavak kijelölése** az **Attribútumszerkesztő** ablakban, és kattintson a **Szerkesztés** gombra.
   1. Válassza ki az **azureADId** és **az azureADName** értékeit (egyenként), és kattintson az **Eltávolítás** gombra.
1. **AZ ADSI szerkesztésbezárása**


### <a name="configure-client-side-registry-setting-for-scp"></a>Ügyféloldali beállításjegyzék-beállításbeállítás konfigurálása az SCP-hez

Az alábbi példában csoportházirend-objektumot hozhat létre egy beállításjegyzék-beállításbeállítási beállítás telepítéséhez, amely egy SCP-bejegyzést konfigurál az eszközök beállításjegyzékében.

1. Nyisson meg egy Csoportházirend-kezelés konzolt, és hozzon létre egy új csoportházirend-objektumot a tartományban.
   1. Adjon meg egy nevet az újonnan létrehozott gpo-nak (például ClientSideSCP).
1. A csoportházirend-csoport házirendjének szerkesztése és a következő elérési út megkeresése: **Számítógép konfigurációs** > **Preferences** > beállításai**A Windows beállításai beállításjegyzék** > **Registry**
1. Kattintson a jobb gombbal a rendszerleíró adatbázisra, és válassza az **Új** > **beállításjegyzék-elem parancsot.**
   1. Az **Általános** lapon konfigurálja a következő
      1. Művelet: **Frissítés**
      1. Kaptár: **HKEY_LOCAL_MACHINE**
      1. Kulcs elérési útja: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Érték neve: **TenantId**
      1. Érték típusa: **REG_SZ**
      1. Értékadatok: Az Azure AD-példány GUID-azonosítója vagy **címtárazonosítója** (Ez az érték az **Azure** > **Active Directory** > **properties** > directory**azonosítójában**található)
   1. Kattintson **az OK gombra**
1. Kattintson a jobb gombbal a rendszerleíró adatbázisra, és válassza az **Új** > **beállításjegyzék-elem parancsot.**
   1. Az **Általános** lapon konfigurálja a következő
      1. Művelet: **Frissítés**
      1. Kaptár: **HKEY_LOCAL_MACHINE**
      1. Kulcs elérési útja: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Érték neve: **TenantName**
      1. Érték típusa: **REG_SZ**
      1. Értékadatok: Az ellenőrzött **tartománynév,** ha összevont környezetet használ, például Az FS-t. Az ellenőrzött **tartománynév** vagy a onmicrosoft.com `contoso.onmicrosoft.com` tartománynév például felügyelt környezet használata esetén
   1. Kattintson **az OK gombra**
1. Az újonnan létrehozott házirend-objektum szerkesztőjének bezárása
1. Az újonnan létrehozott csoportházirend-kiszolgáló csatolása a kívánt szervezeti egységhez, amely a szabályozott bevezetési sokasághoz tartozó tartományhoz csatlakozó számítógépeket tartalmazza

### <a name="configure-ad-fs-settings"></a>AD FS-beállítások konfigurálása

Ha AD FS-t használ, először a fent említett utasítások alapján kell konfigurálnia az ügyféloldali SCP-t a csoportházirend-kiszolgálónak az AD FS-kiszolgálókhoz való csatolásával. Az SCP-objektum határozza meg az eszközobjektumok jogosultsági forrását. Lehet helyszíni vagy Azure AD. Ha az ügyféloldali SCP konfigurálva van az AD FS, az eszköz objektumok forrása jön létre, mint az Azure AD.

> [!NOTE]
> Ha nem sikerült beállítani az ügyféloldali SCP-t az AD FS-kiszolgálókon, az eszközidentitások forrása helyszíninek minősül. Az ADFS ezután az ADFS-eszközregisztráció "MaximumInactiveDays" attribútumában meghatározott időszak után megkezdi az eszközobjektumok törlését a helyszíni könyvtárból. Az ADFS-eszközregisztrációs objektumok a [Get-AdfsDeviceRegistration parancsmag](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps)használatával találhatók.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>A hibrid Azure AD-csatlakozás ellenőrzött érvényesítése Windows-alapú down-level eszközökön

A Windows-alapú lefelé irányuló eszközök regisztrálásához a szervezeteknek telepíteniük kell a Microsoft Workplace Join programot a Microsoft letöltőközpontban elérhető [nem Windows 10-es számítógépekre.](https://www.microsoft.com/download/details.aspx?id=53554)

A csomagot a [Microsoft Endpoint Configuration Manager](/configmgr/)hez hasonló szoftverelosztó rendszerrel telepítheti. A csomag támogatja a szabványos csendes telepítési lehetőségeket a csendes paraméterrel. A Configuration Manager jelenlegi ága előnyöket kínál a korábbi verziókhoz képest, például a befejezett regisztrációk nyomon követésére.

A telepítő létrehoz egy ütemezett feladatot a rendszeren, amely a felhasználói környezetben fut. A feladat akkor indul el, amikor a felhasználó bejelentkezik a Windows rendszerbe. A feladat az Azure AD-vel való folyamatos csatlakoztatása az Azure AD-vel az Azure AD-vel való hitelesítés után.

Az eszközregisztráció szabályozásához telepítse a Windows Installer csomagot a Windows lefelé irányuló eszközök kijelölt csoportjára.

> [!NOTE]
> Ha az SCP nincs konfigurálva az AD-ben, akkor ugyanazt a módszert kell követnie, mint a csoportházirend-objektum (GPO) használatával a tartományhoz csatlakozott számítógépek [ügyféloldali beállításjegyzék-beállításbeállításának konfigurálása](#configure-client-side-registry-setting-for-scp)című módszerben leírtak szerint.


Miután meggyőződött arról, hogy minden a várt módon működik, automatikusan regisztrálhatja a windowsos aktuális és down-level eszközök többi részét az Azure AD-vel az [SCP konfigurálásával az Azure AD Connect használatával.](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)

## <a name="next-steps"></a>További lépések

[A hibrid Azure Active Directory-csatlakozási megvalósítás megtervezése](hybrid-azuread-join-plan.md)

---
title: Szabályozott ellenőrzés hibrid Azure AD JOIN – Azure ad-ben
description: Ismerje meg, hogyan teheti a hibrid Azure AD-csatlakozás szabályozott ellenőrzés egyszerre a teljes szervezetre kiterjedő az engedélyezés előtt
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
ms.openlocfilehash: d59104bf9c7675fdac2c245fff89ab1483b96b67
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481726"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás szabályozott ellenőrzése

Előfeltételek az összes érvényesítve van, amikor Windows-eszközök automatikusan regisztrálnak az Azure AD-bérlő eszközökként. Az Azure ad-ben ezek eszközidentitások állapotát a hibrid Azure AD-csatlakozás nevezik. Ebben a cikkben ismertetett fogalmakat további információt a cikkekben található [bemutatása az Eszközfelügyelet az Azure Active Directory](overview.md) és [a hibrid Azure Active Directory join megvalósítás megtervezése ](hybrid-azuread-join-plan.md).

Szervezetek előfordulhat, hogy szeretné a hibrid Azure AD-csatlakozás szabályozott ellenőrzés előtt teszi lehetővé, és egyszerre a teljes szervezet. Ez a cikk a hibrid Azure AD JOIN szabályozott ellenőrzés elvégzésének módját ismertetik.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Szabályozott ellenőrzés a hibrid Azure AD-csatlakozás az aktuális Windows-eszközökön

A Windows asztali operációs rendszert futtató eszközök esetében a támogatott verziója a Windows 10 Évfordulós frissítés (1607-es verzió) vagy újabb. Ajánlott eljárásként frissítse a Windows 10 legújabb verzióját.

Ehhez az aktuális Windows-eszközök hibrid Azure AD joinnal ellenőrzött érvényesség, kell tennie:

1. A szolgáltatási csatlakozási pontot (SCP) az Active Directory (AD) bejegyzés törlése, ha létezik
1. Ügyféloldali beállításjegyzék-beállítás a szolgáltatáskapcsolódási pont konfigurálása a tartományhoz csatlakoztatott számítógépek olyan csoportházirend-objektum (GPO) használatával
1. Az AD FS használatakor is konfigurálnia kell az ügyféloldali beállításjegyzék-beállítást, a szolgáltatáskapcsolódási pont az AD FS-kiszolgáló csoportházirend-objektum  



### <a name="clear-the-scp-from-ad"></a>Törölje az az SCP-t az AD-ből

Az Active Directory Services Interfaces szerkesztőprogram (ADSI szerkesztő) segítségével módosíthatja az az SCP objektumokat az ad-ben.

1. Indítsa el a **ADSI-szerkesztő** asztali alkalmazás és a felügyeleti munkaállomás vagy egy tartományvezérlő, egy vállalati rendszergazda engedélyezte.
1. Csatlakozás a **konfigurációs névhasználati környezet** annak a tartománynak.
1. Keresse meg a **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = Device Registration Configuration**
1. A levél objektum alatt kattintson a jobb gombbal **CN = Device Registration Configuration** válassza **tulajdonságai**
   1. Válassza ki **kulcsszavak** származó a **Attribútumszerkesztő** ablakot, és kattintson **szerkesztése**
   1. Válassza ki az értékeket a **azureADId** és **azureADName** (egyenként), és kattintson a **eltávolítása**
1. Bezárás **ADSI-szerkesztő**


### <a name="configure-client-side-registry-setting-for-scp"></a>A szolgáltatáskapcsolódási pont ügyféloldali beállításjegyzék-beállítás konfigurálása

A következő példa használatával hozza létre a csoportházirend objektumot (GPO), telepítsen egy beállításjegyzék-beállítást jegyezve szolgáltatáskapcsolódási pont konfigurálása a beállításjegyzékben, az eszközök.

1. Nyissa meg a Csoportházirend kezelése konzolon, és hozzon létre egy új csoportházirend-objektumot a tartomány.
   1. Adja meg az újonnan létrehozott csoportházirend-objektum nevét (például ClientSideSCP).
1. A csoportházirend-objektum szerkesztéséhez, és keresse meg a következő elérési út: **Számítógép konfigurációja** > **beállítások** > **Windows beállítások** > **beállításjegyzék**
1. Kattintson a jobb gombbal a beállításjegyzékben, és válassza **új** > **beállításjegyzék-elem**
   1. Az a **általános** lapra, adja meg a következőket
      1. Művelet: **Update**
      1. Struktúra: **HKEY_LOCAL_MACHINE**
      1. Kulcs elérési útja: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Érték neve: **TenantId**
      1. Érték típusa: **REG_SZ**
      1. Érték: A globálisan egyedi Azonosítót vagy **címtár-azonosító** az Azure AD-példány (Ez az érték megtalálható a **az Azure portal** > **Azure Active Directory**  >   **Tulajdonságok** > **címtár-azonosító**)
   1. Kattintson az **OK** gombra
1. Kattintson a jobb gombbal a beállításjegyzékben, és válassza **új** > **beállításjegyzék-elem**
   1. Az a **általános** lapra, adja meg a következőket
      1. Művelet: **Update**
      1. Struktúra: **HKEY_LOCAL_MACHINE**
      1. Kulcs elérési útja: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Érték neve: **tenantName**
      1. Érték típusa: **REG_SZ**
      1. Érték: Az ellenőrzött **tartománynév** az Azure ad-ben (például `contoso.onmicrosoft.com` vagy bármely más ellenőrzött tartomány nevét a címtár)
   1. Kattintson az **OK** gombra
1. Zárja be a szerkesztőt az újonnan létrehozott csoportházirend-objektum
1. Az újonnan létrehozott csoportházirend-Objektumot a kívánt szervezeti egység, amely az ellenőrzött bevezetését population tartozik tartományhoz csatlakoztatott számítógépeket tartalmazó mutató hivatkozás

### <a name="configure-ad-fs-settings"></a>Az AD FS-beállítások konfigurálása

Ha az AD FS használja, először az utasításokat a fent említett, de a csoportházirend-objektum kapcsolása az AD FS-kiszolgálók ügyféloldali szolgáltatáskapcsolódási pont konfigurálása. Ez a konfiguráció az AD FS eszközidentitások forrása létrehozására, mint az Azure AD szükséges.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Hibrid Azure AD join a Windows régebbi verziójú eszközök vezérelt érvényesítése

Windows régebbi verziójú eszközök regisztrálása, telepíteni kell a szervezetek [Microsoft munkahelyi csatlakoztatás Windows 10-számítógépek](https://www.microsoft.com/download/details.aspx?id=53554) elérhető a Microsoft Download Center.

A csomag például egy szoftverterjesztési rendszer segítségével telepíthet [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). A csomag a standard szintű beavatkozás nélküli telepítés beállítások quiet paraméter támogatja. A Configuration Manager aktuális ágának előnyöket kínál, hiszen nyomon követheti a befejezett regisztrációk például korábbi verzióihoz képest.

A telepítő, amely a felhasználó nevében a rendszer létrehoz egy ütemezett feladatot. A feladat akkor aktiválódik, ha a felhasználó bejelentkezik a Windows. A feladat csendes csatlakoztatja az eszközt az Azure AD-felhasználói hitelesítő adatok az Azure AD-hitelesítés után.

Szabályozhatók az eszközök regisztrációját, hogy üzembe kell helyeznie a Windows Installer-csomag régebbi verziójú Windows-eszközök a kiválasztott csoporthoz.

> [!NOTE]
> A szolgáltatáskapcsolódási pont nem történik meg az ad-ben, akkor kövesse ugyanazt a megközelítést leírtak szerint [ügyféloldali beállításjegyzék-beállítás a szolgáltatáskapcsolódási pont konfigurálása](#configure-client-side-registry-setting-for-scp)) a tartományhoz csatlakoztatott számítógépek olyan csoportházirend-objektum (GPO) használatával.


Miután ellenőrizte, hogy minden a várt módon működik, automatikusan rögzítheti a többi, a Windows jelenlegi és a régebbi verziójú eszközök az Azure AD által [konfigurálása az Azure AD Connect használatával SCP](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>További lépések

[A hibrid Azure Active Directory join megvalósítás megtervezése](hybrid-azuread-join-plan.md)

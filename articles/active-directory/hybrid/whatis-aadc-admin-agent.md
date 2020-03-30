---
title: Mi az Azure AD Connect felügyeleti ügynök - Azure AD Connect | Microsoft dokumentumok
description: A helyszíni környezet és az Azure AD szinkronizálásához és figyeléséhez használt eszközök ismertetése.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79f68635820125161ed4f5777e27a20de9e6fbe8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049386"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Mi az az Azure AD Connect felügyeleti ügynök? 
Az Azure AD Connect felügyeleti ügynök az Azure Active Directory Connect új összetevője, amely telepíthető egy Azure Active Directory Connect kiszolgálóra. Az Active Directory-környezetből gyűjtött adatok, amelyek segítenek a Microsoft támogatási szakembereinek a támogatási eset megnyitásakor felmerülő problémák elhárításában. 

>[!NOTE]
>A rendszergazdai ügynök alapértelmezés szerint nincs telepítve és engedélyezve.  Telepítenie kell az ügynököt, hogy adatokat gyűjtsön a támogatási esetek hez.

A telepítés után az Azure AD Connect felügyeleti ügynök megvárja az Azure Active Directoryból érkező konkrét adatkéréseket, lekéri a kért adatokat a szinkronizálási környezetből, és elküldi azokat az Azure Active Directorynak, ahol megjelenik a Microsoft támogatási szolgálatának. Mérnök. 

Az Azure AD Connect felügyeleti ügynök által a környezetből lekért adatok semmilyen módon nem tárolódnak – csak a Microsoft támogatási szakembere számára jelenik meg, hogy segítse őket az Azure Active Directory Connect vizsgálatában és hibaelhárításában kapcsolódó támogatási eset, amely megnyitotta az Azure AD Connect felügyeleti ügynök alapértelmezés szerint nincs telepítve az Azure AD Connect server. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Az Azure AD Connect felügyeleti ügynök telepítése az Azure AD Connect kiszolgálóra 

Előfeltételek:
1.    Az Azure AD Connect telepítve van a kiszolgálón
2.    Az Azure AD Connect Health telepítve van a kiszolgálón

![rendszergazdai ügynök](media/whatis-aadc-admin-agent/adminagent0.png)

Az Azure AD Connect felügyeleti ügynök bináris fájlok kerülnek az AAD Connect-kiszolgáló. Az ügynök telepítéséhez tegye a következőket:

1.    Powershell megnyitása felügyeleti módban
2.    Keresse meg azt a könyvtárat, amelyben az alkalmazás található, cd "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.    ConfigureAdminAgent.ps1 futtatása

Amikor a rendszer kéri, adja meg az Azure AD globális rendszergazdai hitelesítő adatait. Ennek meg kell egyeznie az Azure AD Connect telepítése során megadott hitelesítő adatokkal.

Az ügynök telepítése után a kiszolgáló Vezérlőpultjának "Programok telepítése/törlése" listájában a következő két új program jelenik meg: 

![rendszergazdai ügynök](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Milyen adatok jelennek meg a Szinkronizálás szolgáltatásban a Microsoft szolgáltatásmérnöke számára? 
Amikor megnyit egy támogatási esetet, a Microsoft támogatási szakembere egy adott felhasználó számára láthatja az Active Directory ban szereplő releváns adatokat, az Azure Active Directory Connect kiszolgáló Active Directory összekötő területét, az Azure Active Directory-összekötő területét az Azure-ban Active Directory connect kiszolgáló és a metaverzum az Azure Active Directory Connect kiszolgálón. 

A Microsoft támogatási szakembere nem módosíthatja a rendszerben lévő adatokat, és nem látja a jelszavakat. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Mi a teendő, ha nem szeretném, hogy a Microsoft támogatási szakembere hozzáférjen az adataimhoz? 
Az ügynök telepítése után, Ha nem szeretné, hogy a Microsoft szervizszakembere hozzáférjen a támogatási hívás hozadékához szükséges adatokhoz, az alábbi módon módosíthatja a szolgáltatás konfigurációs fájlját: 

1.    Nyissa **meg a C:\Program Files\Microsoft Azure AD Connect felügyeleti ügynököt\AzureADConnectAdministrationAgentAgentService.exe.config** a jegyzettömbben.
2.    A **UserDataEnabled** beállítás letiltása az alábbi módon. Ha **a UserDataEnabled** beállítás létezik, és értéke igaz, akkor állítsa false értékre. Ha a beállítás nem létezik, adja hozzá a beállítást az alábbi módon.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.    Mentse a konfigurációs fájlt.
4.    Az Azure AD Connect felügyeleti ügynök szolgáltatásának újraindítása az alábbiak szerint

![rendszergazdai ügynök](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

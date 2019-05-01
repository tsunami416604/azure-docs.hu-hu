---
title: Mi az az Azure AD Connect felügyeleti ügynök – az Azure AD Connect |} A Microsoft Docs
description: Szinkronizálását és figyelni a helyszíni környezetet az Azure ad-vel használt eszközöket írja le.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687220"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Mi az az Azure AD Connect felügyeleti ügynök? 
Az Azure AD Connect felügyeleti ügynök része egy új Azure Active Directory Connect, amelyet telepíthet egy Azure Active Directory Connect-kiszolgálón. Adott adatok gyűjtését az Active Directory-környezetet, amelynek segítségével a Microsoft támogatási szakértője hibák elhárításához, ha a támogatási eset nyitása szolgál. 

>[!NOTE]
>A felügyeleti ügynök nincs telepítve, és alapértelmezés szerint engedélyezve van.  Annak érdekében, hogy az adatgyűjtést, amelyek segítik a támogatási esetek telepítenie kell az ügynököt.

Telepítésekor a bizonyos kéréseket az Azure AD Connect felügyeleti ügynök vár az adatokat az Azure Active Directoryból, a kért adatokat olvas be a sync-környezetet, és elküldi azt az Azure Active Directoryhoz, ahol mutatnak be a Microsoft támogatja a visszafejtés. 

Semmilyen módon nem tárolja a az Azure AD Connect felügyeleti ügynök kérdezi le a környezetből információkat – csak jelenik meg a Microsoft támogatási szakértője vizsgálata és elhárítása az Azure Active Directory Connect kapcsolódó támogatási esetet, hogy megnyitotta az Azure AD Connect felügyeleti ügynök nincs telepítve a kiszolgálón az Azure AD Connect alapértelmezés szerint. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Az Azure AD Connect felügyeleti ügynök telepítése az Azure AD Connect-kiszolgálón 
Az Azure AD Connect felügyeleti ügynök bináris fájljainak kerülnek az AAD Connect-kiszolgálón. Az ügynök telepítéséhez tegye a következőket: 



1. Adminisztrátori módban megnyitott powershell 
2. Keresse meg azt a könyvtárat, ahol az alkalmazás található cd "C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles" 
3. A AADConnectAdminAgentSetup.exe alkalmazás futtatása 
 
Amikor a rendszer kéri, adja meg az Azure AD globális rendszergazdai hitelesítő adatait. 

>[!NOTE]
>Nincs olyan ismert probléma, ahol kérni fogja a hitelesítő adatok több alkalommal. Ez a következő kiadás javítja.

Az ügynök telepítését követően látni fogja az alábbi két új program a kiszolgáló a Vezérlőpulton a "Program eltávolítása" listában: 

![felügyeleti ügynök](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>A Microsoft szolgáltatás-mérnökkel, megjelennek a saját szinkronizálási szolgáltatás milyen adatok? 
Támogatási eset megnyitásakor a Microsoft támogatási szakembere látható, az adott felhasználó, a vonatkozó adatokat az Active Directory, az Azure Active Directory Connect-kiszolgálón az Active Directory-összekötő-térben az Azure Active Directory összekötőterében, az Azure-ban Az Active Directory Connect-kiszolgáló és az Azure Active Directory Connect-kiszolgálón a Metaverzumba. 

A Microsoft támogatási szakembere nem módosítják az adatokat a rendszer, és nem látható a jelszavakat. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Mi történik, ha most nem kívánok adatok eléréséhez a Microsoft támogatási szakértő? 
Az ügynök telepítve van, ha nem szeretné, hogy a Microsoft-szolgáltatás mérnök, egy támogatási hívás az adatok eléréséhez, ha a szolgáltatás konfigurációs fájl módosításával, az alább ismertetett letilthatják a funkciók: 

1.  Nyissa meg **C:\Program Files\Microsoft Azure AD Connect felügyeleti Agent\AzureADConnectAdministrationAgentService.exe.config** a Jegyzettömbben.
2.  Tiltsa le **UserDataEnabled** beállítása a lent látható módon. Ha **UserDataEnabled** beállítás létezik, és igaz értékre, majd állítsa be hamis értékre van állítva. Ha a beállítás nem létezik, majd adja hozzá a beállítást alább látható módon.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Mentse a konfigurációs fájlt.
4.  Azure AD Connect felügyeleti ügynök szolgáltatás újraindításához, ahogy az alábbi

![felügyeleti ügynök](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

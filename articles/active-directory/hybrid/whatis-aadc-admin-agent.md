---
title: Mi az az Azure AD Connect felügyeleti ügynök – az Azure AD Connect |} A Microsoft Docs
description: Szinkronizálását és figyelni a helyszíni környezetet az Azure ad-vel használt eszközöket írja le.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33143626e136523b4af086e841b92e9ad30fa86
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577609"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Mi az az Azure AD Connect felügyeleti ügynök? 
Az Azure AD Connect felügyeleti ügynök része egy új Azure Active Directory Connect, amely egy Azure Active Directory Connect-kiszolgálón telepítve van. Adott adatok gyűjtését az Active Directory-környezetet, amelynek segítségével a Microsoft támogatási szakértője hibák elhárításához, ha a támogatási eset nyitása szolgál.

Telepítésekor a bizonyos kéréseket az Azure AD Connect felügyeleti ügynök várakozhat adatokat az Azure Active Directoryból, a kért adatokat olvas be a sync-környezetet, és elküldi azt az Azure Active Directoryhoz, ahol mutatnak be a Microsoft támogatja a visszafejtés.

Semmilyen módon nem tárolja a az Azure AD Connect felügyeleti ügynök kérdezi le a környezetből információkat – csak jelenik meg a Microsoft támogatási szakértője vizsgálata és elhárítása az Azure Active Directory Connect megnyitott kapcsolatos támogatási eset

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Hogyan telepíthető az Azure AD Connect felügyeleti ügynök az Azure AD Connect-kiszolgáló? 
A felügyeleti ügynök telepítése után, akkor az alábbi két új program a kiszolgáló a Vezérlőpulton a "Program eltávolítása" listában jelenik meg: 

![felügyeleti ügynök](media/whatis-aadc-admin-agent/adminagent1.png)

Ne távolítsa el vagy vonja vissza a rendszert telepíteni, azok a kritikus része az Azure AD Connect-telepítésre.

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>A Microsoft szolgáltatás-mérnökkel, megjelennek a saját szinkronizálási szolgáltatás milyen adatok?
Ha megnyitja egy támogatási esetet, a Microsoft támogatási szakembere látható, az adott felhasználó, a vonatkozó adatokat az Active Directory, az Azure Active Directory Connect-kiszolgálón az Active Directory-összekötő-térben az Azure-ban az Azure Active Directory-összekötő-térben Az Active Directory Connect-kiszolgáló és az Azure Active Directory Connect-kiszolgálón a Metaverzumba.

A Microsoft támogatási szakembere nem módosítják az adatokat a rendszer, és nem látható a jelszavakat.

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Mi történik, ha most nem kívánok adatok eléréséhez a Microsoft támogatási szakértő? 
 
Ha nem szeretné, hogy a Microsoft-szolgáltatás mérnök, egy támogatási hívás az adatok eléréséhez a szolgáltatás konfigurációs fájl módosításával, az alább ismertetett letilthatja ezt: 

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
---
title: Az Azure AD Connect felhőalapú kiépítési hibaelhárítása
description: Ez a cikk ismerteti, hogyan hárítsa el a felhőkiépítési ügynök esetlegesen felmerülő problémák elhárítása.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549485"
---
# <a name="cloud-provisioning-troubleshooting"></a>Felhőalapú kiépítés – hibaelhárítás

A felhőkiépítés számos különböző dolgot érint, és számos különböző függőséget okoz. Ez a széles körű alkalmazás különböző problémákat okozhat. Ez a cikk segítséget nyújt a problémák elhárításához. Bemutatja azokat a tipikus területeket, amelyekre összpontosíthat, hogyan gyűjthet további információkat, és a különböző technikákat, amelyekkel nyomon követheti a problémákat.


## <a name="common-troubleshooting-areas"></a>Gyakori hibaelhárítási területek

|Név|Leírás|
|-----|-----|
|[Ügynöki problémák](#agent-problems)|Ellenőrizze, hogy az ügynök megfelelően lett-e telepítve, és hogy kommunikál-e az Azure Active Directoryval (Azure AD).|
|[Objektumszinkronizálási problémák](#object-synchronization-problems)|A létesítési naplók segítségével hárítsa el az objektumszinkronizálási problémákat.|
|[Karanténba helyezett problémák kiépítése](#provisioning-quarantined-problems)|Ismerje meg a karanténproblémák kiépítési problémáit, és hogyan javíthatja azokat.|


## <a name="agent-problems"></a>Ügynöki problémák
Az első dolog, amit ellenőrizni szeretne az ügynökkel, a következők:

-  Telepítve van?
-  Az ügynök helyileg fut?
-  Az ügynök a portálon van?
-  Az ügynök egészségesként van megjelölve?

Ezek az elemek ellenőrizhetők az Azure Portalon és az ügynököt futtató helyi kiszolgálón.

### <a name="azure-portal-agent-verification"></a>Az Azure Portal ügynökének ellenőrzése

Annak ellenőrzéséhez, hogy az ügynök az Azure-ban látható, és kifogástalan állapotú, kövesse az alábbi lépéseket.

1. Jelentkezzen be az Azure portálra.
1. A bal oldalon válassza az **Azure Active Directory** > **Azure AD Connect lehetőséget.** A központban válassza a **Kiépítés kezelése (előzetes verzió) lehetőséget.**
1. Az **Azure AD-kiépítés (előzetes verzió)** képernyőn válassza **az Összes ügyintéző áttekintése**lehetőséget.

   ![Az összes ügyintéző áttekintése](media/how-to-install/install7.png)</br>
 
1. A **helyszíni létesítési ügynökök** képernyőn láthatja a telepített ügynököket. Ellenőrizze, hogy a kérdéses ügynök létezik-e, és kifogástalan jelöléssel *van-e ellátva.*

   ![Helyszíni létesítési ügynökök képernyő](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>A port ellenőrzése

Annak ellenőrzéséhez, hogy az Azure figyel-e a 443-as porton, és hogy az ügynök kommunikálhat-e vele, használja a következő eszközt:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ez a teszt ellenőrzi, hogy az ügynökök kommunikálhatnak-e az Azure-ral a 443-as porton keresztül. Nyisson meg egy böngészőt, és lépjen az előző URL-címre abból a kiszolgálóból, amelyen az ügynök telepítve van.

![A kikötői elérhetőség ellenőrzése](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>A helyi kiszolgálón

Az ügynök futásának ellenőrzéséhez kövesse az alábbi lépéseket.

1. Azon a kiszolgálón, amelyen telepítve van az ügynök, nyissa meg a **Szolgáltatásokat** úgy, hogy rá navigál, vagy **megnyitja** > a**Start Run** > **Services.msc programot.**
1. A **Szolgáltatások csoportban**győződjön meg arról, hogy a **Microsoft Azure AD Connect Agent Updater** és a **Microsoft Azure AD Connect provisioning Agent** ott van, és állapotuk *fut.*

   ![Szolgáltatások képernyő](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Gyakori ügynöktelepítési problémák

A következő szakaszok néhány gyakori ügynöktelepítési problémát és tipikus megoldási problémát ismernek.

#### <a name="agent-failed-to-start"></a>Az ügynök nem indult el

A következő hibaüzenet jelenhet meg:

**A "Microsoft Azure AD Connect kiépítési ügynök" szolgáltatás nem indult el. Ellenőrizze, hogy rendelkezik-e a rendszerszolgáltatások indításához szükséges jogosultságokkal.** 

Ezt a problémát általában egy csoportházirend okozza, amely megakadályozta, hogy a telepítő által létrehozott helyi NT szolgáltatás bejelentkezési fiókjára (NT SERVICE\AADConnectProvisioningAgent) engedélyek kerüljenek alkalmazásra. Ezek az engedélyek szükségesek a szolgáltatás elindításához.

A probléma megoldásához kövesse az alábbi lépéseket.

1. Jelentkezzen be a kiszolgálóra rendszergazdai fiókkal.
1. **Nyissa meg** a Szolgáltatásokat úgy, hogy rá navigál, vagy a **Start** > **Run** > **Services.msc**.
1. A **Szolgáltatások csoportban**kattintson duplán a **Microsoft Azure AD Connect kiépítési ügynök elemre.**
1. A **Bejelentkezés** lapon módosítsa ezt a **fiókot** tartományi rendszergazdára. Ezután indítsa újra a szolgáltatást. 

   ![Bejelentkezés lap](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Az ügynök idővel kimenő, vagy a tanúsítvány érvénytelen

Az ügynök regisztrálásakor a következő hibaüzenet jelenhet meg.

![Idő- megtasztaatia hibaüzenet](media/how-to-troubleshoot/troubleshoot4.png)

Ezt a problémát általában az okozza, hogy az ügynök nem tud csatlakozni a hibrid identitásszolgáltatáshoz, és http-proxyt kell konfigurálnia. A probléma megoldásához konfiguráljon egy kimenő proxyt. 

A kiépítési ügynök támogatja a kimenő proxy használatát. Konfigurálhatja a *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*ügynökkonfigurációs fájl szerkesztésével. Adja hozzá a következő sorokat a fájl vége `</configuration>` felé közvetlenül a záró címke előtt.
Cserélje le `[proxy-server]` a `[proxy-port]` változókat és a proxykiszolgáló nevére és portértékeire.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Az ügynök regisztrációja biztonsági hibával sikertelen

Előfordulhat, hogy hibaüzenet jelenik meg a felhőkiépítési ügynök telepítésekor.

Ezt a problémát általában az okozza, hogy az ügynök nem tudja végrehajtani a PowerShell regisztrációs parancsfájlok helyi PowerShell-végrehajtási szabályzatok miatt.

A probléma megoldásához módosítsa a PowerShell végrehajtási szabályzatait a kiszolgálón. A Számítógép és a Felhasználó házirendeket *nem definiált* vagy *távoli ra vonatkozóan*kell beállítani. Ha *korlátozás nélküliként*vannak beállítva, ez a hibaüzenet jelenik meg. További információ: [PowerShell végrehajtási szabályzatok.](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) 

### <a name="log-files"></a>Naplófájlok

Alapértelmezés szerint az ügynök minimális hibaüzeneteket és veremkövetési adatokat bocsát ki. Ezeket a nyomkövetési naplókat a *C:\ProgramData\Microsoft\Azure AD Connect provisioning Agent\Trace*mappában találja.

Az ügynökökkel kapcsolatos problémák elhárításával kapcsolatos további részletek összegyűjtéséhez kövesse az alábbi lépéseket.

1. Állítsa le a szolgáltatást a **Microsoft Azure AD Connect kiépítési ügynök**.
1. Az eredeti konfigurációs fájl másolatának létrehozása: *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*.
1. Cserélje le `<system.diagnostics>` a meglévő szakaszt a következőkre, és az összes nyomkövetési üzenet a *ProvAgentTrace.log*fájlba kerül.

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <add name="textWriterListener"/>
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
         </sharedListeners>
     </system.diagnostics>
    
   ```
1. Indítsa el a szolgáltatást a **Microsoft Azure AD Connect kiépítési ügynök**.
1. A következő paranccsal lehúzhat a fájllal és a hibakeresési problémákkal. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Objektumszinkronizálási problémák

A következő szakasz az objektumok szinkronizálásának hibaelhárításával kapcsolatos információkat tartalmazza.

### <a name="provisioning-logs"></a>Üzembehelyezési naplók

Az Azure Portalon a kiépítési naplók segítségével nyomon követheti és elháríthatja az objektumszinkronizálási problémákat. A naplók megtekintéséhez válassza a **Naplók**lehetőséget.

![Naplók gomb](media/how-to-troubleshoot/log1.png)

A kiépítési naplók számos információt nyújtanak a helyszíni Active Directory-környezet és az Azure között szinkronizált objektumok állapotáról.

![A naplók kiépítése képernyő](media/how-to-troubleshoot/log2.png)

A lap tetején található legördülő listák segítségével a nézetet nullára szűrheti bizonyos problémák, például dátumok esetén. További információk megtekintéséhez kattintson duplán egy adott eseményre.

![A Naplók kiépítéslegördülő lista adatai](media/how-to-troubleshoot/log3.png)

Ez az információ részletes lépéseket tartalmaz, és hogy hol fordul elő a szinkronizálási probléma. Ily módon pontosan meghatározhatja a probléma pontos helyét.


## <a name="provisioning-quarantined-problems"></a>Karanténba helyezett problémák kiépítése

A felhőkiépítés figyeli a konfiguráció állapotát, és karanténállapotba helyezi a nem megfelelő állapotú objektumokat. Ha a célrendszeren végrehajtott hívások többsége vagy egésze egy hiba miatt következetesen sikertelen, például érvénytelen rendszergazdai hitelesítő adatok miatt, a létesítési feladat karanténként van megjelölve.

![Karantén állapota](media/how-to-troubleshoot/quarantine1.png)

Az állapot kiválasztásával további információkat láthat a karanténról. A hibakódot és az üzenetet is beszerezheti.

![Karantén állapotadatai](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Karantén feloldása

- Az Azure Portal használatával indítsa újra a létesítési feladatot. Az ügynök konfigurációs lapján válassza a **Kiépítés újraindítása**lehetőséget.

  ![Kiépítés újraindítása](media/how-to-troubleshoot/quarantine3.png)

- A [Kiépítési feladat újraindításához](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)használja a Microsoft Graph programot. Teljes mértékben szabályozhatja, hogy mit indít újra. A következőket törölheti:
  - Escrows, hogy indítsa újra a letéti számláló, hogy felhalmozódik felé karantén állapotát.
  - Karantén, hogy távolítsa el az alkalmazást a karanténból.
  - Vízjelek. 
  
  Használja az alábbi kérelmet:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)




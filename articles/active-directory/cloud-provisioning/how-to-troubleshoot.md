---
title: A felhő üzembe helyezésének Azure AD Connect hibaelhárítása
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a felhőalapú kiépítési ügynökkel kapcsolatban felmerülő problémákat.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 34796a435536a48100b7434ed5267802cd2d549f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89226947"
---
# <a name="cloud-provisioning-troubleshooting"></a>Felhőalapú kiépítés – hibaelhárítás

A Felhőbeli kiépítés számos különböző dolgot érint, és számos különböző függőséggel rendelkezik. Ez az általános hatókör számos problémát eredményezhet. Ez a cikk segítséget nyújt a problémák elhárításában. Bevezeti a tipikus területeket, amelyekkel koncentrálhat, hogyan gyűjthet további információkat, valamint a problémák nyomon követéséhez használható különféle technikákat.


## <a name="common-troubleshooting-areas"></a>Gyakori hibaelhárítási területek

|Név|Leírás|
|-----|-----|
|[Ügynökkel kapcsolatos problémák](#agent-problems)|Ellenőrizze, hogy az ügynök megfelelően lett-e telepítve, és hogy a Azure Active Directory (Azure AD) szolgáltatással kommunikál-e.|
|[Objektum-szinkronizálási problémák](#object-synchronization-problems)|Az objektumok szinkronizálásával kapcsolatos problémák elhárításához használjon kiépítési naplókat.|
|[Karanténba helyezett problémák kiépítés](#provisioning-quarantined-problems)|Megtudhatja, hogyan lehet kiépíteni a karanténba helyezési problémákat, és hogyan javíthatja őket.|


## <a name="agent-problems"></a>Ügynökkel kapcsolatos problémák
Az ügynökkel ellenőrizni kívánt első lépések:

-  Telepítve van?
-  Az ügynök helyileg fut?
-  Az ügynök a portálon?
-  Az ügynök kifogástalanként van megjelölve?

Ezek az elemek ellenőrizhetők a Azure Portal és az ügynököt futtató helyi kiszolgálón.

### <a name="azure-portal-agent-verification"></a>Azure Portal ügynök ellenőrzése

Az alábbi lépéseket követve ellenőrizheti, hogy az ügynök látható-e az Azure-ban és kifogástalan állapotban van-e.

1. Jelentkezzen be az Azure portálra.
1. A bal oldalon válassza a **Azure Active Directory**  >  **Azure ad Connect**lehetőséget. A központban válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.
1. Az **Azure ad-kiépítés (előzetes verzió)** képernyőn válassza az **összes ügynök áttekintése**lehetőséget.

   ![Az összes ügynök áttekintése](media/how-to-install/install7.png)</br>
 
1. A helyszíni **kiépítési ügynökök** képernyőjén láthatja a telepített ügynököket. Ellenőrizze, hogy a szóban forgó ügynök van-e kijelölve, és hogy *kifogástalan*állapotú-e.

   ![Helyszíni kiépítési ügynökök képernyő](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>A port ellenőrzése

Ellenőrizze, hogy az Azure figyel-e a 443-es porton, és hogy az ügynök tud-e kommunikálni vele. 

Ez a teszt ellenőrzi, hogy az ügynökök az 443-as porton keresztül kommunikálnak-e az Azure-ban. Nyisson meg egy böngészőt, és keresse meg az előző URL-címet arról a kiszolgálóról, amelyen az ügynököt telepítették.

![A port elérhetőségének ellenőrzése](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>A helyi kiszolgálón

Az ügynök futtatásának ellenőrzéséhez kövesse az alábbi lépéseket.

1. A kiszolgálón, amelyen telepítve van az ügynök, nyissa meg a **szolgáltatásokat** vagy navigáljon a szolgáltatáshoz, vagy **indítsa el**a  >  **Run**  >  **Services. msc**parancsot.
1. Győződjön meg arról, hogy a **szolgáltatások**területen **Microsoft Azure ad a összekapcsolási ügynök frissítése** és a **Microsoft Azure ad kapcsolat létesítése ügynök** van, és az állapota *fut*.

   ![Szolgáltatások képernyő](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Gyakori ügynök-telepítési problémák

A következő szakaszok ismertetik az ügynök gyakori telepítési problémáit és a tipikus megoldásokat.

#### <a name="agent-failed-to-start"></a>Az ügynök elindítása nem sikerült

Előfordulhat, hogy a következő hibaüzenet jelenik meg:

**Nem sikerült elindítani a (z) "Microsoft Azure AD kapcsolódási kiépítési ügynök" szolgáltatást. Ellenőrizze, hogy rendelkezik-e megfelelő jogosultsággal a rendszerszolgáltatások elindításához.** 

Ezt a problémát általában egy olyan csoportházirend okozta, amely megakadályozta, hogy az engedélyek a telepítő (NT SERVICE\AADConnectProvisioningAgent) által létrehozott helyi NT szolgáltatásbeli bejelentkezési fiókra legyenek alkalmazva. Ezek az engedélyek szükségesek a szolgáltatás elindításához.

A probléma megoldásához kövesse az alábbi lépéseket.

1. Jelentkezzen be a kiszolgálóra egy rendszergazdai fiókkal.
1. Nyissa meg a **szolgáltatásokat** vagy navigáljon a szolgáltatásba, vagy **indítsa el**a  >  **Run**  >  **Services. msc**parancsot.
1. A **szolgáltatások**területen kattintson duplán **Microsoft Azure ad a kiépítési ügynök összekapcsolására**.
1. A **Bejelentkezés** lapon módosítsa a **fiókot** egy tartományi rendszergazdára. Ezután indítsa újra a szolgáltatást. 

   ![Bejelentkezés lap](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Az ügynök időtúllépése vagy a tanúsítvány érvénytelen

Előfordulhat, hogy az alábbi hibaüzenet jelenik meg, amikor megkísérli regisztrálni az ügynököt.

![Időtúllépési hibaüzenet](media/how-to-troubleshoot/troubleshoot4.png)

Ezt a problémát általában az okozza, hogy az ügynök nem tud csatlakozni a hibrid identitásszolgáltatáshoz, és konfigurálnia kell egy HTTP-proxyt. A probléma megoldásához konfiguráljon egy kimenő proxyt. 

A kiépítési ügynök támogatja a kimenő proxy használatát. A konfigurálásához módosítsa az ügynök konfigurációs fájljának *C:\Program Files\Microsoft Azure ad Connect kiépítési Agent\AADConnectProvisioningAgent.exe.config*. Adja hozzá a következő sorokat a fájl végéhez közvetlenül a záró `</configuration>` címke előtt.
Cserélje le a változókat `[proxy-server]` és a `[proxy-port]` proxykiszolgáló nevét és portszámát.

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

#### <a name="agent-registration-fails-with-security-error"></a>Biztonsági hiba miatt sikertelen volt az ügynök regisztrálása.

A felhőalapú kiépítési ügynök telepítésekor hibaüzenet jelenhet meg.

Ezt a problémát általában az okozza, hogy az ügynök nem tudja végrehajtani a PowerShell regisztrációs parancsfájlokat a helyi PowerShell-végrehajtási házirendek miatt.

A probléma megoldásához módosítsa a PowerShell végrehajtási szabályzatait a kiszolgálón. A számítógép-és felhasználói házirendeket nem *definiált* vagy *RemoteSigned*kell beállítani. Ha nem *korlátozottként*vannak beállítva, akkor ez a hibaüzenet jelenik meg. További információ: [PowerShell végrehajtási szabályzatok](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Naplófájlok

Alapértelmezés szerint az ügynök minimális hibaüzeneteket és hívásláncadatokat bocsát ki. Ezeket a nyomkövetési naplókat az AD- *kapcsolat létesítési Agent\Trace*mappájában találja meg C:\ProgramData\Microsoft\Azure.

Az ügynökkel kapcsolatos problémák elhárításához kövesse az alábbi lépéseket.

1. Állítsa le a szolgáltatás Microsoft Azure AD a létesítési **ügynök összekapcsolását**.
1. Hozza létre az eredeti konfigurációs fájl másolatát: *C:\Program Files\Microsoft Azure ad Connect kiépítési Agent\AADConnectProvisioningAgent.exe.config*.
1. Cserélje le a meglévő `<system.diagnostics>` szakaszt a következőre, és az összes nyomkövetési üzenet a *ProvAgentTrace. log*fájlra lép.

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
1. Indítsa el a szolgáltatást **Microsoft Azure ad a kiépítési ügynök összekapcsolását**.
1. A fájl-és hibakeresési problémák kitöltéséhez használja az alábbi parancsot. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Objektum-szinkronizálási problémák

A következő szakasz az objektumok szinkronizálásával kapcsolatos hibaelhárítással kapcsolatos információkat tartalmaz.

### <a name="provisioning-logs"></a>Üzembehelyezési naplók

A Azure Portalban az üzembe helyezési naplók segítséget nyújtanak az objektumok szinkronizációs problémáinak nyomon követéséhez és hibaelhárításához. A naplók megtekintéséhez válassza a **naplók**lehetőséget.

![Naplók gomb](media/how-to-troubleshoot/log1.png)

A kiépítési naplók rengeteg információt biztosítanak a helyszíni Active Directory környezet és az Azure között szinkronizált objektumok állapotáról.

![Naplók kiépítés képernyője](media/how-to-troubleshoot/log2.png)

A lap tetején található legördülő lista használatával szűrheti a nézetet nulla értékre az adott problémákon, például a dátumokon. További információk megjelenítéséhez kattintson duplán egy adott eseményre.

![Kiépítési naplók legördülő lista adatai](media/how-to-troubleshoot/log3.png)

Ez az információ részletesen ismerteti a szinkronizálási problémát. Így pontosan megadhatja a probléma pontos helyét.


## <a name="provisioning-quarantined-problems"></a>Karanténba helyezett problémák kiépítés

A Felhőbeli kiépítés figyeli a konfiguráció állapotát, és nem kifogástalan állapotú objektumokat helyez el karantén állapotban. Ha egy hiba miatt a megcélzott rendszerre irányuló hívások többsége vagy mindegyike, például érvénytelen rendszergazdai hitelesítő adatok, a kiépítési feladat karanténként van megjelölve.

![Karantén állapota](media/how-to-troubleshoot/quarantine1.png)

Az állapot kiválasztásával további információkat tekinthet meg a karanténba. Megszerezheti a hibakódot és az üzenetet is.

![Karantén állapotával kapcsolatos információk](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Karantén feloldása

- A kiépítési feladatok újraindításához használja a Azure Portal. Az ügynök konfigurációja lapon válassza a **kiépítés újraindítása**lehetőséget.

  ![Kiépítés újraindítása](media/how-to-troubleshoot/quarantine3.png)

- [A kiépítési feladatok újraindításához használja a](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)Microsoft Graph. Az újraindítást teljes mértékben szabályozhatja. A következőket törölheti:
  - A rendszer a karanténba helyezési állapot felé felmerülő letéti számláló újraindítására.
  - Karanténba helyezheti az alkalmazást a karanténba.
  - Vízjelek. 
  
  Használja az alábbi kérelmet:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
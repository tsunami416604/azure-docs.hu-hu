---
title: A felhő üzembe helyezésének Azure AD Connect hibaelhárítása
description: Ez a dokumentum leírja, hogyan lehet elhárítani a felhőalapú kiépítési ügynökkel kapcsolatban felmerülő problémákat.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794236"
---
# <a name="cloud-provisioning-troubleshooting"></a>Felhőalapú kiépítés – hibaelhárítás

A Felhőbeli kiépítés számos különböző dolgot érint, és számos különböző függőséggel rendelkezik.  Ez természetesen számos problémát eredményezhet.  Ez a dokumentum úgy lett kialakítva, hogy megismerkedjen a hibák elhárításával.  Ez a dokumentum bemutatja azokat a tipikus területeket, amelyeknek összpontosítania kell, hogyan gyűjthet további információkat, valamint a problémák nyomon követéséhez használható különféle technikákat.  


## <a name="common-troubleshooting-areas"></a>Gyakori hibaelhárítási területek

|Név|Leírás|
|-----|-----|
|[Ügynökkel kapcsolatos problémák](#agent-issues)|Ellenőrizze, hogy az ügynök megfelelően van-e telepítve, és kommunikál-e az Azure AD-vel.|
|[Objektum-szinkronizálási problémák](#object-synchronization-issues)|Az objektumok szinkronizálásával kapcsolatos problémák elhárításához használjon kiépítési naplókat.|
|[Karanténba helyezési problémák kiépítés](#provisioning-quarantined-issues)|Megtudhatja, hogyan lehet kiépíteni a karanténba helyezési problémát, és hogyan javíthatja őket.|


## <a name="agent-issues"></a>Ügynökkel kapcsolatos problémák
Az ügynökkel ellenőrizni kívánt első lépések:


-  telepítve van?
-  az ügynök helyileg fut?
-  az ügynök a portálon?
-  az ügynök kifogástalanként van megjelölve?  

Ezek az elemek ellenőrizhetők a Azure Portal és az ügynököt futtató helyi kiszolgálón.

### <a name="azure-portal-agent-verification"></a>Azure Portal ügynök ellenőrzése

A következő lépések végrehajtásával ellenőrizheti, hogy az ügynök látható-e az Azure-ban:

1. Jelentkezzen be az Azure portálra.
2. A bal oldalon válassza a **Azure Active Directory**, majd a **Azure ad Connect** , és a központban válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.
3.  Az **Azure ad-kiépítés (előzetes verzió)** képernyőn kattintson az **összes ügynök áttekintése**elemre.
 Azure AD-kiépítés ![](media/how-to-install/install7.png)</br>
 
4. A helyszíni **kiépítési ügynökök képernyőjén** látni fogja a telepített ügynököket.  Ellenőrizze, hogy a szóban forgó ügynök van-e kijelölve, és hogy **kifogástalan**állapotú-e.
 ![kiépítési ügynökök](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>A port ellenőrzése

Annak ellenőrzéséhez, hogy az Azure figyel-e a 443-es porton, és hogy az ügynök képes-e kommunikálni vele, a következő eszközzel végezheti el a használatát:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ez a teszt azt ellenőrzi, hogy az ügynökök képesek-e kommunikálni az Azure-ban a 443-as porton keresztül.  Nyisson meg egy böngészőt, és navigáljon a fenti URL-címre abban a kiszolgálón, ahol az ügynököt telepítették.
 ![Szolgáltatások](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>A helyi kiszolgálón

Az ügynök futtatásának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  A kiszolgálón, amelyen telepítve van az ügynök, nyissa meg a szolgáltatásokat vagy navigáljon a **szolgáltatáshoz** , vagy indítsa el a Start/Run/Services. msc parancsot.
2.  Győződjön meg arról, hogy a **szolgáltatások**területen **Microsoft Azure ad a összekapcsolási ügynök frissítése** és a **Microsoft Azure ad kapcsolat létesítése ügynök** van, és az állapota **fut**.
 ![Szolgáltatások](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>Gyakori ügynök-telepítési problémák

Az alábbiakban néhány gyakori ügynök-telepítési problémát talál, és a tipikus megoldás a következő:.

#### <a name="agent-failed-to-start"></a>Az ügynök elindítása nem sikerült

Ha a következő hibaüzenet jelenik meg:

**Nem sikerült elindítani a (z) "Microsfoft Azure AD Connect kiépítési ügynök" szolgáltatást.  Ellenőrizze, hogy rendelkezik-e megfelelő jogosultsággal a rendszerszolgáltatások elindításához.** 

Ezt általában egy olyan csoportházirend okozta, amely megakadályozta, hogy az engedélyek a telepítő (NT SERVICE\AADConnectProvisioningAgent) által létrehozott "bejelentkezési fiók" helyi NT szolgáltatásra legyenek alkalmazva, ezek az engedélyek a szolgáltatás indításához szükségesek.

A probléma megoldásához kövesse az alábbi lépéseket:

1.  Jelentkezzen be a kiszolgálóra egy rendszergazdai fiókkal
2.  Nyissa meg a szolgáltatásokat vagy navigáljon a **szolgáltatáshoz** , vagy indítsa el a Start/Run/Services. msc parancsot.
3.  A **szolgáltatások** területen kattintson duplán **Microsoft Azure ad a kiépítési ügynök összekapcsolására**
4. A lapon módosítsa a "bejelentkezési fiókot" egy tartományi rendszergazdára, majd indítsa újra a szolgáltatást. 

 ![Szolgáltatások](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Az ügynök időtúllépése vagy a tanúsítvány érvénytelen

Az ügynök regisztrálására tett kísérlet során a következő hibaüzenetek jelenhetnek meg.

 ![Szolgáltatások](media/how-to-troubleshoot/troubleshoot4.png)

Ezt általában az okozza, hogy az ügynök nem tud csatlakozni a hibrid identitási szolgáltatáshoz, és a HTTP-proxy konfigurálását igényli.  A kimenő proxy konfigurálásának feloldásához. 

A kiépítési ügynök támogatja a kimenő proxy használatát. A konfigurálásához módosítsa az ügynök konfigurációs fájljának **C:\Program Files\Microsoft Azure ad Connect kiépítési Agent\AADConnectProvisioningAgent.exe.config**. Adja hozzá a következő sorokat a fájl végéhez közvetlenül a záró `</configuration>` címke előtt.
Cserélje le a [Proxy-Server] és a [proxy-port] változót a proxykiszolgáló nevére és a port értékeire.

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

A felhőalapú kiépítési ügynök telepítésekor a következő hibaüzenet jelenhet meg.

Ezt általában az okozza, hogy az ügynök nem tudja végrehajtani a PowerShell regisztrációs parancsfájlokat a helyi PowerShell-végrehajtási házirendek miatt.

Ennek megoldásához módosítsa a PowerShell végrehajtási szabályzatait a kiszolgálón. A számítógép-és felhasználói házirendeket "nem definiált" vagy "RemoteSigned" értékre kell állítania. Ha a "korlátlan" érték jelenik meg, akkor ez a hibaüzenet jelenik meg.  További információ: [PowerShell végrehajtási szabályzatok](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Naplófájlok

Alapértelmezés szerint az ügynök nagyon minimális hibaüzeneteket és verem-nyomkövetési adatokat bocsát ki. A nyomkövetési naplók a következő mappában találhatók: **C:\PROGRAMDATA\MICROSOFT\AZURE ad-kapcsolat létesítése Agent\Trace**

A következő lépésekkel további részleteket gyűjthet az ügynökkel kapcsolatos problémák elhárításához.

1. A Service **Microsoft Azure ad a kiépítési ügynök összekapcsolásának** leállítása
2. Másolat készítése az eredeti konfigurációs fájlról: **C:\Program Files\Microsoft Azure ad Connect kiépítési Agent\AADConnectProvisioningAgent.exe.config**
3. Cserélje le a meglévő < System. Diagnostics > szakaszt a következőre, és az összes nyomkövetési üzenet a **ProvAgentTrace. log** fájlba kerül.

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
4. A szolgáltatás elindítása **Microsoft Azure ad a létesítési ügynök összekapcsolásával**
5. A fájl-és hibakeresési hibák kitöltéséhez a következő parancsot használhatja: 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>Objektum-szinkronizálási problémák

A következő szakasz az objektumok szinkronizálásával kapcsolatos hibaelhárítással kapcsolatos információkat tartalmaz.

### <a name="provisioning-logs"></a>Üzembehelyezési naplók

A Azure Portalban az üzembe helyezési naplók segítségével nyomon követheti és elháríthatja az objektumok szinkronizálásával kapcsolatos problémákat.  A naplók megtekintéséhez válassza a **naplók**lehetőséget.
 ![kiépítési naplók](media/how-to-troubleshoot/log1.png)

A kiépítési naplók rengeteg információt biztosítanak a helyszíni AD-környezet és az Azure között szinkronizált objektumok állapotáról.

 ![Üzembehelyezési naplók](media/how-to-troubleshoot/log2.png)

A lap tetején található legördülő lista használatával a nézet a megadott hibákra, dátumokra stb. alapján szűrheti a nézetet nulla értékre.  Ha duplán kattint egy adott eseményre, további részletes információkat is megadhat.

 ![Üzembehelyezési naplók](media/how-to-troubleshoot/log3.png)

Ezek az információk részletesen ismertetik a szinkronizálási problémát.  Így lehetővé teszi a nulla bekapcsolását, és pontosan a probléma pontos helyét.


## <a name="provisioning-quarantined-issues"></a>Karanténba helyezett problémák kiépítés

A Felhőbeli kiépítés figyeli a konfiguráció állapotát, és nem kifogástalan állapotú objektumokat helyez el a "karantén" állapotban. Ha a megcélzott rendszerre irányuló hívások többsége vagy mindegyike egy hiba miatt nem sikerül, például érvénytelen rendszergazdai hitelesítő adatok, a kiépítési feladat karanténként van megjelölve.

 ![Karantén](media/how-to-troubleshoot/quarantine1.png)

Az állapotra kattintva további információkat tekinthet meg a karanténról.  Megszerezheti a hibakódot és az üzenetet is.

 ![Karantén](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>Karantén feloldása

- A kiépítési feladatok újraindításához használja a Azure Portal. Az ügynök konfigurációja lapon válassza a **kiépítés újraindítása**lehetőséget.

  ![Karantén](media/how-to-troubleshoot/quarantine3.png)

- [A kiépítési feladatok újraindításához használja a](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)Microsoft Graph. Az újraindítást teljes mértékben szabályozhatja. Dönthet úgy, hogy törli a letéteket (a karantén állapotának újraindításához), a karantén törlését (az alkalmazás karanténból való eltávolítását) vagy a vízjelek törlését. Használja a következő kérelmet:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)




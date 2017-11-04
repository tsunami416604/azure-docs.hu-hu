---
title: "Az adatkezelési átjáró elhárítása |} Microsoft Docs"
description: "Tippek az adatkezelési átjáró kapcsolatos problémák elhárítása érdekében."
services: data-factory
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b3b34921168661089946b5c5dd9e6d489880733b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Az adatkezelési átjáró használata közben felmerülő hibák elhárítása
Ez a cikk tájékoztatást nyújt az adatkezelési átjáró használatával kapcsolatos hibák elhárításához.

> [!NOTE]
> Ez a cikk vonatkozik 1 a Azure Data Factory, amelyek általánosan elérhető (GA). Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [önállóan üzemel az adat-előállítóban 2-es integrációs futásidejű](../create-self-hosted-integration-runtime.md).

Tekintse meg a [az adatkezelési átjáró](data-factory-data-management-gateway.md) cikk az átjáró kapcsolatos részletes információkat. Tekintse meg a [helyezze át az adatokat a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) áthelyezése adatok egy helyi SQL Server-adatbázis a Microsoft Azure Blob storage az átjáró használatával kapcsolatos általános bemutatóért cikkében.

## <a name="failed-to-install-or-register-gateway"></a>Nem sikerült telepíteni vagy az átjáró regisztrálása
### <a name="1-problem"></a>1. Probléma
Megjelenik a hibaüzenet, amikor telepítése és regisztrálása az átjáró, az átjáró telepítési fájl letöltése során.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Ok
A gép, amelyen az átjáró telepítéséhez kívánt letöltőközpontból az átjáró legújabb telepítési fájlját a hálózati probléma miatt nem sikerült.

#### <a name="resolution"></a>Megoldás:
Ellenőrizze a tűzfal proxykiszolgáló beállításait megtekintéséhez, hogy a beállítások tiltsák le a hálózati kapcsolat a számítógépről a [letöltőközpontból](https://download.microsoft.com/), frissítse a beállításokat, és ennek megfelelően.

Azt is megteheti, letöltheti a legújabb átjárót telepítőfájlja a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717) más számítógépeken, amelyek hozzáférhetnek a letöltőközpontból. A telepítőfájl másolja az átjáró gazdaszámítógépet, majd futtassa azt manuálisan kell telepíteni, és frissítheti az átjárót.

### <a name="2-problem"></a>2. Probléma
Ezt a hibaüzenetet látja, ha telepít egy átjárót kattintva végrehajtani kívánt **telepíthető közvetlenül a számítógépen** az Azure portálon.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Ok
Átjáró már telepítve van a számítógépen.

#### <a name="resolution"></a>Megoldás:
Távolítsa el a gépen a jelenlegi átjárót, majd kattintson a **telepíthető közvetlenül a számítógépen** újra hivatkozásra.

### <a name="3-problem"></a>3. Probléma
Ez a hiba jelenhet meg, amikor egy új átjáró regisztrálása.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Ok
Ez az üzenet a következő okok valamelyike láthatja:

* Az átjáró kulcsát formátuma érvénytelen.
* Az átjáró kulcsát érvénytelenné vált.
* Az átjáró kulcsát újra lett létrehozza a rendszer a portálról.  

#### <a name="resolution"></a>Megoldás:
Győződjön meg arról, hogy használ-e a megfelelő átjárókulcsot, amit a portálon. Ha szükséges, a kulcs újragenerálása és a kulcs segítségével regisztrálja az átjárót.

### <a name="4-problem"></a>4. Probléma
Átjáró regisztrálásakor még a következő hibaüzenet jelenhet meg.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Tartalom és a kulcs formátuma érvénytelen](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Ok
A tartalom vagy a bemeneti átjárókulcs formátuma nem megfelelő. Egyik oka lehet, hogy a kulcsot csak egy részét átmásolva a portálon, vagy érvénytelen kulcsot használ.

#### <a name="resolution"></a>Megoldás:
Hozzon létre egy átjáró kulcsot a portálon, és használja a Másolás gombra a teljes kulcsot másolja. Illessze be az ablakot, hogy regisztrálja az átjárót majd.

### <a name="5-problem"></a>5. Probléma
Átjáró regisztrálásakor még a következő hibaüzenet jelenhet meg.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Átjáró kulcsa érvénytelen vagy üres:](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Ok
Az átjáró kulcsát újra lett létrehozva. vagy az átjáró törölve lett az Azure portálon. Akkor is előfordulhat, ha az adatkezelési átjáró beállítása nincs legújabb.

#### <a name="resolution"></a>Megoldás:
Ellenőrizze, hogy ha a telepítő az adatkezelési átjáró legújabb verziója, a Microsoft a legújabb verzió található [letöltőközpontból](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Ha a beállítás jelenlegi / legújabb és átjáró még létezik a portálon, újragenerálja az átjáró kulcsát az Azure-portálon használ a Másolás gombra a teljes kulcsot másolja, és majd illessze be az ablakot, hogy regisztrálja az átjárót. Ellenkező esetben hozza létre újra az átjárót, és kezdje újra a folyamatot.

### <a name="6-problem"></a>6. Probléma
Átjáró regisztrálásakor még a következő hibaüzenet jelenhet meg.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Átjáró kulcsa érvénytelen vagy üres:](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Ok
Ez a hiba akkor fordulhat elő, mert az átjáró törölve lett, vagy a kapcsolódó átjárókulcs újra lett létrehozva.

#### <a name="resolution"></a>Megoldás:
Ha az átjáró törölve lett, hozza létre újból az átjárót a portál, kattintson a **regisztrálása**, a kulcs másolása a portálról, illessze be és próbálja meg regisztrálni az átjárót.

Ha az átjáró még létezik, de a kulcsot újra lett létrehozva., az új kulcs segítségével regisztrálja az átjárót. Ha még nem rendelkezik a kulcsot, újragenerálja a kulcsot újra a portálról.

### <a name="7-problem"></a>7. Probléma
Átjáró regisztrálásakor van szükség lehet a tanúsítvány elérési útja és a jelszó megadását.

![Adja meg a tanúsítvány](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Ok
Az átjáró más gépeken előtt regisztrálva van. Átjáró, a kezdeti regisztráció során egy titkosítási tanúsítvány nincs hozzárendelve az átjárót. A tanúsítvány lehet önálló az átjáró által generált, vagy a felhasználó által megadott.  Ezzel a tanúsítvánnyal az adattár (társított szolgáltatás) hitelesítő adatok titkosításához.  

![Tanúsítvány exportálása](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Az átjáró másik gazdaszámítógépet visszaállításakor a regisztrációs varázsló megkérdezi, a tanúsítvány visszafejtése a korábban a tanúsítvánnyal titkosított hitelesítő adatokat.  Ez a tanúsítvány nélkül nem tudja visszafejteni a hitelesítő adatokat az új átjáró, és az új átjáró társított későbbi másolási tevékenység végrehajtások meghiúsul.  

#### <a name="resolution"></a>Megoldás:
Ha exportálta a hitelesítési tanúsítványt az eredeti átjáró gépen használatával a **exportálása** gombra a **beállítások** az adatkezelési átjáró konfigurációkezelőjének lapon, a tanúsítvány használatára itt.

Ebben a szakaszban nem hagyható ki, amikor egy átjáró helyreállítása. Ha a tanúsítvány hiányzik, törölje az átjárót a portálról, és hozza létre az új átjáró szüksége.  Emellett frissítse az átjáró által ismét be kell írni a hitelesítő adataik kapcsolódó összes társított szolgáltatások.

### <a name="8-problem"></a>8. Probléma
A következő hibaüzenet jelenhet meg.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Ok
Ez a hiba történik, ha az átjáró egy olyan környezetben, szükséges a HTTP-proxy elérni az internetes erőforrásokhoz, vagy a proxy hitelesítési jelszó megváltozik, de nem frissül megfelelően a átjáróban.

#### <a name="resolution"></a>Megoldás:
Kövesse az utasításokat a [Proxy server szempontjai](#proxy-server-considerations) Ez a szakasz a következő cikket, és konfigurálja a proxybeállításokat az adatkezelési átjáró konfigurációkezelőjének.

## <a name="gateway-is-online-with-limited-functionality"></a>Átjáró korlátozott funkciójú online állapotban
### <a name="1-problem"></a>1. Probléma
Az átjáró állapotának online, az korlátozott funkciókkal láthatja.

#### <a name="cause"></a>Ok
Megjelenik az átjáró állapotának online korlátozott szolgáltatásokkal a következő okok valamelyike:

* Átjáró nem tud kapcsolódni a felhőalapú szolgáltatás Azure Service Buson keresztül.
* A felhőalapú szolgáltatás nem tud kapcsolódni a Service Buson keresztül átjáró.

Ha az átjáró online korlátozott szolgáltatásokkal, nem feltétlenül a Data Factory másolása varázsló használatával hozzon létre az adatok másolása, vagy a helyszíni adattárolókhoz adatok folyamatok. A probléma megoldásához használhatja Data Factory Editor a portálon, a Visual Studio vagy az Azure PowerShell.

#### <a name="resolution"></a>Megoldás:
A probléma feloldása (online korlátozott funkciójú) azon alapul, hogy az átjáró nem tud csatlakozni a felhőalapú szolgáltatás vagy a más módon. A következő szakaszokban ezek a megoldások.

### <a name="2-problem"></a>2. Probléma
Az alábbi hibaüzenet látható.

`Error: Gateway cannot connect to cloud service through service bus`

![Átjáró felhőalapú szolgáltatás nem tud kapcsolódni.](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Ok
Átjáró nem tud kapcsolódni a Service Buson keresztül a felhőalapú szolgáltatáshoz.

#### <a name="resolution"></a>Megoldás:
Kövesse az alábbi lépéseket követve az átjáró online:

1. IP-címet az átjáró számítógépe és a vállalati tűzfalon kimenő szabályok engedélyezése. Található IP-címek a Windows Eseménynapló (ID == 401): kísérlet történt olyan módon, a hozzáférési engedélyeket XX tiltott hozzáférés. A(Z) XX. A(Z) XX. XX:9350.
* Proxybeállítások konfigurálása az átjárón. Tekintse meg a [Proxy server szempontjai](#proxy-server-considerations) című szakaszban talál információt.
* Engedélyezze a kimenő portok 5671 és 9350 – 9354-es mindkét a Windows tűzfal az átjáró gépen és a vállalati tűzfalon. Tekintse meg a [portok és a tűzfalon](#ports-and-firewall) című szakaszban talál információt. Ez a lépés nem kötelező, de a teljesítmény okokból ajánlott.

### <a name="3-problem"></a>3. Probléma
Az alábbi hibaüzenet látható.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Ok
Egy átmeneti hiba történt a hálózati kapcsolatot.

#### <a name="resolution"></a>Megoldás:
Kövesse az alábbi lépéseket követve az átjáró online:

1. Várjon néhány percig, a kapcsolatot a rendszer automatikusan helyreállítja a eltűnt a hiba esetén.
* Ha a probléma továbbra is fennáll, indítsa újra az átjáró szolgáltatást.

## <a name="failed-to-author-linked-service"></a>Nem sikerült a társított szolgáltatás létrehozásához
### <a name="problem"></a>Probléma
Ez a hiba jelenhet meg a portálon hitelesítőadat-kezelő segítségével adjon meg egy új társított szolgáltatás hitelesítő adatait, vagy egy meglévő kapcsolt szolgáltatás hitelesítő adatait megkísérlésekor.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Amikor megjelenik ez a hiba, a beállítások lapon az adatkezelési átjáró konfigurációkezelőjének nézhet ki például az alábbi képernyőfelvételen.

![Adatbázis nem érhető el](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Ok
Az SSL-tanúsítvány előfordulhat, hogy már megszakadt az átjáró számítógépén. Az átjáró-számítógép nem tudja betölteni a jelenleg használt tanúsítvány az SSL-titkosítást. Az eseménynaplóban, az alábbihoz hasonló hibaüzenetet is megjelenhet.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Megoldás:
Kövesse az alábbi lépéseket a probléma megoldásához:

1. Indítsa el az adatkezelési átjáró Konfigurációkezelőjében.
2. Váltás a **beállítások** fülre.  
3. Kattintson a **módosítása** gombra kattintva módosíthatja az SSL-tanúsítvány.

   ![Módosítás tanúsítvány gombra](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Jelöljön ki új tanúsítványt, az SSL-tanúsítvány. Bármely Ön által létrehozott SSL-tanúsítvány vagy bármely szervezeti is használhatja.

   ![Adja meg a tanúsítvány](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Másolási tevékenység sikertelen lesz.
### <a name="problem"></a>Probléma
Bizonyára észrevette, hogy a következő "UserErrorFailedToConnectToSqlserver" hiba a portál folyamat beállítása után.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Ok
Ez akkor fordulhat elő különböző okokból, és a megoldás ennek megfelelően változik.

#### <a name="resolution"></a>Megoldás:
Engedélyezi az kimenő TCP-kapcsolatok az adatkezelési átjáró ügyféloldali TCP/1433-as porton keresztül egy SQL-adatbázishoz szeretne csatlakozni.

Ha a céladatbázis Azure SQL-adatbázis, ellenőrizze az SQL-kiszolgáló a tűzfal beállításai Azure is.

A következő részben a helyszíni adattárolóihoz létrehozott kapcsolat ellenőrzéséhez.

## <a name="data-store-connection-or-driver-related-errors"></a>Az adattároló csatlakozási vagy kapcsolatos hibák
Ha adatok tárolásához a kapcsolódáshoz vagy az illesztőprogram-kapcsolatos hibákat, kövesse az alábbi lépéseket:

1. Indítsa el az adatkezelési átjáró Konfigurációkezelőjét az átjárót működtető gépen.
2. Váltás a **diagnosztika** fülre.
3. A **kapcsolat tesztelése**, az átjáró csoport értékek hozzáadásához.
4. Kattintson a **teszt** megjelenítéséhez, ha csatlakozhat a helyszíni adatforráshoz az átjárót működtető gépen a kapcsolati adatokat, és a hitelesítő adatok használatával. Amennyiben a kapcsolat tesztelése az illesztő telepítése után is sikertelen, indítsa újra az átjárót, hogy az érvényesítse a legutóbbi módosítást.

![Kapcsolat tesztelése a Diagnosztika lap](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Átjáró naplói
### <a name="send-gateway-logs-to-microsoft"></a>Átjáró naplókat küldeni a Microsoftnak
Amikor kapcsolatba lép a Microsoft Support átjáró problémák hibaelhárításával kapcsolatos, a program kérheti megosztani az átjáró naplói. Az átjáró számára készült kötelező átjáró naplók és a két gombra történő kattintás az adatkezelési átjáró konfigurációkezelőjének is megoszthatja.    

1. Váltás a **diagnosztika** az adatkezelési átjáró konfigurációkezelőjének fülre.

    ![Felügyeleti átjáró Diagnostics lapon](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Kattintson a **naplók küldése** a következő párbeszédpanel megjelenítéséhez.

    ![Felügyeleti átjáró küldése adatnaplók](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Választható) Kattintson a **naplók megtekintése** áttekintéséhez naplózza az esemény viewer.
4. (Választható) Kattintson a **adatvédelmi** áttekintése a Microsoft web services adatvédelmi nyilatkozatát.
5. Ha elégedett a feltöltés, kattintson a kívánt esetén **naplók küldése** ténylegesen a naplók az elmúlt hét napban a Microsoftnak küldendő hibaelhárításhoz. Az alábbi képernyőfelvételen látható módon kell megjelennie a küldési-naplók művelet állapotát.

    ![Adatok felügyeleti átjáró küldése naplózza állapota](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. A művelet befejezése után megjelenik egy párbeszédpanel az alábbi képernyőfelvételen látható módon.

    ![Adatok felügyeleti átjáró küldése naplózza állapota](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Mentse a **azonosítója** és ossza meg a Microsoft Support. A jelentés Azonosítót az átjáró naplók hibaelhárítási feltöltött kereséséhez használható.  A jelentés Azonosítót is a menti viewer.  Az eseményazonosító "25" megtekintésével megtalálja, és ellenőrizze a dátum és idő.

    ![Adatok felügyeleti átjáró küldése naplózza az azonosítója](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Az átjáró állomás számítógépén archív-átjáró naplói
Léteznek olyan forgatókönyvek, ahol átjáró problémák vannak, és közvetlenül nem osztható meg átjáró naplói:

* Manuálisan telepítse az átjárót, és regisztrálja az átjárót.
* Próbálja meg regisztrálni az átjárót az adatkezelési átjáró konfigurációkezelőjének újragenerált kulccsal.
* Próbálja meg elküldeni a naplókat, és nem lehet csatlakozni az átjáró gazdaszolgáltatás.

Ezek a forgatókönyvek az átjáró naplói elmentse egy zip-fájlt, és ossza meg, amikor a Microsoft támogatási szolgálatához. Például ha arra vonatkozó hibaüzenetet kap, amíg az átjáró, regisztrálnia jelenik meg az alábbi képernyőfelvételen.   

![Felügyeleti átjáró regisztrációs hiba](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Kattintson a **átjáró naplók archiválása** csatolása archivált, és mentse a naplókat, és majd a zip-fájl megosztása a Microsoft támogatási szolgálatához.

![Felügyeleti átjáró archív adatnaplók](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Keresse meg az átjáró naplói
A Windows eseménynaplóiban keresse meg a részletes átjáró naplóadatok találja.

1. A Windows indítása **Eseménynapló**.
2. Keresse meg a naplókat a **alkalmazás- és szolgáltatásnaplók** > **az adatkezelési átjáró** mappát.

 Átjáró kapcsolatos problémák elhárításakor még keressen szintű hibaesemények az Eseménynapló viewer.

![Az adatkezelési átjáró naplózza az eseménynaplóban](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)

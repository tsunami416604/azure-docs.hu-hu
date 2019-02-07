---
title: Az adatkezelési átjáró hibáinak elhárítása |} A Microsoft Docs
description: Tippek a Data Management Gateway kapcsolatos problémák elhárítása.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: dcbf011d6e5f035a1934b69f94cf95b2318491f0
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813840"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Az adatkezelési átjáró használata közben felmerülő hibák elhárítása
Ez a cikk információkat biztosít az adatkezelési átjáró segítségével kapcsolatos hibák elhárítása.

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [saját üzemeltetésű integrációs modul az adat-előállító](../create-self-hosted-integration-runtime.md).

Tekintse meg a [adatkezelési átjáró](data-factory-data-management-gateway.md) ismertető cikkben talál részletes információt az átjárót. Tekintse meg a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) bemutató cikk, amely adatokat helyez át egy helyszíni SQL Server-adatbázis a Microsoft Azure Blob storage az átjáró használatával.

## <a name="failed-to-install-or-register-gateway"></a>Nem sikerült telepíteni vagy átjáró regisztrálása
### <a name="1-problem"></a>1. Probléma
Ez a hibaüzenet, ha telepítése és regisztrálása az átjáró, az átjáró telepítőfájl letöltése közben látni.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Ok
A gép, amelyen az átjáró telepítése kívánt hálózati hiba következtében a letöltőközpontból töltheti le az átjáró legújabb telepítési fájlját nem sikerült.

#### <a name="resolution"></a>Megoldás:
Ellenőrizze a proxy server tűzfalbeállításokat megtekintéséhez, hogy a beállítások letiltása a hálózati kapcsolat a számítógépről a [letöltőközpontból](https://download.microsoft.com/), frissítse a beállításokat, és ennek megfelelően.

Másik lehetőségként letöltheti a telepítőfájlt a legújabb átjáróhoz a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717) más gépeken, amelyek hozzáférhetnek a letöltőközpontból. A telepítőfájl átmásolása az átjáró gazdaszámítógépet, majd manuálisan kell telepíteni és frissíteni az átjáró futtatható.

### <a name="2-problem"></a>2. Probléma
Ezt a hibát látja, amikor az átjáró telepítéséhez kattintson a végrehajtani kívánt **telepítheti közvetlenül a számítógépre** az Azure Portalon.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Ok
Az átjáró már telepítve van a gépen.

#### <a name="resolution"></a>Megoldás:
Távolítsa el a meglévő átjárót a gépen, majd kattintson a **telepítheti közvetlenül a számítógépre** újra hivatkozásra.

### <a name="3-problem"></a>3. Probléma
Előfordulhat, hogy ezt a hibaüzenetet, ha új átjáró regisztrálása.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Ok
Ez az üzenet a következő okok valamelyike jelenhetnek meg:

* Az átjáró kulcs formátuma érvénytelen.
* Az átjáró kulcs érvénytelenné vált.
* Az átjáró kulcs a portálról rendelkezik újra lett létrehozva.  

#### <a name="resolution"></a>Megoldás:
Győződjön meg arról, hogy használ-e a megfelelő átjáró kulcsot a portálról. Ha szükséges, újragenerálja a kulcsot, és a kulcs használatával regisztrálnia kell az átjárót.

### <a name="4-problem"></a>4. Probléma
Láthatja a következő hibaüzenetet kapja, már regisztrálhatja az átjárót.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Tartalom és a kulcs formátuma érvénytelen](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Ok
A tartalma vagy a bemeneti átjáró kulcs formátuma helytelen. Az okok egyike miatt lehet, hogy csak egy része a kulcsot a portálról kimásolt, vagy érvénytelen kulcsot használ.

#### <a name="resolution"></a>Megoldás:
Hozzon létre egy átjáró kulcsot a portálon, és a másolási gomb használatával másolja a teljes kulcsot. Majd illessze be az ablakot, hogy regisztrálja az átjárót.

### <a name="5-problem"></a>5. Probléma
Láthatja a következő hibaüzenetet kapja, már regisztrálhatja az átjárót.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Átjáró kulcsa érvénytelen vagy nincs megadva](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Ok
Az átjáró kulcs újbóli létrehozása rendelkezik lett, vagy az átjáró törölve lett az Azure Portalon. Azt is történhet, ha a Data Management Gateway beállítása nem a legutóbbi.

#### <a name="resolution"></a>Megoldás:
Ellenőrizze, hogy ha az adatkezelési átjárót a telepítő legújabb verziója, Észreveheti, hogy a legújabb verziót a Microsoft [letöltőközpontból](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Ha a beállítás jelenlegi / legújabb és átjáró még létezik-e a portálon, az Azure Portalon, az átjáró kulcsának újragenerálása és a másolási gomb használatával másolja a teljes kulcsot, és majd illessze be az ablakot, hogy regisztrálja az átjárót. Ellenkező esetben hozza létre újra az átjárót, és kezdje elölről.

### <a name="6-problem"></a>6. Probléma
Láthatja a következő hibaüzenetet kapja, már regisztrálhatja az átjárót.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Átjáró kulcsa érvénytelen vagy nincs megadva](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Ok
Ez a hiba akkor fordulhat elő, mert az átjáró törölve lett, vagy a kapcsolódó átjárókulcs újra lett létrehozva.

#### <a name="resolution"></a>Megoldás:
Ha az átjáró törölve lett, hozza létre újból az átjárót a portálon, kattintson a **regisztrálása**, másolja a kulcsot a portálról, illessze be és próbálja meg regisztrálni az átjárót.

Ha az átjáró továbbra is létezik, de rendelkezik lett újragenerálta a kulcsot, az új kulcs használatával regisztrálnia kell az átjárót. Ha a kulcs nem rendelkezik, újragenerálja a kulcsot a portálról újra.

### <a name="7-problem"></a>7. Probléma
Az átjáró regisztráció során szüksége lehet adja meg a tanúsítvány elérési útja és a jelszavát.

![Adja meg a tanúsítvány](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Ok
Az átjáró regisztrálása előtt más gépeken. Egy átjáró, a kezdeti regisztráció során egy titkosítási tanúsítvány nincs hozzárendelve az átjárót. A tanúsítvány az átjáró által önállóan létrehozott vagy a felhasználó által megadott.  Ezt a tanúsítványt az adattár (társított szolgáltatás) hitelesítő adatok titkosításához használatos.  

![Tanúsítvány exportálása](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Az átjáró egy másik gazdagépen visszaállításakor a regisztrációs varázsló kéri, a tanúsítvány visszafejtése a tanúsítvánnyal a korábban titkosított hitelesítő adatokat.  Ez a tanúsítvány nélkül a hitelesítő adatokat nem lehet visszafejteni az új átjárót, és az új átjáró társított későbbi másolási tevékenység-végrehajtások sikertelenek lesznek.  

#### <a name="resolution"></a>Megoldás:
Ha rendelkezik exportált hitelesítőadat-tanúsítványa az eredeti átjárót tartalmazó számítógépen használatával a **exportálása** gombot a **beállítások** a Data Management Gateway Configuration Manager lapon, a tanúsítvány használatára itt.

Ebben a szakaszban az átjáró helyreállításakor nem hagyhatja ki. Ha a tanúsítvány hiányzik, törölje az átjárót a portálon, és a egy új átjárót hozhat újra létre szüksége.  Emellett frissítse az összes társított szolgáltatás által kell újra megadniuk hitelesítő adataikat az átjáróhoz kapcsolódó.

### <a name="8-problem"></a>8. Probléma
Láthatja, hogy a következő hibaüzenetet kapja.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Ok
Ez a hiba akkor fordul elő, amikor az átjáró egy olyan környezetben, szükséges egy HTTP-proxy el az internetes erőforrások, vagy a proxy hitelesítési jelszó megváltozik, de nem frissül megfelelően működik az átjáróban.

#### <a name="resolution"></a>Megoldás:
A Proxy server szempontok című szakaszt a cikk utasításait, és konfigurálja a proxybeállításokat a Data Management Gateway Configuration Managerrel.

## <a name="gateway-is-online-with-limited-functionality"></a>Átjáró korlátozott funkciókkal online állapotban
### <a name="1-problem"></a>1. Probléma
Az átjáró állapotának, online, korlátozott funkciókkal láthatja.

#### <a name="cause"></a>Ok
Megjelenik az átjáró állapotának online korlátozott funkciókkal a következő okok valamelyike:

* Átjáró nem tud csatlakozni a felhőalapú szolgáltatás az Azure Service Buson keresztül.
* A felhőalapú szolgáltatás nem tud kapcsolódni a gateway Service Buson keresztül.

Ha az átjáró online korlátozott funkciókkal, nem feltétlenül tudja használni a Data Factory Copy varázslót, vagy a helyszíni adattárakból származó adatok másolása az adatfolyamatok létrehozására. Áthidaló megoldásként használhatja a portal, a Visual Studio vagy az Azure PowerShell-lel a Data Factory Editor.

#### <a name="resolution"></a>Megoldás:
A probléma megoldási (online korlátozott funkciókkal) alapuló e az átjáró nem tud csatlakozni a felhőszolgáltatáshoz, vagy a más módon. A következő szakaszok ezek a megoldások.

### <a name="2-problem"></a>2. Probléma
Az alábbi hibát látja.

`Error: Gateway cannot connect to cloud service through service bus`

![Átjáró nem tud csatlakozni a felhőszolgáltatáshoz](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Ok
Átjáró nem tud csatlakozni a felhőszolgáltatáshoz a Service Buson keresztül.

#### <a name="resolution"></a>Megoldás:
Kövesse az alábbi lépéseket az átjáró online állapotba:

1. IP-címének engedélyezéséhez az átjárót tartalmazó számítógépen, és a vállalati tűzfalon a kimenő szabályok. Annak IP-címek a Windows Eseménynapló (azonosítója == 401-es): Kísérlet történt olyan módon, a hozzáférési engedélyeket XX tiltott hozzáférés. XX. XX. XX:9350.
* Proxybeállítások konfigurálása az átjárón. Tekintse meg a Proxy server szempontok című szakasz részletezi.
* Engedélyezze a kimenő 5671 és 9350 – 9354-es mind a Windows tűzfal az átjárót tartalmazó számítógépen és a vállalati tűzfalon. A portok és a tűzfal részleteket a következő szakaszban talál. Ez a lépés nem kötelező, de ajánlott kiegészítője teljesítményének figyelembe veszi.

### <a name="3-problem"></a>3. Probléma
Az alábbi hibát látja.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Ok
Átmeneti hiba történt a hálózati kapcsolatot.

#### <a name="resolution"></a>Megoldás:
Kövesse az alábbi lépéseket az átjáró online állapotba:

1. Várjon néhány percet, a kapcsolat a rendszer automatikusan helyreállítja a eltűnt a hiba esetén.
* Ha a hiba továbbra is fennáll, indítsa újra az átjárószolgáltatást.

## <a name="failed-to-author-linked-service"></a>Nem sikerült létrehozni a társított szolgáltatás
### <a name="problem"></a>Probléma
Előfordulhat, hogy ezt a hibaüzenetet a portálon a hitelesítőadat-kezelő használatával adjon meg egy új társított szolgáltatás hitelesítő adatait, vagy egy meglévő társított szolgáltatás hitelesítő adatainak frissítése közben.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Ha ezt a hibát látja, a beállítások lapon Data Management Gateway Configuration Manager a következő képernyőképhez hasonlóan nézhet ki.

![Adatbázis nem érhető el](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Ok
Az SSL-tanúsítvány előfordulhat, hogy már megszakadt az átjárót tartalmazó számítógépen. Az átjáró-számítógép nem tudja betölteni a jelenleg használt tanúsítványt az SSL-titkosítást. Az eseménynaplóban az alábbihoz hasonló hibaüzenetet is megjelenhet.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Megoldás:
Kövesse az alábbi lépéseket a probléma megoldásához:

1. Indítsa el a Data Management Gateway Configuration Manager.
2. Váltson a **Settings** (Beállítások) lapra.  
3. Kattintson a **módosítása** gombra kattintva módosíthatja az SSL-tanúsítványt.

   ![Módosítás tanúsítvány gombra](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Válasszon egy új tanúsítványt SSL-tanúsítványt. Bármely Ön által létrehozott SSL-tanúsítvány vagy bármely szervezet is használhatja.

   ![Adja meg a tanúsítvány](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Másolási tevékenység sikertelen
### <a name="problem"></a>Probléma
A következő "UserErrorFailedToConnectToSqlserver" hiba előfordulhat, hogy figyelje meg, miután beállította egy folyamatot a portálon.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Ok
Ez különböző okok miatt fordulhat elő, és kockázatcsökkentési ennek megfelelően változik.

#### <a name="resolution"></a>Megoldás:
Kimenő TCP-kapcsolat engedélyezése az adatkezelési átjáró ügyféloldalon TCP/1433-as porton keresztül SQL Database-adatbázishoz való csatlakozás előtt.

Ha a céladatbázis egy Azure SQL database, ellenőrizze az SQL Server Tűzfalbeállításaiban az Azure-hoz is.

A következő részben tesztelje a kapcsolatot a helyszíni adattárban.

## <a name="data-store-connection-or-driver-related-errors"></a>Az adattároló kapcsolat vagy illesztőprogram-kapcsolatos hibák
Ha kapcsolat vagy illesztőprogram kapcsolatos hibákat tárolt adatokat látja, hajtsa végre az alábbi lépéseket:

1. Indítsa el a Data Management Gateway Configuration Manager az átjárót tartalmazó számítógépen.
2. Váltson a **diagnosztikai** fülre.
3. A **kapcsolat tesztelése**, adja hozzá az átjáró értékeit.
4. Kattintson a **teszt** megtekintheti, ha csatlakozhat a helyszíni adatforráshoz az átjárót tartalmazó számítógépen a kapcsolati adatokat és hitelesítő adatok használatával. Amennyiben a kapcsolat tesztelése az illesztő telepítése után is sikertelen, indítsa újra az átjárót, hogy az érvényesítse a legutóbbi módosítást.

![Diagnosztika lap a kapcsolat tesztelése](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Átjárónaplók
### <a name="send-gateway-logs-to-microsoft"></a>Átjáró-naplók küldése a Microsoftnak
Amikor kapcsolatba lép a Microsoft Support segítség a hibaelhárítási kérdéseket, előfordulhat, hogy megkérdezi, hogy az átjáró naplóit megosztásához. Az átjáró kiadása szükséges átjárónaplók két gombra kattintással a Data Management Gateway Configuration Manager megoszthatja.    

1. Váltson a **diagnosztikai** fülre a Data Management Gateway Configuration Managerben.

    ![Data Management Gateway-Diagnosztika lap](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Kattintson a **naplók küldése** a következő párbeszédpanel megjelenítéséhez.

    ![Data Management Gateway küldhet naplók](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Nem kötelező) Kattintson a **megtekinthetők a naplófájlok** tekintse át a naplók az eseménynaplóban megjelenítő.
4. (Nem kötelező) Kattintson a **adatvédelmi** , tekintse át a Microsoft web services adatvédelmi nyilatkozatát.
5. Ha elégedett tölthet fel, kattintson a kívánt **naplók küldése** ténylegesen a naplók az elmúlt hét nap a Microsoftnak küldendő hibaelhárításhoz. A naplók küldése művelet állapotát az alábbi képernyőképen látható módon kell megjelennie.

    ![Data Management Gateway küldhet naplók állapota](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. A művelet befejezése után egy párbeszédpanelen megjelenik az alábbi képernyőképen látható módon.

    ![Data Management Gateway küldhet naplók állapota](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Mentse a **Jelentésazonosító** , és megoszthatja Support. A Jelentésazonosító keresse meg az átjáró naplóit hibaelhárítási feltöltött szolgál.  A jelentés Azonosítót is menti a rendszer az megjelenítő.  Keresse meg azt az eseményazonosító "25" megnézzük, és ellenőrizze a dátum és idő.

    ![Data Management Gateway küldhet naplók jelentés azonosítója](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Átjáró gazdagépen archív-átjáró naplói
Léteznek olyan forgatókönyvek, ahol kérdéseket rendelkezik, és közvetlenül nem oszthat meg átjárónaplók:

* Manuális módszerrel telepítse az átjárót, és regisztrálja az átjárót.
* Próbálja meg a kulcs újragenerálása a Data Management Gateway Configuration Manager regisztrálja az átjárót.
* Próbálja meg elküldeni a naplókat, és az átjárószolgáltatás gazdagép nem lehet csatlakozni.

Ebben az esetben átjárónaplók mentheti egy zip-fájlba, és ossza meg, amikor kapcsolatba lép a Microsoft ügyfélszolgálatához. Például ha hibaüzenetet kap, akkor regisztrálnia kell az átjárót, amíg a következő képernyőképen látható.   

![Data Management Gateway regisztrációs hiba](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Kattintson a **átjáró naplói archiválása** archiválni és menteni a naplók hivatkozásra, és megoszthatják a zip-fájlt a Microsoft ügyfélszolgálatához.

![Data Management Gateway archív naplók](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Keresse meg az átjáró naplóit
A Windows eseménynaplóiban annak részletes átjáró adatait.

1. Indítsa el a Windows **Eseménynapló**.
2. Keresse meg a naplókat a **alkalmazás- és szolgáltatásnaplók** > **adatkezelési átjáró** mappát.

 Átjáró kapcsolatos problémák elhárításakor van keressen hibaszintű eseményeket az eseménynaplóban megjelenítő.

![Az eseménynaplóban naplózza az adatkezelési átjáró](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)

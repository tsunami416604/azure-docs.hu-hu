---
title: Adatkezelési átjáróval kapcsolatos problémák elhárítása
description: Tippek az Adatkezelési átjáróval kapcsolatos problémák elhárításához.
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 09d51de3ae0bd4baca585d2abdd936b1a29567d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065029"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Az adatkezelési átjáró használata közben felmerülő hibák elhárítása
Ez a cikk az Adatkezelési átjáró használatával kapcsolatos problémák elhárításáról nyújt tájékoztatást.

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [önkiszolgáló integrációs futásidejű adatgyárat.](../create-self-hosted-integration-runtime.md)

Az átjáróval kapcsolatos részletes információkért tekintse meg az [Adatkezelési átjáró](data-factory-data-management-gateway.md) cikkét. Tekintse meg az [adatok áthelyezése a helyszíni és a felhőbeli](data-factory-move-data-between-onprem-and-cloud.md) cikk et a helyszíni SQL Server-adatbázisból a Microsoft Azure Blob storage-ba az átjáró használatával történő átcsoportosításának forgatókönyvét.

## <a name="failed-to-install-or-register-gateway"></a>Nem sikerült telepíteni vagy regisztrálni az átjárót
### <a name="1-problem"></a>1. Probléma
Ez a hibaüzenet jelenik meg, amikor telepít iratot és regisztrál egy átjárót, különösen az átjáró telepítési fájljának letöltésekénél.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Ok
Az a gép, amelyre az átjárót telepíteni próbálja, hálózati probléma miatt nem tudta letölteni a legújabb átjárótelepítő fájlt a letöltőközpontból.

#### <a name="resolution"></a>Megoldás:
Ellenőrizze a tűzfalproxy-kiszolgáló beállításait, és ellenőrizze, hogy a beállítások blokkolják-e a hálózati kapcsolatot a számítógép és a [letöltőközpont](https://download.microsoft.com/)között, és ennek megfelelően frissítse a beállításokat.

Másik lehetőségként letöltheti a legújabb átjáró telepítési fájlját a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717) más gépeken, amelyek hozzáférhetnek a letöltőközponthoz. Ezután átmásolhatja a telepítőfájlt az átjárógazda számítógépre, és manuálisan futtathatja az átjáró telepítéséhez és frissítéséhez.

### <a name="2-problem"></a>2. Probléma
Ez a hibaüzenet jelenik meg, amikor egy átjárót próbál telepíteni, ha **közvetlenül erre a számítógépre** kattint az Azure Portalon a telepítésre kattintva.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Ok
Már telepítve van egy átjáró a számítógépen.

#### <a name="resolution"></a>Megoldás:
Távolítsa el a meglévő átjárót a számítógépen, és kattintson ismét közvetlenül a **telepítésre a számítógép kapcsolatán.**

### <a name="3-problem"></a>3. Probléma
Ez a hiba új átjáró regisztrálásakor jelenhet meg.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Ok
Ez az üzenet a következő okok miatt jelenhet meg:

* Az átjárókulcs formátuma érvénytelen.
* Az átjárókulcs érvénytelenné vált.
* Az átjárókulcs újralett létrehozva a portálról.  

#### <a name="resolution"></a>Megoldás:
Ellenőrizze, hogy a megfelelő átjárókulcsot használja-e a portálról. Szükség esetén hozzon létre újra egy kulcsot, és a kulcs segítségével regisztrálja az átjárót.

### <a name="4-problem"></a>4. Probléma
Az átjáró regisztrálásakor a következő hibaüzenet jelenhet meg.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Érvénytelen a kulcs tartalma vagy formátuma](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Ok
A bemeneti átjárókulcs tartalma vagy formátuma helytelen. Ennek egyik oka lehet, hogy a kulcsnak csak egy részét másolta a portálról, vagy érvénytelen kulcsot használ.

#### <a name="resolution"></a>Megoldás:
Hozzon létre egy átjárókulcsot a portálon, és a másolás gombbal másolja a teljes kulcsot. Ezután illessze be ebbe az ablakba az átjáró regisztrálásához.

### <a name="5-problem"></a>5. Probléma
Az átjáró regisztrálásakor a következő hibaüzenet jelenhet meg.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Az átjárókulcs érvénytelen vagy üres](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Ok
Az átjárókulcs újralett létrehozva, vagy az átjáró törölve lett az Azure Portalon. Ez akkor is előfordulhat, ha az adatkezelési átjáró beállítása nem legújabb.

#### <a name="resolution"></a>Megoldás:
Ellenőrizze, hogy az Adatkezelési átjáró beállítása a legújabb verzió-e, a [microsoftos letöltőközpontban](https://go.microsoft.com/fwlink/p/?LinkId=271260)találja a legújabb verziót.

Ha a telepítő aktuális/ legújabb, és az átjáró továbbra is létezik a portálon, hozza létre újra az átjárókulcsot az Azure Portalon, és a másolás gombbal másolja a teljes kulcsot, majd illessze be ebbe az ablakba az átjáró regisztrálásához. Ellenkező esetben hozza létre újra az átjárót, és kezdje újra.

### <a name="6-problem"></a>6. Probléma
Az átjáró regisztrálásakor a következő hibaüzenet jelenhet meg.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Az átjárókulcs érvénytelen vagy üres](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Ok
Ez a hiba azért fordulhat elő, mert az átjárót törölték, vagy a társított átjárókulcsot újralétrehozták.

#### <a name="resolution"></a>Megoldás:
Ha az átjáró t törölték, hozza létre újra az átjárót a portálról, kattintson a **Regisztráció**gombra, másolja a kulcsot a portálról, illessze be, és próbálja meg regisztrálni az átjárót.

Ha az átjáró továbbra is létezik, de a kulcs újralett létrehozva, az új kulccsal regisztrálja az átjárót. Ha nem rendelkezik a kulccsal, hozza létre újra a kulcsot a portálról.

### <a name="7-problem"></a>7. Probléma
Átjáró regisztrálásakor előfordulhat, hogy meg kell adnia egy tanúsítvány elérési útját és jelszavát.

![Tanúsítvány megadása](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Ok
Az átjáró már korábban is regisztrálva van más gépeken. Az átjáró első regisztrálása során egy titkosítási tanúsítvány lett társítva az átjáróhoz. A tanúsítvány tönállóan generálhatja az átjáró, vagy a felhasználó biztosítja.  Ez a tanúsítvány az adattár (csatolt szolgáltatás) hitelesítő adatainak titkosítására szolgál.  

![Tanúsítvány exportálása](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Amikor az átjárót egy másik gazdagépen állítja vissza, a regisztrációs varázsló kéri a tanúsítványt a tanúsítvánnyal korábban titkosított hitelesítő adatok visszafejtésére.  E tanúsítvány nélkül a hitelesítő adatokat az új átjáró nem tudja visszafejteni, és az új átjáróhoz társított további másolási tevékenység-végrehajtások sikertelenek lesznek.  

#### <a name="resolution"></a>Megoldás:
Ha a hitelesítő adatok tanúsítványát az eredeti átjárógépről exportálta az Adatkezelési átjáró konfigurációkezelőjének **Beállítások** lapján található **Exportálás** gombbal, használja az itt található tanúsítványt.

Ezt a szakaszt nem hagyhatja ki átjáró helyreállításakor. Ha a tanúsítvány hiányzik, törölnie kell az átjárót a portálról, és újra létre kell hoznia egy új átjárót.  Ezenkívül frissítse az átjáróhoz kapcsolódó összes csatolt szolgáltatást a hitelesítő adataik újbóli megadásával.

### <a name="8-problem"></a>8. Probléma
A következő hibaüzenet jelenhet meg.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Ok
Ez a hiba akkor fordul elő, ha az átjáró olyan környezetben van, amelyben HTTP-proxy szükséges az internetes erőforrások eléréséhez, vagy a proxy hitelesítési jelszava megváltozik, de ennek megfelelően nem frissül az átjáróban.

#### <a name="resolution"></a>Megoldás:
Kövesse a cikk Proxykiszolgálóval kapcsolatos szempontokra vonatkozó részében található utasításokat, és konfigurálja a proxybeállításokat az Adatkezelési átjáró konfigurációkezelőjével.

## <a name="gateway-is-online-with-limited-functionality"></a>Az átjáró online, korlátozott funkcionalitással
### <a name="1-problem"></a>1. Probléma
Az átjáró állapota korlátozott funkcionalitással rendelkező online állapotként jelenik meg.

#### <a name="cause"></a>Ok
Az átjáró állapota korlátozott funkcionalitással rendelkező online állapota a következő okok valamelyike miatt:

* Az átjáró nem tud csatlakozni a felhőszolgáltatáshoz az Azure Service Bus-on keresztül.
* A felhőszolgáltatás nem tud csatlakozni az átjáróhoz a Service Bus-on keresztül.

Ha az átjáró korlátozott funkcionalitással rendelkezik, előfordulhat, hogy nem tudja használni a Data Factory Copy wizardt adatfolyamatok létrehozására a helyszíni adattárakba vagy a helyszíni adattárakból történő másoláshoz. Kerülő megoldásként használhatja a Data Factory Editor a portálon, a Visual Studio vagy az Azure PowerShell.

#### <a name="resolution"></a>Megoldás:
A probléma megoldása (korlátozott funkcionalitással rendelkező online) attól függ, hogy az átjáró nem tud-e csatlakozni a felhőszolgáltatáshoz, vagy más módon. A következő szakaszok biztosítják ezeket a megoldásokat.

### <a name="2-problem"></a>2. Probléma
A következő hibaüzenet jelenik meg.

`Error: Gateway cannot connect to cloud service through service bus`

![Az átjáró nem tud csatlakozni a felhőszolgáltatáshoz](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Ok
Az átjáró nem tud csatlakozni a felhőszolgáltatáshoz a Service Bus szolgáltatáson keresztül.

#### <a name="resolution"></a>Megoldás:
Az átjáró online állapotba hozása érdekében kövesse az alábbi lépéseket:

1. Ip-cím kimenő szabályok engedélyezése az átjárógépen és a vállalati tűzfalon. Az IP-címeket a Windows eseménynaplóból (ID == 401): Kísérlet történt a szoftvercsatorna elérésére a XX hozzáférési engedélyei által tiltott módon. Xx. Xx. XX:9350.
1. Konfigurálja a proxybeállításokat az átjárón. A részleteket a Proxykiszolgáló valamerre vonatkozó szempontok című részében találja.
1. Engedélyezze az 5671-es és a 9350-9354-es kimenő portokat az átjárószámítógép Windows tűzfalán és a vállalati tűzfalon is. A részleteket a Portok és tűzfal című részben találja. Ez a lépés nem kötelező, de azt javasoljuk, hogy a teljesítmény megfontolás.

### <a name="3-problem"></a>3. Probléma
A következő hibaüzenet jelenik meg.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Ok
Átmeneti hiba a hálózati kapcsolatban.

#### <a name="resolution"></a>Megoldás:
Az átjáró online állapotba hozása érdekében kövesse az alábbi lépéseket:

1. Várjon néhány percet, a kapcsolat automatikusan helyreáll, ha a hiba eltűnik.
1. Ha a hiba továbbra is fennáll, indítsa újra az átjárószolgáltatást.

## <a name="failed-to-author-linked-service"></a>Nem sikerült a csatolt szolgáltatás szerzője
### <a name="problem"></a>Probléma
Ez a hibaüzenet akkor jelenhet meg, amikor a portálon a Hitelesítő adatok kezelőjével próbál hitelesítő adatokat megadni egy új csatolt szolgáltatáshoz, vagy frissíti egy meglévő csatolt szolgáltatás hitelesítő adatait.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Ha ezt a hibát látja, az Adatkezelési átjáró konfigurációkezelőjének beállítási lapja a következő képernyőképhez hasonlóan nézhet ki.

![Az adatbázis nem érhető el](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Ok
Lehet, hogy a TLS/SSL-tanúsítvány elveszett az átjárógépen. Az átjárószámítógép nem tudja betölteni a TLS-titkosításhoz jelenleg használt tanúsítványt. Az eseménynaplóban az alábbihoz hasonló hibaüzenet is megjelenhet.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Megoldás:
A probléma megoldásához kövesse az alábbi lépéseket:

1. Indítsa el az Adatkezelési átjáró konfigurációkezelőjét.
2. Váltson a **Beállítások** lapra.  
3. A TLS/SSL-tanúsítvány módosításához kattintson a **Módosítás** gombra.

   ![Tanúsítvány módosítása gomb](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Válasszon új tanúsítványt TLS/SSL tanúsítványként. Bármilyen TLS/SSL tanúsítványt használhat, amelyet Ön vagy bármely szervezet hoz létre.

   ![Tanúsítvány megadása](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>A másolási tevékenység sikertelen
### <a name="problem"></a>Probléma
Előfordulhat, hogy a következő "UserErrorFailedToConnectToSqlserver" hiba jelenik meg, miután beállított egy folyamatot a portálon.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Ok
Ez különböző okok miatt fordulhat elő, és a kockázatcsökkentés ennek megfelelően változik.

#### <a name="resolution"></a>Megoldás:
Sql-adatbázishoz való csatlakozás előtt engedélyezze a kimenő TCP-kapcsolatokat a TCP/1433 porton keresztül az Adatkezelési átjáró ügyféloldalán.

Ha a céladatbázis egy Azure SQL-adatbázis, ellenőrizze az SQL Server tűzfal beállításait az Azure-ban is.

Tekintse meg a következő szakaszban a helyszíni adattárhoz való csatlakozás teszteléséhez.

## <a name="data-store-connection-or-driver-related-errors"></a>Adattár-kapcsolat vagy illesztőprogrammal kapcsolatos hibák
Ha adattár-kapcsolat vagy illesztőprogrammal kapcsolatos hibákat lát, hajtsa végre az alábbi lépéseket:

1. Indítsa el az Adatkezelési átjáró konfigurációkezelőjét az átjárógépen.
2. Váltson a **Diagnosztika** lapra.
3. A **Kapcsolat tesztelése csoportban**adja hozzá az átjárócsoport értékeit.
4. Kattintson **a Tesztelés** gombra, ha meg szeretné tekinteni, hogy az átjárógépről csatlakozhat-e a helyszíni adatforráshoz a kapcsolati adatok és hitelesítő adatok használatával. Amennyiben a kapcsolat tesztelése az illesztő telepítése után is sikertelen, indítsa újra az átjárót, hogy az érvényesítse a legutóbbi módosítást.

![Kapcsolat tesztelése a Diagnosztika lapon](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Átjárónaplók
### <a name="send-gateway-logs-to-microsoft"></a>Átjárónaplók küldése a Microsoftnak
Amikor a Microsoft támogatási szolgálatához szeretne segítséget kérni az átjárókkal kapcsolatos problémák elhárításához, előfordulhat, hogy meg kell osztania az átjárónaplókat. Az átjáró kiadásával megoszthatja a szükséges átjárónaplókat két gombbal az Adatkezelési átjáró konfigurációkezelőjében.    

1. Váltson az Adatkezelési átjáró **konfigurációkezelőjének Diagnosztika** fülére.

    ![Adatkezelési átjáró diagnosztikája lapja](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. A **naplók küldése gombra** kattintva megtekintheti a következő párbeszédpanelt.

    ![Adatkezelési átjáró naplók küldése](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Nem kötelező) Kattintson **a naplók megtekintéséhez** naplók az eseménynaplóban.
4. (Nem kötelező) Kattintson az **adatvédelemre** a Microsoft webszolgáltatások adatvédelmi nyilatkozatának áttekintéséhez.
5. Ha elégedett azzal, amit fel szeretne tölteni, kattintson a **Naplók küldése gombra,** ha az elmúlt hét nap naplóit ténylegesen el szeretné küldeni a Microsoftnak hibaelhárításra. A küldési naplók művelet állapotát az alábbi képernyőképen látható módon kell látnia.

    ![Adatkezelési átjáró Naplók küldése állapot](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. A művelet befejezése után megjelenik egy párbeszédpanel, ahogy az a következő képernyőképen látható.

    ![Adatkezelési átjáró Naplók küldése állapot](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Mentse a **jelentésazonosítót,** és ossza meg a Microsoft támogatási szolgálatával. A jelentésazonosító segítségével megkeresheti a hibaelhárításhoz feltöltött átjárónaplókat.  A jelentésazonosító az eseménynaplóba is mentésre kerül.  Megtalálhatja, ha megnézi a "25" eseményazonosítót, és ellenőrzi a dátumot és az időt.

    ![Adatkezelési átjáró Naplók jelentésazonosítójának küldése](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Átjárónaplók archiválása az átjárógazdagépen
Vannak olyan esetek, amikor átjáróproblémák merülnek fel, és nem oszthatja meg közvetlenül az átjárónaplókat:

* Manuálisan telepíti az átjárót, és regisztrálja az átjárót.
* Próbálja meg regisztrálni az átjárót egy újragenerált kulccsal az Adatkezelési átjáró konfigurációkezelőjében.
* Naplókat próbál küldeni, és az átjárógazda szolgáltatás nem csatlakoztatható.

Az ilyen esetekben az átjárónaplókat zip-fájlként mentheti, és megoszthatja, amikor kapcsolatba lép a Microsoft támogatási szolgálatával. Ha például hibaüzenetet kap az átjáró regisztrálása közben, ahogy az az alábbi képernyőképen látható.   

![Adatkezelési átjáró regisztrációs hibája](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Kattintson az **Archív átjáró naplók** hivatkozásra a naplók archiválásához és mentéséhez, majd ossza meg a zip fájlt a Microsoft támogatásával.

![Adatkezelési átjáró archiválási naplói](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Átjárónaplók megkeresése
Az átjárónapló részletes adatait a Windows eseménynaplóiban találja.

1. Indítsa el a Windows **Eseménynaplót**.
2. Keresse meg a naplókat az **Alkalmazás- és szolgáltatásnaplók** > **adatkezelési átjárómappájában.**

   Az átjáróval kapcsolatos problémák elhárításakor keresse meg a hibaszintű eseményeket az eseménynaplóban.

![Adatkezelési átjáró naplói az eseménynaplóban](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)

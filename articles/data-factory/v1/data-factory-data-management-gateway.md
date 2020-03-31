---
title: Adatkezelési átjáró az adatgyárhoz
description: Állítson be egy adatátjárót az adatok helyszíni és a felhő közötti áthelyezéséhez. Az adatok áthelyezéséhez használja az Azure Data Factory adatkezelési átjáróját.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1340c205477b256e3d96ff7ccacb64e575725c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065407"
---
# <a name="data-management-gateway"></a>Adatkezelési átjáró
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [önkiszolgáló integrációs futásidejű t.](../create-self-hosted-integration-runtime.md)

> [!NOTE]
> Az adatkezelési átjáró mostantól saját üzemeltetésű integrációs futásidőként lett átnevezve.

Az adatkezelési átjáró egy ügyfélügynök, amelyet telepítenie kell a helyszíni környezetben az adatok másolásához a felhőbeli és a helyszíni adattárak között. A Data Factory által támogatott helyszíni adattárak a [Támogatott adatforrások](data-factory-data-movement-activities.md#supported-data-stores-and-formats) szakaszban találhatók.

Ez a cikk kiegészíti az adatok áthelyezése a [helyszíni és a felhőbeli adattárolók között](data-factory-move-data-between-onprem-and-cloud.md) cikk forgatókönyvét. A forgatókönyvben hozzon létre egy folyamatot, amely az átjáró segítségével átaz adatokat egy helyszíni SQL Server-adatbázis egy Azure blob. Ez a cikk részletes információkat tartalmaz az adatkezelési átjáróról.

Az adatkezelési átjárót úgy skálázhatja ki, hogy több helyszíni gépet társít az átjáróval. A növekvő skálázhatja az adatmozgatási feladatok számának növelésével, amelyek egyidejűleg futtathatók egy csomóponton. Ez a szolgáltatás egy egyetlen csomópontos logikai átjáróesetén is elérhető. A részletekért tekintse meg [az Adatkezelési átjáró méretezése az Azure Data Factory cikkében.](data-factory-data-management-gateway-high-availability-scalability.md)

> [!NOTE]
> Jelenleg átjáró támogatja csak a másolási tevékenység és a tárolt eljárás tevékenység Data Factory. Az átjáró egyéni tevékenységből nem használható a helyszíni adatforrások eléréséhez.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
### <a name="capabilities-of-data-management-gateway"></a>Az adatkezelési átjáró képességei
Az adatkezelési átjáró a következő lehetőségeket biztosítja:

* Modellezze a helyszíni adatforrásokat és a felhőalapú adatforrásokat ugyanazon az adat-előállítón belül, és helyezze át az adatokat.
* Egyetlen ablaktábla a figyelés és a felügyelet, a Data Factory oldalon az átjáró állapotának láthatóságát.
* A helyszíni adatforrásokhoz való hozzáférés biztonságos kezelése.
  * A vállalati tűzfalon nincs szükség módosításokra. Az átjáró csak kimenő HTTP-alapú kapcsolatokat létesít a nyitott internethez.
  * Titkosítsa a helyszíni adattárak hitelesítő adatait a tanúsítvánnyal.
* Adatok hatékony mozgatása – az adatok átvitele párhuzamosan történik, és az automatikus újrapróbálkozási logikával rugalmasak az időszakos hálózati problémákkal szemben.

### <a name="command-flow-and-data-flow"></a>Parancsfolyam és adatfolyam
Ha másolási tevékenység használatával másolja az adatokat a helyszíni és a felhőközötti adatok másolásához, a tevékenység egy átjárót használ az adatok átviteléhez a helyszíni adatforrásból a felhőbe, és fordítva.

Itt van a magas szintű adatfolyam és az adatátjáróval történő másolás lépéseinek összegzése: ![Adatfolyam átjáró használatával](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Az adatfejlesztő az [Azure Portal vagy](https://portal.azure.com) a [PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/az.datafactory/)használatával hoz létre átjárót az Azure Data Factory számára.
2. Az adatfejlesztő az átjáró megadásával létrehoz egy csatolt szolgáltatást a helyszíni adattárhoz. A csatolt szolgáltatás beállításának részeként az adatfejlesztő a Hitelesítő adatok beállítása alkalmazást használja a hitelesítési típusok és hitelesítő adatok megadására. A Hitelesítő adatok beállítása alkalmazás párbeszédpanel kommunikál az adattárral a kapcsolat teszteléséhez és az átjáróhoz a hitelesítő adatok mentéséhez.
3. Az átjáró titkosítja a hitelesítő adatokat az átjáróhoz társított (az adatfejlesztő által biztosított) tanúsítvánnyal, mielőtt a hitelesítő adatokat a felhőbe mentené.
4. A Data Factory szolgáltatás kommunikál az átjáróval a feladatok ütemezéséhez & felügyeletéhez egy megosztott Azure-szolgáltatásbusz-várólistát használó vezérlőcsatornán keresztül. Ha egy másolási tevékenység feladatot ki kell rúgni, a Data Factory várólistára helyezi a kérelmet a hitelesítő adatokkal együtt. Az átjáró a várólista lekérdezése után elindítja a feladatot.
5. Az átjáró visszafejti a hitelesítő adatokat ugyanazzal a tanúsítvánnyal, majd megfelelő hitelesítési típussal és hitelesítő adatokkal csatlakozik a helyszíni adattárhoz.
6. Az átjáró adatokat másol egy helyszíni tárolóból egy felhőalapú tárolóba, vagy fordítva attól függően, hogy a másolási tevékenység hogyan van konfigurálva az adatfolyamatban. Ehhez a lépéshez az átjáró közvetlenül kommunikál a felhőalapú tárolási szolgáltatásokkal, például az Azure Blob Storage-szal egy biztonságos (HTTPS) csatornán keresztül.

### <a name="considerations-for-using-gateway"></a>Az átjáró használatának szempontjai
* Az adatkezelési átjáró egyetlen példánya több helyszíni adatforráshoz is használható. Azonban **egy átjárópéldány csak egy Azure-adat-előállítóhoz van kötve,** és nem osztható meg egy másik adat-előállítóval.
* Egyetlen számítógépre csak **egy adatkezelési átjáró példánya** telepíthető. Tegyük fel, hogy két adatüzemet kell elérnie a helyszíni adatforrásokhoz, és két helyszíni számítógépre kell telepítenie az átjárókat. Más szóval egy átjáró egy adott adat-előállítóhoz van kötve
* Az **átjárónak nem kell ugyanazon a gépen lennie, mint az adatforrásnak.** Ha azonban az átjáró közelebb van az adatforráshoz, csökken az idő, hogy az átjáró csatlakozzon az adatforráshoz. Azt javasoljuk, hogy telepítse az átjárót egy olyan számítógépre, amely eltér a helyszíni adatforrást tartalmazótól. Ha az átjáró és az adatforrás különböző gépeken van, az átjáró nem versenyez az adatforrással rendelkező erőforrásokért.
* Több átjárót is létrehozhat **különböző gépeken, amelyek ugyanahhoz a helyszíni adatforráshoz csatlakoznak.** Előfordulhat például, hogy két átjáró két adatgyárat szolgál ki, de ugyanaz a helyszíni adatforrás regisztrálva van mindkét adatgyárban.
* Ha már telepítve van egy power **BI-forgatókönyvet** kiszolgáló számítógépre telepített átjáró, telepítsen egy **külön átjárót az Azure Data Factory számára** egy másik számítógépre.
* Az átjárót akkor is használni kell, ha **ExpressRoute-ot**használ.
* Az adatforrást az **ExpressRoute**használata esetén is helyszíni adatforrásként kezelje (amely tűzfal mögött van). Az átjáró segítségével kapcsolatot létesítsen a szolgáltatás és az adatforrás között.
* Akkor is használnia kell **az átjárót,** ha az adattár a felhőben van egy **Azure IaaS virtuális gépen.**

## <a name="installation"></a>Telepítés
### <a name="prerequisites"></a>Előfeltételek
* A támogatott **operációs rendszer** verziói a Következők: Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Az adatkezelési átjáró telepítése tartományvezérlőre jelenleg nem támogatott.
* A . Ha átjárót telepít Windows 7 rendszerű számítógépre, telepítse a .NET Framework 4.5-ös vagy újabb verziót. A részleteket a [.NET framework rendszerkövetelményei](https://msdn.microsoft.com/library/8z6watww.aspx) ben találja.
* Az átjárógép ajánlott **konfigurációja** legalább 2 GHz, 4 mag, 8 GB RAM és 80 GB lemez.
* Ha a gazdagép hibernált állapotban van, az átjáró nem válaszol az adatkérésekre. Ezért az átjáró telepítése előtt konfigurálja a megfelelő **energiasémát** a számítógépen. Ha a számítógép hibernálásra van konfigurálva, az átjáró telepítése üzenetet küld.
* Az adatkezelési átjáró sikeres telepítéséhez és konfigurálásához rendszergazdai jogosultságnak kell lennie a számítógépen. További felhasználókat adhat hozzá az **adatkezelési átjáróhoz: A helyi** Windows-csoport. A csoport tagjai az **Adatkezelési átjáró konfigurációkezelő eszközével** konfigurálhatják az átjárót.

Másolási tevékenység futtatása történik egy adott gyakorisággal, az erőforrás-használat (CPU, memória) a gépen is követi ugyanazt a mintát a csúcs és tétlen alkalommal. Az erőforrások kihasználtságátis nagymértékben függ az áthelyezett adatok mennyiségétől. Ha több másolási feladat van folyamatban, az erőforrás-használat csúcsidőben felfelé halad.

### <a name="installation-options"></a>Telepítési lehetőségek
Az adatkezelési átjáró a következő módokon telepíthető:

* Az MSI telepítőcsomag letöltésével a [Microsoft letöltőközpontból.](https://www.microsoft.com/download/details.aspx?id=39717) Az MSI a meglévő adatkezelési átjáró legújabb verzióra történő frissítésére is használható, az összes beállítás megőrzésével.
* Kattintson **az Adatátjáró letöltése és telepítése** hivatkozásra a KÉZI BEÁLLÍTÁS vagy a Telepítés menüpont alatt **közvetlenül erre a számítógépre az** EXPRESS SETUP csoportban. Az [adatok áthelyezése a helyszíni és a felhőbeli](data-factory-move-data-between-onprem-and-cloud.md) környezet között című témakörben részletes útmutatást talál az expressz telepítés használatával kapcsolatban. A manuális lépés a letöltőközpontba vezet. Az átjáró letöltésére és telepítésére vonatkozó utasítások a következő szakaszban találhatók.

### <a name="installation-best-practices"></a>A telepítéssel kapcsolatos gyakorlati tanácsok:
1. Konfigurálja az energiasémát a gazdagépen az átjáróhoz, hogy a számítógép ne hibernáljon. Ha a gazdagép hibernált állapotban van, az átjáró nem válaszol az adatkérésekre.
2. Biztonsági másolatot kell adni az átjáróhoz társított tanúsítványról.

### <a name="install-the-gateway-from-download-center"></a>Az átjáró telepítése a letöltőközpontból
1. Nyissa meg a [Microsoft Data Management Gateway letöltési lapját.](https://www.microsoft.com/download/details.aspx?id=39717)
2. Kattintson a **Letöltés**gombra, válassza ki a **64 bites** verziót (a 32 bites verzió már nem támogatott), majd kattintson a **Tovább**gombra.
3. Futtassa közvetlenül az **MSI-t,** vagy mentse a merevlemezre, és futtassa.
4. Az **Üdvözlőlapon** válasszon egy **nyelvet,** és kattintson a **Tovább**gombra.
5. **Fogadja el** a végfelhasználói licencszerződést, és kattintson a **Tovább**gombra.
6. Jelölje ki az átjáró telepítéséhez szükséges **mappát,** és kattintson a **Tovább**gombra.
7. A **Telepítésre kész** oldalon kattintson a **Telepítés** elemre.
8. A telepítés befejezéséhez kattintson a **Befejezés** gombra.
9. A kulcs beszerezni az Azure Portalon. A következő szakaszban részletes útmutatást talál.
10. A számítógépen futó **Data Management Gateway Configuration Manager** **átjáróregisztrálási** lapján tegye a következő lépéseket:
    1. Illessze be a kulcsot a szövegbe.
    2. A kulcs szövegének megjelenítéséhez kattintson az **átjárókulcs megjelenítése** gombra.
    3. Kattintson a **Regisztráció gombra.**

### <a name="register-gateway-using-key"></a>Átjáró regisztrálása kulcs használatával
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Ha még nem hozott létre logikai átjárót a portálon
Ha átjárót szeretne létrehozni a portálon, és le szeretné szerezni a kulcsot a **Konfigurálás** lapról, kövesse az adatok áthelyezése a [helyszíni és a felhőbeli](data-factory-move-data-between-onprem-and-cloud.md) cikk forgatókönyveit.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Ha már létrehozta a logikai átjárót a portálon
1. Az Azure Portalon keresse meg a **Data Factory** lapot, és kattintson a **Csatolt szolgáltatások** csempére.

    ![Data Factory oldal](media/data-factory-data-management-gateway/data-factory-blade.png)
2. A **Csatolt szolgáltatások** lapon jelölje ki a portálon létrehozott logikai **átjárót.**

    ![logikai átjáró](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Az **Adatátjáró** lapon kattintson az **Adatátjáró letöltése és telepítése gombra.**

    ![Letöltési hivatkozás a portálon](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. A **Konfigurálás** lapon kattintson a **Kulcs újralétrehozása gombra.** A figyelmeztető üzeneten kattintson az Igen gombra, miután figyelmesen elolvasta.

    ![Kulcs újbóli létrehozása](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Kattintson a kulcs melletti Másolás gombra. A program a kulcsot a vágólapra másolja.

    ![Kulcs másolása](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Rendszertálca ikonok/ értesítések
Az alábbi képen látható néhány tálcaikon látható.

![tálcák ikonjai](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Ha a kurzort a rendszertálca ikonja/értesítési üzenete fölé viszi, egy előugró ablakban láthatja az átjáró/frissítési művelet állapotának részleteit.

### <a name="ports-and-firewall"></a>Portok és tűzfal
Két tűzfalat kell figyelembe vennie: a szervezet központi útválasztóján futó **vállalati tűzfalat** és a **Windows tűzfalat** démonként konfigurálva azon a helyi számítógépen, amelyen az átjáró telepítve van.

![Tűzfalak](./media/data-factory-data-management-gateway/firewalls2.png)

A vállalati tűzfal szintjén a következő tartományokat és kimenő portokat kell konfigurálnia:

| Tartománynevek | Portok | Leírás |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Az adatmozgatási szolgáltatás háttérrendszerével való kommunikációhoz használható |
| *.core.windows.net |443 |Szakaszos másoláshoz használható az Azure Blob használatával (ha konfigurálva van)|
| *.frontend.clouddatahub.net |443 |Az adatmozgatási szolgáltatás háttérrendszerével való kommunikációhoz használható |
| *.servicebus.windows.net |9350-9354, 5671 |A Másolás varázsló által használt tcp-n keresztüli választható szolgáltatásbusz-továbbítás |

A Windows tűzfal szintjén ezek a kimenő portok általában engedélyezve vannak. Ha nem, akkor a tartományok és portok ennek megfelelően konfigurálható az átjárógépen.

> [!NOTE]
> 1. A forrás/fogadók alapján előfordulhat, hogy a vállalati/Windows tűzfalon további tartományokat és kimenő portokat kell engedélyezési listára tenni.
> 2. Egyes felhőalapú adatbázisok (például: [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), Azure Data [Lake,](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access)stb), előfordulhat, hogy engedélyezési listára kell tenni az átjárógép IP-címét a tűzfal konfigurációján.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Adatok másolása forrásadattárból fogadóadattárba
Győződjön meg arról, hogy a tűzfalszabályok megfelelően vannak engedélyezve a vállalati tűzfalon, a Windows tűzfalon az átjárógépen és magán az adattárban. A szabályok engedélyezése lehetővé teszi, hogy az átjáró sikeresen csatlakozzon a forráshoz és a fogadóhoz. Engedélyezze a szabályokat a másolási műveletben részt vevő minden egyes adattárhoz.

Ha például egy helyszíni adattárból szeretne másolni **egy Azure SQL Database-fogadóba vagy egy Azure SQL Data Warehouse-fogadóba,** tegye a következő lépéseket:

* Kimenő **TCP-kommunikáció** engedélyezése az **1433-as** porton a Windows tűzfal és a vállalati tűzfal esetében is.
* Konfigurálja az Azure SQL-kiszolgáló tűzfalbeállításait, hogy az átjárógép IP-címét hozzáadja az engedélyezett IP-címek listájához.

> [!NOTE]
> Ha a tűzfal nem engedélyezi az 1433-as kimenő portot, a Gateway nem tud közvetlenül hozzáférni az Azure SQL-hez. Ebben az esetben [használhatja a szakaszos másolás](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) sql Azure Database/ SQL Azure DW. Ebben a forgatókönyvben csak HTTPS (443-as port) az adatáthelyezés.
>
>

### <a name="proxy-server-considerations"></a>A proxykiszolgálóval kapcsolatos szempontok
Ha a vállalati hálózati környezet proxykiszolgálót használ az internet eléréséhez, konfigurálja az adatkezelési átjárót a megfelelő proxybeállítások használatára. Beállíthatja a proxy a kezdeti regisztrációs fázisban.

![Proxy beállítása a regisztráció során](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Az átjáró a proxykiszolgáló segítségével csatlakozik a felhőszolgáltatáshoz. Kattintson **a Hivatkozás módosítása** gombra a kezdeti beállítás során. Megjelenik a **proxybeállítási** párbeszédpanel.

![Proxy beállítása a konfigurációkezelő vel](media/data-factory-data-management-gateway/SetProxySettings.png)

Három beállítási lehetőség van:

* **Ne használjon proxyt:** Az átjáró nem használ kifejezetten proxyt a felhőszolgáltatásokhoz való csatlakozáshoz.
* **Rendszerproxy használata**: Az átjáró a diahost.exe.config és diawp.exe.config fájlban konfigurált proxybeállítást használja. Ha nincs proxy konfigurálva a diahost.exe.config és diawp.exe.config fájlban, az átjáró közvetlenül, proxy nélkül csatlakozik a felhőszolgáltatáshoz.
* **Egyéni proxy használata**: A diahost.exe.config és diawp.exe.config konfigurációk helyett konfigurálja a HTTP-proxybeállítást az átjáróhoz. Cím és port szükséges. A felhasználónév és a jelszó megadása nem kötelező a proxy hitelesítési beállításától függően. Minden beállítás titkosítva van az átjáró hitelesítő tanúsítványával, és helyileg tárolódik az átjárógazdagépen.

Az adatkezelési átjáró gazdaszolgáltatása a frissített proxybeállítások mentése után automatikusan újraindul.

Az átjáró sikeres regisztrálása után, ha meg szeretné tekinteni vagy frissíteni szeretné a proxybeállításokat, használja az Adatkezelési átjáró konfigurációkezelőjét.

1. Indítsa el **az Adatkezelési átjáró konfigurációkezelőjét**.
2. Váltson a **Beállítások** lapra.
3. A **HTTP-proxy beállítása** párbeszédpanel elindításához kattintson a **HTTP-proxy** beállítása párbeszédpanel Hivatkozás **módosítása** gombjára.
4. Miután a **Tovább** gombra kattintott, megjelenik egy figyelmeztető párbeszédpanel, amely engedélyt kér a proxybeállítás mentésére és az Átjárógazda szolgáltatás újraindítására.

A HTTP-proxyt a Configuration Manager eszközzel tekintheti meg és frissítheti.

![Proxy beállítása a konfigurációkezelő vel](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Ha NTLM-hitelesítéssel rendelkező proxykiszolgálót állít be, a Gateway Host Service a tartományi fiók alatt fut. Ha később módosítja a tartományi fiók jelszavát, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, és ennek megfelelően indítsa újra. Ennek a követelménynek köszönhetően azt javasoljuk, hogy használjon dedikált tartományi fiókot a proxykiszolgáló eléréséhez, amely nem igényli a jelszó gyakori frissítését.
>
>

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló beállításainak konfigurálása
Ha a **HTTP-proxy rendszerproxy-beállításának használata** lehetőséget választja, az átjáró a diahost.exe.config és a diawp.exe.config proxybeállítást használja. Ha nincs megadva proxy a diahost.exe.config és a diawp.exe.config fájlban, az átjáró közvetlenül, proxy nélkül csatlakozik a felhőszolgáltatáshoz. Az alábbi eljárás a diahost.exe.config fájl frissítésével kapcsolatos utasításokat tartalmaz.

1. A Fájlkezelőben készítsen biztonságos másolatot a *\\\\C:\\Program\\Files\\\\Microsoft Data Management Gateway 2.0 Shared diahost.exe.config fájlról* az eredeti fájl biztonsági másolatához.
2. A Notepad.exe indítása rendszergazdaként, és a C szövegfájl *megnyitása:\\\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared\\diahost.exe.config*. A system.net alapértelmezett címkéjét a következő kódban látható módon találja meg:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Ezután hozzáadhatja a proxykiszolgáló adatait az alábbi példában látható módon:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    További tulajdonságok engedélyezettek a proxycímkében a szükséges beállítások megadásához, például a scriptLocation. Lásd a [szintaxis proxyelemét (Hálózati beállítások).](https://msdn.microsoft.com/library/sa91de1e.aspx)

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Mentse a konfigurációs fájlt az eredeti helyre, majd indítsa újra az Adatkezelési átjárógazda szolgáltatást, amely felveszi a módosításokat. A szolgáltatás újraindításához: használja a szolgáltatások kisalkalmazása a vezérlőpulton, vagy az **Adatkezelési átjáró konfigurációkezelő** > kattintson a **Szolgáltatás leállítása** gombra, majd kattintson a **Start Szolgáltatás**. Ha a szolgáltatás nem indul el, valószínű, hogy helytelen XML-címkeszintaxist adott hozzá a szerkesztett alkalmazáskonfigurációs fájlhoz.

> [!IMPORTANT]
> Ne felejtsük el, hogy frissítse **mind** diahost.exe.config és diawp.exe.config.

Ezeken a pontokon kívül meg kell győződnie arról is, hogy a Microsoft Azure szerepel a vállalat engedélyezési listáján. Az érvényes Microsoft Azure IP-címek listája letölthető a [Microsoft letöltőközpontjából.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>A tűzfallal és a proxykiszolgálóval kapcsolatos problémák lehetséges tünetei
Ha a következőhöz hasonló hibákat észlel, annak valószínűleg a tűzfal vagy a proxykiszolgáló nem megfelelő konfigurációja miatt van szó, amely megakadályozza, hogy az átjáró csatlakozzon a Data Factory-hoz, hogy hitelesítse magát. A tűzfal és a proxykiszolgáló megfelelő konfigurálásának ellenőrzése az előző szakaszban található.

1. Az átjáró regisztrálásakor a következő hibaüzenet jelenik meg: "Nem sikerült regisztrálni az átjárókulcsot. Mielőtt újra megpróbálna regisztrálni az átjárókulcsot, ellenőrizze, hogy az adatkezelési átjáró csatlakoztatott állapotban van-e, és az Adatkezelési átjárógazda szolgáltatás elindult."
2. A Configuration Manager megnyitásakor az állapot "Leválasztva" vagy "Csatlakozás" állapotú. A Windows eseménynaplóinak megtekintésekor az "Eseménynapló" > az "Alkalmazás- és szolgáltatásnaplók" > az "Adatkezelési átjáró" területen a következő hibaüzenetek jelennek meg:`Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>A 8050-es port megnyitása a hitelesítő adatok titkosításához
A **konfigurációs hitelesítő adatok** alkalmazás a bejövő **8050-es** porton keresztül továbbítja a hitelesítő adatokat az átjáróba, amikor beállít egy helyszíni csatolt szolgáltatást az Azure Portalon. Az átjáró telepítése során az átjáró telepítése alapértelmezés szerint megnyitja azt az átjárógépen.

Ha külső gyártótól származó tűzfalat használ, manuálisan is megnyithatja a 8050-es portot. Ha az átjáró telepítése során tűzfalprobléma lép fel, a következő paranccsal telepítheti az átjárót a tűzfal konfigurálása nélkül.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Ha úgy dönt, hogy nem nyitja meg a 8050-es portot az átjárógépen, a **Hitelesítő adatok beállítása** alkalmazáson kívül más mechanizmust is használhat az adattár hitelesítő adatainak konfigurálásához. Használhatja például a [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell-parancsmast. Az adattár hitelesítő adatainak beállításáról a Hitelesítő adatok beállítása című részben.

## <a name="update"></a>Frissítés
Alapértelmezés szerint az adatkezelési átjáró automatikusan frissül, ha az átjáró újabb verziója érhető el. Az átjáró nem frissül, amíg az összes ütemezett feladat el nem készült. Az átjáró a frissítési művelet befejezéséig nem dolgoz fel további feladatokat. Ha a frissítés sikertelen, az átjáró visszaáll a régi verzióra.

Az ütemezett frissítési idő a következő helyeken jelenik meg:

* Az átjáró tulajdonságai lap az Azure Portalon.
* Az Adatkezelési átjáró konfigurációkezelőjének kezdőlapja
* A rendszertálca értesítési üzenete.

Az Adatkezelési átjáró konfigurációkezelőjének Kezdőlap lapja megjeleníti a frissítési ütemezést és az átjáró legutóbbi telepítésének/frissítésének időpontja.

![Frissítések ütemezése](media/data-factory-data-management-gateway/UpdateSection.png)

Azonnal telepítheti a frissítést, vagy megvárhatja, amíg az átjáró automatikusan frissül az ütemezett időpontban. Az alábbi képen például az átjárókonfiguráció-kezelőben megjelenő értesítési üzenet látható a Frissítés gombbal együtt, amelyre kattintva azonnal telepítheti.

![Frissítés a DMG Configuration Manager ben](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

A tálcán lévő értesítési üzenet az alábbi képen látható módon jelenik meg:

![Tálcás üzenet](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

A frissítési művelet (manuális vagy automatikus) állapota a tálcán jelenik meg. Amikor legközelebb elindítja az Átjáró konfigurációkezelőjét, megjelenik egy üzenet az értesítési sávon, amely szerint az átjáró frissült, valamint egy hivatkozás az [új témakörre.](data-factory-gateway-release-notes.md)

### <a name="to-disableenable-auto-update-feature"></a>Az automatikus frissítési szolgáltatás letiltása/engedélyezése
Az automatikus frissítési szolgáltatást az alábbi lépésekkel tilthatja le/engedélyezheti:

[Egycsomópontos átjáró esetén]
1. Indítsa el a Windows PowerShellt az átjárógépen.
2. Váltson a *C:\\\\Program Files\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\ * mappára.
3. Futtassa a következő parancsot az automatikus frissítéskikapcsolására (letiltására).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Kapcsolja be újra:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Magas szintű elérhető és méretezhető többcsomópontos átjáróhoz](data-factory-data-management-gateway-high-availability-scalability.md)
1. Indítsa el a Windows PowerShellt az átjárógépen.
2. Váltson a *C:\\\\Program Files\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\ * mappára.
3. Futtassa a következő parancsot az automatikus frissítéskikapcsolására (letiltására).

    A magas rendelkezésre állású funkcióval rendelkező átjáróhoz egy további AuthKey param szükséges.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Kapcsolja be újra:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Az átjáró telepítése után az alábbi módokon indíthatja el az Adatkezelési átjáró konfigurációkezelőjét:

1. A **Keresés** ablakban írja be az **Adatkezelési átjáró** kifejezést a segédprogram eléréséhez.
2. Futtassa a futtatható *ConfigManager.exe* fájlt a Következő mappában: *C:\\\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared*.

### <a name="home-page"></a>Kezdőlap
A kezdőlapon a következő műveleteket teheted:

* Az átjáró állapotának megtekintése (a felhőszolgáltatáshoz csatlakoztatva stb.).
* **Regisztráljon** egy kulccsal a portálról.
* **Állítsa le** és indítsa el az **Adatkezelési átjárógazda szolgáltatást** az átjárógépen.
* **A frissítéseket** a napok egy adott időpontjában ütemezheti.
* Az átjáró **legutóbbi frissítésének**dátumának megtekintése.

### <a name="settings-page"></a>Beállítások lap
A Beállítások lapon a következő műveleteket végezheti:

* Az átjáró által használt **tanúsítvány** megtekintése, módosítása és exportálása. Ez a tanúsítvány az adatforrás hitelesítő adatainak titkosítására szolgál.
* Módosítsa a **végpont HTTPS-portját.** Az átjáró megnyit egy portot az adatforrás hitelesítő adatainak beállításához.
* **A** végpont állapota
* A View **SSL tanúsítvány** a portál és az átjáró közötti TLS/SSL-kommunikációhoz használatos az adatforrások hitelesítő adatainak beállításához.

### <a name="remote-access-from-intranet"></a>Távelérés az intranetről
Ez a funkció a jövőben engedélyezve lesz. A közelgő frissítések (v3.4 vagy újabb) lehetővé tesszük, hogy engedélyezze / tiltsa le a távoli kapcsolatot, amely ma a 8050-es port használatával történik (lásd a fenti szakaszt), miközben a PowerShell vagy a Credential Manager alkalmazást használja a hitelesítő adatok titkosításához.

### <a name="diagnostics-page"></a>Diagnosztika lap
A Diagnosztika lap lehetővé teszi a következő műveletek et:

* Engedélyezze a részletes **naplózást**, tekintse meg a naplókat az eseménynaplóban, és küldjön naplókat a Microsoftnak hiba esetén.
* **Tesztelje** a kapcsolatot egy adatforrással.

### <a name="help-page"></a>Súgóoldal
A súgólapon a következő információk jelennek meg:

* Az átjáró rövid leírása
* Verziószám
* Hivatkozások az online súgóra, az adatvédelmi nyilatkozatra és a licencszerződésre.

## <a name="monitor-gateway-in-the-portal"></a>Átjáró figyelése a portálon
Az Azure Portalon megtekintheti az erőforrás-kihasználtság (CPU, memória, hálózat(be/ki) közel valós idejű pillanatképét egy átjárógépen.

1. Az Azure Portalon keresse meg az adatgyár kezdőlapját, és kattintson a **Csatolt szolgáltatások** csempére.

    ![Data factory kezdőlap](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Jelölje ki az **átjárót** a **Csatolt szolgáltatások** lapon.

    ![Csatolt szolgáltatások lap](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Az **átjáró** lapon láthatja az átjáró memóriáját és processzorhasználatát.

    ![Az átjáró processzor- és memóriahasználata](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. **A Speciális beállítások** engedélyezése további részletek, például a hálózati használat megtekintéséhez.
    
    ![Az átjáró fejlett monitorozása](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Az alábbi táblázat az átjárócsomópontok listájának **oszlopait** tartalmazza:

Figyelési tulajdonság | Leírás
:------------------ | :----------
Név | Az átjáróhoz társított logikai átjáró és csomópontok neve. A csomópont egy helyszíni Windows-gép, amelyen az átjáró telepítve van. Ha egylogikaletű átjárón több csomópont (legfeljebb négy csomópont) található, az [Adatkezelési átjáró – magas rendelkezésre állás és méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md)című témakörben talál további információt.
status | A logikai átjáró és az átjárócsomópontok állapota. Példa: Online/Offline/Limited/etc. Ezekről az állapotokról az [Átjáró állapota](#gateway-status) című szakaszban talál további információt.
Verzió | A logikai átjáró és az egyes átjárócsomópontok verzióját jeleníti meg. A logikai átjáró verziója a csoport ban lévő csomópontok többségének verziója alapján kerül meghatározásra. Ha a logikai átjáró beállításában különböző verziójú csomópontok vannak, csak a logikai átjáróval azonos verziószámmal rendelkező csomópontok működnek megfelelően. Mások a korlátozott módban, és manuálisan kell frissíteni (csak abban az esetben, ha az automatikus frissítés sikertelen).
Igénybe vehető memória | Az átjárócsomóponton rendelkezésre álló memória. Ez az érték egy közel valós idejű pillanatkép.
Processzorhasználat | Egy átjárócsomópont processzorkihasználtsága. Ez az érték egy közel valós idejű pillanatkép.
Hálózatkezelés (be/ki) | Átjárócsomópont hálózati kihasználtsága. Ez az érték egy közel valós idejű pillanatkép.
Egyidejű feladatok (futó/korlát) | Az egyes csomópontokon futó feladatok vagy feladatok száma. Ez az érték egy közel valós idejű pillanatkép. A korlát az egyes csomópontok maximális egyidejű feladatait jelenti. Ez az érték a gép mérete alapján van meghatározva. Növelheti a korlátot az egyidejű feladat-végrehajtás ütemezéséhez speciális forgatókönyvekben, ahol a CPU/memória/hálózat kihasználatlan, de a tevékenységek időtúllépés nélkül jelennek meg. Ez a funkció egycsomópontos átjáróval is elérhető (még akkor is, ha a méretezhetőség i és rendelkezésre állási szolgáltatás nincs engedélyezve).
Szerepkör | A többcsomópontos átjáróban kétféle szerepkör található : a diszpécser és a feldolgozó. Minden csomópont dolgozó, ami azt jelenti, hogy mind használható feladatok végrehajtásához. Csak egy diszpécser csomópont van, amely feladatok/feladatok lekérése a felhőszolgáltatásokból, és különböző munkavégző csomópontokba (beleértve magát is) történő elküldésére szolgál.

Ezen a lapon olyan beállítások jelennek meg, amelyek több értelmet nyernek, ha két vagy több csomópont van (horizontális felskálázási forgatókönyv) az átjáróban. A többcsomópontos átjáró beállításával kapcsolatos részletekért lásd: [Adatkezelési átjáró – magas rendelkezésre állás és méretezhetőség.](data-factory-data-management-gateway-high-availability-scalability.md)

### <a name="gateway-status"></a>Átjáró állapota
Az alábbi táblázat egy **átjárócsomópont**lehetséges állapotát tartalmazza:

status  | Megjegyzések/forgatókönyvek
:------- | :------------------
Online | A Data Factory szolgáltatáshoz csatlakoztatott csomópont.
Offline | A csomópont offline állapotban van.
Frissítés | A csomópont automatikus frissítése folyamatban van.
Korlátozott | A kapcsolódási probléma miatt. A HTTP-port 8050-es problémája, a szolgáltatásbusz-kapcsolat problémája vagy a hitelesítő adatok szinkronizálása miatt fordulhat elő.
Inaktív | A csomópont más többségi csomópontok konfigurációjától eltérő konfigurációban van.<br/><br/> Egy csomópont inaktív lehet, ha nem tud csatlakozni más csomópontokhoz.

Az alábbi táblázat a **logikai átjáró**lehetséges állapotát tartalmazza. Az átjáró állapota az átjárócsomópontok állapotától függ.

status | Megjegyzések
:----- | :-------
Regisztrációt igényel | Ehhez a logikai átjáróhoz még nincs csomópont regisztrálva.
Online | Az átjárócsomópontok online állapotban vannak
Offline | Nincs csomópont online állapotban.
Korlátozott | Az átjáró nem minden csomópontja kifogástalan állapotban van. Ez az állapot egy figyelmeztetés, hogy néhány csomópont lehet, hogy nem! <br/><br/>Lehet, hogy a hitelesítő adatok szinkronizálási problémája a diszpécser/feldolgozó csomóponton.

## <a name="scale-up-gateway"></a>Felskálázási átjáró
Konfigurálhatja a csomóponton futtatható **egyidejű adatmozgatási feladatok** számát, hogy a helyszíni és a felhőbeli adattárak között az adatok áthelyezésének kapacitása felskálázható legyen.

Ha a rendelkezésre álló memória és a PROCESSZOR nem jól használja, de az alapjárati kapacitás 0, a csomóponton futtatható egyidejű feladatok számának növelésével kell felskáláznia. Előfordulhat, hogy a tevékenységek időtúllépés esetén is felskálázni szeretné, mert az átjáró túlterhelt. Az átjárócsomópont speciális beállításaiban növelheti a csomópont maximális kapacitását.

## <a name="troubleshooting-gateway-issues"></a>Átjáróproblémák elhárítása
Az adatkezelési átjáró használatával kapcsolatos problémák elhárításáról az [átjáró valkapcsolatos](data-factory-troubleshoot-gateway-issues.md) problémák elhárításáról szóló cikkben olvashat.

## <a name="move-gateway-from-one-machine-to-another"></a>Átjáró áthelyezése egyik gépről a másikra
Ez a szakasz az átjáróügyfél egyik gépről a másikra történő áthelyezésének lépéseit ismerteti.

1. A portálon keresse meg a **Data Factory kezdőlapját,** és kattintson a **Csatolt szolgáltatások** csempére.

    ![Adatátjárók hivatkozása](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Válassza ki az átjárót a **Csatolt szolgáltatások** lap **DATA GATEWAYS** szakaszában.

    ![Csatolt szolgáltatások lap kijelölt átjáróval](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Az **Adatátjáró** lapon kattintson az **Adatátjáró letöltése és telepítése gombra.**

    ![Átjáró letöltési hivatkozása](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. A **Konfigurálás** lapon kattintson az **Adatátjáró letöltése és telepítése gombra,** és kövesse az utasításokat az adatátjáró telepítéséhez a számítógépen.

    ![Lap konfigurálása](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Tartsa nyitva a **Microsoft Data Management Gateway Configuration Manager-t.**

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. A portál **Konfigurálás** lapján kattintson a parancssáv **On Újra létrehozása gombra,** majd a figyelmeztető üzenethez az **Igen** gombra. Kattintson a vágólapra a kulcsot másoló kulcsszöveg melletti **Másolás gombra.** A régi gép átjárója leáll, amint újra létrehozza a kulcsot.

    ![Kulcs újbóli létrehozása](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Illessze be a **kulcsot** a gép **Adatkezelési átjáró konfigurációkezelőjének** **Átjáró regisztrálása** lapján lévő szövegmezőbe. (nem kötelező) A kulcsszöveg megjelenítéséhez kattintson az **Átjárókulcs megjelenítése** jelölőnégyzetre.

    ![Kulcs másolása és regisztrálása](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kattintson a **Regisztráció** gombra az átjáró regisztrálásához a felhőszolgáltatással.
9. A **Beállítások** lapon kattintson a **Módosítás** gombra a régi átjáróval használt tanúsítvány kiválasztásához, írja be a **jelszót**, majd kattintson a **Befejezés gombra.**

   ![Tanúsítvány megadása](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   A régi átjáróból a következő lépésekkel exportálhat tanúsítványt: indítsa el az Adatkezelési átjáró konfigurációkezelőjét a régi számítógépen, váltson át a **Tanúsítvány** fülre, kattintson az **Exportálás** gombra, és kövesse az utasításokat.
10. Az átjáró sikeres regisztrációja után **az** átjáró konfigurációkezelőjének kezdőlapján a **Regisztrált** és **az Állapot** beállítás **elindult** beállításra van állítva.

## <a name="encrypting-credentials"></a>Hitelesítő adatok titkosítása
Ha a Data Factory Editor programban szeretné titkosítani a hitelesítő adatokat, tegye a következő lépéseket:

1. Indítsa el a webböngészőt az **átjárógépen,** keresse meg az [Azure Portal](https://portal.azure.com)t. Szükség esetén keresse meg az adatgyárat, nyissa meg az adatgyárat a **DATA FACTORY** lapon, majd kattintson a **Szerző & üzembe helyezés parancsra** a Data Factory Editor elindításához.
2. Kattintson egy meglévő **csatolt szolgáltatásra** a fanézetben a JSON-definíció megtekintéséhez, vagy hozzon létre egy adatkezelési átjárót igénylő csatolt szolgáltatást (például SQL Server vagy Oracle).
3. A JSON-szerkesztőgatewayName **gatewayName** tulajdonságában adja meg az átjáró nevét.
4. Adja meg az **adatforrás** tulajdonság kiszolgálónevét a **connectionString karakterláncában.**
5. Adja meg a **initial katalógus** tulajdonság ának adatbázisnevét a **connectionString fájlban.**
6. Kattintson a **Titkosítás** gombra a hitelesítő adatok **kezelője** alkalmazást elindító parancssávon. A Hitelesítő **adatok beállítása** párbeszédpanelnek meg kell jelennie.

    ![Hitelesítő adatok beállítása párbeszédpanel](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. A **Hitelesítő adatok beállítása** párbeszédpanelen tegye a következő lépéseket:
   1. Válassza ki azt a **hitelesítést,** amelyet a Data Factory szolgáltatásnak az adatbázishoz való csatlakozáshoz használnia kell.
   2. Adja meg annak a felhasználónak a nevét, akinek hozzáférése van az adatbázishoz a **USERNAME** beállításhoz.
   3. Adja meg a felhasználó jelszavát a **PASSWORD** beállításhoz.
   4. A hitelesítő adatok titkosításához és a párbeszédpanel bezárásához kattintson az **OK** gombra.
8. Most egy **titkosítottCredential** tulajdonságnak kell lennie a **connectionString** karakterláncában.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   Ha a portált az átjárógéptől eltérő gépről éri el, győződjön meg arról, hogy a Hitelesítő adatok kezelője alkalmazás csatlakozhat az átjárógéphez. Ha az alkalmazás nem tudja elérni az átjárógépet, nem teszi lehetővé az adatforrás hitelesítő adatainak beállítását és az adatforráshoz való csatlakozás tesztelését.

A Hitelesítő **adatok beállítása** alkalmazás használatakor a portál titkosítja a hitelesítő adatokat az **átjárókonfiguráció-kezelő** **tanúsítványlapján** megadott tanúsítvánnyal.

Ha a hitelesítő adatok titkosításához API-alapú megközelítést keres, a [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell-parancsmag használatával titkosíthatja a hitelesítő adatokat. A parancsmag azt a tanúsítványt használja, amelyet az átjáró a hitelesítő adatok titkosítására konfigurált. A JSON-ban a **connectionString** **titkosított hitelesítő eleméhez** titkosított hitelesítő adatokat ad hozzá. A JSON-t a [New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) parancsmaggal vagy a Data Factory Editor programban használhatja.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Van még egy módszer a hitelesítő adatok beállításához a Data Factory Editor használatával. Ha a szerkesztő segítségével hoz létre egy SQL Server-csatolt szolgáltatást, és a hitelesítő adatokat egyszerű szövegként adja meg, a hitelesítő adatok a Data Factory szolgáltatás tulajdonában lévő tanúsítvány használatával titkosítva lesznek. NEM használja azt a tanúsítványt, amelyet az átjáró használatára konfigurált. Bár ez a megközelítés lehet, hogy egy kicsit gyorsabb bizonyos esetekben, ez kevésbé biztonságos. Ezért azt javasoljuk, hogy ezt a megközelítést csak fejlesztési/tesztelési célokra kövesse.

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok
Ez a szakasz ismerteti, hogyan hozhat létre és regisztrálhat átjárót az Azure PowerShell-parancsmagok használatával.

1. Indítsa el az **Azure PowerShellt** rendszergazdai módban.
2. Jelentkezzen be az Azure-fiókjába a következő parancs futtatásával és az Azure-hitelesítő adatok megadásával.

    ```powershell
    Connect-AzAccount
    ```
3. A **New-AzDataFactoryGateway** parancsmag használatával logikai átjárót hozhat létre az alábbiak szerint:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Példa parancs és kimenet**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. Az Azure PowerShellben váltson a következő mappára: *C:\\\\Program Files\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\*. Futtassa a helyi **$Key** társított *RegisterGateway.ps1 parancsot* a következő parancsban látható módon. Ez a parancsfájl regisztrálja a számítógépre telepített ügyfélügynököt a korábban létrehozott logikai átjáróval.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Az átjárót távoli gépen regisztrálhatja az IsRegisterOnRemoteMachine paraméter használatával. Példa:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. A **Get-AzDataFactoryGateway** parancsmag segítségével lehívhatja az adat-előállítóban lévő átjárók listáját. Amikor az **állapot** **megjelenik az interneten,** az azt jelenti, hogy az átjáró készen áll a használatra.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Az átjárót eltávolíthatja az **Remove-AzDataFactoryGateway** parancsmag használatával, és a **Set-AzDataFactoryGateway** parancsmagok használatával frissítheti az átjáró leírását. A parancsmagok szintaxisát és egyéb részleteit lásd: Data Factory Parancsmag – referencia.  

### <a name="list-gateways-using-powershell"></a>Átjárók listázása a PowerShell használatával

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Átjáró eltávolítása a PowerShell használatával

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>További lépések
* Lásd: [Adatok áthelyezése a helyszíni és a felhőbeli adattárolók között](data-factory-move-data-between-onprem-and-cloud.md) cikk. A forgatókönyvben hozzon létre egy folyamatot, amely az átjáró segítségével átaz adatokat egy helyszíni SQL Server-adatbázis egy Azure blob.

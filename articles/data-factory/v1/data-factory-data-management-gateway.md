---
title: Az adatkezelési átjáró az adat-előállító |} Microsoft Docs
description: Állítsa be a data gateway áthelyezni az adatokat a helyszíni és a felhő között. Az Azure Data Factoryben az adatkezelési átjáró segítségével az adatok áthelyezése.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 9ed679c555d9bb363ffb4d896e791dcbd1b90f8e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="data-management-gateway"></a>Adatkezelési átjáró
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [önállóan üzemel a 2-es integrációs futásidejű](../create-self-hosted-integration-runtime.md). 

> [!NOTE]
> Az adatkezelési átjáró Self-hosted integrációs futásidejű, most már rebranded.  

Az adatkezelési átjáró egy olyan ügyfélügynök, telepítenie kell a helyszíni környezetben másolása közötti felhő- és a helyszíni adattárolókhoz. A Data Factory által támogatott tárolók jelennek meg a helyszíni adatokhoz a [támogatott adatforrások](data-factory-data-movement-activities.md#supported-data-stores-and-formats) szakasz.

Ez a cikk kiegészíti a forgatókönyv a [adatok áthelyezése között a helyszíni és felhőalapú adattároló](data-factory-move-data-between-onprem-and-cloud.md) cikk. A forgatókönyv hozzon létre egy folyamatot, amely egy helyi SQL Server-adatbázis adatok áthelyezése az Azure blob az átjárót használja. Ez a cikk az adatkezelési átjáró részletes részletes információkat nyújt. 

Az adatkezelési átjáró kiterjesztése több helyszíni gépet társít az átjárót. Méretezheti növelésével csomóponton egyidejűleg futtatható az adatátviteli feladatok száma legfeljebb. Ez a szolgáltatás egy logikai átjárójának egyetlen csomópont is érhető el. Lásd: [méretezés az adatkezelési átjáró az Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) cikkben alább.

> [!NOTE]
> Átjáró jelenleg csak a másolási tevékenység és a tárolt eljárási tevékenység adat-előállítóban. Nincs lehetőség egyéni tevékenység az átjárót a helyszíni adatforrások eléréséhez használható.      

## <a name="overview"></a>Áttekintés
### <a name="capabilities-of-data-management-gateway"></a>Az adatkezelési átjáró képességei
Az adatkezelési átjáró a következő lehetőségeket biztosítja:

* Modellt a helyszíni adatforrások és a felhő adatforrások a data factory és az áthelyezési-adatok között.
* Figyelési és-kezelés az átjáró állapota az adat-előállító oldalról láthatósága üveg egytáblás rendelkezik.
* Biztonságosan kezelheti a helyszíni adatforrások eléréséhez.
  * Nincs változás vállalati tűzfal szükséges. Átjáró csak hoz kimenő HTTP-alapú kapcsolatok internet megnyitásához.
  * A helyszíni adattárolókhoz a tanúsítványhoz tartozó hitelesítő adatok titkosításához.
* Hatékony áthelyezni az adatokat – adatátvitel párhuzamosan lehetséges legyen időszakos hálózati problémák automatikus újrapróbálkozási logika.

### <a name="command-flow-and-data-flow"></a>Parancs folyamata és adatfolyama
Ha a másolási tevékenység használatával másolja az adatokat a helyszíni és a felhő között, a tevékenység átjáró használatával adatok átviteléhez a helyszíni adatforrás felhő-és fordítva.

Ez a magas szintű adatok áramlását és adatátjáró Sablonhivatkozás lépései összefoglalása: ![átjáró adatfolyama](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Adatok fejlesztői egy Azure Data Factory használatával hoz létre egy átjáró a [Azure-portálon](https://portal.azure.com) vagy [PowerShell-parancsmag](https://msdn.microsoft.com/library/dn820234.aspx).
2. Adatok fejlesztő egy helyszíni adattároló összekapcsolt szolgáltatás létrehoz az átjáró megadásával. A társított szolgáltatás beállítása részeként adatok fejlesztő a hitelesítő adatok beállítása a alkalmazás hitelesítési típusok és a hitelesítő adatok megadásához.  Az alkalmazás hitelesítő adatok beállítása párbeszédpanel kommunikál az adattár kapcsolat és az átjáró hitelesítő adatok mentését teszteléséhez.
3. Átjáró (adatok fejlesztő megadva), az átjáróhoz társított tanúsítvány a hitelesítő adatok a felhőben a hitelesítő adatok mentése előtt titkosítja.
4. Az átjáró ütemezés & a felügyeleti feladatok egy közös Azure service bus-üzenetsort használó vezérlő csatornán keresztül kommunikál a Data Factory szolgáltatásnak. A másolási tevékenység feladat kell lennie kezdődött el, ha a Data Factory várólisták a kérelem és a hitelesítő adatokat. A feldolgozás után a várólista lekérdezési átjáró másolattól.
5. Az átjáró visszafejti a hitelesítő adatok ugyanazzal a tanúsítvánnyal, és ezután csatlakozik a helyi tárolót megfelelő hitelesítési típus és a hitelesítő adatokat.
6. Az átjáró a felhőalapú tárolást, vagy fordítva attól függően, hogy hogyan lett konfigurálva a másolási tevékenység során az adatok csővezeték másolja ki egy a helyszíni adatokat. Ebben a lépésben az átjáró közvetlenül kommunikál a felhőalapú tárolási szolgáltatások például az Azure Blob Storage egy biztonságos csatornán (HTTPS).

### <a name="considerations-for-using-gateway"></a>Átjáró használatának szempontjai
* Az adatkezelési átjáró egyetlen példányán több helyszíni adatforrások használható. Azonban **átjáró egyetlen példányán kötődik csak egy Azure data factory** és nem lehet megosztani az egy másik data factoryvel.
* Akkor is **csak egy példányát az adatkezelési átjáró** egy gépen telepítve. Tegyük fel, amely a helyszíni adatforrások eléréséhez szükséges két adat-előállítók rendelkezik, telepítendő átjáró két helyi számítógépen. Más szóval egy átjáró egy adott adat-előállító kötődik
* A **átjáró nem kell ugyanazon a számítógépen, az adatforrással**. Azonban az adatforráshoz való közelebb átjáró csökkenti a az átjáró által az adatforráshoz történő kapcsolódás idő. Azt javasoljuk, hogy az átjáró telepíthető olyan számítógépen, amelyen eltér a, amelyen a helyszíni adatforráshoz. Ha az átjáró és az adatforrás a különböző gépeken vannak, az átjáró nem "versenyeznek" az adatforrás erőforrásokhoz.
* Akkor is **több átjáró ugyanazon a helyszíni adatforráshoz való kapcsolódás különböző gépeken**. Például előfordulhat, hogy két átjáró kiszolgáló két adat-előállítók, de egy helyszíni adatforrás mindkét az adat-előállítók van regisztrálva.
* Ha már van egy átjáró telepíthető a számítógép szolgál egy **Power BI** esetben telepítése egy **az Azure Data Factory külön átjáró** egy másik számítógépen.
* Átjáró kell használni, még akkor is, ha használ **ExpressRoute**.
* Az adatforrás tekinti egy helyszíni adatforrás (tűzfal mögött van) is használatos **ExpressRoute**. Az átjáró használatához a szolgáltatás és az adatforrás közötti kapcsolat létrehozásához.
* Meg kell **az átjáró használatához** akkor is, ha az adattár a felhőben lévő egy **Azure IaaS virtuális**.

## <a name="installation"></a>Telepítés
### <a name="prerequisites"></a>Előfeltételek
* A támogatott **operációs rendszer** azok Windows 7, Windows 8 vagy 8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 verziók. Az adatkezelési átjáró egy olyan tartományvezérlő telepítése jelenleg nem támogatott.
* .NET-keretrendszer 4.5.1-es vagy újabb verzió szükséges. Ha átjáró a Windows 7 számítógépen telepíti, telepítse a .NET-keretrendszer 4.5-ös vagy újabb. Lásd: [.NET-keretrendszer rendszerkövetelmények](https://msdn.microsoft.com/library/8z6watww.aspx) részleteiről.
* Az ajánlott **konfigurációs** az átjáró gépen legalább 2 GHz, 4 mag, 8 GB RAM és 80 GB-os lemezre.
* A gazdaszámítógépen szeretnénk, ha az átjáró nem válaszol a kérelmek. Ezért, konfigurálja a megfelelő **energiaséma** az átjáró telepítése előtt a számítógépen. Ha a számítógép hibernált állapotba van konfigurálva, az átjáró telepítésének megadását kéri az üzenetet.
* A számítógépen történő telepítése és konfigurálása sikeresen megtörtént az adatkezelési átjáró rendszergazdának kell lennie. További felhasználók számára is hozzáadhat a **az adatkezelési átjáró felhasználók** helyi Windows-csoport. Az a csoport tagjai használhatják a **az adatkezelési átjáró konfigurációkezelőjének** eszköz az átjáró konfigurálásához.

Az adott gyakoriságát a másolási tevékenység fut fordulhat elő, mert az az erőforrás-használat (Processzor, memória) a számítógépen is csúcs és üresjárati idő azonos mintát követi. Erőforrás-használat is függ, erősen áthelyezett adatok mennyiségét. Ha több másolási feladat van folyamatban, megjelenik az Erőforrás kihasználtsága csúcsidőben feljebb.

### <a name="installation-options"></a>Telepítési beállítások
Az adatkezelési átjáró a következő módokon telepíthető:

* Az MSI telepítő csomag letöltésével a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).  Az MSI összes beállítás megőrzi a meglévő adatkezelési átjárót a legújabb verzióra frissítés is használható.
* Kattintva **töltse le és telepítse az adatátjáró** manuális telepítés alatt vagy **telepíthető közvetlenül a számítógépen** EXPRESSZ telepítés alatt. Lásd: [helyezze át az adatokat a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk lépéseit a gyors telepítés használatával. A manuális lépés megnyitná a letöltőközpontból.  Letölti és telepíti az átjárót a letöltőközpontból vonatkozó utasításokat a következő szakaszban találhatók.

### <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez:
1. Energiaséma konfigurálja, a gazdagépen az átjáró, hogy a gép hibernálásra nem. A gazdaszámítógépen szeretnénk, ha az átjáró nem válaszol a kérelmek.
2. Készítsen biztonsági másolatot az átjáróhoz társított tanúsítvány.

### <a name="install-the-gateway-from-download-center"></a>Telepítse az átjárót a letöltőközpontból
1. Navigáljon a [Microsoft adatkezelési átjáró letöltési oldala](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kattintson a **letöltése**, válassza ki a megfelelő verzióját (**32 bites** vs. **64 bites**), és kattintson a **következő**.
3. Futtassa a **MSI** közvetlenül, vagy mentse azt a merevlemezt és a Futtatás.
4. Az a **üdvözlő** lapon jelölje be a **nyelvi** kattintson **tovább**.
5. **Fogadja el** a végfelhasználói licencszerződést, majd kattintson **következő**.
6. Válassza ki **mappa** telepítse az átjárót, kattintson **következő**.
7. Az a **készen állnak a telepítésre** kattintson **telepítése**.
8. Kattintson a **Befejezés** telepítésének befejezéséhez.
9. A kulcs lekérése az Azure portálról. Tekintse meg a következő szakasz lépéseit.
10. Az a **Register átjáró** oldalán **az adatkezelési átjáró konfigurációkezelőjének** fut a gépen, tegye a következőket:
    1. A kulcs illessze be a szöveget.
    2. Ha úgy gondolja, a **megjelenítése átjárókulcs** a fő szöveg.
    3. Kattintson a **regisztrálása**.

### <a name="register-gateway-using-key"></a>Regisztrálja az átjárót kulcsával.
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Ha még nem hozott létre egy logikai átjárót a portál
Átjáró létrehozása a portálon, és a kulcsot a **konfigurálása** lapon, a forgatókönyv a következő lépéseket kell a [helyezze át az adatokat a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Ha már hozott létre a logikai átjáró a portálon
1. Azure-portálon lépjen a **adat-előállító** lapon, majd kattintson **összekapcsolt szolgáltatások** csempére.

    ![Data Factory lap](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Az a **összekapcsolt szolgáltatások** lapon, válassza ki a logikai **átjáró** a portálon létrehozott.

    ![logikai átjáró](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. Az a **Data Gateway** kattintson **töltse le és telepítse az adatátjáró**.

    ![Töltse le a hivatkozás a portálon](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. Az a **konfigurálása** kattintson **hozza létre újra kulcs**. Kattintson az Igen gombra a figyelmeztető üzenet, gondosan elolvasása után.

    ![Kulcs újbóli létrehozása](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Kattintson a Másolás gombra a kulcs mellett. A kulcs a vágólapra másolódik.

    ![Kulcs másolása](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Rendszer tálcaikonok / értesítések
A következő kép bemutatja az Tálca ikonok, amelyek akkor jelennek meg.

![rendszer Tálcaikonok igazítása](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Ha kurzor átvitele a rendszer tálcai ikon/értesítési üzenetet, lásd: az átjáró/frissítés műveletet egy felugró ablakban állapotának adatait.

### <a name="ports-and-firewall"></a>Portok és a tűzfalon
Nincsenek a két tűzfal figyelembe kell vennie: **vállalati tűzfal** a szervezet központi útválasztó futó és **Windows tűzfal** démon a helyi számítógépen, amelyen az átjáró van konfigurálva telepítve.  

![tűzfalak](./media/data-factory-data-management-gateway/firewalls2.png)

Vállalati tűzfal szinten a következő tartományokkal és a kimenő portok kell konfigurálni:

| Tartománynevek | Portok | Leírás |
| --- | --- | --- |
| *.servicebus.windows.net |443, 80 |Az adatátviteli szolgáltatás háttér-kommunikációhoz használt |
| *.core.windows.net |443 |Használt előkészített másolása Azure Blob használatával (Ha be van állítva)|
| *.frontend.clouddatahub.net |443 |Az adatátviteli szolgáltatás háttér-kommunikációhoz használt |
| *.servicebus.windows.net |9350-9354, 5671 |Nem kötelező service bus-továbbító a varázsló által használt TCP-n keresztül |


A Windows tűzfal szinten a kimenő portok általában engedélyezve vannak. Ha nem, konfigurálhatja a tartományok és portok ennek megfelelően az átjárót működtető gépen.

> [!NOTE]
> 1. A forrás alapján / felül, előfordulhat, hogy az engedélyezési lista további tartományokkal és a kimenő portok a vállalati és a Windows tűzfalon.
> 2. Az egyes felhőalapú adatbázisok (például: [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access)stb), szükség lehet az engedélyezett IP-címét a tűzfal konfigurációját az átjárót működtető gépen.
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>A forrás-tárolóban egy fogadó adattárolóhoz adatok másolása
Győződjön meg arról, hogy a tűzfalszabályok megfelelően engedélyezve vannak a vállalati tűzfalon, a Windows tűzfal az átjáró számítógépén, és az adatok tárolásához magát. Ezek a szabályok lehetővé teszi, hogy az átjáró mindkét adatforráshoz kapcsolódnak, és sikeresen gyűjtése. Minden egyes adattároló, amely részt vesz a másolási művelet szabályok engedélyezése.

Például a Másolás **egy helyszíni adattároló egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó**, hajtsa végre a következő lépéseket:

* Kimenő forgalom engedélyezése **TCP** kommunikációs port **1433** a Windows tűzfal és a vállalati tűzfalon.
* A tűzfal beállításainak Azure SQL-kiszolgáló hozzáadása az átjáró számítógépe IP-címét az engedélyezett IP-címek listájához.

> [!NOTE]
> Ha a tűzfal nem engedélyezi a 1433-as kimenő port, átjáró közvetlenül Azure SQL nem tud hozzáférni. Ebben az esetben használhatja [előkészített másolási](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) az SQL Azure Database-/ SQL Azure DW. Ebben a forgatókönyvben csak az adatátvitelt jelölik a lenne szükséges HTTPS (443-as port).
>
>


### <a name="proxy-server-considerations"></a>Proxy server szempontjai
Ha a vállalati hálózati környezet az internet eléréséhez proxykiszolgáló használ, konfigurálja az adatkezelési átjáró használatához megfelelő proxybeállításokat. A proxy állíthatja be a kezdeti regisztráció fázis során.

![Set proxy regisztráció során](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Átjáró a proxykiszolgálót segítségével csatlakozik a felhőszolgáltatáshoz. Kattintson a **módosítás** hivatkozás kezdeti beállítás során. Megjelenik a **proxybeállítást** párbeszédpanel.

![A Konfigurációkezelő használatával állítsa proxy](media/data-factory-data-management-gateway/SetProxySettings.png)

Három konfigurációs lehetőség áll rendelkezésre:

* **Ne használjon proxyt**: átjáró nincs explicit módon bármelyik proxyt használhatják felhőszolgáltatások való kapcsolódáshoz.
* **Használja a rendszer proxy**: átjáró használja a beállítást a konfigurált diahost.exe.config és diawp.exe.config proxy.  Ha nincs olyan proxy diahost.exe.config és diawp.exe.config van konfigurálva, átjáró csatlakozik a felhőalapú szolgáltatás közvetlenül a proxy áthaladás nélkül.
* **Egyéni proxyt használ**: beállítás használata az átjáró diahost.exe.config és diawp.exe.config konfigurációk használata helyett a HTTP-proxy konfigurálása.  Cím és Port is szükséges.  Felhasználónév és jelszó megadása nem kötelező, attól függően, hogy a proxy hitelesítési beállítást.  Minden beállítás az átjáró hitelesítő tanúsítványa titkosítva, és helyben tárolja, az átjáró a gazdagépen.

Az adatkezelési átjáró gazdaszolgáltatás a frissített proxybeállítások mentése után automatikusan újraindul.

Miután átjáró sikeresen regisztrálva van, ha azt szeretné, megtekintéséhez, vagy frissíteni a proxykiszolgáló beállításait, használja az adatkezelési átjáró konfigurációkezelőjének.

1. Indítsa el **az adatkezelési átjáró konfigurációkezelőjének**.
2. Váltson a **Settings** (Beállítások) lapra.
3. Kattintson a **módosítás** hivatkozásra **HTTP-Proxy** elindíthatja a szakasz a **HTTP-Proxy beállítása** párbeszédpanel.  
4. Miután rákattintott a **következő** gomb, megjelenik egy figyelmeztető párbeszédpanel, mentse a proxybeállítást, és indítsa újra az átjáró Gazdaszolgáltatást az engedélyt kér.

Megtekintheti és HTTP-proxy frissítse a Configuration Manager eszközzel.

![A Konfigurációkezelő használatával állítsa proxy](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Ha korábban beállított proxykiszolgálót NTLM-hitelesítéssel, átjáró Gazdaszolgáltatást a tartományi fiók alatt fut. Ha módosítja a jelszót később a tartományi fiók, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, és ennek megfelelően indítsa újra. Ez a követelmény miatt javasoljuk, hogy dedikált tartományi fiókot, amely nem szükséges a jelszó gyakran frissíteni a proxykiszolgáló eléréséhez használt.
>
>

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló-beállításainak konfigurálása
Ha **system proxy használata** átjáró használja a HTTP-proxy beállítása, a proxybeállítást diahost.exe.config és diawp.exe.config.  Ha nincs olyan proxy diahost.exe.config és diawp.exe.config van beállítva, átjáró csatlakozik a felhőalapú szolgáltatás közvetlenül a proxy áthaladás nélkül. Az alábbi eljárás ismerteti a diahost.exe.config fájl frissítése.  

1. A Fájlkezelőben másolat egy biztonságos C:\Program Files\Microsoft Data felügyeleti Gateway\2.0\Shared\diahost.exe.config biztonsági mentése az eredeti fájlt.
2. Indítsa el a Notepad.exe rendszergazdaként fut, és nyissa meg a szöveges fájl "C:\Program Files\Microsoft Data felügyeleti Gateway\2.0\Shared\diahost.exe.config. Az alapértelmezett címke található a System.NET névtérbeli az alábbi kódban látható módon:

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   Proxy kiszolgálóadatok majd adhat hozzá, az alábbi példában látható módon:

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   További tulajdonságokat adhatja meg a szükséges beállításokat, például a scriptLocation engedélyezettek a proxy címkén belül. Tekintse meg [proxy (hálózati beállítások) elem](https://msdn.microsoft.com/library/sa91de1e.aspx) a szintaxist.

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. Mentse a konfigurációs fájlt az eredeti helyre, majd indítsa újra a szolgáltatást az adatkezelési átjáró Gazdaszolgáltatáshoz, amely átveszi a módosításokat. A szolgáltatás újraindításához: használja a Vezérlőpultról, vagy a szolgáltatások kisalkalmazását a **az adatkezelési átjáró konfigurációkezelőjének** > kattintson a **szolgáltatás leállítása** gombra, majd kattintson a **indítása Szolgáltatás**. A szolgáltatás nem indul el, akkor valószínű, hogy egy érvénytelen XML-címke szintaxissal szerkesztették alkalmazás konfigurációs fájljába hozzá lett adva.

> [!IMPORTANT]
> Ne felejtse el frissíteni a **mindkét** diahost.exe.config és diawp.exe.config.  


A pontok mellett szükség ügyeljen arra, hogy a Microsoft Azure a vállalat engedélyezett. Érvényes Microsoft Azure IP-címek listájának tölthető le: a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>A tűzfal és proxy-kiszolgálóval kapcsolatos problémák lehetséges jelenségek
Ha hibákba ütközik a következő hasonló, valószínű a tűzfalhoz vagy proxyhoz kiszolgáló, amely blokkolja az átjárót, csatlakozzon a Data Factory hitelesítse magát a helytelen konfiguráció miatt. Tekintse meg a tűzfalat, hogy az előző szakaszban, és a proxykiszolgáló megfelelően vannak konfigurálva.

1. Amikor megpróbálja regisztrálni az átjárót, a következő hibaüzenetet kapja: "nem sikerült regisztrálni az átjáró kulcsa. Regisztrálja újra az átjáró kulcsát előtt győződjön meg arról, hogy az adatkezelési átjáró csatlakoztatott állapotban van, és az adatkezelési átjáró gazdaszolgáltatás elindult."
2. A Configuration Manager megnyitásakor állapota megjelenik "Kapcsolat" vagy "Csatlakozás". Amikor megtekintik a Windows eseménynaplóiban keresse meg, a "Eseménynapló" > "Alkalmazások és szolgáltatások Logs" > "Az adatkezelési átjáró" hibaüzenetek jelennek meg például a következő hibával: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Nyissa meg a portot 8050 a hitelesítő adatok titkosításához
A **hitelesítő adatok beállítása a** alkalmazás használja a bejövő portot **8050** továbbítási hitelesítő adatait az átjáróra, ha beállít egy helyszíni társított szolgáltatást az Azure portálon. Átjáró telepítésekor alapértelmezés szerint az átjáró sikeres telepítése megnyitja azt az átjárót működtető gépen.

Ha egy külső tűzfalat használ, a portot 8050 manuálisan is megnyithatja. Ha futtatja a tűzfallal kapcsolatos probléma átjáró telepítése során, próbálja meg a következő paranccsal telepítse az átjárót a tűzfal konfigurálása nélkül.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Ha nem kíván nyissa meg a portot 8050 az átjáró számítógépén, használja a használatától eltérő funkcióját a **hitelesítő adatok beállítása a** alkalmazás adatok adattárolóhoz használandó hitelesítő adatok konfigurálása. Használhat például [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell-parancsmagot. Lásd: [hitelesítő adatok beállítása és biztonsági](#set-credentials-and-securityy) állíthat be szakaszt, hogyan tárolja az adatokat a hitelesítő adatait.

## <a name="update"></a>Frissítés
Alapértelmezés szerint az adatkezelési átjáró automatikusan frissül, ha az átjáró egy újabb verziója érhető el. Az átjáró frissítése nem történik meg, amíg nem történik az ütemezett feladatokat. Nincsenek további feladatok dolgozza fel az átjáró a frissítési művelet befejeződéséig. Ha a frissítés sikertelen, átjáró a régi verziót vissza lesz állítva.

A következő helyen jelenik meg az ütemezett frissítés időpontja:

* Az átjáró tulajdonságlapján az Azure portálon.
* Az a adatkezelési átjáró konfigurációkezelőjének kezdőlap
* Rendszer tálca értesítési üzenetet.

A Kezdőlap lap, a adatkezelési átjáró konfigurációkezelőjének jeleníti meg a frissítési ütemezés és a legutóbbi az átjáró telepítése/frissítése.

![Frissítések ütemezése](media/data-factory-data-management-gateway/UpdateSection.png)

A frissítés azonnal, vagy várja meg az ütemezett időpontban automatikusan frissíteni kell az átjárót. Például a következő kép bemutatja, hogy az értesítési üzenet jelenik meg az átjáró Konfigurációkezelőjében együtt a frissítés gombra kattintva azonnal telepíteni.

![Frissítés a DMG Configuration Managerben](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Az értesítési üzenet tálcán lenne, az alábbi ábrán látható módon:

![Rendszer tálca üzenet](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Frissítési művelet (Manuális vagy automatikus) a tálcán állapotának megtekintése. Átjáró konfigurációkezelőjének elindításakor legközelebb megjelenik egy üzenet, hogy az átjáró frissítése sikerült hivatkozással együtt értesítési sávján [Mi az az új témakör](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Az automatikus frissítési funkció engedélyezése vagy tiltása
Akkor is tiltása/engedélyezése az automatikus frissítési szolgáltatás a következő lépések végrehajtásával:

[Az átjáró egyetlen csomópont]
1. Indítsa el a Windows Powershellt az átjárót működtető gépen.
2. Váltás a C:\Program Files\Microsoft Data felügyeleti Gateway\2.0\PowerShellScript mappába.
3. Futtassa a következő parancs futtatásával kapcsolja be az automatikus frissítés kikapcsolása (Letiltás) szolgáltatás.   

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off
    ```
4. Ez a állítsa vissza:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on  
    ```
[Több csomópontos magas rendelkezésre állású és méretezhető átjáró (előzetes verzió)](data-factory-data-management-gateway-high-availability-scalability.md)
1. Indítsa el a Windows Powershellt az átjárót működtető gépen.
2. Váltás a C:\Program Files\Microsoft Data felügyeleti Gateway\2.0\PowerShellScript mappába.
3. Futtassa a következő parancs futtatásával kapcsolja be az automatikus frissítés kikapcsolása (Letiltás) szolgáltatás.   

    Átjáró (előzetes verzió) magas rendelkezésre állás szolgáltatással egy extra AuthKey paraméter megadása kötelező.
    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. Ez a állítsa vissza:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 
    ```

## <a name="configuration-manager"></a>Configuration Manager
Miután telepíti az átjárót, indítja el az adatkezelési átjáró Konfigurációkezelőjét az alábbi módszerek valamelyikével:

1. Az a **keresési** ablakot, írja be **az adatkezelési átjáró** elérni ezt a segédprogramot.
2. A végrehajtható fájl futtatásához **ConfigManager.exe** a mappában: **C:\Program Files\Microsoft Data felügyeleti Gateway\2.0\Shared**

### <a name="home-page"></a>Kezdőlap
A kezdőlap lehetővé teszi a következő műveleteket hajthatja végre:

* Tekintse meg az átjáró (csatlakozik a felhőszolgáltatáshoz stb.) állapotát.
* **Regisztrálni** kulccsal a portálról.
* **Állítsa le** , és indítsa el a **az adatkezelési átjáró Gazdaszolgáltatáshoz szolgáltatás** az átjárót működtető gépen.
* **Frissítések ütemezése** a nap adott időpontban.
* A dátum, amikor az átjáró volt megtekintése **utolsó frissítés**.

### <a name="settings-page"></a>Beállítások lap
A beállítások lap lehetővé teszi a következő műveleteket hajthatja végre:

* Megtekintése, módosítása és exportálása **tanúsítvány** az átjáró által használt. Ez a tanúsítvány az adatforrás hitelesítő adatainak titkosítására szolgál.
* Változás **HTTPS-portja** a végpont. Az átjáró egy portot az adatforrás hitelesítő adatainak beállításához nyit meg.
* **Állapot** a végpont
* Nézet **SSL-tanúsítvány** portál és az átjáró közötti SSL-kommunikáció adatforrások hitelesítő adatok beállítására szolgál.  

### <a name="remote-access-from-intranet"></a>Távelérési az intranetes  
Ez a funkció a jövőben engedélyezve lesz. A jövőbeli frissítések (v3.4 vagy újabb) azt lehetővé teszi, hogy engedélyezése / letiltása bármely távoli kapcsolatot, amely ma történik (lásd fent) 8050 portot használ a PowerShell vagy a hitelesítőadat-kezelő alkalmazás használatakor a hitelesítő adatok titkosításához. 

### <a name="diagnostics-page"></a>Diagnosztika lap
A Diagnosztika lapot lehetővé teszi a következő műveleteket hajthatja végre:

* Részletes engedélyezése **naplózás**, naplók megtekintése az eseménynaplóban és naplókat küld a Microsoft, ha hiba történt.
* **A kapcsolat tesztelése** adatforráshoz.  

### <a name="help-page"></a>Súgó lap
A súgólap az alábbi információkat jeleníti meg:  

* Az átjáró rövid leírása
* Verziószám
* Online súgó, az adatvédelmi nyilatkozat és a licencszerződés mutató hivatkozásokat tartalmaz.  

## <a name="monitor-gateway-in-the-portal"></a>A figyelő átjáró a portálon
Az Azure-portálon megtekintheti közel valós idejű pillanatképe erőforrás-használat (Processzor, memória, network(in/out), stb.) egy átjáró gépen.  

1. Az Azure-portálon lépjen a data factory kezdőlapjának, és kattintson **összekapcsolt szolgáltatások** csempére. 

    ![Data factory kezdőlap](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Válassza ki a **átjáró** a a **összekapcsolt szolgáltatások** lap.

    ![Társított szolgáltatások lap](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Az a **átjáró** lapon megtekintheti a memória és CPU-használata az átjáró.

    ![Átjáró Processzor- és memóriahasználatról](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Engedélyezése **speciális beállítások** hálózathasználatot például további részletek megtekintéséhez.
    
    ![Speciális átjáró figyelése](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

A következő táblázat ismerteti az oszlopok a **Átjárócsomópontok** listája:  

Figyelési tulajdonság | Leírás
:------------------ | :---------- 
Name (Név) | A logikai átjáró és az átjáróhoz társított csomópont neve. Csomópont egy a helyi Windows-számítógépen, amelyen az átjáró telepítve van-e. Egynél több csomópont (legfeljebb négy csomópont), amely a egyetlen logikai átjáró információkért lásd: [az adatkezelési átjáró - magas rendelkezésre állás és méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md).    
status | A logikai átjáró és az átjáró csomópontok állapota. Példa: Online/Offline/korlátozott/stb. A fenti állapotok megjelenése kapcsolatos információkért lásd: [az átjáró állapotának](#gateway-status) szakasz. 
Verzió | A logikai átjáró és az egyes átjárócsomópont verzióját jeleníti meg. A logikai átjáró verziója határozza meg a csoportban lévő csomópontok többsége verzióján alapul. Ha nincs a logikai átjáró beállítás, csak a csomópontok a azonos verziószámú a logikai átjáró függvényében eltérő verziójú csomópontok megfelelően. Mások korlátozott módban van, és manuálisan kell frissíteni, (csak abban az esetben az automatikus frissítés sikertelen lesz). 
Rendelkezésre álló memória | Rendelkezésre álló memória egy átjáró-csomóponton. Ez az érték közel valós idejű pillanatképet. 
Processzorkihasználtság | Egy átjáró csomópont CPU-felhasználását. Ez az érték közel valós idejű pillanatképet. 
Hálózatkezelés (In/Out) | Hálózathasználat egy átjáró csomópont. Ez az érték közel valós idejű pillanatképet. 
Egyidejűleg futó feladatainak (futtató / Limit) | Feladatok vagy minden egyes csomóponton futó feladatok száma. Ez az érték közel valós idejű pillanatképet. Korlát azt jelzi, hogy az egyes csomópontok maximális egyidejűleg futó feladatainak. Ez az érték van megadva a mérete alapján. Egyidejű feladatok végrehajtásának speciális forgatókönyvekhez, ahol Processzor/memória/hálózati alatt szükség, de tevékenységek vannak időtúllépés miatt növelheti a korlát növelhető. Ez a funkció érhető el egy egy csomópontos átjáró (még akkor is, ha a méretezhetőség és a rendelkezésre állási funkció nincs engedélyezve).  
Szerepkör | A kézbesítő és feldolgozói szerepkörök az többcsomópontos átjáró – két típusa van. Az összes csomópontja a dolgozók, ami azt jelenti, hogy az összes felhasználásuk feladatok végrehajtásához. A kézbesítő csak egy csomópont, feladatok és feladatok a felhőalapú szolgáltatások lekéréses és mennyi őket (beleértve magát) különböző munkavégző csomópontokhoz használt van.

Ezen a lapon látható egyes beállítások, amelyek több értelme, ha az átjáró két vagy több csomópont (forgatókönyv kibővítési). Lásd: [az adatkezelési átjáró - magas rendelkezésre állás és méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md) többcsomópontos átjárók beállításával kapcsolatos részleteket.

### <a name="gateway-status"></a>Az átjáró állapotának
A következő táblázat a lehetséges állapotok egy **átjárócsomópont**: 

status  | Megjegyzések/forgatókönyvek
:------- | :------------------
Online | Csomópont csatlakozik a Data Factory szolgáltatásnak.
Offline | Csomópontja offline állapotban.
Frissítés | A csomópont automatikus frissítése folyamatban van.
Korlátozott | Kapcsolat nem látható probléma miatt. HTTP-port 8050 problémát, a service bus kapcsolati probléma vagy a hitelesítő adatok szinkronizálási problémája miatt lehet. 
Inaktív | Csomópont van konfigurálva a konfigurációból egyéb többsége csomópontok különböző.<br/><br/> A csomópont inaktív lehet, ha más csomópontok nem tud kapcsolódni. 


A következő táblázat a lehetséges állapotok egy **logikai átjáró**. Az átjáró állapotának átjáró csomópontnak állapotok függ. 

status | Megjegyzések
:----- | :-------
Needs Registration | Nincs csomópont még regisztrálva van a logikai átjáró
Online | Átjáró csomópontja online állapotban.
Offline | Nincs csomópontja online állapotát.
Korlátozott | Ez az átjáró nem minden csomópontja kifogástalan állapotban vannak. Ez az állapot nem figyelmezteti rá, hogy néhány csomópont esetleg nem működik! <br/><br/>Hitelesítő adatok szinkronizálási problémája kézbesítő/munkavégző csomóponton okozhatja. 

## <a name="scale-up-gateway"></a>Átjáró méretezése
Beállíthatja, hogy hány **egyidejű adatátviteli feladatok** egy csomópont növelheti az adatok a helyszínen és a felhő közötti áthelyezése képességének futó adattárolókhoz. 

Amikor a rendelkezésre álló memória és CPU nem használhatók is, de a tétlen kapacitás 0, akkor kell vertikális felskálázás csomópont futtatható egyidejűleg futó feladatainak számát. Érdemes azt is, méretezést kívánó, amikor a tevékenység időtúllépés miatt, mivel az átjáró túl van terhelve. Egy átjáró csomópont speciális beállításai között szereplő is növelheti a maximális csomópont. 
  

## <a name="troubleshooting-gateway-issues"></a>Átjáró problémák elhárítása
Lásd: [átjáró problémák elhárítása](data-factory-troubleshoot-gateway-issues.md) cikk információk/tippek az adatkezelési átjáró használatával kapcsolatos hibák elhárításához.  

## <a name="move-gateway-from-one-machine-to-another"></a>Átjáró áthelyezése egyik gépről egy másikra
Ez a szakasz lépéseit áthelyezése átjáró ügyfél egyik gépről egy másik gépen.

1. A portálon lépjen a **adat-előállító kezdőlap**, és kattintson a **összekapcsolt szolgáltatások** csempére.

    ![Átjárók adatkapcsolat](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Válassza ki az átjáró a **DATA GATEWAYS** szakasza a **összekapcsolt szolgáltatások** lap.

    ![A kiválasztott átjáró csatolt szolgáltatások lap](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Az a **adatátjáró** kattintson **töltse le és telepítse az adatátjáró**.

    ![Töltse le az átjáró-hivatkozás](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Az a **konfigurálása** kattintson **töltse le és telepítse az adatátjáró**, és kövesse az utasításokat az átjáró a számítógépre telepítéséhez.

    ![Lapjának konfigurálása](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Tartsa a **Microsoft adatkezelési átjáró konfigurációkezelőjének** megnyitásához.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. Az a **konfigurálása** oldalra a portálon, kattintson a **hozza létre újra kulcs** a parancssávon, majd kattintson a **Igen** a figyelmeztető üzenet. Kattintson a **Másolás gombra** melletti kulcs szövegét, hogy a kulcs a vágólapra másolja. Az átjáró a régi gépen leáll a működése, amint a kulcs újbóli létrehozása.  

    ![Kulcs újbóli létrehozása](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Beillesztés a **kulcs** a szövegmezőbe írja be a **átjáró regisztrálása** oldalán a **az adatkezelési átjáró konfigurációkezelőjének** a számítógépen. (választható) Kattintson a **megjelenítése átjárókulcs** melletti jelölőnégyzetet, hogy tekintse meg a kulcs szövegét.

    ![Kulcs másolása és regisztrálása](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kattintson a **regisztrálása** az átjáró regisztrálása a felhőalapú szolgáltatáshoz.
9. Az a **beállítások** lapra, majd **módosítása** válassza ki ugyanazt a tanúsítványt, amelyet a régi átjáróként használt, írja be a következőt a **jelszó**, és kattintson a **Befejezés**.

   ![Adja meg a tanúsítvány](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Exportálhatja a tanúsítványt a régi átjáró a következő lépések végrehajtásával: Indítsa el az adatkezelési átjáró konfigurációkezelőjének a régi gépen, váltson a **tanúsítvány** lapra, majd **exportálása** gombra, és kövesse az utasításokat.
10. Az átjáró sikeres regisztrálás után megjelenik a **regisztrációs** beállítása **regisztrált** és **állapot** beállítása **elindítva** a kezdőlapon az átjáró a Configuration Manager.

## <a name="encrypting-credentials"></a>Hitelesítő adatok titkosítása
A Data Factory Editor a hitelesítő adatok titkosításához, tegye a következőket:

1. A webböngésző indítása a **átjárót működtető gépen**, navigáljon a [Azure-portálon](http://portal.azure.com). Keresse meg a data factory, ha szükséges, nyissa meg az adat-előállítót a **adat-előállító** lapon, majd kattintson **Szerző & telepítés** elindíthatja a Data Factory Editor.   
2. Kattintson egy meglévő **társított szolgáltatás** a fanézetben, tekintse meg a JSON-definícióból, vagy hozzon létre az adatkezelési átjárót igénylő összekapcsolt szolgáltatás (például: SQL Server- vagy Oracle).
3. A JSON-szerkesztőben a a **gatewayName** tulajdonság, adja meg az átjáró nevét.
4. Adja meg a kiszolgáló nevét a **adatforrás** tulajdonságot a **connectionString**.
5. Adja meg az adatbázis nevét a **Initial Catalog** tulajdonságot a **connectionString**.    
6. Kattintson a **titkosítása** gombra a parancssávon kattintson a indító-után **hitelesítőadat-kezelő** alkalmazás. Megjelenik a **hitelesítő adatok beállítása a** párbeszédpanel megnyitásához.

    ![A beállítás hitelesítő adatok párbeszédpanel](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. Az a **hitelesítő adatok beállítása a** párbeszédpanelen tegye a következőket:
   1. Válassza ki **hitelesítési** , amelyet a Data Factory szolgáltatásnak az adatbázishoz való kapcsolódáshoz használandó.
   2. Adjon meg nevet a felhasználó, aki hozzáféréssel rendelkezik az adatbázishoz tartozó a **felhasználónév** beállítást.
   3. Írja be a jelszót a felhasználótól a **jelszó** beállítást.  
   4. Kattintson a **OK** hitelesítő adatok titkosításához, és zárja be a párbeszédpanelt.
8. Megjelenik egy **encryptedCredential** tulajdonságot a **connectionString** most.

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
Ha egy gép, amely eltér az átjáró számítógépe elérni a portált, meg kell győződnie arról, hogy a hitelesítő adatokat kezelő alkalmazások csatlakozhat az átjárót működtető gépen. Ha az alkalmazás nem érhető el az átjárót működtető gépen, akkor nem teszi lehetővé az adatforráshoz tartozó hitelesítő adatokat, és állítsa be az adatforráshoz való kapcsolat ellenőrzéséhez.  

Használatakor a **hitelesítő adatok beállítása a** alkalmazás, a portál titkosítja a hitelesítő adatokat a megadott tanúsítvány a **tanúsítvány** lapján a **átjáró konfigurációkezelőjének** az átjárót működtető gépen.

Ha a hitelesítő adatok titkosítására egy API-alapú módszert keres, használhatja a [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell-parancsmag hitelesítő adatok titkosításához. A parancsmag a tanúsítványt használja, hogy az átjáró a hitelesítő adatok titkosításához használatára van konfigurálva. Akkor adja hozzá a titkosított hitelesítő adatokat a **EncryptedCredential** eleme a **connectionString** a JSON-ban. A JSON-t használ a [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) parancsmag vagy a Data Factory Editor.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Nincs egy további módszer használatával a Data Factory Editor hitelesítő adatok beállítására. A szerkesztő használatával hoz létre a kapcsolódó SQL Server szolgáltatás, és adja meg hitelesítő adatait egyszerű szövegként, ha az azonosító adatok titkosítása a Data Factory szolgáltatásnak birtokló tanúsítványt használ. Az a tanúsítvány nem használható, hogy az átjáró használatára van konfigurálva. Lehet, hogy bizonyos esetekben kissé gyorsabb ezt a módszert használja, de napjainkban kevésbé biztonságos. Ezért azt javasoljuk, hogy hajtsa végre ezt a módszert csak fejlesztési/tesztelési célokra.

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok
Ez a szakasz ismerteti, hogyan hozhat létre, és regisztrálnia kell egy átjárót, Azure PowerShell-parancsmagok használatával.

1. Indítsa el **Azure PowerShell** rendszergazdai módban.
2. Jelentkezzen be a következő parancs futtatásával, majd írja be Azure hitelesítő adatait az Azure-fiókjával.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Használja a **New-AzureRmDataFactoryGateway** parancsmaggal hozzon létre egy logikai átjáró az alábbiak szerint:

    ```PowerShell
    $MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Példa parancs és a kimeneti**:

    ```
    PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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

1. Az Azure PowerShell, a mappa váltani: **C:\Program Files\Microsoft Data felügyeleti Gateway\2.0\PowerShellScript\**. Futtatás **RegisterGateway.ps1** a lokális változó társított **$Key** látható módon a következő parancsot. Ez a parancsfájl regisztrálja az ügyfél-ügynököt, korábban létrehozott logikai átjáró a számítógépre telepítve.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Az átjáró egy távoli számítógépen a IsRegisterOnRemoteMachine paraméter használatával tud regisztrálni. Példa:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Használhatja a **Get-AzureRmDataFactoryGateway** parancsmagot, hogy megkapja az átjárók listája az adat-előállítóban. Ha a **állapot** látható **online**, ez azt jelenti, az átjáró készen áll a használatra.

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Egy átjáró használatával is eltávolíthatja a **Remove-AzureRmDataFactoryGateway** egy átjáró használatára vonatkozó parancsmag és a frissítés leírása a **Set-AzureRmDataFactoryGateway** parancsmagok. A szintaxis és egyéb részletek ezekről a parancsmagokról tekintse meg a Data Factory parancsmag-referencia.  

### <a name="list-gateways-using-powershell"></a>PowerShell-lel átjáróit

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Távolítsa el a PowerShell használatával átjáró

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>További lépések
* Lásd: [adatok áthelyezése között a helyszíni és felhőalapú adattároló](data-factory-move-data-between-onprem-and-cloud.md) cikk. A forgatókönyv hozzon létre egy folyamatot, amely egy helyi SQL Server-adatbázis adatok áthelyezése az Azure blob az átjárót használja.  

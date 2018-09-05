---
title: Adatkezelési átjárót a Data Factory |} A Microsoft Docs
description: Állítsa be a data gateway adatok áthelyezése a helyszíni és a felhő között. Az Azure Data Factoryban az adatkezelési átjáró segítségével az adatok áthelyezése.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 988c264ef6052b4b41de493944ac8d39a197a083
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698757"
---
# <a name="data-management-gateway"></a>Adatkezelési átjáró
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [saját üzemeltetésű integrációs modul az](../create-self-hosted-integration-runtime.md). 

> [!NOTE]
> Az adatkezelési átjáró most lett nevezve, helyi Integration Runtime.  

Az adatkezelési átjáró egy ügyfélügynök, amely telepítenie kell másolni a helyszíni környezetben, felhőbeli és helyszíni adattárak közötti. A Data Factory által támogatott adattárak szerepel a helyszíni adatokhoz a [támogatott adatforrások](data-factory-data-movement-activities.md#supported-data-stores-and-formats) szakaszban.

Ebben a cikkben található útmutatások követéséhez egészíti ki a [között a helyszíni és felhőbeli adatok áthelyezése adattárak](data-factory-move-data-between-onprem-and-cloud.md) cikk. A forgatókönyv az adatok áthelyezése a helyszíni SQL Server-adatbázisból Azure-blobba az átjárót használó folyamatot hoz létre. Ez a cikk a data management gateway részletes részletes információkat nyújt. 

Adatkezelési átjáró méretezése több helyszíni gépek társítását az átjáró. Méretezhető beállítása növelje a csomóponton egyidejűleg futtatható adatok mozgását feladatok számát. Ez a funkció egy egyetlen csomóponttal logikai átjáró is érhető el. Lásd: [adatkezelési átjáró méretezés az Azure Data Factoryban](data-factory-data-management-gateway-high-availability-scalability.md) részleteivel.

> [!NOTE]
> Jelenleg átjáró támogatja a csak a másolási tevékenység és a tárolt eljárási tevékenység adat-előállítóban. Nem alkalmas az átjáró az egyéni tevékenységek használata a helyszíni adatforrások eléréséhez.      

## <a name="overview"></a>Áttekintés
### <a name="capabilities-of-data-management-gateway"></a>Az adatkezelési átjáró képességeit
Az adatkezelési átjáró az alábbi képességeket biztosítja:

* Modell a helyszíni adatforrások és a data factory és a move-adatok felhőalapú adatforrásai.
* A monitorozás és kezelés az átjáró állapota az adat-előállító lapon betekintést tekinthesse egyetlen ablaktábla rendelkezik.
* Biztonságosan kezelheti a helyszíni adatforrásokhoz való hozzáférés.
  * Nincs szükség a vállalati tűzfalon módosítására. Átjáró csak lehetővé teszi a HTTP-alapú kimenő kapcsolatokat, az internet megnyitásához.
  * A tanúsítvány a helyi adattárak hitelesítő adatok titkosításához.
* Adatok áthelyezése a hatékony – adatok továbbítása párhuzamosan, az automatikus rugalmas az időszakos hálózati problémák újrapróbálkozási logika.

### <a name="command-flow-and-data-flow"></a>Parancsot a folyamat és az adatfolyam
A másolási tevékenység használatával másolja az adatokat a helyszíni és a felhő között, amikor a tevékenység használja az átjáró át helyszíni adatforrásból származó adatok felhőbeli futtatását.

Íme az adatok magas szintű folyamata és az átjáróval másolására szolgáló lépések összefoglalása: ![adatfolyam-átjáró használatával](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Adatok fejlesztői átjárót hoz létre egy Azure Data Factory segítségével a [az Azure portal](https://portal.azure.com) vagy [PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/azurerm.datafactories/).
2. Adatok fejlesztői egy helyszíni adattár társított szolgáltatás létrehoz az átjáró megadásával. A társított szolgáltatás beállításának részeként az adatok fejlesztői hitelesítési típusok és a hitelesítő adatok megadásához hitelesítő adatok beállítása alkalmazást használja.  A hitelesítő adatok beállítása párbeszédpanel kommunikál az adattár tesztelheti a kapcsolatot és az átjáró a hitelesítő adatok mentéséhez.
3. Átjáró (adatok fejlesztő által megadott), az átjáró társított tanúsítvány a hitelesítő adatok a felhőben a hitelesítő adatok mentése előtt titkosítja.
4. A Data Factory szolgáltatás kommunikál az átjáró az ütemezés és a egy vezérlőcsatorna által használt megosztott Azure service bus-üzenetsor-feladatok kezelését. Egy másolási tevékenység feladat kell lennie kezdődjön, amikor a Data Factory együtt hitelesítő adatokat a kérelem várólistára helyezi. A feldolgozás után a várólista lekérdezési átjáró elindít.
5. Az átjáró visszafejti a hitelesítő adatokat, az ugyanazt a tanúsítványt, és ezután csatlakozik a helyi adattár megfelelő hitelesítési típust és a hitelesítő adatokat.
6. Az átjáró adatokat másol egy helyszíni adattár, a felhőalapú tárolást, vagy fordítva az adatok folyamatban, a másolási tevékenység konfigurációjától függően. Ehhez a lépéshez az átjáró közvetlenül kommunikál a felhőalapú tárolási szolgáltatások az Azure Blob Storage például egy biztonságos csatornán (HTTPS).

### <a name="considerations-for-using-gateway"></a>Átjáró használatának szempontjai
* Az adatkezelési átjáró egyetlen példánya több helyszíni adatforrás is használható. Azonban **egyetlen átjárópéldány vannak kötve, csak egy Azure data factory** és a egy másik data factory nem osztható.
* Rendelkezhet **adatkezelési átjárót csak egy példánya** egyetlen gépen telepítve van. Tegyük fel, a helyszíni adatforrások elérését igénylő két adat-előállítók rendelkezik, a két helyszíni számítógépeken átjárók telepítenie kell. Más szóval az átjáró egy adott adat-előállító kötődik
* A **átjáró nem kell ugyanarra a gépre adatforrásként**. Az adatforráshoz közelebb átjáró kellene azonban csökkenti az idő az átjáró csatlakozik az adatforráshoz. Azt javasoljuk, hogy az átjáró telepítését olyan számítógépen, amelyen eltér a, amelyen a helyszíni adatforráshoz. Ha az átjáró és az adatforrás különböző gépeken vannak, az átjáró nem versengenek adatforrás erőforrás esetén.
* Rendelkezhet **több átjárót ugyanazon a helyszíni adatforráshoz csatlakozik, különböző gépeken**. Például előfordulhat, hogy adat-előállítók két kiszolgáló két átjárót, de ugyanazon a helyszíni adatforrás regisztrálva van a mindkét az adat-előállítók.
* Ha már rendelkezik egy átjáró telepítve a számítógépre a(z) egy **Power BI** forgatókönyvben telepítse egy **különálló átjáróra, az Azure Data Factoryhoz** egy másik gépen.
* Átjáró kell használni, akkor is, ha használ **ExpressRoute**.
* Forrásként való kezelése az adatforrás egy a helyszíni adatok (azaz a tűzfal mögött található) is használhatja **ExpressRoute**. A szolgáltatás és az adatforrás közötti kapcsolatot létesíteni az átjáró használatára.
* Meg kell **az átjáró használatára** akkor is, ha az adattárban a felhőben található egy **Azure IaaS virtuális gépek**.

## <a name="installation"></a>Telepítés
### <a name="prerequisites"></a>Előfeltételek
* A támogatott **operációs rendszer** Windows 7, Windows 8 és 8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 verziók a következők. Az adatkezelési átjáró a tartományvezérlő telepítése jelenleg nem támogatott.
* .NET-keretrendszer 4.5.1-es vagy újabb verziója szükséges. Ha az átjáró Windows 7 gépre telepíti, telepítse a .NET-keretrendszer 4.5-ös vagy újabb. Lásd: [.NET-keretrendszer követelményei a System](https://msdn.microsoft.com/library/8z6watww.aspx) részleteiről.
* Az ajánlott **konfigurációs** az átjáró a gép legalább 2 GHz-es, 4 mag, 8 GB RAM és 80 GB-os lemezt.
* A gazdagép frissítéséből szeretnénk, ha az átjáró nem fog válaszolni kérelmek. Ezért a megfelelő konfigurálása **energiaséma** azon a számítógépen az átjáró telepítése előtt. Ha a számítógép hibernált állapotba van konfigurálva, az átjáró telepítése kéri egy üzenetet.
* Egy rendszergazda a gépen telepítheti, és sikeresen konfigurálja az adatkezelési átjárót kell lennie. További felhasználók is hozzáadhat a **adatkezelési átjáró felhasználók** helyi Windows-csoport. Ez a csoport tagjai egyaránt használhatják a **Data Management Gateway Configuration Manager** eszköz az átjáró konfigurálásához.

Másolási tevékenység-végrehajtás egy adott gyakoriságát fordulhat elő, mert az erőforrás-használat (CPU, memória) a gépen is ugyanazt a mintát követi, csúcs-és üresjárati. Erőforrás-használat is nagyban függ az áthelyezett adatok mennyisége. Amikor több másolási feladat van folyamatban, erőforrás-használat csúcsidőben feljebb görgetünk láthatja.

### <a name="installation-options"></a>Telepítési beállítások
Az adatkezelési átjáró a következő módokon telepíthető:

* Töltse le az MSI-telepítő csomag a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).  Az MSI a meglévő a legújabb verzióra, az adatkezelési átjáró frissítése megőrzi a beállításokkal is használható.
* Kattintva **töltse le és telepítse az adatátjáró** manuális telepítés alatti hivatkozásra vagy **telepítheti közvetlenül a számítógépre** EXPRESSZ telepítés alatt. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) gyors telepítés használatával részletesen ismertető cikket. A manuális lépés végigvezeti a letöltőközpontból.  A következő útmutatót: letöltése és telepítése az átjáró a letöltőközpontból a következő szakaszban találhatók.

### <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez:
1. A gazdagépen az átjáró energiasémát beállítani, hogy a gép hibernálásra nem. A gazdagép frissítéséből szeretnénk, ha az átjáró nem fog válaszolni kérelmek.
2. Készítsen biztonsági másolatot az átjáró társított tanúsítvány.

### <a name="install-the-gateway-from-download-center"></a>Az átjáró telepítése a letöltőközpontból
1. Navigáljon a [Microsoft Data Management Gateway letöltési oldal](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kattintson a **letöltése**, válassza ki a megfelelő verziót (**32 bites** vs. **64 bites**), és kattintson a **tovább**.
3. Futtassa a **MSI** közvetlenül, vagy mentse a merevlemezen, és futtassa.
4. Az a **üdvözlő** lapon válassza ki a **nyelvi** kattintson **tovább**.
5. **Fogadja el** a végfelhasználói licencszerződést, majd kattintson **tovább**.
6. Válassza ki **mappa** telepítse az átjárót, és kattintson a **tovább**.
7. Az a **készen áll a telepítésre** kattintson **telepítése**.
8. Kattintson a **Befejezés** telepítésének befejezéséhez.
9. A kulcs lekérése az Azure Portalról. A részletes útmutatást a következő szakaszban talál.
10. Az a **Register átjáró** lapján **Data Management Gateway Configuration Manager** fut a gépen, kövesse az alábbi lépéseket:
    1. Illessze be a kulcs szövegét.
    2. Másik lehetőségként kattinthat **Show átjáró kulcs** , tekintse meg a kulcs szövegét.
    3. Kattintson a **regisztrálása**.

### <a name="register-gateway-using-key"></a>Kulcs használatával átjáró regisztrálása
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Ha még nem hozott létre egy logikai átjárót a portálon
Az átjáró létrehozása a portálon, és a kulcs lekérése a **konfigurálása** lapon, a forgatókönyv a következő lépésekben a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Ha már létrehozta a logikai átjárót a portálon
1. Az Azure Portalon lépjen a **adat-előállító** lapon, és kattintson a **társított szolgáltatások** csempére.

    ![Adat-előállító lap](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Az a **társított szolgáltatások** lapon, válassza ki a logikai **átjáró** a portálon létrehozott.

    ![logikai átjáró](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. Az a **adatátjáró** kattintson **töltse le és telepítse az adatátjáró**.

    ![Letöltési hivatkozás a portálon](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. Az a **konfigurálása** kattintson **hozza létre újra kulcs**. Kattintson az Igen gombra a figyelmeztető üzenetet, gondosan elolvasása után.

    ![Hozza létre újra a kulcsot](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Kattintson a kulcs melletti Másolás gombra. A kulcs a vágólapra másolja.

    ![Kulcs másolása](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Rendszer ikony hlavního panelu és értesítések
Az alábbi képen láthatja, amelyeket ikony hlavního panelu némelyike.

![rendszer ikony hlavního panelu](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Ha a rendszer tálcai ikon/értesítési üzenet fölé viszi a kurzort, egy felugró ablakban az átjáró/frissítés művelet állapotának részleteit láthatja.

### <a name="ports-and-firewall"></a>Portok és tűzfal
Nincsenek a két tűzfal figyelembe kell vennie: **vállalati tűzfal** fut a szervezet a központi útválasztón és **Windows tűzfal** démon a helyi gépen, amelyen az átjáró van konfigurálva telepítve van.  

![tűzfalak](./media/data-factory-data-management-gateway/firewalls2.png)

Vállalati tűzfalon szinten kell konfigurálnia a következő tartományok és a kimenő portokat:

| Tartománynevek | Portok | Leírás |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Adatátviteli szolgáltatás háttérrendszer való kommunikációhoz használatos |
| *.core.windows.net |443 |Használja a szakaszos másolás az Azure Blob használatával (Ha be van állítva)|
| *.frontend.clouddatahub.net |443 |Adatátviteli szolgáltatás háttérrendszer való kommunikációhoz használatos |
| *.servicebus.windows.net |9350-9354, 5671 |Nem kötelező a service bus relay a másolás varázsló által használt TCP-n keresztül |


Windows tűzfal szinten a kimenő portokon általában engedélyezve. Ha nem, konfigurálhatja, hogy a tartományok portok ennek megfelelően átjárót tartalmazó számítógépen.

> [!NOTE]
> 1. Az adatforrás alapján / fogadóként, előfordulhat, hogy az engedélyezési lista további tartományokkal és a kimenő portok tartalmaz a vállalati Windows/tűzfal.
> 2. Az egyes felhőalapú adatbázisok (például: [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [az Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access)használatához és így tovább), szükség lehet a tűzfal-konfiguráció az átjárót tartalmazó számítógépen engedélyezett IP-címét.
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Adatokat másol egy forrásadattárból egy fogadó adattárba
Győződjön meg arról, hogy a tűzfalszabályok megfelelően engedélyezve vannak a vállalati tűzfalon, a Windows tűzfal az átjárót tartalmazó számítógépen, és az adattároló magát. Ezek a szabályok lehetővé teszi, hogy az átjáró mindkét forrás csatlakozhat, és a fogadó sikeresen megtörtént. Egyes adattároló, amely részt vesz a másolási művelet szabályok engedélyezése.

Például, hogy a Másolás **egy helyszíni adattár egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó**, tegye a következőket:

* Engedélyezi a kimenő **TCP** kommunikációs port **1433-as** Windows tűzfal és a vállalati tűzfalon.
* A tűzfalbeállítások konfigurálása az Azure SQL server adja hozzá az átjárót tartalmazó számítógép IP-címét az engedélyezett IP-címek listájához.

> [!NOTE]
> Ha a tűzfal nem engedélyezi a 1433-as kimenő porton, átjáró közvetlenül az Azure SQL nem tud hozzáférni. Ebben az esetben használhatja [szakaszos Másolás](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) az SQL Azure Database és SQL Azure DW. Ebben a forgatókönyvben csak a adatáthelyezéskor lenne szükség HTTPS (443-as porton).
>
>


### <a name="proxy-server-considerations"></a>Proxy server kapcsolatos szempontok
Ha a vállalati hálózati környezet az egy proxykiszolgálón keresztül csatlakozik az internetre, konfigurálja az adatkezelési átjárót a megfelelő proxykiszolgáló-beállításokat használja. A proxy állíthatja be a kezdeti regisztrációs fázis során.

![Proxy beállítása a regisztráció során](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Átjáró a proxykiszolgáló használatával csatlakozik a felhőszolgáltatáshoz. Kattintson a **módosítása** hivatkozásra a kezdeti beállítás során. Megjelenik a **proxybeállítást** párbeszédpanel.

![A Konfigurációkezelő használatával set-proxy](media/data-factory-data-management-gateway/SetProxySettings.png)

Három konfigurációs lehetőség áll rendelkezésre:

* **Proxy használatának mellőzése**: átjáró nem explicit módon használja bármely proxy cloud serviceshez való csatlakozáshoz.
* **Rendszerproxy használata**: átjáró használja a proxybeállításokat, hogy a konfigurált diahost.exe.config és diawp.exe.config.  Ha nincs proxy van konfigurálva a diahost.exe.config és diawp.exe.config, átjáró felhőszolgáltatásához csatlakozva közvetlenül a proxy áthaladás nélkül.
* **Egyéni proxy használatát**: beállítás használata az átjáró diahost.exe.config és diawp.exe.config konfigurációk használata helyett a HTTP-proxy konfigurálása.  Cím és Port is szükséges.  Felhasználónév és jelszó megadása nem kötelező, attól függően, a proxy hitelesítési beállítást.  Minden beállítás titkosítva az átjáró hitelesítőadat-tanúsítványa és az állomás átjárót tartalmazó számítógépen helyben tárolja.

Az adatkezelési átjárót gazdaszolgáltatása automatikusan újraindul, miután mentette a frissített proxybeállításokat.

Miután az átjáró sikeresen regisztrálva lett, ha szeretné megtekintheti vagy frissítheti a webproxy beállításai, használja a Data Management Gateway Configuration Manager.

1. Indítsa el a **Data Management Gateway Configuration Manager**.
2. Váltson a **Settings** (Beállítások) lapra.
3. Kattintson a **módosítása** hivatkozásra **HTTP-Proxy** indítsa el a szakasz a **állítsa be a HTTP-Proxy** párbeszédpanel.  
4. Miután rákattintott a **tovább** gombra, megjelenik egy figyelmeztető párbeszédpanel, mentse a proxybeállításokat, és indítsa újra a gazdagépet a engedélyt kér.

Megtekintheti, és a HTTP-proxy frissítése a Configuration Manager eszközzel.

![A Konfigurációkezelő használatával set-proxy](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Ha az NTLM-hitelesítés proxykiszolgálót állít be, átjáró Gazdaszolgáltatásának a tartományi fiók alatt fut. Ha módosítja a később a tartományi fiók jelszavát, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, és ennek megfelelően indítsa újra. Ez a követelmény miatt javasoljuk, hogy a proxykiszolgálóhoz, hogy a jelszó frissítése gyakran nem igényel dedikált tartományi fiókot használhatja.
>
>

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló-beállításainak konfigurálása
Ha **rendszerproxy használata** átjáró használja a HTTP-proxy beállítása, a proxybeállítást diahost.exe.config és diawp.exe.config.  Ha nincs proxy diahost.exe.config és diawp.exe.config van megadva, átjáró felhőszolgáltatásához csatlakozva közvetlenül a proxy áthaladás nélkül. Az alábbi eljárás ismerteti a diahost.exe.config fájl frissítése.  

1. A Fájlkezelőben győződjön meg a C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config biztonsági mentése az eredeti fájlt egy biztonságos példányát.
2. Indítsa el a Notepad.exe rendszergazdaként futtatja, és nyissa meg a szöveges fájl "C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config. Az alapértelmezett címke a system.net található, az alábbi kódban látható módon:

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   Ezután hozzáadhatja a proxykiszolgáló adatai az alábbi példában látható módon:

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   További tulajdonságok, adja meg a szükséges beállításokat, például a scriptLocation engedélyezettek a proxy címkén belül. Tekintse meg [elem (hálózati beállítások) proxy](https://msdn.microsoft.com/library/sa91de1e.aspx) szintaxisról.

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. A konfigurációs fájlt az eredeti helyre mentse, majd indítsa újra a Data Management Gateway gazdagép szolgáltatást, amely szerzi be a módosításokat. A szolgáltatás újraindításához: használja a Vezérlőpult, vagy a szolgáltatások kisalkalmazás a **Data Management Gateway Configuration Manager** > kattintson a **szolgáltatás leállítása** gombra, majd kattintson a **indítása Szolgáltatás**. Ha a szolgáltatás nem indul el, valószínű, hogy hozzáadta-e egy XML-címke helytelen szintaxis szerkesztettek alkalmazás konfigurációs fájlba.

> [!IMPORTANT]
> Ne felejtse el frissíteni a **mindkét** diahost.exe.config és diawp.exe.config.  


Ezeken a pontokon kívül is szüksége, hogy a Microsoft Azure, amely a vállalat engedélyezési lista. Érvényes Microsoft Azure IP-címek listájának letölthető a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Tűzfal és proxy-kiszolgálóval kapcsolatos problémák lehetséges tünetek
Ha a következő hasonló hibákat észlel, akkor valószínű, a tűzfal vagy proxy kiszolgálóra, amely blokkolja az átjárót a Data Factory kapcsolódni önmaga hitelesítéséhez a helytelen konfiguráció miatt. Tekintse át a tűzfalat, hogy az előző szakaszban, és a proxykiszolgáló megfelelően legyenek konfigurálva.

1. Amikor megpróbálja regisztrálni az átjáró, a következő hibaüzenetet kapja: "nem sikerült regisztrálni az átjáró-kulcsot. Regisztrálja újra az átjáró kulcs előtt győződjön meg arról, hogy az adatkezelési átjárót egy csatlakoztatott állapotban van, és a Data Management Gateway szolgáltatás elindult."
2. A Configuration Manager megnyitásakor állapotot látja "Leválasztott" vagy "Csatlakozás". Ha Windows-eseménynaplók, a "Eseménynapló" > "Alkalmazások és szolgáltatások Logs" > "Adatkezelési átjáró", hibaüzenetek jelennek meg például a következő hiba: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Nyissa meg a hitelesítő adatok titkosításához 8050 port
A **hitelesítő adatok beállítása** alkalmazás használja a bejövő portot **8050** relay hitelesítő adatait az átjáróhoz, az Azure Portalon egy helyszíni társított szolgáltatás üzembe helyezésekor meg. Átjáró telepítésekor alapértelmezés szerint az átjáró telepítése megnyitja az átjárót tartalmazó számítógépen.

Ha egy külső tűzfalat használ, a portot 8050 manuálisan is megnyithatja. Ha tűzfal probléma átjáró telepítése során, próbálja meg a következő paranccsal telepítse az átjárót a tűzfal konfigurálása nélkül.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Ha nem kíván nyissa meg a-8050 az átjárót tartalmazó számítógépen, eltérő használatával mechanizmusok használata a **hitelesítő adatok beállítása** alkalmazás konfigurálása az adattár hitelesítő adatait. Használhat például [New-AzureRmDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) PowerShell-parancsmagot. Lásd: [hitelesítő adatok beállítása és biztonsági](#set-credentials-and-securityy) állítható be, hogyan tárolhatja az adatokat a hitelesítő adatok szakaszban.

## <a name="update"></a>Frissítés
Alapértelmezés szerint az adatkezelési átjáró automatikusan frissül, ha az átjáró egy újabb verziója érhető el. Az átjáró nem frissül, amíg az ütemezett feladatokat kell elvégezni. Nincsenek további tevékenységeket dolgozza fel az átjáró a frissítési művelet befejeződéséig. Ha a frissítés sikertelen, átjáró, a régi verziót vissza lesz állítva.

Az ütemezett frissítés ideje jelenik meg a következő helyeken:

* Az átjáró tulajdonságai lap az Azure Portalon.
* A Data Management Gateway Configuration Manager kezdőlapja
* Rendszer tálca értesítési üzenetet.

Kezdőlap lapján a Data Management Gateway Configuration Manager megjeleníti a frissítési ütemezés és a legutóbbi alkalommal az átjáró telepítése/frissítése.

![Frissítések ütemezése](media/data-factory-data-management-gateway/UpdateSection.png)

Azonnal telepítse a frissítést, vagy várja meg az ütemezett időpontban automatikusan frissíteni kell az átjárót. Ha például az alábbi képen látható, az értesítési üzenet jelenik meg a Gateway Configuration Manager és a frissítés gomb, amelyre kattintva közvetlenül telepítheti.

![Frissítés a DMG Configuration Managerben](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Az értesítési üzenet oznamovací oblasti hlavního jelenne meg, az alábbi képen látható módon:

![Rendszerüzenet tálca](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Frissítési művelet (Manuális vagy automatikus) v oznamovací oblasti hlavního állapotának megtekintéséhez. Átjáró a Configuration Manager indítása következő alkalommal, amikor egy üzenet jelenik meg az értesítési sávban, hogy az átjáró együtt mutató hivatkozás lett frissítve a [Mi az új témakör](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Tiltsa le/engedélyezze az automatikus frissítés funkció
Akkor is tiltsa le/engedélyezze az automatikus frissítési szolgáltatás a következő lépések végrehajtásával:

[Az egyetlen csomópontos átjárót]
1. Indítsa el a Windows Powershellt az átjárót tartalmazó számítógépen.
2. Váltson arra a C:\Program Files\Microsoft integrációs Runtime\3.0\PowerShellScript\ mappára.
3. Futtassa a következő parancsot, kapcsolja be az automatikus frissítési szolgáltatás kikapcsolása (Letiltás).   

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -off
    ```
4. Kapcsolja be újra be:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on  
    ```
[Több csomópontos magas rendelkezésre állású és méretezhető átjáró](data-factory-data-management-gateway-high-availability-scalability.md)
1. Indítsa el a Windows Powershellt az átjárót tartalmazó számítógépen.
2. Váltson arra a C:\Program Files\Microsoft integrációs Runtime\3.0\PowerShellScript\ mappára.
3. Futtassa a következő parancsot, kapcsolja be az automatikus frissítési szolgáltatás kikapcsolása (Letiltás).   

    Átjáró magas rendelkezésre állású szolgáltatás egy extra AuthKey param szükség.
    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. Kapcsolja be újra be:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 
    ```

## <a name="configuration-manager"></a>Configuration Manager
Miután telepítette az átjárót, a következő módszerek valamelyikével indíthatja el Data Management Gateway Configuration Manager:

1. Az a **keresési** ablakot, írja be **adatkezelési átjáró** elérni ezt a segédprogramot.
2. Futtassa a végrehajtható fájlt **ConfigManager.exe** mappában: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

### <a name="home-page"></a>Kezdőlap
A kezdőlap lehetővé teszi, hogy a következő műveleteket:

* (Csatlakozva a felhőszolgáltatáshoz stb.) az átjáró állapotának megtekintése.
* **Regisztráljon** egy kulccsal a portálról.
* **Állítsa le** indítsa el a **Data Management Gateway gazdaszolgáltatása** az átjárót tartalmazó számítógépen.
* **Frissítések ütemezése** a nap egy adott időpontban.
* A dátum, amikor az átjáró volt megtekintése **utolsó frissítés**.

### <a name="settings-page"></a>Beállítások lap
A beállítások lapon tegye a következőket teszi lehetővé:

* Megtekintése, módosítása és exportálása **tanúsítvány** az átjáró által használt. Ezt a tanúsítványt az adatforráshoz tartozó hitelesítő adatok titkosításához használatos.
* Változás **HTTPS-port** a végponthoz. Az átjáró megnyit egy portot az adatforrás hitelesítő adatainak beállításához.
* **Állapot** a végpont
* Nézet **SSL-tanúsítvány** adatforrásokhoz tartozó hitelesítő adatok beállítása a portál és az átjáró közötti SSL-kommunikációra szolgál.  

### <a name="remote-access-from-intranet"></a>Az intranetes távoli hozzáférés  
Ez a funkció a jövőben engedélyezve lesz. Az az elkövetkező frissítések (v3.4 vagy újabb) tudatjuk, engedélyezése / letiltása a távoli kapcsolat, amely még ma történik (lásd fent) 8050 portot használja a hitelesítő adatok titkosításához a PowerShell vagy a hitelesítőadat-kezelő alkalmazás használatakor. 

### <a name="diagnostics-page"></a>Diagnosztika lap
A Diagnosztika lap lehetővé teszi, hogy a következő műveleteket:

* A részletes engedélyezés **naplózás**naplóinak megtekintése az eseménynaplóban és naplók küldése a Microsoftnak, ha hiba lépett fel.
* **Kapcsolat tesztelése** egy adatforrásba.  

### <a name="help-page"></a>Súgóoldalt
A következő súgóoldalt az alábbi információkat jeleníti meg:  

* Az átjáró rövid leírása
* Verziószám
* Online súgó, az adatvédelmi nyilatkozat és a licencszerződés mutató hivatkozásokat tartalmaz.  

## <a name="monitor-gateway-in-the-portal"></a>A figyelő átjárót a portálon
Az Azure Portalon megtekintheti közel valós idejű pillanatképét erőforrás-használat (CPU, memória, network(in/out), stb.) egy átjárót tartalmazó számítógépen.  

1. Az Azure Portalon keresse meg az adat-előállító kezdőlapja, és kattintson **társított szolgáltatásokat** csempére. 

    ![Data factory kezdőlap](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Válassza ki a **átjáró** a a **társított szolgáltatásokat** lapot.

    ![Társított szolgáltatások lap](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Az a **átjáró** lapon láthatja a memória és CPU-használat az átjáró.

    ![Átjáró CPU és memória kihasználtsága](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Engedélyezése **speciális beállítások** például a hálózati forgalom további részletek megtekintéséhez.
    
    ![Speciális monitorozás átjáró](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Az alábbi táblázat ismerteti az oszlopok a **Átjárócsomópontok** lista:  

Figyelési tulajdonság | Leírás
:------------------ | :---------- 
Name (Név) | A logikai átjáró és a csomópontok kívánt átjáróval társított neve. Csomópont egy helyszíni Windows-gépen, amelyen az átjáró telepítve. Egynél több csomópont (legfeljebb négy csomópont) az egyetlen logikai átjáró, amely további információkért lásd: [Data Management Gateway - magas rendelkezésre állás és méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md).    
status | A logikai átjáró és az átjáró csomópontok állapotát. Példa: Online/Offline/korlátozott/stb. A fenti állapotok megjelenése kapcsolatos információkért lásd: [átjáró állapota](#gateway-status) szakaszban. 
Verzió | A logikai átjáró, és minden egyes átjárócsomópont verzióját mutatja. A logikai átjáró verziója határozza meg a csoport csomópontjának többsége verzióján alapul. Ha nincs a logikai átjáró beállításai, és verzió száma azonos a logikai átjáró függvény csak a csomópontok különböző verziójú csomópontok megfelelően. Mások a korlátozott módban van, és manuálisan kell frissíteni, (csak abban az esetben az automatikus frissítés nem működik). 
Elérhető memória | Rendelkezésre álló memória egy átjáró-csomóponton. Ez az érték közel valós idejű pillanatképet. 
Processzorkihasználtság | CPU-kihasználtság egy átjáró-csomópont. Ez az érték közel valós idejű pillanatképet. 
Hálózatkezelés (In/Out) | A hálózathasználat egy átjáró-csomópont. Ez az érték közel valós idejű pillanatképet. 
Egyidejű feladatok (futó / Limit) | Feladatok és minden egyes csomóponton futó feladatok száma. Ez az érték közel valós idejű pillanatképet. Korlát azt jelzi, hogy az egyidejű feladatok maximális száma minden egyes csomópont esetében. Ez az érték van megadva a mérete alapján. A vertikális felskálázása speciális esetekben, ahol CPU/memória/hálózati kevésbé használt, de tevékenységek időkorlátja egyidejű feladat-végrehajtási korlát növeléséhez. Ez a funkció egy egycsomópontos átjárón (akkor is, ha a skálázhatóság és rendelkezésre állás funkció nincs engedélyezve) is érhető el.  
Szerepkör | Dispatcher és feldolgozói szerepkörök a több csomópontos átjárót – két típusa van. Minden csomópont a dolgozók, ami azt jelenti, hogy az összes felhasználásuk feladatok végrehajtásához. Nincs dispatcher csak egy csomópont, amely lekéréses feladatok/feladatok a cloud servicesből, és melyik másik munkavégző csomópontok (beleértve a saját maga) használható.

Ezen a lapon láthatja bizonyos beállítások, amelyek több értelme, ha az átjáró legalább két csomóponttal (horizontális felskálázás forgatókönyv). Lásd: [Data Management Gateway - magas rendelkezésre állás és méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md) több csomópontos átjáró beállítása részleteit.

### <a name="gateway-status"></a>Átjáró állapota
Az alábbi táblázat ismerteti a lehetséges állapotok egy **átjárócsomópont**: 

status  | Megjegyzések és forgatókönyvek
:------- | :------------------
Online | Csomópont csatlakoztatva a Data Factory szolgáltatásban.
Offline | Csomópont offline állapotban.
Frissítés | A csomópont automatikus frissítése folyamatban van.
Korlátozott | Kapcsolat nem látható probléma miatt. HTTP-port 8050 probléma, a service bus kapcsolódási probléma vagy a hitelesítő adatok szinkronizálási problémája miatt lehet. 
Inaktív | Csomópontnak számít eltér a többi legtöbb csomópont konfigurációjának konfigurációban.<br/><br/> Egy csomópont inaktív lehet, ha a többi csomópont nem tud kapcsolódni. 


Az alábbi táblázat ismerteti a lehetséges állapotok egy **logikai átjáró**. Az átjáró állapotának ellenőrzéséhez az átjárócsomópontok állapotainak függ. 

status | Megjegyzések
:----- | :-------
Regisztrálni kell | Nem csomópont még regisztrálva van a logikai átjáró
Online | Az Átjárócsomópontok online állapotban.
Offline | Nincsenek csomópont online állapotú.
Korlátozott | Ezt az átjárót nem minden csomópontja kifogástalan állapotban vannak. Ez az állapot nem figyelmezteti, hogy egyes csomópontok esetleg nem működik! <br/><br/>Hitelesítő adatok szinkronizálási problémát a kezelő/munkavégző csomópont okozhatja. 

## <a name="scale-up-gateway"></a>Vertikális felskálázás átjáró
Beállíthatja, hogy hány **egyidejű adatok mozgását feladatok** , amely képes futni a csomóponton, vertikális felskálázása az adatok áthelyezését a helyszíni és a felhő között a funkció adattárakban. 

A rendelkezésre álló memória és a Processzor nem használhatók jól, de a kihasználatlan kapacitásért értéke 0, érdemes a vertikális felskálázáshoz, amely képes futni a csomóponton egyidejű feladatok számának növelése. Érdemes azt is, vertikális felskálázás akár, amikor a tevékenységek időtúllépésekbe ütközzenek, mert az átjáró túl van terhelve. Az átjáró csomópont speciális beállításait a maximális kapacitás egy csomópont lehet növelni. 
  

## <a name="troubleshooting-gateway-issues"></a>Átjáró kapcsolatos hibák elhárítása
Lásd: [hibaelhárítási kérdéseket](data-factory-troubleshoot-gateway-issues.md) cikk információk/tippek kapcsolatos hibák elhárításának az adatkezelési átjáró segítségével.  

## <a name="move-gateway-from-one-machine-to-another"></a>Átjáró áthelyezése egyik gépről egy másikra
Ez a szakasz lépéseit mozgó átjáró ügyfél egyik gépről egy másik számítógépre.

1. A portálon lépjen a **adat-előállító kezdőlapja**, és kattintson a **társított szolgáltatásokat** csempére.

    ![Átjárók adatkapcsolat](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Válassza ki az átjáró a **DATA GATEWAYS** szakaszában a **társított szolgáltatások** lap.

    ![A kiválasztott átjáró társított szolgáltatások lap](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Az a **adatátjáró** kattintson **töltse le és telepítse az adatátjáró**.

    ![Töltse le az átjáró-hivatkozás](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Az a **konfigurálása** kattintson **töltse le és telepítse az adatátjáró**, és kövesse az utasításokat követve telepítse az átjárót a gépen.

    ![Lapjának konfigurálása](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Tartsa a **Microsoft Data Management Gateway Configuration Manager** megnyitásához.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. Az a **konfigurálása** oldalra a portálon, kattintson a **hozza létre újra kulcs** a parancssávon, majd kattintson a **Igen** a figyelmeztető üzenet. Kattintson a **Másolás gombbal** mellett kulcsfontosságú, hogy a kulcs másolása a vágólapra. A régi gépen az átjáró nem működik, mert hamarosan hozza létre a kulcsot.  

    ![Hozza létre újra a kulcsot](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Illessze be a **kulcs** a szövegmezőbe írja be a **átjáró regisztrálása** lapján a **Data Management Gateway Configuration Manager** a gépen. (nem kötelező) Kattintson a **Show átjáró kulcs** melletti jelölőnégyzetet, hogy tekintse meg a kulcs szövegét.

    ![Kulcs másolása és regisztrálása](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kattintson a **regisztrálása** , regisztrálja az átjárót a felhőszolgáltatáshoz.
9. A a **beállítások** lapra, majd **módosítása** válassza ki ugyanazt a tanúsítványt, amelyet a régi átjáróval használt, adja meg a **jelszó**, és kattintson a **Befejezés**.

   ![Adja meg a tanúsítvány](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Exportálhatja a tanúsítványt a régi átjáró a következő lépések végrehajtásával: Indítsa el a Data Management Gateway Configuration Manager a régi gépen, váltson át a **tanúsítvány** lapra, majd **exportálása** gombra, és kövesse az utasításokat.
10. Ha az átjáró sikeres regisztráció esetén jelenik meg a **regisztrációs** beállítása **regisztrált** és **állapota** beállítása **elindítva** a kezdőlapon az átjáró a Configuration Manager.

## <a name="encrypting-credentials"></a>Hitelesítő adatok titkosítása
A Data Factory Editorban hitelesítő adatok titkosításához, tegye a következőket:

1. A webböngésző indítása a **átjárót tartalmazó számítógépen**, navigáljon a [az Azure portal](http://portal.azure.com). Az adat-előállító keresése, ha szükséges, nyissa meg az adat-előállítóhoz a **adat-előállító** lapon, majd kattintson **létrehozás és üzembe helyezés** Data Factory Editor elindításához.   
2. Kattintson egy meglévő **társított szolgáltatás** a fanézetben megtekintheti a JSON-definíciójában, vagy létrehoz egy társított szolgáltatást, amely egy adatkezelési átjárón igényel (például: SQL Server- vagy Oracle).
3. A JSON-szerkesztőben a a **átjárónév** tulajdonság, adja meg az átjárója nevére.
4. Adja meg a kiszolgáló nevét a **adatforrás** tulajdonságot a **connectionString**.
5. Adja meg az adatbázisnév a **Initial Catalog** tulajdonságot a **connectionString**.    
6. Kattintson a **titkosítása** gombra a parancssávon kattintson a indító – Miután **hitelesítőadat-kezelő** alkalmazás. Megtekintheti a **hitelesítő adatok beállítása** párbeszédpanel bezárásához.

    ![A beállítás hitelesítő adatok párbeszédpanel](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. Az a **hitelesítő adatok beállítása** párbeszédpanelen tegye a következőket:
   1. Válassza ki **hitelesítési** , amelyet a Data Factory szolgáltatás csatlakozik az adatbázishoz.
   2. Adjon meg nevet a felhasználó, aki hozzáféréssel rendelkezik az adatbázishoz tartozó a **felhasználónév** beállítás.
   3. Adja meg a jelszót a felhasználó a **jelszó** beállítás.  
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
Ha a portálon, amely eltér az átjárót tartalmazó számítógépen gépről éri el, győződjön meg arról, hogy a hitelesítő adatok kezelőjének alkalmazás képes-e csatlakozni az átjárót tartalmazó számítógépen. Ha az alkalmazás nem tudja elérni az átjárót tartalmazó számítógépen, azt nem teszi lehetővé az adatforráshoz tartozó hitelesítő adatok beállítása és az adatforrás kapcsolat ellenőrzéséhez.  

Használatakor a **hitelesítő adatok beállítása** alkalmazás, a portál titkosítja a hitelesítő adatokat a megadott tanúsítvány a **tanúsítvány** lapján a **Gateway Configuration Manager**  az átjárót tartalmazó számítógépen.

Ha a hitelesítő adatok titkosításához egy API-alapú módszert keres, akkor használhatja a [New-AzureRmDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) hitelesítő adatainak titkosítása PowerShell-parancsmagot. A parancsmag a tanúsítványt használja, hogy az átjáró a hitelesítő adatok titkosításához használatára van konfigurálva. A titkosított hitelesítő adatok hozzáadása a **EncryptedCredential** eleme a **connectionString** a JSON-fájlban. A JSON-t használ a [New-AzureRmDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) parancsmag vagy a Data Factory szerkesztőjében.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Van egy további megközelítés a Data Factory Editor használatával hitelesítő adatok beállítása. Ha létrehoz egy SQL Server társított szolgáltatást a szerkesztő használatával, és adja meg hitelesítő adatait egyszerű szövegként, a hitelesítő adatok titkosítottak egy tanúsítvánnyal, amely a Data Factory szolgáltatás tulajdonosa. Ez a tanúsítvány nem használható, hogy az átjáró használatára van konfigurálva. Lehet, hogy ez a módszer bizonyos esetekben egy kicsit gyorsabb, míg a kevésbé biztonságos. Ezért azt javasoljuk, hogy kövesse ezt a megközelítést csak fejlesztési-tesztelési célokra.

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok
Ez a szakasz azt ismerteti, hogyan hozhat létre, és regisztrálnia kell egy átjárót, az Azure PowerShell-parancsmagok használatával.

1. Indítsa el a **Azure PowerShell-lel** rendszergazdai módban.
2. Jelentkezzen be az Azure-fiókjába a következő parancs futtatásával, és az Azure hitelesítő adatok megadása.

    ```PowerShell
    Connect-AzureRmAccount
    ```
3. Használja a **New-AzureRmDataFactoryGateway** logikai-átjárók létrehozására a következő parancsmagot:

    ```PowerShell
    $MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **A példában szereplő parancs és a kimeneti**:

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

1. Az Azure PowerShellben váltson arra a mappára: **C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript\**. Futtatás **RegisterGateway.ps1** a helyi változókhoz kapcsolódó **$Key** , ahogyan az alábbi parancsot. Ez a szkript a ügyfél ügynöke telepítve van a gépén a korábban létrehozott logikai átjáróval regisztrálja.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Az átjáró egy távoli gépen a IsRegisterOnRemoteMachine paraméter használatával regisztrálhatja. Példa:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Használhatja a **Get-AzureRmDataFactoryGateway** az átjárók listájának beolvasása a data Factory-parancsmagot. Ha a **állapot** látható **online**, ez azt jelenti, hogy az átjáró készen áll a használatra.

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Egy átjáró használatával eltávolíthatja a **Remove-AzureRmDataFactoryGateway** egy átjáró használatával a parancsmag és a frissítés leírását a **Set-AzureRmDataFactoryGateway** parancsmagok. Szintaxist és egyéb részletek ezekről a parancsmagokról lásd: a Data Factory parancsmagjainak leírása.  

### <a name="list-gateways-using-powershell"></a>Lista átjárókon a PowerShell használatával

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Távolítsa el a gatewayen a PowerShell használatával

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>További lépések
* Lásd: [közötti a helyszíni és felhőalapú adattárak](data-factory-move-data-between-onprem-and-cloud.md) cikk. A forgatókönyv az adatok áthelyezése a helyszíni SQL Server-adatbázisból Azure-blobba az átjárót használó folyamatot hoz létre.  

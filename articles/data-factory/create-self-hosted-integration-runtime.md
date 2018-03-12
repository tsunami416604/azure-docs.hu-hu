---
title: "Önálló üzemeltetett integrációs futásidejű létrehozása az Azure Data Factory |} Microsoft Docs"
description: "Ismerje meg, hogy önálló üzemeltetett integrációs futásidejű létrehozása az Azure Data Factory, amely lehetővé teszi az adat-előállítók való hozzáférést egy magánhálózaton."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 3f1b55f2752821de447e6c03bcbf79f01d9f8264
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Hozzon létre, és Self-hosted integrációs futásidejű konfigurálása
Az integrációs futásidejű (IR) a számítási infrastruktúrától által használt Azure Data Factory adatok integrációs funkciók különböző hálózati környezetek között. Infravörös kapcsolatos részletekért lásd: [integrációs futásidejű áttekintése](concepts-integration-runtime.md).

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

Egy önálló üzemeltetett integrációs futásidejű rendszere között egy felhőbeli adatát másolási tevékenység futtatására képes, tárolók és a magánhálózati és átalakítás tevékenységeket célozzon terjesztéséhez adattárat számítási erőforrásokat egy helyszíni vagy Azure virtuális hálózat. Önálló üzemeltetett integrációs futásidejű szükségleteinek telepíthető egy a helyi számítógépen vagy egy virtuális gépet egy magánhálózaton belül.  

Ez a dokumentum bemutatja, hogyan hozhat létre, és Self-hosted infravörös konfigurálása

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Magas szintű lépéseket kell IR önálló központi telepítése
1.  Hozzon létre egy önálló üzemeltetett integrációs futásidejű. Íme egy PowerShell-példa:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  Töltse le és telepítse önállóan üzemel integrációs futásidejű (a helyi számítógép).
3.  Hitelesítési kulcs lekérését és önálló üzemeltetett integrációs futásidejű regisztrálja a kulcsot. Íme egy PowerShell-példa:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>Parancs folyamata és adatfolyama
Amikor az adatok a helyszínen és a felhő között, a tevékenység egy önálló üzemeltetett integrációs futásidejű használ az adatok átvitele a helyszíni adatforrás felhő-és fordítva.

Íme egy magas szintű adatfolyama példányához, az önálló üzemeltetett IR lépéseket összefoglalása:

![Áttekintés](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Adatok fejlesztő létrehoz egy önálló üzemeltetett integrációs futásidejű egy PowerShell-parancsmag használatával az Azure data factory belül. Az Azure-portál jelenleg nem támogatja ezt a szolgáltatást.
2. Adatok fejlesztői megadását követően az önálló üzemeltetett integrációs futásidejű példány, amelyet csatlakozni az adattároló használnia kell a helyszíni adatokat tároló csatolt szolgáltatás létrehozása. A társított szolgáltatás beállítása részeként adatok fejlesztő a hitelesítőadat-kezelő alkalmazások (jelenleg nem támogatott) hitelesítési típusok és a hitelesítő adatok beállításához. A hitelesítő alkalmazás párbeszédpanel kommunikál az adattár kapcsolat és a hitelesítő adatok mentését önálló üzemeltetett integrációs futásidejű teszteléséhez.
4.  Önálló üzemeltetett integrációs futásidejű csomópont a hitelesítő adatok használata a Windows Data Protection alkalmazás alkalmazásprogramozási felületet (DPAPI) titkosítja, és helyileg menti. Ha a magas rendelkezésre állás több csomópont, a hitelesítő adatok további szinkronizálódnak más csomópontjai között. Minden csomópont titkosítja azokat a DPAPI-t használ, és helyileg tárolja. Hitelesítő adatok szinkronizálása az adatok fejlesztője számára átlátható, és önálló üzemeltetett infravörös kezeli    
5.  Az önálló üzemeltetett integrációs futásidejű ütemezés & a felügyeleti feladatok keresztül kommunikál a Data Factory szolgáltatásnak **vezérlőcsatorna** , amely használja, a megosztott Azure service bus-üzenetsorba. Ha egy tevékenység feladat kell futtatni, adat-előállító várólisták a kérelmet, és semmilyen hitelesítő információt, (Ha a hitelesítő adatok már nem tárolják az önálló üzemeltetett integrációs Runtime). A feldolgozás után a várólista lekérdezési önálló üzemeltetett integrációs futásidejű másolattól.
6.  Önálló üzemeltetett integrációs futásidejű másolja ki egy a helyszíni adatokat, a felhőalapú tárolást, vagy fordítva a másolási tevékenység során az adatok adatcsatorna beállításaitól függően. Ebben a lépésben az önálló üzemeltetett integrációs futásidejű közvetlenül kommunikál a felhőalapú tárolási szolgáltatások például az Azure Blob Storage egy biztonságos csatornán (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Önálló üzemeltetett IR használatának szempontjai

- Egy egyetlen önálló üzemeltetett integrációs futásidejű használható több helyszíni adatforráshoz. Azonban egy **egyetlen önálló üzemeltetett integrációs futásidejű kötődik csak egy Azure data factory** és nem lehet megosztani az egy másik data factoryvel.
- Akkor is **önálló üzemeltetett integrációs futásidejű csak egy példánya** egy gépen telepítve. Tegyük fel, amely a helyszíni adatforrások eléréséhez szükséges két adat-előállítók rendelkezik, a két helyszíni számítógépeken önálló üzemeltetett integrációs futásidejű telepíteni szeretné. Más szóval egy önálló üzemeltetett integrációs futásidejű egy adott adat-előállító kötődik
- A **önálló üzemeltetett integrációs futásidejű nem kell ugyanazon a számítógépen, az adatforrással**. Azonban rendelkező önállóan üzemel integrációs futásidejű közelebb az adatforráshoz való lerövidíti a önálló üzemeltetett integráció a futási időben az adatforráshoz való kapcsolódáshoz. Azt javasoljuk, hogy telepítse az önálló üzemeltetett integrációs futásidejű olyan gépen, amely eltér a gazdagép a helyszíni adatforráshoz. Ha az önálló üzemeltetett integrációs futásidejű és az adatforrás a különböző gépeken vannak, az önálló üzemeltetett integrációs futásidejű nem "versenyeznek" az adatforrás erőforrásokhoz.
- Akkor is **több önálló üzemeltetett integrációs futtatókörnyezetek csatlakozik egy helyszíni adatforrás különböző gépeken**. Például előfordulhat, hogy adat-előállítók két kiszolgáló két önálló üzemeltetett integrációs futásidejű, de mindkét az adat-előállítók regisztrálva van a helyszíni ugyanazon az adatforráson.
- Ha már van telepítve a számítógépen szolgál átjáró egy **Power BI** forgatókönyvben telepítse egy **külön önálló üzemeltetett integrációs futásidejű az Azure Data Factory** egy másik számítógépen.
- Önálló üzemeltetett integrációs futásidejű-t támogató Adatintegráció Azure virtuális hálózaton belül kell használni.
- Az adatforrás tekinti egy helyszíni adatforrás (tűzfal mögött van) is használatos **ExpressRoute**. Az önálló üzemeltetett integrációs futásidejű használja a szolgáltatás és az adatforrás közötti kapcsolat létrehozásához.
- Az önálló üzemeltetett integrációs futásidejű kell használnia, akkor is, ha az adattár a felhőben lévő egy **Azure IaaS virtuális gépként**.
- Feladatok sikertelenek lehetnek a Self-hosted integrációs futásidejű engedélyezve van a mely FIPS előírásainak megfelelő Windows-kiszolgálóra telepíthető. A probléma megoldása érdekében tiltsa le a FIPS előírásainak megfelelő titkosítási a kiszolgálón. A FIPS előírásainak megfelelő titkosítási letiltásához módosítsa a következő beállításazonosítót 1 (engedélyezve) 0 (letiltva): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Előfeltételek

- A támogatott **operációs rendszer** azok verziók, Windows 7 SP1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. Az önálló üzemeltetett integrációs runtime telepítését egy **tartományvezérlő nem támogatott**.
- **.NET-keretrendszer 4.6.1 vagy újabb** szükséges. Ha önálló üzemeltetett integrációs futásidejű telepíti a Windows 7 gépen, telepítse a .NET-keretrendszer 4.6.1 vagy újabb. Lásd: [.NET-keretrendszer rendszerkövetelmények](/dotnet/framework/get-started/system-requirements) részleteiről.
- Az ajánlott **konfigurációs** az önálló üzemeltetett integrációs futásidejű gép legalább 2 GHz, 4 mag, 8 GB RAM és 80 GB-os lemezre.
- Ha szeretné a gazdaszámítógépet, az önálló üzemeltetett integrációs futásidejű nem válaszol a kérelmek. Ezért konfigurálni egy megfelelő energiaséma azon a számítógépen az önálló üzemeltetett integrációs futásidejű telepítése előtt. Ha a számítógép hibernált állapotba van konfigurálva, az önálló üzemeltetett integrációs futásidejű telepítési megadását kéri az üzenetet.
- A gépen telepítése és konfigurálása sikeresen megtörtént az önálló üzemeltetett integrációs futásidejű rendszergazdának kell lennie.
- Az adott gyakoriságát a másolási tevékenység fut fordulhat elő, mert az az erőforrás-használat (Processzor, memória) a számítógépen is csúcs és üresjárati idő azonos mintát követi. Erőforrás-használat is függ, erősen áthelyezett adatok mennyiségét. Ha több másolási feladat van folyamatban, megjelenik az Erőforrás kihasználtsága csúcsidőben feljebb.

## <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez
Önálló üzemeltetett integrációs futásidejű telepítheti az MSI telepítő csomag letöltése a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Lásd: [helyezze át az adatokat a helyszíni és a felhő között cikk](tutorial-hybrid-copy-powershell.md) részletes útmutatásait.

- Energiaséma konfigurálásához a gazdagépen az önálló üzemeltetett integrációs futásidejű, hogy a gép hibernálásra nem. A gazdaszámítógépen szeretnénk, ha az önálló üzemeltetett integrációs futásidejű offline bekapcsolása.
- Készítsen biztonsági másolatot az önálló üzemeltetett integrációs futásidejű rendszeresen társított hitelesítő adatok.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Telepítése és regisztrálása Self-hosted infravörös letöltőközpontból

1. Navigáljon a [Microsoft integrációs futásidejű letöltési oldala](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kattintson a **letöltése**, válassza ki a megfelelő verzióját (**32 bites** vs. **64 bites**), és kattintson a **következő**.
3. Futtassa a **MSI** közvetlenül, vagy mentse azt a merevlemezt és a Futtatás.
4. Az a **üdvözlő** lapon jelölje be a **nyelvi** kattintson **tovább**.
5. **Fogadja el** a végfelhasználói licencszerződést, majd kattintson **következő**.
6. Válassza ki **mappa** az önálló üzemeltetett integrációs futásidejű telepítéséhez, és kattintson a **következő**.
7. Az a **készen állnak a telepítésre** kattintson **telepítése**.
8. Kattintson a **Befejezés** telepítésének befejezéséhez.
9. Az Azure PowerShell hitelesítési kulcs lekérése. PowerShell-példa a hitelesítési kulcs beolvasása:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Az a **regisztrálni integrációs futásidejű (önállóan üzemel)** oldal a Microsoft integrációs futásidejű Configuration Manager a számítógépen fut, tegye a következőket:
    1. Beillesztés a **hitelesítési kulcs** a szöveg területen.
    2. Ha úgy gondolja, a **megjelenítése hitelesítési kulcs** a fő szöveg.
    3. Kattintson a **regisztrálása**.


## <a name="high-availability-and-scalability"></a>Magas rendelkezésre állás és méretezhetőség
Egy Self-hosted integrációs futásidejű társítható több helyszíni gépeket. Ezek a gépek csomópontoknak nevezzük. Egy Self-hosted integrációs futásidejű társított legfeljebb négy csomópont lehet. Több csomópont (a telepített átjárót a helyszíni gépeket) rendelkező logikai átjáró előnyei a következők:
1. Magas rendelkezésre állás Self-hosted integrációs futtatókörnyezet, hogy már nem a hibaérzékeny pontok kialakulását a Big Data típusú adatok megoldás vagy a felhőbeli adatok integráció az Azure Data Factoryvel, legfeljebb 4 csomópontokkal folytonosságának biztosítása érdekében.
2. A helyszíni és a felhő közötti adatátvitel során teljesítményére és átviteli továbbfejlesztett adattárolókhoz. További információ [teljesítmény összehasonlítása](copy-activity-performance.md).

A Self-hosted integrációs futásidejű szoftverek egyszerűen telepítésével több csomópont is társíthat a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717) és a hitelesítési kulcsokat nyert egyikével regisztrálásával Új AzureRmDataFactoryV2IntegrationRuntimeKey parancsmag leírtak szerint a [oktatóanyag](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Nem kell új Self-hosted integrációs futásidejű az egyes csomópontok közötti társítás létrehozásával. Az önálló üzemeltetett integrációs futásidejű telepítheti egy másik számítógépen, és regisztrálja a azonos hitelesítési kulcs használatával. 

> [!NOTE]
> Egy másik csomópont hozzáadása előtt **magas rendelkezésre állás és méretezhetőség**, győződjön meg arról **"Távelérés-intranethez"** lehetőség **engedélyezett** az 1. csomóponton (Microsoft Integrációs futásidejű Configuration Manager -> Beállítások -> Távoli intranetes hozzáférés). 

### <a name="tlsssl-certificate-requirements"></a>A TLS/SSL-tanúsítványokkal szemben támasztott követelmények
Az alábbiakban a futásidejű csomópontok integrációs közötti kommunikáció tehető biztonságossá használt TLS/SSL-tanúsítványra vonatkozó követelményekről:

- A tanúsítványnak kell lennie egy nyilvánosan megbízható X509 v3 tanúsítvány. Azt javasoljuk, hogy a nyilvános (külső) hitelesítésszolgáltató (CA) által kiállított tanúsítványokat használ.
- Minden integrációs futásidejű csomópont ezt a tanúsítványt megbízhatónak kell lennie.
- Helyettesítő tanúsítványokat támogatottak. Ha a tartománynév **node1.domain.contoso.com**, használhat ***. domain.contoso.com** , a tanúsítvány tulajdonosának nevével.
- SAN-tanúsítványok használata nem ajánlott, mert a tulajdonos alternatív neve csak az utolsó elem használja, az összes többit figyelmen kívül aktuális korlátai miatt. Például a SAN tanúsítványa, amelynek SAN vannak **node1.domain.contoso.com** és **node2.domain.contoso.com**, amelynek FQDN-je gépen csak használható ezzel a tanúsítvánnyal **node2.domain.contoso.com**.
- Támogatja az SSL-tanúsítványok Windows Server 2012 R2 által támogatott bármely kulcs mérete.
- Tanúsítvány használatával CNG kulcsok használata nem támogatott. Doesrted DoesDoes nem támogatja a CNG-kulccsal használó tanúsítványokat.

## <a name="system-tray-icons-notifications"></a>Rendszer tálcaikonok / értesítések
Ha kurzor átvitele a rendszer tálcai ikon/értesítési üzenetet, az önálló üzemeltetett integrációs futásidejű állapotának adatait találja.

![Rendszer tálca értesítések](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portok és a tűzfalon
Nincsenek két tűzfal figyelembe kell vennie: **vállalati tűzfal** a szervezet központi útválasztó futó és **Windows tűzfal** konfigurálva démon a helyi számítógépen, ahol a önálló üzemeltetett integrációs futásidejű telepítve van.

![Tűzfal](media\create-self-hosted-integration-runtime\firewall.png)

A **vállalati tűzfal** szint kell konfigurálnia a következő tartományokkal és a kimenő portok:

Tartománynevek | Portok | Leírás
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | Az adatátviteli szolgáltatás háttér-kommunikációhoz használt
*.core.windows.net | 443 | Használt előkészített másolása Azure Blob használatával (Ha be van állítva)
*.frontend.clouddatahub.net | 443 | Az adatátviteli szolgáltatás háttér-kommunikációhoz használt

A **Windows tűzfal** szintet (számítógép), a kimenő portok általában engedélyezve vannak. Ha nem, a tartományok és ennek megfelelően a önállóan üzemel portok konfigurálhatók integrációs futásidejű gép.

> [!NOTE]
> A forrás alapján / felül, előfordulhat, hogy az engedélyezési lista további tartományokkal és a kimenő portok a vállalati és a Windows tűzfalon.
>
> Az egyes felhőalapú adatbázisok (például: Azure SQL-adatbázis, az Azure Data Lake, stb.), szükség lehet önálló központi integrációs futásidejű gépet a tűzfal konfigurációját az engedélyezett IP-címére.

### <a name="copy-data-from-a-source-to-a-sink"></a>A fogadó forrásból származó adatok másolása
Győződjön meg arról, hogy a tűzfalszabályok megfelelően engedélyezve vannak a vállalati tűzfalon, a Windows tűzfal az önálló üzemeltetett integrációs futásidejű gépen, és az adatok tárolásához magát. Ezek a szabályok lehetővé teszi, hogy az önálló üzemeltetett integrációs futásidejű mindkét adatforráshoz kapcsolódnak, és sikeresen gyűjtése. Minden egyes adattároló, amely részt vesz a másolási művelet szabályok engedélyezése.

Például, hogy másolását egy **a helyszíni adatok tárolására az Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó**, hajtsa végre a következő lépéseket:

- Kimenő forgalom engedélyezése **TCP** kommunikációs port **1433** a Windows tűzfal és a vállalati tűzfalon.
- A tűzfal beállításainak Azure SQL-kiszolgáló hozzáadása a saját üzemeltetett integrációs futásidejű gép IP-címét az engedélyezett IP-címek listájához.

> [!NOTE]
> Ha a tűzfal nem engedélyezi a 1433-as kimenő port, önálló üzemeltetett integrációs futásidejű nem férhetnek hozzá Azure SQL közvetlenül. Ebben az esetben használhatja [előkészített másolási](copy-activity-performance.md) az SQL Azure Database-/ SQL Azure DW. Ebben a forgatókönyvben csak az adatátvitelt jelölik a lenne szükséges HTTPS (443-as port).


## <a name="proxy-server-considerations"></a>Proxy server szempontjai
Ha a vállalati hálózati környezet az internet eléréséhez proxykiszolgáló használ, konfigurálja a megfelelő proxybeállításokat használandó önálló üzemeltetett integrációs futásidejű. A proxy állíthatja be a kezdeti regisztráció fázis során.

![Adja meg a proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

Önálló üzemeltetett integrációs futásidejű a proxykiszolgálót segítségével csatlakozik a felhőszolgáltatáshoz. Kattintson a Módosítás hivatkozásra a kezdeti beállítás során. A proxy beállítása párbeszédpanel megjelenik.

![Set proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Három konfigurációs lehetőség áll rendelkezésre:

- **Ne használjon proxyt**: önálló üzemeltetett integrációs futásidejű nem kifejezetten bármelyik proxyt használhatják felhőszolgáltatások való kapcsolódáshoz.
- **Használja a rendszer proxy**: önállóan üzemel integrációs futásidejű használ, hogy a proxy diahost.exe.config és diawp.exe.config van konfigurálva. Ha nincs olyan proxy diahost.exe.config és diawp.exe.config van konfigurálva, önálló üzemeltetett integrációs futásidejű csatlakozik a felhőalapú szolgáltatás közvetlenül a proxy áthaladás nélkül.
- **Egyéni proxyt használ**: beállítás használata önálló üzemeltetett integrációs futásidejű diahost.exe.config és diawp.exe.config konfigurációk használata helyett a HTTP-proxy konfigurálása. Cím és Port is szükséges. Felhasználónév és jelszó megadása nem kötelező, attól függően, hogy a proxy hitelesítési beállítást. Minden beállítás Windows DPAPI-t az önálló üzemeltetett integrációs Runtime titkosítva, és helyileg tárolja a számítógépen.

A frissített proxy beállítások mentését követően automatikusan újraindul az integrációs futásidejű gazdaszolgáltatás.

Miután önálló üzemeltetett integrációs futásidejű sikeresen regisztrálva van, ha azt szeretné, megtekintéséhez, vagy frissíteni a proxykiszolgáló beállításait, használja az integrációs futásidejű Configuration Manager.

1.  Indítsa el **Microsoft integrációs futásidejű a Configuration Manager**.
2.  Váltson a **Settings** (Beállítások) lapra.
3.  Kattintson a **módosítás** hivatkozásra **HTTP-Proxy** elindíthatja a szakasz a **HTTP-Proxy beállítása** párbeszédpanel.
4.  Miután rákattintott a **következő** gomb, megjelenik egy figyelmeztető párbeszédpanel, mentse a proxybeállítást, és indítsa újra az integrációs futásidejű Gazdaszolgáltatást az engedélyt kér.

Megtekintheti és HTTP-proxy frissítse a Configuration Manager eszközzel.

![Nézet proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Ha korábban beállított proxykiszolgálót NTLM-hitelesítéssel, integrációs futásidejű Host szolgáltatás fut a tartományi fiókkal. Ha módosítja a jelszót később a tartományi fiók, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, és ennek megfelelően indítsa újra. Ez a követelmény miatt javasoljuk, hogy dedikált tartományi fiókot, amely nem szükséges a jelszó gyakran frissíteni a proxykiszolgáló eléréséhez használt.

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló-beállításainak konfigurálása

Ha **system proxy használata** önálló üzemeltetett integrációs futásidejű használja a HTTP-proxy beállítása, a proxybeállítást diahost.exe.config és diawp.exe.config. Ha nincs olyan proxy diahost.exe.config és diawp.exe.config van beállítva, önálló üzemeltetett integrációs futásidejű csatlakozik a felhőalapú szolgáltatás közvetlenül a proxy áthaladás nélkül. Az alábbi eljárás ismerteti a diahost.exe.config fájl frissítése.

1. A Fájlkezelőben másolat egy biztonságos C:\Program Files\Microsoft integrációs Runtime\3.0\Shared\diahost.exe.config biztonsági mentése az eredeti fájlt.
2. Indítsa el a Notepad.exe rendszergazdaként fut, és nyissa meg a szöveges fájl "C:\Program Files\Microsoft integrációs Runtime\3.0\Shared\diahost.exe.config. Az alapértelmezett címke található a System.NET névtérbeli az alábbi kódban látható módon:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Proxy kiszolgálóadatok majd adhat hozzá, az alábbi példában látható módon:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    További tulajdonságokat adhatja meg a szükséges beállításokat, például a scriptLocation engedélyezettek a proxy címkén belül. Lásd: [proxy (hálózati beállítások) elem](https://msdn.microsoft.com/library/sa91de1e.aspx) szintaxis.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Mentse a konfigurációs fájlt az eredeti helyre, majd indítsa újra a Self-hosted integrációs futásidejű állomás szolgáltatást, amely átveszi a módosításokat. A szolgáltatás újraindításához: használja a Vezérlőpultról, vagy a szolgáltatások kisalkalmazását a **integrációs futásidejű Configuration Manager** > kattintson a **szolgáltatás leállítása** gombra, majd kattintson a **indítása Szolgáltatás**. A szolgáltatás nem indul el, akkor valószínű, hogy egy érvénytelen XML-címke szintaxissal szerkesztették alkalmazás konfigurációs fájljába hozzá lett adva.

> [!IMPORTANT]
> Ne felejtse el frissíteni a diahost.exe.config és diawp.exe.config is.

A pontok mellett szükség ügyeljen arra, hogy a Microsoft Azure a vállalat engedélyezett. Érvényes Microsoft Azure IP-címek listájának tölthető le: a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>A tűzfal és proxy-kiszolgálóval kapcsolatos problémák lehetséges jelenségek
Ha hibákba ütközik a következő hasonló, valószínű a tűzfalhoz vagy proxyhoz kiszolgáló, amely megakadályozza önálló üzemeltetett integrációs futásidejű csatlakozzon a Data Factory hitelesítse magát a helytelen konfiguráció miatt. Tekintse meg a tűzfalat, hogy az előző szakaszban, és a proxykiszolgáló megfelelően vannak konfigurálva.

1.  Amikor megpróbálja regisztrálni az önálló üzemeltetett integrációs futásidejű, a következő hibaüzenetet kapja: "nem sikerült regisztrálni a integrációs futásidejű csomópont! Győződjön meg arról, hogy a hitelesítési kulcs érvényes-e, és az integrációs szolgáltatások állomás fut ezen a számítógépen. "
2.  Integrációs futásidejű Configuration Manager megnyitásakor látni állapotának "**Disconnected**"vagy"**csatlakozás**". Amikor megtekintik a Windows eseménynaplóiban keresse meg, a "Eseménynapló" > "Alkalmazások és szolgáltatások Logs" > "A Microsoft integrációs futásidejű" hibaüzenetek jelennek meg például a következő hibával:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>Engedélyezze a távelérést intranetről  
Abban az esetben ha **PowerShell** vagy **hitelesítőadat-kezelő alkalmazás** egy másik számítógépről (hálózati) a hitelesítő adatok titkosításához eltérő, ahol az önálló üzemeltetett integrációs futásidejű telepítve van, majd szüksége lesz a **"Távelérési az intranetes"** beállítást engedélyezni kell. Ha futtatja a **PowerShell** vagy **hitelesítőadat-kezelő alkalmazás** , ahol az önálló üzemeltetett integrációs futásidejű telepítve van, majd ugyanazon hitelesítő adatok titkosításához **"távelérés az Intranet "** nem lehet engedélyezni.

Távelérési az intranetes kell **engedélyezett** egy másik csomópont hozzáadása előtt **magas rendelkezésre állás és méretezhetőség**.  

Önálló üzemeltetett integrációs futásidejű a telepítés során (v 3.3.xxxx.x és újabb verziók esetében), alapértelmezés szerint az önálló üzemeltetett integrációs futásidejű telepítési letiltja a **"Távelérési az intranetes"** az önálló üzemeltetett integrációs futásidejű gépen.

Ha egy külső tűzfalat használ, manuálisan megnyithatja a 8060 (vagy a felhasználó konfigurált port). Ha futtatja a tűzfallal kapcsolatos probléma önálló üzemeltetett integrációs futásidejű telepítés közben, próbálja meg a következő paranccsal telepítheti az önálló üzemeltetett integrációs futásidejű a tűzfal konfigurálása.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> **Hitelesítőadat-kezelő alkalmazás** még nem érhető el a ADFv2 hitelesítő adatok titkosításához. Ez a támogatás később adjuk hozzá.  

Ha nem kíván az nyissa meg a portot 8060 az önálló üzemeltetett integrációs futásidejű gépen használatától eltérő szolgáltatások használata a ** hitelesítő adatok beállítása a ** adatok adattárolóhoz használandó hitelesítő adatok konfigurálandó alkalmazáshoz. Például használhatja új-AzureRmDataFactoryV2LinkedServiceEncryptCredential PowerShell-parancsmagot. Tekintse meg, hogyan tárolja az adatokat a hitelesítő adatait a hitelesítő adatok beállítása és biztonsági szakaszban állítható be.


## <a name="next-steps"></a>További lépések
Tekintse meg az alábbi részletes útmutatás: [oktatóanyag: másolás a helyszíni adatok felhőbe](tutorial-hybrid-copy-powershell.md).

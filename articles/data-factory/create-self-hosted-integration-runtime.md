---
title: Saját üzemeltetésű integrációs modul létrehozása az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg a saját üzemeltetésű integrációs modul létrehozása az Azure Data Factoryról, amely lehetővé teszi az adat-előállítók egy magánhálózaton lévő adattárak eléréséhez.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 863cda349ca951bee1c43f09eefd364645de9dbd
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746801"
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Hogyan hozhat létre, és a helyi integrációs modul konfigurálása
Az Integration Runtime (IR) a különböző hálózati környezetekben adatintegrációs képességeket biztosít az Azure Data Factory által használt számítási infrastruktúra áll. Integrációs modul kapcsolatos részletekért lásd: [Integration Runtime áttekintése](concepts-integration-runtime.md).

Egy saját üzemeltetésű integrációs modul képes a másolási tevékenységek futtatása a felhőbeli adatok között a tárolók és a egy adattárban magánhálózat és az átalakítási tevékenységek elleni zahájeno a számítási erőforrások a helyszíni vagy Azure virtuális hálózaton. Saját üzemeltetésű integrációs modul igények telepíthető egy helyszíni gépre vagy magánhálózaton belüli virtuális gépek.  

Ez a dokumentum bemutatja, hogyan hozzon létre és konfigurálhatja a helyi IR.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Telepítse a saját üzemeltetésű integrációs modul magas szintű lépései
1. Hozzon létre egy saját üzemeltetésű integrációs modult. Használhat ADF felhasználói felület létrehozása a saját üzemeltetésű Itt látható egy PowerShell-példa:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2. Töltse le és telepítse a saját üzemeltetésű integration runtime (helyi gépen).
3. Kérje le a hitelesítési kulcsot, és saját üzemeltetésű integrációs modult regisztrálhassa a a kulcsot. Itt látható egy PowerShell-példa:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="setting-up-self-hosted-ir-on-azure-vm-using-azure-resource-manager-template-automatation"></a>Az Azure Resource Manager-sablon (automatation) használata Azure virtuális Gépen lévő saját üzemeltetésű integrációs modul beállítása
Helyi integrációs modul telepítése egy Azure virtuális gép használatával automatizálható [ezen Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Ez itt egy egyszerű módja annak, hogy rendelkezik Azure virtuális hálózaton belül teljes mértékben működő helyi integrációs modul magas Avalaibility és méretezhetőséget biztosít a szolgáltatással (feltéve, meg kell a 2-es vagy újabb csomópontok száma).

## <a name="command-flow-and-data-flow"></a>Parancsot a folyamat és az adatfolyam
Amikor a helyszíni és a felhő között helyezi át az adatokat, a tevékenység használja egy saját üzemeltetésű integrációs modult helyszíni adatforrásból az adatok átvitelét a felhőbe, és fordítva.

Íme egy magas szintű adatfolyamot másolatát a saját üzemeltetésű integrációs modul lépések összefoglalása:

![Áttekintés](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Adatok fejlesztői hoz létre egy saját üzemeltetésű integrációs modul egy Azure data factory egy PowerShell-parancsmaggal belül. Az Azure portal jelenleg nem támogatja ezt a szolgáltatást.
2. Adatok fejlesztői egy helyszíni adattár társított szolgáltatás megadja a saját üzemeltetésű integrációsmodul-példány, amelyet szeretne csatlakozni az adattárak használnia kell hoz létre. A társított szolgáltatás beállításának részeként az adatok fejlesztői hitelesítőadat-kezelő alkalmazást használja (jelenleg nem támogatott) hitelesítési típusok és a hitelesítő adatok beállításához. A Credential manager párbeszédpanel kommunikál az adattár kapcsolati és hitelesítő adatok mentését a saját üzemeltetésű integrációs modul teszteléséhez.
   - Saját üzemeltetésű integration runtime csomópontja a hitelesítő adatok használata a Windows Data Protection alkalmazás alkalmazásprogramozási felületet (DPAPI) titkosítja, és helyben menti. Ha több csomóponton vannak beállítva, a magas rendelkezésre állás, a hitelesítő adatok további szinkronizálódnak, más csomópontok között. Minden egyes csomópont titkosítja a DPAPI-t, és helyileg tárolja azokat. Adatok szinkronizálása a data-fejlesztési átlátható és kezeli a saját üzemeltetésű    
   - A saját üzemeltetésű integrációs modul az ütemezés és a felügyeleti feladatok keresztül kommunikál a Data Factory szolgáltatás **vezérlőcsatorna** , amely egy megosztott az Azure service bus-üzenetsorba használ. Amikor egy tevékenység feladatot kell futtatni, a Data Factory semmilyen hitelesítő információt együtt a kérelem várólistára helyezi (Ha a hitelesítő adatok már nem kerülnek be a saját üzemeltetésű integrációs modul). Saját üzemeltetésű integrációs modul elindít a feladat az üzenetsorba lekérdezés után.
   - Saját üzemeltetésű integrációs modul adatokat másol egy helyszíni adattár, a felhőalapú tárolást, vagy fordítva az adatok folyamatban, a másolási tevékenység konfigurációjától függően. Az ebben a lépésben a saját üzemeltetésű integrációs modul közvetlenül kommunikál a felhőalapú tárolási szolgáltatások az Azure Blob Storage például egy biztonságos csatornán (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Saját üzemeltetésű integrációs modul használatának szempontjai

- Saját üzemeltetésű integrációs több helyszíni adatforrás is használható. Azonban egy **csak egy Azure data Factory egy saját üzemeltetésű integrációs modul kötődik** és a egy másik data factory nem osztható.
- Rendelkezhet **csak egy példányát a saját üzemeltetésű integrációs modul** egyetlen gépen telepítve van. Tegyük fel, a helyszíni adatforrások elérését igénylő két adat-előállítók rendelkezik, saját üzemeltetésű integrációs modul telepítése a két helyszíni számítógépeken kell. Más szóval vannak kötve egy saját üzemeltetésű integrációs modult egy adott adat-előállítót
- A **saját üzemeltetésű integrációs modul nem kell ugyanarra a gépre adatforrásként**. Azonban kellene saját üzemeltetésű integrációs modult az adatforrás közelebb csökkenti az idő a saját üzemeltetésű integrációs modul kapcsolódni az adatforráshoz. Javasoljuk, hogy telepítse a saját üzemeltetésű integrációs modul olyan számítógépen, amelyen eltér a gazdagép a helyszíni adatforrás. Ha a saját üzemeltetésű integrációs modul és az adatforrás különböző gépeken vannak, a saját üzemeltetésű integrációs modul nem versengenek adatforrás erőforrás esetén.
- Rendelkezhet **több saját üzemeltetésű integrációs modulok ugyanazon a helyszíni adatforráshoz csatlakozik, különböző gépeken**. Például előfordulhat, hogy adat-előállítók két kiszolgáló két saját üzemeltetésű integrációs modult, de ugyanazon a helyszíni adatforrás regisztrálva van a mindkét az adat-előállítók.
- Ha már rendelkezik egy átjáró telepítve a számítógépre a(z) egy **Power BI** forgatókönyvben telepítse egy **külön saját üzemeltetésű integrációs modul az Azure Data Factoryhoz** egy másik gépen.
- Saját üzemeltetésű integrációs modul Azure virtuális hálózaton belüli Adatintegráció támogatásához kell használni.
- Forrásként való kezelése az adatforrás egy a helyszíni adatok (azaz a tűzfal mögött található) is használhatja **ExpressRoute**. A saját üzemeltetésű integrációs modul használatával a szolgáltatás és az adatforrás közötti kapcsolatot.
- A saját üzemeltetésű integrációs modult kell használnia, akkor is, ha az adattárban a felhőben található egy **Azure IaaS virtuális gép**.
- Feladatok sikertelenek lehetnek a helyi Integration Runtime telepítve van egy olyan Windows Serveren, amely a FIPS előírásainak megfelelő titkosítás engedélyezve van. A probléma megkerüléséhez, tiltsa le a FIPS-kompatibilis titkosítás a kiszolgálón. FIPS-kompatibilis titkosítás letiltásához módosítsa a következő beállításazonosítót (engedélyezve) 0 (letiltva) 1-től: `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Előfeltételek

- A támogatott **operációs rendszer** verziók a következők Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016-ban. A saját üzemeltetésű integrációs modul telepítése egy **tartományvezérlő nem támogatott**.
- **.NET-keretrendszer 4.6.1-es verzióját vagy újabb** megadása kötelező. Ha saját üzemeltetésű integrációs modult egy Windows 7 gépre telepíti, telepítse a .NET-keretrendszer 4.6.1-es verzióját vagy újabb. Lásd: [.NET-keretrendszer követelményei a System](/dotnet/framework/get-started/system-requirements) részleteiről.
- Az ajánlott **konfigurációs** a saját üzemeltetésű integrációs modul gép legalább 2 GHz-es, 4 mag, 8 GB RAM és 80 GB-os lemezt.
- Ha szeretné a gazdagépen, a saját üzemeltetésű integrációs modul nem válaszol kérelmek. Ezért egy megfelelő energiasémát beállítani a számítógépen a saját üzemeltetésű integrációs modul telepítése előtt. Ha a számítógép hibernált állapotba van konfigurálva, a saját üzemeltetésű integrációs modul telepítése kéri egy üzenetet.
- A gépen telepítheti és konfigurálhatja a saját üzemeltetésű integrációs modul sikeresen rendszergazdának kell lennie.
- Másolási tevékenység-végrehajtás egy adott gyakoriságát fordulhat elő, mert az erőforrás-használat (CPU, memória) a gépen is ugyanazt a mintát követi, csúcs-és üresjárati. Erőforrás-használat is nagyban függ az áthelyezett adatok mennyisége. Amikor több másolási feladat van folyamatban, erőforrás-használat csúcsidőben feljebb görgetünk láthatja.

## <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez
Saját üzemeltetésű integrációs modult telepítheti az MSI-telepítő csomag letöltése az [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Lásd: [adatok áthelyezése a helyszíni és a felhő között a cikk](tutorial-hybrid-copy-powershell.md) részletes útmutatásait.

- A gazdagépen a saját üzemeltetésű integrációs modul energiasémát beállítani, hogy a gép hibernálásra nem. A gazdagép frissítéséből szeretnénk, ha kikapcsolja a a saját üzemeltetésű integrációs modul offline állapotban van.
- Készítsen biztonsági másolatot a saját üzemeltetésű integrációs modul rendszeresen társított hitelesítő adatok.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Telepítés és a helyi integrációs modul regisztrálása a letöltőközpontból

1. Navigáljon a [letöltési oldala a Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kattintson a **letöltése**, válassza ki a megfelelő verziót (**32 bites** vs. **64 bites**), és kattintson a **tovább**.
3. Futtassa a **MSI** közvetlenül, vagy mentse a merevlemezen, és futtassa.
4. Az a **üdvözlő** lapon válassza ki a **nyelvi** kattintson **tovább**.
5. **Fogadja el** a végfelhasználói licencszerződést, majd kattintson **tovább**.
6. Válassza ki **mappa** a saját üzemeltetésű integrációs modul telepítése, és kattintson a **tovább**.
7. Az a **készen áll a telepítésre** kattintson **telepítése**.
8. Kattintson a **Befejezés** telepítésének befejezéséhez.
9. Az Azure PowerShell-lel hitelesítési kulcs beszerzése. PowerShell-példa hitelesítési kulcs beolvasása céljából:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Az a **integrációs modul regisztrálása (saját üzemeltetésű)** oldal a Microsoft Integration Runtime konfigurációkezelőjének a gépen futó, tegye a következőket:
    1. Illessze be a **hitelesítési kulcs** szöveg területén.
    2. Másik lehetőségként kattinthat **megjelenítése a hitelesítési kulcs** , tekintse meg a kulcs szövegét.
    3. Kattintson a **regisztrálása**.


## <a name="high-availability-and-scalability"></a>Magas rendelkezésre állás és méretezhetőség
Egy helyi Integration Runtime több helyszíni gépekhez társíthatók. Ezek a gépek csomópontok nevezzük. Egy helyi Integration Runtime társított legfeljebb négy csomóponttal rendelkezhet. Több csomópont (a telepített átjáró a helyszíni gépek) rendelkező logikai átjáró előnyei a következők:
1. Helyi integrációs modul úgy, hogy már nem az egypontos meghibásodás Big Data megoldást vagy felhőalapú Adatintegráció az Azure Data Factoryt, akár 4 csomóponttal rendelkező folytonosság biztosítása a magasabb rendelkezésre állás.
2. Javult a teljesítmény és az átviteli sebesség a helyszíni és felhő közötti adatátvitel során adattárakban. További információ [teljesítmény összehasonlítások](copy-activity-performance.md).

Több csomópont egyszerűen csak a helyi Integration Runtime szoftver telepítésével társíthatja a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717) , és regisztrálja azt a hitelesítési kulcsok származó egyikével Új AzureRmDataFactoryV2IntegrationRuntimeKey parancsmag leírtak a [oktatóanyag](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Nem kell minden egyes csomópont társíthatunk új helyi integrációs modul létrehozása. A saját üzemeltetésű integrációs modul telepítése egy másik gépen, és ugyanazon hitelesítési kulccsal regisztrálja. 

> [!NOTE]
> Egy másik csomópont hozzáadása előtt **magas rendelkezésre állás és méretezhetőség**, ellenőrizze, hogy **"Az intranetes távoli hozzáférés"** beállítás **engedélyezve** az 1. csomóponton (Microsoft Integration Runtime konfigurációkezelőjének -> Beállítások -> távoli hozzáférést az intranet). 

### <a name="scale-considerations"></a>Méretezési szempontok

#### <a name="scale-out"></a>Horizontális felskálázás

Ha a **a saját üzemeltetésű integrációs modul az elérhető memória pedig kevés** és a **CPU-használata túl magas**, új csomópont hozzáadása segíti a horizontális felskálázást a terhelés gépek között. Ha a tevékenységek időtúllépés miatt sikertelen, vagy saját üzemeltetésű integrációs modul csomópontja offline állapotú, ha csomópontot ad hozzá az átjáró segít.

#### <a name="scale-up"></a>Vertikális felskálázás

A rendelkezésre álló memória és a Processzor nem használhatók jól, de az egyidejű feladatok végrehajtását hamarosan eléri a korlátot, érdemes a vertikális felskálázáshoz, amely képes futni a csomóponton egyidejű feladatok számának növelése. Érdemes azt is, vertikális felskálázás akár, amikor a tevékenységek időtúllépésekbe ütközzenek, mert a saját üzemeltetésű integrációs modul túl van terhelve. Ahogy az az alábbi képen is látható, a maximális kapacitás egy csomópont lehet növelni.  

![](media\create-self-hosted-integration-runtime\scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>A TLS/SSL-tanúsítvány követelményei

Az alábbiakban az integrációsmodul-csomópontot integrációs közötti kommunikáció tehető biztonságossá használt TLS/SSL-tanúsítvány követelményei:

- A tanúsítványnak kell lennie egy nyilvánosan megbízható X509 v3 tanúsítvány. Azt javasoljuk, hogy a nyilvános (külső) hitelesítésszolgáltató (CA) által kiállított tanúsítványokat használ.
- Minden egyes az integration runtime csomópontja meg kell bíznia a tanúsítványt.
- A rendszer támogatja a helyettesítő tanúsítványokat. Ha a tartománynév **node1.domain.contoso.com**, használhatja ***. domain.contoso.com** a tanúsítvány tulajdonos neve.
- A SAN-tanúsítványok használata nem ajánlott, mert a tulajdonos alternatív neveket csak az utolsó elem fogja használni, és minden más figyelmen kívül jelenlegi korlátozás miatt. Például van egy SAN-tanúsítvány, amelynek SAN vannak **node1.domain.contoso.com** és **node2.domain.contoso.com**, ezzel a tanúsítvánnyal csak használhatja a gépet, amelynek FQDN-je **node2.domain.contoso.com**.
- Támogatja az SSL-tanúsítványokra vonatkozó Windows Server 2012 R2 által támogatott bármely kulcsának mérete.
- Tanúsítvány használata a CNG kulcsok nem támogatottak.  

## <a name="sharing-the-self-hosted-integration-runtime-ir-with-multiple-data-factories"></a>A saját üzemeltetésű integrációs modul (IR) osztanak meg több adat-előállítók

Használhat egy meglévő saját üzemeltetésű integrációs modul infrastruktúra, hogy előfordulhat, hogy már rendelkezik a telepítő egy adat-előállítóban. Ez lehetővé teszi, hogy hozzon létre egy **társított saját üzemeltetésű integrációs modul** a másik egy már meglévő hivatkozva factory saját üzemeltetésű IR (megosztott).

#### <a name="terminologies"></a>**Terminológiát**

- **Integrációs modul megosztott** – az eredeti saját üzemeltetésű integrációs modul, amely a fizikai infrastruktúra fut-e.  
- **Integrációs modul társított** – hivatkozó egy másik megosztott integrációs az integrációs modul Ez egy logikai integrációs modul és a egy másik saját üzemeltetésű integrációs modul (megosztott)-infrastruktúrát használja.

#### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Társított saját üzemeltetésű integrációs modul létrehozásának magas szintű lépései

A megosztását a saját üzemeltetésű integrációs modul

1. Az adat-előállítót, amelyben szeretné létrehozni a csatolt bemutathatja engedély megadása 

   ![](media\create-self-hosted-integration-runtime\grant-permissions-IR-sharing.png)

2. Megjegyzés: a **erőforrás-azonosító** a megosztását a saját üzemeltetésű integrációs modul.

   ![](media\create-self-hosted-integration-runtime\4_ResourceID_self-hostedIR.png)

Az adat-Előállítóban, amelyhez az engedélyeket megadta,

3. Hozzon létre egy új helyi integrációs modul (csatolt), és adja meg a fenti **erőforrás-azonosító**

   ![](media\create-self-hosted-integration-runtime\6_create-linkedIR_2.png)

   ![](media\create-self-hosted-integration-runtime\6_create-linkedIR_3.png)

#### <a name="known-limitations-of-self-hosted-ir-sharing"></a>A saját üzemeltetésű integrációs modul megosztási ismert korlátozások

1. Alapértelmezett száma társított integrációs modul egy saját üzemeltetésű integrációs modul alatt létrehozható **20**. Ha szüksége van több majd forduljon az ügyfélszolgálathoz. 

2. Az adat-előállítót, társított integrációs modul van, amelyben a létrehozandó rendelkeznie kell egy olyan MSI Csomaghoz ([felügyeltszolgáltatás-identitás](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)). Alapértelmezés szerint az adat-előállítók Ibiza portálon létrehozott, vagy a PowerShell-parancsmagokat kell MSI implicit módon létrehozva. Azonban bizonyos esetekben létrehozásakor a data factory használatával az Azure Resorce Manager-sablon vagy az SDK-t a "**identitás**" **tulajdonságot be kell állítani** explicit módon az Azure Resorce Manager létrehoz egy adat-előállító biztosítása egy olyan MSI Csomaghoz tartalmazó. 

3. A saját üzemeltetésű integrációs modul verzióját egyenlő vagy nagyobb, mint 3.8.xxxx.xx kell lennie. Adjon [a legújabb verzió letöltéséhez](https://www.microsoft.com/download/details.aspx?id=39717) saját üzemeltetésű integrációs modul

4. Az adat-előállítót, társított integrációs modul van, amelyben a létrehozandó rendelkeznie kell egy olyan MSI Csomaghoz ([felügyeltszolgáltatás-identitás](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)). Alapértelmezés szerint az Ibiza portal vagy a PowerShell-parancsmagokkal létrehozott adat-előállítók MSI lesz ([felügyeltszolgáltatás-identitás](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)).
létrehozott implicit módon, azonban az Azure Resource Manager (ARM) sablon vagy az SDK használatával létrehozott adat-előállítók kell állítani annak érdekében, hogy egy MSI-csomag létrehozása "Identity" tulajdonság.

5. Az ADF .net SDK, amely támogatja ezt a funkciót az verziója > = 1.1.0-s

6. Az Azure PowerShell, amely támogatja ezt a funkciót az verziója > = 6.6.0 (AzureRM.DataFactoryV2 > = 0.5.7)

7. Engedélyt adni a felhasználó "Owner" vagy az adat-előállítót, ahol a megosztott integrációs modul létezik-e a "Tulajdonos" örökölt szerepkör szükséges. 

  > [!NOTE]
  > Ez a funkció csak érhető el az Azure Data Factory 2-es verzió 

## <a name="system-tray-icons-notifications"></a>Rendszer ikony hlavního panelu és értesítések

Ha a rendszer tálcai ikon/értesítési üzenet fölé viszi a kurzort, megtalálhatja a saját üzemeltetésű integrációs modul állapotának részleteit.

![Rendszerértesítésekhez. tálca](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portok és tűzfal
Nincsenek két tűzfal figyelembe kell vennie: **vállalati tűzfal** fut a szervezet a központi útválasztón és **Windows tűzfal** konfigurálva egy démon a helyi számítógépen, ahol a saját üzemeltetésű integrációs modul telepítve van.

![Tűzfal](media\create-self-hosted-integration-runtime\firewall.png)

A **vállalati tűzfal** szint kell konfigurálnia a következő tartományok és a kimenő portokat:

Tartománynevek | Portok | Leírás
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | Adatátviteli szolgáltatás háttérrendszer való kommunikációhoz használatos
*.core.windows.net | 443 | Használja a szakaszos másolás az Azure Blob használatával (Ha be van állítva)
*.frontend.clouddatahub.net | 443 | Adatátviteli szolgáltatás háttérrendszer való kommunikációhoz használatos
download.microsoft.com | 443 | A frissítések letöltéséhez használt

A **Windows tűzfal** szintet (számítógép), ezeket a kimenő portokat általában engedélyezve vannak. Ha nem, konfigurálhatja a tartományok és a portok, ennek megfelelően a saját üzemeltetésű integrációs modult tartalmazó számítógépen.

> [!NOTE]
> Az adatforrás alapján / fogadóként, előfordulhat, hogy az engedélyezési lista további tartományokkal és a kimenő portok tartalmaz a vállalati Windows/tűzfal.
>
> Az egyes felhőalapú adatbázisok (például: Azure SQL Database, az Azure Data Lake, stb.), szükség lehet a tűzfal-konfigurációt a saját üzemeltetésű integrációs modult tartalmazó számítógépen engedélyezett IP-címét.

### <a name="copy-data-from-a-source-to-a-sink"></a>Adatok másolása a forrásból a fogadóba másolt
Győződjön meg arról, hogy a tűzfalszabályok megfelelően engedélyezve vannak a vállalati tűzfalon, a saját üzemeltetésű integrációs modult tartalmazó számítógépen, a Windows tűzfal és az adattároló magát. Ezek a szabályok lehetővé teszi, hogy a saját üzemeltetésű integrációs modul csatlakozni mindkét forrás és fogadó sikeresen. Egyes adattároló, amely részt vesz a másolási művelet szabályok engedélyezése.

Például, hogy a Másolás egy **a helyszíni adatok tárolásához, egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó**, tegye a következőket:

- Engedélyezi a kimenő **TCP** kommunikációs port **1433-as** Windows tűzfal és a vállalati tűzfalon.
- A saját üzemeltetésű integrációs modul gép IP-cím hozzáadása az engedélyezett IP-címek listája az Azure SQL server tűzfal beállításainak konfigurálása.

> [!NOTE]
> Ha a tűzfal nem engedélyezi a 1433-as kimenő porton, saját üzemeltetésű integrációs modul nem érhető el az Azure SQL közvetlenül. Ebben az esetben használhatja [szakaszos Másolás](copy-activity-performance.md) az SQL Azure Database és SQL Azure DW. Ebben a forgatókönyvben csak a adatáthelyezéskor lenne szükség HTTPS (443-as porton).


## <a name="proxy-server-considerations"></a>Proxy server kapcsolatos szempontok
Ha a vállalati hálózati környezet az egy proxykiszolgálón keresztül csatlakozik az internetre, konfigurálja a megfelelő proxykiszolgáló-beállításokat kívánja használni a saját üzemeltetésű integrációs modult. A proxy állíthatja be a kezdeti regisztrációs fázis során.

![Adja meg a proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

Saját üzemeltetésű integrációs modul csatlakozni a felhőszolgáltatáshoz a proxykiszolgálót használ. Kattintson a Módosítás hivatkozásra a kezdeti beállítás során. Láthatja, hogy a proxy beállítása párbeszédpanel.

![Proxy beállítása](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Három konfigurációs lehetőség áll rendelkezésre:

- **Proxy használatának mellőzése**: saját üzemeltetésű integrációs modul nem explicit módon használja bármely proxy cloud serviceshez való csatlakozáshoz.
- **Rendszerproxy használata**: saját üzemeltetésű integrációs modult használja diahost.exe.config és diawp.exe.config beállítás, amely a proxy van konfigurálva. Nincs proxy van konfigurálva a diahost.exe.config és diawp.exe.config, saját üzemeltetésű integrációs modul kapcsolódik közvetlenül a proxy áthaladás nélkül felhőszolgáltatáshoz.
- **Egyéni proxy használatát**: beállítás diahost.exe.config és diawp.exe.config konfigurációk használata helyett a saját üzemeltetésű integrációs modul használata a HTTP-proxy konfigurálása. Cím és Port is szükséges. Felhasználónév és jelszó megadása nem kötelező, attól függően, a proxy hitelesítési beállítást. Minden beállítás a saját üzemeltetésű integrációs modul a Windows DPAPI titkosítva, és a gép helyben tárolja.

Az integration runtime gazdaszolgáltatása automatikusan újraindul, miután mentette a frissített proxybeállításokat.

Miután a saját üzemeltetésű integrációs modul sikeresen regisztrálva lett, ha szeretné megtekintheti vagy frissítheti a webproxy beállításai, használja az Integration Runtime konfigurációkezelőjének.

1. Indítsa el a **Microsoft Integration Runtime konfigurációkezelőjének**.
   - Váltson a **Settings** (Beállítások) lapra.
   - Kattintson a **módosítása** hivatkozásra **HTTP-Proxy** indítsa el a szakasz a **állítsa be a HTTP-Proxy** párbeszédpanel.
   - Miután rákattintott a **tovább** gombra, megjelenik egy figyelmeztető párbeszédpanel, mentse a proxybeállításokat, és indítsa újra az Integration Runtime gazdaszolgáltatása engedélyt kér.

Megtekintheti, és a HTTP-proxy frissítése a Configuration Manager eszközzel.

![Nézet proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Az NTLM-hitelesítés proxykiszolgálót állít be, ha az Integration runtime gazdaszolgáltatása fut a tartományi fiók alatt. Ha módosítja a később a tartományi fiók jelszavát, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, és ennek megfelelően indítsa újra. Ez a követelmény miatt javasoljuk, hogy a proxykiszolgálóhoz, hogy a jelszó frissítése gyakran nem igényel dedikált tartományi fiókot használhatja.

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló-beállításainak konfigurálása

Ha **rendszerproxy használata** saját üzemeltetésű integrációs modult használja a HTTP-proxy beállítása, a proxybeállítást diahost.exe.config és diawp.exe.config. Ha nincs proxy diahost.exe.config és diawp.exe.config van megadva, saját üzemeltetésű integrációs modul felhőszolgáltatásához csatlakozva közvetlenül a proxy áthaladás nélkül. Az alábbi eljárás ismerteti a diahost.exe.config fájl frissítése.

1. A Fájlkezelőben győződjön meg a C:\Program Files\Microsoft integrációs Runtime\3.0\Shared\diahost.exe.config biztonsági mentése az eredeti fájlt egy biztonságos példányát.
2. Indítsa el a Notepad.exe rendszergazdaként futtatja, és nyissa meg a szöveges fájl "C:\Program Files\Microsoft integrációs Runtime\3.0\Shared\diahost.exe.config. Az alapértelmezett címke a system.net található, az alábbi kódban látható módon:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Ezután hozzáadhatja a proxykiszolgáló adatai az alábbi példában látható módon:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    További tulajdonságok, adja meg a szükséges beállításokat, például a scriptLocation engedélyezettek a proxy címkén belül. Lásd: [elem (hálózati beállítások) proxy](https://msdn.microsoft.com/library/sa91de1e.aspx) szintaxis.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. A konfigurációs fájlt az eredeti helyre mentse, majd indítsa újra a helyi Integration Runtime gazdagép szolgáltatást, amely szerzi be a módosításokat. A szolgáltatás újraindításához: használja a Vezérlőpult, vagy a szolgáltatások kisalkalmazás a **Integration Runtime konfigurációkezelőjének** > kattintson a **szolgáltatás leállítása** gombra, majd kattintson a **indítása Szolgáltatás**. Ha a szolgáltatás nem indul el, valószínű, hogy hozzáadta-e egy XML-címke helytelen szintaxis szerkesztettek alkalmazás konfigurációs fájlba.

> [!IMPORTANT]
> Ne felejtse el frissíteni a diahost.exe.config és diawp.exe.config is.

Ezeken a pontokon kívül is szüksége, hogy a Microsoft Azure, amely a vállalat engedélyezési lista. Érvényes Microsoft Azure IP-címek listájának letölthető a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Tűzfal és proxy-kiszolgálóval kapcsolatos problémák lehetséges tünetek
Ha a következő hasonló hibákat észlel, akkor valószínű, a tűzfal vagy proxy kiszolgálóra, amely letiltja a csatlakozzanak a Data Factory saját üzemeltetésű integrációs modul önmaga hitelesítéséhez a helytelen konfiguráció miatt. Tekintse át a tűzfalat, hogy az előző szakaszban, és a proxykiszolgáló megfelelően legyenek konfigurálva.

1. Ha a saját üzemeltetésű integrációs modult regisztrálni próbál, a következő hibaüzenetet kapja: "nem sikerült regisztrálni az Integration Runtime ezen csomópontja! Győződjön meg arról, hogy a hitelesítési kulcs érvényes, és az integrációs szolgáltatási gazdaszolgáltatása fut ezen a gépen. "
   - Integration Runtime konfigurációkezelőjének megnyitásakor látni állapotjelentése "**leválasztott**"vagy"**csatlakozás**". Ha Windows-eseménynaplók, a "Eseménynapló" > "Alkalmazások és szolgáltatások Logs" > "Microsoft Integration Runtime" hibaüzenetek jelennek meg például a következő hiba:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>Az intranetes távoli hozzáférés engedélyezése  
Abban az esetben ha **PowerShell** vagy **hitelesítőadat-kezelő alkalmazás** eltérő, ahol a saját üzemeltetésű integrációs modul telepítve van, majd (a hálózatban) egy másik gépről hitelesítő adatok titkosításához lenne szüksége a **"Távoli hozzáférés intranetről"** beállítást engedélyezni kell. Ha futtatja a **PowerShell** vagy **hitelesítőadat-kezelő alkalmazás** ugyanarra a gépre, ahol a saját üzemeltetésű integrációs modul telepítve van, majd a hitelesítő adatok titkosításához **"távoli hozzáférés az intranetes "** nincs engedélyezve.

Távoli hozzáférés intranetről kell **engedélyezve** egy másik csomópont hozzáadása előtt **magas rendelkezésre állás és méretezhetőség**.  

Saját üzemeltetésű integrációs modul a telepítés során (v 3.3.xxxx.x és újabb verziók esetében), alapértelmezés szerint a saját üzemeltetésű integrációs modul telepítése letiltja a **"Távoli hozzáférés intranetről"** a saját üzemeltetésű integrációs modul gépen.

Ha egy külső tűzfalat használ, manuálisan megnyithatja a 8060 (vagy a felhasználó konfigurált port). Ha tűzfal probléma saját üzemeltetésű integrációs modul telepítése során, próbálja meg a saját üzemeltetésű integrációs modul telepítése nélkül a tűzfal konfigurálásáról a következő parancs használatával.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> **Hitelesítőadat-kezelő alkalmazás** még nem áll rendelkezésre a ADFv2 hitelesítő adatok titkosításához. Később fogja hozzáadni a támogatás.  

Ha nem kíván az nyissa meg a-8060 a saját üzemeltetésű integrációs modul gépen eltérő használatával mechanizmusok használata a ** hitelesítő adatok beállítása ** alkalmazást tároló hitelesítő adatainak konfigurálása. Például használhatja a New-AzureRmDataFactoryV2LinkedServiceEncryptCredential PowerShell-parancsmagot. Tekintse meg, hogyan tárolhatja az adatokat a hitelesítő adatok a hitelesítő adatok beállítása és biztonsági szakaszban beállíthatja.


## <a name="next-steps"></a>További lépések
A következő oktatóanyaggal, a részletes útmutatást lásd: [oktatóanyag: másolási a helyszíni adatok felhőbe](tutorial-hybrid-copy-powershell.md).

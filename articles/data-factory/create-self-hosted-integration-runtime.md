---
title: Saját üzemeltetésű integrációs modul létrehozása a Azure Data Factoryban | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre saját üzemeltetésű integrációs modult Azure Data Factoryban, amely lehetővé teszi az adattárak számára, hogy hozzáférjenek a magánhálózaton lévő adattárokhoz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 8ea6a365b0c7bc6c254c1313445bb54231e161ae
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285648"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul létrehozása és konfigurálása
Az Integration Runtime (IR) az a számítási infrastruktúra, amelyet a Azure Data Factory használ az adatintegrációs képességek biztosítására különböző hálózati környezetekben. Az IR-vel kapcsolatos részletekért lásd: [Integration Runtime – áttekintés](concepts-integration-runtime.md).

A saját üzemeltetésű integrációs modul másolási tevékenységeket futtathat egy felhőalapú adattár és egy magánhálózaton lévő adattár között, és átadhatja az átalakítási tevékenységeket egy helyszíni hálózaton vagy egy Azure-beli virtuális hálózaton lévő számítási erőforrásokkal szemben. A saját üzemeltetésű integrációs modult egy helyszíni gépen vagy egy virtuális gépen (VM) kell telepíteni egy magánhálózati hálózaton belül.  

Ebből a dokumentumból megtudhatja, hogyan hozhat létre és konfigurálhat egy saját üzemeltetésű integrációs modult.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>A saját üzemeltetésű integrációs modul telepítésének magas szintű lépései
1. Hozzon létre egy saját üzemeltetésű integrációs modult. Ehhez a feladathoz használhatja a Azure Data Factory felhasználói felületet. Itt látható egy PowerShell-példa:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) és telepítse a saját üzemeltetésű integrációs modult egy helyi gépre.

3. Kérje le a hitelesítési kulcsot, és regisztrálja a saját üzemeltetésű integrációs modult a kulccsal. Itt látható egy PowerShell-példa:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template"></a>Saját üzemeltetésű IR beállítása Azure-beli virtuális gépen Azure Resource Manager sablon használatával 
[Ezzel a Azure Resource Manager sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)automatizálhatja a saját ÜZEMELTETÉSű IR-telepítőt egy Azure-beli virtuális gépen. Ez a sablon egyszerű módszert kínál a teljes mértékben működőképes, saját üzemeltetésű, magas rendelkezésre állást és méretezhetőségi funkciókat biztosító Azure-beli virtuális hálózatban való működésére (ha a csomópontok száma 2 vagy magasabb).

## <a name="command-flow-and-data-flow"></a>A parancs folyamata és adatfolyama
Ha a helyszíni és a felhő közötti adatáthelyezést végez, a tevékenység egy saját üzemeltetésű integrációs modult használ az adatok átviteléhez egy helyszíni adatforrásból a felhőbe, és fordítva.

Az alábbiakban a saját üzemeltetésű IR-vel történő másolás lépéseinek összegzését ismertető, magas szintű adatfolyam:

![Áttekintés](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Az adatfejlesztő létrehoz egy saját üzemeltetésű integrációs modult egy Azure-beli adatgyárban egy PowerShell-parancsmag használatával. A Azure Portal jelenleg nem támogatja ezt a funkciót.
2. Az adatfejlesztő létrehoz egy társított szolgáltatást egy helyszíni adattárhoz a saját üzemeltetésű Integration Runtime-példány megadásával, amelyet az adattárakhoz való csatlakozáshoz használnia kell.
3. A saját üzemeltetésű Integration Runtime csomópontja titkosítja a hitelesítő adatokat a Windows adatvédelmi alkalmazásprogramozási felületének (DPAPI) használatával, és a hitelesítő adatokat helyileg menti. Ha több csomópont van beállítva a magas rendelkezésre álláshoz, a hitelesítő adatok tovább szinkronizálhatók más csomópontok között. Az egyes csomópontok a DPAPI használatával titkosítják a hitelesítő adatokat, és helyileg tárolják azokat. A hitelesítő adatok szinkronizálása átlátható az adatfejlesztő számára, és a saját üzemeltetésű integrációs modul kezeli.    
4. A Data Factory szolgáltatás a saját üzemeltetésű integrációs modult használja a feladatok ütemezéséhez és kezeléséhez egy olyan *vezérlési csatornán* keresztül, amely megosztott [Azure Service Bus továbbítót](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay)használ. Ha egy tevékenység-feladatnak futnia kell, Data Factory várólistára helyezi a kérelmet a hitelesítő adatokkal együtt (ha a hitelesítő adatok még nincsenek tárolva a saját üzemeltetésű integrációs modulban). A saját üzemeltetésű integrációs modul a várólista lekérdezését követően elindítja a feladatot.
5. A saját üzemeltetésű integrációs modul az adatok egy helyszíni tárolóból a Felhőbeli tárolóba történő másolásával vagy fordítva, attól függően, hogy a másolási tevékenység hogyan lett konfigurálva az adatfolyamatban. Ebben a lépésben a saját üzemeltetésű integrációs modul közvetlenül kommunikál a felhőalapú tárolási szolgáltatásokkal, például az Azure Blob Storage-nal a biztonságos (HTTPS) csatornán keresztül.

## <a name="considerations-for-using-a-self-hosted-ir"></a>A saját üzemeltetésű integrációs modul használatának szempontjai

- Egyetlen saját üzemeltetésű integrációs modul is használható több helyszíni adatforráshoz. Egyetlen saját üzemeltetésű integrációs modul is megosztható egy másik, ugyanazon Azure Active Directory bérlőn belüli adatelőállítóval. További információ: [a saját üzemeltetésű integrációs modul megosztása](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- A saját üzemeltetésű integrációs modulnak csak egy példánya telepíthető egyetlen gépre. Ha két, a helyszíni adatforrásokhoz hozzáférő adatfeldolgozóval rendelkezik, használja a saját üzemeltetésű [IR megosztási szolgáltatást](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories) a saját üzemeltetésű integrációs modul megosztásához, vagy telepítse a saját üzemeltetésű Integration Runtime-t két helyszíni számítógépre, egyet a következőhöz: minden egyes adatfeldolgozó.  
- A saját üzemeltetésű integrációs modulnak nem kell ugyanazon a gépen lennie, mint az adatforrásnak. Azonban a saját üzemeltetésű integrációs modulnak az adatforráshoz való közelítése csökkenti a saját üzemeltetésű integrációs modul az adatforráshoz való csatlakozásának idejét. Azt javasoljuk, hogy a saját üzemeltetésű integrációs modult olyan gépre telepítse, amely eltér a helyszíni adatforrást üzemeltető gépről. Ha a saját üzemeltetésű Integration Runtime és az adatforrás különböző gépeken található, a saját üzemeltetésű integrációs modul nem versenyez az adatforrással rendelkező erőforrások esetében.
- Több saját üzemeltetésű integrációs modult is használhat különböző gépeken, amelyek ugyanahhoz a helyszíni adatforráshoz csatlakoznak. Előfordulhat például, hogy két saját üzemeltetésű integrációs modulja van, amelyek két adat-előállítót szolgáltatnak, de ugyanaz a helyszíni adatforrás is regisztrálva van az adat-előállítók esetében is.
- Ha már telepítve van egy átjáró a számítógépen egy Power BI-forgatókönyv kiszolgálására, telepítsen egy különálló, saját üzemeltetésű integrációs modult a Azure Data Factory egy másik gépen.
- A saját üzemeltetésű integrációs modult az Azure-beli virtuális hálózaton belüli Adatintegráció támogatásához kell használni.
- Az adatforrást olyan helyszíni adatforrásként kezelheti, amely tűzfal mögött található, még akkor is, ha az Azure ExpressRoute-t használja. A saját üzemeltetésű integrációs modul használatával kapcsolatot létesíthet a szolgáltatás és az adatforrás között.
- Akkor is a saját üzemeltetésű integrációs modult kell használnia, ha az adattár a felhőben van egy Azure IaaS virtuális gépen.
- Előfordulhat, hogy a feladatok sikertelenek lehetnek a saját üzemeltetésű integrációs modulban, amely egy olyan Windows-kiszolgálóra van telepítve, amelyen engedélyezve van az FIPS-kompatibilis titkosítás. A probléma megkerüléséhez tiltsa le a FIPS-kompatibilis titkosítást a kiszolgálón. A FIPS-kompatibilis titkosítás letiltásához módosítsa a következő beállításértéket 1 (engedélyezve) értékről 0 (letiltva) értékre: `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Előfeltételek

- A támogatott operációsrendszer-verziók a Windows 7 Service Pack 1, a Windows 8,1, a Windows 10, a Windows Server 2008 R2 SP1, a Windows Server 2012, a Windows Server 2012 R2, a Windows Server 2016 és a Windows Server 2019. A saját üzemeltetésű integrációs modul tartományvezérlőn való telepítése nem támogatott.
- A .NET-keretrendszer 4.6.1-es vagy újabb verziója szükséges. Ha a saját üzemeltetésű integrációs modult egy Windows 7 rendszerű gépen telepíti, telepítse a .NET-keretrendszer 4.6.1-es vagy újabb verzióját. A részletekért lásd a [.NET-keretrendszer rendszerkövetelményeit](/dotnet/framework/get-started/system-requirements) ismertető témakört.
- A saját üzemeltetésű integrációs modul ajánlott konfigurációja legalább 2 GHz, négy mag, 8 GB RAM és egy 80 GB méretű lemez.
- Ha a gazdaszámítógép hibernált állapotba kerül, a saját üzemeltetésű integrációs modul nem válaszol az adatkérésekre. Konfigurálja a megfelelő energiasémát a számítógépen a saját üzemeltetésű integrációs modul telepítése előtt. Ha a gép hibernált állapotra van állítva, a saját üzemeltetésű integrációs modul telepítése üzenetet kér.
- A saját üzemeltetésű integrációs modul sikeres telepítéséhez és konfigurálásához rendszergazdának kell lennie a gépen.
- A másolási tevékenység futtatása egy adott gyakoriságon történik. Az erőforrás-használat (CPU, memória) a gépen ugyanazt a mintát követi, mint a csúcs és az üresjárati idő. Az erőforrás-használat az áthelyezett adatok mennyiségétől függ. Ha több másolási feladat van folyamatban, az erőforrás-használat a csúcs idején jelenik meg.
- Előfordulhat, hogy a feladatok a parketta, az ork vagy a Avro formátumú adatok kinyerése esetén meghiúsulnak. A fájl létrehozása a saját üzemeltetésű integrációs gépen fut, és a következő előfeltételek szükségesek ahhoz, hogy a várt módon működjön (lásd a [parketta formátumát Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)).
    - [Visual C++ 2010 újraterjeszthető](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) csomag (x64)
    - Java Runtime (JRE) 8-as verzió egy JRE-szolgáltatótól, például a [OpenJDK elfogadásával](https://adoptopenjdk.net/), amely biztosítja, hogy a `JAVA_HOME` környezeti változó be legyen állítva.

## <a name="installation-best-practices"></a>Ajánlott eljárások a telepítéshez
A saját üzemeltetésű integrációs modult úgy telepítheti, ha letölt egy MSI-telepítőcsomagot a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717). Részletes útmutatásért lásd: az [adatáthelyezés a helyszíni és a felhőalapú cikk között](tutorial-hybrid-copy-powershell.md) .

- Konfiguráljon egy energiasémát a gazdagépen a saját üzemeltetésű integrációs modulhoz, hogy a gép ne hibernálja. Ha a gazdaszámítógép hibernált állapotba kerül, a saját üzemeltetésű integrációs modul offline állapotba kerül.
- Rendszeresen biztonsági másolatot készíthet a saját üzemeltetésű integrációs modulhoz társított hitelesítő adatokról.
- A saját üzemeltetésű IR-telepítési műveletek automatizálásához tekintse meg az [alábbi szakaszt](#automation-support-for-self-hosted-ir-function).  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Saját üzemeltetésű IR telepítése és regisztrálása a letöltőközpontból

1. Nyissa meg a [Microsoft Integration Runtime letöltési lapját](https://www.microsoft.com/download/details.aspx?id=39717).
2. Válassza a **Letöltés**lehetőséget, válassza ki a 64 bites verziót (a 32 bites verzió nem támogatott), majd kattintson a **tovább**gombra.
3. Futtassa közvetlenül az MSI-fájlt, vagy mentse a merevlemezre, és futtassa.
4. Az **üdvözlőlapon** válassza ki a kívánt nyelvet, majd kattintson a **tovább**gombra.
5. Fogadja el a Microsoft szoftverlicenc-szerződését, és válassza a **tovább**lehetőséget.
6. Válassza a **mappa** lehetőséget a saját üzemeltetésű integrációs modul telepítéséhez, majd kattintson a **Tovább gombra**.
7. A **telepítésre kész** lapon válassza a **telepítés**lehetőséget.
8. A telepítés befejezéséhez kattintson a **Befejezés** gombra.
9. A hitelesítési kulcs beszerzése Azure PowerShell használatával. Íme egy PowerShell-példa a hitelesítési kulcs lekéréséhez:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. A számítógépen futó Microsoft Integration Runtime Configuration Manager **regisztráció Integration Runtime (helyi)** lapján hajtsa végre a következő lépéseket:

    a. Illessze be a hitelesítési kulcsot a szövegmezőbe.

    b. Ha szeretné, válassza a **hitelesítési kulcs megjelenítése** lehetőséget a kulcs szövegének megtekintéséhez.

    c. Kattintson a **Register** (Regisztrálás) elemre.

## <a name="automation-support-for-self-hosted-ir-function"></a>Automatizálási támogatás a saját üzemeltetésű IR-függvények számára


> [!NOTE]
> Ha a saját üzemeltetésű integrációs modult egy Azure-beli virtuális gépen szeretné beállítani, és Azure Resource Manager-sablonok használatával szeretné automatizálni a telepítést, tekintse át a [szakaszt](#setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template).

A meglévő, saját üzemeltetésű integrációs modul beállításához vagy kezeléséhez a parancssort használhatja. Ezt különösen a telepítés automatizálására, a saját üzemeltetésű IR-csomópontok regisztrálására lehet használni. 

A **Dmgcmd. exe** a saját üzemeltetésű telepítés része, amely általában a C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ mappában található. Ez a művelet különböző paramétereket támogat, és parancssoron keresztül hívható meg az automatizáláshoz használható batch-parancsfájlok használatával. 

*Használati* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Részletek (paraméterek/tulajdonság):* 

| Tulajdonság                                                    | Leírás                                                  | Szükséges |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Integration Runtime (helyi) csomópont regisztrálása a megadott hitelesítési kulccsal | Nem       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | A távelérés engedélyezése az aktuális csomóponton magas rendelkezésre állású fürt beállításához és/vagy a hitelesítő adatok beállításának engedélyezése közvetlenül a saját üzemeltetésű integrációs modul (az ADF szolgáltatás használata **nélkül) New-AzDataFactoryV2LinkedServiceEncryptedCredential** parancsmag egy távoli gépről ugyanazon a hálózaton. | Nem       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | Távoli hozzáférés engedélyezése az aktuális csomóponthoz, ha a csomópont a tárolóban fut | Nem       |
| DisableRemoteAccess                                         | Távoli hozzáférés letiltása az aktuális csomóponthoz. A többcsomópontos telepítéshez távoli hozzáférés szükséges. A New-**AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell-parancsmag akkor is működik, ha a távoli hozzáférés le van tiltva, ha ugyanazon a gépen fut, mint a saját ÜZEMELTETÉSű IR-csomópont. | Nem       |
| Kulcs "`<AuthenticationKey>`"                                 | Az előző hitelesítési kulcs felülírása/frissítése. Körültekintően járjon el, mivel ez azt eredményezheti, hogy a korábbi saját üzemeltetésű IR-csomópont offline állapotba kerül, ha a kulcs egy új Integration Runtime. | Nem       |
| GenerateBackupFile "`<filePath>`" "`<password>`"            | Biztonságimásolat-fájl létrehozása az aktuális csomóponthoz, a biztonságimásolat-fájl tartalmazza a csomópont kulcsát és az adattár hitelesítő adatait | Nem       |
| ImportBackupFile "`<filePath>`" "`<password>`"              | A csomópont visszaállítása biztonságimásolat-fájlból                          | Nem       |
| Újraindítás                                                     | A Integration Runtime (helyi) gazdagép szolgáltatás újraindítása   | Nem       |
| Kezdés                                                       | A Integration Runtime (helyi) gazdagép szolgáltatás elindítása     | Nem       |
| Leállítás                                                        | Integration Runtime (helyi) frissítési szolgáltatás leállítása        | Nem       |
| StartUpgradeService                                         | Integration Runtime (helyi) frissítési szolgáltatás elindítása       | Nem       |
| StopUpgradeService                                          | Integration Runtime (helyi) frissítési szolgáltatás leállítása        | Nem       |
| TurnOnAutoUpdate                                            | Integration Runtime (helyi) automatikus frissítés bekapcsolása        | Nem       |
| TurnOffAutoUpdate                                           | Integration Runtime (helyi) automatikus frissítés kikapcsolása       | Nem       |
| SwitchServiceAccount "< tartomány \ felhasználó >" ["password"]           | Állítsa be úgy a DIAHostService, hogy az új fiókként fusson. Üres jelszó ("") használata a rendszerfiókhoz vagy a virtuális fiókhoz | Nem       |
| Naplózási szint @no__t – 0                                       | ETW naplózási szintjének beállítása (kikapcsolva, hiba, részletes vagy mind). A hibakeresés során általában a Microsoft ügyfélszolgálata használja. | Nem       |

   


## <a name="high-availability-and-scalability"></a>Magas rendelkezésre állás és méretezhetőség
A saját üzemeltetésű integrációs modul több helyszíni géppel vagy Virtual Machines az Azure-ban is társítható. Ezeket a gépeket csomópontoknak nevezzük. Legfeljebb négy, saját üzemeltetésű integrációs modulhoz társított csomópontot lehet hozzárendelni. A logikai átjárók számára a következő előnyökkel jár, ha több csomópontot (helyszíni gépeket telepítettek egy átjáróval):
* A saját üzemeltetésű integrációs modul magasabb rendelkezésre állása annak érdekében, hogy a big data-megoldás vagy a Felhőbeli Adatintegráció a Azure Data Factoryával többé ne legyen sikertelen, a folytonosságot akár négy csomóponttal is biztosíthatja.
* Jobb teljesítmény és teljesítmény a helyszíni és a Felhőbeli adattárak közötti adatáthelyezés során. További információ a [teljesítmény-összehasonlításokról](copy-activity-performance.md).

Több csomópontot úgy is hozzárendelhet, ha telepíti a saját üzemeltetésű Integration Runtime szoftvert a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717). Ezt követően regisztrálja azt a **New-AzDataFactoryV2IntegrationRuntimeKey** parancsmagból beszerzett hitelesítési kulcsok valamelyikével, az [oktatóanyagban](tutorial-hybrid-copy-powershell.md)leírtak szerint.

> [!NOTE]
> Nem kell létrehoznia új, saját üzemeltetésű integrációs modult az egyes csomópontok társításához. Telepítheti a saját üzemeltetésű integrációs modult egy másik gépre, és ugyanazzal a hitelesítési kulccsal regisztrálhatja azt. 

> [!NOTE]
> Mielőtt újabb csomópontot ad hozzá a magas rendelkezésre álláshoz és a méretezhetőséghez, győződjön meg arról, hogy az első csomóponton engedélyezve van az **intranetes távelérés** beállítás (**Microsoft Integration Runtime Configuration Manager** > **Beállítások** >  **Távoli hozzáférés az intranethez**). 

### <a name="scale-considerations"></a>Méretezési szempontok

#### <a name="scale-out"></a>Horizontális felskálázás

Ha a saját üzemeltetésű integrációs modul rendelkezésre álló memóriája alacsony, és a CPU-használat magas, egy új csomópont hozzáadásával kibővítheti a terhelést a gépek között. Ha a tevékenységek sikertelenek, mert időtúllépést okoznak, vagy mert a saját üzemeltetésű IR-csomópont offline állapotban van, akkor segít, ha csomópontot ad hozzá az átjáróhoz.

#### <a name="scale-up"></a>Vertikális felskálázás

Ha a rendelkezésre álló memória és a CPU nem jól működik, de az egyidejű feladatok végrehajtása eléri a korlátot, akkor a csomóponton futtatható egyidejű feladatok számának növelésével bővíteni kell. Előfordulhat, hogy vertikális felskálázásra van szükség, ha a tevékenységek időtúllépés miatt megtörténik, mert a saját üzemeltetésű integrációs modul túlterhelt. Ahogy az az alábbi ábrán is látható, növelheti a csomópontok maximális kapacitását:  

![Csomóponton futtatható egyidejű feladatok növelése](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL-tanúsítványokra vonatkozó követelmények

Az Integration Runtime-csomópontok közötti kommunikáció biztonságossá tételéhez használt TLS/SSL-tanúsítványra vonatkozó követelmények a következők:

- A tanúsítványnak nyilvánosan megbízható X509 v3 tanúsítványnak kell lennie. Javasoljuk, hogy használjon nyilvános (partneri) hitelesítésszolgáltató (CA) által kiadott tanúsítványokat.
- Minden Integration Runtime csomópontnak meg kell bíznia a tanúsítványban.
- Nem javasoljuk a tulajdonos alternatív neve (SAN) tanúsítványait, mert csak a legutóbbi SAN-elemek lesznek használatban, és az összes többi figyelmen kívül lesz hagyva a jelenlegi korlátozások miatt. Ha például egy SAN-tanúsítvánnyal rendelkezik, amelynek a **node1.domain.contoso.com** és a **node2.domain.contoso.com**, akkor ezt a tanúsítványt csak olyan gépen használhatja, amelynek teljes tartományneve **node2.domain.contoso.com**.
- A tanúsítvány támogatja a Windows Server 2012 R2 által támogatott összes kulcs méretét az SSL-tanúsítványokhoz.
- A CNG-kulcsokat használó tanúsítványok nem támogatottak.  

> [!NOTE]
> Ezzel a tanúsítvánnyal titkosítható a portok a saját üzemeltetésű IR-csomópontokon a **csomópontok közötti kommunikációhoz** (az állapot-szinkronizáláshoz, amely tartalmazza a társított szolgáltatások hitelesítő adatait a csomópontok között), és **a PowerShell-parancsmagot is használhatja a következőhöz: a társított szolgáltatás hitelesítő adatainak beállítása** a helyi hálózaton belülről. Azt javasoljuk, hogy ezt a tanúsítványt használja, ha a magánhálózati környezet nem biztonságos, vagy ha a magánhálózaton belüli csomópontok közötti kommunikációt is szeretné védeni. A saját üzemeltetésű integrációs modulból más adattárba irányuló adatáthelyezés mindig titkosított csatornán történik, függetlenül attól, hogy ez a tanúsítvány be van-e állítva. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>A saját üzemeltetésű integrációs modul megosztása több adatgyárral

Újra felhasználhatja a meglévő, saját üzemeltetésű integrációs modul infrastruktúráját, amelyet már beállított egy adatelőállítóban. Ez lehetővé teszi, hogy egy másik adatgyárban hozzon létre egy *társított saját üzemeltetésű integrációs* modult egy meglévő, saját üzemeltetésű (megosztott) IR-re hivatkozva.

Ha saját üzemeltetésű integrációs modult szeretne megosztani a PowerShell használatával, tekintse meg [a megosztott saját üzemeltetésű integrációs modul létrehozása a PowerShell-lel Azure Data Factory](create-shared-self-hosted-integration-runtime-powershell.md)című témakört.

A szolgáltatás tizenkét perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Szakkifejezések

- **Megosztott IR**: az eredeti, saját üzemeltetésű, fizikai infrastruktúrán futó IR.  
- **Csatolt IR**: az IR, amely egy másik megosztott IR-re hivatkozik. Ez egy logikai IR, és egy másik, saját üzemeltetésű IR (Shared) infrastruktúrát használ.

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>A társított saját üzemeltetésű integrációs modul létrehozásának magas szintű lépései

1. A saját üzemeltetésű integrációs modulban adja meg azt az adat-előállítót, amelyben létre kívánja hozni a társított IR-t. 

   ![Engedélyek megadására szolgáló gomb a megosztás lapon](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Engedélyek hozzárendelésének kiválasztása](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Jegyezze fel a megosztott saját üzemeltetésű integrációs modul erőforrás-AZONOSÍTÓját.

   ![Az erőforrás-azonosító helye](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. Hozzon létre egy új, saját üzemeltetésű IR-t (csatolt) az adatelőállítóban, és adja meg az erőforrás-azonosítót.

   ![A társított saját üzemeltetésű integrációs modul létrehozásának gombja](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Név és erőforrás-azonosító mezői](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Monitoring 

- **Megosztott IR**

  ![Megosztott integrációs modul keresésének kiválasztása](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Figyelésre szolgáló lap](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Csatolt IR**

  ![A társított integrációs modul keresésének kiválasztása](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Figyelésre szolgáló lap](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>A saját üzemeltetésű IR-megosztás ismert korlátai

* Az az adatelőállító, amelyben létre kell hozni egy társított IR-t, [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)-vel kell rendelkeznie. Alapértelmezés szerint a Azure Portal-vagy PowerShell-parancsmagokban létrehozott adatfeldolgozók implicit módon létrehozták az MSI-t. Ha azonban egy Azure Resource Manager sablonon vagy SDK-n keresztül hoznak létre egy adatelőállítót, explicit módon be kell állítani az **Identity** tulajdonságot annak biztosítására, hogy a Azure Resource Manager egy MSI-t tartalmazó adatelőállítót hozzon létre. 

* A szolgáltatást támogató Azure Data Factory .NET SDK a 1.1.0 vagy újabb verzió.

* Az engedély megadásához a felhasználónak szüksége van a tulajdonos szerepkörre vagy az örökölt tulajdonosi szerepkörre abban az adat-előállítóban, ahol a megosztott IR létezik.

* A megosztási funkció csak az azonos Azure Active Directory bérlőn belüli adatfeldolgozók esetében működik.

* Active Directory [vendég felhasználók](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)számára a felhasználói felületen [nem működik](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits)a keresési funkció (az összes adat-előállítót egy keresési kulcsszó használatával listázva). Ha azonban a vendég felhasználó az adat-előállító tulajdonosa, akkor a keresési funkció nélkül is megoszthatja az IR-t, ha közvetlenül beírja annak az adat-előállítónak az MSI-fájlját, amelyhez az IR-t meg kell osztani az **engedély kiosztása** szövegmezőben, majd a **Hozzáadás** elemet kell választania. a Azure Data Factory felhasználói felületen. 

  > [!NOTE]
  > Ez a funkció csak Azure Data Factory v2-ben érhető el. 

## <a name="notification-area-icons-and-notifications"></a>Értesítési területek ikonjai és értesítései

Ha az egérmutatót az értesítési területen lévő ikonra vagy üzenet fölé helyezi, a saját üzemeltetésű integrációs modul állapotával kapcsolatban talál részleteket.

![Értesítések az értesítési területeken](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portok és tűzfal
Két tűzfalat kell figyelembe venni: a szervezet központi útválasztóján futó *vállalati tűzfalat* és a *Windows tűzfal* démonként konfigurált a helyi gépen, ahol a saját üzemeltetésű integrációs modul telepítve van.

![Tűzfal](media/create-self-hosted-integration-runtime/firewall.png)

A *vállalati tűzfal* szintjén a következő tartományokat és kimenő portokat kell konfigurálnia:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]


A *Windows tűzfal* szintjén (gépi szint) Ezek a kimenő portok általában engedélyezve vannak. Ha nem, akkor a tartományokat és a portokat a saját üzemeltetésű integrációs modul számítógépén megfelelően konfigurálhatja.

> [!NOTE]
> A forrás-és a elsüllyedés alapján előfordulhat, hogy további tartományokat és kimenő portokat kell engedélyeznie a vállalati tűzfalon vagy a Windows tűzfalon.
>
> Egyes felhőalapú adatbázisok (például Azure SQL Database és Azure Data Lake) esetében előfordulhat, hogy engedélyeznie kell a saját üzemeltetésű Integration Runtime-gépek IP-címeit a tűzfal konfigurációjában.

### <a name="copy-data-from-a-source-to-a-sink"></a>Adatok másolása forrásból a fogadóba
Győződjön meg arról, hogy a tűzfalszabályok megfelelően engedélyezve vannak a vállalati tűzfalon, a Windows tűzfalon a saját üzemeltetésű Integration Runtime gépen, valamint magát az adattárat. Ezeknek a szabályoknak a engedélyezése lehetővé teszi, hogy a saját üzemeltetésű integrációs modul sikeresen kapcsolódjon a forráshoz és a fogadóhoz. Engedélyezze a szabályokat a másolási műveletben érintett összes adattárhoz.

Ha például egy helyszíni adattárból egy Azure SQL Database fogadóba vagy egy Azure SQL Data Warehouse fogadóba szeretne másolni, hajtsa végre a következő lépéseket:

1. Engedélyezze a kimenő TCP-kommunikációt az 1433-as porton a Windows tűzfal és a vállalati tűzfal esetében is.
2. Az Azure SQL Database tűzfalbeállítások konfigurálásával adja hozzá a saját üzemeltetésű Integration Runtime-gép IP-címét az engedélyezett IP-címek listájához.

> [!NOTE]
> Ha a tűzfal nem engedélyezi a 1433-es kimenő portot, a saját üzemeltetésű integrációs modul nem fér hozzá közvetlenül az Azure SQL Database-hez. Ebben az esetben egy [szakaszos másolatot](copy-activity-performance.md) használhat Azure SQL Database és Azure SQL Data Warehouse. Ebben az esetben csak HTTPS (443-es port) szükséges az adatáthelyezéshez.


## <a name="proxy-server-considerations"></a>A proxykiszolgáló szempontjai
Ha a vállalati hálózati környezet proxykiszolgálót használ az Internet eléréséhez, konfigurálja a saját üzemeltetésű integrációs modult a megfelelő proxybeállítások használatára. A proxyt a kezdeti regisztrációs fázisban állíthatja be.

![Proxy meghatározása](media/create-self-hosted-integration-runtime/specify-proxy.png)

Ha be van állítva, a saját üzemeltetésű integrációs modul a proxykiszolgálót használva csatlakozik a Cloud Service-hez, a forráshoz/célhoz (HTTP/HTTPS protokollt használó). A kezdeti beállításnál válassza a **módosítás hivatkozás** lehetőséget. Megjelenik a proxy beállítás párbeszédpanel.

![Proxy beállítása](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Három konfigurációs lehetőség közül választhat:

- Ne **használja a proxyt**: a saját üzemeltetésű Integration Runtime nem használ explicit módon semmilyen proxyt a Cloud Serviceshez való csatlakozáshoz.
- A **rendszerproxy használata**: a saját üzemeltetésű integrációs modul a diahost. exe. config és a diawp. exe. config fájlban konfigurált proxy-beállítást használja. Ha nincs proxy konfigurálva a diahost. exe. config és a diawp. exe. config fájlban, a saját üzemeltetésű integrációs modul közvetlenül a proxyn keresztül csatlakozik a Cloud Service-hez.
- **Egyéni proxy használata**: KONFIGURÁLJA a http-proxy beállítását a saját üzemeltetésű integrációs modulhoz a diahost. exe. config és a diawp. exe. config fájlban található konfigurációk használata helyett. A **címnek** és a **portnak** kötelező megadni. A proxy hitelesítési beállításától függően a **Felhasználónév** és a **jelszó** nem kötelező. Az összes beállítás titkosítása a Windows DPAPI a saját üzemeltetésű integrációs modulban és helyileg, a gépen történik.

Az Integration Runtime Host szolgáltatás automatikusan újraindul a frissített proxybeállítások mentése után.

Ha a saját üzemeltetésű integrációs modult sikeresen regisztrálta, a proxybeállítások megtekintéséhez vagy frissítéséhez használja a Integration Runtime Configuration Manager.

1. Nyissa meg **Microsoft Integration Runtime Configuration Manager**.
2. Váltson a **Settings** (Beállítások) lapra.
3. Válassza a **módosítás** hivatkozást a **http-proxy** szakaszban a http- **proxy beállítása** párbeszédpanel megnyitásához.
4. Kattintson a **Tovább** gombra. Ekkor megjelenik egy figyelmeztetés, amely arra kéri, hogy mentse a proxybeállításokat, és indítsa újra az Integration Runtime Host szolgáltatást.

A HTTP-proxyt a Configuration Manager eszköz használatával tekintheti meg és frissítheti.

![Proxy megtekintése](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Ha NTLM-hitelesítést használó proxykiszolgálót állít be, az Integration Runtime Host szolgáltatás a tartományi fiók alatt fut. Ha később módosítja a tartományi fiók jelszavát, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, majd indítsa újra. Ennek a követelménynek a használata miatt javasoljuk, hogy egy dedikált tartományi fiókot használjon a proxykiszolgáló eléréséhez, amely nem igényli a jelszó gyakori frissítését.

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló beállításainak konfigurálása

Ha bejelöli a **rendszerproxy használata** a http-proxyhoz beállítást, a saját üzemeltetésű integrációs modul a diahost. exe. config és a diawp. exe. config fájlban lévő proxy beállítást használja. Ha nincs megadva proxy a diahost. exe. config és a diawp. exe. config fájlban, a saját üzemeltetésű integrációs modul közvetlenül a proxyn keresztül csatlakozik a Cloud Service-hez. Az alábbi eljárás útmutatást nyújt a diahost. exe. config fájl frissítéséhez:

1. A Fájlkezelőben hozzon létre egy biztonságos másolatot a C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config az eredeti fájl biztonsági mentéséhez.
2. Nyissa meg a Notepad. exe alkalmazást rendszergazdaként, és nyissa meg a C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. szövegfájlt. Keresse meg a system.net alapértelmezett címkéjét az alábbi kódban látható módon:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Ezután a következő példában látható módon adhatja hozzá a proxykiszolgáló adatait:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    A proxy címkén belül további tulajdonságok is megadhatók a szükséges beállítások (például `scriptLocation`) megadásához. A szintaxishoz lásd: [proxy elem (hálózati beállítások)](https://msdn.microsoft.com/library/sa91de1e.aspx) .

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Mentse a konfigurációs fájlt az eredeti helyre. Ezután indítsa újra a saját üzemeltetésű Integration Runtime Host szolgáltatást, amely felveszi a módosításokat. 

   A szolgáltatás újraindításához használja a Vezérlőpulton a szolgáltatások kisalkalmazást. Vagy Integration Runtime Configuration Manager kattintson a **szolgáltatás leállítása** gombra, majd válassza a **szolgáltatás indítása**lehetőséget. 
   
   Ha a szolgáltatás nem indul el, valószínűleg helytelen XML-címke-szintaxis lett hozzáadva a szerkesztett alkalmazás konfigurációs fájljához.

> [!IMPORTANT]
> Ne felejtse el frissíteni a diahost. exe. config és a diawp. exe. config fájlt is.

Emellett meg kell győződnie arról, hogy Microsoft Azure a vállalat engedélyezési listájában van. Az érvényes Microsoft Azure IP-címek listáját a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=41653)töltheti le.

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>A tűzfal és a proxykiszolgáló szolgáltatással kapcsolatos problémák lehetséges tünetei
Ha a következőhöz hasonló hibák fordulnak elő, valószínűleg a tűzfal vagy a proxykiszolgáló helytelen konfigurációja miatt következik be, amely blokkolja a saját üzemeltetésű integrációs modul csatlakozását a Data Factoryhoz a hitelesítéshez. A tűzfal és a proxykiszolgáló megfelelő konfigurálásának biztosításához tekintse meg az előző szakaszt.

* Amikor megpróbálja regisztrálni a saját üzemeltetésű integrációs modult, a következő hibaüzenet jelenik meg: "nem sikerült regisztrálni ezt a Integration Runtime csomópontot! Győződjön meg arról, hogy a hitelesítési kulcs érvényes, és az Integration Service Host szolgáltatás fut ezen a gépen. "
* A Integration Runtime Configuration Manager megnyitásakor a **leválasztott** vagy a **Csatlakozás**állapot jelenik meg. Ha a Windows-eseménynaplókat tekinti meg, akkor a **Eseménynapló** > **alkalmazás és szolgáltatások naplójában** > **Microsoft Integration Runtime**jelenik meg, ehhez hasonló hibaüzenetek jelennek meg:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Távoli hozzáférés engedélyezése intranetről  
Ha a PowerShell használatával titkosítja a hitelesítő adatokat a saját üzemeltetésű integrációs modult futtató másik gépről (a hálózatban), akkor engedélyezheti a **távelérést az intranetről** lehetőséggel. Ha a PowerShellt a hitelesítő adatoknak a saját üzemeltetésű integrációs modult tartalmazó gépen való titkosítására futtatja, akkor nem engedélyezheti a **távelérést az intranetről**.

Mielőtt újabb csomópontot hozna létre a magas rendelkezésre állás és a méretezhetőség érdekében, engedélyeznie kell **a távelérést az intranetről** .  

A saját üzemeltetésű integrációs modul telepítője (3.3. xxxx. x) során alapértelmezés szerint a saját üzemeltetésű integrációs modul telepítése letiltja a **távelérést az intranetről** a saját üzemeltetésű Integration Runtime-gépen.

Ha külső gyártótól származó tűzfalat használ, a 8060-as portot (vagy a felhasználó által konfigurált portot) manuálisan is megnyithatja. Ha tűzfallal kapcsolatos probléma merül fel a saját üzemeltetésű integrációs modul beállítása során, próbálja meg a következő paranccsal telepíteni a saját üzemeltetésű integrációs modult a tűzfal konfigurálása nélkül:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Ha úgy dönt, hogy nem nyitja meg a 8060-as portot a saját üzemeltetésű integrációs modulban, használja a hitelesítő adatok beállítása az adattároló hitelesítő adatainak beállítására szolgáló mechanizmusokat. Használhatja például a **New-AzDataFactoryV2LinkedServiceEncryptCredential PowerShell-** parancsmagot.


## <a name="next-steps"></a>Következő lépések
Az alábbi oktatóanyag részletes útmutatást nyújt: [oktatóanyag: helyszíni információk másolása a felhőbe](tutorial-hybrid-copy-powershell.md).

---
title: Saját üzemeltetésű Integration Runtime létrehozása
description: Ismerje meg, hogyan hozhat létre saját üzemeltetésű integrációs futásidejűt az Azure Data Factoryban, amely lehetővé teszi az adatgyárak számára a magánhálózat adattáraihoz való hozzáférést.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 03/13/2020
ms.openlocfilehash: 6bc0f002c6927cfd9a314797663e1dabbac392b6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416641"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Helyi integrációs modul létrehozása és konfigurálása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az integrációs futásidejű (IR) az a számítási infrastruktúra, amelyet az Azure Data Factory a különböző hálózati környezetekben az adatintegrációs képességek biztosításához használ. Az integrációs adatokkal kapcsolatos részletek az [Integráció sidőszakának áttekintése](concepts-integration-runtime.md)című témakörben találhatók.

A saját üzemeltetésű integrációs futásidejű futtathatja a másolási tevékenységeket egy felhőbeli adattár és egy magánhálózat ban lévő adattár között. Emellett a helyszíni hálózat vagy egy Azure virtuális hálózat számítási erőforrásaival való átalakítási tevékenységeket is feladhat. A telepítés egy saját üzemeltetésű integrációs futásidejű szüksége van egy helyszíni gép vagy egy virtuális gép egy magánhálózaton belül.  

Ez a cikk azt ismerteti, hogyan hozhat létre és konfigurálhat saját üzemeltetésű infravörös kapcsolatokat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs futásidő beállítása

Saját üzemeltetésű integrációs futásidejű létrehozása és beállítása, kövesse az alábbi eljárásokat.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Saját üzemeltetésű infravörös kapcsolat létrehozása az Azure PowerShell használatával

1. Ehhez a feladathoz használhatja az Azure PowerShellt. Például:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) és telepítse az önkiszolgáló integrációs futásidejű helyi számítógépen.

3. A hitelesítési kulcs lekérése és az önkiszolgáló integrációs futásidő regisztrálása a kulccsal. Íme egy PowerShell-példa:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Saját üzemeltetésű infravörös kapcsolat létrehozása az Azure Data Factory felhasználói felületén keresztül

Az alábbi lépésekkel hozzon létre egy saját üzemeltetésű infravörös azure Data Factory felhasználói felülethasználatával.

1. Az Azure Data Factory felhasználói felületének **Get's get s get started (Lépések)** lapján válassza a **Szerző** lapot a bal szélső ablaktáblán.

   ![A kezdőlap Szerző gombja](media/doc-common-process/get-started-page-author-button.png)

1. Válassza a **Kapcsolatok** lehetőséget a bal szélső ablaktábla alján, majd a **Kapcsolatok** ablakban válassza az **Integráció safuválasztót.** Válassza **a +Új**lehetőséget.

   ![Integrációs modul létrehozása](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. Az **Integrációs futásidejű beállítási** lapon válassza az **Azure, Self-Hosted**( Integrációs futásidejű beállítási lap) lehetőséget, majd a **Continue (Folytatás)** lehetőséget. 

1. A következő lapon válassza a **Saját üzemeltetésű** infravörös szolgáltatás létrehozásához válassza a Saját üzemeltetésű infravörös kapcsolat választógombot, majd kattintson a **Folytatás gombra.**
   ![Saját üzemeltetett infravörös kapcsolat létrehozása](media/create-self-hosted-integration-runtime/new-selfhosted-ir.png)

1. Adja meg az infravörös kapcsolat nevét, és válassza a **Létrehozás gombot.**

1. Az **Integrációs futásidejű beállítás** lapon válassza az **1.** Vagy kövesse a **2.** A következő utasítások a kézi beállításon alapulnak:

   ![Integrációs modul telepítése](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Másolja és illessze be a hitelesítési kulcsot. Válassza **az Integrációs futásidő letöltése és telepítése lehetőséget.**

    1. Töltse le a saját üzemeltetésű integrációs modult egy helyi windowsos gépre. Indítsa el a telepítőt.

    1. Az **Integrációs futásidő regisztrálása (saját üzemeltetésű)** lapon illessze be a korábban mentett kulcsot, és válassza a **Regisztráció**lehetőséget.
    
       ![Az integrációs modul regisztrálása](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Az **Új integrációs futásidejű (saját üzemeltetésű) csomópont** lapon válassza a **Befejezés**lehetőséget.

1. A saját üzemeltetésű integrációs futásidő sikeres regisztrálása után a következő ablak jelenik meg:

    ![Sikeres regisztráció](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Saját üzemeltetésű infravörös szolgáltatás beállítása Azure-beli virtuális gépen egy Azure Resource Manager-sablonon keresztül

Automatizálhatja a saját üzemeltetésű infravörös beállításokat egy Azure virtuális gépen az [Önkiszolgáló infravörös sablon létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)segítségével. A sablon egy egyszerű módja annak, hogy egy teljesen működőképes, saját üzemeltetésű infravörös egy Azure virtuális hálózaton belül. Az infravörös magas rendelkezésre állású és méretezhetőségi funkciókkal rendelkezik, feltéve, hogy a csomópontszámot 2-re vagy magasabbra állítja.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Meglévő saját üzemeltetésű infravörös kapcsolat beállítása a helyi PowerShellen keresztül

A parancssor segítségével meglévő, saját üzemeltetésű infravörös kapcsolatra is beállítható vagy kezelhető. Ez a használat különösen segíthet a saját üzemeltetésű infravörös csomópontok telepítésének és regisztrálásának automatizálásában.

A Dmgcmd.exe fájl megtalálható az önkiszolgáló telepítőben. Általában a C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ mappában található. Ez az alkalmazás támogatja a különböző paramétereket, és meglehet hívni egy parancssorból kötegelt parancsfájlok automatizálási használatával.

Használja az alkalmazást az alábbiak szerint:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Az alkalmazás paramétereinek és tulajdonságainak részletei: 

| Tulajdonság                                                    | Leírás                                                  | Kötelező |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegisztrációÚjnode** "`<AuthenticationKey>`"                     | Regisztráljon egy saját üzemeltetésű integrációs futásidejű csomópontot a megadott hitelesítési kulccsal. | Nem       |
| **RegisztrációÚjnode** "`<AuthenticationKey>`" "`<NodeName>`"      | Regisztráljon egy saját üzemeltetésű integrációs futásidejű csomópontot a megadott hitelesítési kulccsal és csomópontnévvel. | Nem       |
| **Engedélyezés: Távoli elérés** "`<port>`" ["`<thumbprint>`"]            | Engedélyezze a távelérést az aktuális csomóponton egy magas rendelkezésre állású fürt beállításához. Vagy engedélyezze a hitelesítő adatok közvetlenül a saját üzemeltetésű infravörös az Azure Data Factory nélkül. Az utóbbit a **New-AzDataFactoryV2LinkedServiceEncryptedCredential** parancsmag használatával, ugyanazon a hálózaton lévő távoli számítógépről. | Nem       |
| **RemoteAccessInContainer engedélyezése** "`<port>`" ["`<thumbprint>`"] | Engedélyezze a távoli hozzáférést az aktuális csomóponthoz, amikor a csomópont egy tárolóban fut. | Nem       |
| **DisableRemoteAccess**                                         | Tiltsa le az aktuális csomópont távoli elérését. A többcsomópontos telepítéshez távelérésre van szükség. A **New-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell-parancsmag akkor is működik, ha a távelérés le van tiltva. Ez a viselkedés mindaddig igaz, amíg a parancsmag ugyanazon a gépen történik, mint az önkiszolgáló infravörös csomópont. | Nem       |
| **Kulcs** "`<AuthenticationKey>`"                                 | Az előző hitelesítési kulcs felülírása vagy frissítése. Legyen óvatos ezzel a művelettel. A korábbi saját üzemeltetésű infravörös csomópont offline állapotba kerülhet, ha a kulcs egy új integrációs futásidejű. | Nem       |
| **GenerateBackupFile (GenerateBackupFile)** "`<filePath>`" "`<password>`"            | Készítsen biztonsági másolatot az aktuális csomóponthoz. A biztonságimásolat-fájl tartalmazza a csomópontkulcsot és az adattár hitelesítő adatait. | Nem       |
| **ImportBackupFile** "`<filePath>`" "`<password>`"              | A csomópont visszaállítása biztonsági másolatból.                          | Nem       |
| **Indítsa újra**                                                     | Indítsa újra az önkiszolgáló integrációs runtime gazdagép-szolgáltatást.   | Nem       |
| **Kezdés**                                                       | Indítsa el az önkiszolgáló integrációs runtime gazdagép-szolgáltatást.     | Nem       |
| **Leállítás**                                                        | Állítsa le az önkiszolgáló integrációs runtime gazdagép-szolgáltatást.        | Nem       |
| **StartUpgradeService**                                         | Indítsa el a saját üzemeltetésű integrációs futásidejű frissítési szolgáltatást.       | Nem       |
| **StopUpgradeService**                                          | Állítsa le az önkiszolgáló integrációs futásidejű frissítési szolgáltatás leállítását.        | Nem       |
| **Turnonautofrissítés**                                            | Kapcsolja be az önkiszolgáló integrációs futásidejű automatikus frissítést.        | Nem       |
| **TurnOffAutoUpdate**                                           | Kapcsolja ki az önkiszolgáló integrációs futásidejű automatikus frissítést.       | Nem       |
| **SwitchServiceAccount** "`<domain\user>`" ["`<password>`"]           | Állítsa be, hogy a DIAHostService új fiókként fusson. Használja az üres jelszót "" a rendszer fiókok és virtuális számlák. | Nem       |


## <a name="command-flow-and-data-flow"></a>Parancsfolyam és adatfolyam

Amikor adatokat helyez át a helyszíni és a felhő között, a tevékenység egy saját üzemeltetésű integrációs futásidejű adatok átvitelére a helyszíni adatforrás és a felhő közötti.

Az alábbiakban a saját üzemeltetésű infravörös kapcsolattal történő másolás folyamatlépéseinek magas szintű összegzését olvashatja:

![Az adatáramlás magas szintű áttekintése](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Az adatfejlesztő egy PowerShell-parancsmag használatával saját üzemeltetésű integrációs futásidőt hoz létre egy Azure-adatgyárban. Jelenleg az Azure Portal nem támogatja ezt a funkciót.
1. Az adatfejlesztő létrehoz egy csatolt szolgáltatást egy helyszíni adattárhoz. A fejlesztő ezt úgy éri el, hogy megadja a saját üzemeltetésű integrációs futásidejű példányt, amelyet a szolgáltatásnak az adattárakhoz való csatlakozáshoz kell használnia.
1. A saját üzemeltetésű integrációs futásidejű csomópont titkosítja a hitelesítő adatokat a Windows Adatvédelmi alkalmazásprogramozási felület (DPAPI) használatával, és helyileg menti a hitelesítő adatokat. Ha több csomópont van beállítva a magas rendelkezésre állás, a hitelesítő adatok további szinkronizálása a többi csomópont között. Minden csomópont titkosítja a hitelesítő adatokat a DPAPI használatával, és helyileg tárolja őket. A hitelesítő adatok szinkronizálása az adatfejlesztő számára transzparens, és az önkiszolgáló infravörös kapcsolat kezeli.
1. Az Azure Data Factory kommunikál a saját üzemeltetésű integrációs futásidejű feladatok ütemezése és kezelése. A kommunikáció egy megosztott Azure [Service Bus Relay](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) kapcsolatot használó vezérlőcsatornán keresztül történik. Ha egy tevékenységfeladatot futtatni kell, a Data Factory várólistára helyezi a kérelmet a hitelesítő adatokkal együtt. Ezt abban az esetben teszi, ha a hitelesítő adatok még nincsenek tárolva az önkiszolgáló integrációs futásidőben. Az önkiszolgáló integrációs futásidejű elindítja a feladatot, miután lekérdezi a várólistát.
1. A saját üzemeltetésű integrációs futásidejű adatokat másol a helyszíni tároló és a felhőalapú tárhely között. A másolás iránya attól függ, hogy a másolási tevékenység hogyan van konfigurálva az adatfolyamatban. Ehhez a lépéshez a saját üzemeltetésű integrációs futásidejű közvetlenül kommunikál a felhőalapú tárolási szolgáltatásokkal, például az Azure Blob storage-tal egy biztonságos HTTPS-csatornán keresztül.

## <a name="considerations-for-using-a-self-hosted-ir"></a>A saját üzemeltetésű infravörös szolgáltatás használatának szempontjai

- Egyetlen saját üzemeltetésű integrációs futásidejű több helyszíni adatforráshoz is használható. Megoszthatja azt egy másik adat-előállítóval ugyanazon Az Azure Active Directory (Azure AD) bérlőn belül. További információ: [Sharing a self-hosted integration runtime](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- Az önkiszolgáló integrációs futásidejű ek csak egy példányát telepítheti egyetlen számítógépre. Ha két adatfeldolgozóval rendelkezik, amelyeknek helyszíni adatforrásokhoz kell hozzáférniük, használja a [saját üzemeltetésű infravörös megosztási szolgáltatást](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) a saját üzemeltetésű infravörös kapcsolat megosztásához, vagy telepítse a saját üzemeltetésű infravörös modult két helyszíni számítógépre, minden adat-előállítóhoz egyet.  
- A saját üzemeltetésű integrációs futásidejű nem kell ugyanazon a gépen, mint az adatforrás. Azonban az, hogy a saját üzemeltetésű integrációs futásidejű közel az adatforrás csökkenti az időt a saját üzemeltetésű integrációs futásidejű csatlakozni az adatforráshoz. Azt javasoljuk, hogy telepítse a saját üzemeltetésű integrációs futásidejű egy olyan gépen, amely eltér attól, amely a helyszíni adatforrást. Ha a saját üzemeltetésű integrációs futásidejű és adatforrás különböző gépeken, a saját üzemeltetésű integrációs futásidejű nem versenyez az adatforrás az erőforrások.
- Több saját üzemeltetésű integrációs futásidő is rendelkezhet különböző gépeken, amelyek ugyanahhoz a helyszíni adatforráshoz csatlakoznak. Ha például két saját üzemeltetésű integrációs futásidővel rendelkezik, amelyek két adatgyárat szolgálnak ki, ugyanaz a helyszíni adatforrás regisztrálható mindkét adatgyárban.
- Ha már telepítve van egy átjáró a számítógépre egy Power BI-forgatókönyv kiszolgálásához, telepítsen egy külön, saját üzemeltetésű integrációs futásidőt a Data Factory számára egy másik számítógépre.
- Az Azure virtuális hálózaton belüli adatintegráció támogatásához használjon saját üzemeltetésű integrációs futásidejű t.
- Kezelje az adatforrást olyan helyszíni adatforrásként, amely tűzfal mögött van, még akkor is, ha az Azure ExpressRoute szolgáltatást használja. Használja a saját üzemeltetésű integrációs futásidejű, hogy csatlakoztassa a szolgáltatást az adatforráshoz.
- Használja a saját üzemeltetésű integrációs futásidejű akkor is, ha az adattár a felhőben egy Azure-infrastruktúra szolgáltatásként (IaaS) virtuális gép.
- A feladatok sikertelenek lehetnek egy olyan Windows-kiszolgálón telepített, saját üzemeltetésű integrációs futásidőben, amelyhez a FIPS-kompatibilis titkosítás engedélyezve van. A probléma kerülő megoldásához tiltsa le a FIPS-kompatibilis titkosítást a kiszolgálón. A FIPS-kompatibilis titkosítás letiltásához módosítsa a rendszerleíró alkulcs következő értékét 1-ről (engedélyezve) 0-ra (letiltva): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Előfeltételek

- A Windows támogatott verziói a következők:
  + Windows 7 Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   A saját üzemeltetésű integrációs futásidejű tartományvezérlőn történő telepítése nem támogatott.
- A . Ha az önkiszolgáló integrációs futásidőt Windows 7 rendszerű számítógépre telepíti, telepítse a .NET Framework 4.6.1-es vagy újabb verziót. A részleteket a [.NET framework rendszerkövetelményei](/dotnet/framework/get-started/system-requirements) ben találja.
- Az önkiszolgáló integrációs üzemidejű gép ajánlott minimális konfigurációja egy 2 GHz-es processzor, 4 maggal, 8 GB RAM-mal és 80 GB szabad merevlemez-területtel.
- Ha a gazdagép hibernálja, a saját üzemeltetésű integrációs futásidő nem válaszol az adatkérésekre. Konfigurálja a megfelelő energiasémát a számítógépen az önkiszolgáló integrációs futásidő telepítése előtt. Ha a számítógép hibernálásra van konfigurálva, a saját üzemeltetésű integrációs futásidejű telepítő egy üzenettel kéri a kérdést.
- A saját üzemeltetésű integrációs futásidő sikeres telepítéséhez és konfigurálásához rendszergazdának kell lennie a számítógépen.
- Copy-tevékenység fut történik egy adott gyakorisággal. A processzor és a RAM használata a gépen ugyanazt a mintát követi, csúcs- és tétlen idővel. Az erőforrás-használat nagymértékben függ az áthelyezett adatok mennyiségétől is. Ha több másolási feladat van folyamatban, az erőforrás-használat csúcsidőben felfelé halad.
- A feladatok sikertelenek lehetnek a Parketta, AZ ORC vagy az Avro formátumban lévő adatok kinyerése során. A Parketta szolgáltatásról további információkért lásd: [Parketta formátum az Azure Data Factoryban.](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime) A fájllétrehozása az önkiszolgáló integrációs gépen fut. A várt módon való munkához a fájl létrehozásához a következő előfeltételek szükségesek:
    - [Visual C++ 2010 terjeszthető](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Csomag (x64)
    - Java Runtime (JRE) 8-as verzió egy JRE szolgáltatótól, például [Adopt OpenJDK](https://adoptopenjdk.net/). Győződjön `JAVA_HOME` meg arról, hogy a környezeti változó be van állítva.

## <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez

Az önkiszolgáló integrációs futásidejű alkalmazást a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717)származó Managed Identity telepítőcsomag letöltésével telepítheti. Tekintse meg az [Adatok áthelyezése a helyszíni és a felhőbeli](tutorial-hybrid-copy-powershell.md) között című cikket, amely részletes útmutatást tartalmaz.

- Konfiguráljon egy energiasémát a gazdagépen az önkiszolgáló integrációs futásidejű, hogy a gép nem hibernálódik. Ha a gazdagép hibernálja magát, az önkiszolgáló integrációs futásidő offline állapotba kerül.
- Rendszeresen biztonsági másolatot a hitelesítő adatok a saját üzemeltetésű integrációs futásidejű társított.
- A saját üzemeltetésű infravörös telepítési műveletek automatizálásához olvassa el [A PowerShellen keresztül meglévő saját üzemeltetett infravörös kapcsolat beállítása.](#setting-up-a-self-hosted-integration-runtime)  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Saját üzemeltetésű infravörös szolgáltatás telepítése és regisztrálása a Microsoft letöltőközpontjából

1. Nyissa meg a [Microsoft integrációs futásidejű letöltési lapját.](https://www.microsoft.com/download/details.aspx?id=39717)
1. Válassza **a Letöltés**lehetőséget, válassza a 64 bites verziót, majd a **Tovább**gombot. A 32 bites verzió nem támogatott.
1. Futtassa közvetlenül a felügyelt identitásfájlt, vagy mentse a merevlemezre, és futtassa azt.
1. Az **Üdvözlőablakban** jelöljön ki egy nyelvet, és válassza a **Tovább**gombot.
1. Fogadja el a Microsoft szoftverlicenc-feltételeit, és válassza a **Tovább gombot.**
1. Jelölje ki a saját üzemeltetésű integrációs futásidejű telepítéséhez szükséges **mappát,** és válassza a **Tovább**gombot.
1. A **Telepítésre kész** lapon válassza a **Telepítés**lehetőséget.
1. A telepítés befejezéséhez válassza a **Befejezés** lehetőséget.
1. A hitelesítési kulcs beszerezni a PowerShell használatával. Íme egy PowerShell-példa a hitelesítési kulcs beolvasására:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. A számítógépen futó Microsoft Integration Runtime Configuration Manager **(Self-hosted) regiszterintegrációs futtató)ablakában** tegye a következő lépéseket:

    1. Illessze be a hitelesítési kulcsot a szövegterületre.

    1. A kulcsszöveg megjelenítéséhez válassza a **Hitelesítési kulcs megjelenítése** lehetőséget.

    1. Kattintson a **Register** (Regisztrálás) elemre.

## <a name="high-availability-and-scalability"></a>Magas rendelkezésre állás és méretezhetőség

A saját üzemeltetésű integrációs futásidejű eket több helyszíni géphez vagy virtuális géphez társíthatja az Azure-ban. Ezeket a gépeket csomópontoknak nevezzük. Legfeljebb négy csomópont ot társíthat egy saját üzemeltetésű integrációs futásidőhez. A logikai átjáróhoz átjárót tartalmazó helyszíni gépeken több csomópont előnyei a következők:

* Az önkiszolgáló integrációs futásidejű, így már nem az egyetlen hibapont a big data-megoldás vagy a Data Factory felhőalapú adatok integrálása. Ez a rendelkezésre állás segít biztosítani a folytonosságot, ha legfeljebb négy csomópontot használ.
* Jobb teljesítmény és átviteli sebesség a helyszíni és a felhőbeli adattárak közötti adatáthelyezés során. További információ a [teljesítmény-összehasonlításról.](copy-activity-performance.md)

Több csomópontot is társíthat, ha telepíti a saját üzemeltetésű integrációs futásidejű szoftvert a [letöltőközpontból.](https://www.microsoft.com/download/details.aspx?id=39717) Ezután regisztrálja az **új-AzDataFactoryV2IntegrationRuntimeKey** parancsmagból beszerzett hitelesítési kulcsok egyikével, az [oktatóanyagban](tutorial-hybrid-copy-powershell.md)leírtak szerint.

> [!NOTE]
> Nem kell létrehoznia egy új, saját üzemeltetésű integrációs futásidejű t az egyes csomópontok társításához. Telepítheti a saját üzemeltetésű integrációs futásidejű egy másik gépen, és regisztrálja ugyanazt a hitelesítési kulcsot.

> [!NOTE]
> Mielőtt egy másik csomópontot ad hozzá a magas rendelkezésre állás és méretezhetőség érdekében, győződjön meg arról, hogy az **intranethez való távoli hozzáférés** beállítás engedélyezve van az első csomóponton. Ehhez válassza a **Microsoft Integration Runtime Configuration Manager** > Settings Remote access**to intranet (Microsoft**Integration Runtime Configuration Manager**Settings** > Remote access to intranet ) lehetőséget.

### <a name="scale-considerations"></a>Méretezési szempontok

#### <a name="scale-out"></a>Horizontális felskálázás

Ha a processzorhasználat magas, és a rendelkezésre álló memória kevés a saját üzemeltetésű infravörös, adjon hozzá egy új csomópontot, hogy segítsen horizontálisan ki a terhelést a gépek között. Ha a tevékenységek sikertelenek, mert időtúltöltés, vagy a saját üzemeltetésű infravörös csomópont offline állapotban van, akkor segít, ha csomópontot ad hozzá az átjáróhoz.

#### <a name="scale-up"></a>Vertikális felskálázás

Ha a processzor és a rendelkezésre álló RAM nem jól kihasznált, de az egyidejű feladatok végrehajtása eléri a csomópont korlátait, a csomópont által futtatható egyidejű feladatok számának növelésével növelje a növekvő számú párhuzamos feladatokat. Előfordulhat, hogy a tevékenységek időtúllépésesetén is szeretné felskálázni, mert a saját üzemeltetésű infravörös túlcsordulás. Az alábbi képen látható módon növelheti egy csomópont maximális kapacitását:  

![A csomóponton futtatható egyidejű feladatok számának növelése](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL tanúsítványkövetelmények

Az integrációs futásidejű csomópontok közötti kommunikáció biztonságossá tétele érdekében használt TLS/SSL-tanúsítvány követelményei a következők:

- A tanúsítványnak nyilvánosan megbízható X509 v3 tanúsítványnak kell lennie. Azt javasoljuk, hogy olyan tanúsítványokat használjon, amelyeket nyilvános partnerhitelesítési hatóság (CA) állít ki.
- Minden integrációs futásidejű csomópontnak meg kell bíznia ebben a tanúsítványban.
- Nem javasoljuk a Tulajdonos alternatív neve (SAN) tanúsítványokat, mert csak az utolsó SAN-elemet használja a program. Az összes többi SAN-elem figyelmen kívül lesz hagyva. Ha például olyan SAN-tanúsítvánnyal rendelkezik, amelynek san-tanúsítványa **node1.domain.contoso.com** és **node2.domain.contoso.com**, ezt a tanúsítványt csak olyan számítógépen használhatja, amelynek teljesen minősített tartományneve (FQDN) **node2.domain.contoso.com.**
- A tanúsítvány a Windows Server 2012 R2 által tls-tanúsítványokhoz támogatott bármely kulcsméretet használhatja.
- A CNG-kulcsokat használó tanúsítványok nem támogatottak.  

> [!NOTE]
> Ez a tanúsítvány a következő:
>
> - Saját üzemeltetésű infravörös csomópont portjainak titkosítása.
> - Csomópont-csomópont kommunikáció állapotszinkronizáláshoz, amely magában foglalja a csatolt szolgáltatások csomópontok közötti hitelesítő adatok szinkronizálását.
> - Ha egy PowerShell-parancsmag a helyi hálózaton belülről a csatolt szolgáltatás hitelesítő adatainak beállításaihoz használatos.
>
> Javasoljuk, hogy használja ezt a tanúsítványt, ha a magánhálózati környezet nem biztonságos, vagy ha biztosítani szeretné a kommunikációt a magánhálózaton belüli csomópontok között.
>
> A saját üzemeltetésű infravörös kapcsolatról más adattárolókba történő adatátvitel során történő adatáthelyezés mindig titkosított csatornán belül történik, függetlenül attól, hogy ez a tanúsítvány be van-e állítva.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Megosztott, saját üzemeltetésű integrációs futásidő létrehozása az Azure Data Factoryban

Újra felhasználhatja a meglévő, önkiszolgáló integrációs futásidejű infrastruktúrát, amelyet már beállított egy adat-előállítóban. Ez az újrafelhasználás lehetővé teszi, hogy hozzon létre egy csatolt saját üzemeltetésű integrációs futásidejű egy másik adat-előállító hivatkozva egy meglévő megosztott saját üzemeltetésű infravörös.

A funkció bemutatását és bemutatását a következő 12 perces videóban láthatod:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminológia

- **Megosztott infravörös**: Egy eredeti, saját üzemeltetésű infravörös, amely fizikai infrastruktúrán fut.  
- **Csatolt infravörös**: Egy másik megosztott infravörös kapcsolatra hivatkozó infravörös kapcsolat. A csatolt infravörös kapcsolat egy logikai infravörös kapcsolat, amely egy másik megosztott, saját üzemeltetésű infravörös kapcsolat infrastruktúráját használja.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs futásidejű megosztásának módszerei

Ha több adatgyárral szeretne megosztani egy saját üzemeltetésű integrációs futásidejűt, a részletekért olvassa [el a Megosztott, saját üzemeltetésű integrációs futásidejű létrehozása](create-shared-self-hosted-integration-runtime-powershell.md) című témakört.

### <a name="monitoring"></a>Figyelés

#### <a name="shared-ir"></a>Megosztott IR

![A megosztott integrációs futásidejű ek keresése](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Megosztott integrációs futásidejű figyelése](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Csatolt ir

![Kijelölések a csatolt integrációs futásidejűek megkereséséhez](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Csatolt integrációs futásidő figyelése](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>A saját üzemeltetésű infravörös megosztás ismert korlátai

* Annak az adat-előállítónak, amelyben a csatolt infravörös kapcsolat létrehozása felügyelt [identitással](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)rendelkezik. Alapértelmezés szerint az Azure Portalon vagy a PowerShell-parancsmagokban létrehozott adatgyárak implicit módon létrehozott felügyelt identitással rendelkeznek. Ha azonban egy Adat-előállító egy Azure Resource Manager-sablon vagy SDK-n keresztül jön létre, explicit módon be kell állítania az **Identity** tulajdonságot. Ez a beállítás biztosítja, hogy az Erőforrás-kezelő olyan adat-előállítót hoz létre, amely felügyelt identitást tartalmaz.

* A szolgáltatást támogató Data Factory .NET SDK-nak 1.1.0-s vagy újabb verziónak kell lennie.

* Az engedély megadásához szüksége van a tulajdonosi vagy az örökölt tulajdonos szerepkörre abban az adat-előállítóban, ahol a megosztott infravörös kapcsolat létezik.

* A megosztási funkció csak az azonos Azure AD-bérlőn belüli adatgyárakesetében működik.

* Az Azure AD [vendégfelhasználók](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)számára a felhasználói felület keresési funkciója, amely az összes adatgyárat keresési kulcsszó használatával sorolja fel, [nem működik.](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits) De mindaddig, amíg a vendég felhasználó tulajdonosa az adat-előállító, megoszthatja az infravörös a keresési funkció nélkül. Az infravörös kapcsolat megosztásához szükséges adat-előállító felügyelt identitásához írja be a felügyelt identitást az **Engedély hozzárendelése** mezőbe, és válassza a **Hozzáadás lehetőséget** az adatgyár felhasználói felületén.

  > [!NOTE]
  > Ez a funkció csak a Data Factory V2 szolgáltatásban érhető el.

## <a name="notification-area-icons-and-notifications"></a>Értesítési terület ikonjai és értesítései

Ha a kurzort az értesítési területen lévő ikon vagy üzenet fölé viszi, megtekintheti az önkiszolgáló integrációs futásidejű állapotának részleteit.

![Értesítések az értesítési területen](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Portok és tűzfalak

Két tűzfalat kell figyelembe venni:

- A szervezet központi útválasztóján futó *vállalati tűzfal*
- Démonként konfigurált *Windows* tűzfal azon a helyi számítógépen, amelyen telepítve van az önkiszolgáló integrációs futásidő

![A tűzfalak](media/create-self-hosted-integration-runtime/firewall.png)

A vállalati tűzfal szintjén a következő tartományokat és kimenő portokat kell konfigurálnia:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

A Windows tűzfal szintjén vagy a számítógép szintjén ezek a kimenő portok általában engedélyezve vannak. Ha nem, konfigurálhatja a tartományok és portok egy saját üzemeltetésű integrációs üzemidejű gépen.

> [!NOTE]
> A forrás és a fogadók alapján előfordulhat, hogy további tartományokat és kimenő portokat kell engedélyeznie a vállalati tűzfalon vagy a Windows tűzfalon.
>
> Egyes felhőalapú adatbázisok, például az Azure SQL Database és az Azure Data Lake, előfordulhat, hogy engedélyeznie kell az IP-címek saját üzemeltetésű integrációs futásidejű gépek a tűzfal-konfiguráción.

### <a name="copy-data-from-a-source-to-a-sink"></a>Adatok másolása forrásból fogadóba

Győződjön meg arról, hogy megfelelően engedélyezi a tűzfalszabályokat a vállalati tűzfalon, az önkiszolgáló integrációs futásidejű gép Windows tűzfalán és magán az adattárban. Ezeket a szabályokat engedélyezve lehetővé teszi, hogy a saját üzemeltetésű integrációs futásidejű sikeresen csatlakozzon a forrás és a fogadó. Engedélyezze a szabályokat a másolási műveletben részt vevő minden egyes adattárhoz.

Ha például egy helyszíni adattárból szeretne másolni egy SQL Database-fogadóba vagy egy Azure SQL Data Warehouse-fogadóba, tegye a következő lépéseket:

1. Kimenő TCP-kommunikáció engedélyezése az 1433-as porton a Windows tűzfal és a vállalati tűzfal számára is.
1. Konfigurálja az SQL-adatbázis tűzfalbeállításait úgy, hogy az önkiszolgáló integrációs üzemidejű gép IP-címét hozzáadja az engedélyezett IP-címek listájához.

> [!NOTE]
> Ha a tűzfal nem engedélyezi az 1433-as kimenő portot, a saját üzemeltetésű integrációs futásidő nem tud közvetlenül hozzáférni az SQL-adatbázishoz. Ebben az esetben az SQL Database és az SQL Data Warehouse [szakaszos másolását](copy-activity-performance.md) is használhatja. Ebben az esetben csak HTTPS (443-as port) szükséges az adatáthelyezéshez.

## <a name="proxy-server-considerations"></a>A proxykiszolgálóval kapcsolatos szempontok

Ha a vállalati hálózati környezet proxykiszolgálót használ az internet eléréséhez, konfigurálja a saját üzemeltetésű integrációs futásidőt a megfelelő proxybeállítások használatára. Beállíthatja a proxy a kezdeti regisztrációs fázisban.

![A proxy megadása](media/create-self-hosted-integration-runtime/specify-proxy.png)

Ha konfigurálva van, a saját üzemeltetésű integrációs futásidő a proxykiszolgáló t használja a felhőszolgáltatás forrásához és céljához való csatlakozáshoz (amely a HTTP vagy HTTPS protokollt használja). Ezért válassza a **Hivatkozás módosítása** lehetőséget a kezdeti telepítés során.

![A proxy beállítása](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Három beállítási lehetőség van:

- **Ne használjon proxyt:** A saját üzemeltetésű integrációs futásidejű nem használ kifejezetten semmilyen proxyt a felhőszolgáltatásokhoz való csatlakozáshoz.
- **Rendszerproxy használata**: Az önkiszolgáló integrációs futásidő a diahost.exe.config és diawp.exe.config fájlban konfigurált proxybeállítást használja. Ha ezek a fájlok nem adnak meg proxykonfigurációt, a saját üzemeltetésű integrációs futásidejű közvetlenül csatlakozik a felhőszolgáltatáshoz anélkül, hogy proxyn keresztül haladna.
- **Egyéni proxy használata**: A diahost.exe.config és a diawp.exe.config konfigurációk helyett konfigurálja a SAJÁT üzemeltetésű integrációs futásidejű HTTP-proxybeállítást. **Cím-** és **portértékek** szükségesek. **A felhasználónév** és **a jelszó** értékek megadása nem kötelező a proxy hitelesítési beállításától függően. Minden beállítás titkosítva van a Windows DPAPI rendszerrel az önkiszolgáló integrációs futásidőben, és helyileg tárolódik a számítógépen.

Az integrációs futásidejű gazdagép szolgáltatás a frissített proxybeállítások mentése után automatikusan újraindul.

Az önkiszolgáló integrációs futásidejű regisztrálása után, ha meg szeretné tekinteni vagy frissíteni szeretné a proxybeállításokat, használja a Microsoft Integration Runtime Configuration Manager programot.

1. Nyissa **meg a Microsoft Integration Runtime Configuration Manager programot.**
1. Válassza a **Settings** (Beállítások) fület.
1. A **HTTP-proxy csoportban**kattintson a **Módosítás** hivatkozásra a **HTTP-proxy beállítása** párbeszédpanel megnyitásához.
1. Kattintson a **Tovább** gombra. Ezután megjelenik egy figyelmeztetés, amely engedélyt kér a proxybeállítás mentésére és az integrációs futásidejű gazdagép szolgáltatás újraindítására.

A Configuration Manager eszközzel megtekintheti és frissítheti a HTTP-proxyt.

![A proxy megtekintése és frissítése](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Ha NTLM-hitelesítéssel rendelkező proxykiszolgálót állít be, az integrációs futásidejű állomásszolgáltatás a tartományi fiók alatt fut. Ha később módosítja a tartományi fiók jelszavát, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, és indítsa újra a szolgáltatást. E követelmény miatt azt javasoljuk, hogy a proxykiszolgálót egy dedikált tartományi fiók használatával érje el, amely nem igényli a jelszó gyakori frissítését.

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló beállításainak konfigurálása

Ha a **HTTP-proxy rendszerproxy használata** beállítását választja, a saját üzemeltetésű integrációs futásidő a diahost.exe.config és a diawp.exe.config proxybeállításait használja. Ha ezek a fájlok nem adnak meg proxyt, a saját üzemeltetésű integrációs futásidejű közvetlenül, proxy nélkül csatlakozik a felhőszolgáltatáshoz. Az alábbi eljárás a diahost.exe.config fájl frissítésével kapcsolatos utasításokat tartalmazza:

1. A Fájlkezelőben készítsen biztonságos másolatot a C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config fájlról az eredeti fájl biztonsági másolataként.
1. Nyissa meg a rendszergazdaként futó Jegyzettömböt.
1. A Jegyzettömbben nyissa meg a C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config szövegfájlt.
1. Keresse meg az alapértelmezett **system.net** címkét a következő kódban látható módon:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Ezután hozzáadhatja a proxykiszolgáló adatait az alábbi példában látható módon:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    A proxycímke lehetővé teszi, `scriptLocation`hogy további tulajdonságok adja meg a szükséges beállításokat, mint például . A [ \<\> szintaxist lásd a proxyelemben (Hálózati beállítások).](https://msdn.microsoft.com/library/sa91de1e.aspx)

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Mentse a konfigurációs fájlt az eredeti helyére. Ezután indítsa újra az önkiszolgáló integrációs runtime gazdagép-szolgáltatást, amely felveszi a módosításokat.

   A szolgáltatás újraindításához használja a Vezérlőpult szolgáltatás-kisalkalmazási lapját. Vagy az Integrációs futásidejű konfigurációkezelőben válassza a **Szolgáltatás leállítása** gombot, majd a **Szolgáltatás indítása parancsot.**

   Ha a szolgáltatás nem indul el, valószínűleg helytelen XML-címkeszintaxist adott hozzá a szerkesztett alkalmazáskonfigurációs fájlhoz.

> [!IMPORTANT]
> Ne felejtse el frissíteni a diahost.exe.config és a diawp.exe.config fájlját.

Azt is meg kell győződnie arról, hogy a Microsoft Azure szerepel a vállalat engedélyezési listáján. Az érvényes Azure IP-címek listáját a [Microsoft letöltőközpontjából](https://www.microsoft.com/download/details.aspx?id=41653)töltheti le.

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>A tűzfallal és a proxykiszolgálóval kapcsolatos problémák lehetséges tünetei

Ha az alábbihoz hasonló hibaüzenetek jelennek meg, ennek valószínűok a tűzfal vagy a proxykiszolgáló nem megfelelő konfigurációja. Az ilyen konfiguráció megakadályozza, hogy a saját üzemeltetésű integrációs futásidejű a Data Factory-hoz való csatlakozást, hogy hitelesítse magát. A tűzfal és a proxykiszolgáló megfelelő konfigurálásának biztosításához olvassa el az előző szakaszt.

* Amikor megpróbálja regisztrálni az önkiszolgáló integrációs futásidejűt, a következő hibaüzenet jelenik meg: "Nem sikerült regisztrálni ezt az integrációs futásidejű csomópontot! Ellenőrizze, hogy a hitelesítési kulcs érvényes-e, és hogy az integrációs szolgáltatás gazdagépe fut-e ezen a számítógépen."
* Az Integrációs futásidejű konfigurációkezelő megnyitásakor a **Kapcsolat bontásvagy** a Csatlakozás állapota **jelenik**meg. A Windows eseménynaplóinak megtekintésekor az **Eseménynapló** > **alkalmazás- és szolgáltatásnaplók** > **Microsoft-integrációs futásidejű**területén a következőhöz hasonló hibaüzenetek jelennek meg:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Távelérés engedélyezése intranetről

Ha a PowerShell segítségével titkosítja a hitelesítő adatokat egy hálózati gépről, amely nem a saját üzemeltetésű integrációs futásidejű, engedélyezheti a **távelérés intranetről** beállítást. Ha a PowerShell segítségével titkosítja a hitelesítő adatokat azon a számítógépen, amelyen telepítette a saját üzemeltetésű integrációs futásidejűt, nem engedélyezheti **a távelérést az intranetről.**

Engedélyezze **a távelérést az intranetről,** mielőtt egy másik csomópontot ad hozzá a magas rendelkezésre állás és méretezhetőség érdekében.  

A saját üzemeltetésű integrációs futásidejű telepítő 3.3-as vagy újabb verziójának futtatásakor alapértelmezés szerint a saját üzemeltetésű integrációs futásidejű telepítő letiltja a **távelérést az intranetről** a saját üzemeltetésű integrációs futásidejű gépen.

Ha egy partnertől vagy másoktól használ tűzfalat, manuálisan is megnyithatja a 8060-as portot vagy a felhasználó által konfigurált portot. Ha tűzfallal van probléma az önkiszolgáló integrációs futásidő beállításakor, a következő paranccsal telepítheti az önkiszolgáló integrációs futásidőt a tűzfal konfigurálása nélkül:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Ha úgy dönt, hogy nem nyitja meg a 8060-as portot a saját üzemeltetésű integrációs futásidejű gépen, a Hitelesítő adatok beállítása alkalmazástól eltérő mechanizmusokat használjon az adattároló hitelesítő adatainak konfigurálásához. Használhatja például a **New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell-parancsmaghasználatát.

## <a name="next-steps"></a>További lépések

A részletes útmutatásról az [Oktatóanyag: Helyszíni adatok másolása a felhőbe](tutorial-hybrid-copy-powershell.md)című témakörben talál.

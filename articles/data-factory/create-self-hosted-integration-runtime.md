---
title: Hozzon létre egy saját üzemeltetésű integrációs modul az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy saját üzemeltetésű integrációs modul az Azure Data Factoryben, amely lehetővé teszi az adat-előállítók egy magánhálózaton lévő adattárak eléréséhez.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: abnarain
ms.openlocfilehash: dc9f24f948e32d1b87745016852a875d440323de
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443697"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Létrehozhat és konfigurálhat egy saját üzemeltetésű integrációs modul
Az integrációs modul (IR) a számítási infrastruktúra, amellyel Azure Data Factory adatintegrációs képességeket biztosítja különböző hálózati környezetekben. Integrációs modul kapcsolatos részletekért lásd: [Integration runtime áttekintése](concepts-integration-runtime.md).

Egy saját üzemeltetésű integrációs modulját futtathatja a másolási tevékenységek egy felhőalapú adattár és a egy magánhálózaton lévő adattár között, és azt is melyik átalakítási tevékenységek számítási erőforrásokon, egy helyszíni hálózat vagy az Azure-beli virtuális hálózathoz. Egy saját üzemeltetésű integrációs modul telepítése kell egy helyszíni gépre vagy magánhálózaton belüli virtuális gép (VM).  

Ez a dokumentum ismerteti, hogyan, létrehozhat és konfigurálhat egy saját üzemeltetésű

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Magas szintű lépéseket kell egy saját üzemeltetésű integrációs modul telepítése
1. Hozzon létre egy saját üzemeltetésű integrációs modult. Ez a feladat használható az Azure Data Factory felhasználói felületén. Itt látható egy PowerShell-példa:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) , és telepítse a saját üzemeltetésű integrációs modult egy helyi gépen.

3. A hitelesítési kulcs lekérése, és regisztrálja a kulcsot a saját üzemeltetésű integrációs modult. Itt látható egy PowerShell-példa:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template-automation"></a>Egy saját üzemeltetésű integrációs modul egy Azure virtuális gépen az Azure Resource Manager-sablon (automatizálás) beállítása
Egy Azure virtuális gépen a saját üzemeltetésű integrációs modul telepítő használatával automatizálható [ezen Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Ez a sablon tartalmaz egy egyszerű módja annak, hogy rendelkezik egy teljes mértékben működő saját üzemeltetésű IR egy magas rendelkezésre állás és méretezhetőség funkciókat az Azure virtuális hálózaton belül, (feltéve, megadhatja a csomópontok száma 2 vagy nagyobb értékre).

## <a name="command-flow-and-data-flow"></a>Parancsot a folyamat és az adatfolyam
Amikor adatok áthelyezése a helyszíni és a felhőben, a tevékenység egy saját üzemeltetésű integrációs modult használja az adatok átviteléhez a helyszíni adatforrás a felhőben, és ez fordítva is igaz.

A következő magas szintű adatok folyamat lépéseit egy saját üzemeltetésű integrációs modul másolás összefoglalása:

![Áttekintés](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. A data-fejlesztési belül egy Azure data factory egy saját üzemeltetésű integrációs modult egy PowerShell-parancsmag használatával hoz létre. Az Azure portal jelenleg nem támogatja ezt a szolgáltatást.
2. A data-fejlesztési egy helyszíni adattár társított szolgáltatás megadja a saját üzemeltetésű integrációsmodul-példány, amelyet szeretne csatlakozni az adattárak használnia kell hoz létre. A társított szolgáltatás beállításának részeként az adatok fejlesztői használ a hitelesítőadat-kezelő alkalmazás (jelenleg nem támogatott) hitelesítési típusok és a hitelesítő adatok beállítása. A hitelesítőadat-kezelő alkalmazás kommunikál az adattár, tesztelje a kapcsolatot és a saját üzemeltetésű integrációs modult a hitelesítő adatok mentéséhez.
3. A saját üzemeltetésű integration runtime csomópontja a hitelesítő adatokat a Windows Data Protection alkalmazás alkalmazásprogramozási felületet (DPAPI) segítségével titkosítja, és a hitelesítő adatok helyben menti. Ha több csomóponton vannak beállítva, a magas rendelkezésre állás, a hitelesítő adatok további szinkronizálódnak, más csomópontok között. Minden egyes csomópont titkosítja a hitelesítő adatok használatával a DPAPI-t, és helyileg tárolja azokat. Adatok szinkronizálása a data-fejlesztési átlátható és kezeli a saját üzemeltetésű    
4. A Data Factory szolgáltatás a saját üzemeltetésű integrációs modul az ütemezés és a felügyeleti feladatok keresztül kommunikál egy *vezérlőcsatorna* megosztott Azure Service Bus-üzenetsort használó. Amikor egy tevékenység feladatot kell futtatni, a Data Factory semmilyen hitelesítő információt együtt a kérelem várólistára helyezi (Ha a hitelesítő adatok már nem kerülnek be a saját üzemeltetésű integrációs modul). A várólista lekérdezés után a feladat a saját üzemeltetésű integrációs modul címmel.
5. A saját üzemeltetésű integrációs modul adatokat másol egy helyszíni adattár, a felhőalapú tárolást, vagy fordítva az adatok folyamatban, a másolási tevékenység konfigurációjától függően. Az ebben a lépésben a saját üzemeltetésű integrációs modul közvetlenül kommunikál a felhőalapú tárolási szolgáltatások – például az Azure Blob storage egy biztonságos csatornán (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Egy saját üzemeltetésű integrációs modul használatának szempontjai

- Saját üzemeltetésű integrációs több helyszíni adatforrás is használható. Egy egyetlen saját üzemeltetésű integrációs modul az Azure Active Directory ugyanazon a bérlőn belül egy másik data factoryvel is megoszthatók. További információkért lásd: [megosztása egy saját üzemeltetésű integrációs modul](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Használhat egy saját üzemeltetésű integrációs modul telepítve egyetlen gépen csak egy példánya. Ha két adat-előállítók adott igényelnek hozzáférést a helyszíni adatforrások, szükséges a saját üzemeltetésű integrációs modul telepítése a két helyszíni számítógépeken. Más szóval egy saját üzemeltetésű integrációs modult az adott adat-előállító vannak kötve.
- A saját üzemeltetésű integrációs modul nem kell ugyanarra a gépre, az adatforrással kell. Azonban a saját üzemeltetésű integrációs modul kellene csökkenti a közelebb az adatforrás az idő a saját üzemeltetésű integrációs modul kapcsolódni az adatforráshoz. Javasoljuk, hogy telepítse a saját üzemeltetésű integrációs modul olyan számítógépen, amelyen eltér a gazdagép a helyszíni adatforrás. Ha a saját üzemeltetésű integrációs modul és az adatforrás különböző gépeken vannak, a saját üzemeltetésű integrációs modul befolyásolják az adatforrás erőforrás esetén.
- Az azonos helyszíni adatforráshoz csatlakozó különböző gépeken több saját üzemeltetésű integrációs modulok is rendelkezhet. Például előfordulhat, hogy két saját üzemeltetésű integrációs modulok, amely két adat-előállítók szolgálnak, de ugyanazon a helyszíni adatforrás regisztrálva van a mindkét az adat-előállítók.
- Ha már van egy átjáró telepítve a számítógépen a Power bi-ban Ez a forgatókönyv kiszolgálására, telepítse egy külön saját üzemeltetésű integrációs modul az Azure Data Factory egy másik gépen.
- A saját üzemeltetésű integrációs modul egy Azure virtuális hálózaton belüli Adatintegráció támogatásához kell használni.
- Kezelje az adatforráshoz egy helyszíni adatforráshoz, hogy van egy tűzfal mögött található, akkor is, ha az Azure ExpressRoute használata. A saját üzemeltetésű integrációs modul használatával a szolgáltatás és az adatforrás közötti kapcsolatot.
- A saját üzemeltetésű integrációs modult kell használnia, akkor is, ha az adattárban a felhőben az Azure IaaS virtuális gépen.
- Feladatok egy saját üzemeltetésű integrációs modult, amely telepítve van egy Windows server mely FIPS-kompatibilis titkosítás engedélyezve van a sikertelen lehet. Ez a probléma megkerüléséhez, tiltsa le a FIPS-kompatibilis titkosítás a kiszolgálón. FIPS-kompatibilis titkosítás letiltásához módosítsa a következő beállításazonosítót (engedélyezve) 0 (letiltva) 1-től: `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Előfeltételek

- Az operációs rendszerek támogatott verzióinak a Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2 és a Windows Server 2016 rendszer. A saját üzemeltetésű integrációs modult egy tartományvezérlő telepítése nem támogatott.
- .NET-keretrendszer 4.6.1-es verzióját vagy újabb verzió szükséges. Ha a saját üzemeltetésű integrációs modult egy Windows 7 gépre telepíti, telepítse a .NET-keretrendszer 4.6.1-es vagy újabb. Lásd: [.NET-keretrendszer követelményei a System](/dotnet/framework/get-started/system-requirements) részleteiről.
- Az ajánlott konfiguráció, a saját üzemeltetésű integrációs modul gép legalább 2 GHz-es, négy magot, 8 GB RAM és a egy 80 GB-os lemezt.
- Ha szeretné a gazdagépen, a saját üzemeltetésű integrációs modul nem válaszol kérelmek. A számítógépen egy megfelelő energiasémát beállítani, a saját üzemeltetésű integrációs modul telepítése előtt. Ha a számítógép hibernált állapotba van konfigurálva, a saját üzemeltetésű integrációs modul telepítése kéri egy üzenetet.
- A gépen telepítheti és konfigurálhatja a saját üzemeltetésű integrációs modul sikeresen rendszergazdának kell lennie.
- Másolási tevékenység-végrehajtás egy adott gyakoriságát fordulhat elő. Erőforrás-használat (CPU, memória) a gépen csúcs-és üresjárati ugyanazt a mintát követi. Erőforrás-használat is nagyban függ az áthelyezett adatok mennyisége. Amikor több másolási feladat van folyamatban, erőforrás-használat csúcsidőben feljebb görgetünk láthatja.

## <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez
A saját üzemeltetésű integrációs modult telepítheti az MSI-telepítő csomag letöltésével a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Lásd: [adatok áthelyezése a helyszíni és a felhő között a cikk](tutorial-hybrid-copy-powershell.md) részletes útmutatásait.

- A gazdagépen a saját üzemeltetésű integrációs modul energiasémát beállítani, hogy a gép hibernálásra nem. Ha szeretné a gazdagépen, a saját üzemeltetésű integrációs modul offline állapotba kerül.
- Készítsen biztonsági másolatot a saját üzemeltetésű integrációs modul rendszeresen társított hitelesítő adatok.

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Telepítse és regisztrálja a letöltőközpontból saját üzemeltetésű integrációs modul

1. Nyissa meg a [Microsoft integration runtime letöltési oldal](https://www.microsoft.com/download/details.aspx?id=39717).
2. Válassza ki **letöltése**, válassza ki a 64 bites verziót (32-bit-es nem támogatott), és válassza ki **tovább**.
3. Közvetlenül, futtassa az MSI-fájlt, vagy mentse a merevlemezen, és futtathatja.
4. Az a **üdvözlő** lapon válasszon egy nyelvet, és válassza **tovább**.
5. Fogadja el a Microsoft szoftverlicenc-feltételeket, majd válassza ki **tovább**.
6. Válassza ki **mappa** a saját üzemeltetésű integrációs modul telepítése és a kívánt **tovább**.
7. Az a **készen áll a telepítésre** lapon jelölje be **telepítése**.
8. Kattintson a **Befejezés** telepítésének befejezéséhez.
9. A hitelesítési kulcs beszerzése az Azure PowerShell használatával. Itt látható egy PowerShell-példa a hitelesítési kulcs beolvasása céljából:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Az a **integrációs modul regisztrálása (saját üzemeltetésű)** oldal a Microsoft Integration Runtime konfigurációkezelőjének a gépen futó, az alábbi lépéseket:

    a. Illessze be a hitelesítési kulcs a terület.

    b. Bejelölheti **megjelenítése a hitelesítési kulcs** , tekintse meg a kulcs szövegét.

    c. Kattintson a **Register** (Regisztrálás) elemre.


## <a name="high-availability-and-scalability"></a>Magas rendelkezésre állás és méretezhetőség
Egy saját üzemeltetésű integrációs modul több helyszíni gép társítható. Ezek a gépek csomópontok nevezzük. Egy saját üzemeltetésű integrációs modul társított legfeljebb négy csomóponttal rendelkezhet. Több csomópont (telepített átjáró a helyszíni gépek) rendelkező logikai átjáró előnyei a következők:
* A saját üzemeltetésű integrációs modult úgy, hogy a már nem az egypontos meghibásodás big Data típusú adatok megoldást vagy felhőalapú Adatintegráció az Azure Data Factoryt, legfeljebb négy csomópont folytonosság biztosítása a magasabb rendelkezésre állás.
* Javult a teljesítmény és az átviteli sebesség a helyszíni és felhő közötti adatátvitel során adattárakban. További információ [teljesítmény összehasonlítások](copy-activity-performance.md).

Több csomópont társíthatja a saját üzemeltetésű integrációs modul szoftver telepítésével a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717). Ezután, azt a hitelesítési kulcsok egyikével kapott Regisztrálás a **New-AzDataFactoryV2IntegrationRuntimeKey** leírtak szerint a parancsmag a [oktatóanyag](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Hozzon létre új saját üzemeltetésű integrációs modul minden egyes csomópont társíthatunk nincs szükségünk. A saját üzemeltetésű integrációs modul telepítése egy másik gépen, és ugyanazt a hitelesítési kulcs használatával regisztrálja. 

> [!NOTE]
> Mielőtt hozzáadja a magas rendelkezésre állás és méretezhetőség érdekében egy másik csomópontra, győződjön meg arról, hogy a **az intranetes távoli hozzáférés** első csomópontjára engedélyezve van (**Microsoft Integration Runtime konfigurációkezelőjének**  >  **Beállítások** > **az intranetes távoli hozzáférés**). 

### <a name="scale-considerations"></a>Méretezési szempontok

#### <a name="scale-out"></a>Horizontális felskálázás

Ha alacsony a rendelkezésre álló memóriát a saját üzemeltetésű integrációs modul és a CPU-használata túl magas, új csomópont hozzáadása segít a horizontális felskálázást a terhelés gépek között. Ha a tevékenység sikertelen, mert éppen időtúllépés, vagy mert a saját üzemeltetésű integrációs modul csomópontja kapcsolat nélküli üzemmódban, ha egy csomópont hozzáadása az átjáró nyújt segítséget.

#### <a name="scale-up"></a>Vertikális felskálázás

A rendelkezésre álló memória és a Processzor nem használhatók jól, de az egyidejű feladatok végrehajtásának hamarosan eléri a korlátot, érdemes a vertikális felskálázáshoz, amely képes futni a csomóponton egyidejű feladatok számának növelése. Érdemes azt is, amikor a tevékenységek időtúllépésekbe ütközzenek, mert a saját üzemeltetésű integrációs modul túl van terhelve vertikális. Ahogy az az alábbi képen is látható, növelheti a maximális kapacitás egy csomópont:  

![Egyidejű feladatok, amely képes futni a csomóponton növelése](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>A TLS/SSL-tanúsítvány követelményei

Az alábbiakban az integrációsmodul-csomópontot integrációs közötti kommunikáció tehető biztonságossá használt TLS/SSL-tanúsítvány követelményei:

- A tanúsítványnak kell lennie egy nyilvánosan megbízható X509 v3 tanúsítvány. Azt javasoljuk, hogy használja-e nyilvános (partnereknek) által kiállított tanúsítványokat hitelesítésszolgáltató (CA).
- Minden egyes az integration runtime csomópontja meg kell bíznia a tanúsítványt.
- Tulajdonos alternatív nevére (SAN) tanúsítványok nem ajánlott, mert az csak az utolsó SAN elem fogja használni, és minden más figyelmen kívül hagyja az aktuális korlátozásai miatt. Például, ha SAN-tanúsítvány rendelkezik azon San lesznek **node1.domain.contoso.com** és **node2.domain.contoso.com**, használhatja ezt a tanúsítványt csak egy gép, amelynek a teljes tartománynév  **node2.domain.contoso.com**.
- A tanúsítvány támogatja az SSL-tanúsítványokra vonatkozó Windows Server 2012 R2 által támogatott bármely kulcsának mérete.
- CNG-kulccsal használó tanúsítványok nem támogatottak.  

> [!NOTE]
> Ez a tanúsítvány használható titkosításához a saját üzemeltetésű integrációs modul csomóponton használt portok **csomópontok közötti kommunikáció** (a szinkronizálási állapot), és miközben **PowerShell-lel társított szolgáltatáshoz parancsmag hitelesítőadat-beállítás**a helyi hálózaton belül. Javasoljuk, hogy ezt a tanúsítványt használja, ha a magánhálózati környezetben nem biztonságos, vagy ha szeretné, valamint a magánhálózaton lévő csomópontok közötti kommunikáció védelméhez. Adatok áthelyezése a saját üzemeltetésű integrációs modul átvitel pedig más adattárakban mindig bekövetkezik, titkosított csatornán, attól függetlenül, ezt a tanúsítványt, vagy nincs beállítva. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>A saját üzemeltetésű integrációs modul osztanak meg több adat-előállítók

Használhat egy meglévő saját üzemeltetésű integrációs modul infrastruktúra, amely korábban már beállított egy adat-előállítóban. Ez lehetővé teszi, hogy hozzon létre egy *társított saját üzemeltetésű integrációs modul* a másik egy meglévő hivatkozva factory saját üzemeltetésű IR (megosztott).

Szeretne megosztani egy saját üzemeltetésű integrációs modult használja a Powershellt, tekintse meg a [megosztott saját üzemeltetésű integrációs modul létrehozása az Azure Data Factoryban a PowerShell-lel](create-shared-self-hosted-integration-runtime-powershell.md).

A tizenkét perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminológia

- **Integrációs modul megosztott**: Az eredeti saját üzemeltetésű integrációs modul, amely a fizikai infrastruktúra fut-e.  
- **Integrációs modul társított**: Az integrációs modul egy másik hivatkozó megosztott IR. Ez egy logikai integrációs modul és a egy másik saját üzemeltetésű integrációs modul (megosztott)-infrastruktúrát használja.

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Magas szintű lépései egy csatolt saját üzemeltetésű integrációs modul létrehozása

1. A saját üzemeltetésű integrációs modul megosztását, az engedélyeket a data factoryhoz, amelyben szeretné létrehozni a csatolt bemutathatja 

   ![A Megosztás lapon engedély megadása gombra](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Engedélyek hozzárendelése a kiválasztott beállításokat](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Vegye figyelembe a megosztását a saját üzemeltetésű integrációs modul erőforrás-Azonosítóját.

   ![Az erőforrás-azonosító helye](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. Az adat-előállítóban, amelyhez engedélyeket kapnak hozzon létre egy új helyi Vezérlésű integrációs modul (csatolt), és adja meg az erőforrás-azonosítója.

   ![A csatolt saját üzemeltetésű integrációs modul létrehozására szolgáló gombok](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Mezők nevét és az erőforrás-azonosítóhoz](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Figyelés 

- **Megosztott integrációs modul**

  ![Egy megosztott integrációs modul megkeresése szolgáló kiválasztások](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![A figyelés lapon](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Társított integrációs modul**

  ![Keresés, a társított integrációs modul szolgáló kiválasztások](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![A figyelés lapon](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>A saját üzemeltetésű integrációs modul megosztási ismert korlátozások

* Az adat-előállítót, társított IR létrehozott rendelkeznie kell egy [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Alapértelmezés szerint az Azure Portalon létrehozott adat-előállítók vagy PowerShell-parancsmagokkal rendelkezik egy olyan MSI Csomaghoz implicit módon létrehozva. De ha egy adat-előállító jön létre egy Azure Resource Manager-sablon vagy az SDK-t, a **identitás** tulajdonság explicit módon kell állítani annak érdekében, hogy az Azure Resource Manager létrehoz egy adat-előállítót, amely tartalmazza az MSI. 

* Az Azure Data Factory .NET SDK, amely támogatja ezt a szolgáltatást az 1.1.0-s verzió vagy újabb.

* Engedélyt adni a felhasználónak a tulajdonosi szerepkör vagy az örökölt tulajdonosi szerepkör a data factoryban, ahol a megosztott integrációs modul létezik.

* Csak a Data Factoryt az Azure Active Directory ugyanazon bérlőn belüli megosztási szolgáltatás működik.

* Az Active Directory [vendégfelhasználók](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews), a keresési funkciókat (az összes adat-előállítók listázása a keresési kulcsszó használatával) a felhasználói felületen [nem működik](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Mindaddig, amíg a vendégfelhasználót a data Factory a tulajdonos, meg is oszthatják az integrációs modul nélkül a keresési funkciókat közvetlenül írja be az MSI a data Factory, amellyel az integrációs modul kell osztható meg, de a **engedély hozzárendelése** szövegmező és kiválasztásával **Hozzáadás** az Azure Data Factory felhasználói felületén. 

  > [!NOTE]
  > Ez a funkció csak az Azure Data Factory v2-ben érhető el. 

## <a name="notification-area-icons-and-notifications"></a>Értesítési terület ikonok és értesítések

Ha az ikon vagy az üzenet az értesítési területen lévő viszi a kurzort, megtalálhatja a saját üzemeltetésű integrációs modul állapotának részleteit.

![Értesítések az értesítési területen](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portok és tűzfal
Nincsenek kell figyelembe venni két tűzfal: a *vállalati tűzfal* fut a szervezet a központi útválasztón és a *Windows tűzfal* konfigurálva egy démon a helyi számítógépen, ahol a saját üzemeltetésű integrációs modul telepítve van.

![Tűzfal](media/create-self-hosted-integration-runtime/firewall.png)

Jelenleg a *vállalati tűzfal* szintű, konfigurálnia kell a következő tartományok és a kimenő portokat:

Tartománynevek | Portok | Leírás
------------ | ----- | ------------
*.servicebus.windows.net | 443 | A háttér-adatátviteli szolgáltatás való kommunikációhoz használatos
*.core.windows.net | 443 | Azure Blob storage (Ha be van állítva) szolgáltatáson keresztül szakaszos másolás használt
*.frontend.clouddatahub.net | 443 | A háttér-adatátviteli szolgáltatás való kommunikációhoz használatos
download.microsoft.com | 443 | A frissítések letöltéséhez használt

Jelenleg a *Windows tűzfal* szintet (számítógép), ezeket a kimenő portokat általában engedélyezve vannak. Ha nem, konfigurálhatja, hogy a tartományok portok ennek megfelelően a egy saját üzemeltetésű integrációs modult tartalmazó számítógépen.

> [!NOTE]
> A forrás- és fogadóként alapján, előfordulhat, hogy rendelkezik az engedélyezési lista további tartományokkal és a vállalati tűzfal vagy a Windows tűzfalon a kimenő portok.
>
> Az egyes felhőalapú adatbázisok (például Azure SQL Database és az Azure Data Lake) szüksége lehet a tűzfal-konfigurációt a saját üzemeltetésű integrációs modul gépek IP-címeket.

### <a name="copy-data-from-a-source-to-a-sink"></a>Adatok másolása a forrásból a fogadóba másolt
Győződjön meg arról, hogy a tűzfalszabályok megfelelően engedélyezve vannak a vállalati tűzfalon, a saját üzemeltetésű integrációs modult tartalmazó számítógépen, a Windows tűzfalat, és az adattároló magát. Ezek a szabályok lehetővé teszi, hogy a saját üzemeltetésű integrációs modul csatlakozni mindkét forrás és fogadó sikeresen. Egyes adattároló, amely részt vesz a másolási művelet szabályok engedélyezése.

Ha például szeretné másolni a helyszíni adattárolókból az Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó, tegye a következőket:

1. A 1433-as porton figyeli a Windows tűzfal és a vállalati tűzfalon a kimenő TCP-kommunikációt engedélyezze.
2. A saját üzemeltetésű integrációs modul gép IP-cím hozzáadása az engedélyezett IP-címek listája az Azure SQL database-tűzfal beállításainak konfigurálása.

> [!NOTE]
> A tűzfal nem engedélyezi a 1433-as kimenő porton, a saját üzemeltetésű integrációs modul közvetlenül az Azure SQL database nem lehet hozzáférni. Ebben az esetben használhatja egy [szakaszos Másolás](copy-activity-performance.md) Azure SQL Database és az Azure SQL Data warehouse-bA. Ebben a forgatókönyvben a adatáthelyezéskor kellene a csak HTTPS (443-as porton).


## <a name="proxy-server-considerations"></a>Proxy server kapcsolatos szempontok
Ha a vállalati hálózati környezet az egy proxykiszolgálón keresztül csatlakozik az internetre, konfigurálja a megfelelő proxykiszolgáló-beállításokat kívánja használni a saját üzemeltetésű integrációs modult. A proxy állíthatja be a kezdeti regisztrációs fázis során.

![Adja meg a proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

A saját üzemeltetésű integrációs modul csatlakozni a felhőszolgáltatáshoz a proxykiszolgálót használ. Válassza ki **módosítás hivatkozásra** kezdeti beállítás során. A proxy-beállítás párbeszédpanel jelenik meg.

![Proxy beállítása](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Három konfigurációs lehetőség áll rendelkezésre:

- **Proxy használatának mellőzése**: A saját üzemeltetésű integrációs modul nem explicit módon használja bármely proxy a cloud serviceshez való csatlakozáshoz.
- **Rendszerproxy használata**: A saját üzemeltetésű integrációs modult használja a proxybeállítást a diahost.exe.config és diawp.exe.config konfigurált. Nincs proxy diahost.exe.config és diawp.exe.config van konfigurálva, a saját üzemeltetésű integrációs modul kapcsolódik közvetlenül a proxy áthaladás nélkül a felhőszolgáltatáshoz.
- **Egyéni proxy használatát**: A beállítás használata a saját üzemeltetésű integrációs modul diahost.exe.config és diawp.exe.config konfigurációk használata helyett a HTTP-proxy konfigurálása. **Cím** és **Port** szükség. **Felhasználónév** és **jelszó** megadása nem kötelező, attól függően, a proxy hitelesítési beállítást. Minden beállítás a saját üzemeltetésű integrációs modul a Windows DPAPI titkosítva, és a gép helyben tárolja.

Az integration runtime gazdaszolgáltatása automatikusan újraindul, miután mentette a frissített proxybeállításokat.

Miután a saját üzemeltetésű integrációs modul sikeresen regisztrálva lett, ha szeretné megtekintheti vagy frissítheti a webproxy beállításai, használja az Integration Runtime konfigurációkezelőjének.

1. Nyissa meg **Microsoft Integration Runtime konfigurációkezelőjének**.
2. Váltson a **Settings** (Beállítások) lapra.
3. Válassza ki a **módosítása** hivatkozásra a **HTTP-Proxy** szakaszban megnyitásához a **állítsa be a HTTP-Proxy** párbeszédpanel bezárásához.
4. Kattintson a **Tovább** gombra. Ezután megjelenik egy figyelmeztetés, amely a proxybeállítást mentése engedélyt kér, és indítsa újra az integration runtime gazdaszolgáltatása.

Megtekintheti, és a HTTP-proxy frissítése a Configuration Manager eszközzel.

![Nézet proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Az NTLM-hitelesítés proxykiszolgálót állít be, ha az integration runtime Gazdaszolgáltatásának a tartományi fiók alatt fut. Ha módosítja a később a tartományi fiók jelszavát, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, és ennek megfelelően indítsa újra. Ez a követelmény miatt javasoljuk, hogy, dedikált tartományi fiók használatával, amely nem igényel, hogy a jelszó gyakran frissíteni a proxykiszolgáló eléréséhez.

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló-beállításainak konfigurálása

Ha a **rendszerproxy használata** beállítása a HTTP-proxy, a saját üzemeltetésű integrációs modult használja a proxybeállítást diahost.exe.config és diawp.exe.config. Nincs proxy diahost.exe.config és diawp.exe.config megadva, a saját üzemeltetésű integrációs modul kapcsolódik közvetlenül a proxy áthaladás nélkül a felhőszolgáltatáshoz. Az alábbi eljárás útmutatást ad a diahost.exe.config fájl frissítéséhez:

1. A Fájlkezelőben győződjön meg a C:\Program Files\Microsoft integrációs Runtime\3.0\Shared\diahost.exe.config biztonsági mentése az eredeti fájlt egy biztonságos példányát.
2. Nyissa meg a Notepad.exe rendszergazdaként, és nyissa meg a C:\Program Files\Microsoft integrációs Runtime\3.0\Shared\diahost.exe.config szövegfájl. Az alapértelmezett címke található system.net az alábbi kódban látható módon:

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

    Adja meg a szükséges beállításokat, például a további tulajdonságok megengedettek a proxy címkére `scriptLocation`. Lásd: [elem (hálózati beállítások) proxy](https://msdn.microsoft.com/library/sa91de1e.aspx) szintaxis.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. A konfigurációs fájlt az eredeti helyre mentse. Ezután indítsa újra a saját üzemeltetésű integrációs modul gazdagép szolgáltatás, amely szerzi be a módosításokat. 

   Indítsa újra a szolgáltatást, használja a Vezérlőpultról a szolgáltatások kisalkalmazást. Az Integration Runtime konfigurációkezelőjének, válassza ki, vagy a **szolgáltatás leállítása** gombra, és válassza ki **szolgáltatás indítása**. 
   
   Ha a szolgáltatás nem indul el, valószínű, hogy az alkalmazás konfigurációs fájljának, amely szerkesztettek egy XML-címke helytelen szintaxis hozzáadva.

> [!IMPORTANT]
> Ne felejtse el frissíteni a diahost.exe.config és diawp.exe.config is.

Szükség is győződjön meg arról, hogy a Microsoft Azure szerepel-e a vállalati engedélyezési listáján. Érvényes Microsoft Azure IP-címek listájának letöltheti a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Tűzfal és proxy-kiszolgálóval kapcsolatos problémák lehetséges tünetek
Ha a következő hasonló hibákat észlel, akkor valószínű, a tűzfal vagy proxy kiszolgálóra, amely letiltja a Data Factory kapcsolódjon a saját üzemeltetésű integrációs modul önmaga hitelesítéséhez a helytelen konfiguráció miatt. Győződjön meg arról, hogy a tűzfal és proxy-kiszolgáló megfelelően vannak-e konfigurálva, tekintse meg az előző szakaszban.

* Ha a saját üzemeltetésű integrációs modult regisztrálni próbál, a következő hibaüzenetet kapja: "Nem sikerült regisztrálni az Integration Runtime ezen csomópontja! Győződjön meg arról, hogy a hitelesítési kulcs érvényes, és az integrációs szolgáltatás gazdaszolgáltatása fut ezen a számítógépen."
* Integration Runtime konfigurációkezelőjének megnyitásakor megjelenik egy állapotát **leválasztott** vagy **csatlakozás**. A Ha megtekintése Windows-eseménynaplók, **Eseménynapló** > **alkalmazás- és szolgáltatásnaplók** > **Microsoft Integration Runtime**, Ehhez hasonló hibaüzenetek jelennek meg:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Az intranetes távoli hozzáférés engedélyezése  
Ha a hitelesítőadat-kezelő alkalmazás vagy a PowerShell használatával eltérő, ahol a saját üzemeltetésű integrációs modul telepítve van (a hálózatban) egy másik gépről hitelesítő adatok titkosításához, engedélyezheti a **távoli hozzáférés intranetről**lehetőséget. Ha Powershellt vagy a hitelesítőadat-kezelő alkalmazás ugyanazon a gépen hitelesítő adatainak titkosítása a saját üzemeltetésű integrációs modul telepítési helyéül, nem engedélyezheti **távoli hozzáférés intranetről**.

Engedélyeznie kell a **távoli hozzáférés intranetről** a magas rendelkezésre állás és méretezhetőség érdekében egy másik csomópont hozzáadása előtt.  

Saját üzemeltetésű integrációs modul a telepítés során (verzió 3.3.xxxx.x később), alapértelmezés szerint letiltja a saját üzemeltetésű integrációs modul telepítése **távoli hozzáférés intranetről** a saját üzemeltetésű integrációs modul gépen.

Ha egy külső tűzfalat használ, manuálisan megnyithatja 8060 (vagy felhasználó által konfigurált port). Ha tűzfal probléma van a saját üzemeltetésű integrációs modul beállítása közben, próbálja meg a saját üzemeltetésű integrációs modul telepítése nélkül a tűzfal konfigurálásáról a következő paranccsal:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> A hitelesítőadat-kezelő alkalmazás még nem érhető el az Azure Data Factory V2 hitelesítő adatok titkosításához.  

Ha nem kíván nyissa meg a saját üzemeltetésű integrációs modul gépen 8060 portot, a mechanizmus a hitelesítő adatok beállítása alkalmazás eltérő segítségével konfigurálhatja az adattár hitelesítő adatait. Használhatja például a **New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell-parancsmagot.


## <a name="next-steps"></a>További lépések
Tekintse meg a részletes útmutatást a következő oktatóanyaggal: [Oktatóanyag: Másolás a helyszíni adatok felhőbe](tutorial-hybrid-copy-powershell.md).

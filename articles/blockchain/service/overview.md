---
title: Az Azure Blockchain szolgáltatás áttekintése
description: Az Azure Blockchain szolgáltatás áttekintése
ms.date: 03/12/2020
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: b9f5deb501fb93327fa5d5cfcfd5bb583ed6135e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79205072"
---
# <a name="what-is-azure-blockchain-service"></a>Mi az az Azure Blockchain Service?

Az Azure Blockchain Service egy teljes körűen felügyelt főkönyvi szolgáltatás, amely lehetővé teszi a felhasználók számára, hogy az Azure-ban nagy méretekben növekedjenek és működtessenek blokklánc-hálózatokat. Az Azure Blockchain Service egységes vezérlést biztosít mind az infrastruktúra-kezelés, mind a blokklánc-hálózat szabályozása számára:

* Egyszerű hálózati telepítés és műveletek
* Beépített konzorciumkezelés
* Intelligens szerződések kidolgozása ismerős fejlesztési eszközökkel

Az Azure Blockchain szolgáltatás több főkönyvi protokoll t támogatja. Jelenleg az [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) konszenzusos mechanizmus alkalmazásával támogatja az Ethereum [Quorum](https://www.goquorum.com/) főkönyvét.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Az alkalmazásfejlesztésre és az üzleti logikára összpontosíthat ahelyett, hogy időt és erőforrásokat különítene meg a virtuális gépek és az infrastruktúra kezelésére. Ezenkívül tovább fejlesztheti alkalmazását az Ön által választott nyílt forráskódú eszközökkel és platformokkal, hogy új készségek elsajátítása nélkül is szállíthassa megoldásait.

## <a name="network-deployment-and-operations"></a>Hálózat üzembe helyezése és műveletei

Az Azure Blockchain szolgáltatás üzembe helyezése az Azure Portalon, az Azure CLI-n keresztül vagy a Visual Studio-kódon keresztül történik az Azure Blockchain bővítmény használatával. A központi telepítés egyszerűsödik, beleértve a tranzakciós és az érvényesítő csomópontok kiépítését, az Azure virtuális hálózatokat a biztonsági elkülönítéshez, valamint a szolgáltatás által felügyelt tárolást.  Emellett egy új blokklánc-tag telepítésekor a felhasználók konzorciumot is létrehoznak, vagy csatlakoznak hozzá.  A konzorciumok lehetővé teszik, hogy a különböző Azure-előfizetésekben lévő több fél biztonságosan kommunikáljon egymással egy megosztott blokkláncon.  Ez az egyszerűsített telepítés napokról percekre csökkenti a blokklánc-hálózati telepítést.

### <a name="performance-and-service-tiers"></a>Teljesítmény- és szolgáltatásszintek

Az Azure Blockchain Szolgáltatás két szolgáltatási szintet kínál: *alapszintű* és *standard.* Minden egyes szint különböző teljesítményt és képességeket kínál a könnyű fejlesztési és a számítási feladatok teszteléséhez a masszívan skálázott éles blokklánc-telepítésekig. Használja az *alapszintű* szintet a fejlesztéshez, teszteléshez és a fogalmak igazolásához. Használja a *standard* szint éles szintű központi telepítések. Mindkét szint legalább egy tranzakciócsomópontot és egy érvényesítő csomópontot (Alapszintű) vagy két validátor-csomópontot (Standard) tartalmaz. 

![Árképzési szintek](./media/overview/pricing-tiers.png)

Amellett, hogy két validator csomópontot kínál, a *standard* szint 2 *virtuális magot* biztosít minden tranzakcióhoz és validator csomóponthoz, míg az alapszintű szint 1 virtuálismagos konfigurációt kínál.  2 virtuális mag felajánlásával a tranzakciós és érvényesítő csomópontokhoz, 1 virtuális mag is felsodható a Kvórum főkönyvhöz, míg a fennmaradó 1 virtuális mag használható más infrastruktúrával kapcsolatos szolgáltatásokhoz, biztosítva az optimális teljesítményt az éles blokklánc-számítási feladatokhoz. A díjszabással kapcsolatos további információkért lásd: [Azure Blockchain Service díjszabása.](https://azure.microsoft.com/pricing/details/blockchain-service)

### <a name="security-and-maintenance"></a>Biztonság és karbantartás

Az első blokklánc-tag kiépítése után további tranzakciócsomópontokat adhat hozzá a taghoz.  Alapértelmezés szerint a tranzakciócsomópontok tűzfalszabályokon keresztül védettek, és a hozzáféréshez konfigurációra van szükség.  Emellett minden tranzakciós csomópont titkosítja a mozgásban lévő adatokat a TLS-en keresztül.  A tranzakciós csomópont-hozzáférés biztonságossá tétele több lehetőség gel rendelkezik, beleértve a tűzfalszabályokat, az alapfokú hitelesítést, a hozzáférési kulcsokat és az Azure Active Directory-integrációt. További információt a [Tranzakciócsomópontok konfigurálása](configure-transaction-nodes.md) és [az Azure Active Directory-hozzáférés konfigurálása](configure-aad.md)című témakörben talál.

Felügyelt szolgáltatásként az Azure Blockchain Szolgáltatás biztosítja, hogy a blokklánc-tag csomópontjai javításra kerülnek a legújabb gazdaoperációs rendszer- és főkönyvi szoftververem-frissítésekkel, amelyek magas rendelkezésre állásra vannak konfigurálva (csak standard szinten), így a DevOps nagy része ki van kapcsolva. hagyományos IaaS blokklánc-csomópontokhoz szükséges.  A javításokkal és frissítésekkel kapcsolatos további információkért tekintse meg az [Azure Blockchain Service támogatott főkönyvi verzióit.](ledger-versions.md)

### <a name="monitoring-and-logging"></a>Monitorozás és naplózás

Emellett az Azure Blockchain Service gazdag metrikákat biztosít az Azure Monitor szolgáltatáson keresztül, amelyek betekintést nyújtanak a csomópontok PROCESSZOR-, memória- és tárhelyhasználatába.  Az Azure Monitor hasznos betekintést nyújt a blokklánc hálózati tevékenységekbe, például a tranzakciókba és a bányászott blokkokba, a tranzakciós várólista mélységébe és az aktív kapcsolatokba.  Metrikák testreszabható, hogy a blockchain alkalmazás számára fontos elemzési nézeteket.  Emellett küszöbértékek segítségével adható meg a riasztások, amelyek lehetővé teszik a felhasználók számára, hogy olyan műveleteket, például e-mail vagy szöveges üzenet küldése, logikai alkalmazás futtatása, Az Azure-függvény vagy egy egyéni legdefiniált webhook.

![Mérőszámok](./media/overview/metrics.png)

Az Azure Log Analytics segítségével a felhasználók megtekinthetik a Kvórum főkönyvhöz kapcsolódó naplókat, vagy más fontos információkat, például a tranzakciócsomópontokhoz való megkísérelt kapcsolatokat.

## <a name="built-in-consortium-management"></a>Beépített konzorciumkezelés

Az első blokklánc-tag telepítésekor vagy csatlakozik, vagy hozzon létre egy új konzorciumot.  A konzorcium egy logikai csoport, amely a többrésztvevős folyamatban lebonyolító blokklánc-tagok közötti irányítás és kapcsolat kezelésére szolgál.  Az Azure Blockchain Service beépített cégirányítási vezérlőket biztosít előre meghatározott intelligens szerződések en keresztül, amelyek meghatározzák, hogy a konzorcium tagjai milyen műveleteket hajthatnak végre.  Ezeket a cégirányítási vezérlőket szükség szerint testre szabhatja a konzorcium rendszergazdája. Amikor új konzorciumot hoz létre, a blockchain tagja a konzorcium alapértelmezett rendszergazdája, lehetővé téve, hogy más feleket is meghívjon a konzorciumhoz.  Csak akkor csatlakozhat konzorciumhoz, ha korábban már meghívást kapott.  Konzorciumhoz való csatlakozáskor a blokklánc-tag a konzorcium rendszergazdája által bevezetett irányítási vezérlők hatálya alá tartozik.

![Konzorciumirányítás](./media/overview/consortium.png)

A konzorciumkezelési műveletek, például a tagok hozzáadása és eltávolítása egy konzorciumból a PowerShellen és a REST API-n keresztül érhető el. A szilárdságalapú intelligens szerződések módosítása és benyújtása helyett programozott módon kezelheti a konzorciumokat a közös interfészek használatával. További információt a [konzorciumkezelés című témakörben talál.](consortium.md)

## <a name="develop-using-familiar-development-tools"></a>Fejlesztés ismert fejlesztési eszközökkel

A nyílt forráskódú Quorum Ethereum főkönyv alapján ugyanúgy fejleszthet alkalmazásokat az Azure Blockchain Szolgáltatáshoz, mint a meglévő Ethereum-alkalmazások esetében. A vezető iparági partnerekkel együttműködve az Azure Blockchain Development Kit Visual Studio Code bővítmény lehetővé teszi a fejlesztők számára, hogy olyan ismerős eszközöket használjanak ki, mint a Szarvasgomba Suite, intelligens szerződések et hozhatnak létre. Az Azure Blockchain Development Kit bővítmény használatával a fejlesztők létrehozhatnak, vagy csatlakozhatnak a konzorciumhoz, így egyetlen IDE-ből hozhat létre és helyezhet üzembe intelligens szerződéseket. Az Azure Blockchain Visual Studio Code bővítmény használatával létrehozhat vagy csatlakozhat egy meglévő konzorciumhoz, így egyetlen IDE-ből hozhat létre és helyezhet üzembe intelligens szerződéseket. További információ: [Azure Blockchain Development Kit a VS Code piactéren](https://aka.ms/vscodebcextension) és az [Azure Blockchain Development Kit felhasználói útmutató.](https://aka.ms/vscodebcextensionwiki)

## <a name="publish-blockchain-data"></a>Blokklánc-adatok közzététele

A Blockchain Data Manager for Azure Blockchain Service rögzíti, átalakítja és kézbesíti az Azure Blockchain Service tranzakciós adatait az Azure Event Grid-témakörökbe, amelyek megbízható és skálázható blockchain főkönyvi integrációt biztosítanak az Azure-szolgáltatásokkal. A Blockchain Data Manager segítségével integrálhatja az off-chain alkalmazásokat és adattárakat. További információ: [Blockchain Data Manager for Azure Blockchain Service](data-manager.md).

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Blockchain hírekért látogasson el az [Azure Blockchain blogjára,](https://azure.microsoft.com/blog/topics/blockchain/) hogy naprakész legyen az Azure Blockchain mérnöki csapatának blockchain szolgáltatásajánlatairól és információiról.

A termékvisszajelzés biztosításához vagy új funkciók igényléséhez szavazzon egy ötletre az [Azure blockchain visszajelzési fórumán](https://aka.ms/blockchainuservoice)keresztül.

### <a name="community-support"></a>Közösségi támogatás

Vegye fel a kapcsolatot a Microsoft mérnökeivel és az Azure Blockchain közösségi szakértőivel.

* [Azure Blockchain MSDN fórum](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/AzureBlockchainService)

## <a name="next-steps"></a>További lépések

Első lépésekhez próbálkozzon egy rövid útmutatóval, vagy további részleteket ezekből az erőforrásokból.
* [Hozzon létre egy blockchain tagot az Azure Portalon,](create-member.md) vagy [hozzon létre egy blockchain tagot az Azure CLI használatával](create-member-cli.md)
* A költségösszehasonlításokat és a számológépeket az [árképzési oldalon](https://azure.microsoft.com/pricing/details/blockchain-service)találja.
* Az első alkalmazás létrehozása az [Azure Blockchain fejlesztői készletével](https://github.com/Azure-Samples/blockchain-devkit)
* Az Azure Blockchain VSCode bővítmény [felhasználói útmutatója](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)

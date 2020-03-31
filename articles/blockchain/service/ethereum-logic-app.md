---
title: Az Ethereum Blockchain-összekötő használata az Azure Logic Apps alkalmazásokkal – Azure Blockchain szolgáltatás
description: Az Ethereum Blockchain-összekötő és az Azure Logic Apps intelligens szerződésfunkciók indításához és az intelligens szerződéseseményekre való reagáláshoz használható.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: chrisseg
ms.openlocfilehash: 4a9acfd6098ed45fd92c7e3047b5d1446eeddbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325221"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Az Ethereum Blockchain-összekötő használata az Azure Logic Apps alkalmazásokkal

Az [Ethereum Blockchain-összekötő](https://docs.microsoft.com/connectors/blockchainethereum/) és az [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) intelligens szerződésműveletek végrehajtásához és az intelligens szerződéseseményekre való reagáláshoz használható. Tegyük fel például, hogy egy REST-alapú mikroszolgáltatást szeretne létrehozni, amely egy blokklánc főkönyvből származó információkat ad vissza. Egy logikai alkalmazás használatával elfogadhatja a blokklánc-főkönyvben tárolt adatokat lekérdező HTTP-kérelmeket.

## <a name="prerequisites"></a>Előfeltételek

Töltse ki az opcionális előfeltételként használható [rövid útmutatót: A Visual Studio-kód dal csatlakozhat egy Azure Blockchain Service konzorciumi hálózathoz.](connect-vscode.md) A rövid útmutatók segítségével telepítheti az [Azure Blockchain Development Kit for Ethereum-ot,](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) és üzembe helyezheti a blokklánc-fejlesztési környezetet.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Az Azure Logic Apps segítségével ütemezheti és automatizálhatja az üzleti folyamatokat és munkafolyamatokat, amikor integrálnia kell a rendszereket és szolgáltatásokat. Először hozzon létre egy logikát, amely az Ethereum Blockchain összekötőt használja.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Integráció** > **Logikai alkalmazás** lehetőséget.
1. A **Logikai alkalmazás létrehozása**csoportban adja meg a logikai alkalmazás létrehozásának helyével kapcsolatos részleteket. Miután elkészült, válassza a **Létrehozás gombot.**

    A logikai alkalmazások létrehozásáról további információt az [Automatikus munkafolyamatok létrehozása az Azure Logic Apps alkalmazásokkal című témakörben talál.](../../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Miután az Azure üzembe helyezte az alkalmazást, válassza ki a logikai alkalmazás erőforrás.
1. A Logic Apps Designer **Sablonok csoportban**válassza az **Üres logikai alkalmazás lehetőséget.**

Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot.

Az Ethereum Blockchain összekötő egy eseményindítóval és több műveletel. A ttól függ, hogy melyik eseményindítót vagy műveletet használja a forgatókönyvtől.

Ha a munkafolyamat:

* Eseményindítók, amikor egy esemény történik a blockchain, [Használja az eseményindító](#use-the-event-trigger).
* Lekérdezések vagy telepíti az intelligens szerződés, [Műveletek használata.](#use-actions)
* Egy gyakori forgatókönyvet követ, [a Munkafolyamat létrehozása a fejlesztői készlet használatával.](#generate-a-workflow)

## <a name="use-the-event-trigger"></a>Az eseményindító használata

Használja Ethereum Blockchain eseményesemény eseményindítók, ha azt szeretné, hogy egy logikai alkalmazás fut, miután egy intelligens szerződés esemény történik. Például egy intelligens szerződésfunkció megnevezésekekénél e-mailt szeretne küldeni.

1. A Logic Apps Designer ben válassza ki az Ethereum Blockchain összekötőt.
1. Az **Eseményindítók** lapon válassza az **Intelligens szerződésesemény bekövetkeztekor**lehetőséget.
1. [API-kapcsolat](#create-an-api-connection) módosítása vagy létrehozása az Azure Blockchain Szolgáltatással.
1. Adja meg az eseményeket ellenőrizni kívánt intelligens szerződés részleteit.

    ![Logic Apps Designer eseményindító-tulajdonságokkal](./media/ethereum-logic-app/event-properties.png)

    | Tulajdonság | Leírás |
    |----------|-------------|
    | **Szerződés ABI** | A szerződéses alkalmazás bináris felülete (ABI) határozza meg az intelligens szerződés felületeket. További információ: [Get the contract ABI](#get-the-contract-abi). |
    | **Intelligens szerződés címe** | A szerződés címe az intelligens szerződés célcíme az Ethereum blokkláncon. További információ: [Get the contract address](#get-the-contract-address). |
    | **Esemény neve** | Válassza ki az ellenőrizni kívánt intelligens szerződéseseményt. Az esemény elindítja a logikai alkalmazást. |
    | **Időköz** és **gyakoriság** | Adja meg, hogy milyen gyakran szeretné ellenőrizni az eseményt. |

1. Kattintson a **Mentés** gombra.

A logikai alkalmazás befejezéséhez hozzáadhat egy új lépést, amely az Ethereum Blockchain eseményindító alapján hajt végre egy műveletet. Például küldjön egy e-mailt.

## <a name="use-actions"></a>Műveletek használata

Használja az Ethereum Blockchain műveleteket, ha azt szeretné, hogy egy logikai alkalmazás végrehajtson egy műveletet a blokklánc főkönyvi. Például létre szeretne hozni egy REST-alapú mikroszolgáltatást, amely intelligens szerződésfüggvényt hív meg, amikor egy LOGIKAi alkalmazáshttp-kérelmet küld.

Összekötő műveletek eseményindítót igényel. Használhatja az Ethereum Blockchain-összekötő művelet, mint a következő lépés után egy eseményindító, például egy HTTP-kérelem eseményindító egy mikroszolgáltatás.

1. A Logic Apps Designer ben válassza az **Új lépés** az eseményindítót követően lehetőséget.
1. Válassza ki az Ethereum Blockchain csatlakozót.
1. A **Műveletek** lapon válassza ki az elérhető műveletek egyikét.

    ![Logic Apps Designer műveletek tulajdonságaival](./media/ethereum-logic-app/action-properties.png)

1. [API-kapcsolat](#create-an-api-connection) módosítása vagy létrehozása az Azure Blockchain Szolgáltatással.
1. A választott művelettől függően adja meg az intelligens szerződés funkciójával kapcsolatos alábbi részleteket.

    | Tulajdonság | Leírás |
    |----------|-------------|
    | **Szerződés ABI** | A szerződés ABI határozza meg az intelligens szerződés interfészek. További információ: [Get the contract ABI](#get-the-contract-abi). |
    | **Szerződés bytecode** | A lefordított intelligens szerződés bytecode. További információ: [Get the contract bytecode](#get-the-contract-bytecode). |
    | **Intelligens szerződés címe** | A szerződés címe az intelligens szerződés célcíme az Ethereum blokkláncon. További információ: [Get the contract address](#get-the-contract-address). |
    | **Intelligens egyezményfüggvény neve** | Válassza ki a művelet intelligens egyezményfüggvényének nevét. A lista a szerződés abi-ban szereplő részletekből kerül feltöltésre. |

    Az intelligens egyezményfüggvény nevének kiválasztása után előfordulhat, hogy a függvényparaméterekhez szükséges mezők jelennek meg. Adja meg a forgatókönyvhöz szükséges értékeket vagy dinamikus tartalmat.

Most már használhatja a logikai alkalmazást. Amikor a logikai alkalmazás esemény aktiválódik, az Ethereum Blockchain művelet fut. Például egy HTTP-kérelem eseményindító fut egy Ethereum blockchain művelet lekérdezni egy intelligens szerződés állapotértékét. Ez a lekérdezés http-választ eredményez, amely az értéket adja vissza.

## <a name="generate-a-workflow"></a>Munkafolyamat létrehozása

Az Azure Blockchain Development Kit for Ethereum Visual Studio Code bővítmény logikai alkalmazás-munkafolyamatokat hozhat létre a gyakori forgatókönyvekhez. Négy forgatókönyv áll rendelkezésre:

* Adatközzététel Azure SQL Database-példányban
* Eseményközzététel az Azure Event Grid vagy az Azure Service Bus egy példányában
* Jelentés közzététele
* REST-alapú mikroszolgáltatás

 Az Azure Blockchain Development Kit szarvasgombát használ a blokklánc-fejlesztés egyszerűsítésére. Egy intelligens szerződésen alapuló logikai alkalmazás létrehozásához szarvasgomba-megoldásra van szükség az intelligens szerződéshez. Az Azure Blockchain Service konzorciumi hálózatához is szüksége van egy kapcsolatra. További információ: [A Visual Studio Code használata az Azure Blockchain Service konzorciumhálózati rövid útmutatóhoz való csatlakozáshoz című témakörben található.](connect-vscode.md)

A következő lépések például egy REST-alapú mikroszolgáltatás-logikai alkalmazást hoznak létre a **HelloBlockchain** intelligens szerződés gyorsindítása alapján:

1. A Visual Studio Kódkezelő oldalsávján bontsa ki a megoldás **szerződési** mappáját.
1. Kattintson a jobb gombbal **a HelloBlockchain.sol** elemre, és válassza a **mikroszolgáltatások generálása intelligens szerződésekhez parancsot** a menüből.

    ![A Visual Studio kódablaktáblája a Mikroszolgáltatások létrehozása intelligens szerződésekhez beállítással](./media/ethereum-logic-app/generate-logic-app.png)

1. A parancspalettán válassza a **Logic App**lehetőséget.
1. Adja meg a **szerződés címét**. További információ: [Get the contract address](#get-the-contract-address).
1. Válassza ki az Azure-előfizetés és erőforráscsoport a logikai alkalmazáshoz.

    A logikai alkalmazás konfigurációja és kódfájlok jönnek létre a **generáltLogicApp** könyvtárban.

1. Tekintse meg a **generáltLogicApp/HelloBlockchain** könyvtárat. Minden intelligens szerződésfüggvényhez, eseményhez és tulajdonsághoz van egy logikai alkalmazás JSON-fájlja.
1. Nyissa meg a **generatedLogicApp/HelloBlockchain/Service/property kapcsolót. RequestMessage.logicapp.json** fájlt, és másolja a tartalmát.

    ![JSON-fájl másolandó kóddal](./media/ethereum-logic-app/requestmessage.png)

1. A logikai alkalmazásban válassza a **Logikai alkalmazás kódnézetét.** Cserélje le a meglévő JSON-t a létrehozott JSON logikai alkalmazásra.

    ![Logikai alkalmazáskód nézet új lecserélt alkalmazáskóddal](./media/ethereum-logic-app/code-view.png)

1. Válassza **a Tervező** lehetőséget a tervezőnézetre való váltáshoz.
1. A logikai alkalmazás tartalmazza a forgatókönyv alapvető lépéseit. Azonban frissítenie kell az Ethereum Blockchain összekötő konfigurációs adatait.
1. Válassza ki a **Kapcsolatok lépést,** és módosítsa vagy [hozzon létre egy API-kapcsolatot](#create-an-api-connection) az Azure Blockchain Szolgáltatással.

    ![Tervezői nézet a Kapcsolatok kijelöléssel](./media/ethereum-logic-app/microservice-logic-app.png)

1. Most már használhatja a logikai alkalmazást. A REST-alapú mikroszolgáltatás teszteléséhez adjon ki HTTP POST-kérelmet a logikai alkalmazás kérelem URL-címére. Másolja a **HTTP-BEJEGYZÉS URL-cím** tartalmát a **HTTP-kérelem fogadása** kor lépésből.

    ![Logic Apps Designer ablaktábla a HTTP POST URL-címmel](./media/ethereum-logic-app/post-url.png)

1. HTTP POST-kérelem létrehozásához használja a cURL-t. Cserélje le a helyőrző * \<szövegHTTP POST\> URL-címét* az előző lépés URL-címére.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    A cURL parancs a logikai alkalmazás válaszát adja vissza. Ebben az esetben a válasz a **RequestMessage** intelligens szerződés függvény kimenete.

    ![Kódkimenet a RequestMessage intelligens egyezmény függvényéből](./media/ethereum-logic-app/curl.png)

A fejlesztői készlet használatáról további információt az [Azure Blockchain Development Kit for Ethereum wiki oldalán](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)talál.

## <a name="create-an-api-connection"></a>API-kapcsolat létrehozása

Az Ethereum Blockchain-összekötőhöz API-kapcsolat szükséges a blokklánchoz. Az API-összekötő több logikai alkalmazások is használhatja. Egyes tulajdonságok szükségesek, míg mások a forgatókönyvtől függenek.

> [!IMPORTANT]
> A blokkláncon végrehajtott tranzakciók létrehozásához titkos kulcs vagy fiókcím és jelszó szükséges. Csak egy hitelesítési forma szükséges. Nem kell megadnia mind a személyes kulcsot, sem a fiók adatait. Az egyezmények lekérdezéséhez nincs szükség tranzakcióra. Ha olyan műveleteket használ, amelyek lekérdezik a szerződés állapotát, a személyes kulcs vagy a fiókcím és a jelszó nem szükséges.

Az Azure Blockchain szolgáltatás egyik tagjával való kapcsolat beállításához az alábbi lista a forgatókönyvtől függően szükség lehet a lehetséges tulajdonságokra.

| Tulajdonság | Leírás |
|----------|-------------|
|**Kapcsolat neve** | Az API-kapcsolat neve. Kötelező. |
|**Ethereum RPC végpont** | Az Azure Blockchain Service tranzakciós csomópont HTTP-címe. Kötelező. További információ: [Az RPC-végpont beolvassa.](#get-the-rpc-endpoint) |
|**Titkos kulcs** | Ethereum fiók titkos kulcs. A tranzakciókhoz személyes kulcs vagy fiókcím és jelszó szükséges. További információ: [A személyes kulcs beszereznie.](#get-the-private-key) |
|**Fiók címe** | Az Azure Blockchain Service-tagfiók címe. A tranzakciókhoz személyes kulcs vagy fiókcím és jelszó szükséges. További információ: [A fiók címének beszereznie](#get-the-account-address). |
|**Fiók jelszava** | A fiók jelszava a tag létrehozásakor van beállítva. A jelszó alaphelyzetbe állításáról az [Ethereum-fiók című témakörben](consortium.md#ethereum-account)talál további információt.|

## <a name="get-the-rpc-endpoint"></a>Az RPC-végpont beszerezése

Az Azure Blockchain Service RPC-végpont címe szükséges a blokklánc-hálózathoz való csatlakozáshoz. A végpont címét az Azure Blockchain Development Kit for Ethereum vagy az Azure Portal használatával szerezheti be.

**A fejlesztői készlet használata:**

1. Az **Azure Blockchain Szolgáltatás** a Visual Studio-kód, kattintson a jobb gombbal a konzorcium.
1. Válassza **az RPC végpontcím másolása lehetőséget.**

    ![A Visual Studio Kód ablaktáblája, amely a konzorciumot jeleníti meg az RPC végpontcím másolása beállítással](./media/ethereum-logic-app/devkit-rpc.png)

    A program az RPC-végpontot a vágólapra másolja.

**Az Azure Portal használata:**

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa meg az Azure Blockchain szolgáltatás tagot. Válassza **a Tranzakciócsomópontok és** az alapértelmezett tranzakciócsomópont-kapcsolat lehetőséget.

    ![Tranzakciócsomópontok lap az (alapértelmezett csomópont) kijelöléssel](./media/ethereum-logic-app/transaction-nodes.png)

1. Válassza **a Kapcsolati karakterláncok** > **Hozzáférési kulcsok lehetőséget**.
1. Másolja a végpont címét **HTTPS (1. hozzáférési kulcs)** vagy **HTTPS (Access key 2)** címből.

    ![Az Azure Portal a kapcsolati karakterlánc-hozzáférési kulcsokkal](./media/ethereum-logic-app/connection-string.png)

    Az RPC-végpont a HTTPS URL-cím, amely tartalmazza az Azure Blockchain Service tagtranzakció-csomópontcím- és hozzáférési kulcsát.

## <a name="get-the-private-key"></a>A személyes kulcs beszerezése

Az Ethereum-fiók személyes kulcsa segítségével hitelesítheti magát, amikor tranzakciót küld a blokkláncnak. Az Ethereum-fiók nyilvános és személyes kulcsai egy 12 szavas mnemonikus ból származnak. Az Azure Blockchain Development Kit for Ethereum mnemonikus, amikor csatlakozik egy Azure Blockchain Service konzorciumi taghoz. A végpont címét a fejlesztői készlet bővítmény használatával kaphatja meg.

1. A Visual Studio-kódban nyissa meg a parancspalettát (F1).
1. Válassza az **Azure Blockchain: Személyes kulcs lekérése**lehetőséget.
1. Válassza ki a konzorciumtaghoz való csatlakozáskor mentett mnemonikus elemet.

    ![Parancspaletta a mnemonikus kiválasztása](./media/ethereum-logic-app/private-key.png)

    A személyes kulcs a vágólapra kerül.

## <a name="get-the-account-address"></a>A fiók címének beszerezése

A tagfiók és a jelszó segítségével hitelesítheti magát, amikor tranzakciót küld a blokkláncnak. A jelszó a tag létrehozásakor van beállítva.

1. Az Azure Portalon nyissa meg az Azure Blockchain-szolgáltatás áttekintő lapját.
1. A tagfiók címének **másolása.**

    ![Áttekintő lap a tagfiók címével](./media/ethereum-logic-app/member-account.png)

A fiókcímről és a jelszóról további információt az [Ethereum-fiók című témakörben talál.](consortium.md#ethereum-account)

## <a name="get-the-contract-abi"></a>Szerezd meg a szerződést ABI

A szerződés ABI határozza meg az intelligens szerződés interfészek. Leírja, hogyan lehet kölcsönhatásba lépni az intelligens szerződéssel. A szerződés ABI az Azure Blockchain Development Kit for Ethereum használatával szerezhető be. A Solidity fordító által létrehozott szerződés metaadatfájlból is beszerezheti.

**A fejlesztői készlet használata:**

Ha a fejlesztőkészletet vagy a Szarvasgombát használta az intelligens szerződés létrehozásához, a kiterjesztés segítségével másolhatja a szerződés ABI-t a vágólapra.

1. A Visual Studio Kódkezelő ablaktábláján bontsa ki a Solidity projekt **build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadat-jsonfájljára. A fájlnév az intelligens szerződés neve, amelyet a **.json** kiterjesztés követ.
1. Válassza **a Szerződés ABI másolása lehetőséget.**

    ![A Visual Studio Kód ablaktáblája a Szerződés másolása ABI-kijelöléssel](./media/ethereum-logic-app/abi-devkit.png)

    A szerződés ABI másolja a vágólapra.

**A szerződés metaadatfájljának használata:**

1. Nyissa meg a Solidity projekt **build/szerződések** mappájában található szerződés metaadatfájlját. A fájlnév az intelligens szerződés neve, amelyet a **.json** kiterjesztés követ.
1. Keresse meg az **abi** szakaszt a JSON-fájlban.
1. Másolja az **abi** JSON tömböt.

    ![ABI-kód a szerződés metaadatfájljában](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>A szerződés bytecode-jának beszereznie

A szerződés bytecode az Ethereum virtuális gép által végrehajtott lefordított intelligens szerződés. A szerződés bytecode az Azure Blockchain Development Kit for Ethereum használatával. Azt is kap, hogy a Solidity fordító.

**A fejlesztői készlet használata:**

Ha a fejlesztőkészletet vagy a Szarvasgombát használta az intelligens szerződés létrehozásához, a bővítmény segítségével másolhatja a szerződés bytecode-ját a vágólapra.

1. A Visual Studio Kódkezelő ablaktábláján bontsa ki a Solidity projekt **build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadat-jsonfájljára. A fájlnév az intelligens szerződés neve, amelyet a **.json** kiterjesztés követ.
1. Válassza **a Szerződés bájtekód másolása lehetőséget.**

    ![A Visual Studio Kód ablaktáblája a Szerződés bájkód másolása beállítással](./media/ethereum-logic-app/bytecode-devkit.png)

    A szerződés bytecode-ja a vágólapra kerül.

**A szerződés metaadatfájljának használata:**

1. Nyissa meg a Solidity projekt **build/szerződések** mappájában található szerződés metaadatfájlját. A fájlnév az intelligens szerződés neve, amelyet a **.json** kiterjesztés követ.
1. Keresse meg a **bytecode** elemet a JSON-fájlban.
1. Másolja a **bytecode** értéket.

    ![A Visual Studio Kód ablaktáblája bájtkóddal a metaadatokban](./media/ethereum-logic-app/bytecode-metadata.png)

**A Solidity fordító használata:**

A parancs `solc --bin <smart contract>.sol` segítségével hozza létre a szerződés bytecode.

## <a name="get-the-contract-address"></a>A szerződés címének beszereznie

A szerződés címe az intelligens szerződés célcíme az Ethereum blokkláncon. Ezzel a címmel tranzakciót vagy lekérdezési állapotot küldhet egy intelligens szerződéshez. A szerződés címét a szarvasgomba áttelepítési kimenetéből vagy a szerződés metaadatfájljából szerezheti be.

**A szarvasgomba áttelepítési kimenetének használata:**

Szarvasgomba az intelligens szerződés üzembe helyezése után jeleníti meg a szerződés címét. Másolja a **szerződés címét** a kimenetből.

![Szarvasgomba-áttelepítési kimenet a Szerződés címével a Visual Studio Code-ban](./media/ethereum-logic-app/contract-address-truffle.png)

**A szerződés metaadatfájljának használata:**

1. Nyissa meg a Solidity projekt **build/szerződések** mappájában található szerződés metaadatfájlját. A fájlnév az intelligens szerződés neve, amelyet a **.json** kiterjesztés követ.
1. Keresse meg a **Hálózatok** szakaszt a JSON-fájlban.
1. A magánhálózatokat egész hálózati azonosító azonosítja. Keresse meg a címértéket a hálózati szakaszban.
1. Másolja a **cím** értékét.

![Metaadatok a Visual Studio-kód címértékével](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>További lépések

Tekintse meg a gyakori forgatókönyveket a [videóban, ha többet szeretne tenni a Logic Apps alkalmazásokkal.](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true)

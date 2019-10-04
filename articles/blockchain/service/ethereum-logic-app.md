---
title: A Ethereum blockchain-összekötő használata a Azure Logic Apps
description: A Ethereum blockchain-összekötő használata az intelligens szerződési funkciók kiváltására és az intelligens szerződési eseményekre való reagálásra szolgáló Azure Logic Apps használatával.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720400"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>A Ethereum Blockchain-összekötő használata a Azure Logic Apps

Az Ethereum Blockchain- [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/) [összekötővel](https://docs.microsoft.com/connectors/blockchainethereum/) intelligens szerződéses műveleteket hajthat végre, és reagálhat az intelligens szerződési eseményekre. Tegyük fel például, hogy olyan REST-alapú szolgáltatást szeretne létrehozni, amely egy blockchain-főkönyvből származó információkat ad vissza. Logikai alkalmazás használatával elfogadhatja azokat a HTTP-kérelmeket, amelyek a blockchain-főkönyvben tárolt információkat kérdezik le.

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre a választható előfeltételek @no__t – 0Quickstart: A Visual Studio Code használatával csatlakozhat egy Azure Blockchain Service Consortium-hálózathoz](connect-vscode.md). A rövid útmutató végigvezeti a Ethereum készült [Azure Blockchain Development Kit](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) telepítésének és a Blockchain-fejlesztési környezet beállításának lépésein.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Azure Logic Apps segítségével ütemezhet, automatizálhatja az üzleti folyamatokat és munkafolyamatokat, ha integrálni kell a rendszereket és szolgáltatásokat. Először létre kell hoznia egy olyan logikát, amely a Ethereum Blockchain-összekötőt használja.

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása > integráció > logikai alkalmazás**lehetőséget.
1. A **logikai alkalmazás létrehozása**területen adja meg a logikai alkalmazás létrehozásának részleteit. Ha elkészült, válassza a **Létrehozás**lehetőséget.

    A Azure Logic Apps létrehozásával kapcsolatos további információkért lásd: [Azure Logic apps létrehozása](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Miután az Azure üzembe helyezte az alkalmazást, válassza ki a logikai alkalmazás erőforrását.
1. A Logic Apps Designer **sablonok**területén válassza az **üres logikai alkalmazás**lehetőséget.

Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot.

A Ethereum Blockchain-összekötő egyetlen triggerrel és számos művelettel rendelkezik. A használt trigger vagy művelet a forgatókönyvtől függ.

Válassza ki az alábbi részeket, ha a munkafolyamat:

* Eseményindítót [használ](#use-the-event-trigger), ha a blockchain esemény történik.
* Egy intelligens szerződés lekérdezése vagy üzembe helyezése, [műveletek használata](#use-actions).
* Egy gyakori forgatókönyvet követ, [amely egy munkafolyamatot készít a fejlesztői készlet használatával](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Az esemény eseményindítójának használata

Akkor használja a Ethereum Blockchain-eseményindítókat, ha azt szeretné, hogy egy logikai alkalmazás egy intelligens szerződési esemény után fusson. Tegyük fel például, hogy e-mailt szeretne küldeni egy intelligens szerződési függvény meghívásakor.

1. A Logic app Designerben válassza a Ethereum Blockchain-összekötőt.
1. Az **Eseményindítók** lapon válassza ki, **hogy mikor történjen az intelligens szerződési esemény**.
1. Módosítsa vagy [hozzon létre egy API-kapcsolódást](#create-an-api-connection) az Azure Blockchain szolgáltatáshoz.
1. Adja meg az események kereséséhez használni kívánt intelligens szerződés részleteit.

    ![Esemény-eseményindító tulajdonságai](./media/ethereum-logic-app/event-properties.png)

    | Tulajdonság | Leírás |
    |----------|-------------|
    | **Egyezményes ABI** | A szerződéses alkalmazás bináris felülete (ABI) meghatározza az intelligens szerződési felületeket. [A szerződési ABI beszerzése](#get-contract-abi). |
    | **Intelligens Szerződés címe** | A Szerződés címe az intelligens szerződési cél címe a Ethereum blockchain. [A szerződési címek beszerzése](#get-contract-address). |
    | **Esemény neve** | Válasszon egy intelligens szerződési eseményt az ellenőrzési művelethez. Az esemény elindítja a logikai alkalmazást. |
    | **Intervallum** és **gyakoriság** | Válassza ki, hogy milyen gyakran szeretné megkeresni az eseményt. |

1. Kattintson a **Mentés** gombra.

A logikai alkalmazás befejezéséhez hozzáadhat egy új lépést, amely a Ethereum blockchain esemény eseményindítóján alapuló műveletet hajt végre. Küldjön például egy e-mailt.

## <a name="use-actions"></a>Műveletek használata

Használja a Ethereum Blockchain műveleteit, ha azt szeretné, hogy egy logikai alkalmazás végrehajtson egy műveletet a Blockchain-főkönyvben. Létrehozhat például egy olyan REST-alapú szolgáltatást, amely intelligens szerződési funkciót hív meg, ha egy logikai alkalmazás HTTP-kérést végez.

Az összekötő műveleteihez trigger szükséges. A Ethereum Blockchain-összekötő műveletet a következő lépéseként használhatja egy trigger után. Például egy HTTP-kérelem triggere egy microsservice.

1. A Logic app Designerben válasszon egy triggert követő **új lépést** .
1. Válassza ki a Ethereum Blockchain-összekötőt.
1. A **műveletek** lapon válassza ki az elérhető műveletek egyikét.

    ![Művelet tulajdonságai](./media/ethereum-logic-app/action-properties.png)

1. Módosítsa vagy [hozzon létre egy API-kapcsolódást](#create-an-api-connection) az Azure Blockchain szolgáltatáshoz.
1. A választott művelettől függően adja meg az intelligens szerződés funkciójának következő részleteit.

    | Tulajdonság | Leírás |
    |----------|-------------|
    | **Egyezményes ABI** | A szerződéses alkalmazás bináris felülete (ABI) meghatározza az intelligens szerződési felületeket. [A szerződési ABI beszerzése](#get-contract-abi). |
    | **Szerződés bytecode** | A lefordított intelligens szerződés bytecode. [A szerződés bytecode beszerzése](#get-contract-bytecode). |
    | **Intelligens Szerződés címe** | A Szerződés címe az intelligens szerződési cél címe a Ethereum blockchain. [A szerződési címek beszerzése](#get-contract-address). |
    | **Intelligens szerződés függvényének neve** | Válassza ki az intelligens szerződés funkciójának nevét a művelethez. A lista a szerződési ABI-ban található részletekből van feltöltve. |

    Az intelligens szerződési függvény nevének kiválasztása után megtekintheti a függvények paramétereinek kötelező mezőit. Adja meg a forgatókönyvhöz szükséges értékeket vagy dinamikus tartalmat.

Ezután használhatja a logikai alkalmazást. A Logic app-esemény elindítása után a Ethereum Blockchain művelet fut. A HTTP-kérések triggere például egy Ethereum blockchain műveletet futtat egy intelligens szerződési állapot értékének lekérdezéséhez, ami egy olyan HTTP-válasz, amely az értéket adja vissza.

## <a name="generate-a-workflow"></a>Munkafolyamat létrehozása

A Visual Studio Code Ethereum készült Azure Blockchain Development Kit képes logikai alkalmazás-munkafolyamatokat előállítani a gyakori forgatókönyvekhez. Négy forgatókönyv áll rendelkezésre:

* Adatközzététel egy Azure SQL Database
* Esemény-közzététel egy Azure Event Grid vagy Azure Service Bus
* Jelentés közzététele
* REST-alapú szolgáltatás

 Az Azure Blockchain Development Kit a szarvasgomba segítségével egyszerűsíti a Blockchain fejlesztését. Egy intelligens szerződésen alapuló logikai alkalmazás létrehozásához szükség van egy szarvasgombás-megoldásra az intelligens szerződéshez. Az Azure Blockchain Service Consortium-hálózathoz is csatlakoznia kell. További információ: a [Visual Studio Code használata az Azure Blockchain Service Consortium Network rövid útmutatóhoz való kapcsolódáshoz](connect-vscode.md).

Például a következő lépések egy REST-alapú, a gyors üzembe helyezési **HelloBlockchain** intelligens szerződésen alapuló Service Logic app-alkalmazást hoznak elő:

1. A VS Code Explorer oldalsávján bontsa ki a **szerződések** mappát a megoldásban.
1. Kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, és válassza az **intelligens szerződések előállítása** a menüből lehetőséget.

    ![Logikai alkalmazás létrehozása](./media/ethereum-logic-app/generate-logic-app.png)

1. A parancs palettáján válassza a **logikai alkalmazás**lehetőséget.
1. Adja meg a **szerződési címeket**. További információt [a szerződési címek beszerzését](#get-contract-address)ismertető témakörben talál.
1. Válassza ki a logikai alkalmazáshoz tartozó Azure-előfizetést és erőforráscsoportot.

    A Logic app-konfiguráció és a kód fájljai a **generatedLogicApp** könyvtárban jönnek létre.

1. Tekintse meg a **generatedLogicApp/HelloBlockchain** könyvtárat. Minden egyes intelligens szerződési funkció, esemény és tulajdonság esetében létezik egy logikai alkalmazás JSON-fájlja.
1. Nyissa meg a **generatedLogicApp/HelloBlockchain/szolgáltatás/tulajdonságot. RequestMessage. logicapp. JSON** fájlt, és másolja a tartalmat.

    ![JSON a RequestMessage tulajdonsághoz](./media/ethereum-logic-app/requestmessage.png)

1. A logikai alkalmazásban válassza a **logikai alkalmazás kódjának megtekintése**lehetőséget. Cserélje le a meglévő JSON-t a generált Logic app JSON-ra.

    ![Logikai alkalmazás konfigurációjának cseréje kód nézetben](./media/ethereum-logic-app/code-view.png)

1. Válassza ki a **tervezőt** a Tervező nézetre váltáshoz.
1. A logikai alkalmazás a forgatókönyv alapvető lépéseit tartalmazza. Azonban frissítenie kell a Ethereum Blockchain-összekötő konfigurációs adatait.
1. Válassza a **kapcsolatok** lépést, és módosítsa vagy [hozzon létre egy API-kapcsolatot](#create-an-api-connection) az Azure Blockchain szolgáltatással.

    ![A szolgáltatáshoz tartozó logikai alkalmazás](./media/ethereum-logic-app/microservice-logic-app.png)

1. Ezután használhatja a logikai alkalmazást. A REST-alapú webszolgáltatás teszteléséhez adjon ki egy HTTP POST-kérelmet a logikai alkalmazás kérésének URL-címére. Másolja a **http-post URL-címét** a **http-kérelem fogadása** lépés után.

    ![HTTP POST URL-CÍM](./media/ethereum-logic-app/post-url.png)

1. Hozzon létre egy HTTP POST-kérelmet a cURL használatával. Cserélje le a helyőrző szövegre **\<HTTP post URL @ no__t-2** elemet az előző lépés URL-címével.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    A cURL parancs a logikai alkalmazás válaszát adja vissza. Ebben az esetben a **RequestMessage** intelligens szerződési függvény kimenete.

    ![RequestMessage tulajdonság kimenete](./media/ethereum-logic-app/curl.png)

A fejlesztői csomag használatával kapcsolatos további információkért lásd: az [Azure Blockchain Development Kit for Ethereum wiki](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) lapja.

## <a name="create-an-api-connection"></a>API-kapcsolatok létrehozása

A Ethereum Blockchain-összekötőhöz egy blockchain API-csatlakozás szükséges. Az API-összekötő több logikai alkalmazáshoz is használható. Bizonyos tulajdonságok szükségesek, mások pedig a forgatókönyvtől függenek.

> [!IMPORTANT]
> A blockchain tranzakcióinak létrehozásához titkos kulcs vagy fiók címe és jelszava szükséges. Csak egy hitelesítési űrlapra van szükség. Nem kell megadnia a titkos kulcs és a fiók adatait is. A szerződések lekérdezéséhez nincs szükség tranzakcióra. Ha a szerződési állapotot lekérdező műveleteket használ, a titkos kulcs vagy a fiók címe és jelszava nem szükséges.

Egy Azure Blockchain-szolgáltatáshoz való kapcsolódás beállításához az alábbi lista a forgatókönyvtől függően szükség lehet a lehetséges tulajdonságokra.

| Tulajdonság | Leírás |
|----------|-------------|
|**Kapcsolat neve** | Az API-kapcsolatok neve. Kötelező. |
|**Ethereum RPC-végpont** | Az Azure Blockchain szolgáltatás tranzakciós csomópontjának HTTP-címe. Kötelező. [Az RPC-végpont beszerzése](#get-rpc-endpoint). |
|**Titkos kulcs** | Ethereum fiók titkos kulcsa. A tranzakcióhoz titkos kulcs vagy fiók címe és jelszava szükséges. [A titkos kulcs beszerzése](#get-private-key). |
|**Fiók címe** | Az Azure Blockchain-szolgáltatási tag fiókjának címe. A tranzakcióhoz titkos kulcs vagy fiók címe és jelszava szükséges. [A fiókhoz tartozó címek beszerzése](#get-account-address). |
|**Fiók jelszava** | A fiók jelszava a tag létrehozásakor van beállítva. A jelszó alaphelyzetbe állításával kapcsolatos információkért lásd: [Ethereum-fiók](consortium.md#ethereum-account).|

## <a name="get-rpc-endpoint"></a>RPC-végpont beolvasása

Az Azure Blockchain szolgáltatás RPC-végpontjának címe Blockchain hálózathoz való kapcsolódáshoz szükséges. A végponti címeket a Ethereum vagy a Azure Portal Azure Blockchain Development Kit használatával szerezheti be.

**Fejlesztői csomag használata:**

1. Az **Azure Blockchain Service** -ben a Visual Studio Code-ban kattintson a jobb gombbal a konzorciumra.
1. Válassza az **RPC-végpont másolása**lehetőséget.

    ![RPC-végpont másolása](./media/ethereum-logic-app/devkit-rpc.png)

    A rendszer átmásolja az RPC-végpontot a vágólapra.

**Azure Portal használata:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az Azure Blockchain-szolgáltatás tagjához. Válassza a **tranzakciós csomópontok** és az alapértelmezett tranzakció csomópontja hivatkozást.

    ![Alapértelmezett tranzakciós csomópont kiválasztása](./media/ethereum-logic-app/transaction-nodes.png)

1. Válassza a **kapcsolati karakterláncok > hozzáférési kulcsok**elemet.
1. Másolja a végponti címeket a https-ről **(1. hozzáférési kulcs)** vagy a 2. hozzáférési kulcsra.

    ![Kapcsolati sztring](./media/ethereum-logic-app/connection-string.png)

    Az RPC-végpont a HTTPS URL-cím, amely tartalmazza az Azure Blockchain-szolgáltatási tag tranzakciós csomópontjának címét és hozzáférési kulcsát.

## <a name="get-private-key"></a>Titkos kulcs lekérése

A Ethereum-fiók titkos kulcsa hitelesítésre használható, amikor tranzakciót küld a blockchain. A Ethereum-fiók nyilvános és titkos kulcsai egy 12 szavas hívóbetűje jönnek létre. A Ethereum készült Azure Blockchain Development Kit létrehoz egy hívóbetűje az Azure Blockchain Service Consortium egyik tagjához való csatlakozáskor. A végponti címeket a fejlesztői csomag bővítmény használatával szerezheti be.

1. A Visual Studio Code-ban nyissa meg a Command paletta (F1) parancsot.
1. Válassza a **Azure Blockchain: A @ no__t-0 titkos kulcs lekérése.
1. Válassza ki azokat a hívóbetűje, amelyeket a konzorcium tagjához való csatlakozáskor mentett.

    ![Hívóbetűje kiválasztása](./media/ethereum-logic-app/private-key.png)

    A titkos kulcsot a rendszer a vágólapra másolja.

## <a name="get-account-address"></a>Fiók címe lekérése

A fiók és a jelszó használatával végezhető el a hitelesítés, amikor tranzakciót küld a blockchain. A jelszó beállítása a tag létrehozásakor történik.

1. A Azure Portal nyissa meg az Azure Blockchain szolgáltatás áttekintés lapját.
1. Másolja a **tag fiókjának** a címeit.

    ![Tag másolása fiók](./media/ethereum-logic-app/member-account.png)

A fiókra és a jelszóra vonatkozó további információkért lásd: [Ethereum-fiók](consortium.md#ethereum-account).

## <a name="get-contract-abi"></a>Egyezményes ABI beszerzése

A szerződéses alkalmazás bináris felülete (ABI) meghatározza az intelligens szerződési felületeket. Leírja, hogyan kezelheti az intelligens szerződést. A Ethereum-hez készült Azure Blockchain Development Kit vagy a Solidion Compiler szerződés metaadatainak fájljában lekérheti az ABI szerződést.

**Fejlesztői csomag használata:**

Ha a fejlesztői készletet vagy a szarvasgombát használja az intelligens szerződés kiépítéséhez, a bővítmény használatával másolhatja át a szerződéses ABI-t a vágólapra.

1. A Visual Studio Code Explorer ablaktáblán bontsa ki a szilárdtest-projekt **Build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadatainak JSON-fájljára. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Válassza a **Szerződés másolása ABI**elemet.

    ![Egyezményes ABI másolása a fejlesztői készlet használatával](./media/ethereum-logic-app/abi-devkit.png)

    A rendszer átmásolja a szerződéses ABI-t a vágólapra.

**A szerződés metaadatainak fájljának használata:**

1. Nyissa meg a konszolidációs projekt **Build/szerződések** mappájában található szerződéses metaadat-fájlt. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Keresse meg az **ABI** SZAKASZT a JSON-fájlban.
1. Másolja az **ABI** JSON-tömböt.

    ![Egyezményes ABI szakasz a metaadatokban](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>Szerződés bytecode beolvasása

A szerződés bytecode a Ethereum virtuális gép által végrehajtott lefordított intelligens szerződés. A bytecode a Ethereum Azure Blockchain Development Kit vagy a Solidion Compiler használatával kérheti le.

**Fejlesztői csomag használata:**

Ha a fejlesztői készletet vagy a szarvasgombát használja az intelligens szerződés kiépítéséhez, a bővítményt használhatja a szerződés bytecode másolásához a vágólapra.

1. A Visual Studio Code Explorer ablaktáblán bontsa ki a szilárdtest-projekt **Build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadatainak JSON-fájljára. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Válassza a **Szerződés bytecode másolása**lehetőséget.

    ![Szerződés bytecode másolása a fejlesztői készlet használatával](./media/ethereum-logic-app/bytecode-devkit.png)

    A rendszer átmásolja a bytecode a vágólapra.

**A szerződés metaadatainak fájljának használata:**

1. Nyissa meg a konszolidációs projekt **Build/szerződések** mappájában található szerződéses metaadat-fájlt. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Keresse meg a **bytecode** elemet a JSON-fájlban.
1. Másolja a **bytecode** értéket.

    ![Bytecode másolása metaadatok használatával](./media/ethereum-logic-app/bytecode-metadata.png)

**A Solidion Compiler használata:**

A szerződés bytecode létrehozásához használja a `solc --bin <smart contract>.sol` parancsot.

## <a name="get-contract-address"></a>Szerződési címek beolvasása

A Szerződés címe az intelligens szerződési cél címe a Ethereum blockchain. Ez a címe egy intelligens szerződés tranzakciós vagy lekérdezési állapotának elküldésére használható. A Szerződés címe a szarvasgomba-áttelepítési kimenetből vagy a szerződés metaadatainak fájljából kérhető le.

**A szarvasgomba-áttelepítés kimenetének használata:**

A szarvasgomba az intelligens szerződés üzembe helyezése után megjeleníti a szerződés címeit. Másolja a **Szerződés címeit** a kimenetből.

![A szarvasgomba kimenetből származó Szerződés címe](./media/ethereum-logic-app/contract-address-truffle.png)

**A szerződés metaadatainak fájljának használata:**

1. Nyissa meg a konszolidációs projekt **Build/szerződések** mappájában található szerződéses metaadat-fájlt. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Keresse meg a **Networks** SZAKASZT a JSON-fájlban.
1. A magánhálózati hálózatokat egész számú hálózati azonosító azonosítja. A címek értékének megkeresése a hálózat szakaszon belül.
1. Másolja a **címtartomány** értékét.

![A metaadatokból származó Szerződés címe](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>További lépések

Tekintse meg [a blockchain-hez kapcsolódó gyakori forgatókönyveket Azure Logic Apps használatával](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).

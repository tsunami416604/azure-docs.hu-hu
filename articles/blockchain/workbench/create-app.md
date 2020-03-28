---
title: Blockchain alkalmazás létrehozása - Azure Blockchain Workbench
description: Oktatóanyag az Azure Blockchain Workbench Előzetes verziójához blockchain alkalmazás létrehozásáról.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 33a9e9c10c07d0808626353a7edfd505e0f60bc9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74324803"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>Oktatóanyag: Hozzon létre egy blockchain alkalmazást az Azure Blockchain Workbench számára

Az Azure Blockchain Workbench segítségével olyan blokklánc-alkalmazásokat hozhat létre, amelyek a konfiguráció és az intelligens szerződéskód által meghatározott többrésztvevős munkafolyamatokat képviselnek.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Blokklánc-alkalmazás konfigurálása
> * Intelligens szerződéskódfájl létrehozása
> * Blockchain alkalmazás hozzáadása a Blockchain Workbench-hez
> * Tagok hozzáadása a blockchain alkalmazáshoz

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A Blockchain Workbench üzembe helyezése. További információ: [Azure Blockchain Workbench deployment](deploy.md) a központi telepítés részleteiért.
* Az Azure Active Directory-felhasználók a blockchain workbench társított bérlőben. További információ: [Azure AD-felhasználók hozzáadása az Azure Blockchain Workbench ben.](manage-users.md#add-azure-ad-users)
* Blockchain workbench rendszergazdai fiók. További információ: [Blockchain Workbench-rendszergazdák hozzáadása az Azure Blockchain Workbench alkalmazásban című témakörben.](manage-users.md#manage-blockchain-workbench-administrators)

## <a name="hello-blockchain"></a>Helló, Blockchain!

Hozzon létre egy alapszintű alkalmazást, amelyben a kérelmező kérést küld, a válaszadó pedig választ küld a kérésre.
Például egy kérés lehet, "Hello, hogy vagy?", és a válasz lehet, "Remekül vagyok!". Mind a kérés, mind a válasz az alapul szolgáló blokkláncon van rögzítve.

Kövesse az alkalmazásfájlok létrehozásának lépéseit, vagy [töltse le a mintát a GitHubról.](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain)

## <a name="configuration-file"></a>Konfigurációs fájl

A konfigurációs metaadatok határozzák meg a blockchain alkalmazás magas szintű munkafolyamatait és interakciós modelljét. A konfigurációs metaadatok a blokklánc-alkalmazás munkafolyamat-szakaszait és interakciós modelljét jelölik.

1. A kedvenc szerkesztőjében hozzon létre egy . `HelloBlockchain.json`
2. Adja hozzá a következő JSON-t a blockchain alkalmazás konfigurációjának meghatározásához.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Mentse a `HelloBlockchain.json` fájlt.

A konfigurációs fájl több szakaszból is rendelkezik. Az egyes szakaszok részletei a következők:

### <a name="application-metadata"></a>Alkalmazás metaadatai

A konfigurációs fájl eleje az alkalmazással kapcsolatos információkat tartalmaz, beleértve az alkalmazás nevét és leírását.

### <a name="application-roles"></a>Alkalmazás-szerepkörök

Az alkalmazásszerepkörök szakasz határozza meg a felhasználói szerepköröket, akik működhetnek, vagy részt vesznek a blockchain alkalmazáson belül. A funkciók alapján különböző szerepköröket határoz meg. A kérelem-válasz forgatókönyvben különbséget tesz a kérelmező, mint olyan entitás, amely kéréseket és válaszadóként válaszokat eredményező entitásként való funkcionalitása között van.

### <a name="workflows"></a>Munkafolyamatok

A munkafolyamatok a szerződés egy vagy több szakaszát és műveleteit határozzák meg. A kérelem-válasz forgatókönyv, az első szakasz (állapot) a munkafolyamat egy kérelmező (szerepkör) egy műveletet (átmenet) küld egy kérelmet (függvény). A következő lépés (állapot) egy válaszadó (szerepkör) műveletet (átmenetet) hoz válasz küldéséhez (függvény). Az alkalmazás munkafolyamatai olyan tulajdonságokat, függvényeket és állapotokat foglalhatnak magukban, amelyek a szerződés folyamatát írják le.

A konfigurációs fájlok tartalmáról az [Azure Blockchain munkafolyamat konfigurációs útmutatójában talál](configuration.md)további információt.

## <a name="smart-contract-code-file"></a>Intelligens szerződéskódfájl

Az intelligens szerződések a blockchain alkalmazás üzleti logikáját képviselik. Jelenleg a Blockchain Workbench támogatja az Ethereum-ot a blockchain főkönyvhöz. Az Ethereum a [Solidity-t](https://solidity.readthedocs.io) használja programozási nyelvként az intelligens szerződések önérvényesítő üzleti logikájának írásához.

A Solidity intelligens szerződései hasonlóak az objektumorientált nyelvek osztályaihoz. Minden szerződés tartalmazza az állapot és a funkciók végrehajtására szakaszában és műveletek az intelligens szerződés.

A kedvenc szerkesztőjében hozzon létre egy fájlt, amelynek neve `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Változat pragma

Ajánlott eljárásként adja meg a megcélzott Tömörség verzióját. A verzió megadása segít elkerülni a jövőbeli solidity verziókkal való inkompatibilitást.

Adja hozzá az alábbi verziót pragma tetején `HelloBlockchain.sol` intelligens szerződés kód fájlt.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfigurációs és intelligens szerződéskód-kapcsolat

A Blockchain Workbench a konfigurációs fájlt és az intelligens szerződéskódfájlt használja egy blockchain alkalmazás létrehozásához. Kapcsolat van a konfigurációban meghatározott a kódban és az intelligens szerződésben megadott kód között. Az alkalmazás létrehozásához szerződésrészleteire, függvényeire, paramétereire és típusaira van szükség. A Blockchain Workbench az alkalmazás létrehozása előtt ellenőrzi a fájlokat.

### <a name="contract"></a>Szerződés

Adja hozzá a `HelloBlockchain.sol` **szerződésfejlécet** az intelligens szerződéskódfájlhoz.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Állami változók

Az állami változók az állapot értékeit tárolják az egyes szerződéspéldányokhoz. A szerződésben lévő állapotváltozóknak meg kell egyezniük a konfigurációs fájlban definiált munkafolyamat-tulajdonságokkal.

Adja hozzá az állapotváltozókat `HelloBlockchain.sol` a szerződéshez az intelligens szerződéskódfájlban.

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Konstruktor

A konstruktor a munkafolyamat új intelligens szerződéspéldányának bemeneti paramétereit határozza meg. A konstruktor kötelező paraméterei a konfigurációs fájlban konstruktorparaméterekként vannak definiálva. A paraméterek számának, sorrendjének és típusának mindkét fájlban egyeznie kell.

A konstruktor függvényben írjon bármilyen üzleti logikát, amelyet a szerződés létrehozása előtt végre szeretne hajtani. Például inicializálja az állapotváltozókat kezdő értékekkel.

Adja hozzá a konstruktorfunkciót a szerződéshez az `HelloBlockchain.sol` intelligens szerződéskódfájlban.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Functions

A függvények a szerződésen belüli üzleti logika végrehajtható egységei. A függvény kötelező paraméterei a konfigurációs fájlban függvényparaméterekként vannak definiálva. A paraméterek számának, sorrendjének és típusának mindkét fájlban egyeznie kell. A függvények a konfigurációs fájlban lévő Blockchain Workbench-munkafolyamatban lévő átmenetekhez vannak társítva. Az átmenet olyan művelet, amely et az alkalmazás munkafolyamatának következő szakaszába lépa szerződés szerint.

Írja meg a függvényben végrehajtani kívánt üzleti logikát. Például egy állapotváltozó értékének módosítása.

1. Adja hozzá a következő funkciókat a szerződéshez az `HelloBlockchain.sol` intelligens szerződéskódfájlban.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. Mentse `HelloBlockchain.sol` az intelligens szerződéskódfájlt.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Blockchain alkalmazás hozzáadása a Blockchain Workbench-hez

Ha hozzá szeretne adni egy blockchain alkalmazást a Blockchain Workbench-hez, töltse fel a konfigurációs és intelligens szerződésfájlokat az alkalmazás meghatározásához.

1. A webböngészőben keresse meg a Blockchain Workbench webcímét. Például `https://{workbench URL}.azurewebsites.net/` a webalkalmazás jön létre, amikor telepíti blockchain workbench. A Blockchain Workbench webcím megkeresésével kapcsolatos további információkért lásd: [Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. Jelentkezzen be [blockchain munkaterület-rendszergazdaként.](manage-users.md#manage-blockchain-workbench-administrators)
3. Válassza **az Új alkalmazások** > **New**lehetőséget. Megjelenik az **Új alkalmazás** ablaktábla.
4. Válassza **a Kötéskonfiguráció** > **feltöltése Tallózás** lehetőséget a létrehozott **HelloBlockchain.json konfigurációs** fájl megkereséséhez. A konfigurációs fájl automatikusan érvényesítésre kerül. Az érvényesítési hibák megjelenítéséhez jelölje ki a **Megjelenítés** hivatkozást. Az alkalmazás telepítése előtt javítsa ki az érvényesítési hibákat.
5. Válassza a >  **Kötéskód feltöltése****Tallózás** lehetőséget a **HelloBlockchain.sol** intelligens szerződéskódfájl megkereséséhez. A kódfájl automatikusan érvényesítve lesz. Az érvényesítési hibák megjelenítéséhez jelölje ki a **Megjelenítés** hivatkozást. Az alkalmazás telepítése előtt javítsa ki az érvényesítési hibákat.
6. Válassza **a Telepítés lehetőséget** a blockchain alkalmazás létrehozásához a konfiguráció és az intelligens szerződésfájlok alapján.

A blokklánc-alkalmazás telepítése néhány percet vesz igénybe. A telepítés befejezése után az új alkalmazás megjelenik az **Alkalmazások**alkalmazásban. 

> [!NOTE]
> Az [Azure Blockchain Workbench REST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)használatával is létrehozhat blokklánc-alkalmazásokat.

## <a name="add-blockchain-application-members"></a>Blockchain alkalmazástagok hozzáadása

Alkalmazástagok hozzáadása az alkalmazáshoz a szerződések kezdeményezéséhez és az azon végrehajtott műveletek kezdeményezéséhez és meghozásához. Alkalmazástagok hozzáadásához [blockchain idomár rendszergazdának](manage-users.md#manage-blockchain-workbench-administrators)kell lennie.

1. Válassza **alkalmazások** > **Hello, Blockchain!**.
2. Az alkalmazáshoz társított tagok száma az oldal jobb felső sarkában jelenik meg. Új alkalmazás esetén a tagok száma nulla lesz.
3. Jelölje ki a **tagok** hivatkozását a lap jobb felső sarkában. Megjelenik az alkalmazás tagjainak aktuális listája.
4. A tagsági listában válassza a **Tagok hozzáadása lehetőséget.**
5. Jelölje ki vagy írja be a hozzáadni kívánt tag nevét. Csak a Blockchain Workbench-bérlőben létező Azure AD-felhasználók szerepelnek a listában. Ha a felhasználó nem található, hozzá kell [adnia az Azure AD-felhasználókat.](manage-users.md#add-azure-ad-users)
6. Válassza ki a tag **szerepkörét.** Az első tag esetében válassza a **Kérelmező** szerepkörként lehetőséget.
7. Válassza a **Hozzáadás** lehetőséget, ha hozzá szeretné adni a társított szerepkörrel rendelkező tagot az alkalmazáshoz.
8. Adjon hozzá egy másik tagot a **Válaszadó** szerepkörrel rendelkező alkalmazáshoz.

A felhasználók blockchain workbench-ben való kezeléséről a [Felhasználók kezelése az Azure Blockchain Workbench alkalmazásban](manage-users.md) című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

Ebben az útmutató cikkben létrehozott egy alapvető kérés-válasz alkalmazást. Az alkalmazás használatának megismeréséhez folytassa a következő útmutatócikkel.

> [!div class="nextstepaction"]
> [Blockchain alkalmazás használata](use.md)

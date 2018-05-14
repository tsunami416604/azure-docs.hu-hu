---
title: Azure Blockchain munkaterület blockchain-alkalmazás létrehozása
description: Megtudhatja, hogyan hozhat létre blockchain alkalmazást Azure Blockchain munkaterület.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/9/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 485f761e22871396dace71333868ba7712e41f67
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Azure Blockchain munkaterület blockchain-alkalmazás létrehozása

Azure Blockchain munkaterület hozhat létre a megfelelő több résztvevős munkafolyamatok konfigurációs és intelligens szerződés kód blockchain alkalmazások.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Beállíthat egy blockchain alkalmazást
> * Hozzon létre egy intelligens szerződés kód fájlt
> * Blockchain munkaterület blockchain alkalmazás hozzáadása
> * Tagok hozzáadása a blockchain alkalmazás

## <a name="prerequisites"></a>Előfeltételek

* A Blockchain munkaterület központi telepítés. További információkért lásd: [Azure Blockchain munkaterület központi telepítési](blockchain-workbench-deploy.md) központi telepítés leírását.
* Az Azure Active Directory-felhasználók Blockchain munkaterület társított tenant. További információkért lásd: [az Azure Active Directory-felhasználók hozzáadása az Azure Blockchain munkaterület](blockchain-workbench-manage-users.md#add-azure-ad-users).
* Egy Blockchain munkaterület rendszergazdai fiók. További információkért lásd: hozzáadása [Blockchain munkaterület a rendszergazdák Azure Blockchain munkaterület](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

Létrehozzuk, amelyben a kérelmezőnek egy kérést küld egy alapszintű alkalmazást, és a válaszoló küldése egy kérelemre adott válasz. Például egy kérelem lehetnek, "Hello, hogy folytatja?", és a válasz lehet, "Vagyok nagyszerű!". Az alapul szolgáló blockchain mind a kérés- és a rendszer rögzíti. 

A lépéseket követve létrehozhat az alkalmazásfájlok vagy beállíthatja a [a minta letöltéséhez a Githubról](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Konfigurációs fájl

Konfigurációs metaadatok magas szintű munkafolyamatok és a blockchain alkalmazás beavatkozás modelljét határozza meg. Konfigurációs metaadatok a munkafolyamat-szakaszok és a blockchain alkalmazás beavatkozás típusát jelöli.

1. A kedvenc szerkesztő, hozzon létre egy fájlt `HelloBlockchain.json`.
2. Adja hozzá a következő JSON blockchain alkalmazás konfigurációjának meghatározása.

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

A konfigurációs fájl több részből áll. Az egyes szakaszokon adatait a következők:

### <a name="application-metadata"></a>Alkalmazás metaadatainak

A konfigurációs fájl elejéhez az alkalmazásadatokat, például az alkalmazás nevét és leírását tartalmazza.

### <a name="application-roles"></a>Alkalmazás-szerepkörök

Az alkalmazás szerepkörök szakaszban definiálja a felhasználói szerepkörök, kik hozhatnak működésre, illetve részt venni a blockchain alkalmazáson belül. Ön definiálása különböző szerepkörök funkciókon alapulnak. A kérés-válasz az esetben nincs megkülönböztetve a kérelmezőnek egy egységként kérelmek előállító funkcióit és egy válaszadó egy egységként, amely létrehozza a válaszokat.

### <a name="workflows"></a>Munkafolyamatok

A munkafolyamatok határozzák meg, egy vagy több fázisból áll, és a szerződés műveletek. A kérés-válasz a forgatókönyvben az első fázist (állapot), a munkafolyamat, a kérelmezőnek (szerepkör) végrehajt egy műveletet (átmeneti) (függvény) kérést küldeni. A következő szakasz (állapot) egy válaszadó (szerepkör) végrehajt egy műveletet (átmeneti) (függvény) választ el. Egy alkalmazás munkafolyamata magába foglaló tulajdonságok, függvények, és szükséges állapotok ismertetik a folyamatot a szerződés. 

A konfigurációs fájlok tartalma kapcsolatos további információkért lásd: [Azure Blockchain munkafolyamat hivatkozás](blockchain-workbench-configuration-overview.md).

## <a name="smart-contract-code-file"></a>Intelligens szerződés kódfájl

Intelligens szerződések határoz meg az üzleti logika blockchain alkalmazás. Jelenleg Blockchain munkaterület Ethereum támogatja a blockchain főkönyvi. Ethereum használ [Tömörség](https://solidity.readthedocs.io) a önálló végrehajtó üzleti logikát a intelligens szerződések írására szolgáló programozási nyelv.

Intelligens egyezményének Tömörség objektumorientált nyelvű osztályok hasonlóak. Minden szerződés tartalmazza, állapota és a funkciók szakaszból és az intelligens egyezmény műveletek végrehajtásához.

A kedvenc szerkesztőben nevű fájl létrehozása `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Verzió pragma

Ajánlott eljárásként Tömörség céloz meg verzióját jelzi. Adja meg a verzió segít elkerülni a nem kompatibilis Tömörség későbbi verzióiban. 

Adja hozzá a következő verzió pragma tetején `HelloBlockchain.sol` intelligens szerződés forráskód fájlja.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Alaposztály

**WorkbenchBase** alaposztály lehetővé teszi, hogy a munkaterület Blockchain létre kell hoznia egy frissítést a szerződést. Az alaposztály Blockchain munkaterület intelligens szerződés kód szükség. A szerződés kell örökölnie a **WorkbenchBase** alaposztály.

A `HelloBlockchain.sol` szerződés kódfájl intelligens, adja hozzá a **WorkbenchBase** a fájl elején osztály. 

```
contract WorkbenchBase {
    event WorkbenchContractCreated(string applicationName, string workflowName, address originatingAddress);
    event WorkbenchContractUpdated(string applicationName, string workflowName, string action, address originatingAddress);

    string internal ApplicationName;
    string internal WorkflowName;

    function WorkbenchBase(string applicationName, string workflowName) internal {
        ApplicationName = applicationName;
        WorkflowName = workflowName;
    }

    function ContractCreated() internal {
        WorkbenchContractCreated(ApplicationName, WorkflowName, msg.sender);
    }

    function ContractUpdated(string action) internal {
        WorkbenchContractUpdated(ApplicationName, WorkflowName, action, msg.sender);
    }
}
```
Az alaposztály két fontos funkciókat tartalmazza:

|Alaposztály függvény  | Cél  | Mikor legyen meghívva  |
|---------|---------|---------|
| ContractCreated() | Értesíti a szerződés létrejött Blockchain munkaterület | Mielőtt kilépne a szerződés konstruktor |
| ContractUpdated() | Értesíti a szerződés állapota frissítve lett Blockchain munkaterület | A szerződés függvény való kilépés előtt |

### <a name="configuration-and-smart-contract-code-relationship"></a>A kód kapcsolat konfigurációs és intelligens szerződés

Blockchain munkaterület blockchain alkalmazás létrehozása a konfigurációs fájlban és intelligens szerződés kódfájl használja. A konfiguráció és a kódot az intelligens szerződésben meghatározott közötti kapcsolat áll fenn. Felel meg az alkalmazás létrehozása a szerződés részletei, Funkciók, paraméterek és típusok szükségesek. Blockchain munkaterület ellenőrzi az alkalmazás létrehozása előtt fájlokat. 

### <a name="contract"></a>Szerződés

Blockchain munkaterület szerződések örökölhet kell a **WorkbenchBase** alaposztály. Ha a szerződés deklaráló, kell argumentumként adja meg az alkalmazás és a munkafolyamat nevét.

Adja hozzá a **szerződés** fejlécének a `HelloBlockchain.sol` intelligens szerződés forráskód fájlja. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

A szerződés kell örökölnie a **WorkbenchBase** alaposztály, és adja át a paraméterek **ApplicationName** és a munkafolyamat **neve** az a konfiguráció fájl. Ebben az esetben az alkalmazás és a munkafolyamat nevét azonosak.

### <a name="state-variables"></a>Állapot változók

Rendszerállapot-változókat minden szerződés példány állapotának értékek tárolásához. A rendszerállapot-változókat a szerződésben meg kell egyeznie a munkafolyamat-tulajdonságok a a konfigurációs fájlban.

A rendszerállapot-változókat hozzáadni a szerződést a `HelloBlockchain.sol` intelligens szerződés forráskód fájlja. 

```
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

A konstruktor bemeneti paraméterek egy új intelligens szerződés példányát munkafolyamat határozza meg. A konstruktor van deklarálva, a függvény neve megegyezik a szerződés az. A konstruktor szükséges paraméterek konstruktorparaméterek a konfigurációs fájlban van meghatározva. A száma, sorrendben és típusú paramétert meg kell egyezniük a fájlt.

A konstruktor függvényben szereplő bármely üzleti logika írását szeretne végezni a szerződés létrehozása előtt. A rendszerállapot-változókat, amelynek értékek például inicializálni.

Mielőtt kilépne a konstruktor függvény, hívja az `ContractCreated()` függvény. Ez a függvény egy szerződés létrejött Blockchain munkaterület értesíti.

A konstruktor függvény hozzáadni a szerződést a `HelloBlockchain.sol` intelligens szerződés forráskód fájlja. 

```
    // constructor function
    function HelloBlockchain(string message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    
        // call ContractCreated() to create an instance of this workflow
        ContractCreated();
    }
```

### <a name="functions"></a>Functions

Csak olyan üzleti logika egy szerződés belül végrehajtható egységek függvényt. A függvény kötelező paraméter is meg van adva függvény paraméterei a konfigurációs fájlban. A száma, sorrendben és típusú paramétert meg kell egyezniük a fájlt. Funkciók társítva a következőhöz: átmenetek Blockchain munkaterület munkafolyamatban a konfigurációs fájlban. Átmenet egy alkalmazás munkafolyamat a szerződés alapján a következő szintre végrehajtott műveletet.

Szeretne végezni a függvényben lévő bármely üzleti logika írását. Például módosítása egy állapot változó értékét.

Mielőtt kilépne a függvény, hívja az `ContractUpdated()` függvény. A függvény értesíti Blockchain munkaterület szerződés állapota frissítve lett. Ha azt szeretné, a függvény végrehajtott állapot módosítások visszavonásához, hívja az revert(). Állítsa vissza elvetések állapot ContractUpdated() utolsó hívása óta végrehajtott módosításokat.

1. A következő funkciók hozzáadni a szerződést a `HelloBlockchain.sol` intelligens szerződés forráskód fájlja. 

    ```
        // call this function to send a request
        function SendRequest(string requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
    
            // call ContractUpdated() to record this action
            ContractUpdated('SendRequest');
        }
    
        // call this function to send a response
        function SendResponse(string responseMessage) public
        {
            Responder = msg.sender;
    
            // call ContractUpdated() to record this action
            ResponseMessage = responseMessage;
            State = StateType.Respond;
            ContractUpdated('SendResponse');
        }
    }
    ```

2. Mentse a `HelloBlockchain.sol` intelligens szerződés forráskód fájlja.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Blockchain munkaterület blockchain alkalmazás hozzáadása

Blockchain munkaterület blockchain alkalmazás hozzáadása, a konfigurációs és intelligens szerződés fájlok meghatározásához az alkalmazás feltöltése.

1. Egy webböngészőben keresse meg a Blockchain munkaterület webcímet. Például `https://{workbench URL}.azurewebsites.net/` a webes alkalmazás létrehozásakor Blockchain munkaterület központi telepítésekor. Információk megkereséséhez a Blockchain munkaterület webcímet: [Blockchain munkaterület webes URL-címe](blockchain-workbench-deploy.md#blockchain-workbench-web-url)
2. Jelentkezzen be egy Blockchain munkaterület rendszergazdaként. Felhasználók kezelésével kapcsolatban további információkért lásd: [felhasználók kezelése az Azure Blockchain munkaterület](blockchain-workbench-manage-users.md).
3. Válassza ki **alkalmazások** > **új**. A **új alkalmazás** ablaktáblán jelenik meg.
4. Válassza ki **töltse fel a szerződés konfigurációs** > **Tallózás** kereséséhez a **HelloBlockchain.json** létrehozott konfigurációs fájl. A konfigurációs fájl automatikusan érvényesítve. Válassza ki a **megjelenítése** érvényesítési hibák megjelenítése csatolás. Javítsa az érvényesítési hibák, az alkalmazás központi telepítése előtt.
5. Válassza ki **töltse fel a szerződés kód** > **Tallózás** kereséséhez a **HelloBlockchain.sol** intelligens szerződés forráskód fájlja. A forráskód fájlja automatikusan érvényesítve. Válassza ki a **megjelenítése** érvényesítési hibák megjelenítése csatolás. Javítsa az érvényesítési hibák, az alkalmazás központi telepítése előtt.
6. Válassza ki **telepítés** létrehozni a konfigurációs és intelligens szerződés fájlok alapján blockchain alkalmazást.

A blockchain alkalmazás központi telepítésének néhány percet vesz igénybe. Amikor a telepítés befejeződött, az új alkalmazás megjelenik a **alkalmazások**. 

> [!NOTE]
> Blockchain alkalmazások használatával is létrehozhat a [Azure Blockchain munkaterület REST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Blockchain alkalmazás tagok hozzáadása

Kérelem tagok hozzá az alkalmazáshoz kezdeményezéséhez és a műveletek végrehajtása a szerződéseket. Kérelem tagok kell lennie egy [Blockchain munkaterület Rendszergazda](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

1. Válassza ki **alkalmazások** > **Hello, Blockchain!**.
2. Az alkalmazáshoz tartozó tagok száma a lap jobb felső sarkában jelennek meg. Egy új alkalmazást a tagok száma nulla lesz.
3. Válassza ki a **tagok** az oldal jobb felső sarkában található hivatkozásra. A tagok az alkalmazás aktuális listája jelenik meg.
4. Válassza a tagsági lista **tagok hozzáadása**.
5. Válassza ki, vagy adja meg a tag nevét hozzá szeretné adni. Csak a Blockchain munkaterület bérlő szerepel az Azure AD-felhasználók szerepelnek. Ha a felhasználó nem található, akkor [adja hozzá az Azure Active Directory-felhasználók](blockchain-workbench-manage-users.md#add-azure-ad-users).
6. Válassza ki a **szerepkör** a tag számára. Válassza ki az első tag **kérelmező** szerepkörként.
7. Válassza ki **Hozzáadás** a tag a kapcsolódó szerepkör hozzáadása az alkalmazáshoz.
8. Az alkalmazás egy másik tag hozzáadása a **válaszadó** szerepkör.

Felhasználók Blockchain munkaterület kezelésével kapcsolatos további információkért lásd: [Azure Blockchain munkaterület felhasználók kezelése](blockchain-workbench-manage-users.md)

## <a name="next-steps"></a>További lépések

Útmutató cikkben létrehozott egy alapszintű kérés- és alkalmazás. Megtudhatja, hogyan használhatja az alkalmazást, folytassa a következő cikkben található útmutató.

> [!div class="nextstepaction"]
> [Egy blockchain alkalmazással](blockchain-workbench-use.md)

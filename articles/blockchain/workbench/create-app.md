---
title: A blockchain-alkalmazások létrehozása az Azure Blockchain Workbench használatával
description: Arra vonatkozó útmutató, hogyan hozhat létre a blockchain-alkalmazások az Azure Blockchain Workbench alkalmazásban.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/08/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 513f9501d46c9a391ad5db759fe943390b5a654c
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332421"
---
# <a name="tutorial-create-a-blockchain-application-in-azure-blockchain-workbench"></a>Oktatóanyag: A blockchain-alkalmazások létrehozása az Azure Blockchain Workbench használatával

Azure Blockchain Workbench használatával hozzon létre a blockchain-alkalmazások, amelyek több résztvevős munkafolyamatok konfigurációs és intelligens szerződés kód határozza meg.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * A blockchain-alkalmazások konfigurálása
> * Hozzon létre egy intelligens szerződés kód fájlt
> * Blockchain Workbenchet blockchain alkalmazás hozzáadása
> * Tagok hozzáadása a blockchain-application

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A Blockchain Workbench üzembe helyezés. További információkért lásd: [Azure Blockchain Workbench üzembe helyezési](deploy.md) üzembe helyezési részleteiért.
* Az Azure Active Directory-felhasználók Blockchain Workbenchet társított tenant. További információkért lásd: [az Azure AD-felhasználók hozzáadása az Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* A Blockchain Workbenchet rendszergazdai fiók. További információkért lásd: hozzáadása [Blockchain Workbench használatával a rendszergazdák az Azure Blockchain Workbench alkalmazásban](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Hello, Blockchain!

Készítsünk, amelyben egy kérést küld a kérelmezőnek egy egyszerű alkalmazás, és a egy válaszadó válaszol a kérelemre. Egy kérés lehet például, "Helló, hogy Ön?", és a válasz lehet "Vagyok nagyszerű!". Az alapul szolgáló blockchain mind a kérés- és a rendszer rögzíti. 

Kövesse a lépéseket az alkalmazás-fájlok létrehozása vagy beállíthatja a [töltse le a mintát a Githubon](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Konfigurációs fájl

Konfigurációs metaadatok határozza meg, a magas szintű munkafolyamatok és a blockchain-application interakció típusát. Konfigurációs metaadatok a munkafolyamat-szakaszok és a blockchain-application interakció típusát jelöli.

1. A kedvenc szerkesztőjében, hozzon létre egy fájlt `HelloBlockchain.json`.
2. Adja hozzá a következő JSON konfigurációjának az a blockchain-application meghatározása.

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

A konfigurációs fájl több részből áll. Egyes szakaszok részleteit a következők:

### <a name="application-metadata"></a>Alkalmazás-metaadatok

A konfigurációs fájl elejére az alkalmazásadatokat, például az alkalmazás nevét és leírását tartalmazza.

### <a name="application-roles"></a>Alkalmazás-szerepkörök

Az alkalmazás-szerepkörök szakaszban határozza meg a felhasználói szerepkörök, akik működésre vagy részt venni a blockchain-alkalmazáson belül. Ön meghatároz egy olyan különálló szerepkörök, funkciók alapján. A forgatókönyvben a kérés-válasz nincs megkülönböztetve a funkciókat egy entitás, amely a kérelmek, a kérelmező és a egy olyan entitás, amely létrehozza a válaszokat, válaszadó.

### <a name="workflows"></a>Munkafolyamatok

A munkafolyamatok határozzák meg, egy vagy több szakaszában, és a szerződés műveleteket. A kérés-válasz a forgatókönyvben az első szakasz (állapot), a munkafolyamat, a kérelmező (szerepkör) végrehajt egy műveletet (átmeneti) (funkce) kérést küldeni. A következő szakaszban (állapot) egy válaszadó (szerepkör) végrehajt egy műveletet (átmeneti) (funkce) választ el. Egy alkalmazás munkafolyamatot magába foglaló tulajdonságainak, functions, és a szükséges állapotok ismertetik a folyamatot egy szerződés. 

A konfigurációs fájlok tartalma kapcsolatos további információkért lásd: [Azure Blokklánc-munkafolyamatok konfigurációs hivatkozás](configuration.md).

## <a name="smart-contract-code-file"></a>Az intelligens szerződés kódfájl

Az intelligens szerződések képviseli a blockchain-application üzleti logikáját. Jelenleg a Blockchain Workbenchet támogatja Ethereum a blockchain Főkönyv. Ethereum használ [Solidity](https://solidity.readthedocs.io) a programozási nyelv, az intelligens szerződések önálló végrehajtó üzleti logika.

Az intelligens szerződések Solidity objektumorientált nyelv található osztályok hasonlóak. Minden szerződés tartalmazza az állapot és a functions-szakaszok és intelligens kontraktu műveletek végrehajtásához.

A kedvenc szerkesztőjében, hozzon létre egy fájlt, nevű `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Verzió pragma

Ajánlott eljárásként céloz meg Solidity verzióját jelzi. A verzió megadása elkerülhetők az inkompatibilitást Solidity jövőbeli verzióiban. 

Adja hozzá a következő verzió pragma tetején `HelloBlockchain.sol` intelligens szerződés kódfájlt.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="configuration-and-smart-contract-code-relationship"></a>A kód kapcsolat konfigurációs és intelligens szerződés

Blockchain Workbenchet a konfigurációs fájlt, és intelligens szerződés kódfájl használja a blockchain-alkalmazások létrehozásához. A konfigurációban és a kódot az intelligens szerződésben meghatározott közötti kapcsolat áll fenn. Podrobnosti o kontraktu, függvények, paraméterek és típusok felel meg az alkalmazás létrehozásához szükségesek. Blockchain Workbenchet ellenőrzi az alkalmazás létrehozása előtt fájlokat. 

### <a name="contract"></a>Szerződés

Adja hozzá a **szerződés** fejlécet a `HelloBlockchain.sol` intelligens szerződés kódfájlt.

```
contract HelloBlockchain {
```

### <a name="state-variables"></a>Állapot változók

Állapot változói a szerinti Szerződés példányonként tárolására. Az állapot változói a szerződésben meg kell egyeznie a munkafolyamat tulajdonságait a konfigurációs fájlban.

Az állapot változói a szerződés hozzáadása a `HelloBlockchain.sol` intelligens szerződés kódfájlt. 

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

### <a name="constructor"></a>A konstruktor

A konstruktor bemeneti paramétereket a munkafolyamat új intelligens szerződés példányát határozza meg. A konstruktor van deklarálva, egy függvény a neve megegyezik a szerződést. A konstruktor szükséges paramétereket a konfigurációs fájlban lévő konstruktor paraméterek vannak meghatározva. A szám, sorrend és paraméterek típusú egyeznie kell mindkét fájlban.

A konstruktor függvényben bármely üzleti logika írását a szerződés létrehozása előtt végrehajtására vonatkozó szándékát. Például inicializálni az állapot változói az értékek indítása.

A konstruktor függvény hozzáadása a szerződés a `HelloBlockchain.sol` intelligens szerződés kódfájlt. 

```
    // constructor function
    function HelloBlockchain(string message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Functions

A függvények olyan végrehajtható a szerződés belüli üzleti logikai egységeket. A függvény kötelező paraméterei függvény paraméterei a konfigurációs fájlban vannak meghatározva. A szám, sorrend és paraméterek típusú egyeznie kell mindkét fájlban. Függvények társítva átmenetek Blockchain Workbenchet munkafolyamatban a konfigurációs fájlban. Áttérés a következő szintre a szerződés határoz meg egy alkalmazás a munkafolyamat végrehajtott művelet.

Bármely üzleti logika írását a függvényben végrehajtására vonatkozó szándékát. Például módosítása egy állam változó értékét.

1. A következő funkciók hozzáadni a szerződést a `HelloBlockchain.sol` intelligens szerződés kódfájlt. 

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
        }
    
        // call this function to send a response
        function SendResponse(string responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. Mentse a `HelloBlockchain.sol` intelligens szerződés kódfájlt.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Blockchain Workbench használatával a blockchain-alkalmazás hozzáadása

Vegye fel a Blockchain Workbench használatával a blockchain alkalmazást, töltse fel a konfigurációs és intelligens szerződés fájlok határozzák meg az alkalmazást.

1. Keresse meg a Blockchain Workbenchet webhely címét egy webböngészőben. Ha például `https://{workbench URL}.azurewebsites.net/` Blockchain Workbenchet központi telepítésekor a webes alkalmazás létrehozása. A Blockchain Workbenchet webcímet megkereséséhez információkért lásd: [Blockchain Workbench webes URL-címe](deploy.md#blockchain-workbench-web-url)
2. Jelentkezzen be egy [Blockchain Workbenchet rendszergazda](manage-users.md#manage-blockchain-workbench-administrators).
3. Válassza ki **alkalmazások** > **új**. A **új alkalmazás** ablaktáblán jelenik meg.
4. Válassza ki **töltse fel a szerződés konfigurációs** > **Tallózás** keresse meg a **HelloBlockchain.json** létrehozott konfigurációs fájl. A konfigurációs fájl automatikusan érvényesítve lesz. Válassza ki a **megjelenítése** érvényesítési hibák megjelenítése hivatkozásra. Javítsa az érvényesítési hibák, az alkalmazás telepítése előtt.
5. Válassza ki **töltse fel a szerződés kód** > **Tallózás** keresse meg a **HelloBlockchain.sol** intelligens szerződés kódfájl. A kódfájl automatikusan érvényesítve lesz. Válassza ki a **megjelenítése** érvényesítési hibák megjelenítése hivatkozásra. Javítsa az érvényesítési hibák, az alkalmazás telepítése előtt.
6. Válassza ki **telepítés** hozhat létre a blockchain alkalmazást a konfiguráció és a fájlok intelligens szerződés alapján.

A blockchain-alkalmazás központi telepítésének néhány percet vesz igénybe. Üzembe helyezés befejezését követően az új alkalmazás megjelenik a **alkalmazások**. 

> [!NOTE]
> Blockchain-alkalmazások használatával is létrehozhat a [Azure Blockchain Workbench REST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Blockchain-application tagok hozzáadása

Kérelem tagok hozzáadása az alkalmazáshoz kezdeményezéséhez és a műveletek végrehajtása a szerződéseket. Kérelem tagok felvétele, kell lennie egy [Blockchain Workbenchet rendszergazda](manage-users.md#manage-blockchain-workbench-administrators).

1. Válassza ki **alkalmazások** > **Hello, Blockchain!**.
2. Az oldal jobb felső sarokban megjelenik az alkalmazáshoz tartozó tagok számát. Új alkalmazáshoz a tagok száma nulla lesz.
3. Válassza ki a **tagok** az oldal jobb felső sarokban található hivatkozásra. Tag az alkalmazás jelenlegi listája jelenik meg.
4. Válassza a tagsági lista **tagok hozzáadása**.
5. Válasszon ki vagy adja meg a hozzáadni kívánt a tag nevét. Csak a Blockchain Workbenchet bérlő szerepel az Azure AD-felhasználók jelennek meg. Ha a felhasználó nem található, akkor [adja hozzá az Azure AD-felhasználók](manage-users.md#add-azure-ad-users).
6. Válassza ki a **szerepkör** a tagja. Válassza ki az első tag **kérelmező** az szerepkörként.
7. Válassza ki **Hozzáadás** hozzárendelt szerepkörrel rendelkező tag hozzáadása az alkalmazáshoz.
8. Az alkalmazás egy másik tag hozzáadása a **válaszadó** szerepkör.

Blockchain Workbench használatával a felhasználók kezelésével kapcsolatos további információkért lásd: [Azure Blockchain Workbench használatával a felhasználók kezelése](manage-users.md)

## <a name="next-steps"></a>További lépések

Az útmutató a cikkben létrehozott egy egyszerű kérés- és alkalmazás. Ismerje meg, hogyan használják az alkalmazást, folytassa a következő cikkben található útmutató.

> [!div class="nextstepaction"]
> [Blockchain-alkalmazás használatával](use.md)

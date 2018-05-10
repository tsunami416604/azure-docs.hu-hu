---
title: Az Azure Blockchain munkaterület hivatkozás
description: Az Azure Blockchain munkaterület konfigurálása – áttekintés.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 5569a7608a61b4e79a03264e0ccf62682782264b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Az Azure Blockchain munkaterület hivatkozás

 Több résztvevős munkafolyamatok konfigurációs metaadatok és a kódot az intelligens az Azure Blockchain munkaterület alkalmazások legyenek. Konfigurációs metaadatok magas szintű munkafolyamatok és a blockchain alkalmazás beavatkozás modelljét határozza meg. Intelligens szerződések határozza meg az üzleti logika blockchain alkalmazás. Munkaterület használják a konfigurációs és intelligens szerződés kód blockchain alkalmazás felhasználói élmény létrehozásához.

Konfigurációs metaadatok határozza meg, hogy minden blockchain alkalmazáshoz a következő információkat: 

* Név és leírás a blockchain alkalmazás
* A felhasználókat, akik működésre vagy részt venni a blockchain alkalmazáson belül egyedi szerepkörök
* Egy vagy több munkafolyamatra. Minden munkafolyamat úgy működik, mint egy állapotgép az üzleti logika üzenetáramlásának szabályozására. Munkafolyamatok függetlenek, illetve egymással.

Minden egyes definiált munkafolyamat határozza meg a következő:

* Nevét és leírását, a munkafolyamat
* A munkafolyamat állapotát.  Minden egyes állapota folyamatábrán az üzleti logika egy fázisában. 
* A következő állapotban való célzó műveletek
* Felhasználói szerepkörök számára minden művelet
* Intelligens szerződések, amelyek megfelelnek az üzleti logikája kódfájlok

## <a name="application"></a>Alkalmazás

Egy blockchain alkalmazás konfigurációs metaadatok, a munkafolyamatok és a felhasználói szerepkörök is működésre, vagy az alkalmazáson belül részt tartalmaz.

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| ApplicationName | Egyedi alkalmazásnevet. A megfelelő intelligens szerződés kell használnia az azonos **ApplicationName** vonatkozó szerződés osztálynál.  | Igen |
| displayName | Az alkalmazás jól hangzó megjelenítési nevet. | Igen |
| Leírás | Az alkalmazás leírását. | Nem |
| ApplicationRoles | A gyűjtemény [ApplicationRoles](#application-roles). Felhasználói szerepkörök, kik hozhatnak működésre, illetve részt venni az alkalmazáson belül.  | Igen |
| Munkafolyamatok | A gyűjtemény [munkafolyamatok](#workflows). Minden munkafolyamat úgy működik, mint egy állapotgép az üzleti logika üzenetáramlásának szabályozására. | Igen |

Egy vonatkozó példáért lásd: [konfigurációs fájl példa](#configuration-file-example).

## <a name="workflows"></a>Munkafolyamatok

Egy alkalmazás üzleti logika, hogy hol tart a művelet hatására az üzleti logika áthelyezése egy másik áramló az állapotjelző gép előfordulhat, hogy modellezni. Egy munkafolyamat ilyen állapotok és műveletek gyűjteménye. Minden munkafolyamat egy vagy több intelligens szerződést, kódfájlok üzleti logikája megfelelői áll. Egy végrehajtható szerződés egy példányát munkafolyamat.

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | A munkafolyamat egyedi neve. A megfelelő intelligens szerződés kell használnia az azonos **neve** vonatkozó szerződés osztálynál. | Igen |
| displayName | A munkafolyamat jól hangzó megjelenítési nevet. | Igen |
| Leírás | A munkafolyamat leírását. | Nem |
| Kezdeményezők | A gyűjtemény [ApplicationRoles](#application-roles). Szerepkörök, felhasználók, akik jogosultak a szerződések a munkafolyamat létrehozásához rendelt. | Igen |
| StartState | A kezdeti állapot, a munkafolyamat nevét. | Igen |
| Tulajdonságok | A gyűjtemény [azonosítók](#identifiers). Lánc vagy a felhasználó a feladatkonfigurációkat olvasható adatok pedig a felhasználói élmény eszköz. | Igen |
| Konstruktor | Meghatározza a bemeneti paramétereket egy példányát munkafolyamat létrehozásához. | Igen |
| Functions | A gyűjtemény [funkciók](#functions) , amely a munkafolyamat hajtható végre. | Igen |
| állapotok | Munkafolyamat gyűjteménye [állapotok](#states). | Igen |

Egy vonatkozó példáért lásd: [konfigurációs fájl példa](#configuration-file-example).

## <a name="type"></a>Típus

A támogatott adattípusokat.

| Típus | Leírás |
|-------|-------------|
| Cím  | Blockchain címtípus, például a *szerződések* vagy *felhasználók* |
| logikai érték     | A logikai típusú |
| Szerződés | Típus adategyezmény-címe |
| int      | Integer adattípus |
| pénz    | Pénz adattípus |
| state    | A munkafolyamat állapota |
| karakterlánc   | Karakterlánc adattípusra |
| felhasználótól     | Típus felhasználó címe |
| time     | Idő adattípus |
|`[ Application Role Name ]`| Bármely alkalmazás-szerepkör által megadott nevét. Korlátozza a felhasználók számára az adott szerepkör típusú lehet. |

## <a name="constructor"></a>Konstruktor

Meghatározza a bemeneti paraméterek egy munkafolyamat-példányok esetében.

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Paraméterek | A gyűjtemény [azonosítók](#identifiers) intelligens szerződés kezdeményezéséhez szükséges. | Igen |

### <a name="constructor-example"></a>Konstruktor – példa

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Functions

Meghatározza a függvények, amelyek a munkafolyamat hajtható végre.

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | A függvény egyedi neve. A megfelelő intelligens szerződés kell használnia az azonos **neve** a alkalmazható függvény. | Igen |
| displayName | A függvény jól hangzó megjelenítési nevet. | Igen |
| Leírás | A funkció leírása | Nem |
| Paraméterek | A gyűjtemény [azonosítók](#identifiers) megfelelő a függvény paraméterei. | Igen |

### <a name="functions-example"></a>Funkciók – példa

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>állapotok

A munkafolyamaton belül egyedi állapotokat gyűjteménye. Az egyes üzleti logikát folyamatábrán lépése rögzíti. 

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | Az állapot egyedi nevét. A megfelelő intelligens szerződés kell használnia az azonos **neve** a megfelelő állapothoz. | Igen |
| displayName | Az állapot jól hangzó megjelenítési nevet. | Igen |
| Leírás | Az állapot leírása. | Nem |
| KészültségiSzint | Megjeleníti a folyamatot, az üzleti logika folyamatábrán belül Blockchain munkaterület felhasználói felületen megjelenő egész szám. | Igen |
| Stílus | Visual mutatót, amely azt jelzi, hogy az állapot sikeres vagy sikertelen állapotát jeleníti meg. Két érvényes érték: `Success` vagy `Failure`. | Igen |
| Átmenetek | A rendelkezésre álló gyűjteményben [átmenetek](#transitions) a jelenlegi állapotában a következő állapotok-készlethez. | Nem |

### <a name="states-example"></a>Állapotok – példa

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Átmenetek

A következő állapotban elérhető műveletek. Egy vagy több felhasználói szerepkört egy műveletet is, minden egyes állapotban, ahol egy művelet lehet, hogy szüntesse meg a munkafolyamat egy másik állapothoz állapotában. 

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| AllowedRoles | Alkalmazások szerepkörök listája az átmenet kezdeményezése engedélyezett. Lehet, hogy a megadott szerepkör minden felhasználó képes végrehajtani a műveletet. | Nem |
| AllowedInstanceRoles | Részt vevő vagy kezdeményezheti az átmenet engedélyezett intelligens szerződésben meghatározott felhasználói szerepkörök listáját. Példány szerepkörök definiálják **tulajdonságok** munkafolyamatok belül. Ezek a felhasználók határoz meg egy felhasználó részt vevő, vagy minden felhasználó, szerepkör típusú figyelésekor intelligens szerződésben meghatározott. | Nem |
| displayName | Az átmenet jól hangzó megjelenítési nevet. | Igen |
| Leírás | Az átmenet leírása. | Nem |
| Függvény | Az átmenet kezdeményezése függvény neve. | Igen |
| NextStates | A lehetséges következő állapotainak gyűjteményét sikeres átmenet után. | Igen |

### <a name="transitions-example"></a>Átmenetek – példa

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Alkalmazás-szerepkörök

Alkalmazás-szerepkörök a felhasználók, akik működésre, vagy az alkalmazásban részt rendelt szerepkörök a kulcstulajdonságokat határozza meg. Alkalmazási szerepköröknek segítségével korlátozhatja a műveletek és a blockchain részvételi alkalmazás és a megfelelő munkafolyamatok. 

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | Az alkalmazás-szerepkör egyedi neve. A megfelelő intelligens szerződés kell használnia az azonos **neve** a megfelelő szerepkörhöz. Alap típusnevek vannak fenntartva. Egy alkalmazás-szerepkör nem nevével megegyező névvel [típusa](#type)| Igen |
| Leírás | Az alkalmazás-szerepkör leírása. | Nem |

### <a name="application-roles-example"></a>Alkalmazás szerepkörök – példa

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Azonosítók

Azonosítók munkafolyamat-tulajdonságok konstruktor és függvényparamétereket leíró információkat gyűjteményét képviseli. 

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | A következő tulajdonság vagy paraméternek egyedi neve. A megfelelő intelligens szerződés kell használnia az azonos **neve** a megfelelő tulajdonság vagy paraméter. | Igen |
| displayName | A következő tulajdonság vagy paraméter jól hangzó megjelenítési nevet. | Igen |
| Leírás | A következő tulajdonság vagy paraméter leírását. | Nem |

### <a name="identifiers-example"></a>Azonosítók – példa

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Konfigurációs fájl példa

A következő példa egy alapszintű kérés-válasz kérelem, amelyben a kérelmező elküldi a kérelmet, és egy válaszadó küldése egy kérelemre adott válasz meghatározása.

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
      "Name": "RequestResponse",
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
## <a name="next-steps"></a>További lépések

[Az Azure Blockchain munkaterület központi telepítése](blockchain-workbench-deploy.md)


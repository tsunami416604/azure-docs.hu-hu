---
title: Az Azure Blockchain Workbench konfiguráció leírása
description: Az Azure Blockchain Workbench alkalmazás konfigurációs áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 7/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 60a84609c6ec8c1733f0938c69ab683f01ecb975
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224534"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Az Azure Blockchain Workbench konfiguráció leírása

 Az Azure Blockchain Workbench-alkalmazások olyan konfigurációs metaadatok és az intelligens szerződés kód által meghatározott több résztvevős munkafolyamatok. Konfigurációs metaadatok határozza meg, a magas szintű munkafolyamatok és a blockchain-application interakció típusát. Az intelligens szerződések adja meg a blockchain-application üzleti logikáját. Workbench konfigurációs és intelligens szerződés kód használatával hozzon létre a blockchain-application felhasználói élményt.

Konfigurációs metaadatok megadja a blockchain-alkalmazások mindegyike a következő információkat: 

* Nevét és leírását, a blockchain-application
* Egyedi szerepkörök is működjön, és részt venni a blockchain-application belüli felhasználók számára
* Egy vagy több munkafolyamatra. Mindegyik munkafolyamat az üzleti logika szabályozásához állapotautomata funkcionál. A munkafolyamatok lehet független vagy egymással kapcsolatot tarthatnak.

Minden egyes meghatározott munkafolyamat adja meg a következő:

* Nevét és leírását, a munkafolyamat
* A munkafolyamat-állapotok.  Minden egyes állapota egy üzleti logikát átvitelvezérlés fázisában. 
* Átmenet tovább állapotára célzó műveletek
* Felhasználói szerepkörök engedélyezett minden művelet
* Az intelligens szerződések, amelyek az üzleti logika kódfájlok

## <a name="application"></a>Alkalmazás

A blockchain-alkalmazások konfigurációs metaadatok, a munkafolyamatok és a felhasználói szerepkört betöltő jár el vagy az alkalmazásban lévő részt is tartalmaz.

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Alkalmazásnév | Egyedi alkalmazásnevet. A megfelelő intelligens szerződést kell használnia az azonos **ApplicationName** a vonatkozó szerződési osztályhoz.  | Igen |
| Megjelenítendő név | Az alkalmazás rövid megjelenített neve. | Igen |
| Leírás | Az alkalmazás leírását. | Nem |
| ApplicationRoles | A gyűjtemény [ApplicationRoles](#application-roles). Felhasználói szerepkörök számára is reagálhat, vagy hogy részt vegyen, az alkalmazáson belül.  | Igen |
| Munkafolyamatok | A gyűjtemény [munkafolyamatok](#workflows). Mindegyik munkafolyamat az üzleti logika szabályozásához állapotautomata funkcionál. | Igen |

Egy vonatkozó példáért lásd: [konfigurációs fájl példa](#configuration-file-example).

## <a name="workflows"></a>Munkafolyamatok

Egy alkalmazás üzleti logika egy állapotú gép, hol tart a művelet hatására a folyamatot, az üzleti logika áthelyezése egyik állapotból a másikba, előfordulhat, hogy modellezni. Egy munkafolyamat olyan ilyen állapotok és tevékenységek gyűjteménye. Minden munkafolyamat egy vagy több intelligens szerződések, amelyek tartalmazzák az üzleti logika kódfájlok áll. Egy végrehajtható szerződés egy munkafolyamat egy példányát.

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | A munkafolyamat egyedi neve. A megfelelő intelligens szerződést kell használnia az azonos **neve** a vonatkozó szerződési osztályhoz. | Igen |
| Megjelenítendő név | A munkafolyamat megjelenített felhasználóbarát neve. | Igen |
| Leírás | A munkafolyamat leírása. | Nem |
| Kezdeményező | A gyűjtemény [ApplicationRoles](#application-roles). A felhasználók számára, akik jogosultak a szerződések létrehozása a munkafolyamat rendelt szerepkörök. | Igen |
| StartState | A kezdeti állapota a munkafolyamat nevét. | Igen |
| Tulajdonságok | A gyűjtemény [azonosítók](#identifiers). Jelöli data láncon kívüli vagy teszi a felhasználó olvasható élmény eszközt. | Igen |
| A konstruktor | Bemeneti paramétereit a munkafolyamat egy példányát határozza meg. | Igen |
| Functions | Gyűjteménye [funkciók](#functions) , amely a munkafolyamat hajtható végre. | Igen |
| Állapotok | A munkafolyamat gyűjteménye [állapotok](#states). | Igen |

Egy vonatkozó példáért lásd: [konfigurációs fájl példa](#configuration-file-example).

## <a name="type"></a>Típus

Támogatott adattípusok.

| Típus | Leírás |
|-------|-------------|
| Cím  | Blockchain-címtípus, mint például *szerződések* vagy *felhasználók* |
| Logikai     | Logikai adattípus |
| Szerződés | Cím típusa szerződés |
| Enum     | Névvel ellátott értékek sorszámozott készlete. A számbavételi típus használatakor is EnumValues listáját adja meg. Minden egyes értéke legfeljebb 255 karakter hosszúságú lehet. Érvényes értéket karakterek a következők felső és kisbetűs betűket (A – Z, a – z), és számokat (0 – 9). |
| int      | Integer adattípus |
| költséget takaríthat meg    | Pénz adattípus |
| state    | A munkafolyamat-állapot |
| sztring   | Karakterlánc adattípus |
| Felhasználó     | Írja be a felhasználó címe |
| time     | Idő adattípus |
|`[ Application Role Name ]`| Bármely alkalmazás-szerepkör a megadott név. Korlátozza a felhasználók számára az adott szerepkör típusú lehet. |

### <a name="example-configuration-of-type-string"></a>Példa konfigurációs karakterlánc típusú

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Példa felsorolási típus konfigurálása

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>A Solidity enumerálástípusra használatával

Ha egy konfigurációs van definiálva, Solidity enumerálástípusok is használhatja. Ha például PropertyTypeEnum nevű enum definiálhat.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

A karakterláncok listájának meg kell egyeznie a konfigurációs és intelligens szerződés csak a Blockchain Workbenchet érvényes és következetes deklarációk között.

Hozzárendelési. példa:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Függvény paraméterrel. példa: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>A konstruktor

Bemeneti paramétereket a munkafolyamat egy példányát határozza meg.

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Paraméterek | A gyűjtemény [azonosítók](#identifiers) egy intelligens szerződés kezdeményezéséhez szükséges. | Igen |

### <a name="constructor-example"></a>Konstruktor példa

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

A munkafolyamat a végrehajtható függvények határozza meg.

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | A függvény egyedi neve. A megfelelő intelligens szerződést kell használnia az azonos **neve** a alkalmazni függvény. | Igen |
| Megjelenítendő név | A függvény rövid megjelenített neve. | Igen |
| Leírás | A funkció leírása | Nem |
| Paraméterek | A gyűjtemény [azonosítók](#identifiers) megfelelő paramétereire a függvény. | Igen |

### <a name="functions-example"></a>Functions-példa

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

## <a name="states"></a>Állapotok

Egy munkafolyamaton belül egyedi állapotok gyűjteménye. Minden állapot egyik lépése az üzleti logika átvitelvezérlés rögzíti. 

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | Az állapot egyedi nevét. A megfelelő intelligens szerződést kell használnia az azonos **neve** a megfelelő állapothoz. | Igen |
| Megjelenítendő név | Az állapot hangzó megjelenítési nevet. | Igen |
| Leírás | Az állapot leírása. | Nem |
| KészültségiSzint | Egy egész számot a Blockchain Workbenchet felhasználói felületen a folyamat ellenőrzéséhez az üzleti logika átvitelvezérlés belül jelenik meg. | Igen |
| Stílus | Vizuális mutató jelzi, hogy az állapot a sikeres vagy sikertelen állapotát jelöli. Két érvényes értékek: `Success` vagy `Failure`. | Igen |
| Átmenetek | A rendelkezésre álló gyűjtemény [átmenetek](#transitions) aktuális állapotát a következő állapotok-készlethez. | Nem |

### <a name="states-example"></a>Állapotok példa

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

A következő állapotban elérhető műveletek. Egy vagy több felhasználói szerepkör egyes állapotokhoz, ahol áttérés a művelet lehetséges, hogy a munkafolyamat állapotát egy másik olyan állapotban található egy művelet végezhet. 

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| AllowedRoles | Az átállás kezdeményezése engedélyezett alkalmazások szerepkörök listája. Lehet, hogy minden felhasználó a megadott szerepkör tudni elvégezni a műveletet. | Nem |
| AllowedInstanceRoles | Való részvétellel vagy az átállás kezdeményezése engedélyezett intelligens szerződésben meghatározott felhasználói szerepkörök listája. Példány szerepkörök meghatározott **tulajdonságok** munkafolyamatok belül. AllowedInstanceRoles egy felhasználó részt vesz egy intelligens szerződés egy példányát jelentik. AllowedInstanceRoles lehetővé teszik, korlátozhatja a felhasználói szerepkörhöz művelet véve egy szerződés-példányban.  Például előfordulhat, hogy csak szeretné, hogy a felhasználó, aki létrehozta a szerződést (InstanceOwner) tudják megszűnik ahelyett, hogy a szerepkör típusa (tulajdonos) lévő összes felhasználó számára, ha a szerepkör AllowedRoles megadott. | Nem |
| Megjelenítendő név | Az átmenet hangzó megjelenítési nevet. | Igen |
| Leírás | A Váltás leírása. | Nem |
| Függvény | Az átállás kezdeményezése a függvény neve. | Igen |
| NextStates | Egy lehetséges tovább állapotainak gyűjteményét sikeres váltás után. | Igen |

### <a name="transitions-example"></a>Átmenet példa

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

Alkalmazás-szerepkörök meghatározhatja egy adott szerepkörök, felhasználók, akik jár el, vagy hogy részt vegyen, az alkalmazáson belül kívánja kell hozzárendelni. Alkalmazás-szerepkörök használható műveletek és a blockchain belül való részvétel korlátozása az alkalmazás és a megfelelő munkafolyamatok. 

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | Az alkalmazás-szerepkör egyedi neve. A megfelelő intelligens szerződést kell használnia az azonos **neve** a alkalmazni szerepkörhöz. Základní typ nevek használata nem engedélyezett. Nem lehet nevezze el az alkalmazás-szerepkör neve megegyezik a [típusa](#type)| Igen |
| Leírás | Az alkalmazás-szerepkör leírása. | Nem |

### <a name="application-roles-example"></a>Példa az alkalmazások szerepkörök

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

Azonosítók munkafolyamat tulajdonságait, konstruktor és függvény paraméterei leírására szolgáló információkat gyűjteményét képviseli. 

| Mező | Leírás | Szükséges |
|-------|-------------|:--------:|
| Name (Név) | A tulajdonság vagy paraméter egyedi neve. A megfelelő intelligens szerződést kell használnia az azonos **neve** a megfelelő tulajdonság vagy paraméter. | Igen |
| Megjelenítendő név | A paraméter vagy hangzó megjelenítési nevet. | Igen |
| Leírás | A tulajdonság, vagy a paraméter leírása. | Nem |

### <a name="identifiers-example"></a>Azonosítók példa

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

Eszköz átadása egy intelligens szerződés forgatókönyv vásárlása és értékesítése a nagy értékű eszközökhöz, egy vizsgáló és appraiser igénylő. Az eladók listázhatja eszközeik hárítható el egy eszköz átviteli intelligens szerződés szerint. Adatoktól a szövegfordításon keresztül teheti ajánlatok az intelligens szerződés művelet végrehajtásával, és más feleknek műveleteket végezhet vizsgálja meg, vagy az eszköz értékelje. Miután az eszköz be van jelölve is megvizsgálni, és értékelni, a vevő és értékesítői megerősíti az értékesítés újra a szerződés végrehajtásához beállítása előtt. A folyamat minden pontján minden felhasználónak, hogy a szerződés állapotának frissítésekor. 

Többek között a kódfájlok további információkért lásd: [eszköz átviteli minta az Azure Blockchain Workbench használatával](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

A következő konfigurációs fájl van az eszköz adatátviteli mintában:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
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
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
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
      },
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
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
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
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
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
    }
  ]
}
```
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench REST API leírása](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)


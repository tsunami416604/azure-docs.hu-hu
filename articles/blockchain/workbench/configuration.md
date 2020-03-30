---
title: Az Azure Blockchain Workbench konfigurációs metaadatokra vonatkozó hivatkozása
description: Az Azure Blockchain Workbench Előzetes alkalmazás konfigurációs metaadatai – áttekintés.
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 661e795f0e85f872b1072a8f641b8938115c5d7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252193"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Az Azure Blockchain Workbench konfigurációs útmutatója

Az Azure Blockchain Workbench-alkalmazások többrésztvevős munkafolyamatok, amelyeket konfigurációs metaadatok és intelligens szerződéskód határoz meg. A konfigurációs metaadatok határozzák meg a blockchain alkalmazás magas szintű munkafolyamatait és interakciós modelljét. Az intelligens szerződések határozzák meg a blockchain alkalmazás üzleti logikáját. A Workbench konfigurációt és intelligens szerződéskódot használ a blockchain alkalmazás felhasználói élményének létrehozásához.

A konfigurációmetaadatai a következő információkat határozzák meg az egyes blokklánc-alkalmazásokhoz:

* A blockchain alkalmazás neve és leírása
* Egyedi szerepkörök a blockchain alkalmazáson belül cselekedhető vagy részt vevő felhasználók számára
* Egy vagy több munkafolyamat. Minden munkafolyamat állapotgépként működik az üzleti logika áramlásának szabályozásához. A munkafolyamatok lehetnek függetlenek, vagy kölcsönhatásba léphetnek egymással.

Minden egyes definiált munkafolyamat a következőket határozza meg:

* A munkafolyamat neve és leírása
* a munkafolyamat tagállamait.  Minden állapot az üzleti logika vezérlési folyamatának egy szakasza. 
* A következő állapotra való áttéréslépése
* Az egyes műveletek kezdeményezésére engedélyezett felhasználói szerepkörök
* Intelligens szerződések, amelyek üzleti logikát képviselnek a kódfájlokban

## <a name="application"></a>Alkalmazás

A blokklánc-alkalmazások konfigurációs metaadatokat, munkafolyamatokat és felhasználói szerepköröket tartalmaznak, akik cselekedhetnek vagy részt vehetnek az alkalmazáson belül.

| Mező | Leírás | Kötelező |
|-------|-------------|:--------:|
| ApplicationName | Egyedi alkalmazásnév. A megfelelő intelligens szerződésnek ugyanazt az **ApplicationName-t** kell használnia a megfelelő szerződésosztályhoz.  | Igen |
| DisplayName | Az alkalmazás rövid megjelenítendő neve. | Igen |
| Leírás | A kérelem leírása. | Nem |
| Alkalmazási szerepkörök | [ApplicationRoles](#application-roles)gyűjtemény . Felhasználói szerepkörök, akik cselekedhetnek vagy részt vehetnek az alkalmazásban.  | Igen |
| Munkafolyamatok | [Munkafolyamatok](#workflows)gyűjteménye . Minden munkafolyamat állapotgépként működik az üzleti logika áramlásának szabályozásához. | Igen |

Például lásd: [konfigurációs fájl példa](#configuration-file-example).

## <a name="workflows"></a>Munkafolyamatok

Az alkalmazás üzleti logikája állapotgépként modellezhető, ahol egy művelet művelet eredményeképpen az üzleti logika folyamata egyik állapotból a másikba kerül. A munkafolyamat az ilyen állapotok és műveletek gyűjteménye. Minden munkafolyamat egy vagy több intelligens szerződésből áll, amelyek a kódfájlok üzleti logikáját képviselik. A végrehajtható szerződés a munkafolyamat egy példánya.

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Név | Egyedi munkafolyamat-név. A megfelelő intelligens szerződésnek ugyanazt a nevet kell **használnia** az adott szerződésosztályhoz. | Igen | 50 |
| DisplayName | A munkafolyamat rövid megjelenítendő neve. | Igen | 255 |
| Leírás | A munkafolyamat leírása. | Nem | 255 |
| Kezdeményezők | [ApplicationRoles](#application-roles)gyűjtemény . Olyan szerepkörök, amelyek olyan felhasználókhoz vannak rendelve, akik jogosultak szerződéseket létrehozni a munkafolyamatban. | Igen | |
| StartState (Kezdőállam) | A munkafolyamat kezdeti állapotának neve. | Igen | |
| Tulajdonságok | [Azonosítók](#identifiers)gyűjteménye . Azokat az adatokat jelöli, amelyek láncon kívül olvashatók vagy egy felhasználói élményeszközben láthatók. | Igen | |
| Konstruktor | A munkafolyamat egy példányának létrehozásához bemeneti paramétereket határoz meg. | Igen | |
| Functions | A munkafolyamatban végrehajtható [függvények](#functions) gyűjteménye. | Igen | |
| Állapotok | [Munkafolyamat-állapotok](#states)gyűjteménye. | Igen | |

Például lásd: [konfigurációs fájl példa](#configuration-file-example).

## <a name="type"></a>Típus

Támogatott adattípusok.

| Típus | Leírás |
|-------|-------------|
| address  | A blokklánc címtípusa, például *szerződések* vagy *felhasználók.* |
| tömb    | Egyszintű tömb típusú egész, bool, pénz, vagy idő. A tömbök lehetnek statikusak vagy dinamikusak. Az **ElementType segítségével** adja meg a tömbelemeinek adattípusát. Lásd [a példakonfigurációt](#example-configuration-of-type-array). |
| Bool     | Logikai adattípus. |
| Szerződés | Szerződés típusú cím. |
| Enum     | Elnevezett értékek számba vétele. A felsorolástípus használatakor az EnumValues listát is meg kell adnia. Minden érték legfeljebb 255 karakter. Az érvényes értékkarakterek közé tartoznak a kis- és nagybetűk (A-Z, a-z) és a számok (0-9). Lásd [a példakonfigurációt és -használatot a Solidity területen.](#example-configuration-of-type-enum) |
| int      | Egész adattípus. |
| Pénzt    | Pénz adattípusa. |
| state    | Munkafolyamat állapota. |
| sztring  | Sztring adattípusú. Legfeljebb 4000 karakter. Lásd [a példakonfigurációt](#example-configuration-of-type-string). |
| felhasználó!     | Felhasználó típusú cím. |
| time     | Időadattípus. |
|`[ Application Role Name ]`| Az alkalmazásszerepkörben megadott bármely név. Korlátozza, hogy a felhasználók ilyen szerepkörtípusba legyenek. |

### <a name="example-configuration-of-type-array"></a>Példa típusú tömb konfigurációjára

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Tömb típusú tulajdonság használata

Ha egy tulajdonságot típustömbként határoz meg a konfigurációban, akkor explicit leési függvényt kell megadnia a tömbtípus nyilvános tulajdonságának visszaadására a Solidity függvényben. Példa:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Példa karakterlánc típusú konfigurációra

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

### <a name="example-configuration-of-type-enum"></a>Példa felsorakó típus konfigurációjára

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

#### <a name="using-enumeration-type-in-solidity"></a>Felsorolástípus használata a tömörségben

Miután egy felsorolás definiált a konfigurációban, használhatja a sorszámbavételi típusokat a Tömörségben. Például megadhat egy PropertyTypeEnum nevű felsoratot.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

A karakterláncok listájának meg kell egyeznie a konfiguráció és az intelligens szerződés között, hogy érvényes és konzisztens deklarációk legyenek a Blockchain Workbench-ben.

Példa hozzárendelésre:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Példa függvényparaméterre: 

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

## <a name="constructor"></a>Konstruktor

A munkafolyamat egy példányának bemeneti paramétereit határozza meg.

| Mező | Leírás | Kötelező |
|-------|-------------|:--------:|
| Paraméterek | Intelligens szerződés kezdeményezéséhez szükséges [azonosítók](#identifiers) gyűjteménye. | Igen |

### <a name="constructor-example"></a>Példa konstruktorra

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

A munkafolyamaton végrehajtható függvényeket határozza meg.

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Név | A függvény egyedi neve. A megfelelő intelligens szerződésnek ugyanazt a nevet kell **használnia** a megfelelő függvényhez. | Igen | 50 |
| DisplayName | A függvény rövid megjelenítendő neve. | Igen | 255 |
| Leírás | A függvény leírása | Nem | 255 |
| Paraméterek | A függvény paramétereinek megfelelő [azonosítók](#identifiers) gyűjteménye. | Igen | |

### <a name="functions-example"></a>Példa függvényekre

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

A munkafolyamaton belüli egyedi állapotok gyűjteménye. Minden állapot rögzít egy lépést az üzleti logika vezérlési folyamatában. 

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Név | Az állam egyedi neve. A megfelelő intelligens szerződésnek ugyanazt a nevet kell **használnia** a megfelelő állapothoz. | Igen | 50 |
| DisplayName | Az állapot rövid megjelenítendő neve. | Igen | 255 |
| Leírás | Az állapot leírása. | Nem | 255 |
| Százalékteljes | A Blockchain Workbench felhasználói felületén megjelenő egész érték az üzleti logika vezérlési folyamatán belüli előrehaladás megjelenítéséhez. | Igen | |
| Stílus | Vizuális tipp, amely azt jelzi, hogy az állapot sikeres vagy sikertelen állapotot jelent-e. Két érvényes érték `Success` létezik: vagy `Failure`. | Igen | |
| Áttűnések | Az aktuális állapotról a következő állapotra [való váltandó átmenetek](#transitions) gyűjteménye. | Nem | |

### <a name="states-example"></a>Példa államokra

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
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Áttűnések

Elérhető műveletek a következő állapotban. Egy vagy több felhasználói szerepkör minden államban végrehajthat egy műveletet, ahol egy művelet átválthat egy állapotot a munkafolyamat egy másik állapotába. 

| Mező | Leírás | Kötelező |
|-------|-------------|:--------:|
| AllowedRoles (AllowedRoles) | Az átmenet kezdeményezésére engedélyezett alkalmazásszerepkörök listája. Előfordulhat, hogy a megadott szerepkör összes felhasználója végrehajthatja a műveletet. | Nem |
| AllowedInstanceRoles (AllowedInstanceRoles) | Az átmenet kezdeményezésére engedélyezett intelligens szerződésben részt vevő vagy megadott felhasználói szerepkörök listája. A példányszerepkörök a munkafolyamatokon belüli **tulajdonságokban** vannak definiálva. Az AllowedInstanceRoles egy intelligens szerződés példányában részt vevő felhasználót jelöl. Az AllowedInstanceRoles lehetővé teszi, hogy egy műveletet egy szerződéspéldány felhasználói szerepkörére korlátozza.  Előfordulhat például, hogy csak azt szeretné engedélyezni, hogy a szerződést létrehozó felhasználó (InstanceOwner) a szerepkörtípusban (Tulajdonos) szereplő összes felhasználó helyett bonthassa ki, ha megadta a szerepkört az AllowedRoles-ban. | Nem |
| DisplayName | Az átmenet rövid megjelenítendő neve. | Igen |
| Leírás | Az átmenet leírása. | Nem |
| Függvény | Az átmenetet kezdeményező függvény neve. | Igen |
| Következő államok | A sikeres átmenet után a lehetséges következő állapotok gyűjteménye. | Igen |

### <a name="transitions-example"></a>Példa átmenetekre

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

Az alkalmazásszerepkörök olyan szerepköröket határoznak meg, amelyek az alkalmazáson belül cselekedni vagy részt venni kívánó felhasználókhoz rendelhetők. Az alkalmazásszerepkörök segítségével korlátozhatja a műveleteket és a részvételt a blokklánc-alkalmazáson és a megfelelő munkafolyamatokban. 

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Név | Az alkalmazásszerepkör egyedi neve. A megfelelő intelligens szerződésnek ugyanazt a nevet kell **használnia** a megfelelő szerepkörhöz. Az alaptípusnevek le vannak foglalva. A Típus névvel megegyező nevű alkalmazásszerepkör nem [nevezhető el.](#type)| Igen | 50 |
| Leírás | Az alkalmazásszerepkör leírása. | Nem | 255 |

### <a name="application-roles-example"></a>Példa alkalmazásszerepkörökre

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

Az azonosítók a munkafolyamat tulajdonságainak, a konstruktornak és a függvényparamétereknek a leírásához használt információk gyűjteményét jelentik. 

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Név | A tulajdonság vagy paraméter egyedi neve. A megfelelő intelligens szerződésnek ugyanazt a nevet kell **használnia** a megfelelő tulajdonsághoz vagy paraméterhez. | Igen | 50 |
| DisplayName | A tulajdonság vagy paraméter rövid megjelenítendő neve. | Igen | 255 |
| Leírás | A tulajdonság vagy paraméter leírása. | Nem | 255 |
| Típus | Tulajdonság [adattípusa](#type). | Igen |

### <a name="identifiers-example"></a>Példa azonosítókra

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

## <a name="configuration-file-example"></a>Példa konfigurációs fájlra

Az eszközátruházás egy intelligens szerződési forgatókönyv nagy értékű eszközök vételére és eladására, amelyekhez ellenőrre és értékbecslőre van szükség. Az eladók az eszközátruházási intelligens szerződés létrehozásával listázhatják eszközeiket. A vevők az intelligens szerződéssel kapcsolatos művelettel tehetnek ajánlatot, és más felek műveleteket tehetnek az eszköz vizsgálatára vagy értékelésére. Miután az eszköz van megjelölve mind ellenőrzött és értékelik, a vevő és az eladó megerősíti az értékesítés újra, mielőtt a szerződés van beállítva, hogy teljes. A folyamat minden egyes pontján minden résztvevő láthatja a szerződés naprakészen történő állapotát. 

További információ, beleértve a kódfájlokat, [lásd: eszközátadási minta az Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

A következő konfigurációs fájl az eszközátviteli mintához kapcsolódik:

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
          "Description": "Asset transfer has been canceled",
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


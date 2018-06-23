---
title: Adatok másolása a Dynamics CRM vagy Dynamics 365 (közös szolgáltatás) és Azure Data Factory használatával |} Microsoft Docs
description: Adatok másolása a Microsoft Dynamics CRM-hez vagy a Microsoft Dynamics 365 (közös adatszolgáltatás) támogatott adattárolókhoz gyűjtése, vagy a forrás adattárolókhoz a Dynamics CRM vagy Dynamics 365 használata támogatott a másolási tevékenység az adatokat előállító adatcsatorna.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
ms.openlocfilehash: 2078fd3d23891a471d34de89b8e068ae896f8eb2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332303"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Adatok másolása Dynamics 365 (közös adatszolgáltatás) vagy a Dynamics CRM és Azure Data Factory használatával

Ez a cikk a másolási tevékenység használható az Azure Data Factory másolja az adatokat a Microsoft Dynamics 365 vagy a Microsoft Dynamics CRM és módját ismerteti. Buildekről nyújtanak a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory, amely általában a rendelkezésre álló, 1 verzióját használja [másolási tevékenység az 1-es verziójú](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatokat másolhat a Dynamics 365 (közös adatszolgáltatás) vagy a Dynamics CRM bármely támogatott fogadó adattárolóhoz. Is másolhatja adatok bármely támogatott forrás adattár Dynamics 365 (közös adatszolgáltatás) vagy a Dynamics CRM-hez. Adatforrások vagy mosdók a másolási tevékenység által támogatott adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

A Dynamics összekötő támogatja a következő Dynamics verziói és a hitelesítési típus. (Internetes Elérésű a rövid a internetre irányuló központi telepítési.)

| Dynamics verziók | Hitelesítéstípusok | A kapcsolódószolgáltatás-minták |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + Office365 hitelesítés](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 helyszíni ELÉRÉS <br> Dynamics CRM 2016 helyszíni ELÉRÉS <br> Dynamics CRM 2015 a helyszíni az ELÉRÉS | ELÉRÉS | [Dynamics a helyszíni az internetes Elérésű + ELÉRÉS hitelesítés](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

A Dynamics 365 konkrétan az alábbi alkalmazástípusokat támogatja:

- Dynamics 365 az értékesítés
- Dynamics 365 az ügyfélszolgálat
- Dynamics 365 mező szolgáltatás
- Dynamics 365 a projekt szolgáltatás automatizálásához
- Dynamics 365 a Marketing

Másik alkalmazás típusokat, például a műveletek és a pénzügyi, a tehetség, stb. használata nem támogatott.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják Dynamics adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A Dynamics társított szolgáltatás a következő tulajdonságok támogatottak.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 és a Dynamics CRM Online

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **Dynamics**. | Igen |
| deploymentType | A központi telepítési típus a Dynamics példány. Lehet **"Online"** Dynamics online. | Igen |
| serviceUri | A szolgáltatás URL-CÍMÉT a Dynamics példány, pl. `https://adfdynamics.crm.dynamics.com`. | Igen |
| authenticationType | A hitelesítési típus Dynamics kiszolgálóhoz csatlakozik. Adja meg **"Office365"** Dynamics online. | Igen |
| felhasználónév | Adja meg a felhasználónevet Dynamics való kapcsolódáshoz. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nincs forrás, Igen a fogadó Ha a forrás kapcsolódó szolgáltatás nem rendelkezik egy integrációs futásidejű |

>[!IMPORTANT]
>Dynamics adatait átmásolja, amikor az alapértelmezett Azure integrációs futásidejű nem hajthatók végre másolása. Ez azt jelenti, ha a forrás társított szolgáltatás nem rendelkezik a megadott integrációs futásidejű explicit módon [hozzon létre egy Azure integrációs futásidejű](create-azure-integration-runtime.md#create-azure-ir) egy helyen lévő közelében a Dynamics példányát. Rendelje hozzá azt a Dynamics társított szolgáltatás az alábbi példában látható módon.

>[!NOTE]
>A Dynamics összekötő "szervezetnév" tulajdonság segítségével azonosíthatja a Dynamics CRM/365 Online példányát használja. Eszköz tartja működik, amíg a használata javasolt a ahhoz, hogy a jobb teljesítmény érdekében például felderítési Ehelyett adja meg az új "serviceUri" tulajdonságot.

**Példa: A Dynamics online Office365-hitelesítés használatával**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 és a Dynamics CRM a helyszíni az ELÉRÉS

*Hasonlítsa össze a Dynamics online további tulajdonságok a következők: "állomásnév" és "port".*

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **Dynamics**. | Igen |
| deploymentType | A központi telepítési típus a Dynamics példány. Lehet **"OnPremisesWithIfd"** Dynamics helyszíni az Internetre.| Igen |
| Állomásnév | A helyszíni Dynamics kiszolgáló állomásnevét. | Igen |
| port | A helyszíni Dynamics kiszolgáló portját. | Nem, az alapértelmezett: 443 |
| Szervezetnév | A Dynamics példány szervezet nevét. | Igen |
| authenticationType | A hitelesítési típus a Dynamics kiszolgálóhoz való csatlakozáshoz. Adja meg **"Elérés"** Dynamics helyszíni az Internetre. | Igen |
| felhasználónév | Adja meg a felhasználónevet Dynamics való kapcsolódáshoz. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy a jelszó tárolása az Azure Key Vault választhat, és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem a forrást, a fogadó Igen |

>[!IMPORTANT]
>Használatával adatait átmásolhatja Dynamics, explicit módon [hozzon létre egy Azure integrációs futásidejű](create-azure-integration-runtime.md#create-azure-ir) a helyen lévő közelében a Dynamics példányát. Rendelje hozzá azt az alábbi példában látható módon a hivatkozott szolgáltatásban található.

**Példa: Dynamics a helyszíni az internetes Elérésű hitelesítéssel ELÉRÉS**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Dynamics dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása a kezdő és a Dynamics állítsa be a type tulajdonságot az adathalmaz **DynamicsEntity**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani **DynamicsEntity**. |Igen |
| entityName | Az entitást lekérdezni a logikai neve. | Nincs forrás (Ha a tevékenység forrása az "query" meg van adva), a fogadó az Igen |

> [!IMPORTANT]
>- Adatok másolása Dynamics, ha a "structure" szakasz Dynamics adatkészlet szükséges. Meghatározza az oszlop nevét és az adatok típusának Dynamics adatok, amelyet szeretne másolja át. További tudnivalókért lásd: [adatkészlet-szerkezetekben](concepts-datasets-linked-services.md#dataset-structure) és [adattípus-hozzárendelése Dynamics](#data-type-mapping-for-dynamics).
>- Ha adatok Dynamics, a "structure" szakasz választható Dynamics adatkészlet kerül. Oszlopok átmásolja a forrás adatkulcsokat határozza meg. Ha a forrás egy CSV-fájl, fejléc nélküli bemeneti adatkészlet, adja meg a "structure" az oszlop nevét és az adatok típusának. Leképezik a a CSV-fájlban szereplő egyenként sorrendben mezőkkel.

**Példa**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a forrás és a fogadó típusa Dynamics által támogatott tulajdonságokról listáját tartalmazza.

### <a name="dynamics-as-a-source-type"></a>Dynamics a forrás típusa

Adatok másolása Dynamics, állítsa be a forrás típusa a másolási tevékenység **DynamicsSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani **DynamicsSource**. | Igen |
| lekérdezés | FetchXML Dynamics használt jogvédett lekérdezésnyelvet (online és helyszíni). Tekintse meg a következő példát. További tudnivalókért lásd: [hozhatók létre olyan lekérdezések a FeachXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nem (Ha a "entityName" adatkészlet paraméter van megadva) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>A minta FetchXML lekérdezés

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics a fogadó típusa

Adatok másolása Dynamics, állítsa be a fogadó típusa a másolási tevékenység **DynamicsSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani **DynamicsSink**. | Igen |
| writeBehavior | A művelet írási viselkedését.<br/>Az engedélyezett értéket **"Upsert"**. | Igen |
| writeBatchSize | A sorok számát az egyes kötegekben Dynamics írt adatok. | Nem (alapértelmezett érték 10) |
| ignoreNullValues | Azt jelzi, hogy a bemeneti adatokból (kivéve a kulcsmezők) null értékek kihagyása írási művelet során.<br/>Két érték engedélyezett **igaz** és **hamis**.<br>- **Igaz**: változatlanul a célobjektum adatainak upsert/frissítési művelet végrehajtásakor. Szúrja be egy meghatározott alapértelmezett értéket, ha így tesz, az insert művelet.<br/>- **Hamis**: frissítse az adatokat a célobjektum NULL upsert/frissítési művelet végrehajtásakor. Helyezze be NULL értékre, ha így tesz, az insert művelet. | Nem (alapértelmezett értéke "false") |

>[!NOTE]
>Az alapértelmezett érték a fogadó "**writeBatchSize**"és a másolási tevékenység"**[parallelCopies](copy-activity-performance.md#parallel-copy)**" a Dynamics fogadó a rendszer mind a 10. Ezért 100 rekordig elküldi Dynamics egyidejűleg.

Dynamics 365 online, a maximális száma nincs [szervezet 2 egyidejű kötegelt hívásszám](https://msdn.microsoft.com/en-us/library/jj863631.aspx#Run-time%20limitations). Ha túllépi ezt a határértéket, a "Kiszolgáló elfoglalt" hiba történt legalább egyszer az első kérelem végrehajtása előtt. "WriteBatchSize" tartása, legfeljebb 10 ne volna ilyen sávszélesség-szabályozás számos egyidejű hívás.

Az optimális kombinációja "**writeBatchSize**"és"**parallelCopies**" függ a séma az entitás pl. száma oszlopok, sorméret csatlakoztatnia beépülő modulok/munkafolyamatok/munkafolyamat-tevékenységek száma e hívás, stb. Az alapértelmezett beállítás a 10 writeBatchSize * 10 parallelCopies a javaslat alapján Dynamics szolgáltatást, amely a legtöbb Dynamics entitás azonban nem lehet lehető legjobb teljesítményt is működhetnek. A teljesítmény észlelheti a beállításával a másolási tevékenység beállítások kombinációja.

**Példa**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Adattípus-leképezést Dynamics

Dynamics adatokat másolni, ha a következő megfeleltetéseket segítségével Dynamics adattípusok a Data Factory ideiglenes adattípusok. Hogyan a másolási tevékenység van leképezve a séma- és adatok típusa a fogadó kapcsolatban lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md).

A Data Factory megfelelő adattípus konfigurálása egy adatkészlet-szerkezetekben a következő hozzárendelési tábláját használatával a forrás Dynamics adatok típusa alapján.

| Dynamics adattípus | Data Factory ideiglenes adattípus | Támogatott forrásaként | A fogadó támogatott |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Hosszú | ✓ | ✓ |
| AttributeTypeCode.Boolean | Logikai | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | | 
| AttributeType.DateTime | Dátum és idő | ✓ | ✓ |
| AttributeType.Decimal | Decimális | ✓ | ✓ |
| AttributeType.Double | Dupla | ✓ | ✓ |
| AttributeType.EntityName | Sztring | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓ |
| AttributeType.ManagedProperty | Logikai | ✓ | |
| AttributeType.Memo | Sztring | ✓ | ✓ |
| AttributeType.Money | Decimális | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | Sztring | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> A Dynamics adattípusok AttributeType.CalendarRules és AttributeType.PartyList nem támogatottak.

## <a name="next-steps"></a>További lépések
Források és mosdók adat-előállítóban másolási tevékenység által támogatott adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).

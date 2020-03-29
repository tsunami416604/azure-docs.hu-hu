---
title: Adatok másolása a Dynamics rendszerben (Common Data Service)
description: Megtudhatja, hogy miként másolhat adatokat a Microsoft Dynamics CRM vagy a Microsoft Dynamics 365 (Common Data Service) rendszerről a támogatott fogadó adattárakba, illetve a támogatott forrásadat-tárolókból a Dynamics CRM vagy a Dynamics 365 rendszerbe egy adatfeldolgozó folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: d065439839ba5db479305ae81c61892cb5cf5e70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929456"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Adatok másolása a Dynamics 365 (Common Data Service) vagy a Dynamics CRM rendszerbe az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan lehet az Azure Data Factory másolási tevékenységhasználatával adatokat másolni a Microsoft Dynamics 365-ről vagy a Microsoft Dynamics CRM rendszerről. A [Tevékenység másolása áttekintéscímű](copy-activity-overview.md) cikkre épül, amely a Másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Dynamics 365 (Common Data Service) vagy a Dynamics CRM rendszerből bármely támogatott fogadó adattárba másolhat adatokat. Bármely támogatott forrásadattárból adatokat másolhat a Dynamics 365 (Common Data Service) vagy a Dynamics CRM rendszerbe. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Ez a Dynamics-összekötő támogatja a Dynamics 7.x-9.x verzióját online vagy helyszíni használatra. Konkrétabban,

- A 7.x verzió leképezi a Dynamics CRM 2015-öt
- A 8.x verzió leképezi a Dynamics CRM 2016-ot és a Dynamics 365 korai verzióját
- A 9.x verzió leképezi a Dynamics 365 újabb verzióját

Tekintse meg az alábbi táblázatot a megfelelő Dynamics-verziók/-termékek támogatott hitelesítési típusairól és konfigurációiról. (Az IFD az internetre néző telepítés rövidítése.)

| Dynamics verziók | Hitelesítési típusok | Csatolt szolgáltatásminták |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 online <br> Dynamics CRM Online | AAD szolgáltatásnév <br> Office365 | [Dynamics online + AAD szolgáltatásnév vagy Office365 hitelesítés](#dynamics-365-and-dynamics-crm-online) |
| A Dynamics 365 helyszíni rendszeraz IFD-vel <br> Dynamics CRM 2016 helyszíni ifd-vel <br> A Dynamics CRM 2015 helyszíni, AZ IFD-vel | IFD | [A helyszíni dinamika IFD + IFD hitelesítéssel](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

A Dynamics 365 esetében a következő alkalmazástípusok támogatottak:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Más alkalmazástípusokat, pl. pénzügyeket és műveleteket, tehetségeket stb.

Ez a Dynamics összekötő a [Dynamics XRM eszközelemre](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)épül.

>[!TIP]
>Ha adatokat szeretne másolni a **Dynamics 365 Finance and Operations**programból, használhatja a [Dynamics AX összekötőt.](connector-dynamics-ax.md)

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Dynamics-specifikus Data Factory entitások definiálására használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak a Dynamics csatolt szolgáltatás.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 és Dynamics CRM Online

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot Dynamics , DynamicsCrm vagy CommonDataServiceForApps **(Dynamics,** **DynamicsCrm**vagy **CommonDataServiceForApps**) tulajdonságra kell állítani. | Igen |
| központi telepítési típus | A Dynamics-példány központi telepítési típusa. A Dynamics online verzióhoz **"Online"** állapotban kell lennie. | Igen |
| serviceUri | A Dynamics-példány szolgáltatásURL-címe, `https://adfdynamics.crm.dynamics.com`pl. . | Igen |
| authenticationType | A Dynamics-kiszolgálóhoz való csatlakozás hitelesítési típusa. Az engedélyezett értékek a következők: **AADServicePrincipal** vagy **"Office365"**. | Igen |
| servicePrincipalId | Adja meg az Azure Active Directory-alkalmazás ügyfélazonosítóját. | Igen hitelesítés `AADServicePrincipal` használata esetén |
| servicePrincipalCredentialType | Adja meg az egyszerű szolgáltatáshitelesítéshez használandó hitelesítő adatok típusát. Az engedélyezett értékek a következők: **ServicePrincipalKey** vagy **ServicePrincipalCert**. | Igen hitelesítés `AADServicePrincipal` használata esetén |
| servicePrincipalCredential | Adja meg a szolgáltatásnév hitelesítő adatait. <br>Hitelesítő `ServicePrincipalKey` adatok típusaként `servicePrincipalCredential` használata esetén lehet egy karakterlánc (az ADF titkosítja azt csatolt szolgáltatás telepítéskor) vagy egy titkos kulcsot az AKV-ban. <br>Hitelesítő `ServicePrincipalCert` adatként `servicePrincipalCredential` való használat esetén az AKV-ban lévő tanúsítványra mutató hivatkozásnak kell lennie. | Igen hitelesítés `AADServicePrincipal` használata esetén | 
| felhasználónév | Adja meg a Dynamics rendszerhez csatlakozni kívánt felhasználónevet. | Igen hitelesítés `Office365` használata esetén |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen hitelesítés `Office365` használata esetén |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem a forráshoz, Igen a fogadóhoz, ha a forráscsatolt szolgáltatás nem rendelkezik integrációs futásidejűnel |

>[!NOTE]
>A Dynamics connector, amely a választható "organizationName" tulajdonságot használja a Dynamics CRM/365 Online példány azonosítására. Miközben folyamatosan működik, javasoljuk, hogy adja meg az új "serviceUri" tulajdonság helyett jobb teljesítményt például felderítés.

**Példa: Dynamics online az AAD szolgáltatásegyszerű + kulcshitelesítés használatával**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**Példa: Dynamics online az AAD szolgáltatásegyszerű + tanúsítványhitelesítés használatával**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**Példa: Dynamics online verzió az Office365-hitelesítés használatával**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>A Dynamics 365 és a Dynamics CRM helyszíni, AZ IFD-vel

*A Dynamics online rendszerhez képest további tulajdonságok: "hostName" és "port".*

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot Dynamics , DynamicsCrm vagy CommonDataServiceForApps **(Dynamics,** **DynamicsCrm**vagy **CommonDataServiceForApps**) tulajdonságra kell állítani. | Igen |
| központi telepítési típus | A Dynamics-példány központi telepítési típusa. Az IFD-vel rendelkező Helyszíni Dynamics esetében **"OnPremisesWithIfd"** legyen.| Igen |
| Hostname | A helyszíni Dynamics-kiszolgáló állomásneve. | Igen |
| port | A helyszíni Dynamics-kiszolgáló portja. | Nem, az alapértelmezett érték 443 |
| szervezetneve | A Dynamics-példány szervezeti neve. | Igen |
| authenticationType | A Dynamics kiszolgálóhoz való csatlakozáshoz szükséges hitelesítési típus. Adja meg az **"Ifd"** értéket az IFD-vel rendelkező Dynamics helyszíni rendszerhez. | Igen |
| felhasználónév | Adja meg a Dynamics rendszerhez csatlakozni kívánt felhasználónevet. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ezt a mezőt SecureStringként jelölheti meg, hogy biztonságosan tárolhassa az ADF-ben, vagy tárolja a jelszót az Azure Key Vaultban, és hagyja, hogy a másolási tevékenység lekérése az adatok másolásakor származzon – további információ a [Key Vault Áruházhitelesítő adataiból.](store-credentials-in-key-vault.md) | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem a forráshoz, Igen a mosogatóhoz |

**Példa: Helyszíni dinamika IFD-vel IFD-hitelesítéssel**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz a Dynamics adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a Dynamics programból és a Dynamics programba a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát **DynamicsEntity**, **DynamicsCrmEntity**vagy **CommonDataServiceForAppsEntity ( DynamicsEntity , vagy CommonDataServiceForAppsEntity**) típusú tulajdonságra kell állítani. |Igen |
| entitásnév | A beolvasandó entitás logikai neve. | Nem a forráshoz (ha a tevékenységforrásban "lekérdezés" van megadva), igen a fogadóhoz |

**Példa:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Dynamics forrás- és fogadótípusok által támogatott tulajdonságok listáját tartalmazza.

### <a name="dynamics-as-a-source-type"></a>Dinamika mint forrástípus

Adatok másolása a Dynamics programból, a következő tulajdonságokat támogatja a másolási tevékenység **forrása** szakaszban.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságát DynamicsSource , DynamicsCrmSource vagy CommonDataServiceForAppsSource **(DynamicsSource**, **DynamicsCrmSource**vagy **CommonDataServiceForAppsSource )** tulajdonságra kell állítani. | Igen |
| lekérdezés | A FetchXML egy saját lekérdezési nyelv, amelyet a Dynamics (online és helyszíni) használ. Tekintse meg a következő példát. További információ: [Lekérdezések létrehozása fetchxml fájllal.](https://msdn.microsoft.com/library/gg328332.aspx) | Nem (ha az adatkészletben az "entityName" van megadva) |

>[!NOTE]
>A PK oszlop mindig ki lesz másolva, még akkor is, ha a FetchXML lekérdezésben konfigurált oszlopvetület nem tartalmazza azt.

> [!IMPORTANT]
>- Amikor adatokat másol a Dynamics programból, a Dynamics programból származó explicit oszlopleképezés nem kötelező, de erősen ajánlott a determinisztikus másolási eredmény biztosítása érdekében.
>- A séma importálásakor a felhasználói felület szerzői során az ADF úgy következtet a sémából, hogy a Dynamics lekérdezés eredményéből a legfelső sorokat veszi át a forrásoszloplistájának inicializálásához, amely esetben a felső sorokban értékeket nem tartalmazó oszlopok kimaradnak. Ugyanez a viselkedés vonatkozik a másolási végrehajtásokra is, ha nincs explicit leképezés. Áttekintheti és további oszlopokat adhat hozzá a leképezéshez, amelyeket a másolási futtatás során figyelembe veszünk.

**Példa:**

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

### <a name="sample-fetchxml-query"></a>Minta FetchXML-lekérdezés

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

### <a name="dynamics-as-a-sink-type"></a>Dinamika mint mosogatótípus

Adatok másolásához a Dynamics, a következő tulajdonságok at támogatja a másolási tevékenység **fogadó** szakaszban.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységfogadó típustulajdonságának DynamicsSink , DynamicsCrmSink vagy **CommonDataServiceForAppsSink** **(DynamicsSink**, **DynamicsCrmSink**) vagy CommonDataServiceForAppsSink típusú tulajdonságának kell lennie. | Igen |
| writeBehavior (írási viselkedés) | A művelet írási viselkedése.<br/>Az engedélyezett érték **"Upsert"**. | Igen |
| másodlagos kulcsnév | Adja meg az entitáson definiált másodlagos kulcsnevet az "Upsert" végrehajtásához. | Nem |
| writeBatchSize | A Dynamics programba minden kötegben írt adatok sorszáma. | Nem (az alapértelmezett érték 10) |
| ignoreNullValues | Azt jelzi, hogy az írási művelet során figyelmen kívül kell-e hagyni a bemeneti adatok (a kulcsmezők kivételével) null értékeit.<br/>Az engedélyezett értékek **igazak** és **hamisak.**<br>- **Igaz**: A upsert/update művelet során változatlanul hagyja a célobjektumban lévő adatokat. Beszúrási művelet esetén szúrjon be egy definiált alapértelmezett értéket.<br/>- **Hamis**: Frissítse a célobjektumadatait NULL értékre upsert/update művelet esetén. Null érték beszúrása beszúrási művelet esetén. | Nem (az alapértelmezett érték hamis) |

>[!NOTE]
>A **"writeBatchSize**" fogadó alapértelmezett értéke és a Dynamics fogadó " copy activity "**[parallelCopies](copy-activity-performance.md#parallel-copy)**" másolási tevékenysége 10. Ezért egyszerre 100 rekordot küld a Dynamics rendszernek.

A Dynamics 365 online rendszerben [szervezetenként legfeljebb 2 kötegelt hívás érhető el.](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations) Ha ezt a korlátot túllépi, az első kérés végrehajtása előtt "Kiszolgáló foglalt" hiba lép fel. A "writeBatchSize" 10-es vagy annál kisebb tartása elkerülné az egyidejű hívások ilyen szabályozását.

A "**writeBatchSize**" és a "**parallelCopies**" optimális kombinációja az entitás sémájától függ, például az oszlopok számától, a sormérettől, a hívásokhoz kötött bővítmények/munkafolyamatok/munkafolyamat-tevékenységek számától stb. Az alapértelmezett beállítás 10 writeBatchSize * 10 parallelCopies a Dynamics szolgáltatás szerinti javaslat, amely a legtöbb Dynamics entitások, bár nem lehet a legjobb teljesítményt. A teljesítményt úgy állíthatja be, hogy módosítja a kombinációt a másolási tevékenység beállításaiban.

**Példa:**

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

## <a name="data-type-mapping-for-dynamics"></a>Adattípus-leképezés a Dynamics alkalmazáshoz

Amikor adatokat másol a Dynamics programból, a rendszer a következő leképezéseket használja a Dynamics adattípusokból a Data Factory köztes adattípusokba. Ha meg szeretné tudni, hogy a másolási tevékenység hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

Konfigurálja a megfelelő Data Factory adattípust egy adatkészlet-struktúrában a forrásDynamics adattípus alapján a következő leképezési tábla használatával.

| Dynamics adattípus | Adatgyár köztes adattípusa | Forrásként támogatott | Mosogatóként támogatott |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Hosszú | ✓ | ✓ |
| AttributeTypeCode.Logikai | Logikai | ✓ | ✓ |
| Attribútumtípus.Vevő | Guid | ✓ | |
| Attribútumtípus.Dátumidő | Datetime | ✓ | ✓ |
| AttributeType.Decimális | Decimal | ✓ | ✓ |
| Attribútumtípus.Dupla | Double | ✓ | ✓ |
| Attribútumtípus.Entitásnév | Sztring | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (egyetlen célkapcsolódó) |
| Attribútumtípus.ManagedProperty tulajdonság | Logikai | ✓ | |
| Attribútumtípus.Feljegyzés | Sztring | ✓ | ✓ |
| Attribútumtípus.Pénz | Decimal | ✓ | ✓ |
| Attribútumtípus.Tulajdonos | Guid | ✓ | |
| Attribútumtípus.Ellenőrzőlista | Int32 | ✓ | ✓ |
| Attribútumtípus.Egyedi azonosító | Guid | ✓ | ✓ |
| Attribútumtípus.Karakterlánc | Sztring | ✓ | ✓ |
| Attribútumtípus.Állapot | Int32 | ✓ | ✓ |
| Attribútumtípus.Állapot | Int32 | ✓ | ✓ |

> [!NOTE]
> Az AttributeType.CalendarRules, az AttributeType.MultiSelectPicklist és az AttributeType.PartyList Dynamics adattípusok nem támogatottak.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a Támogatott adattárak című témakörben [tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)

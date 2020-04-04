---
title: Az Apache Kafka alkalmazás eseményközpontjának használata – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk az Azure Event Hubs Apache Kafka-támogatásáról nyújt tájékoztatást.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 7d3525bc7a25550a79ceeb008e3c69c77a56aa0a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632874"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Azure Event Hubs használata Apache Kafka-alkalmazásokból
Az Event Hubs egy Kafka-végpontot biztosít, amelyet a meglévő Kafka alapú alkalmazások a saját Kafka-fürt futtatása alternatívájaként használhatnak. Az Event Hubs támogatja [az Apache Kafka 1.0-s és újabb protokollt,](https://kafka.apache.org/documentation/)és együttműködik a meglévő Kafka alkalmazásokkal, beleértve a MirrorMakert is.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Mit biztosít a Kafka eseményközpontja?

A Kafka Eseményközpontok szolgáltatás egy protokollfejet biztosít az Azure Event Hubs tetején, amely binárisan kompatibilis a Kafka 1.0-s és újabb verzióival a Kafka-témakörökből való olvasáshoz és íráshoz. Előfordulhat, hogy elkezdi használni a Kafka-végpontot az alkalmazásokkód módosítása nélkül, de minimális konfigurációs változás. A konfigurációkban frissíti a kapcsolati karakterláncot, hogy az eseményközpont által elérhetővé tett Kafka végpontra mutasson, ahelyett, hogy a Kafka-fürtre mutatna. Ezután megkezdheti az események streamelését az alkalmazásokból, amelyek a Kafka protokollt használják az Event Hubs-ba. Ez az integráció támogatja a keretrendszerek, mint [a Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), amely jelenleg előzetes verzióban érhető el. 

Fogalmilag Kafka és Event Hubs közel azonosak: mindkettő particionált naplók streamelési adatokhoz készült. Az alábbi táblázat leképezi a Kafka és az Event Hubs közötti fogalmakat.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka és az Event Hub fogalmi leképezése

| Kafka koncepció | Eseményközpontok – koncepció|
| --- | --- |
| Fürt | Névtér |
| Témakör | Eseményközpont |
| Partíció | Partíció|
| Fogyasztói csoport | Fogyasztói csoport |
| Eltolás | Eltolás|

### <a name="key-differences-between-kafka-and-event-hubs"></a>A Kafka és az Event Hubs közötti legfontosabb különbségek

Bár az [Apache Kafka](https://kafka.apache.org/) szoftver, amelyet bárhol futtathat, az Event Hubs az Azure Blob Storage-hoz hasonló felhőszolgáltatás. Nincsenek kezelhetkiszolgálók vagy hálózatok, és nincsenek konfigurálható brókerek. Hozzon létre egy névteret, amely egy teljes tartománynév, amelyben a témakörök élnek, majd hozzon létre Event Hubs vagy témakörök belül, hogy a névtérben. Az Event Hubs-ról és a névterekről az [Event Hubs szolgáltatásai című](event-hubs-features.md#namespace)témakörben talál további információt. Felhőszolgáltatásként az Event Hubs egyetlen stabil virtuális IP-címet használ végpontként, így az ügyfeleknek nem kell tudniuk a fürtön belüli brókerekről vagy gépekről. 

Az Event Hubs méretezését a megvásárolt átviteli egységek száma szabályozza, és minden átviteli egység másodpercenként 1 MB-ra, vagy 1000 bejövő kapcsolat másodpercenként1000 eseményre jogosít. Alapértelmezés szerint az Event Hubs felskálázi az átviteli egységeket, amikor eléri a korlátot az [Automatikus felfújás](event-hubs-auto-inflate.md) funkcióval; ez a funkció a Kafka eseményközpontok funkciójával is működik. 

### <a name="security-and-authentication"></a>Biztonság és hitelesítés
Minden alkalommal, amikor eseményeket tesz közzé vagy használ fel a Kafka eseményközpontokból, az ügyfél megpróbál hozzáférni az Event Hubs-erőforrásokhoz. Biztosítani szeretné, hogy az erőforrások egy engedélyezett entitás használatával érhetők el. Ha Apache Kafka protokollt használ az ügyfelekkel, beállíthatja a konfigurációt a hitelesítéshez és a titkosításhoz a SASL mechanizmusok használatával. Ha a Kafka Eseményközpontok használata kor a TLS-titkosítást igényli (mivel az Event Hubs-szal való átvitel során minden adat TLS-titkosítással van eltikelve). Meg lehet tenni, amely megadja a SASL_SSL opciót a konfigurációs fájlban. 

Az Azure Event Hubs több lehetőséget biztosít a biztonságos erőforrásokhoz való hozzáférés engedélyezéséhez. 

- Oauth
- Közös hozzáférésű jogosultságkód (SAS)

#### <a name="oauth"></a>Oauth
Az Event Hubs integrálható az Azure Active Directoryval (Azure AD), amely egy **OAuth** 2.0-nak megfelelő központi engedélyezési kiszolgálót biztosít. Az Azure AD használatával szerepköralapú hozzáférés-vezérlés (RBAC) segítségével részletes engedélyeket adhat az ügyfélidentitások. Ezt a funkciót a Kafka-ügyfelekkel használhatja, ha megadja a **protokoll SASL_SSL** és az **OAUTHBEARER-t** a mechanizmushoz. Az RBAC-szerepkörökről és a hozzáférés hatókörének szintjéről a [Hozzáférés engedélyezése az Azure AD-vel](authorize-access-azure-active-directory.md)című témakörben talál.

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Közös hozzáférésű jogosultságkód (SAS)
Az Event Hubs a kafka-erőforrások eseményelosztóihoz való delegált hozzáféréshez is biztosítja a **megosztott hozzáférés-aláírásokat (SAS).** Az OAuth 2.0 token alapú mechanizmussal történő hozzáférés engedélyezése kiváló biztonságot és egyszerű használatot biztosít a SAS-on keresztül. A beépített szerepkörök is szükségtelenné teheti az ACL-alapú engedélyezés, amelyet a felhasználónak kell karbantartania és kezelnie. Ezt a szolgáltatást a Kafka-ügyfelekkel használhatja, ha megadja a **protokoll SASL_SSL** és a **PLAIN-t** a mechanizmushoz. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Példák 
Az **tutorial** eseményközpont létrehozásához és a SAS vagy az OAuth használatával történő eléréséhez részletes útmutatót a [Rövid útmutató: Adatfolyam-továbbítás a Kafka protokollhasználatával című témakörben](event-hubs-quickstart-kafka-enabled-event-hubs.md)talál.

További **minták,** amelyek bemutatják, hogyan kell használni az OAuth a Kafka Eseményközpontok, tekintse meg [a mintákat a GitHubon.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)

## <a name="other-event-hubs-features-available-for-kafka"></a>A Kafka egyéb Eseményközpontok funkciói

A Kafka eseményközpontok funkció lehetővé teszi, hogy írjon egy protokollal, és olvassa el a másikkal, hogy a jelenlegi Kafka-gyártók továbbra is közzé teheti a Kafka-n keresztül, és hozzáadhat olvasókat az Event Hubs, például az Azure Stream Analytics vagy az Azure Functions. Emellett az Event Hubs szolgáltatásai, például [a rögzítés](event-hubs-capture-overview.md) és a [geokatasztrófa-helyreállítás](event-hubs-geo-dr.md) is együttműködnek a Kafka eseményközpontok funkciójával.

## <a name="features-that-are-not-yet-supported"></a>Még nem támogatott szolgáltatások 

Itt van a lista a Kafka funkciók, amelyek még nem támogatott:

*   Idempotens gyártó
*   Tranzakció
*   Tömörítés
*   Méretalapú megőrzés
*   Naplótömörítés
*   Partíciók hozzáadása meglévő témakörhöz
*   HTTP Kafka API-támogatás
*   Kafka patakok

## <a name="next-steps"></a>További lépések
Ez a cikk a Kafka eseményközpontok című bemutatkozását ismerteti. További információ: [Apache Kafka fejlesztői útmutató az Azure Event Hubs-hoz.](apache-kafka-developer-guide.md)



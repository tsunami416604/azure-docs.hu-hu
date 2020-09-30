---
title: Apache Kafka REST-proxy – Azure HDInsight
description: Megtudhatja, hogyan hajthat végre Apache Kafka műveleteket egy Kafka REST-proxy használatával az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: 97bd71dd5a70a867f45915a5a5f95f6513f2a824
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541730"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Az Azure HDInsight Apache Kafka-fürtök használata REST-proxy használatával

A Kafka REST proxy lehetővé teszi, hogy egy REST API HTTP-n keresztül kommunikáljon a Kafka-fürttel. Ez a művelet azt jelenti, hogy a Kafka-ügyfelek a virtuális hálózatán kívül is lehetnek. Az ügyfelek a Kafka-könyvtárakra való támaszkodás helyett egyszerű HTTP-hívásokat végezhetnek a Kafka-fürthöz. Ebből a cikkből megtudhatja, hogyan hozhat létre REST proxyt használó Kafka-fürtöt. Egy mintakód is elérhetővé teszi, amely bemutatja, hogyan hívhatja meg a REST proxyt.

## <a name="rest-api-reference"></a>REST API-referencia

A Kafka REST API által támogatott műveletekhez tekintse meg a [HDInsight KAFKA Rest proxy API-referenciáját](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Háttér

![Kafka REST-proxy kialakítása](./media/rest-proxy/rest-proxy-architecture.png)

Az API által támogatott műveletek teljes leírását lásd: [Apache KAFKA Rest proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>REST-proxy végpontja

A REST-proxyval rendelkező HDInsight Kafka-fürt létrehozása új nyilvános végpontot hoz létre a fürt számára, amely a Azure Portalban található a HDInsight-fürt **tulajdonságai** között.

### <a name="security"></a>Biztonság

A Kafka REST-proxyhoz való hozzáférés Azure Active Directory biztonsági csoportokkal felügyelhető. A Kafka-fürt létrehozásakor adja meg az Azure AD biztonsági csoportot a REST-végponti hozzáféréssel. A REST-proxyhoz hozzáférést igénylő Kafka-ügyfeleket a csoport tulajdonosának kell regisztrálnia ehhez a csoporthoz. A csoport tulajdonosa regisztrálhat a portálon keresztül vagy a PowerShell használatával.

A REST proxy-végponti kérelmek esetében az ügyfélalkalmazások OAuth jogkivonatot kapnak. A jogkivonat a biztonsági csoporttagság ellenőrzéséhez használatos. Keresse meg az alábbi [ügyfélalkalmazás-mintát](#client-application-sample) , amely bemutatja, hogyan szerezhet be egy OAuth tokent. Az ügyfélalkalmazás átadja a OAuth tokent a HTTP-kérelemben a REST-proxynak.

> [!NOTE]
> További információ a HRE biztonsági csoportokról: [alkalmazás-és erőforrás-hozzáférés kezelése Azure Active Directory csoportok használatával](../../active-directory/fundamentals/active-directory-manage-groups.md). A OAuth-tokenek működésével kapcsolatos további információkért lásd: [hozzáférés engedélyezése Azure Active Directory webalkalmazásokhoz a OAuth 2,0 Code Grant flow használatával](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Kafka REST-proxy hálózati biztonsági csoportokkal
Ha saját VNet használ, és hálózati biztonsági csoportokkal vezérli a hálózati forgalmat, a 443-es porton kívül engedélyezze a **bejövő** forgalmat a **9400** -as porton. Ez biztosítja, hogy a Kafka REST-proxykiszolgáló elérhető legyen.

## <a name="prerequisites"></a>Előfeltételek

1. Alkalmazás regisztrálása az Azure AD-ben. A Kafka REST-proxyval való interakcióra írt ügyfélalkalmazások az alkalmazás AZONOSÍTÓját és titkos kulcsát fogják használni az Azure-ban való hitelesítéshez.

1. Hozzon létre biztonsági csoportot az Azure AD-ben. Adja hozzá az Azure AD-ben regisztrált alkalmazást a csoport **tagjaként** a biztonsági csoporthoz. Ezzel a biztonsági csoporttal szabályozhatja, hogy mely alkalmazások használhatják a REST-proxyt. Az Azure AD-csoportok létrehozásával kapcsolatos további információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Ellenőrizze, hogy a csoport **Biztonság**típusú-e.
    ![Biztonsági csoport](./media/rest-proxy/rest-proxy-group.png)

    Annak ellenőrzése, hogy az alkalmazás tagja-e a csoportnak.
    ![Tagság beadása](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Kafka-fürt létrehozása REST proxyval engedélyezve

Az alábbi lépések a Azure Portal használják. Az Azure CLI használatával kapcsolatos példáért lásd: [Apache KAFKA Rest-proxy fürt létrehozása az Azure CLI használatával](tutorial-cli-rest-proxy.md).

1. A Kafka-fürt létrehozási munkafolyamata alatt, a **Biztonság és hálózatkezelés** lapon jelölje be a **Kafka Rest-proxy engedélyezése** lehetőséget.

     ![Képernyőfelvétel: a H D Insight-fürt létrehozása lap a biztonság és a hálózat kiválasztásával.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Kattintson a **biztonsági csoport kiválasztása**elemre. A biztonsági csoportok listájából válassza ki azt a biztonsági csoportot, amelyhez hozzáférést szeretne biztosítani a REST-proxyhoz. A keresőmező segítségével megkeresheti a megfelelő biztonsági csoportot. Kattintson a lap alján található **kiválasztás** gombra.

     ![Képernyőfelvétel: a H D Insight-fürt létrehozása lap, amely a biztonsági csoportok kiválasztásának lehetőségét mutatja be.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Hajtsa végre a további lépéseket a fürt létrehozásához a következő témakörben leírtak szerint: [Apache Kafka-fürt létrehozása az Azure HDInsight Azure Portal használatával](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. A fürt létrehozása után lépjen a fürt tulajdonságaiba, és jegyezze fel a Kafka REST-proxy URL-címét.

     ![REST-proxy URL-címének megtekintése](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Ügyfélalkalmazás mintája

A következő Python-kóddal használhatja a Kafka-fürt REST-proxyját. A kód minta használatához kövesse az alábbi lépéseket:

1. Mentse a mintakód egy olyan gépen, amelyen telepítve van a Python.
1. Telepítse a szükséges Python-függőségeket a végrehajtásával `pip3 install msal` .
1. Módosítsa a kód szakasz **ezeket a tulajdonságokat konfigurálja** , és frissítse a környezet következő tulajdonságait:

    |Tulajdonság |Leírás |
    |---|---|
    |Bérlőazonosító|Az Azure-bérlő, ahol az előfizetése van.|
    |Ügyfél-azonosító|A biztonsági csoportban regisztrált alkalmazás azonosítója.|
    |Titkos ügyfélkulcs|A biztonsági csoportban regisztrált alkalmazás titka.|
    |Kafkarest_endpoint|Szerezze be ezt az értéket a fürt áttekintése **Tulajdonságok** lapján, az [üzembe helyezés részben](#create-a-kafka-cluster-with-rest-proxy-enabled)leírtak szerint. A következő formátumúnak kell lennie: `https://<clustername>-kafkarest.azurehdinsight.net`|

1. A parancssorból hajtsa végre a Python-fájlt a következő végrehajtásával: `sudo python3 <filename.py>`

Ez a kód a következő műveleteket hajtja végre:

1. OAuth-token beolvasása az Azure AD-ből.
1. Bemutatja, hogyan lehet kérést készíteni a Kafka REST proxyra.

A Python OAuth-jogkivonatok beszerzésével kapcsolatos további információkért lásd: [Python AuthenticationContext osztály](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Előfordulhat, `topics` hogy a KAFKA Rest-proxyn keresztül nem létrehozott vagy törölt késések is megjelennek. Ez a késés a gyorsítótár frissítése miatt fordul elő.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_custom_value_json_object():

    custom_value_json_object = {
        "static_value": "welcome to HDI Kafka REST proxy",
        "random_value": get_random_string(),
    }

    return custom_value_json_object


def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"
        },
        {
            "value": "5"
        },
        {
            "partition": 0,
            "value": json.dumps(get_custom_value_json_object())  # need to be a serialized string. For example, "{\"static_value\": \"welcome to HDI Kafka REST proxy\", \"random_value\": \"pAPrgPk\"}"
        },
        {
            "value": json.dumps(get_custom_value_json_object())  # need to be a serialized string. For example, "{\"static_value\": \"welcome to HDI Kafka REST proxy\", \"random_value\": \"pAPrgPk\"}"
        }
    ]
}

print("Producing 4 messages in a request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
```

Az alábbi minta alapján megtalálhatja, hogyan szerezhet be tokent az Azure for REST proxyhoz egy curl-parancs használatával. **Figyelje meg, hogy a `scope=https://hib.azurehdinsight.net/.default` token beszerzése során meg kell adni a megadott értéket.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>További lépések

* [A Kafka REST proxy API-dokumentációja](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)

---
title: Apache Kafka REST-proxy – Azure HDInsight
description: Megtudhatja, hogyan hajthat végre Apache Kafka műveleteket egy Kafka REST-proxy használatával az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b53fc3af71ce872c9ca9f513139c8179fd4165ed
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031395"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Az Azure HDInsight Apache Kafka-fürtök használata REST-proxy használatával

A Kafka REST proxy lehetővé teszi, hogy egy REST API HTTP-n keresztül kommunikáljon a Kafka-fürttel. Ez azt jelenti, hogy a Kafka-ügyfelek a virtuális hálózaton kívül is lehetnek. Emellett az ügyfelek létrehozhatnak egyszerű HTTP-hívásokat a Kafka-fürtre irányuló üzenetek küldéséhez és fogadásához ahelyett, hogy a Kafka-könyvtárakra támaszkodnak.  

## <a name="background"></a>Háttér

### <a name="architecture"></a>Architektúra

REST proxy nélkül a Kafka-ügyfeleknek ugyanabban a VNet kell lenniük, mint a Kafka-fürtnek vagy egy egyenrangú VNet. A REST-proxy lehetővé teszi, hogy bárhonnan összekapcsolja az adatelőállítókat vagy a fogyasztókat. A REST-proxy üzembe helyezése új nyilvános végpontot hoz létre a fürthöz, amelyet a portál beállításaiban talál.

Az API által támogatott összes művelet részletes leírását lásd: [Apache KAFKA Rest proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="security"></a>Biztonság

A Kafka REST-proxyhoz való hozzáférés Azure Active Directory biztonsági csoportokkal felügyelhető. További információ: [alkalmazás-és erőforrás-hozzáférés kezelése Azure Active Directory csoportok használatával](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).

Ha a Kafka-fürtöt a REST-proxyn keresztül hozza létre, akkor meg kell adnia azt a HRE biztonsági csoportot, amelynek hozzáféréssel kell rendelkeznie a REST-végponthoz. A REST-proxyhoz hozzáférést igénylő Kafka-ügyfeleket (alkalmazásokat) a csoport tulajdonosának kell regisztrálnia ehhez a csoporthoz. A csoport tulajdonosa ezt a portálon vagy a Powershellen keresztül teheti meg.

A REST proxy-végpontra irányuló kérések megkezdése előtt az ügyfélalkalmazás OAuth tokent kap a megfelelő biztonsági csoport tagságának ellenőrzéséhez. A OAuth-tokenek működésével kapcsolatos további információkért lásd: [hozzáférés engedélyezése Azure Active Directory webalkalmazásokhoz a OAuth 2,0 Code Grant flow használatával](../../active-directory/develop/v1-protocols-oauth-code.md). Egy OAuth-token Pythonban való beolvasására példa: [ügyfélalkalmazás minta](#client-application-sample)

Ha az ügyfélalkalmazás rendelkezik a OAuth-jogkivonattal, át kell adni ezt a tokent a REST-proxynak nyújtott HTTP-kérelemben.

## <a name="prerequisites"></a>Előfeltételek

1. Alkalmazás regisztrálása az Azure AD-ben. A Kafka REST-proxyval való interakcióra írt ügyfélalkalmazások az alkalmazás AZONOSÍTÓját és titkos kulcsát fogják használni az Azure-ban való hitelesítéshez.
1. Hozzon létre egy Azure AD biztonsági csoportot, és adja hozzá az Azure AD-ben regisztrált alkalmazást a biztonsági csoporthoz. Ezzel a biztonsági csoporttal szabályozhatja, hogy mely alkalmazások használhatják a REST-proxyt. Az Azure AD-csoportok létrehozásával kapcsolatos további információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Kafka-fürt létrehozása REST proxyval engedélyezve

1. A Kafka-fürt létrehozási munkafolyamata során a "biztonság és hálózatkezelés" lapon jelölje be a "Kafka REST-proxy engedélyezése" beállítást.

     ![A Kafka REST-proxy engedélyezése és a biztonsági csoport kiválasztása](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Kattintson a **biztonsági csoport kiválasztása**elemre. A biztonsági csoportok listájából válassza ki azt a biztonsági csoportot, amelyhez hozzáférést szeretne biztosítani a REST-proxyhoz. A keresőmező segítségével megkeresheti a megfelelő biztonsági csoportot. Kattintson a lap alján található **kiválasztás** gombra.

     ![A Kafka REST-proxy engedélyezése és a biztonsági csoport kiválasztása](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Hajtsa végre a további lépéseket a fürt létrehozásához a következő témakörben leírtak szerint: [Apache Kafka-fürt létrehozása az Azure HDInsight Azure Portal használatával](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. A fürt létrehozása után lépjen a fürt tulajdonságaiba, és jegyezze fel a Kafka REST-proxy URL-címét.

     ![REST-proxy URL-címének megtekintése](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Ügyfélalkalmazás mintája

A következő Python-kóddal használhatja a Kafka-fürt REST-proxyját. A kód a következő műveleteket végzi el:

1. OAuth-jogkivonat beolvasása az Azure AD-ből
1. Létrehozza a megadott témakört.
1. Üzeneteket küld az adott témakörnek
1. Az adott témakör üzeneteinek felhasználása

A Python OAuth-jogkivonatok beszerzésével kapcsolatos további információkért lásd: [Python AuthenticationContext osztály](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Előfordulhat, hogy a Kafka REST-proxyn keresztül nem létrehozott vagy törölt témakörök nem jelennek meg. Ezt a késleltetést a gyorsítótár frissítése okozza.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

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

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

A kód minta használatához kövesse az alábbi lépéseket:

1. Mentse a mintakód egy olyan gépen, amelyen telepítve van a Python.
1. Telepítse a szükséges Python-függőségeket `pip3 install adal` és `pip install msrestazure`végrehajtásával.
1. Módosítsa a kódot, és frissítse a környezet következő tulajdonságait:
    1.  *Bérlő azonosítója* – az Azure-bérlő, ahol az előfizetése van.
    1.  *Ügyfél-azonosító* – a biztonsági csoportban regisztrált alkalmazás azonosítója.
    1.  *Ügyfél titkos kulcsa* – a biztonsági csoportban regisztrált alkalmazás titka
    1.  *Kafkarest_endpoint* – szerezze be ezt az értéket a fürt áttekintése "tulajdonságok" lapján, a [telepítés szakaszban](#create-a-kafka-cluster-with-rest-proxy-enabled)leírtak szerint. A következő formátumúnak kell lennie – `https://<clustername>-kafkarest.azurehdinsight.net`
3. A parancssorból hajtsa végre a Python-fájlt `python <filename.py>` végrehajtásával

## <a name="next-steps"></a>Következő lépések

* [A Kafka REST proxy API-dokumentációja](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Oktatóanyag: a Apache Kafka producer és a fogyasztói API-k használata](apache-kafka-producer-consumer-api.md)
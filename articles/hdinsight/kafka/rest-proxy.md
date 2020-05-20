---
title: Apache Kafka REST-proxy – Azure HDInsight
description: Megtudhatja, hogyan hajthat végre Apache Kafka műveleteket egy Kafka REST-proxy használatával az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: has-adal-ref
ms.date: 04/03/2020
ms.openlocfilehash: 9b5771197c3e2de109af1a3b3475ab28fcbd6453
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647762"
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

## <a name="prerequisites"></a>Előfeltételek

1. Alkalmazás regisztrálása az Azure AD-ben. A Kafka REST-proxyval való interakcióra írt ügyfélalkalmazások az alkalmazás AZONOSÍTÓját és titkos kulcsát fogják használni az Azure-ban való hitelesítéshez.

1. Hozzon létre egy Azure AD biztonsági csoportot. Adja hozzá az Azure AD-ben regisztrált alkalmazást a csoport **tagjaként** a biztonsági csoporthoz. Ezzel a biztonsági csoporttal szabályozhatja, hogy mely alkalmazások használhatják a REST-proxyt. Az Azure AD-csoportok létrehozásával kapcsolatos további információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Ellenőrizze, hogy a csoport **Biztonság**típusú-e.
    ![Biztonsági csoport](./media/rest-proxy/rest-proxy-group.png)

    Annak ellenőrzése, hogy az alkalmazás tagja-e a csoportnak.
    ![Tagság beadása](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Kafka-fürt létrehozása REST proxyval engedélyezve

Az alábbi lépések a Azure Portal használják. Az Azure CLI használatával kapcsolatos példáért lásd: [Apache KAFKA Rest-proxy fürt létrehozása az Azure CLI használatával](tutorial-cli-rest-proxy.md).

1. A Kafka-fürt létrehozási munkafolyamata alatt, a **Biztonság és hálózatkezelés** lapon jelölje be a **Kafka Rest-proxy engedélyezése** lehetőséget.

     ![A Kafka REST-proxy engedélyezése és a biztonsági csoport kiválasztása](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Kattintson a **biztonsági csoport kiválasztása**elemre. A biztonsági csoportok listájából válassza ki azt a biztonsági csoportot, amelyhez hozzáférést szeretne biztosítani a REST-proxyhoz. A keresőmező segítségével megkeresheti a megfelelő biztonsági csoportot. Kattintson a lap alján található **kiválasztás** gombra.

     ![A Kafka REST-proxy engedélyezése és a biztonsági csoport kiválasztása](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Hajtsa végre a további lépéseket a fürt létrehozásához a következő témakörben leírtak szerint: [Apache Kafka-fürt létrehozása az Azure HDInsight Azure Portal használatával](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. A fürt létrehozása után lépjen a fürt tulajdonságaiba, és jegyezze fel a Kafka REST-proxy URL-címét.

     ![REST-proxy URL-címének megtekintése](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Ügyfélalkalmazás mintája

A következő Python-kóddal használhatja a Kafka-fürt REST-proxyját. A kód minta használatához kövesse az alábbi lépéseket:

1. Mentse a mintakód egy olyan gépen, amelyen telepítve van a Python.
1. Telepítse a szükséges Python-függőségeket a végrehajtásával `pip3 install msal` .
1. Módosítsa a kód szakasz **ezeket a tulajdonságokat konfigurálja** , és frissítse a környezet következő tulajdonságait:

    |Tulajdonság |Description |
    |---|---|
    |Bérlőazonosító|Az Azure-bérlő, ahol az előfizetése van.|
    |Ügyfél-azonosító|A biztonsági csoportban regisztrált alkalmazás azonosítója.|
    |Titkos ügyfélkulcs|A biztonsági csoportban regisztrált alkalmazás titka.|
    |Kafkarest_endpoint|Szerezze be ezt az értéket a fürt áttekintése **Tulajdonságok** lapján, az [üzembe helyezés részben](#create-a-kafka-cluster-with-rest-proxy-enabled)leírtak szerint. A következő formátumúnak kell lennie:`https://<clustername>-kafkarest.azurehdinsight.net`|

1. A parancssorból hajtsa végre a Python-fájlt a következő végrehajtásával:`sudo python3 <filename.py>`

Ez a kód a következő műveleteket hajtja végre:

1. OAuth-token beolvasása az Azure AD-ből.
1. Bemutatja, hogyan lehet kérést készíteni a Kafka REST proxyra.

A Python OAuth-jogkivonatok beszerzésével kapcsolatos további információkért lásd: [Python AuthenticationContext osztály](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Előfordulhat, `topics` hogy a KAFKA Rest-proxyn keresztül nem létrehozott vagy törölt késések is megjelennek. Ez a késés a gyorsítótár frissítése miatt fordul elő.

```python
#Required python packages
#pip3 install msal

import msal

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

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Az alábbi minta alapján megtalálhatja, hogyan szerezhet be tokent az Azure for REST proxyhoz egy curl-parancs használatával. **Figyelje meg, hogy a `scope=https://hib.azurehdinsight.net/.default` token beszerzése során meg kell adni a megadott értéket.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>További lépések

* [A Kafka REST proxy API-dokumentációja](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)

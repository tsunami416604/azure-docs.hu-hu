---
title: Apache Kafka REST-proxy – Azure HDInsight
description: Ismerje meg, hogyan hajthat végre Apache Kafka-műveleteket egy Kafka REST-proxy használatával az Azure HDInsight ban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8997b385960c58b17747dfcfced74010af80550b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548221"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Apache Kafka-fürtök használata az Azure HDInsightban REST-proxy használatával

A Kafka REST-proxy lehetővé teszi, hogy a Kafka-fürtöt HTTP-n keresztül i. Ez azt jelenti, hogy a Kafka-ügyfelek a virtuális hálózaton kívül is lehetnek. Emellett az ügyfelek egyszerű HTTP-hívásokat is kezdeményezhetnek a Kafka-fürtbe küldött és fogadott üzenetek küldéséhez és fogadásához, ahelyett, hogy a Kafka-kódtárakra támaszkodnának. Ez az oktatóanyag bemutatja, hogyan hozhat létre REST-proxykompatibilis Kafka-fürtöt, és milyen mintakódot biztosít, amely bemutatja, hogyan kezdeményezheti a hívásokat a REST-proxyhoz.

## <a name="rest-api-reference"></a>REST API-referencia

A Kafka REST API által támogatott műveletek teljes specifikációját lásd: [HDInsight Kafka REST Proxy API-referencia.](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)

## <a name="background"></a>Háttér

![Kafka REST proxy architektúra](./media/rest-proxy/rest-proxy-architecture.png)

Az API által támogatott műveletek teljes specifikációját az [Apache Kafka REST Proxy API című témakörben tetszésszerint.](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)

### <a name="rest-proxy-endpoint"></a>REST-proxy végpont

HdInsight Kafka-fürt REST-proxyval való létrehozása létrehoz egy új nyilvános végpontot a fürthöz, amely megtalálható az Azure Portalon található "Properties" HDInsight-fürtben.

### <a name="security"></a>Biztonság

A Kafka REST-proxyhoz való hozzáférést az Azure Active Directory biztonsági csoportjai kezelik. A Kafka-fürt létrehozásakor a REST-proxy engedélyezve van, biztosítja az Azure Active Directory biztonsági csoport, amely hozzáférést kell biztosítania a REST-végponthoz. A Kafka-ügyfelek (alkalmazások), amelyek hozzáférést igényelnek a REST-proxy regisztrálnia kell ezt a csoportot a csoport tulajdonosa. A csoport tulajdonosa ezt a portálon keresztül vagy a PowerShellen keresztül teheti meg.

A REST-proxy végpontra irányuló kérelmek kérése előtt az ügyfélalkalmazásnak oauth-jogkivonatot kell kapnia a megfelelő biztonsági csoport tagságának ellenőrzéséhez. Keresse meg [az ügyfélalkalmazás-minta](#client-application-sample) az alábbiakban, amely bemutatja, hogyan juthat be egy OAuth token. Miután az ügyfélalkalmazás rendelkezik az OAuth-jogkivonattal, át kell adniuk a jogkivonatot a REST-proxynak küldött HTTP-kérelemben.

> [!NOTE]  
> Az AAD biztonsági csoportokról az [Alkalmazás- és erőforrás-hozzáférés kezelése az Azure Active Directory-csoportok használatával.](../../active-directory/fundamentals/active-directory-manage-groups.md) Az OAuth-jogkivonatok működéséről az [Azure Active Directory webalkalmazásokhoz való hozzáférés engedélyezése az OAuth 2.0-s kódtámogatási folyamat használatával című témakörben](../../active-directory/develop/v1-protocols-oauth-code.md)talál további információt.

## <a name="prerequisites"></a>Előfeltételek

1. Alkalmazás regisztrálása az Azure AD-ben. A Kafka REST-proxyval való interakcióhoz írt ügyfélalkalmazások az alkalmazás azonosítóját és titkos ját használják az Azure-hitelesítéshez.

1. Hozzon létre egy Azure AD biztonsági csoportot, és adja hozzá az Azure AD-vel regisztrált alkalmazást a biztonsági csoporthoz a csoport "tagjaként". Ez a biztonsági csoport szabályozza, hogy mely alkalmazások kommunikálhatnak a REST-proxyval. Az Azure AD-csoportok létrehozásáról további információt az [Alapszintű csoport létrehozása és tagok hozzáadása az Azure Active Directory használatával című témakörben talál.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

    A csoport ellenőrzése "Biztonsági" ![biztonsági csoport típusú](./media/rest-proxy/rest-proxy-group.png)

    Annak ellenőrzése, hogy ![az alkalmazás tagja-e a Csoportérvényesítési tagságnak](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Kafka-fürt létrehozása REST-proxyval

1. A Kafka fürtlétrehozási munkafolyamat során a "Biztonság + hálózat" lapon ellenőrizze a "Kafka REST proxy engedélyezése" lehetőséget.

     ![Kafka REST-proxy engedélyezése és biztonsági csoport kiválasztása](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Kattintson **a Biztonsági csoport kiválasztása gombra.** A biztonsági csoportok listájából válassza ki azt a biztonsági csoportot, amelyhez hozzá szeretne férni a REST-proxyhoz. A keresőmező segítségével megkeresheti a megfelelő biztonsági csoportot. Kattintson az alsó **Kijelölés** gombra.

     ![Kafka REST-proxy engedélyezése és biztonsági csoport kiválasztása](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Hajtsa végre a fürt létrehozásának további lépéseit az Azure HDInsight ban az [Apache Kafka-fürt létrehozása az Azure Portalhasználatával című részen leírtak szerint.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)

1. A fürt létrehozása után lépjen a fürt tulajdonságaira a Kafka REST-proxy URL-címének rögzítéséhez.

     ![REST-proxy URL-címének megtekintése](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Ügyfélalkalmazás-minta

Az alábbi python-kód segítségével a Kafka-fürt REST-proxyjával kommunikálhat. A kódminta használatához kövesse az alábbi lépéseket:

1. Mentse a mintakódot egy olyan számítógépen, amelyen telepítve van a Python.
1. Telepítse a szükséges python-függőségeket a `pip3 install adal` végrehajtásával és `pip install msrestazure`a.
1. A kódszakasz *módosítása: Konfigurálja ezeket a tulajdonságokat,* és frissítse a következő tulajdonságokat a környezetben:
    1.    *Bérlői azonosító* – Az Azure-bérlő, ahol az előfizetés.
    1.    *Ügyfélazonosító* – a biztonsági csoportban regisztrált alkalmazás azonosítója.
    1.    *Ügyféltitok* – A biztonsági csoportban regisztrált alkalmazás titkost asztatitkára
    1.    *Kafkarest_endpoint* – ezt az értéket a fürt "Tulajdonságok" lapján, a [központi telepítés szakaszban](#create-a-kafka-cluster-with-rest-proxy-enabled)leírtak szerint kapja meg. Meg kell a következő formátumban -`https://<clustername>-kafkarest.azurehdinsight.net`
1. A parancssorból hajtsa végre a python fájlt a`python <filename.py>`

Ez a kód a következőket teszi:

1. OAuth-jogkivonat beolvasása az Azure AD-ből
1. A Kafka REST-proxy kérésének megjelenítése

Az OAuth-tokenek pythonban való beszerzéséről a [Python AuthenticationContext osztályban](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)talál további információt. Előfordulhat, hogy a késés t, míg a kafka REST-proxyn keresztül nem létrehozott vagy törölt témakörök jelennek meg. Ez a késleltetés a gyorsítótár frissítésének köszönhető.

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

Az alábbiakban egy másik minta, hogyan kaphat le egy jogkivonatot az Azure for REST proxy egy curl parancs használatával. Figyelje meg, `resource=https://hib.azurehdinsight.net` hogy szükségünk van a megadott, míg egy token.

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&resource=https://hib.azurehdinsight.net' 'https://login.microsoftonline.com/<tenantid>/oauth2/token'
```

## <a name="next-steps"></a>További lépések

* [Kafka REST proxy API referenciadokumentumok](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)

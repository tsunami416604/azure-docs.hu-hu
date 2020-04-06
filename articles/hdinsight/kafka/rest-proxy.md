---
title: Apache Kafka REST-proxy – Azure HDInsight
description: Ismerje meg, hogyan csinálhat Apache Kafka-műveleteket egy Kafka REST-proxy használatával az Azure HDInsight ban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 6bf34f8fb15bf8fddb1ba398ed678d5c98b8c84f
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667788"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Apache Kafka-fürtök használata az Azure HDInsightban REST-proxy használatával

A Kafka REST-proxy lehetővé teszi, hogy a Kafka-fürtöt HTTP-n keresztül i. Ez a művelet azt jelenti, hogy a Kafka-ügyfelek a virtuális hálózaton kívül is lehetnek. Az ügyfelek egyszerű HTTP-hívásokat kezdeményezhetnek a Kafka-fürthöz, ahelyett, hogy a Kafka-kódtárakra támaszkodnának. Ez a cikk bemutatja, hogyan hozhat létre rest-proxy kompatibilis Kafka-fürt. Egy mintakódot is biztosít, amely bemutatja, hogyan lehet hívásokat kezdeményezni a REST-proxyhoz.

## <a name="rest-api-reference"></a>REST API-referencia

A Kafka REST API által támogatott műveletekről a [HDInsight Kafka REST-proxy API-referencia című témaköre látható.](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)

## <a name="background"></a>Háttér

![Kafka REST proxy design](./media/rest-proxy/rest-proxy-architecture.png)

Az API által támogatott műveletek teljes specifikációját az [Apache Kafka REST Proxy API című témakörben tetszésszerint.](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)

### <a name="rest-proxy-endpoint"></a>REST-proxy végpont

HdInsight Kafka-fürt rest-proxyval való létrehozása létrehoz egy új nyilvános végpontot a fürthöz, amely megtalálható az Azure Portalon található HDInsight-fürt **tulajdonságai** ban.

### <a name="security"></a>Biztonság

A Kafka REST-proxyhoz való hozzáférést az Azure Active Directory biztonsági csoportjai kezelik. A Kafka-fürt létrehozásakor adja meg az Azure AD biztonsági csoport REST-végpont-hozzáféréssel. A Kafka-ügyfelek, amelyek hozzáférést igényelnek a REST proxy regisztrálnia kell a csoport a csoport tulajdonosa. A csoport tulajdonosa regisztrálhat a portálon keresztül vagy a PowerShellen keresztül.

REST-proxyvégpont-kérelmek esetén az ügyfélalkalmazásoknak OAuth-jogkivonatot kell beszerezniük. A jogkivonat a biztonsági csoporttagság ellenőrzésére szolgál. Keresse meg [az ügyfélalkalmazás-minta](#client-application-sample) az alábbiakban, amely bemutatja, hogyan juthat be egy OAuth token. Az ügyfélalkalmazás átadja az OAuth-jogkivonatot a HTTP-kérelemben a REST-proxynak.

> [!NOTE]  
> Az AAD biztonsági csoportokról az [Alkalmazás- és erőforrás-hozzáférés kezelése az Azure Active Directory-csoportok használatával.](../../active-directory/fundamentals/active-directory-manage-groups.md) Az OAuth-jogkivonatok működéséről az [Azure Active Directory webalkalmazásokhoz való hozzáférés engedélyezése az OAuth 2.0-s kódtámogatási folyamat használatával című témakörben](../../active-directory/develop/v1-protocols-oauth-code.md)talál további információt.

## <a name="prerequisites"></a>Előfeltételek

1. Alkalmazás regisztrálása az Azure AD-ben. A Kafka REST-proxyval való interakcióhoz írt ügyfélalkalmazások az alkalmazás azonosítóját és titkos ját használják az Azure-hitelesítéshez.

1. Hozzon létre egy Azure AD biztonsági csoportot. Adja hozzá az Azure AD-vel regisztrált alkalmazást a biztonsági csoporthoz a csoport **tagjaként.** Ez a biztonsági csoport szabályozza, hogy mely alkalmazások kommunikálhatnak a REST-proxyval. Az Azure AD-csoportok létrehozásáról további információt az [Alapszintű csoport létrehozása és tagok hozzáadása az Azure Active Directory használatával című témakörben talál.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

    A csoport ellenőrzése **Biztonság**típusú.
    ![Biztonsági csoport](./media/rest-proxy/rest-proxy-group.png)

    Annak ellenőrzése, hogy az alkalmazás a Csoport tagja.Validate that application is member of Group.
    ![Tagság ellenőrzése](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Kafka-fürt létrehozása REST-proxyval

1. A Kafka fürtlétrehozási munkafolyamat során a **Biztonság + hálózat** lapon jelölje be a **Kafka REST proxy engedélyezése** jelölőnégyzetet.

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
1. A kódszakasz **módosítása: Konfigurálja ezeket a tulajdonságokat,** és frissítse a következő tulajdonságokat a környezetben:

    |Tulajdonság |Leírás |
    |---|---|
    |Bérlőazonosító|Az Azure-bérlő, ahol az előfizetés.|
    |Ügyfél-azonosító|A biztonsági csoportban regisztrált alkalmazás azonosítója.|
    |Titkos ügyfélkulcs|A biztonsági csoportban regisztrált alkalmazás titkost.|
    |Kafkarest_endpoint|Ezt az értéket a fürt **tulajdonságai** lapján, a [központi telepítés szakaszban](#create-a-kafka-cluster-with-rest-proxy-enabled)leírtak szerint kaphatja meg. Meg kell a következő formátumban -`https://<clustername>-kafkarest.azurehdinsight.net`|

1. A parancssorból hajtsa végre a python fájlt a`python <filename.py>`

Ez a kód a következő műveletet teszi:

1. OAuth-jogkivonat ot az Azure AD-ből lehív.
1. Bemutatja, hogyan lehet kérelmet kérni a Kafka REST proxy.

Az OAuth-tokenek pythonban való beszerzéséről a [Python AuthenticationContext osztályban](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)talál további információt. Előfordulhat, hogy a `topics` késés, amíg a kafka REST proxy nem jön létre, vagy törölt jelennek meg. Ez a késleltetés a gyorsítótár frissítése miatt van.

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

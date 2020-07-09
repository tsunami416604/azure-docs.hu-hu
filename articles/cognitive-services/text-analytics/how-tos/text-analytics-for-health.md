---
title: Az Text Analytics használata az állapothoz
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan kinyerheti és címkézheti az orvosi adatokat strukturálatlan klinikai szövegből Text Analytics for Health használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: cbc950647dc2f7102f20766c22f434526ab66639
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122316"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Útmutató: a Text Analytics for Health használata (előzetes verzió)

> [!IMPORTANT] 
> A Text Analytics for Health egy előzetes verzió, amely az "adott állapotban" és "minden HIBÁval" érhető el. Ennek megfelelően a **text Analytics for Health (előzetes verzió) nem valósítható meg, és nem helyezhető üzembe semmilyen éles használatban.** Az egészségügyi Text Analytics nem az orvosi eszközként, klinikai támogatásként, diagnosztikai eszközként vagy más olyan technológiával való használatra készültek, amelyet a diagnosztika, a gyógyítás, a mérséklés, a kezelés vagy más feltételek megelőzéséhez, illetve más feltételekhez való felhasználáshoz kíván használni, és a Microsoft semmilyen jogot nem biztosít erre a képességre ilyen célra. Ezt a képességet nem úgy tervezték, hogy az orvosi szakorvosok vagy egészségügyi szakvélemények, diagnózis, kezelés vagy az egészségügyi szakemberek klinikai ítélete helyett ne legyenek implementálva, és ne legyenek használhatók. Az ügyfél kizárólag a Text Analytics for Health szolgáltatásért felelős. A Microsoft nem garantálja, hogy az állapotra vagy a képességgel kapcsolatban biztosított bármely anyagra vonatkozó Text Analytics bármely egészségügyi célra elegendő, vagy bármely személy egészségügyi vagy orvosi követelményeinek kielégítése érdekében. 


Az Azure Text Analytics for Health egy olyan tárolós szolgáltatás, amely a strukturálatlan szövegektől, például az orvos megjegyzései, a mentesítési összefoglalók, a klinikai dokumentumok és az elektronikus egészségügyi jelentések alapján kinyeri és címkézi a kapcsolódó egészségügyi adatokat.  

## <a name="features"></a>Szolgáltatások

Az állapot-tároló Text Analyticsa jelenleg elnevezett entitások felismerése, kapcsolat kibontása, entitások kivonása és az angol nyelvű szöveghez kapcsolódó entitások összekapcsolása a saját fejlesztési környezetében, amely megfelel az adott biztonsági és adatirányítási követelményeknek.

#### <a name="named-entity-recognition"></a>[Megnevezett entitások felismerése](#tab/ner)

A nevesített entitások felismerése észleli a strukturálatlan szövegben említett szavakat és kifejezéseket, amelyek egy vagy több szemantikai típushoz társíthatók, például a diagnosztika, a gyógyszer neve, a tünet/aláírás vagy az életkor.

> [!div class="mx-imgBorder"]
> ![Állapot](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Kapcsolatok kibontása](#tab/relation-extraction)

A kapcsolat kibontása azonosítja a szövegben említett fogalmak közötti értelmes kapcsolatokat. A "feltétel időpontja" típusú kapcsolatok például a feltétel nevének egy időponthoz társításával találhatók meg. 

> [!div class="mx-imgBorder"]
> ![Állapot újraindítása](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Entitáskapcsolás](#tab/entity-linking)

Az entitások a disambiguates különböző entitásokat kapcsolnak össze úgy, hogy a szövegben megnevezett entitásokat társítanak a fogalmak előre definiált adatbázisában található fogalmakhoz. Például az egységes orvosi nyelvi rendszer (UMLS).

> [!div class="mx-imgBorder"]
> ![Állapot EL](../media/ta-for-health/health-entity-linking.png)

A Text Analytics for Health támogatja az egyesített orvosi nyelvi rendszer ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) Metathesaurus tudásbázisban található állapot-és orvosbiológiai szótárakhoz összekapcsolását.

#### <a name="negation-detection"></a>[Tagadás észlelése](#tab/negation-detection) 

Az orvosi tartalom értelmét nagy hatással van a módosítók, például a tagadás, ami kritikus következményekkel járhat, ha nem diagnosztizálják. A Text Analytics for Health támogatja a szövegben említett különböző entitások tagadásának észlelését. 

> [!div class="mx-imgBorder"]
> ![Állapot negatív](../media/ta-for-health/health-negation.png)

---

A támogatott entitások teljes listájáért tekintse meg az Text Analytics által visszaadott [entitások kategóriáit](../named-entity-types.md?tabs=health) .

## <a name="supported-languages"></a>Támogatott nyelvek

A Text Analytics for Health csak az angol nyelvű dokumentumokat támogatja.

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Töltse ki és küldje el a [Cognitive Services tárolók kérelem űrlapját](https://aka.ms/cognitivegate) , hogy hozzáférést Kérjen a tárolóhoz. Jelenleg nem számítunk fel díjat a Text Analytics az állapotadatok használatáért. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>A tároló telepítése

Több módon is telepítheti és futtathatja a tárolót. 

1. A [Azure Portal](text-analytics-how-to-install-containers.md?tabs=healthcare) használatával hozzon létre egy Text Analytics-erőforrást, és a Docker használatával szerezze be a tárolót.
2. Az alábbi PowerShell-és [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) -parancsfájlok segítségével automatizálhatja az erőforrás-telepítési tárolók konfigurációját.

### <a name="install-the-container-using-azure-web-app-for-containers"></a>A tároló telepítése az Azure Web App for Containers használatával

Az Azure [Web App for containers](https://azure.microsoft.com/services/app-service/containers/) egy Azure-erőforrás, amely a Felhőbeli tárolók futtatására szolgál. Olyan beépített funkciókat kínál, mint például az automatikus skálázás, a Docker-tárolók támogatása és a Docker-összeállítás, a HTTPS-támogatás és még sok más.

> [!NOTE]
> Az Azure Web App használatával automatikusan egy tartományt fog kapni`<appservice_name>.azurewebsites.net`

Futtassa ezt a PowerShell-szkriptet az Azure CLI használatával egy Web App for Containers létrehozásához, az előfizetése és a tároló-rendszerkép használatával a HTTPS protokollon keresztül. Várjon, amíg a parancsfájl befejeződik (körülbelül 20 perc) az első kérelem elküldése előtt.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>A tároló telepítése az Azure Container instance használatával

Az üzembe helyezést az Azure Container instance (ACI) használatával is egyszerűbbé teheti. Az ACI egy olyan erőforrás, amely lehetővé teszi a Docker-tárolók igény szerinti futtatását egy felügyelt, kiszolgáló nélküli Azure-környezetben. 

Lásd: a [Azure Container instances használata](text-analytics-how-to-use-container-instances.md) az ACI-erőforrások Azure Portal használatával történő üzembe helyezésének lépéseihez. Az Azure CLI-vel az alábbi PowerShell-szkriptet is használhatja, amely egy ACI-t hoz létre az előfizetésében a tároló képe alapján.  Várjon, amíg a parancsfájl befejeződik (körülbelül 20 perc) az első kérelem elküldése előtt.

> [!NOTE] 
> Azure Container Instances nem tartalmaz HTTPS-támogatást a beépített tartományokhoz. Ha HTTPS-re van szüksége, manuálisan kell konfigurálnia, beleértve a tanúsítvány létrehozását és a tartomány regisztrálását. Ezt az alábbi, NGINX-mel kapcsolatos utasításokat követve teheti meg.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 5 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Biztonságos ACI-kapcsolat

Alapértelmezés szerint az ACI és a Container API használatával nem biztosítunk biztonságot. Ennek az az oka, hogy általában a tárolók egy olyan Pod részeként futnak, amely egy hálózati híddal kívülről van védve. Az előtérben lévő összetevővel azonban módosíthat egy tárolót, így a tároló végpontja magánjellegű marad. Az alábbi példák az [NGINX](https://www.nginx.com) -et bejövő átjáróként használják a https/SSL és az ügyféltanúsítvány-alapú hitelesítés támogatásához.

> [!NOTE]
> Az NGINX egy nyílt forráskódú, nagy teljesítményű HTTP-kiszolgáló és-proxy. Az NGINX-tárolók a TLS-kapcsolatok egyetlen tárolóhoz való leállítására használhatók. Összetettebb, az NGINX-beli bejövő forgalomra épülő TLS-megszakítási megoldások is lehetségesek.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>NGINX beállítása bejövő átjáróként

A NGINX [konfigurációs fájlokat](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) használ a funkciók futásidőben történő engedélyezéséhez. Egy másik szolgáltatás TLS-megszakításának engedélyezéséhez meg kell adnia egy SSL-tanúsítványt a TLS-kapcsolat megszakításához és a `proxy_pass` szolgáltatáshoz tartozó címek megadásához. Alább látható egy minta.


> [!NOTE]
> `ssl_certificate`az NGINX-tároló helyi fájlrendszerében megadható elérési utat vár. A számára megadott címnek `proxy_pass` elérhetőnek kell lennie az NGINX-tároló hálózatán belül.

Az NGINX-tároló betölti az összes olyan fájlt, `_.conf_` amely a http- `/etc/nginx/conf.d/` konfigurációs elérési úton van csatlakoztatva.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Példa Docker-összeállítás fájlra

Az alábbi példa bemutatja, hogyan hozható létre [Docker-összeállítási](https://docs.docker.com/compose/reference/overview) fájl az NGINX és a Text Analytics for Health containers telepítéséhez:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

A Docker-összeállítási fájl elindításához futtassa a következő parancsot egy konzolon a fájl legfelső szintjén:

```bash
docker-compose up
```

További információ: NGINX dokumentációja az [NGINX SSL-lezárásáról](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).


## <a name="example-api-request"></a>Példa API-kérelemre
A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít.

Az alábbi példában szereplő cURL-kérelem használatával küldjön le egy lekérdezést arra a tárolóra, amelyet telepített, majd a `serverURL` megfelelő értékkel cserélje le a változót.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.0-preview.1/domains/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

A következő JSON egy példa egy JSON-fájlra, amely a Text Analytics for Health API-kérelem POST törzséhez van csatolva:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>API-válasz törzse

A következő JSON példa az állapot API-válasz törzsének Text Analytics:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "type": "SYMPTOM_OR_SIGN",
                    "score": 0.97,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "type": "DOSAGE",
                    "score": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "type": "MEDICATION_NAME",
                    "score": 0.99,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "type": "FREQUENCY",
                    "score": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DOSAGE_OF_MEDICATION",
                    "score": 1.0,
                    "entities": [
                        {
                            "id": "0",
                            "role": "ATTRIBUTE"
                        },
                        {
                            "id": "1",
                            "role": "ENTITY"
                        }
                    ]
                },
                {
                    "relationType": "FREQUENCY_OF_MEDICATION",
                    "score": 1.0,
                    "entities": [
                        {
                            "id": "1",
                            "role": "ENTITY"
                        },
                        {
                            "id": "2",
                            "role": "ATTRIBUTE"
                        }
                    ]
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-05-08"
}
```

> [!NOTE] 
> A tagadás észlelésével bizonyos esetekben egyetlen tagadási időszak több feltételt is tartalmazhat egyszerre. Egy felismert entitás tagadását a rendszer a JSON-kimenetben a jelző logikai értékével jeleníti meg `isNegated` :

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "type": "SYMPTOM_OR_SIGN",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

## <a name="see-also"></a>Lásd még

* [Text Analytics áttekintése](../overview.md)
* [Elnevezett entitások kategóriái](../named-entity-types.md)
* [Újdonságok](../whats-new.md)

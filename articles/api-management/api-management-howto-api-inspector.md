---
title: "Nyomkövetés-hívások API Inspector - Azure API Management |} Microsoft Docs"
description: "Útmutató az Azure API Management az API-Inspector használatával hívások nyomon követéséhez."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 4b222327-c8a4-4f33-9a06-adff2a9834d9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 8090b22b63dda6f67e321867977d5dece3d72132
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>A követni kívánt API-Inspector használatával meghívja az Azure API Management
Az API Management rendelkezik egy API-Inspector eszközzel segítséget nyújtanak az hibakeresését vagy hibaelhárítását az API-kat. Az API-Inspector programozott módon használható, és a developer portálról közvetlenül is használható. 

Nyomkövetés műveletek mellett is követi az API-Inspector [házirend-kifejezést](https://msdn.microsoft.com/library/azure/dn910913.aspx) értékelések. Bemutatója, lásd: [felhő fedik le a epizód 177: több API-felügyeleti funkciókat](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és előretekerés 21:00.

Ez az útmutató egy segédlet API Inspector használatával.

> [!NOTE]
> API-Inspector nyomkövetések csak jön létre és tartozó előfizetés kulcsok tartalmazó kérelmek elérhetővé a [rendszergazda](api-management-howto-create-groups.md) fiók.
> 
> 

## <a name="trace-call"></a> Használata API-Inspector hívás nyomkövetéséhez
API-Inspector, vegye fel egy **ocp-apim-nyomkövetési: true** a művelet hívásához kérelemfejléc, majd letöltését, és vizsgálja meg a nyomkövetési azt az URL-cím segítségével a **ocp-apim-nyomkövetési-hely** válasz fejléc. Programozott módon lehet végrehajtani, és közvetlenül a developer portálról is el lehet végezni.

Ez az oktatóanyag bemutatja, hogyan használható az API-Inspector nyomkövetést műveletekhez konfigurált alapvető Számológép API használatával a [kezelése az első API](api-management-get-started.md) használatába bevezető oktatóanyagot. Ha még nem fejeződött be, hogy az oktatóanyag csak az alapszintű Számológép API importálása néhány percet vesz igénybe, vagy egy másik API-hoz a például az Echo API kiválasztása. Minden API Management szolgáltatáspéldányhoz előre konfigurálva van egy kipróbálható Echo API, amely segít megismerni az API Management szolgáltatást. Az Echo API bármilyen bemeneti által küldött vissza beolvasása. A használatához hívhat meg a HTTP-műveletet, és a visszatérési érték egyszerűen lesz az Ön által küldött. 

A kezdéshez kattintson **fejlesztői portálján** az Azure portálon az API Management szolgáltatás. Műveletek hívható közvetlenül a megtekintése, és az API-k működésének teszteléséhez kényelmes megoldást kínál a fejlesztői portálhoz.

> Ha még nem hozott az API Management szolgáltatáspéldány, lásd: [hozzon létre egy API-kezelés szolgáltatás példányt] [ Create an API Management service instance] a a [Ismerkedés az Azure API Management] [ Get started with Azure API Management] oktatóanyag.
> 
> 

![Az API Management fejlesztői portálján][api-management-developer-portal-menu]

Kattintson a **API-k** a felső menüben, majd kattintson a **alapvető Számológép**.

![Echo API][api-management-api]

Kattintson a **kipróbálás** próbálkozzon a **két egész számok hozzáadása** műveletet.

![Kipróbálom][api-management-open-console]

Tartsa meg az alapértelmezett paraméterértékek, és válassza ki a használni kívánt termék előfizetés kulcsot a **előfizetés-kulcs** legördülő listán.

A fejlesztői portálra alapértelmezés szerint a **Ocp-Apim-nyomkövetési** fejléc már be van állítva **igaz**. Ezt a fejlécet konfigurálja-e a nyomkövetési jön létre.

![Küldés][api-management-http-get]

Kattintson a **küldése** meghívni a műveletet.

![Küldés][api-management-send-results]

A válaszban szereplő fejlécek kerülnek egy **ocp-apim-nyomkövetési-hely** az alábbi példához hasonló értékkel.

```
ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742
```

A nyomkövetés a megadott helyről letölthető, és tekintse át, ahogyan az a következő lépéssel. Ne feledje, hogy csak az utolsó 100 naplóbejegyzések tárolt naplók helye újra felhasználja a rendszer az elforgatástól. Igen, ha több mint 100 hívásokat a kérelmek nyomon követése engedélyezve végül hozzákezdhet az első nyomkövetések helyen felülírja.

## <a name="inspect-trace"></a>Vizsgálhatja meg a nyomkövetési
Tekintse át a nyomkövetési értékei, töltse le a nyomkövetési fájl a **ocp-apim-nyomkövetési-hely** URL-CÍMÉT. Ez JSON formátumban a fájlt, és az alábbi példához hasonló bejegyzést tartalmaz.

```json
{
    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
    "traceEntries": {
        "inbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0725926",
                "data": {
                    "request": {
                        "method": "GET",
                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "Connection",
                                "value": "Keep-Alive"
                            },
                            {
                                "name": "Host",
                                "value": "contoso5.azure-api.net"
                            }
                        ]
                    }
                }
            },
            {
                "source": "mapper",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0726213",
                "data": {
                    "configuration": {
                        "api": {
                            "from": "/calc",
                            "to": {
                                "scheme": "http",
                                "host": "calcapi.cloudapp.net",
                                "port": 80,
                                "path": "/api",
                                "queryString": "",
                                "query": {},
                                "isDefaultPort": true
                            }
                        },
                        "operation": {
                            "method": "GET",
                            "uriTemplate": "/add?a={a}&b={b}"
                        },
                        "user": {
                            "id": 1,
                            "groups": [
                                "Administrators",
                                "Developers"
                            ]
                        },
                        "product": {
                            "id": 1
                        }
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0727522",
                "data": {
                    "message": "Request is being forwarded to the backend service.",
                    "request": {
                        "method": "GET",
                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "X-Forwarded-For",
                                "value": "33.52.215.35"
                            }
                        ]
                    }
                }
            }
        ],
        "outbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1960601",
                "data": {
                    "response": {
                        "status": {
                            "code": 200,
                            "reason": "OK"
                        },
                        "headers": [
                            {
                                "name": "Pragma",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Length",
                                "value": "124"
                            },
                            {
                                "name": "Cache-Control",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Type",
                                "value": "application/xml; charset=utf-8"
                            },
                            {
                                "name": "Date",
                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                            },
                            {
                                "name": "Expires",
                                "value": "-1"
                            },
                            {
                                "name": "Server",
                                "value": "Microsoft-IIS/8.5"
                            },
                            {
                                "name": "X-AspNet-Version",
                                "value": "4.0.30319"
                            },
                            {
                                "name": "X-Powered-By",
                                "value": "ASP.NET"
                            }
                        ]
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1961112",
                "data": {
                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1963155",
                "data": {
                    "message": "Response body streaming to the caller is complete."
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Következő lépések
* Tekintse meg a házirend-kifejezések helyesen a nyomkövetés bemutatója [felhő fedik le a epizód 177: több API-felügyeleti funkciókat](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Előre 21:00 a bemutató megjelenítéséhez.

> [!VIDEO https://channel9.msdn.com/Shows/Cloud+Cover/Episode-177-More-API-Management-Features-with-Vlad-Vinogradsky/player]
> 
> 

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png





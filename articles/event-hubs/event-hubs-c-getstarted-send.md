---
title: Események C – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk egy útmutató egy C alkalmazás, amely az Azure Event Hubs küldi az eseményeket létrehozásához.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6cb1f788f41fe07516d759b177e1d76405dd2bf8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57529711"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Események küldése az Azure Event Hubs C használatával

## <a name="introduction"></a>Bevezetés
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag azt ismerteti, hogyan küldhet eseményeket egy eseményközpontba egy konzolalkalmazás a c használatával 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* C-fejlesztési környezetre. Ez az oktatóanyag feltételezi, hogy a gcc-verem Ubuntu 14.04 Azure Linux rendszerű virtuális Gépekhez.
* [A Microsoft Visual Studio](https://www.visualstudio.com/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása
Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md).

Az eseményközpont hozzáférési kulcs értékének lekéréséhez kövesse a cikkben szereplő: [Kapcsolati sztring lekérése](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A hozzáférési kulcsot a kód írása az oktatóanyag későbbi részében fogja használni. Az alapértelmezett nevet, majd: **RootManageSharedAccessKey**.

Most folytassa a következő lépéseket ebben az oktatóanyagban.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Kód írása az üzenetek küldése az Event hubs szolgáltatásba való
Ez a rész bemutatja, hogyan küldhet eseményeket az eseményközpontjába C alkalmazások írásához. A kód a Proton AMQP kódtárat használja a [Apache Qpid projekt](https://qpid.apache.org/). Ez hasonló a Service Bus-üzenetsorok és témakörök az amqp-vel a C látható módon a [ebben a példában](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). További információkért lásd: a [Qpid Proton dokumentáció](https://qpid.apache.org/proton/index.html).

1. Az a [Qpid AMQP Messenger lap](https://qpid.apache.org/proton/messenger.html), kövesse az utasításokat követve Qpid Proton, telepítse a környezettől függően.
2. A Proton könyvtár fordítása, telepítse a következő csomagokat:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Töltse le a [Qpid Proton könyvtár](https://qpid.apache.org/proton/index.html), és csomagolja ki, például:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Hozzon létre egy build könyvtárat, fordítás, és telepítse:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. A munkahelyi könyvtárban hozzon létre egy új fájlt **sender.c** az alábbi kódra. Ne feledje el az értékeket a SAS/kulcsnév, az eseményközpont neve és a névtér. Továbbá cserélje le a kulcs URL-kódolású verzióját a **SendRule** korábban létrehozott. URL-kódolása is azt [Itt](https://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Fordítsa le a fájlt, feltéve, hogy **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Ezt a kódot egy kimenő ablakban 1 használja az üzenetek meg minél hamarabb kényszerítése. Javasoljuk, hogy az alkalmazás próbál kötegelt üzenetek átviteli sebesség növelése érdekében. Tekintse meg a [Qpid AMQP Messenger lap](https://qpid.apache.org/proton/messenger.html) ezzel és más környezetekben, és platformokon, amelynek kötések vannak megadva a Qpid Proton library használatával kapcsolatos információk (jelenleg a Perl, a PHP, Python vagy Ruby).

Üzenetek küldése az event hubs, az alkalmazás futtatásához. 

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="next-steps"></a>További lépések
Események fogadása az event hubs kapcsolatos további információkért kattintson a megfelelő fogadónyelvre a **események fogadása az event hub** csomópont a tartalomjegyzékben.


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
